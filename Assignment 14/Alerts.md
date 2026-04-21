# Smart Transportation: Automated SMS Geofence Alerts

## 1. Twilio Output Binding Configuration
To send an SMS without writing custom API client code, we configure a Twilio output binding in the `function.json`. This binding uses the Twilio Account SID and Auth Token stored in the Function App's Application Settings.

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
      "type": "twilioSms",
      "name": "message",
      "accountSidSetting": "TwilioAccountSid",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+15551234567",
      "to": "+15559876543",
      "direction": "out"
    }
  ]
}
