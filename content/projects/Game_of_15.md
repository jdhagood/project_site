+++
author = "JD Hagood"
title = "Game of 15"
date = "2022-11-19"
description = "3D printed Clothes"
tags = [
]
categories = [
]
+++


# Inspiration
For my final project in the class How to Make Almost Anything, I wanted to make a device that times how fast you can solve a puzzle to encourage some fun competition amongst MIT puzzle nerds. During an introduction to Abstract Algebra lecture, the professor used the Game of 15 puzzle as an example to discuss the parity of permutations. For those who are not familiar, the Game of 15 is a slider puzzle played on a 4x4 grid with numbered slider tiles. The goal is to slide around the tiles until all the pieces are in order. It is essentially a 2D rubix cube.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/pic-puzzle-15.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




I was immediately inspired from this lecture to try to create my own 3D printable version, but with a twist. I wanted the puzzle to know when it was in the solved state and reward the solver. I therefore needed a way to electronically detect the state of the puzzle. While a camera module and a raspberry pi running a vision model would work, I wanted to make the puzzle a cohesive unit. I decided that magnets and hall effect sensors would be my best bet. Each slider piece will have 4 magnets on the back. With 4 magnets I can encode what the piece is in binary based on how the magnets are flipped. With 4 magnets I can encode up to 16 pieces which is perfect for my application. Below is how I planned to place the magnets.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/encoding_magnets.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




# CAD Design
The first big challenge to overcome was the sliding mechanism. I need all the pieces to stay on the board while also offering little friction to sliding. I designed each piece such that they protrude on two sides, and have a track on the other two sides.




{{< rawhtml >}}
        <img src="/project_site/images/game_of_15/slider_piece_male.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
        <img src="/project_site/images/game_of_15/slider_piece_female.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/piece_connection.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
The frame that holds all the pieces together also has these tracks and protrusions. It is held down to a slider bed with M2 screws.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/slider_holder.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
The slider bed also acts to hold the hall effect sensor grid and the retainer for that PCB is the retainer for the main PCB with a smaller retainer on it.




{{< rawhtml >}}
    <div class="image-grid">
        <img src="/project_site/images/game_of_15/slider_floor_1.jpg" alt="Image 1">
        <img src="/project_site/images/game_of_15/slider_floor_2.jpg" alt="Image 1">
        <img src="/project_site/images/game_of_15/slider_floor_3.jpg" alt="Image 1">
    </div>
{{< /rawhtml >}}




The bottom piece of the unit holds up the slider puzzle, provides a mounting place for a TFT display and 5 input buttons, and a place to plug the machine in and turn it on.
{{< rawhtml >}}
    <div class="image-grid">
        <img src="/project_site/images/game_of_15/bottom_1.jpg" alt="Image 1">
        <img src="/project_site/images/game_of_15/bottom_2.jpg" alt="Image 1" style = "width: 150%">
    </div>
{{< /rawhtml >}}
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/bottom_3.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




Finally I CADed the main wall to hide all of the electronics and screws. I made it wavy to make it more visually interesting and make it look like it was made on a router.




{{< rawhtml >}}
    <div class="image-grid">
        <img src="/project_site/images/game_of_15/main_wall_1.jpg" alt="Image 1">
        <img src="/project_site/images/game_of_15/main_wall_2.jpg" alt="Image 1">
        <img src="/project_site/images/game_of_15/main_wall_3.jpg" alt="Image 1">
    </div>
{{< /rawhtml >}}




# Electronics Overview
The main challenge with this project is reading the inputs from 64 hall effect sensors. I initially planned to use shift registers to multiplex the 8x8 grid of hall effect sensors to detect the pieces like in the image below.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/electronics_overview.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
However, I soon realized that using shift registers would be more complicated than helpful because they are 5v devices while the Raspberry Pi Pico is a 3.3v device so I would need a level shifter. After I started designing my PCB, I realized that I had exactly enough GPIO pins to not use the shift registers and do the multiplexing directly from the Pico's GPIO pins.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/overview_v2.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
I knew that I could drive the gate of the MOSFETS with a GPIO pin by changing it from input to output. This allowed me to simply break out all the pins from the Pico on my main board which I would then plug other boards into.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/kicad_sch.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




I used the AH3144E digital hall effect sensor to detect the state of the board. As we can see looking at the functional block diagram in the datasheet, this hall effect sensor leaves the output pin floating when it does not detect a magnetic field and ties it to ground when it does detect a magnetic field.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/hall_block_diagram.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/hall.gif" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




I made the hall effect sensor array with Leo McElroy's [SVG-PCB](https://leomcelroy.com/svg-pcb/). You can check out the code I wrote [here](https://github.com/jdhagood/svg_pcbs/blob/master/hall_module.js). I connected all the output lines columns and the power and ground in rows. I planned to connect all the ground pins together and apply 5V through a MOSFET to the row I am interested in reading. I then read the output from the columns with a GPIO pin with the internal pullup resistor enabled.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/svg_pcb.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
The PCB is modular and allows you to tile it to make an arbitrarily large grid. I only need 4 of these for my project.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/modular_apart.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
I then populated the boards and soldered and glued them together.
{{< rawhtml >}}
<div class="image-grid">
        <img src="/project_site/images/game_of_15/soldering_hall.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
        <img src="/project_site/images/game_of_15/modular_together.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
        <img src="/project_site/images/game_of_15/glued.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}




The high side switching board was again designed in SVG-PCB.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/p-fet_board.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
And hand soldered together.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/soldered_p-fet.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




