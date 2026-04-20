`relay_on/__init__.py:`:
```json
import azure.functions as func
from azure.iot.hub import IoTHubRegistryManager
import os

# Connection string for the IoT Hub (Service Policy)
IOTHUB_CONNECTION_STRING = os.getenv("IOTHUB_CONNECTION_STRING")
DEVICE_ID = os.getenv("IOTHUB_DEVICE_ID")

def main(req: func.HttpRequest) -> func.HttpResponse:
    registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)
    
    # Define the command for the device
    # Here we send a direct method 'toggle_relay' with payload 'on'
    device_method = {"methodName": "toggle_relay", "payload": "on", "responseTimeoutInSeconds": 30}
    
    registry_manager.invoke_device_method(DEVICE_ID, device_method)

    return func.HttpResponse("Relay ON command sent successfully.", status_code=200)
