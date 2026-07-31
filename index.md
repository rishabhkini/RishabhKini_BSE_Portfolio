# Smart Glasses

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Rishabh K | Leland High School | Mechanical Engineering | Incoming Senior

![Headstone Image](Rishabh.png)
  
# Final Milestone
<iframe width="560" height="315" src="https://www.youtube.com/embed/_ym2Ywc5L4M?si=mv2lmGQQryo14_us" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
Summary

For my final milestone I connected my Transparent OLED screen and my camera to the Smart Glasses. I also added a button which I could click in order to allow someone to take a picture.


# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/bQmpPXIwCGo?si=ELyeH1S_04Mizyjg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
Summary

For my second milestone I added a transparent OLED which can display any information that I code it to show. For this demonstration, it shows the time and date.

How it works:

I connected my transparent OLED to the Raspberry Pi using SPI pins. The raspberry pi sends graphics pixel by pixel every second to display a live clock on the Transparent OLED. The camera is also attatched to the raspberry pi and takes a picture whenever I press enter. The code splits into two parallel tracks. One track constantly loops to update the screen text without stopping, while the second track waits for the user to press Enter so it can trigger the camera in the background.

Challenges:

One of the main challenges I faced was connecting the transparent OLED 


# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/OXrb9tGNTTQ?si=KPwbgP2sv-ocmgQK" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
Summary

For my first milestone I created a program which takes a picture when it is run which will be used very frequently for this project.

How it works:

I used a RaspberryPI 4, monitor, mouse, keyboard, micro USB adapter, Arducam OV5647, and micro SD card for my first milestone. My RaspberryPI 4 acts like a microscopic computer by processing code and running it. I edit my Raspberry Pi by using my keyboard, mouse, and monitor by connecting it all together. I flashed a bookworm OS which is a newer version of the OS onto my micro SD card and I put it in my Raspberry PI. I did this by using an SD card adapter to connect to my computer and I flashed my SD card using the RaspberryPI imager. The image below shows the inputs of a Raspberry PI zero.

# Starter Project - Retro Arcade Game

<iframe width="560" height="315" src="https://www.youtube.com/embed/yw83-8JkuZA?si=sisLsvt2kWda2Es3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My starter project is a mini arcade game. With this, you can play tetris, snake game, car racing, and space invaders.  


# Schematics 
<!--Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser.--> 

![Oled Holder](image.png)

# Code
<!--Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs.--> 

```c++
import datetime
import os
import subprocess
import time
from threading import Thread
import adafruit_ssd1306
import board
import digitalio
from PIL import Image, ImageDraw, ImageFont

WIDTH = 128
HEIGHT = 64

spi = board.SPI()
oled_dc = digitalio.DigitalInOut(board.D25) 
oled_rst = digitalio.DigitalInOut(board.D27)
oled_cs = digitalio.DigitalInOut(board.D8)

display = adafruit_ssd1306.SSD1306_SPI(
    WIDTH, HEIGHT, spi, oled_dc, oled_rst, oled_cs
)
display.fill(0)
display.show()

canvas = Image.new("1", (WIDTH, HEIGHT))
draw = ImageDraw.Draw(canvas)
font = ImageFont.load_default()

hud_status = "READY"

def run_hud_display():
    global hud_status
    while True:
        draw.rectangle((0, 0, WIDTH, HEIGHT), fill=0)
        now = datetime.datetime.now()
        time_text = now.strftime("%I:%M:%S %p")
        date_text = now.strftime("%b %d, %Y")
        draw.text((2, 2), "GLASSES HUD v1.0", font=font, fill=255)
        draw.text((2, 20), f"Time: {time_text}", font=font, fill=255)
        draw.text((2, 38), f"Date: {date_text}", font=font, fill=255)
        draw.text((2, 54), f"Cam: [{hud_status}]", font=font, fill=255)
        display.image(canvas)
        display.show()

        time.sleep(1)

def snap_photo():
    global hud_status
    hud_status = "CAPTURING"
    time.sleep(0.1) 
    desktop_path = os.path.expanduser("~/Desktop")
    timestamp = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = f"{desktop_path}/glasses_photo_{timestamp}.jpg"

    command = [
        "rpicam-still",
        "-o",
        filename,
        "--immediate",
        "--nopreview", 
    ]

    try:
        subprocess.run(command, check=True)
        hud_status = "SUCCESS"
    except Exception:
        hud_status = "ERROR"
    time.sleep(2)
    hud_status = "READY"

if __name__ == "__main__":
    hud_thread = Thread(target=run_hud_display)
    hud_thread.daemon = True
    hud_thread.start()

    print("\n" + "=" * 45)
    print(" SMART GLASSES EXECUTIVE ENGINE INITIALIZED ")
    print("=" * 45)
    print(" -> Screen running live updates...")
    print(" -> PRESS [ENTER] IN THIS TERMINAL TO SNAP A PHOTO.")
    print(" -> Press Ctrl+C to terminate.")
    print("=" * 45 + "\n")

    try:
        while True:
            capture_thread = Thread(target=snap_photo)
            capture_thread.start()

    except KeyboardInterrupt:
        display.fill(0)
        display.show()
        print("\nShutting down smart glasses suite safely.")
```

