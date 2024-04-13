---
title: "ESP32 #08 -  ROS + ESP32 + Ultrasom + LCD I2C + Servo motor"
date: "2022-12-17"
categories: 
  - "esp32"
  - "robotica"
---
# ESP32 #08 - ROS + ESP32 + Ultrasom + LCD I2C + Servo motor

Neste artigo, iremos desenvolver um sistema com ROS e ESP32, obtendo informações de um ultrassom, acionando um servo motor e mostrando os dados em um LCD via I2C.

Obs.: Não deixe de ver os passos iniciais do artigo [ESP32 #07](http://esp32).

## Conexões


![](image-1%202.png)


Elaborado no Fritzing


## Bibliotecas

1. Rosserial: [ros\_lib.zip](https://ej-ensino.com.br/wp-content/uploads/2022/12/ros_lib.zip)
    - Extraia o .zip na pasta libraries. Essa pasta foi gerada a partir do artigo [ESP32 #07](https://ej-ensino.com.br/2022/10/15/esp32-07-conexao-ros-esp32/).

3. LCD Crystal I2C: [https://github.com/fdebrabander/Arduino-LiquidCrystal-I2C-library](https://github.com/fdebrabander/Arduino-LiquidCrystal-I2C-library)
    - Faça download da biblioteca e salve na basta Arduino/libraries com o nome “LiquidCrystal\_I2C”.

5. Servo motor com ESP32: [https://github.com/RoboticsBrno/ESP32-Arduino-Servo-Library/archive/master.zip](https://github.com/RoboticsBrno/ESP32-Arduino-Servo-Library/archive/master.zip)
    - Extraia o .zip na pasta libraries e renomeie-a para Servo

7. HC-SR04: [https://github.com/d03n3rfr1tz3/HC-SR04](https://github.com/d03n3rfr1tz3/HC-SR04)
    - No Gerenciador de bibliotecas procure por HC-SR04 e instale a biblioteca.

![](image_2022-12-17_172044256.png)

Reinicie a IDE do Arduino para que as bibliotecas sejam reconhecidas.

## Código

### Teste inicial (sem o ROS)

```arduino
/**
 */
#include <Wire.h> 
#include <LiquidCrystal_I2C.h>
#include <HCSR04.h>

// Pinos do ultrassom 
byte triggerPin = 27;
byte echoCount = 2;
byte* echoPins = new byte[echoCount] { 33, 34 };
String mytext = "info";

// Set the LCD address to 0x27 for a 16 chars and 2 line display
LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup()
{
   
  Serial.begin(115200);  
  lcd.begin();
  lcd.clear();
  lcd.backlight();
  lcd.setCursor(0, 0);
  Serial.println("LCD OK");
  lcd.print("Inicializando...");
  HCSR04.begin(triggerPin, echoPins, echoCount);
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Pronto!");
  Serial.println("Pronto!");

}

void loop()
{
  
	// Verifica se tem algum dado na serial
	if (Serial.available()) {
		mytext = Serial.readString();
    // Remove o caracter \n do final da string
    unsigned int mesgLen = mytext.length();
    mytext[mesgLen - 1] = ' ';

    // Mostra no LCD
    lcd.clear();
    lcd.setCursor(0, 1);    
    lcd.print(mytext);

	}

  // Realiza as medições do sensores de distância
  double* distances = HCSR04.measureDistanceCm();
  lcd.clear();
  for (int i = 0; i < echoCount; i++) {
    if (i == 0){
      lcd.setCursor(11, 0);
      lcd.print(distances[i]);
      
    }
    if (i == 1){
      lcd.setCursor(11, 1);
      lcd.print(distances[i]);
      lcd.setCursor(0, 1);   
      lcd.print(mytext);
    }
  } 
}
```

### Enviando e recebendo os dados no ROS

```arduino
/**
 */
#define ROSSERIAL_ARDUINO_TCP
#include <WiFi.h>
#include <Wire.h> 
#include <LiquidCrystal_I2C.h>
#include <HCSR04.h>
#include <Servo.h> 
#include <ros.h>
#include <ros/time.h>
#include <std_msgs/Float32.h>
#include <std_msgs/String.h>

// WIFI config
const char* ssid = "REDE NET 16";
const char* password = "2083688600";
// Defina o endereço IP do servidor do soquete (rosserial)
IPAddress server(192,168,0,5);
// Defina a porta do servidor do soquete (rosserial)
const uint16_t serverPort = 11411;
///////////////////

// ROS config
ros::NodeHandle  nh;


///////////////////
// Ultrassom 
byte triggerPin = 27;
byte echoCount = 2;
byte* echoPins = new byte[echoCount] { 33, 34 };
std_msgs::Float32 msg_ultrassom;
//sensor_msgs::Range msg_ultrassom;
ros::Publisher pub_ultrassom_esquerda( "/ultrassom_esquerda", &msg_ultrassom);
ros::Publisher pub_ultrassom_direita( "/ultrassom_direita", &msg_ultrassom);
//char frameid[] = "/ultrassom";

///////////////////
// LCD
// Set the LCD address to 0x27 for a 16 chars and 2 line display
String mytext = "conectado";
LiquidCrystal_I2C lcd(0x27, 16, 2);

//atualiza o lcd
void callback_lcd( const std_msgs::String& msg){
  mytext = msg.data;
  lcd.setCursor(0, 1);   
  lcd.print("          "); //clear space
  lcd.setCursor(0, 1);   
  lcd.print(mytext);
}
ros::Subscriber<std_msgs::String> sub_lcd("/lcd", &callback_lcd );

///////////////////
// Servo motor
float servo_pos = 0;
const int pinoServo = 26;
Servo servo;
//atualiza o angulo do servo motor
void callback_servo( const std_msgs::Float32& msg){
  servo_pos = msg.data;
  servo.write(servo_pos);  
}
ros::Subscriber<std_msgs::Float32> sub_servo("/servo_pos", &callback_servo );
///////////////////

void setup()
{
  
  Serial.begin(115200); 
  // Inicializa lcd 
  lcd.begin();
  lcd.clear();
  lcd.backlight();
  lcd.setCursor(0, 0);
  Serial.println("LCD OK");
  lcd.print("Inicializando...");
  // Inicializa ultrassom
  HCSR04.begin(triggerPin, echoPins, echoCount);
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Pronto!");
  Serial.println("Pronto!");

  // Inicializa servo motor
  servo.attach(pinoServo); 
  servo.write(0); //inicia na posição 0
  
  // Inicializa wifi
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

  // Inicializa nó ROS
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
    
  // Publishers
  nh.advertise(pub_ultrassom_direita);
  nh.advertise(pub_ultrassom_esquerda);

  //Subscribers
  nh.subscribe(sub_servo);
  nh.subscribe(sub_lcd);
}


void loop()
{
  if (nh.getHardware() ->connected()) {  
    // Realiza as medições do sensores de distância
    double* distances = HCSR04.measureDistanceCm();
    lcd.clear();
    for (int i = 0; i < echoCount; i++) {
      if (i == 0){
        lcd.setCursor(0, 0);   
        lcd.print("servo=");
        lcd.print(servo_pos);
        lcd.setCursor(11, 0);
        lcd.print(distances[i]);
        msg_ultrassom.data = distances[i];
        pub_ultrassom_esquerda.publish(&msg_ultrassom);
        
      }
      if (i == 1){
        lcd.setCursor(11, 1);
        lcd.print(distances[i]);
        lcd.setCursor(0, 1);   
        lcd.print(mytext);
        msg_ultrassom.data = distances[i];
        pub_ultrassom_direita.publish(&msg_ultrassom);
      }
    }
    nh.spinOnce();
    delay(1/10); // 10Hz
    
   }
   else{
    Serial.println("desconectado\n");
      while (nh.getHardware() ->connected()!=1) {
    
      nh.getHardware() -> setConnection(server, serverPort);  
      nh.initNode();
      delay(50);
      Serial.print(".");
      
      }
   }
}
```

No PC abra dois terminais, no primeiro rode:

```
roscore
```

e no segundo:

```
rosrun rosserial_python serial_node.py tcp 11411
```

Para visualizar os dados do ultrassom, abra outro terminal:

```
rostopic echo /ultrassom_esquerda
```

Para enviar um dado para o servo motor:

```
rostopic pub /servo_pos std_msgs/Float32 "data: 180.0"
```

Para enviar um dado para o lcd:

```
rostopic publcd std_msgs/String "data: 'teste'"
```
