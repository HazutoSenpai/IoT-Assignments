Assignment 7
## Part 1: Moisture Data

| Total Pump Time | Soil Moisture Reading | Decrease in Reading |
| :--- | :--- | :--- |
| Dry (0s) | 780 | 0 |
| 1s | 752 | 28 |
| 2s | 728 | 24 |
| 3s | 702 | 26 |
| 4s | 675 | 27 |
| 5s | 650 | 25 |
| 6s | 622 | 28 |

## Part 2: Server Code

### 1. Average Decrease

* **Total decrease over 6 seconds:** $28 + 24 + 26 + 27 + 25 + 28 = 158$
* **Average decrease per second:** $158 / 6 \approx \mathbf{26.3}$

### 2. Server Logic Update

```python
import time

MOISTURE_CALIBRATION_FACTOR = 26.17
TARGET_MOISTURE_VALUE = 520
MAX_PUMP_DURATION = 10.0

def process_irrigation_request(telemetry_data):
    current_moisture = telemetry_data.get('soil_moisture')
    
    if current_moisture <= TARGET_MOISTURE_VALUE:
        return {"action": "PUMP_OFF", "duration": 0, "reason": "Target reached"}

    moisture_deficit = current_moisture - TARGET_MOISTURE_VALUE
    required_seconds = moisture_deficit / MOISTURE_CALIBRATION_FACTOR
    duration = min(round(required_seconds, 2), MAX_PUMP_DURATION)
    
    return {
        "action": "PUMP_ON",
        "duration": duration,
        "msg": f"Calculated {duration}s to bridge {moisture_deficit} point deficit"
    }

telemetry_packet = {
    "device_id": "ESP32_STATION_01",
    "temp_c": 24.5,
    "gas_ppm": 112,
    "soil_moisture": 705
}

command = process_irrigation_request(telemetry_packet)
print(f"Server Command: {command}")
