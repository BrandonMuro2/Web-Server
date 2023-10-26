# Web Server
![](https://www.tijuana.tecnm.mx/wp-content/uploads/2014/11/Heading-Ing-sistemas-768x252.png)
# Depto de Sistemas y Computación
# Ing. En Sistemas Computacionales
# SISTEMAS PROGRAMABLES 
# Autor (es): Muro Andrade Brandon Arturo
# Repositorio:  Web Server
# Fecha de revisión:  
# Objetivo: Encender y apagar el led de la pico w usando el web server

# CÓDIGO
```python
# Despliegue de web server mediante pico w
# Muro Andrade Brandon Arturo 20211818
# Tecnológico Nacional de México ITT
# Sistemas programables


import time
import network
import socket
from machine import Pin

led = machine.Pin('LED', machine.Pin.OUT)
ledState = "Led State Unknown"

button = Pin(14, Pin.IN, Pin.PULL_UP)

ssid = "INFINITUM6D7B"
password = "3405375900"

wlan = network.WLAN(network.STA_IF)
wlan.active(True)
wlan.connect(ssid, password)


html = """<!DOCTYPE html><html>
<head><meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="icon" href="data:,">
<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}
.buttonGreen { background-color: #4CAF50; border: 2px solid #000000;; color: white; border-radius:35px; padding: 15px 32px; text-align: center; text-decoration: none; display: inline-block; font-size: 16px; margin: 4px 2px; cursor: pointer; }
.buttonRed { background-color: #D11D53; border: 2px solid #000000;; color: white; border-radius:35px; padding: 15px 32px; text-align: center; text-decoration: none; display: inline-block; font-size: 16px; margin: 4px 2px; cursor: pointer; }
text-decoration: none; font-size: 30px; margin: 2px; cursor: pointer;}
</style></head>
<body style="background-color:black"><center><h1 style="color:white">Control Panel</h1></center><br><br>
<form><center>
<center> <button class="buttonGreen" name="led" value="on" type="submit">LED ON</button>
<br><br>
<center> <button class="buttonRed" name="led" value="off" type="submit">LED OFF</button>
</form>
<br><br>
<br><br>
<p>%s<p></body></html>
"""

max_wait = 10
while max_wait > 0:
    if wlan.status() < 0 or wlan.status() >= 3:
        break
    max_wait -= 1
    print("waiting for connection...")
    time.sleep(1)
    
if wlan.status() != 3:
    raise RuntimeError("network connection failed")
else:
    print("Connected")
    status = wlan.ifconfig()
    print("ip =" + status[0])
    
addr = socket.getaddrinfo("0.0.0.0", 80)[0][-1]
s = socket.socket()
s.bind(addr)
s.listen(1)
print("listening on", addr)

while True:
    try:
        cl, addr = s.accept()
        print("clien connected from", addr)
        request = cl.recv(1024)
        print("request:")
        print(request)
        request = str(request)
        led_on = request.find('led=on')
        led_off = request.find('led=off')
        
        print('led on =' + str(led_on))
        print( 'led off = ' + str(led_off))
        
        if led_on == 8:
            print('led on')
            led.value(1)
        if led_off == 8:
            print('led off')
            led.value(0)
            
        ledState = "LED is OFF" if led.value() == 0 else "Led is ON"
        
        if button.value() == 1:
            print("button NOT pressed")
            buttonState = "Button is NOT pressed"
        else:
            print("button pressed")
            buttonState = "Button is pressed"
            
        stateis = ledState + " and " + buttonState
        response = html % stateis
        cl.send('HTTP/1.0 200 OK\r\nContent-type: text/html\r\n\r\n')
        cl.send(response)
        cl.close()
        
    except OSError as e:
        cl.close()
        print('connection closed')



```

![image](https://github.com/BrandonMuro2/Web-Server/assets/80359445/9ba67d22-ee91-46ce-824d-41a4efd7f272)






