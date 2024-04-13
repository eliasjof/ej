---
title: "ESP32 #02: Hello World"
date: "2020-05-16"
categories: 
  - "esp32"
  - "projensino-esp32"
---

Inicie a programação do ESP32 com esse simples código no Visual Code:

```cpp
#include <Wire.h>
int LED = 2; //Pino 2 referente ao led
void setup() //é executado uma única vez
{
  pinMode (LED, OUTPUT);
}
/*o loop é executado infinitas vezes,
caso não exista uma condição de parada*/
void loop()  
{
  digitalWrite(LED, HIGH);  //liga led
  delay(100); //delay de 100ms
  digitalWrite(LED, LOW); //apaga led
  delay(100);
}
```

Mais detalhes, assista ao vídeo:

https://www.youtube.com/watch?v=aAneu6x7TM0
