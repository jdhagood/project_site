+++
author = "JD Hagood"
title = "Reciept Poetry Printing Robot"
description = "Poems on Reciepts"
tags = [
]
categories = [
]
+++

The inspireation for this project came from my friend from Yale who really likes poetry. When I was talking to him about thermal printers one day, he got the idea to print poems on reciepts. I thought that this was a great idea and immediately hopped into Onshape. I designed a sheet metal enclosure for the device with a 3D printed sign on top encouraging users to press the big red button. You can check out the Onshape cad [here](https://cad.onshape.com/documents/cf3778deca11ee0736d88359/w/80c19d9216b3187a060b21ef/e/34e68baa4d9532f38bcb4871?renderMode=0&uiState=66f1c5994927d10f4adc73d7).

{{< rawhtml >}}
    <figure>
        <img src="/images/poem_printer/poem_printer_front.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}

{{< rawhtml >}}
    <figure>
        <img src="/images/poem_printer/poem_printer_back.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}

The actual poems are scraped from [poetryfoundation.org](https://www.poetryfoundation.org/) with beautiful soup because I much prefer to read contemperary poetry rather than older poetry. If you are curious how I did this check it out on my [github](https://github.com/jdhagood/poem_scraper/tree/main).  
After that I used a raspberry pi 3A to convert the output into serial so I could send it to a thermal printer. Here is a first test.

{{< rawhtml >}}
    <figure>
        <img src="/images/poem_printer/printed_poems.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}

After some refining I then printed poems like this.
{{< rawhtml >}}
    <figure>
        <img src="/images/poem_printer/printed_poems_2.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}
I even laser cut out the sheet metal enclosure. It was barely manufacturable and in the future I will make my sheet metal enclosures into more parts to rivet together.

{{< rawhtml >}}
    <figure>
        <img src="/images/poem_printer/real_printer_front.jpg" alt="Image 1" style="width: 60%; height: auto; display: block; margin: 0 auto;">
        <figcaption></figcaption>
    </figure>
{{< /rawhtml >}}
Now I need to powder coat it, do the electronics, and write the rest of the script. While making this I was also inspired to have the thermal printers print out thousands of digits of constants. During pi day I would love to set some printers up in lobby 7 to print millions of digits of pi.