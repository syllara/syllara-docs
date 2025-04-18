# Syllara API Integration - Funcaptcha Solver

This document outlines how to integrate with the Syllara API to solve Funcaptcha challenges.  It provides code examples and explanations based on the provided Python script.

## Prerequisites

*   **Syllara API Key:**  You need a valid API key generated by the Syllara admin endpoint.  Replace `"SYLLARA-BALANCE-"` with your actual key.
*   **Python:**  Python 3.6 or higher is required.
*   **`requests` Library:** Install the `requests` library using pip:
    ```bash
    pip install requests
    ```

## API Endpoint

The API endpoint for solving Funcaptcha challenges is:

```
https://syllara.com/api/solve/funcaptcha
```

## Code Example (Python)

```python
import requests
import json

api_url = "https://syllara.com/api/solve/funcaptcha"
api_key = "YOUR_SYLLARA_API_KEY"

challengeInfo = {
    "public_key": "85800716-F435-4981-864C-8B90602D10F7",
    "website_url": "https://syllara.com",
    "service_url": "https://syllara.com",
    "capi_mode": "lightbox",
    "style_theme": "default",
    "language_enabled": False,
    "jsf_enabled": True,
    "ancestor_origins": ["https://syllara.com"],
    "tree_index": [5],
    "tree_structure": "[[[]],[],[],[[]],[],[],[],[],[],[],[]]",
    "location_h_ref": "https://syllara.com",
    "site": "syllara.com",
}

browserInfo = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36",
    "Cookie": "",
}

proxy = None

payload = {
    "api_key": api_key,
    "challenge_info": challengeInfo,
    "browser_info": browserInfo,
    "proxy": proxy,
}

print(f"Sending request to: {api_url}")

try:
    response = requests.post(api_url, json=payload, timeout=120)

    print(f"\nStatus Code: {response.status_code}")

    try:
        response_data = response.json()
        print("\nResponse JSON:")
        print(json.dumps(response_data, indent=2))

        if response.ok and response_data.get("solution"):
            print("\nSuccessfully received solution from API.")
        elif not response.ok:
             print(f"\nAPI returned an error: {response_data.get('error', 'Unknown error')}")

    except json.JSONDecodeError:
        print("\nResponse Content (not JSON):")
        print(response.text)

except requests.exceptions.RequestException as e:
    print(f"\nAn error occurred during the request: {e}")
```

## Explanation

1.  **Import Libraries:**
    *   `requests`:  Used for making HTTP requests to the API.
    *   `json`:  Used for handling JSON data.

2.  **Define Variables:**
    *   `api_url`: The URL of the Syllara API endpoint.
    *   `api_key`:  Your unique API key for authentication. **Replace `"YOUR_SYLLARA_API_KEY"` with your actual key.**
    *   `challengeInfo`: A dictionary containing information about the Funcaptcha challenge. The `website_url`, `service_url`, `ancestor_origins`, `location_h_ref`, and `site` are all set to `https://syllara.com` for example purposes. **These values must be obtained from the Syllara.com website's HTML source code and adjusted to match.**
    *   `browserInfo`: A dictionary containing information about the user's browser, including the User-Agent and Cookies (optional).
    *   `proxy`:  An optional proxy server configuration. Set to `None` if you don't need a proxy.

3.  **Construct Payload:**
    *   The `payload` dictionary is created to hold the data sent to the API.  It includes the `api_key`, `challenge_info`, `browser_info`, and `proxy`.

4.  **Make the API Request:**
    *   `requests.post(api_url, json=payload, timeout=120)`:  Sends a POST request to the API endpoint with the payload as JSON data.  A `timeout` of 120 seconds is set to prevent the script from hanging indefinitely.

5.  **Handle the Response:**
    *   `response.status_code`:  Prints the HTTP status code of the response (e.g., 200 for success, 400 for bad request, 500 for server error).
    *   **JSON Parsing:**  The code attempts to parse the response as JSON using `response.json()`.
        *   If successful, it prints the formatted JSON response.
        *   It then checks if the request was successful and if the `solution` key is present in the response data. If the request was unsuccessful, it prints an error message.
    *   **Non-JSON Handling:** If the response cannot be parsed as JSON, the code prints the raw response content.
    *   **Error Handling:** A `try...except` block catches `requests.exceptions.RequestException` errors and prints an error message.

## Challenge Information (`challengeInfo`)

The `challengeInfo` dictionary is critical for the API to solve the Funcaptcha.  It must contain accurate information about the challenge presented on the target website. **The example values provided are illustrative.  You MUST inspect the HTML source code of `syllara.com` where the Funcaptcha is implemented to extract the CORRECT and CURRENT values for parameters like `public_key`, `service_url`, and any other challenge-specific settings.**

Key parameters include:

*   `public_key`:  The Funcaptcha public key (often found as part of the `data-pkey` attribute).
*   `website_url`: The URL of the website where the Funcaptcha is displayed (should be `https://syllara.com`).
*   `service_url`:  The URL of the Arkose Labs service used for the Funcaptcha. **Important:** This is often a different domain than the `website_url`, however in this example it is set to the same domain for demonstration purposes.

## Browser Information (`browserInfo`)

The `browserInfo` dictionary provides information about the user's browser.

*   `User-Agent`:  A string identifying the browser and operating system. Use a valid User-Agent string to avoid being blocked.
*   `Cookie`:  Any relevant cookies that are needed for the challenge.  This is often empty, but in some cases, specific cookies may be required.

## Proxy Configuration (Optional)

If you need to use a proxy server, set the `proxy` variable to a string in the format `http://user:pass@host:port` or `http://host:port`.  If no proxy is needed, leave the `proxy` variable set to `None`.

## Error Handling

The script includes basic error handling for network issues and API responses.  It's important to implement robust error handling in your production code to handle various scenarios, such as:

*   Invalid API key
*   Incorrect `challengeInfo` parameters
*   Network connectivity issues
*   Insufficient balance on your Syllara account

## Response Interpretation

The API response is a JSON object.  A successful response will typically include a `solution` field.  An error response will include an `error` field with a description of the problem.

```json
// Successful response
{
  "status": "success",
  "solution": "your_funcaptcha_token"
}

// Error response
{
  "status": "error",
  "error": "Invalid API key"
}
```
