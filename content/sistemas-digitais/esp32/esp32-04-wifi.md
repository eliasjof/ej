---
title: "ESP32 #04 - Wifi"
date: 2020-06-09
categories:
  - esp32
  - projensino-esp32
tags:
  - esp32
---

Assista ao vídeo e veja como modificar o estado de um led conectado ao ESP32 por meio da conexão Wifi.

https://youtu.be/psgIQTcxpWI

## Código utilizado no vídeo

```cpp
unsigned long currentTime = millis();
unsigned long previousTime = 0; 
const long timeoutTime = 2000;

void setup() {
    Serial.begin(115200);

    pinMode(LED, OUTPUT);
    ledcSetup (canal, freq, resolucao);
    ledcAttachPin(LED, canal);    

    // Iniciam a conexão WiFI
    Serial.print("Connecting to ");
    Serial.println(ssid);
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
    }
    // Imprime o endereço de IP no Serial Monitor
    Serial.println("");
    Serial.println("WiFi connected.");
    Serial.println("IP address: ");
    Serial.println(WiFi.localIP());
    server.begin();
}

void loop(){
    WiFiClient client = server.available();   

    if (client) {                             // Quando um novo cliente acessa a página
    currentTime = millis();
    previousTime = currentTime;
    Serial.println("Novo Cliente.");          
    String currentLine = "";                
    while (client.connected() && currentTime - previousTime <= timeoutTime) {  
        currentTime = millis();
        if (client.available()) {             
        char c = client.read();             
        Serial.write(c);                    
        header += c;
        if (c == '\n') {                    
            if (currentLine.length() == 0) {                      
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println("Connection: close");
            client.println();
            
            //Comandos
            if (header.indexOf("GET /LED/on") >= 0) {
                Serial.println("LED ligado");
                botaoled = "on";
                ledcWrite (canal, 256);
                } 
                else if (header.indexOf("GET /LED/off") >= 0) {
                Serial.println("LED desligado");
                botaoled = "off";
                ledcWrite (canal, 0);
            } 
            else if (header.indexOf("GET /pwm/up") >= 0) {
                Serial.println("PWM ligado");
                botaopwm = "on";

                for (i = 0; i < 256; i++)
                {
                ledcWrite (canal, i);
                delay(10);
                }
            } else if (header.indexOf("GET /pwm/down") >= 0) {
                Serial.println("PWM desligado");
                botaopwm = "off";

                for (i = 256; i >= 0; i--)
                {
                ledcWrite (canal, i);
                delay(10);
                }
            }
            
            // Display HTML 
            client.println("<!DOCTYPE html><html>");
            client.println("<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">");
            client.println("<link rel=\"icon\" href=\"data:,\">");
            //Botão on e off
            client.println("<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}");
            client.println(".button { background-color: #4CAF50; border: none; color: white; padding: 16px 40px;");
            client.println("text-decoration: none; font-size: 30px; margin: 2px; cursor: pointer;}");
            client.println(".button2 {background-color: #555555;}</style></head>");
            
            // Título da pagina
            client.println("<body><h1>ESP32 Web Server</h1>");
            
            // Botão ON/OFF do LED  
            client.println("<p>LED </p>");
            if (botaoled =="off") {
                client.println("<p><a href=\"/LED/on\"><button class=\"button\">ON</button></a></p>");
            } else {
                client.println("<p><a href=\"/LED/off\"><button class=\"button button2\">OFF</button></a></p>");
            } 
                
            // Botão UP/DOWN PWM
            client.println("<p>PWM Variavel</p>");
            if (botaopwm =="off") {
                client.println("<p><a href=\"/pwm/up\"><button class=\"button\">UP</button></a></p>");
            } else {
                client.println("<p><a href=\"/pwm/down\"><button class=\"button button2\">DOWN</button></a></p>");
            }
            client.println("</body></html>");
            
            
            client.println();
            
            break;
            } else { // Se recebeu uma nova linha, limpe a currentLine
            currentLine = "";
            }
        } else if (c != '\r') {  // Se tiver mais alguma informação que não seja variavel de retorno
            currentLine += c;      // Ela será adicionada ao final da linha
        }
        }
    }
    // Limpa a variável 
    header = "";
    // Interrompe a conexão cliente
    client.stop();
    Serial.println("Cliente desconectado.");
    Serial.println("");
    }
}
```
