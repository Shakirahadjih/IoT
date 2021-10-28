# Connect an API (windy) with ESP8266

![SurfingMouse banner](https://raw.githubusercontent.com/jtlai0921/API/master/assets/intro4.gif)

## Introduction

In this manual we will try to connect an API (windy) with ESP8266. Windy is a wind and waves forecast for kiters, surfers and anyone else. The goal of this manual is printing the data that's coming from the API to the Arduino Serial Monitor.


<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#Necessities">Necessities</a></li>
    <li><a href="#Step_1">Step 1: Get your API Key</a></li>
    <li><a href="#Step_2">Step 2: Test the API using Postman</a></li>
    <li><a href="#Step_3">Step 3: Make a connection to the API on the Arduino</a></li>
    <li><a href="#Step_4">Step 4: Get everything ready</a></li>
    <li><a href="#Step_5">Step 5: Upload the code to your Arduino board</a></li>
    <li><a href="#Other ways">Other ways</a></li>
    <li><a href="#Documentation">Documentation</a></li>
  </ol>
</details>

## Necessities
- 1x Arduino ESP8266 Board

## Step 1: Get your API Key
We use Windy to get our data, because windy is one of the most reliable API's with a great documentation to help us in our project. To get started we need to go to https://api.windy.com/keys to create a new key. Make a Windy account first, if you didn't already have one. 
Once you have made your account, you can create an API key by clicking the button.

Make sure u choose **Point Forecast API**.
I filled in the specs like this. These don't really matter for us, it's more for windy to check how we use their data.

![Windy API Key](https://github.com/Shakirahadjih/IoT/blob/main/styles/images/windy-1.png)

Make sure you save the API key.

## Step 2: Test the API using Postman

Before we start programming in Arduino we want to check the functionality of the API. To do this we use Postman.

1. Go to https://www.postman.com/ and create an account.
2. Go to your Workspace
3. Click "New"
4. Select "HTTP Request"

![Visualisation of steps](https://github.com/Shakirahadjih/IoT/blob/main/styles/images/Schermafbeelding%202021-10-27%20om%2023.45.28.png)

Now we want to fill in the request we just created.

1. Send GET request to the URL: https://api.windy.com/api/point-forecast/v2
2. Fill in your API Key

When you click Send, you should get all the data back.

**Error**

When sending this request i got the following error:

Invalid request - "key", "model", "lat", "lon" or "parameters" are missing.

I've tried making a new key, trying a local host URL and sending a POST request instead of a GET request. 
After struggeling for a while I decided to skip this step for now. I figured that maybe it would work in my code for some magical reason. 
So onto the next step we go.

## Step 3: Make a connection to the API on the Arduino

Before we start making a Library, we should test out that you can make a connection to the API and can get the data back on the Arduino. We do this because not all  API's are easy to work with, especially on a free trial.

To do this we need to create a new sketch in Arduino. Then paste in the following code:

```C++

    Written by Brian Lough
    https://github.com/witnessmenow/arduino-sample-api-request/blob/master/ESP8266/HTTP_GET_JSON/HTTP_GET_JSON.ino
 *******************************************************************/


// ----------------------------
// Standard Libraries
// ----------------------------

#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>

// ----------------------------
// Additional Libraries - each one of these will need to be installed.
// ----------------------------

#include <ArduinoJson.h>
// Library used for parsing Json from the API responses

// Search for "Arduino Json" in the Arduino Library manager
// https://github.com/bblanchon/ArduinoJson

//------- Replace the following! ------
char ssid[] = "SSID";       // your network SSID (name)
char password[] = "password";  // your network key

// For Non-HTTPS requests
// WiFiClient client;

// For HTTPS requests
WiFiClientSecure client;


// Just the base of the URL you want to connect to
#define TEST_HOST "api.coingecko.com"

// OPTIONAL - The finferprint of the site you want to connect to.
#define TEST_HOST_FINGERPRINT "89 25 60 5D 50 44 FC C0 85 2B 98 D7 D3 66 52 28 68 4D E6 E2"
// The finger print will change every few months.


void setup() {

  Serial.begin(115200);

  // Connect to the WiFI
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  delay(100);

  // Attempt to connect to Wifi network:
  Serial.print("Connecting Wifi: ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }
  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  IPAddress ip = WiFi.localIP();
  Serial.println(ip);

  //--------

  // If you don't need to check the fingerprint
  // client.setInsecure();

  // If you want to check the fingerprint
  client.setFingerprint(TEST_HOST_FINGERPRINT);

  makeHTTPRequest();
}

void makeHTTPRequest() {

  // Opening connection to server (Use 80 as port if HTTP)
  if (!client.connect(TEST_HOST, 443))
  {
    Serial.println(F("Connection failed"));
    return;
  }

  // give the esp a breather
  yield();

  // Send HTTP request
  client.print(F("GET "));
  // This is the second half of a request (everything that comes after the base URL)
  client.print("/api/v3/simple/price?ids=ethereum%2Cbitcoin&vs_currencies=usd%2Ceur"); // %2C == ,
  client.println(F(" HTTP/1.1"));

  //Headers
  client.print(F("Host: "));
  client.println(TEST_HOST);

  client.println(F("Cache-Control: no-cache"));

  if (client.println() == 0)
  {
    Serial.println(F("Failed to send request"));
    return;
  }
  //delay(100);
  // Check HTTP status
  char status[32] = {0};
  client.readBytesUntil('\r', status, sizeof(status));
  if (strcmp(status, "HTTP/1.1 200 OK") != 0)
  {
    Serial.print(F("Unexpected response: "));
    Serial.println(status);
    return;
  }

  // Skip HTTP headers
  char endOfHeaders[] = "\r\n\r\n";
  if (!client.find(endOfHeaders))
  {
    Serial.println(F("Invalid response"));
    return;
  }

  // This is probably not needed for most, but I had issues
  // with the Tindie api where sometimes there were random
  // characters coming back before the body of the response.
  // This will cause no hard to leave it in
  // peek() will look at the character, but not take it off the queue
  while (client.available() && client.peek() != '{')
  {
    char c = 0;
    client.readBytes(&c, 1);
    Serial.print(c);
    Serial.println("BAD");
  }

  //  // While the client is still availble read each
  //  // byte and print to the serial monitor
  //  while (client.available()) {
  //    char c = 0;
  //    client.readBytes(&c, 1);
  //    Serial.print(c);
  //  }

  //Use the ArduinoJson Assistant to calculate this:

  //StaticJsonDocument<192> doc;
  DynamicJsonDocument doc(192); //For ESP32/ESP8266 you'll mainly use dynamic.

  DeserializationError error = deserializeJson(doc, client);

  if (!error) {
    float ethereum_usd = doc["ethereum"]["usd"]; // 3961.66
    float ethereum_eur = doc["ethereum"]["eur"]; // 3261.73

    long bitcoin_usd = doc["bitcoin"]["usd"]; // 48924
    long bitcoin_eur = doc["bitcoin"]["eur"]; // 40281

    Serial.print("ethereum_usd: ");
    Serial.println(ethereum_usd);
    Serial.print("ethereum_eur: ");
    Serial.println(ethereum_eur);

    Serial.print("bitcoin_usd: ");
    Serial.println(bitcoin_usd);
    Serial.print("bitcoin_eur: ");
    Serial.println(bitcoin_eur);
    
  } else {
    Serial.print(F("deserializeJson() failed: "));
    Serial.println(error.f_str());
    return;
  }

}

void loop() {
  // put your main code here, to run repeatedly:

}
```

**Fill in the code with your information**

Then start filling in the following snippets in your code;
1. #define TEST_HOST "api.windy.com" 
2. #define TEST_HOST_FINGERPRINT ""

To achieve the fingerprint we need to open the api request in chrome. Go to https://api.windy.com/api/point-forecast/your-api-key. Click the little lock that's      next to the url on the left. Then click "Verbinding is beveiligd", "Certificaat is geldig", "Details", and at the end you will find your SHA-1 key
At this moment I started to give up a little bit already. Because now we are starting to fill out information of a source that gives us nothing but errors.

3. char ssid[] = "SSID";       // your network SSID (name)
4. char password[] = "password";  // your network key
5. client.print("/api/point-forecast/v2");

## Step 4: Get everything ready

**Install the needed libraries**

Now we tested everything (normally what we have tested should have gone right) it's time to get everything ready to upload the code to the board.

First, if you don't already have this, install Json.
- Go to Tools
- Manage libraries...
- Search for "Arduino Json" in the Arduino Library manager.
- Install Arduino Json by blanchon (// https://github.com/bblanchon/ArduinoJson)

![Install json](https://github.com/Shakirahadjih/IoT/blob/main/styles/images/Schermafbeelding%202021-10-28%20om%2007.16.59.png)

**Connect the board to your computer**

Simply plug in your board in your Arduino. Make sure you are connected to the right port.

![Right port for ESP](https://github.com/Shakirahadjih/IoT/blob/main/styles/images/Schermafbeelding%202021-10-28%20om%2007.26.37.png)

## Upload the code to your Arduino board

Now that everyting is set it's time to upload the code to your board. What should happen is that you get the API Data in your monitor. Open the Serial Monitor (Shift Command M or Tools > Serial Monitor) and here you will either stumble upon errors or your API data. 

I only got showed dots, ......., in the Serial Monitor. Which means that there is a connection to the wifi, but apparently there is no connection to the API. As I expected. 

## Other ways

I thought that it should be simple to send a HTTP request using Arduino. There are various other ways than this one. For us the fingerprint is not really necessary. The fingerprint is a safety, which is important if you are playing with users data or other commands that could result into something bad.
In the previous code we also never did anything with our API Key. 
So we could make the code simpeler.

According to Windy we should send a POST request with the following body

```
{
    "lat": 49.809,
    "lon": 16.787,
    "model": "desired_model",
    "parameters": ["desired_parameter_1", "desired_parameter_2", ...],
    "levels": ["optionally_desired_gh_level_1", ...],
    "key": "your_API_key"
}
``` 

<!-- ROADMAP -->
## Documentation

Look at the [wiki](https://github.com/Shakirahadjih/IoT/wiki) or refer to my [design documentation](https://github.com/Shakirahadjih/IoT/blob/main/Ontwerpdocu%20-%20IoT.pdf) for my UX documentation during this project. 

## Sources
https://github.com/witnessmenow/arduino-sample-api-request/blob/master/ESP8266/HTTP_GET_JSON/HTTP_GET_JSON.ino
https://www.youtube.com/watch?v=HUjFMVOpXBM
https://api.windy.com/point-forecast/docs
https://maakbaas.com/esp8266-iot-framework/logs/https-requests/
https://forum.arduino.cc/t/api-get-on-arduino-uno-connected-to-esp8266/468412/7
https://www.w3schools.com/tags/ref_httpmethods.asp
