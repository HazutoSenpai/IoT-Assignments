# IoT Data Archiving: Using Function Bindings for Blob Storage

## 1. Output Binding Configuration
To store incoming telemetry (like GPS or sensor data) into a storage account, the `function.json` must be configured with a `blob` type output binding. This tells Azure to take the return value of the function and save it as a file.

**`function.json` Configuration:**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventHubTrigger",
      "name": "event",
      "direction": "in",
      "eventHubName": "iot-hub-events",
      "connection": "IOTHUB_CONNECTION_STRING",
      "cardinality": "one",
      "consumerGroup": "$Default"
    },
    {
      "type": "blob",
      "name": "outputBlob",
      "path": "telemetry-archive/{sys.datetime}.json",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    }
  ]
}
