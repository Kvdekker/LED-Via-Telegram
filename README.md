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

### Step 4: Set up your controller
To send a message to the ESP8266 board, we need to implement some code into your board.

Copy the code below:

             /*
         Rui Santos
         Complete project details at https://RandomNerdTutorials.com/telegram-control-esp32-esp8266-nodemcu-outputs/
         
         Project created using Brian Lough's Universal Telegram Bot Library: https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot
         Example based on the Universal Arduino Telegram Bot Library: https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot/blob/master/examples/ESP8266/FlashLED/FlashLED.ino
       */
       
       #ifdef ESP32
       #include <WiFi.h>
       #else
       #include <ESP8266WiFi.h>
       #endif
       #include <WiFiClientSecure.h>
       #include <UniversalTelegramBot.h>  // Universal Telegram Bot Library written by Brian Lough: https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot
       #include <ArduinoJson.h>
       #include <FastLED.h>
       // Replace with your network credentials
       const char* ssid = "xxx";
       const char* password = "xxx";
       
       // Initialize Telegram BOT
       #define BOTtoken "xxx"  // your Bot Token (Get from Botfather)
       
       // Use @myidbot to find out the chat ID of an individual or a group
       // Also note that you need to click "start" on a bot before it can
       // message you
       #define CHAT_ID "xxx"
       
       #ifdef ESP8266
       X509List cert(TELEGRAM_CERTIFICATE_ROOT);
       #endif
       
       WiFiClientSecure client;
       UniversalTelegramBot bot(BOTtoken, client);
       
       // How many leds in your strip?
       #define NUM_LEDS 15
       
       // For led chips like WS2812, which have a data line, ground, and power, you just
       // need to define DATA_PIN.  For led chipsets that are SPI based (four wires - data, clock,
       // ground, and power), like the LPD8806 define both DATA_PIN and CLOCK_PIN
       // Clock pin only needed for SPI based chipsets when not using hardware SPI
       #define DATA_PIN D5
       #define CLOCK_PIN 13
       CRGB leds[NUM_LEDS];
       // Checks for new messages every 1 second.
       int botRequestDelay = 1000;
       unsigned long lastTimeBotRan;
       
       const int ledPin = D5;
       bool ledState = LOW;
       
       // Handle what happens when you receive new messages
       void handleNewMessages(int numNewMessages) {
         Serial.println("handleNewMessages");
         Serial.println(String(numNewMessages));
       
         for (int i = 0; i < numNewMessages; i++) {
           // Chat id of the requester
           String chat_id = String(bot.messages[i].chat_id);
           if (chat_id != CHAT_ID) {
             bot.sendMessage(chat_id, "Unauthorized user", "");
             continue;
           }
       
           // Print the received message
           String text = bot.messages[i].text;
           Serial.println(text);
       
           String from_name = bot.messages[i].from_name;
       
           if (text == "/start") {
             String welcome = "Welcome, " + from_name + ".\n";
             welcome += "Use the following commands to control your outputs.\n\n";
             welcome += "/led_on to turn LEDs ON \n";
             welcome += "/led_off to turn LEDs OFF \n";
             welcome += "/state to request current LEDs state \n";
             bot.sendMessage(chat_id, welcome, "");
           }
       
           if (text == "/led_on") {
             ledState = HIGH;  // Update the state first
             bot.sendMessage(chat_id, "LED state set to ON", "");
             for (int i = 0; i < NUM_LEDS; i++) {
               leds[i] = CRGB::White;
             }
             FastLED.show();
           }
       
           if (text == "/led_off") {
             ledState = LOW;  // Update the state first
             bot.sendMessage(chat_id, "LED state set to OFF", "");
             for (int i = 0; i < NUM_LEDS; i++) {
               leds[i] = CRGB::Black;
             }
             FastLED.show();
           }
       
           if (text == "/state") {
             if (ledState == HIGH) {
               bot.sendMessage(chat_id, "LED is ON", "");
             } else {
               bot.sendMessage(chat_id, "LED is OFF", "");
             }
           }
         }
       }
       
       void setup() {
         Serial.begin(115200);
         FastLED.addLeds<NEOPIXEL, DATA_PIN>(leds, NUM_LEDS);
       #ifdef ESP8266
         client.setInsecure();
         configTime(0, 0, "pool.ntp.org");  // get UTC time via NTP
         client.setTrustAnchors(&cert);     // Add root certificate for api.telegram.org
       #endif
       
         pinMode(ledPin, OUTPUT);
       
         ledState = LOW;
       
         // Connect to Wi-Fi
         WiFi.mode(WIFI_STA);
         WiFi.begin(ssid, password);
       #ifdef ESP32
         client.setCACert(TELEGRAM_CERTIFICATE_ROOT);  // Add root certificate for api.telegram.org
       #endif
         while (WiFi.status() != WL_CONNECTED) {
           delay(1000);
           Serial.println("Connecting to WiFi..");
         }
         // Print ESP32 Local IP Address
         Serial.println(WiFi.localIP());
       }
       
       void loop() {
         if (millis() > lastTimeBotRan + botRequestDelay) {
           int numNewMessages = bot.getUpdates(bot.last_message_received + 1);
       
           while (numNewMessages) {
             Serial.println("got response");
             handleNewMessages(numNewMessages);
             numNewMessages = bot.getUpdates(bot.last_message_received + 1);
           }
           lastTimeBotRan = millis();
         }
       }

Click on your Arduino sketch code line, press _Copy All_ (CTRL A on Windows) and press _Paste_ (CTRL V on Windows).

Your Arduino IDE should look like this:

![image](https://github.com/Kvdekker/LED-Via-Telegram/assets/96053886/9d620b81-3d93-4336-8ca4-af44de2c7ebb)

If you scroll through the code, you will find the following lines of code:

// Replace with your network credentials
const char* ssid = "XXXX";
const char* password = "XXXX";

// Initialize Telegram BOT
#define BOTtoken "XXXXXXXXXX:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"  // your Bot Token (Get from Botfather)

// Use @myidbot to find out the chat ID of an individual or a group
// Also note that you need to click "start" on a bot before it can
// message you
#define CHAT_ID "XXXXXXXXXX"


Fill in your network credentials, Telegram BOT code and CHAT ID code.

_Can't find you BOT code or CHAT ID? Go back to step 1_

_Are you connected to a 5GHz or 2.4GHz network? Depending on what you are using, this may not work. If you are using a 5GHz network, you will not be able to connect. In this case, start a hotspot on your phone and enter your credentials from your hotspot._

_Before I pasted the previously added code, I was searching for the FastLED library to include it in the code. I couldn't initially figure out how to turn on the RGB LED strip using Telegram commands. After carefully reviewing the code, I made changes to the part of the code where it handles the "/led_on" command. Instead of the original code, I implemented code that would turn on the LEDs as defined in the FastLED library. I also added a delay to ensure that all the LEDs would turn on. In the end, only one yellow LED turned on instead of all 15._

Go to your Telegram App, type in your bot username and start using it!

If you did everything right, you should be seeing this:

https://github.com/Kvdekker/LED-Via-Telegram/assets/96053886/276e0e73-248e-48e3-8c5c-1902eba2a95a

Congratulations! You have your own working Led controller with the use of a Telegram bot!

https://github.com/FastLED/FastLED
https://randomnerdtutorials.com/telegram-control-esp32-esp8266-nodemcu-outputs/
