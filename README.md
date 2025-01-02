# Chrome-Extension-FEtch-Webhooks-from-an-API
 a quick chrome extension. Basically, a software I use sends webhooks to discord or whatever endpoint, possibly my database on php admin.

Extension will fetch the webhook data quickly and load URL in browser with a click or automatically foreach parameter in a new window. That is it. Fetch data via API. Load it in browser with some basic logic and functionality.
---------
To create a quick Chrome extension that fetches webhook data from an API and loads the URL in the browser automatically or when clicked, you can follow these steps:

    Create the Manifest File (manifest.json)
    Create Background Script to handle fetching API data (via webhook).
    Create a Popup or Action Button to trigger opening URLs.
    Handle fetching and opening URLs based on webhook data.
    Use storage to store the webhook data temporarily.

Below is the code for the Chrome extension:
1. manifest.json

This is the configuration file for the Chrome extension. It includes permissions to access APIs and interact with tabs.

{
  "manifest_version": 3,
  "name": "Webhook URL Opener",
  "version": "1.0",
  "description": "Fetch webhook data and open URLs in the browser.",
  "permissions": [
    "storage",
    "activeTab",
    "tabs",
    "https://*/"  // Allow access to all URLs, can be restricted to specific domains
  ],
  "background": {
    "service_worker": "background.js"
  },
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "icons/icon16.png",
      "48": "icons/icon48.png",
      "128": "icons/icon128.png"
    }
  },
  "host_permissions": [
    "http://*/*",
    "https://*/*"
  ]
}

2. background.js

The background script is responsible for fetching the webhook data from the endpoint and saving it to local storage.

chrome.runtime.onInstalled.addListener(() => {
  console.log("Webhook URL Opener Extension Installed");
});

// Function to fetch the webhook data
async function fetchWebhookData() {
  try {
    const response = await fetch('https://your-webhook-endpoint.com/api/webhook'); // Replace with your webhook URL
    const data = await response.json();

    // Store the data temporarily in Chrome's local storage
    chrome.storage.local.set({ webhookData: data });

    console.log("Webhook data fetched", data);
  } catch (error) {
    console.error("Error fetching webhook data", error);
  }
}

// Trigger fetching the webhook data at regular intervals or on demand
setInterval(fetchWebhookData, 10000); // Fetch data every 10 seconds (adjust as needed)

3. popup.html

This popup file will appear when you click the extension icon in the browser. It provides a button to trigger opening the URLs.

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Webhook URL Opener</title>
  <style>
    body {
      width: 200px;
      padding: 10px;
    }
    button {
      width: 100%;
      padding: 10px;
      background-color: #4CAF50;
      color: white;
      border: none;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h3>Open URL</h3>
  <button id="open-url-btn">Open URL from Webhook</button>

  <script>
    document.getElementById('open-url-btn').addEventListener('click', async () => {
      // Fetch the latest webhook data from storage
      chrome.storage.local.get('webhookData', function(data) {
        if (data.webhookData) {
          // Logic to open each URL from the webhook data in a new window
          const urls = data.webhookData.urls || []; // Assuming the webhook response contains a "urls" array
          urls.forEach(url => {
            chrome.tabs.create({ url: url });
          });
        } else {
          alert("No webhook data available.");
        }
      });
    });
  </script>
</body>
</html>

4. Optional: icons/

You can add some icons (e.g., icon16.png, icon48.png, icon128.png) to represent the extension in the Chrome toolbar. These icons are referenced in the manifest.json under the default_icon section.
5. Loading and Testing the Extension

    Go to chrome://extensions/ in your Chrome browser.
    Enable Developer mode.
    Click on Load unpacked and select the folder where your extension files are located.
    The extension icon should appear in the browser toolbar.
    Click the extension icon to open the popup, and click the "Open URL from Webhook" button to trigger the URLs being opened in new tabs.

Explanation:

    background.js: Handles fetching the data from your API endpoint or webhook URL and storing it in Chrome's local storage.
    popup.html: Displays a simple popup with a button. When clicked, it fetches the webhook data from storage and opens the URLs in new tabs.
    Webhook data format: This extension assumes that the webhook returns an object with an array of URLs under webhookData.urls. You can modify the code to suit your API's response structure.

Customizations:

    Change the Webhook Endpoint: Replace https://your-webhook-endpoint.com/api/webhook in background.js with the actual endpoint you're receiving the webhook data from.
    Modify Data Structure: Adjust the code if the data from the webhook is structured differently. You may need to parse the data accordingly before opening the URLs.

Final Considerations:

    Ensure that your API endpoint supports CORS (Cross-Origin Resource Sharing) if you're fetching data from a remote domain.
    Use secure connections (HTTPS) for API calls and data handling.
    Add additional error handling and validation to ensure robustness, especially when dealing with unpredictable webhook data.