I used a 2.8" TFT 240x320 pixel display that communicated over SPI to give user feedback. The user can navigate this screen using 5 buttons to the right of it on a small button panel.




{{< rawhtml >}}
    <div class="image-grid">
        <img src="/project_site/images/game_of_15/output_holder.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
        <img src="/project_site/images/game_of_15/output_front.jpg" alt="Image 2" style="width: 40%; height: auto; display: block; margin: 0 auto;">
        <img src="/project_site/images/game_of_15/soldered_output.jpg" alt="Image 3" style="width: 40%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}




# Manufacturing
The manufacturing for this design was relatively straightforward because I designed everything to be 3D printable and fit together with screws.
{{< rawhtml >}}
    <div class="image-grid">
        <img src="/project_site/images/game_of_15/printed_base.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
        <img src="/project_site/images/game_of_15/printed_wall.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}
After everything was printed the parts went together smooth as butter. When I was printing the tiles, I paused the print right before the numbers started to print, changed the filament from red to blue, and resumed the print. This made the numbers visually pop out from the red background. Thanks to the tolerances of my tile pieces and the flexibility of the PLA, it was rather easy to snap the last couple of tile pieces in place. I used a little bit of 3D printer lubrication to make the sliding smoother, and after sliding the pieces around for a bit, they were now very smooth.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/printed_cad.jpg" alt="Profile Picture" style="width: 60%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
Now comes the hard part: the electronics manufacturing.




I was happy that the hall effect sensor board perfectly slotted into the back of the slider puzzle.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/completed_back.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




I then milled and soldered together the main board with my Pico. I am a big fan of the ribbon cable that I used. It made the inside of my project more colorful than a pride parade and helped a ton with wire management and easy debugging as I could plug and unplug boards as I was testing them!
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/milled_main_board.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




I soldered everything in place and secured my boards with some 3D printed brackets.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/wiring_open.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/wiring_closed.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}








# Coding
I began by trying to interface with the TFT display I was using. I decided to use circuit python in Thonny because then I could use Adafruit's ili9341 library.


{{< rawhtml >}}
<div style="display: flex; justify-content: center; margin-right: 15px;">
    <video width="400" controls>
        <source src="/project_site/images/game_of_15/button_test.mp4" type="video/mp4">
        Your browser does not support the video tag.
    </video>
</div>
{{< /rawhtml >}}




I then tested out multiplexing the hall effect sensors. The code was relatively straightforward. I found that I could drive the gate of the p-channel mosfets by switching the pins connected to it from an input to an output.
{{< highlight python >}}
import board
import digitalio
import time

# Define the input pins
input_pins = [
    digitalio.DigitalInOut(board.GP5),
    digitalio.DigitalInOut(board.GP6),
    digitalio.DigitalInOut(board.GP7),
    digitalio.DigitalInOut(board.GP8),
    digitalio.DigitalInOut(board.GP9),
    digitalio.DigitalInOut(board.GP12),
    digitalio.DigitalInOut(board.GP13),
    digitalio.DigitalInOut(board.GP14)
]
input_pins.reverse()

# Define the output pins
output_pins = [
    digitalio.DigitalInOut(board.GP15),
    digitalio.DigitalInOut(board.GP19),
    digitalio.DigitalInOut(board.GP20),
    digitalio.DigitalInOut(board.GP21),
    digitalio.DigitalInOut(board.GP22),
    digitalio.DigitalInOut(board.GP26),
    digitalio.DigitalInOut(board.GP27),
    digitalio.DigitalInOut(board.GP28)
]

# Configure input pins as inputs with pull-down resistors
for pin in input_pins:
    pin.direction = digitalio.Direction.INPUT
    pin.pull = digitalio.Pull.UP

# Configure output pins as outputs
for pin in output_pins:
    pin.direction = digitalio.Direction.INPUT
    #pin.value = 1  # Set high initially to turn off P-channel MOSFETs

def read_and_write_gpio():
    while True:
        states = []
        for pin in output_pins:
            pin.direction = digitalio.Direction.OUTPUT
            time.sleep(0.001)
            states.append([1 if p.value else 0  for p in input_pins])
            pin.direction = digitalio.Direction.INPUT
        for row in states:
            print(row)
        print("\n")
        time.sleep(1)

# Call the main function to handle GPIO
read_and_write_gpio()
{{< /highlight >}}
After I had the array of states of the hall effect sensors, it was easy to convert it to the state of the board and check if it was solved or not.
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/detection_working.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




I could then easily connect the two functionalities together.


{{< rawhtml >}}
<div style="display: flex; justify-content: center; margin-right: 15px;">
    <video width="400" controls>
        <source src="/project_site/images/game_of_15/screen_and_slide.mp4" type="video/mp4">
        Your browser does not support the video tag.
    </video>
</div>
{{< /rawhtml >}}




# Final Product
{{< rawhtml >}}
    <img src="/project_site/images/game_of_15/final_setup.jpg" alt="Image 1" style="width: 40%; height: auto; display: block; margin: 0 auto;">
{{< /rawhtml >}}




I am very happy with the final product. The packaging came out very nice and the sensing of the board worked exactly like I envisioned. It even passes the shake test: when I vigorously shake the device I do not hear any rattling!




The device also worked exactly how I envisioned it socially. I got into a competition with people in my lounge to see who could solve it fastest!




{{< rawhtml >}}
<div style="display: flex; justify-content: center; margin-right: 15px;">
    <video width="400" controls>
        <source src="/project_site/images/game_of_15/fast_solve.mp4" type="video/mp4">
        Your browser does not support the video tag.
    </video>
</div>
{{< /rawhtml >}}
