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
We use Windy to get our data, because windy is one of the most reliable API's with a great documentation to help us in our project. To get started we need to go to https://api.windy.com/keys to create a new key. Make a Windy account first, if you did'nt already have one. So, once you have made your account, you can create an Api key by clicking the button.

Make sure u choose **Point Forecast API**.
I filled in the specs like this. These don't really matter for us, it's more for windy to check how we use their data.

![Windy API Key](https://github.com/Shakirahadjih/IoT/blob/main/styles/images/windy-1.png)

Make sure you save the API key.

## Step 2: Test the API using Postman

<!-- ROADMAP -->
## Documentation

Look at the [wiki](https://github.com/Shakirahadjih/IoT/wiki) or refer to my [design documentation](https://github.com/Shakirahadjih/IoT/blob/main/Ontwerpdocu%20-%20IoT.pdf) for my UX documentation during this project. 
