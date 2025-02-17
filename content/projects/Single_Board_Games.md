+++
author = "JD Hagood"
title = "Single Board Games"
date = "2022-11-19"
description = "3D printed Clothes"
tags = [
]
categories = [
]
+++
In the class How to Make Almost Everything, we were tasked with designing a PCB and milling it out. As an electrical engineering major, I found myself in familiar territory. In order to challenge myself and grow my maker toolkit, I decided that I wanted to do this assignment only using a hardware description language to program my board layout and routing rather than doing it manually on Kicad.


I often find the process of laying out a PCB in CAD tedious, and I feel like I have little control over the geometric constraints between the components on my board. I think it would be great if there existed a PCB design software that implemented basic constraint solving between the footprints of components. With simple length constraints, angle constraints, linear pattern, circular patterns, tangency constraint, and some splines, I feel like the process of board layouts could be easier and more precise than ever. This led me to ask if such a software existed in lecture, and I learned that one of the TAs in the class, Leo McElroy, is working on that right now! He made a software called ["SVG-PCB"](https://leomcelroy.com/svg-pcb-website/#/home) that allows you to both graphically place footprints, and also program the placement of the footprints using a hardware description language in java script.


You can find the hardware description language code for this board (and more) on my [github](https://github.com/jdhagood/svg_pcbs). I decided to try to make a handheld timing game. This is the PCB I designed.


{{< rawhtml >}}
    </figure>
    <img src="/images/single_board_games/circle_pcb.jpg" alt="Profile Picture" class="floatright" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    <figcaption>Complied from led_circle.js on my github</figcaption>
    </figure>
{{< /rawhtml >}}


 I first tried to download my gerber files directly from PCB-SVG, but it did not rotate each LED correctly. I had to download it as a Kicad file, open it in Kicad, and then download the gerber files from Kicad. With this small hiccup solved, I could then let the mill do the heavy lifting of milling my board.
{{< rawhtml >}}
    <div class="image-grid">
        <img src="/images/single_board_games/milled_board.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/images/single_board_games/ready_to_be_soldered.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}
It was now time to solder everything on the board. I absolutely love soldering surface mount components with solder paste, so I did this for all the components except for the ATtiny3226 because I was worried about shorting the pins with too much solder paste. I mainly used 1208 sized components, which may look small, but after soldering hundreds of 0805 components on the Solar Car team, 1208 was a breeze.


I did forget to connect the ground pin of the ATtiny3226 to ground, but this was an easy fix as I only needed to short the ground pin to one of the large unconnect copper sections and then short this copper section to the circular ground track on the edge of the board.
{{< rawhtml >}}
    <div class="image-grid">
        <img src="/images/single_board_games/solder_paste.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/images/single_board_games/soldered_board.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}
 
{{< rawhtml >}}
    <img src="/images/single_board_games/programming_board.jpg" alt="Profile Picture" class="floatright" style="width: 60%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
Now came the challenge of actually programming the board. I used the Arduino IDE to do this. I downloaded the megaTinyCore in my boards manager, connected an FTDI programmer to the UPDI pin of the ATtiny 3226, wrote a simple blink sketch, and pressed upload. I held my breath as it complied, but I was then hit with the error message "a programmer is needed to upload."


I had the programmer plugged into my computer, and this sent me down a very deep rabbit hole of trying to configure the drivers on my computer and even converting an arduino uno into a jtag programmer because I thought the FTDI programmer was broken. I was about to give up when I saw the option to "Upload Using Programmer." I clicked this and an LED started blinking on my board. This was a major facepalm moment, but at least I can now upload programs.


The sketch to make the game was not complicated. However, when I tried to run the device off the coin cell battery, the program would randomly restart. I knew that this had to be because the battery could not supply enough current. Thankfully I read in the datasheet that you could make the microcontroller consume less power if you made the internal clock slower. I had the option to reduce the clock speed all the way down to 1MHz, and after this the board worked fine off battery power.


{{< highlight arduino>}}
#define LED_COUNT 14
#define BUTTON_PIN 9
#define START_DELAY 750




const byte leds[] = {
  0, 1, 2, 3, 4, 5, 6, 7, 8, 10, 11, 12, 13, 15
};
unsigned long last_led_move_time = 0;
unsigned long last_button_press_time = 0;
byte stationary_led = random(0, LED_COUNT);
byte moving_led = 0;
int move_delay = START_DELAY;
bool CCW = false;




void setup() {
  for(byte i = 0; i < LED_COUNT; i++) {
    pinMode(leds[i], OUTPUT);
  }
  pinMode(BUTTON_PIN, INPUT_PULLUP);
}




void loop() {
  if(millis()-last_led_move_time >= move_delay) {
    last_led_move_time = millis();
    digitalWrite(leds[moving_led], LOW);
    switch(CCW) {
      case true:
        moving_led = (moving_led + 1) % LED_COUNT;
        break;
      case false:
        moving_led = (moving_led - 1 + LED_COUNT) % LED_COUNT;
        break;
    }
  }




  digitalWrite(leds[stationary_led], HIGH);
  digitalWrite(leds[moving_led], HIGH);
  if(digitalRead(9) == 0) {
    delay(100);
    if(moving_led == stationary_led) {
      digitalWrite(leds[stationary_led], LOW);
      stationary_led = random(0, LED_COUNT);
      move_delay = 4*move_delay/5;
      CCW = !CCW;
    } else {
      fail_animation_and_reset();
    }
    while(digitalRead(9) == 0) {}
  }
}




void fail_animation_and_reset() {
  for(byte j = 0; j < 5; j++) {
    for(byte i = 0; i < LED_COUNT; i++) {
      digitalWrite(leds[i], HIGH);
    }
    delay(300);
    for(byte i = 0; i < LED_COUNT; i++) {
      digitalWrite(leds[i], LOW);
    }
    delay(300);
  }
  move_delay = START_DELAY;
}




{{< /highlight >}}




And ta-da, a working game.
{{< rawhtml >}}
<div style="margin-right: 15px;">
    <video width="700" controls>
    <source src="/images/single_board_games/working_game.mp4" type="video/mp4">
    Your browser does not support the video tag.
    </video>
</div>
{{< /rawhtml >}}
This was fun to play with for a little while, but then I wanted to up the stakes. Using a MOSFET and one of the free GPIO pins on the ATtiny3226, I make it such that when you lose the game it triggers a high voltage boost converter.
{{< rawhtml >}}
<div style="margin-right: 15px;">
    <video width="700" controls>
    <source src="/images/single_board_games/taser.mp4" type="video/mp4">
    Your browser does not support the video tag.
    </video>
</div>
{{< /rawhtml >}}


After the success of this board I knew that I could do better. There were some individually accessible neopixels in the maker space, and my first thought was to recreate the game 2048 with a 4x4 grid. I started by programming the layout of the board.


{{< rawhtml >}}
    </figure>
    <img src="/images/single_board_games/neopixel_pcb.jpg" alt="Profile Picture" class="floatright" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    <figcaption>Compiled from neo_pixel_game.js on my github</figcaption>
    </figure>
{{< /rawhtml >}}


I then milled out the board.


{{< rawhtml >}}
    </figure>
    <img src="/images/single_board_games/milled_neopixel_board.jpg" alt="Profile Picture" class="floatright" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}


Next I populated and began programming it just as the last board. You can check out my code [here](https://github.com/jdhagood/attiny_2048/tree/master).
{{< rawhtml >}}
    </figure>
    <img src="/images/single_board_games/programming_neopixel_board.jpg" alt="Profile Picture" class="floatright" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}


Finally, I was left with a small, hand held game. I am running the board off two AA batteries. Given the power consumption I measured from a power supply, the AA batteries should be able to power the device continuously for over 50 hours.
{{< rawhtml >}}
    </figure>
    <img src="/images/single_board_games/completed_neopixel_game.jpg" alt="Profile Picture" class="floatright" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}
