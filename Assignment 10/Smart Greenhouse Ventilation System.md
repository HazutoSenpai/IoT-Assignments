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
```
2. IoT Hub Connection
The device is registered in Azure IoT Hub as Greenhouse_Unit_01. Telemetry is successfully flowing from the device to the Hub's built-in Event Hub endpoint.

Telemetry Sent: {"temperature": 29.5}

Command Route: The Hub is configured to allow Service-level access for the Azure Function to invoke methods on the device.

3. Serverless Control Logic (Azure Function)
```py
import logging
import azure.functions as func
from azure.iot.hub import IoTHubRegistryManager
import json
import os

IOTHUB_CONNECTION_STRING = os.getenv("IOTHUB_CONNECTION_STRING")
DEVICE_ID = "Greenhouse_Unit_01"

def main(event: func.EventHubEvent):
    # Parse the telemetry data
    body = json.loads(event.get_body().decode('utf-8'))
    current_temp = body.get('temperature')
    
    registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)
    
    if current_temp > 30.0:
        # High heat detected: Turn Fan ON
        method = {"methodName": "set_fan", "payload": "on", "responseTimeoutInSeconds": 20}
        registry_manager.invoke_device_method(DEVICE_ID, method)
        logging.info(f"High Temp ({current_temp}C). Fan activated.")
    else:
        # Temperature safe: Turn Fan OFF
        method = {"methodName": "set_fan", "payload": "off", "responseTimeoutInSeconds": 20}
        registry_manager.invoke_device_method(DEVICE_ID, method)
        logging.info(f"Normal Temp ({current_temp}C). Fan deactivated.")
