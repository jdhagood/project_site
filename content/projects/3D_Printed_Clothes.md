+++
author = "JD Hagood"
title = "3D Printed Clothes"
date = "2022-11-19"
description = "3D printed Clothes"
tags = [
]
categories = [
]
+++

I am currently taking the class How To Make Almost Everything, and during the professor highlighted the work of former student Jack Forman. He found a way to print textile like materials by under extruding filament to create what he called [defeXtiles](https://www.media.mit.edu/publications/defextiles-publication/). By under extruding the filament and building up a wall 1 layer wide, it is possible to make a flexible sheet of plastic. As soon as I saw this unique technique, I knew exactly what I wanted to do this week: I wanted to print clothes. These clothes would also double as my attire to the annual B-3rd ABC party where you have to wear something creative that you made.

{{< rawhtml >}}
    <div class="image-grid">
        <img src="/test_site/images/3d_printed_clothes/special_fil.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/test_site/images/3d_printed_clothes/onshape_cad.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}
Now it was time to start printing. I knew that I wanted my clothes to look unique and catch the attention of anyone who walked by, so I decided to print with OOVID3D Tri-Color PLA. I "designed" my print in onshape which you can view [here](https://cad.onshape.com/documents/e05a0dd7ea2ee930ef19f509/w/363c64096a91fd33f326efd6/e/3cf24c62aeac33bff228997d?renderMode=0&uiState=66fca4ea0cfcd85cc14b9c2e). I use quotation marks because the design only consisted of projecting a helix from a cone to make an Archimedean spiral, giving the spiral some width, and extruding it upwards. I tried to make the spiral width only slightly bigger than the diameter of my nozzle so the Bambu Slicer would print it as one continuous strand. I found it rather amusing that by printing in a spiral you both respected the size constraints of the build plate and made a self supporting structure. Thanks to [Gauss’s Theorema Egregium](https://en.wikipedia.org/wiki/Theorema_Egregium), the same theorem that says to bend your pizza when you eat it so it doesn’t droop, I was sure that the curvature of the spiral would keep the structure supported in the z direction.

It was now time to start some test prints! I first needed to figure out what extruder multiplier to use. The paper gave a wide range from 0.7-0.4, but I only started seeing results when I set the extruder multiplier to around 0.4. Maybe this is a quirk of the bambu slicer trying its best to avoid defects in prints, but I never had enough time to fully troubleshoot. I tried extrusion multipliers from as low as 0.2 to as high as 0.45. This was a trade off between flexibility and strength. However with strength also came the tendency to split along layer lines
{{< rawhtml >}}
    <div class="image-grid">
        <img src="/test_site/images/3d_printed_clothes/1st_test.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/test_site/images/3d_printed_clothes/2nd_test.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}
After I printed off these raw materials, it was time to test out if they could be used to make clothes. The easiest thing I could think of to make was a tie. I started by using a clothes iron on low heat to flatten out the material, traced a tie pattern on it, and used a soldering iron to trace along the outline. By using a soldering iron I was both cutting and cauterizing the fabric as I went. I used the soldering iron to put a small hook on the back of each tie so you could wear them with a shirt on.
I gave one of the ties I made to a friend for a birthday gift, and he just so happened to have a futuristic costume to go with it.
{{< rawhtml >}}
    <div class="image-grid">
        <img src="/test_site/images/3d_printed_clothes/tie1.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/test_site/images/3d_printed_clothes/tie2.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/test_site/images/3d_printed_clothes/tie3.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}
 
Now it was time to scale up production.
 
{{< rawhtml >}}
    <div class="image-grid">
        <img src="/test_site/images/3d_printed_clothes/roll1.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/test_site/images/3d_printed_clothes/roll2.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}
 
I again used an iron to flatten out the roll of PLA to a sheet that I could cut. I downloaded a sewing pattern for a waistcoat from [freesewing.org](https://freesewing.org/designs) and printed it out on some sheets of paper.
{{< rawhtml >}}
    <div class="image-grid">
        <img src="/test_site/images/3d_printed_clothes/sewing_pattern.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/test_site/images/3d_printed_clothes/in_progress1.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/test_site/images/3d_printed_clothes/first_tryon.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <img src="/test_site/images/3d_printed_clothes/final.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
    </div>
{{< /rawhtml >}}


Throughout the construction I kept running into layer separation. I solved this by placing another pla sheet behind each sheet with layer lines rotated 90 degrees. Overall I do not recommend printing clothes out of PLA. If I could do it again I would have printed everything with TPU which would have been a lot more flexible and forgiving. Now I just need to print out some pants, and I will be ready for the party.
