# LED-Via-Telegram
By Koen Dekker Last updated 3 October 2023

## Introduction
Have you ever wanted to control your LEDs through the internet? Well, now you can! With this manual, we will attempt to create a controllable LED strip using Telegram!

## Table of contents

1. What do you need?
2. Building your controller
3. Conclusion
4. Media

## What do you need?
You will need a variety of items to build this controller:
1. A smartphone
2. [NodeMCU Arduino Board, ESP8266](https://www.hobbyelectronica.nl/product/nodemcu-lua-wifi-board-esp8266-cp2102-module/)
3. A (RGB) LED strip
4. [Arduino IDE](https://www.arduino.cc/en/software)
5. A stable WIFI connection

## Building your controller
### Step 1: Create a Telegram Bot
The first thing we are going to do is to make a bot using Telegram. The ESP8266 board will interact with the Telegram bot to receive and handle the messages, and send responses.

Install Telegram from your app store, I will be using an Android smartphone. Follow the instructions on your screen.

![image](https://github.com/Kvdekker/LED-Via-Telegram/assets/96053886/926edf21-e33c-41aa-8012-736176eff9c9)


_Didn't you receive the code while trying to set up Telegram? Wait until the timer runs out, then click on 'Call me' using the provided number. Within 5 seconds, you will receive a call, and your code will be spoken to you._

Go to the magnifying glass icon at the top right and search for **BotFather**. You should see the bot as shown in the picture below.

![image](https://github.com/Kvdekker/LED-Via-Telegram/assets/96053886/049280d9-6591-4e0d-a644-91d7e3660a5b)



## Complications Step 1
