+++
author = "JD Hagood"
title = "NFC Pen Testing GLove"
date = "2022-11-19"
description = "RFID Glove"
tags = [
]
categories = [
]
+++


This was a relatively small and quick project aimed to expose the security risks of NFC tap access doors used at most all universities.


I began learning how RFID cards work when I found a Proxmark 3 Easy in a maker space, and went down a deep rabbit hole of trying to read the data on every card that I owned. I had great fun doing this and learning about the many different RFID card types. I now collect tap cards like they are pokemon cards.


In the same maker space that I found the Proxmark, I also found some arduino compatible NFC modules.


{{< rawhtml >}}
    <figure>
        <img src="/images/nfc_glove/nfc_module.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}


These modules are meant to talk to 13.56 MHz MIFARE cards, and my MIT ID card is a MIFARE Classic 1K card. I was surprised to see that I could read the UID of my card with this module, and then emulate this UID on the proxmark to tap into places. I could essentially clone my card. This is a security risk, but I wanted to take it a step farther.


I built a glove with a hidden card reader. The card reader is on the back of the glove. I imagined that a bad actor could use the glove to brush past someone in the hall and nab their UID. I even added a small motor from a 9g servo motor to vibrate when the glove detected a UID.
{{< rawhtml >}}
    <figure>
        <img src="/images/nfc_glove/exposed_glove.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}
{{< rawhtml >}}
    <figure>
        <img src="/images/nfc_glove/covert_glove.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}


The code for the project was not difficult to write.


{{< highlight arduino>}}
/*
  This program will store the UID of all the cards it sees in EEPROM while avoid duplications:
  EEPROM has 255 addresses for bytes of information. Each UID is 4 bytes. address = 0 will keep
  track of how many cards we have in the system.
*/


#include <SPI.h>
#include <MFRC522.h>
#include <EEPROM.h>
#include <Wire.h>


#define SS_PIN 10
#define RST_PIN 9
#define MOTOR_PIN 2


MFRC522 mfrc522(SS_PIN, RST_PIN);  // Create MFRC522 instance.


void setup() {
  Serial.begin(9600);  // Initiate a serial communication
  SPI.begin();         // Initiate  SPI bus
  mfrc522.PCD_Init();  // Initiate MFRC522
  pinMode(MOTOR_PIN, INPUT);
}




void loop() {
  // Look for new cards
  if (Serial.available()) {


    int cmd = Serial.parseInt();
    String dump = "dump";
    if (cmd == 1) {  //Dump Cards
      Serial.println("Dumping Card UIDs...");
      for (int i = 0; i < EEPROM.read(0); i++) {
        unsigned long UID = 0;
        for (byte j = 1; j <= 4; j++) {
          Serial.print(EEPROM.read(4 * i + j), HEX);
          Serial.print(" ");
          UID += EEPROM.read(4 * i + j) * pow(256, 4 - j);
        }
        Serial.print(" ");
        Serial.print(UID);
        Serial.println("");
      }
    }


    if (cmd == 2) {
      Serial.println("Setting Card Count to 0...");
      EEPROM.write(0, 0);
    }


    Serial.println("");
  }


  if (!mfrc522.PICC_IsNewCardPresent()) {
    return;
  }
  // Select one of the cards
  if (!mfrc522.PICC_ReadCardSerial()) {
    return;
  }
  //Show UID on serial monitor
  String content = "";
  byte letter;
  for (byte i = 0; i < mfrc522.uid.size; i++) {
    Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
    Serial.print(mfrc522.uid.uidByte[i], HEX);
    content.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
    content.concat(String(mfrc522.uid.uidByte[i], HEX));
  }
  Serial.println();
  bool isDuplicate = false;
  for (int i = 0; i < EEPROM.read(0); i++) {
    if (mfrc522.uid.uidByte[0] == EEPROM.read(4 * i + 1) && mfrc522.uid.uidByte[1] == EEPROM.read(4 * i + 2) && mfrc522.uid.uidByte[2] == EEPROM.read(4 * i + 3) && mfrc522.uid.uidByte[3] == EEPROM.read(4 * i + 4)) {
      isDuplicate = true;
      //Serial.println("Duplicate");
    }
  }


  if (isDuplicate == false) {
    Serial.println("New Card Added");
    int i = EEPROM.read(0);
    for (int j = 1; j <= 4; j++) {
      EEPROM.write(4 * i + j, mfrc522.uid.uidByte[j - 1]);
    }
    EEPROM.write(0, i + 1);
    pinMode(MOTOR_PIN, OUTPUT);
    delay(500);
    pinMode(MOTOR_PIN, INPUT);
  }
}


{{< /highlight >}}


This program will save the UIDs that it sees to EEPROM memory, and then I can recover these UIDs later in the serial monitor by sending a "1". Once you have a UID, it is possible to emulate that person's card and tap in as them. This project goes to show the major security flaws inherent to a RFID tap system that only checks the UID for identity verification. While it would be hard to design a system that does not suffer from this simple cloning attack, using the rest of the data on the MIFARE Classic 1K card as a means of verification, perhaps in a zero knowledge proof, could help deter this simple attack.
