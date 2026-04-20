relay_off/__init__.py:
```py
import azure.functions as func
from azure.iot.hub import IoTHubRegistryManager
import os

IOTHUB_CONNECTION_STRING = os.getenv("IOTHUB_CONNECTION_STRING")
DEVICE_ID = os.getenv("IOTHUB_DEVICE_ID")
def main(req: func.HttpRequest) -> func.HttpResponse:
    registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)
    device_method = {"methodName": "toggle_relay", "payload": "off", "responseTimeoutInSeconds": 30}
    registry_manager.invoke_device_method(DEVICE_ID, device_method)
    return func.HttpResponse("Relay OFF command sent successfully.", status_code=200)
