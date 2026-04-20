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
AVG_DECREASE_PER_SEC = 26.3
TARGET_MOISTURE = 500

def calculate_run_time(current_reading):
    if current_reading <= TARGET_MOISTURE:
        return 0
    deficit = current_reading - TARGET_MOISTURE
    return deficit / AVG_DECREASE_PER_SEC
