(ch3)=
WiFi & Door Defrost
=======================
Location: Frost Tower  (Door defrosting)

Grimy McTrollkins asks for help in getting access to the thermostat controlling the freezing status of the Frosty Tower Door.
Fortunately, the thermostat was connected via WI-FI and actually, we got a WI-FI adapter while entering in the North Pole, so we can try to use it here. <br />

By opening the WI-FI cli cranberry (Your badge --> Items) we accessed the challenge. <br />
![WIFI](images/WI-FI.png)

 
```
elf@9128a459c611:~$ iwlist scanning
wlan0     Scan completed :
          Cell 01 - Address: 02:4A:46:68:69:21
                    Frequency:5.2 GHz (Channel 40)
                    Quality=48/70  Signal level=-62 dBm
                    Encryption key:off
                    Bit Rates:400 Mb/s
                    ESSID:"FROST-Nidus-Setup"
```

let's try to connect to "FROST-Nidus-Setup" AccessPoint
```
elf@9128a459c611:~$ iwconfig wlan0 essid FROST-Nidus-Setup
** New network connection to Nidus Thermostat detected! Visit http://nidus-setup:8080/ to complete setup
(The setup is compatible with the 'curl' utility)
```

well, of course we'll go there ... !
```
elf@7e3ecfce0672:~$ curl http://nidus-setup:8080/
```
Showed us the “Nidus Thermostat API” and playing with some of the endpoints
<br/>
```
elf@7e3ecfce0672:~$ curl http://nidus-setup:8080/apidoc
```
we arrived here
```
elf@7e3ecfce0672:~$ curl -XGET http://nidus-setup:8080/api/cooler
```
this endpoint showed the actual temperature
<br />
we just have to try to change the temperature then
```
elf@7e3ecfce0672:~$ curl -XPOST -H 'Content-Type: application/json' \
  --data-binary '{"temperature": 1}' \
  http://nidus-setup:8080/api/cooler
Response: 
​​{
  "temperature": 0.48,
  "humidity": 43.66,
  "wind": 1.82,
  "windchill": 1.11,
  "WARNING": "ICE MELT DETECTED!"
}
```
<br />
We changed the temp to 1 (not following the advice not to go over 0 ... of course :)! ) 
<br />
Done.
