+++
author = "JD Hagood"
title = "Automated Etch-a-Sketch"
date = "2022-11-19"
description = "3D printed Clothes"
tags = [
]
categories = [
]
+++

This project was made for the De Florez competition. You can check out the poster [here](/images/etch_a_sketch/deflorez_poster.pdf). 
{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/dog_sunglasses.jpg" alt="Image 1" style="width: 50%; height: auto; display: block; margin: 0 auto;">
        <figcaption>Prompt: "A dog with sunglasses"</figcaption>
    </figure>
{{< /rawhtml >}}

Project ideas often come from the most mundane objects. One day an Etch-a-Sketch showed up in my dorm's lounge, and after playing with it for a couple of minutes and creating disappointing images, I had the idea to make it automated. I started talking about the idea with some of my friends, and Elijah Bell said that he wanted to help with the project. In order to give ourselves a deadline, we signed up for the De Florez competition, an annual mechanical engineering competition.

## The Human Interface

One of the first challenges was determining how humans would interface with the device. After bouncing some ideas around we realized that we could use generative AI to make the images. We could then just have a human type in what they want to see, send that request in an API request to DALL-E, and get back an image.

## The Pathfinding Algorithm
{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/example.jpg" alt="Image 1" style="width: 100%; height: auto; display: block; margin: 0 auto;">
        <figcaption>Trying to go from an image to connected image</figcaption>
    </figure>
{{< /rawhtml >}}

The next big challenge was figuring out how to go from an image to a path that you can trace on an Etch-a-Sketch. If we allow backtracking, then the only requirement for an image is that it is connected. In order to try to rectify this every prompt we sent to DALL-E started with "Generate a simple image of the following prompt drawn with a single continuous black line without picking up the pen in a line art style. Prompt:." For example, if the user typed in "Two stick figures next to each other" we would send the prompt:

_Generate a simple image of the following prompt drawn with a single continuous black line without picking up the pen in a line art style. Prompt: Two stick figures next to each other_
{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/stick-figures-AI.png" alt="Image 1" style="width: 70%; height: auto; display: block; margin: 0 auto;">
        <figcaption>What we got back from DALL-E</figcaption>
    </figure>
{{< /rawhtml >}}

This is almost what we want except the heads are disconnected. This inspired us to create this algorithm:
1. Get image from Dall-E from instructions + user prompt
2. Convert to Black and White, downscale, find connected components, remove components smaller than 8 pixels.
3. Trace by:
    1. Go from the current point (starts at 0,0) to a point in the next component, minimizing white space traveled through by using Dijkstra's algorithm. Add points traveled through to a path array.
    2. Depth First Search (DFS) through the component, backtracking with BFS if hitting an end point.
    3. Repeat until all components are exhausted

Backtracking is important because without it, when the DFS reaches a dead end, we would get random lines drawn across the image

{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/backtrack-explanation.png" alt="Image 1" style="width: 70%; height: auto; display: block; margin: 0 auto;">
        <figcaption>With and without backtracking</figcaption>
    </figure>
{{< /rawhtml >}}

This was particularly difficult to implement, and the algorithm was not very optimized, but we eventually got it working.

{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/algo_example.png" alt="Image 1" style="width: 100%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}

## Mechanical Design
Elijah is a mechanical engineer with scary good CADing skills. He decided to make the drawing robot as sleek as possible and be easily adaptable to any Etch-a-Sketch. You can check out his cad [here](https://cad.onshape.com/documents/db6a0944b9fb6a9b930142fb/w/7058057337ef4993dfcd203d/e/e47bb00960f17f1c9abce0ec?renderMode=0&uiState=671699c2e360df48201650e8). We were originally going to try to use rubber bands as belts, but after realizing that this would lead to sloppy drawings due to the flexibility of the bands, we switched to custom belts we printed from 95A TPU.
{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/cad.png" alt="Image 1" style="width: 80%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}

## Electrical Design
I was responsible for the electrical design and UI. I decided to run everything on a Raspberry Pi Zero 2 W due to its small form factor, wifi connectivity, and computation power. I connected a bluetooth keyboard and OLED display to the pi to allow users to type in their requests. The stepper motors were driven at 12v with A4998 stepper motor drivers. I wanted to anthropomorphize the device, so I even added a little ASCII art character who pretends to draw and has some cute idle animations. You can check out our source code [here](https://github.com/Bubinga/SketchBot).


{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/prototype-test.jpg" alt="Image 1" style="width: 80%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}

The complete part list became:
* Raspberry Pi Zero W
* Nema 17 Stepper Motor (2x)
* A4988 Stepper Motor Driver (2x)
* Lipo Battery
* PLA frame
* TPU belts
* PLA pulleys
* Steel back plate
* Mini OLED display
* Lipo Battery
* Mini Bluetooth keyboard
* Etch A Sketch

## Integration
Now came the hardest part of any project: tying it all together. We started by mechanically assembling everything. So far so good.

{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/first-test.jpg" alt="Image 1" style="width: 80%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}
Next we tried to draw some squares.
{{< rawhtml >}}
<div style="margin-right: 15px;">
    <video width="400" controls>
    <source src="/test_site/images/etch_a_sketch/squares-test.mp4" type="video/mp4">
    Your browser does not support the video tag.
    </video>
</div>
{{< /rawhtml >}}
This was promising so we tried to draw a picture of a dog.
{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/messed-up.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}

This was a bit too abstract for my taste, and we found a bug in the code that transcribed paths to stepper motor rotations. After fixing this we got a much better result.

{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/first-success.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}

With this success we tried some other prompts
{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/cat-drums.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption>Prompt: "A cat playing the drums"</figcaption>
    </figure>
{{< /rawhtml >}}
{{< rawhtml >}}
    <figure>
        <img src="/test_site/images/etch_a_sketch/luffy.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption>Prompt: "Luffy from One Piece"</figcaption>
    </figure>
{{< /rawhtml >}}

Overall this was a very fun project with a satisfying outcome. While there are still a lot of optimizations that can be made to the design, I am very proud of the final product me and Elijah made.