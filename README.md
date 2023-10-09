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

Click on the bot, then on start. You will receive a message. Type /newbot. Give your bot a name and a username (follow the instructions on your screen). If you are done, you should see the same as shown below:

![image](https://github.com/Kvdekker/LED-Via-Telegram/assets/96053886/015f9b5c-19d3-4017-a919-b22aecd07aa0)

Save the token; you will need it to enable the ESP8266 to interact with the bot.

### Step 2: Prepare you Telegram
Your bot's username is the key to interacting with your ESP8266 board. To ensure that messages from other sources are ignored, you can obtain your Telegram User ID. This ensures that when your Telegram bot receives a message, the ESP8266 can verify if the sender's ID matches your User ID, allowing it to process the message or ignore it accordingly.

Return to your Telegram home screen and search for IDBot. Click on the same one as shown in the picture below:

![image](https://github.com/Kvdekker/LED-Via-Telegram/assets/96053886/5a8a9584-20c2-4194-8625-5da7efedc0d2)

Click on the /getid command or type /getid. Copy the row of numbers that the bot has provided. We will need to use this code later in this manual.

_Unable to obtain the row of numbers? Return to your home screen, close the app and repeat step 2._

### Step 3: Prepare your Arduino IDE
For starters, make sure that you have the ESP8266 installed on your Arduino IDE.

_Follow this tutorial if you haven't already: https://randomnerdtutorials.com/how-to-install-esp8266-board-arduino-ide/_

Start a new sketch by clicking File -> New sketch

![image](https://github.com/Kvdekker/LED-Via-Telegram/assets/96053886/009f96cf-6424-4c19-85bf-77ea64174ab2)

Click here to download the [Bot library for Arduino](https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot/archive/master.zip).

After you've downloaded the zip file, open it in the Arduino IDE by selecting Sketch -> Include Library -> Add.ZIP Library. Wait until the terminal at the bottom of your screen indicates that it's installed. Double-check in the Library Manager if ArduinoJson by Benoit Blanchon is installed.

![image](https://github.com/Kvdekker/LED-Via-Telegram/assets/96053886/0ae80f84-c019-44c1-b098-e66a3f67d7c8)

If you're done with downloading the library and setting up your Arduino, you can proceed to step 3!
