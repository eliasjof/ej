---
title: "ESP32 #03 - Sinal PWM + LED"
date: "2020-06-02"
categories: 
  - "esp32"
  - "projensino-esp32"
---

Confira como acionar um led utilizando um sinal PWM no vídeo abaixo:

https://www.youtube.com/watch?v=vkiDhCImEtk

## Código utilizado no vídeo

```cpp
#include <Wire.h>
//Código da experiencia "luz pulsante"
int LED = 2;
int canal = 0;
int resolucao = 8; //8 bits
int i; //parametro referente ao duty cycle
int freq = 1000; //Dada em Hz
void setup() 
{
  pinMode (LED, OUTPUT);
  ledcSetup (canal, freq, resolucao);
  ledcAttachPin(LED, canal);
}
void loop() 
{
  /*o valor utilizado é a representacao em decimal da resolucao,
  ou seja, 2^8 = 256*/
  for (i = 0; i < 256; i++)
  {
      ledcWrite (canal, i);
      delay(5);
  }
  for (i = 255; i > 0; i--)
  {
      ledcWrite (canal, i);
      delay(5);
  }
}
```