# Bill of Materials
<!--Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs.--> 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Raspberry Pi | Processing Code | $100 | <a href="https://vilros.com/products/raspberry-pi-4-model-b-1?variant=40809478750302&country=US&currency=USD&utm_medium=product_sync&utm_source=google&utm_content=sag_organic&utm_campaign=sag_organic&tw_source=google&tw_adid=&tw_campaign=19684058556&gad_source=1&gad_campaignid=19684058613&gbraid=0AAAAAD1QJAjwAsqve1Ko5ANov0MGcWGae&gclid=Cj0KCQjwo_PRBhDNARIsAEcVALVtimnA-ogRPoFn_vzm8aNlXaiZiaukn7J2mmA_vaF6hLafW63kg8kaAqe8EALw_wcB"> Link </a> |
| Keyboard & Mouse | Typing and Navigating Raspberry Pi | $29.99 | <a href="https://www.hp.com/us-en/shop/pdp/hp-330-wireless-mouse-and-keyboard-combination?s_kwcid=AL!20144!3!!!!x!!&gclsrc=aw.ds&jumpid=cs_con_nc_ns&utm_medium=cs&utm_source=ga&utm_campaign=US_CPS-bu_PMAX_Mix_acce_Other_Google-s_OPEX_ENG_CM016876_Bestseller&utm_content=sp&adid=&addisttype=xpla&2V9E6AA%23ABL&cq_src=google_ads&cq_cmp=20464174558&cq_con=&cq_term=&cq_med=pla&cq_plac=&cq_net=x&cq_pos=&cq_plt=gp&gad_source=1&gad_campaignid=19973636866&gbraid=0AAAAAD-ppXgjUI0iAGWkyWuwgGXhqn-XC&gclid=Cj0KCQjwo_PRBhDNARIsAEcVALUROLYIQ-82c4Ky0qN2-OQkG_KX16h_GnRwe3QXH-RXmPPuZtRL9eMaAnH2EALw_wcB"> Link </a> |
| Blu-Ray Sunglasses | What the item is used for | $3.76 | <a href="https://us.shein.com/goods-p-357088355.html?goods_id=357088355&test=5051&url_from=adhub288746916&scene=1&pf=google&ad_type=DPA&language=en&siteuid=us&version_bid=102311026,101991876,102310816,102353676,101991731,102310676,102310801&version_eid=100902896&landing_page_id=1510&ad_test_id=50860&requestId=olw-5ttnsfkygo4w&cid=22661272745&popup_login=false&gad_source=1&network=g&gad_campaignid=22661272745&adid=757423896894&tv_b=2&activity_id=5051&currency=USD&lang=us&skucode=I6mk1wzfq1v7h1&product_set_id=3249&onelink=0/googlefeed_us&gclid=Cj0KCQjwo_PRBhDNARIsAEcVALW9w6MykbfJOZfve-fCL3U_1wC6Gi0IS54RwxJ0c4xh65UNqIhE5N4aApuFEALw_wcB&ismg=2590a8ef88a6af6f2c708bc44c0a0920bf3d86c4eaeb5f77311a463aabb2ff6c_01_1782389281&geoid=9032171&gbraid=0AAAAADm0yO7uisx_Uz3sD1DiPJMbkL0Kv&setid=179536816286&kwd=pla-2437110428633&ad_material_id=2,3249"> Link </a> |
| Arducam | Taking pictures and videos | $9.49 | <a href="https://www.amazon.com/Camera-Arducam-Raspberry-Raspbian-MotionEye/dp/B01LY05LOE/ref=asc_df_B01LY05LOE/?hvadid=692875362841&hvpos=&hvnetw=g&hvrand=17700836446167088182&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9032171&hvtargid=pla-2281435178578&psc=1&mcid=7c3d8b8b4acb3187a1cf954ff2bc5aee&hvocijid=17700836446167088182-B01LY05LOE-&hvexpln=73&gad_source=1"> Link </a> |
| Keyboard & Mouse | Typing and nativgating my raspberry pi | $26.49 | <a href="https://www.amazon.com/Camera-Arducam-Raspberry-Raspbian-MotionEye/dp/B01LY05LOE/ref=asc_df_B01LY05LOE/?hvadid=692875362841&hvpos=&hvnetw=g&hvrand=17700836446167088182&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9032171&hvtargid=pla-2281435178578&psc=1&mcid=7c3d8b8b4acb3187a1cf954ff2bc5aee&hvocijid=17700836446167088182-B01LY05LOE-&hvexpln=73&gad_source=1"> Link </a> |
| Transparent OLED | Displaying notifications, time, and weather | $19.99 | <a href="https://www.waveshare.com/1.51inch-transparent-oled.htm"> Link </a> |

# Other Resources/Examples
<!--One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.-->
- [How to set up a raspberry pi 4]([https://trashytuber.github.io/YimingJiaBlueStamp/](https://www.youtube.com/watch?v=BpJCAafw2qE))
- [How to connect a transparent oled to a raspberry pi 4]([https://sviatil0.github.io/Sviatoslav_BSE/](https://www.youtube.com/watch?v=wLKNSBn8kNw&t=3s))
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

<!--To watch the BSE tutorial on how to create a portfolio, click here.-->
