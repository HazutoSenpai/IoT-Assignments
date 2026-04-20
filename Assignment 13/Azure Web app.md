# Project Deployment: Azure Static Web Apps & Secure API Management

## 1. Web Application Overview
The web application is a dashboard that visualizes the GPS and sensor telemetry archived in previous assignments. It is hosted via **Azure Static Web Apps**, which automatically deploys the code directly from a GitHub repository.

* **Repository:** `https://github.com/username/iot-transport-dashboard`
* **Live URL:** `https://thankful-sea-0123456.azurestaticapps.net`

---

## 2. Secure Credential Management
Following the security best practices for SWA, the `subscriptionKey` (used to access the Maps API or IoT Hub data) has been removed from the frontend JavaScript and moved to the **Application Settings** in the Azure Portal.

### Refactored Code (frontend/app.js)
Instead of hardcoding the key, the app now calls a lightweight managed function (API) provided by SWA to fetch necessary data or tokens.

```javascript
// Before (Insecure):
// const apiKey = "1a2b3c4d5e6f7g8h9i0j"; 

// After (Secure):
async function getMapData() {
    // The key is hidden on the server-side/portal settings
    const response = await fetch('/api/getMapData');
    const data = await response.json();
    renderMap(data);
}
