# Project: Smart Greenhouse Ventilation System

## 1. IoT Device Code (ESP32 + DHT11 + DC Fan)

```cpp
#include <WiFi.h>
#include <AzureIoTHub.h>
#include <DHT.h>

#define DHTPIN 4
#define FAN_PIN 5
DHT dht(DHTPIN, DHT11);

void setup() {
  pinMode(FAN_PIN, OUTPUT);
  dht.begin();
  // WiFi and IoT Hub connection logic here...
}

void loop() {
  float temp = dht.readTemperature();
  
  // Send Telemetry
  String payload = "{\"temperature\": " + String(temp) + "}";
  sendTelemetry(payload); 
  
  delay(10000); // Send every 10 seconds
}

// Callback for commands from Azure Function
int handle_method(const char* method_name, const char* payload) {
  if (strcmp(method_name, "set_fan") == 0) {
    if (strstr(payload, "on")) digitalWrite(FAN_PIN, HIGH);
    else digitalWrite(FAN_PIN, LOW);
    return 200;
  }
  return 404;
}
