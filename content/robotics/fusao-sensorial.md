---
title: "Fusão Sensorial"
date: "2020-05-18"
categories: 
  - "robotica"
  - "embarcados"
---

## Aplicação para se obter dados coerentes de uma IMU (parte 1)

### 1 - IMU (Unidade Inercial de Medidas)

Nesse primeiro teste, iremos utilizar o sensor [MPU6550](https://www.electronicwings.com/sensors-modules/mpu6050-gyroscope-accelerometer-temperature-sensor-module) que contém um acelerômetro e um giroscópio, fornecendo 6 DOF (graus de liberdade), além de um sensor de temperatura. (Detalhes do funcionamento em: [https://www.embarcados.com.br/sensores-inerciais-parte-2/](https://www.embarcados.com.br/sensores-inerciais-parte-2/))

- O acelerômetro fornece os dados de aceleração nos 3 eixos em \[latex\] \[m/s^2\] \[/latex\] (\[latex\] a\_x \[/latex\], \[latex\] a\_y \[/latex\] e \[latex\] a\_z \[/latex\]).

- O giroscópio fornece a variação dos ângulos nos 3 eixos em \[latex\]\[°/s\]\[/latex\] (\[latex\] \\dot{\\phi} \[/latex\], \[latex\] \\dot{\\theta} \[/latex\] e \[latex\] \\dot{\\psi}\[/latex\]).

Vamos tratar aqui de como obter os ângulos **pitch** \[latex\] (\\theta) \[/latex\] e **roll** \[latex\] (\\phi) \[/latex\], mostrados na Figura 1.

<figure>

![Using the MPU-6050](images/image-5.png)

<figcaption>

Figura 1 - Eixos de referência.

</figcaption>

</figure>

### 2 - Montagem

A conexão entre o MPU6050 e o ESP32 (Dev Kit) é feita por meio da comunicação I2C. Os pinos 22 e 23 foram utilizados, respectivamente, como pino SDA (dados) e pino SCL (clock). A alimentação do MPU6050 utilizada foi de 3,3V (fornecida pelo próprio ESP32) e o GND foi interligado. Mais detalhes podem ser vistos na Figura 2.

<figure>

![](esp32_mpu6050-1024x732.png)

<figcaption>

Figura 2 - Montagem ESP32 + MPU6050.

</figcaption>

</figure>

### 3 - Código embarcado

Como a ideia é testar o filtro de Kalman, optou-se por implementá-lo em python. Dessa forma, o ESP32 é responsável apenas por fazer a leitura da IMU e enviar os dados para o computador (conexão usb-serial).

Compile e faz o envio do seguinte código para o ESP32:

```cpp
#include<Wire.h>


const int MPU_addr=0x68; // I2C address of the MPU-6050

int16_t AcX,AcY,AcZ,Tmp,GyX,GyY,GyZ;
float AccX, AccY, AccZ;
float GyroX, GyroY, GyroZ;

float elapsedTime = 0.0, currentTime = 0.0, previousTime = 0.0;


void setup(){

  Wire.begin(22,23);

  Wire.beginTransmission(MPU_addr);

  Wire.write(0x6B); // PWR_MGMT_1 register

  Wire.write(0x00); // set to zero (wakes up the MPU-6050)
  
  Wire.endTransmission(true);

  Wire.beginTransmission(MPU_addr);     // inicia comunicação com endereço do MPU6050
  Wire.write(0x1B);                      // envia o registro com o qual se deseja trabalhar
  Wire.write(0x00);                      // escreve o valor no registro
  Wire.endTransmission(true);           // termina a transmissão

  Serial.begin(115200);
}



void loop(){
  
  // === Read acceleromter data === //
  Wire.beginTransmission(MPU_addr);
  Wire.write(0x3B); // Start with register 0x3B (ACCEL_XOUT_H)
  Wire.endTransmission(false);  
  Wire.requestFrom(MPU_addr, 6, true); // Read 6 registers total, each axis value is stored in 2 registers
  
  //For a range of +-2g, we need to divide the raw values by 16384, according to the datasheet  
  // and for correct conversion you must use int16_t
  AccX = int16_t(Wire.read() << 8 | Wire.read()) / 16384.0; // X-axis value
  AccY = int16_t(Wire.read() << 8 | Wire.read()) / 16384.0; // Y-axis value
  AccZ = int16_t(Wire.read() << 8 | Wire.read()) / 16384.0; // Z-axis value
  

  // === Read gyro data === //
  Wire.beginTransmission(MPU_addr);
  Wire.write(0x43); // Gyro data first register address 0x43
  Wire.endTransmission(false);
  Wire.requestFrom(MPU_addr, 6, true); // Read 4 registers total, each axis value is stored in 2 registers
  GyroX = int16_t(Wire.read() << 8 | Wire.read()) / 131.0; // For a 250deg/s range we have to divide first the raw value by 131.0, according to the datasheet
  GyroY = int16_t(Wire.read() << 8 | Wire.read()) / 131.0;
  GyroZ = int16_t(Wire.read() << 8 | Wire.read()) / 131.0;


  // === Send data (Serial) === //
  Serial.print("$");
  Serial.print(AccX);
  Serial.print("$");
  Serial.print(AccY);
  Serial.print("$");
  Serial.print(AccZ);
  Serial.print("$");
  Serial.print(GyroX);
  Serial.print("$");
  Serial.print(GyroY);
  Serial.print("$");
  Serial.print(GyroZ); 
  Serial.print("$#");   
  Serial.println();




  currentTime = millis();            // Current time actual time read
  elapsedTime = (currentTime - previousTime) ; // Divide by 1000 to get seconds
  previousTime = currentTime;        // Previous time is stored before the actual time read
  int t = (20 - elapsedTime) > 0 ? int(20 - elapsedTime) : 0;
  delay(t); //for 50Hz
//  Serial.print(elapsedTime);   
//  Serial.println();

}
```

Verifique se os dados estão sendo enviados corretamente:

<figure>

![](images/image-6.png)

<figcaption>

Figura 3 - Dados (raw) do acelerômetro e do giroscópio.

</figcaption>

</figure>

### 4 - Leitura dos dados (python)

Para realizar a leitura dos dados enviados pelo ESP32 via usb-serial, é necessário instalar a biblioteca pyserial.

Obs.: Se estiver utilizando o Anaconda, vá ao seu prompt de comando e digite:

```bash
pip install pyserial
```

Crie um arquivo .py para a leitura correta dos dados, obtendo os valores enviados pelo ESP32 em float. Segue um código exemplo:

```python
### Leitura dos dados da MPU6050

import serial
import time
import numpy as np
import matplotlib.pyplot as plt
import msvcrt
import sys

def getData(serialPort, char_init="$", char_end="#"):    
    """ Obtém os dados da serial
        dado esperado: $ax$ay$az$wx$wy$wz$# 
    """
    line = serialPort.readline().decode(encoding='utf-8',errors='ignore') #converte bytes para string
    line = line.strip("\r\n") #remove tabulação final
    try:
        if line[0]==char_init:
            if line[-1]==char_end:            
                dados = line.split(char_init)[1:]   
                dados = dados[:-1] # retira o último elemento #
                if len(dados) == 6:                          
                    dados = [float(v) for i,v in zip(range(0,6),dados)] #converte para float
                    return True, dados                                 
        return False, [-1,-1,-1,-1,-1,-1]
    except:
        return False, [-1,-1,-1,-1,-1,-1]

def saveData(data, Acx, Acy, Acz, Gx, Gy, Gz):
    """ adiciona os dados recebidos nas devidas listas """
    Acx.append(data[0])
    Acy.append(data[1])
    Acz.append(data[2])
    Gx.append(data[3])
    Gy.append(data[4])
    Gz.append(data[5])


def drawData(Acx, Acy, Acz, Gx, Gy, Gz):
    """ Plota os dados obtidos do acelerômetro e giroscópio na forma de histograma e ao longo do tempo """
    
    N = len(Acx)

    mean_Acx = np.round(np.mean(Acx),7)
    cov_Acx = np.round(np.cov(Acx),7)
    mean_Acy = np.round(np.mean(Acy),7)
    cov_Acy = np.round(np.cov(Acy),7) 
    mean_Acz = np.round(np.mean(Acz),7)
    cov_Acz = np.round(np.cov(Acz),7)
    mean_Gx = np.round(np.mean(Gx),7)
    cov_Gx = np.round(np.cov(Gx),7) 
    mean_Gy = np.round(np.mean(Gy),7)
    cov_Gy = np.round(np.cov(Gy),7)  
    mean_Gz = np.round(np.mean(Gz),7)
    cov_Gz = np.round(np.cov(Gz),7)  

    
    
    fig = plt.figure(figsize=(5,5)) 
    ax = fig.add_subplot(231)     
    ax.hist(Acx, bins=100, color='#e24a33', density=True)  
    ax.set_title("Acx - média = "+str(mean_Acx))
    ax = fig.add_subplot(234)        
    ax.plot(range(N), Acx,'.')    
    ax.set_title("Acx - variância = " + str(cov_Acx))

    
    ax = fig.add_subplot(232)     
    ax.hist(Acy, bins=100, color='#e24a33', density=True) 
    ax.set_title("Acy - média = "+str(mean_Acy)) 
    ax = fig.add_subplot(235)        
    ax.plot(range(N), Acy,'.')
    ax.set_title("Acy - variância = " + str(cov_Acy))


    ax = fig.add_subplot(233)     
    ax.hist(Acz, bins=100, color='#e24a33', density=True) 
    ax.set_title("Acz - média = "+str(mean_Acz) )
    ax = fig.add_subplot(236)  
    ax.plot(range(N), Acz,'.')
    ax.set_title("Acz - variância = " + str(cov_Acz))


    fig = plt.figure(figsize=(5,5)) 
    ax = fig.add_subplot(231)     
    ax.hist(Gx, bins=100, color='#e24a33', density=True)  
    ax.set_title("Gx - média = "+str(mean_Gx))
    x = np.linspace(min(Gx)-1,max(Gx)+1,N//2)
    f = 1/((cov_Gx**0.5)*np.sqrt(2*np.pi))*np.exp(-0.5*((x - mean_Gx)/(cov_Gx**0.5))**2)        
    ax.plot(x,f,'b')
    ax = fig.add_subplot(234)        
    ax.plot(range(N), Gx,'.')    
    ax.set_title("Gx - variância = " + str(cov_Gx))


    ax = fig.add_subplot(232)     
    ax.hist(Gy, bins=100, color='#e24a33', density=True) 
    ax.set_title("Gy - média = "+str(mean_Gy)) 
    x = np.linspace(min(Gy)-1,max(Gy)+1,N//2)
    f = 1/((cov_Gy**0.5)*np.sqrt(2*np.pi))*np.exp(-0.5*((x - mean_Gy)/(cov_Gy**0.5))**2)
    ax.plot(x,f,'b')
    ax = fig.add_subplot(235)        
    ax.plot(range(N), Gy,'.')
    ax.set_title("Gy - variância = " + str(cov_Gy))

    
    ax = fig.add_subplot(233)     
    ax.hist(Gz, bins=100, color='#e24a33', density=True) 
    ax.set_title("Gz - média = "+str(mean_Gz) )
    x = np.linspace(min(Gy)-1,max(Gz)+1,N//2)
    f = 1/((cov_Gz**0.5)*np.sqrt(2*np.pi))*np.exp(-0.5*((x - mean_Gz)/(cov_Gz**0.5))**2)        
    ax.plot(x,f,'b')
    ax = fig.add_subplot(236)  
    ax.plot(range(N), Gz,'.')
    ax.set_title("Gz - variância = " + str(cov_Gz))  

    plt.show()

if __name__ == "__main__":    

    Ts = 1/50.0 #tempo de amostragem    

    serialPort = serial.Serial(port = "COM5", baudrate=115200, bytesize=8, timeout=2, stopbits=serial.STOPBITS_ONE)
    serialPort.timeout = 2  # set read timeout    
    serialPort.flush()
    print("Conexão {}".format(serialPort.is_open))  # True for opened

    
    if not serialPort.is_open:
        print('serialPort not open')
        sys.exit()

    
    Acx, Acy, Acz, Gx, Gy, Gz = [], [], [], [], [], []

    t1, t2, t_total = 0.0 , 0.0, 0.0    
    t1 = time.time()
    while True:
        
        
        t2 = time.time()
        dt = t2 - t1    

        if dt >= Ts:
            ok, data = getData(serialPort)
            t_total += dt
            t1 = t2
        
            if(ok):
                saveData(data, Acx, Acy, Acz, Gx, Gy, Gz)
                print("---------------------------------")
                print("tempo total = {}s".format(t_total))
                print(Acx[-1], Acy[-1], Acz[-1], Gx[-1], Gy[-1], Gz[-1])
                print("número de amostras = ", len(Acx))
                print("---------------------------------")

        if msvcrt.kbhit(): # para de capturar os dados após apertar ESC
            if ord(msvcrt.getch()) == 27:
                break
    
        
    
    drawData(Acx, Acy, Acz, Gx, Gy, Gz)
```

Ao rodar o programa, deixando a montagem em uma condição estacionária (considerada como condição inicial), poderá ser obtido dados similares a esse:

<figure>

![](image-7.png)

<figcaption>

Figura 4 - Resultado no terminal.

</figcaption>

</figure>

Após aproximadamente 3000 amostras, é possível ver a característica normal dos dados na condição estacionária, obtendo um valor de média e de variância. Esses dados serão utilizados para calibrar o sensor e como parâmetro no filtro de Kalman.

<figure>

![](image-8-1024x505.png)

<figcaption>

  
Figura 5 - Dados do giroscópio.

</figcaption>

</figure>

<figure>

![](image-9-1024x515.png)

<figcaption>

Figura 6 - Dados do acelerômetro.

</figcaption>

</figure>

Obs.: Os dados do acelerômetro possuem uma variância menor do que a do giroscópio.

### 5 - Obtenção dos ângulos pitch e roll pelo acelerômetro

Podemos obter os ângulos roll e pitch por geometria:

- roll: \[latex\] \\phi = arctan \\left (\\frac{a\_y}{a\_z} \\right ) \[/latex\]

- pitch: \[latex\] \\theta= arctan \\left ( \\frac{-a\_x}{\\sqrt{a\_y^2 + a\_z^2}} \\right ) \[/latex\]

Podemos definir uma função para retornar esses ângulos:

```python
def getAnglesAcc(data):
    """ Retorna o valor de ângulo (pitch e roll) obtido pelo acelerometro """
    pitch = np.arctan2(-data[0] , np.sqrt(data[1]**2 + data[2]**2))
    roll = np.arctan2(data[1] , data[2])

    return pitch*180/np.pi, roll*180/np.pi
```

Podemos obter os seguintes valores de ângulos para um movimento do sensor:

- Variação de aprox. 90° em torno de y e depois retorno a posição inicial (x3):

<figure>

![](image-10.png)

<figcaption>

Figura 7 - Dados obtidos do ângulo pitch por meio do acelerômetro.

</figcaption>

</figure>

Variação de aprox. 90° em torno de x e depois retorno a posição inicial (x3):

<figure>

![](image-11.png)

<figcaption>

Figura 8 - Dados obtidos do ângulo roll por meio do acelerômetro.

</figcaption>

</figure>

### 6 - Obtenção por Integração numérica dos dados do giroscópio

### 7 - Fusão sensorial

### Outros

#### Calibração do Magnetômetro

[https://github.com/kriswiner/MPU6050/wiki/Simple-and-Effective-Magnetometer-Calibration](https://github.com/kriswiner/MPU6050/wiki/Simple-and-Effective-Magnetometer-Calibration)

[https://diydrones.com/profiles/blogs/advanced-hard-and-soft-iron-magnetometer-calibration-for-dummies](https://diydrones.com/profiles/blogs/advanced-hard-and-soft-iron-magnetometer-calibration-for-dummies)
