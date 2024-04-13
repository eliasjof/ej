---
title: "ESP32 #07 - Conexão ROS + ESP32"
date: "2022-10-15"
categories: 
  - "esp32"
  - "robotica"
---

O objetivo deste tutorial é apresentar como realizar a conexão do ESP32 com o ROS (no ubuntu 20.04), seja por meio da conexão usb seja via wifi.

## Arduino IDE para ESP32

- Download Arduino IDE 2.0 ([https://www.arduino.cc/en/software](https://www.arduino.cc/en/software))

- Instale o Arduino IDE ([https://docs.arduino.cc/software/ide-v2/tutorials/getting-started/ide-v2-downloading-and-installing](https://docs.arduino.cc/software/ide-v2/tutorials/getting-started/ide-v2-downloading-and-installing))

- Configurando e instalando a placa ESP32 na IDE:

Ref.: [https://www.usinainfo.com.br/blog/programar-esp32-com-a-ide-arduino-tutorial-completo/](https://www.usinainfo.com.br/blog/programar-esp32-com-a-ide-arduino-tutorial-completo/)

Em Preferências, cole a URL abaixo no campo de URLs adicionais:

- https://dl.espressif.com/dl/package\_esp32\_index.json

ou

- https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package\_esp32\_index.json

[![](esp1-edited.png)](https://www.usinainfo.com.br/blog/wp-content/uploads/2019/06/esp1-edited.png)

Com isso, permitimos que a IDE acesse uma pequena “base de dados” no formato .json que contém a configuração de inúmeras placas. Após isso, devemos acessar o menu Ferramentas -> Placa -> Gerenciador de Placas.

[![](esp2-edited.png)](https://www.usinainfo.com.br/blog/wp-content/uploads/2019/06/esp2-edited.png)

Instale a versão mais recente do driver que irá aparecer: “esp32 by Espressif Systems’

![](image-1024x956.png)

Selecione a placa ESP32 corretamente:

![](image-1%202.png)

Instale os drivers de conexão no Ubuntu

```bash
sudo usermod -a -G dialout $USER && sudo apt-get install git && wget https://bootstrap.pypa.io/get-pip.py && sudo python get-pip.py && sudo pip install pyserial && mkdir -p ~/Arduino/hardware/espressif && cd ~/Arduino/hardware/espressif && git clone https://github.com/espressif/arduino-esp32.git esp32 && cd esp32 && git submodule update –init –recursive && cd tools && python3 get.py
```

Permissão de acesso/escrita para a porta usb no Ubuntu

```bash
sudo chmod a+rw /dev/ttyUSB0
```

### Teste básico ESP32

Compile e faça o upload do seguinte código de teste, que fará o LED built-in piscar:

```arduino
void setup() {
  // initialize digital pin LED_BUILTIN as an output.
  pinMode(LED_BUILTIN, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(LED_BUILTIN, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(1000);                       // wait for a second
  digitalWrite(LED_BUILTIN, LOW);    // turn the LED off by making the voltage LOW
  delay(1000);                       // wait for a second
}
```

## ROSserial ESP32

Fonte: [https://github.com/sachin0x18/rosserial\_esp32](https://github.com/sachin0x18/rosserial_esp32) e [https://robotisim.com/2022/02/01/ros-communication-with-esp32-using-rosserial-package/](https://robotisim.com/2022/02/01/ros-communication-with-esp32-using-rosserial-package/)

- Na versão 2.0 do Arduino IDE, instale a biblioteca rosserial (Ferramentas --> Gerenciador de bibliotecas)

![](image-2%202.png)

- Instale o ROS (Robot Operating System) seguindo todos os passos de: [http://wiki.ros.org/noetic/Installation/Ubuntu](http://wiki.ros.org/noetic/Installation/Ubuntu)

- (opcional) Na sua máquina após instalar o ROS, crie um novo workspace que será usado para configurar e instalar o ROSserial

```
sudo apt install ros-noetic-rosserial
mkdir rosserial/src -p
cd rosserial/src
catkin_init_workspace
git clone https://github.com/sachin0x18/rosserial_esp32.git
cd ..
catkin_make
source devel/setup.bash 

```

Vá até a pasta libraries do sketchbook do Arduino e faça o seguinte comando (será criada uma pasta ros\_lib para os projetos feitos na IDE do Arduino):

```bash
rosrun rosserial_arduino make_libraries.py . 
```

Altere as seguintes linhas do arquivo ros\_lib/ros.h

De:

```
#if defined(ROSSERIAL_ARDUINO_TCP)
  #include "ArduinoTcpHardware.h"
#else
  #include "ArduinoHardware.h"
#endif
```

Para:

```
#if defined(ESP8266) or defined(ESP32) or defined(ROSSERIAL_ARDUINO_TCP)
  #include "ArduinoTcpHardware.h"
#else
  #include "ArduinoHardware.h"
#endif
```

### Teste subscriber

Compile e faça o upload do código abaixo para o ESP32

```arduino
/* 
 * rosserial Subscriber Example
 * Blinks an LED on callback
 */

#include <ros.h>
#include <std_msgs/Empty.h>

ros::NodeHandle  nh;

void messageCb( const std_msgs::Empty& toggle_msg){
  digitalWrite(LED_BUILTIN, HIGH-digitalRead(LED_BUILTIN));   // blink the led
}

ros::Subscriber<std_msgs::Empty> sub("toggle_led", &messageCb );

void setup()
{ 
  pinMode(LED_BUILTIN, OUTPUT);
  nh.initNode();
  nh.subscribe(sub);
}

void loop()
{  
  nh.spinOnce();
  delay(1);
}
```

Iremos criar agora um novo ambiente (pasta de trabalho) para os projetos com ROS e iremos criar um nó em Python que publica uma informação vazia:

```bash
mkdir esp32_drivers/src -p
cd esp32_drivers/src/
catkin_init_workspace
catkin_create_pkg blinkLed rospy roscpp
mkdir blinkLed/scripts 
```

Dentro da pasta scripts adicione um arquivo blinkLed.py com o código em Python do Publisher:

```python
#!/usr/bin/env python
import rospy
from std_msgs.msg import Float32

    

if __name__ == '__main__':
    try:
        pub = rospy.Publisher('esp32_data', Float32, queue_size=10)
        rospy.init_node('talker', anonymous=True)
        rate = rospy.Rate(2) # 2hz
        data = 0.0
        while not rospy.is_shutdown():       
            rospy.loginfo(data)
            pub.publish(data)
            data += 0.1
            if data > 1000:
                data = 0.0
            rate.sleep()
    except rospy.ROSInterruptException:
        pass
```

Após salvar o arquivo, seguindo os comandos no prompt aberto:

```bash
chmod +x blinkLed/scripts/blinkLed.py 
cd ..
catkin_make
source devel/setup.bash
```

Abra dois novos prompts de comando. No primeiro rode:

```bash
roscore
```

No segundo:

```bash
rosrun rosserial_python serial_node.py _baud:=57600
```

Pronto! Você estará enviando um dado Float para Arduino via ROS no tópico /esp32\_data. Ao receber o dado, o LED irá alternar entre ligado e desligado.

## Enviando um comando de velocidade

Utilizando a mesma estrutura iremos criar um novo arquivo no arquino que recebe a informação no tópico /cmd\_vel e que republica a mensagem de verificação no /vel\_esp32received.

Então no computador iremos rodar um nó em python que lê a informação do teclado (turtlesim turtle\_teleop\_key) e republica no tópico /cmd\_vel.

Código cmd\_vel\_controller.py:

```python
#!/usr/bin/env python
import rospy
from geometry_msgs.msg import Twist

vel_msg = Twist()

def callback(msg):
    global vel_msg
    vel_msg = msg #update info from turtle1_teleop


if __name__ == '__main__':
    try:
        rospy.Subscriber("turtle1/cmd_vel", Twist, callback)
        pub = rospy.Publisher('cmd_vel', Twist, queue_size=1)
        rospy.init_node('controller', anonymous=True)
        rate = rospy.Rate(10) # 2hz
        data = 0.0
        while not rospy.is_shutdown():       
            #rospy.get_time()
            rospy.loginfo(vel_msg)
            pub.publish(vel_msg)
            
            rate.sleep()
    except rospy.ROSInterruptException:
        pass
```

Lembre de dar permissão de execução. Por exemplo, considerando que o arquivo foi criado na pasta src/blinkLed/scripts:

```bash
chmod +x src/blinkLed/scripts/cmd_vel_controller.py
catkin_make
```

Código no ESP32

```arduino
#include <ros.h>
#include <std_msgs/Float32.h>
#include <geometry_msgs/Vector3.h>
#include <geometry_msgs/Twist.h>

ros::NodeHandle  nh;

geometry_msgs::Vector3 vel_msg;
ros::Publisher pub_vel_received("vel_esp32received", &vel_msg);


float vel_linear = 0.0;
float vel_angular = 0.0;

void callback_cmd_vel( const geometry_msgs::Twist& msg){

  vel_linear = msg.linear.x;
  vel_angular = msg.angular.z;

  vel_msg.x = vel_linear;
  vel_msg.z = vel_angular;
  digitalWrite(LED_BUILTIN, HIGH-digitalRead(LED_BUILTIN));   // blink the led
}
ros::Subscriber<geometry_msgs::Twist> sub_vel("/cmd_vel", &callback_cmd_vel );


void setup()
{ 
  pinMode(LED_BUILTIN, OUTPUT);
  nh.initNode();
  nh.advertise(pub_vel_received);
  nh.subscribe(sub_vel);
}

void loop()
{  
  pub_vel_received.publish(&vel_msg);
  nh.spinOnce();
  delay(1/10); // 10Hz
}
```

Abra 4 terminais

```bash
roscore # terminal 1
rosrun rosserial_python serial_node.py _baud:=57600 #terminal 2
rosrun turtlesim turtle_teleop_key  # terminal 3 onde deve ser enviado os comandos pelo teclado
rosrun blinkLed cmd_vel_controller.py # terminal 4 (lembre-se de rodá-lo após habilitar o pacote com source devel/setup.bash)

```

## Conexão via wifi

Compilar e fazer o upload do seguinte código .ino

```arduino
#define ROSSERIAL_ARDUINO_TCP
#define ESP32

#include <WiFi.h>
#include <ros.h>
#include <std_msgs/Float32.h>
#include <geometry_msgs/Vector3.h>
#include <geometry_msgs/Twist.h>

const char* ssid = "colocar o nome da rede";
const char* password = "colocar a senha da rede";

// Defina o endereço IP do servidor do soquete (rosserial)
IPAddress server(192,168,0,116);
// Defina a porta do servidor do soquete (rosserial)
const uint16_t serverPort = 11411;

ros::NodeHandle  nh;

geometry_msgs::Vector3 vel_msg;
ros::Publisher pub_vel_received("vel_esp32received", &vel_msg);


float vel_linear = 0.0;
float vel_angular = 0.0;

void callback_cmd_vel( const geometry_msgs::Twist& msg){

  vel_linear = msg.linear.x;
  vel_angular = msg.angular.z;

  vel_msg.x = vel_linear;
  vel_msg.z = vel_angular;
  digitalWrite(LED_BUILTIN, HIGH-digitalRead(LED_BUILTIN));   // blink the led
}
ros::Subscriber<geometry_msgs::Twist> sub_vel("/cmd_vel", &callback_cmd_vel );


void setup()
{ 
  pinMode(LED_BUILTIN, OUTPUT);
  // Usando o monitor serial do ESP
  Serial.begin(115200);
  Serial.println();
  Serial.print("Conectando em ");
  Serial.println(ssid);

  // conecta o ESP ao wifi

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP Adress: ");
  Serial.println(WiFi.localIP());
  Serial.println("");

  // Definindo a conexão com o servidor do soquete (rosserial)
  nh.getHardware() -> setConnection(server, serverPort);  
  nh.initNode();
  while (nh.getHardware() ->connected()!=1) {
    
    nh.getHardware() -> setConnection(server, serverPort);  
    nh.initNode();
    delay(50);
    Serial.print(".");
    
  }
  Serial.print("\nOK TCP-IP = ");
  Serial.println(nh.getHardware() -> getLocalIP());
  //Serial.println(nh.getHardware() ->connected());
  nh.advertise(pub_vel_received);
  nh.subscribe(sub_vel);
  
}

void loop()
{
  if (nh.getHardware() ->connected()) {  
    pub_vel_received.publish(&vel_msg);
    nh.spinOnce();
    delay(50); // 20Hz
    
  }
  else{
    Serial.println("disconnected\n");
      while (nh.getHardware() ->connected()!=1) {
    
      nh.getHardware() -> setConnection(server, serverPort);  
      nh.initNode();
      delay(50);
      Serial.print(".");
      
    }
  }
}
```

Rodar os comandos anteriores, porém utilizando o parâmetro tcp para o rosserial\_python:

```bash
rosrun rosserial_python serial_node.py tcp 11411
```
