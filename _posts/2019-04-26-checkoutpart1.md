---
published: true
---
## Toy Shopping Checkout - Part 1

I've had an idea for a few years now, about creating a fully working childrens toy checkout and being able to scan toy food items in real time. My initial thoughts were to use a Raspberry Pi, a USB barcode scanner and some Python to link it all up. I was originally put off by the cost of barcode scanners, so shelved the idea.

Around a month ago, I stumbled upon this [page](https://www.pyimagesearch.com/2018/05/21/an-opencv-barcode-and-qr-code-scanner-with-zbar/), which had code examples and explanations on how to use a Pi camera to read barcodes. I got the code working reading a QR code from a random image and realised that I could probably do this, so ordered a cheap Pi camera online ($13). Once that arrived, it didn't take long to get it scanning QR codes off my phone screen.

I whipped up a really dirty script to create some QR codes image files, using pyqrcode.  I then pushed them into a sqllite database, complete with dodgy SQL injection vulnerable code.

```python
import pyqrcode
from pyqrcode import QRCode
from data import Data

data = Data()

prefix = "a-"

items = ["Corn", "Beef", "Cheese", "Milk", "Peas", "Washing Power"]
prices= [1.50, 3.34, 6.00, 5.50, 1.34, 10.54]

for id in range(1,6):
    code = prefix + str(id)
    qrcode = pyqrcode.create(code)
    qrcode.png('./qrcodes/' + code + '.png', scale=6)
    sqlInsert = "insert into items values ('" + code + "', '" + items[id] + "', " + str(prices[id]) + ");"    
    data.insertAdhoc(sqlInsert)
```    

My main scanning program reads the QR code from one of these images, looks it up in the database and returns the item name and price. 

I needed a GUI and not being a Python expert, I did a quick google. The first option I tried was [guizero](https://lawsie.github.io/guizero/). I struggled a lot with trying to lay out the UI nicely. I had to put the scanning on a separate thread, as it constantly scans looking for codes and would lock up the UI otherwise. 

After a few days of wrestling with this, I googled around and found [PYQT](https://wiki.python.org/moin/PyQt), which has a nice-ish drag and drop editor. I hit a few snags with it needing to use qtThreads, as opposed to normal Python threads, but managed to iron out the kinks and get a working program. Writing to the GUI from threads required some investigation around Signals and Events, but once I got it sussed, it was fairly straight forward. However, it looked very basic and windows form like, which is the opposite of how I wanted it to look.

![image-1.png]({{site.baseurl}}/images/image-1.png)

I hunted around and found a cheap ($11) [GUI pack ](https://creativemarket.com/pzUH/18667-Cartoon-Games-GUI-Pack-9) and spent a few nights carving out the elements and building the UI. Most of the buttons and elements have zero style set in QT and just display a background image. From my needs it works well enough, but I'd take a different approach on a larger project.

![image-2.PNG]({{site.baseurl}}/images/image-2.PNG)

With the UI taking shape, I needed to build the actual physical checkout, which I'll cover in Part 2.
