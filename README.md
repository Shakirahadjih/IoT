# Motivation Mouse

![SurfingMouse banner](https://github.com/Shakirahadjih/IoT/blob/main/styles/images/SurfingMouse-banner.png)

## Introduction

In this manual we will try to connect an API (windy) with ESP8266. Windy is a wind and waves forecast for kiters, surfers and anyone else. The goal of this manual is printing the data that's coming from the API to the Arduino Serial Monitor.


<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#Necessities">Necessities</a>
    </li>
    <li>
      <a href="#Step_1">Step 1: Get your API Key</a>
      <ul>
        <li><a href="#Step_1">Prerequisites</a></li>
        <li><a href="#Step_1">Installation</a></li>
      </ul>
    </li>
        <li>
      <a href="#Necessities">Control the API using Postman</a>
    </li>
    <li><a href="#Step_1">Usage</a></li>
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

**Error**

When sending this request i got the following error:

Invalid request - "key", "model", "lat", "lon" or "parameters" are missing.

I've tried making a new key, trying a local host URL and sending a POST request instead of a GET request. 
After struggeling for a while I decided to skip this step for now. I figured that maybe it would work in my code for some magical reason. 
So onto the next step we go.

## Step 3: Test the API using Postman

<!-- ROADMAP -->
## Documentation

Look at the [wiki](https://github.com/Shakirahadjih/IoT/wiki) or refer to my [design documentation](https://github.com/Shakirahadjih/IoT/blob/main/Ontwerpdocu%20-%20IoT.pdf) for my UX documentation during this project. 
