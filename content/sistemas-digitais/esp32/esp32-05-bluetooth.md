---
title: "ESP32 #05 - Bluetooth"
date: "2020-08-06"
categories: 
  - "esp32"
  - "projensino-esp32"
---

Como acionar um led via Bluetooth com o ESP32? Assista ao vídeo explicativo.

https://youtu.be/dGnVKryN5\_o

## Código utilizado

```cpp
#define BLYNK_PRINT Serial
#define BLYNK_USE_DIRECT_CONNECT
#include <BlynkSimpleEsp32_BLE.h>
#include <BLEDevice.h>
#include <BLEServer.h>
// You should get Auth Token in the Blynk App.
// Go to the Project Settings (nut icon).
char auth[] = "YourAuthToken";
void setup()
{
  // Debug console
  Serial.begin(115200);
  Serial.println("Waiting for connections...");
  Blynk.setDeviceName("ESP_32");
  Blynk.begin(auth);
}
void loop()
{
  Blynk.run();
}

```
