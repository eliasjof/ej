---
title: "ESP32 #06 - Acionando um motor CC"
date: 2020-08-06
categories:
  - esp32
  - projensino-esp32
tags:
  - esp32
---

https://www.youtube.com/watch?v=34fM0k6qIEU

## Código

```python
#include <Wire.h>
//Define os pinos conectados a ponte H
int IN1 = 12;
int IN2 = 14;
void setup()
{
  //Define os pinos como saida
 pinMode(IN1, OUTPUT);
 pinMode(IN2, OUTPUT);
}
void loop()
{
 //Gira o Motor no sentido horario
 digitalWrite(IN1, HIGH);
 digitalWrite(IN2, LOW);
 delay(2000);
 //Para o motor
 digitalWrite(IN1, HIGH);
 digitalWrite(IN2, HIGH);
 delay(3000);
 //Gira o Motor no sentido anti-horario
 digitalWrite(IN1, LOW);
 digitalWrite(IN2, HIGH);
 delay(2000);
}
```
