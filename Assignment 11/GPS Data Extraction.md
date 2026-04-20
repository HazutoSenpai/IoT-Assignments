# Smart Transportation: Advanced GPS Data Extraction

## 1. NMEA Sentence Investigation
Standard GPS modules output multiple types of NMEA sentences. While `$GPRMC` provides latitude and longitude, we can extract specialized data from others:

* **$GPVTG (Track Made Good and Ground Speed):** Used to extract current speed in kilometers per hour (km/h).
* **$GPGGA (Global Positioning System Fix Data):** Used to extract Altitude (Mean Sea Level) and the number of satellites in view.
* **$GPRMC (Recommended Minimum):** Used to extract the current UTC Date and Time for system clock synchronization.

---

## 2. IoT Device Implementation (ESP32 + GPS Sensor)
This code demonstrates how to parse the `$GPRMC` sentence to synchronize the internal clock and extract speed data for telemetry.

```cpp
#include <TinyGPS++.h>
#include <HardwareSerial.h>

TinyGPSPlus gps;
HardwareSerial SerialGPS(1);

void setup() {
  Serial.begin(115200);
  SerialGPS.begin(9600, SERIAL_8N1, 16, 17); // GPS RX/TX pins
}

void loop() {
  while (SerialGPS.available() > 0) {
    if (gps.encode(SerialGPS.read())) {
      displayInfo();
    }
  }
}

void displayInfo() {
  if (gps.speed.isValid()) {
    float speedKmh = gps.speed.kmph();
    float altitudeM = gps.altitude.meters();
    
    // Telemetry payload including advanced GPS data
    String payload = "{\"speed\": " + String(speedKmh) + 
                     ", \"alt\": " + String(altitudeM) + 
                     ", \"sats\": " + String(gps.satellites.value()) + "}";
    
    sendTelemetry(payload);

    // Using GPS Time to sync system clock (GPS-based "NTP")
    if (gps.time.isValid()) {
      int hour = gps.time.hour();
      int minute = gps.time.minute();
      // Logic to set internal RTC would go here
    }
  }
}
