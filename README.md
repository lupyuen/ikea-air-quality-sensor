# IKEA VINDRIKTNING Air Quality Sensor connected to LoRaWAN and The Things Network with Apache NuttX OS

[__Follow the updates and pics on Twitter__](https://twitter.com/MisterTechBlog/status/1490147828458405889)

Let's transmit the PM 2.5 Sensor Data from IKEA VINDRIKTNING Air Quality Sensor to The Things Network (via LoRaWAN)...

-   We'll transmit the data with the IKEA Sensor connected to PineDio Stack BL604 running Apache NuttX OS

-   We'll visualise the Sensor Data with Prometheus and Grafana

I found the VINDRIKTNING sensor at my local IKEA Store (IKEA Tampines Singapore), in the Lighting Section, near the Air Purifiers.

(Wow IKEA has Air Purifiers now)

[Watch the IKEA Sensor on YouTube](https://youtu.be/wyXb3aSPet4)

# Install App

To add this repo to your NuttX project...

```bash
## TODO: Change this to the path of our "incubator-nuttx-apps/examples" folder
pushd nuttx/apps/examples
git submodule add https://github.com/lupyuen/ikea_air_quality_sensor
popd
```

Then update the NuttX Build Config...

```bash
## TODO: Change this to the path of our "incubator-nuttx" folder
cd nuttx/nuttx

## Preserve the Build Config
cp .config ../config

## Erase the Build Config and Kconfig files
make distclean

## For BL602: Configure the build for BL602
./tools/configure.sh bl602evb:nsh

## For ESP32: Configure the build for ESP32.
## TODO: Change "esp32-devkitc" to our ESP32 board.
./tools/configure.sh esp32-devkitc:nsh

## Restore the Build Config
cp ../config .config

## Edit the Build Config
make menuconfig 
```

In menuconfig, enable the IKEA Air Quality Sensor App under "Application Configuration" → "Examples".

# Configure Apache NuttX OS

We configure the UART Port on Apache NuttX OS for the IKEA Sensor...

```bash
make menuconfig
```

Enable UART1:
- Select "System Type → BL602 Peripheral Support"
- Check "UART1"

Set to 9600 bps:
- Select "Device Drivers → Serial Driver Support → UART1 Configuration"
- Set "BAUD rate" to 9600

Enable `cat`:
- Select "Application Configuration → NSH Library → Disable Individual commands"
- Uncheck "Disable cat"

Build and flash NuttX OS to PineDio Stack BL604.

IKEA Sensor is now connected to NuttX OS at `/dev/ttyS1`

# Solder UART Port on IKEA Sensor

We expose the UART Port on IKEA Sensor by soldering these pads...

| UART Pin | IKEA Sensor | Wire Colour
|:---|:--:|:---
| UART Tx | REST | Blue
| GND | GND | Black

[See the soldering steps](https://twitter.com/MisterTechBlog/status/1489506811321282565?t=uW3yDgeLLXhf4x3wjmC2HA&s=19)

[(More details)](https://style.oversubstance.net/2021/08/diy-use-an-ikea-vindriktning-air-quality-sensor-in-home-assistant-with-esphome/)

# Connect to PineDio Stack BL604

To transmit the PM 2.5 readings to The Things Network via LoRaWAN, we connect the IKEA Sensor to [PineDio Stack BL604](https://lupyuen.github.io/articles/pinedio)...

| Function | GPIO | PineDio Stack | IKEA Sensor | Wire Colour
| :---: | :---: | :---: | :---: | :---:
| RX | GPIO 3 | 14 | REST | Blue
| TX | GPIO 4 | 13 | Unused |
| GND | GND | 20 | GND | Black

Connect USB Ports of IKEA Sensor and PineDio Stack BL604 to the same computer. Remember: Only One Power Source!

PineDio Stack BL604 has a onboard Semtech SX1262 LoRa Transceiver, so it talks to LoRaWAN and The Things Network.

[(More about LoRaWAN On PineDio Stack)](https://lupyuen.github.io/articles/lorawan3)

# Output Log

Run the following command to test the IKEA Sensor on NuttX OS...

```text
nsh> ikea_air_quality_sensor
16  11  0b  00  00  00  17  00  00  02  ff  00  00  00  21  02  00  00  0b  88
Got PM2.5 Concentration: 23 µg/m³
16  11  0b  00  00  00  17  00  00  02  ff  00  00  00  21  02  00  00  0b  88
Got PM2.5 Concentration: 23 µg/m³
16  11  0b  00  00  00  18  00  00  03  04  00  00  00  22  02  00  00  0b  80
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  04  00  00  00  22  02  00  00  0b  80
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  04  00  00  00  22  02  00  00  0b  80
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  03  00  00  00  22  02  00  00  0b  81
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  02  00  00  00  22  02  00  00  0b  82
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  02  00  00  00  22  02  00  00  0b  82
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  02  00  00  00  22  02  00  00  0b  82
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  04  00  00  00  22  02  00  00  0b  80
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  04  00  00  00  22  02  00  00  0b  80
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  03  00  00  00  22  02  00  00  0b  81
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  03  00  00  00  22  02  00  00  0b  81
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  18  00  00  03  02  00  00  00  22  02  00  00  0b  82
Got PM2.5 Concentration: 24 µg/m³
16  11  0b  00  00  00  17  00  00  03  01  00  00  00  21  02  00  00  0b  85
Got PM2.5 Concentration: 23 µg/m³
16  11  0b  00  00  00  17  00  00  03  00  00  00  00  21  02  00  00  0b  86
Got PM2.5 Concentration: 23 µg/m³
```

[Watch the demo on YouTube](https://youtu.be/dUHlG67pB3M)

# Test with Bus Pirate

Before testing with Apache NuttX OS, we sniffed the IKEA Sensor's UART Port with Bus Pirate. Connect Bus Pirate to IKEA Sensor as follows...

| Bus Pirate | IKEA Sensor | Wire Colour
|:---|:--:|:---
| Data In (MISO) | REST | Blue
| GND | GND | Black

Connect USB Ports of IKEA Sensor and Bus Pirate to the same computer. Remember: Only One Power Source!

Enter these Bus Pirate commands to capture the UART output from IKEA Sensor (9600 bps, 8 bits, no parity, 1 stop bit)...

```text
HiZ>m
1. HiZ
2. 1-WIRE
3. UART
4. I2C
5. SPI
6. 2WIRE
7. 3WIRE
8. KEYB
9. LCD
10. PIC
11. DIO
x. exit(without change)

(1)>3
Set serial port speed: (bps)
 1. 300
 2. 1200
 3. 2400
 4. 4800
 5. 9600
 6. 19200
 7. 38400
 8. 57600
 9. 115200
10. Input Custom BAUD
11. Auto-Baud Detection (Activity Required)

(1)>5
Data bits and parity:
 1. 8, NONE *default 
 2. 8, EVEN 
 3. 8, ODD 
 4. 9, NONE
(1)>
Stop bits:
 1. 1 *default
 2. 2
(1)>
Receive polarity:
 1. Idle 1 *default
 2. Idle 0
(1)>
Select output type:
 1. Open drain (H=Hi-Z, L=GND)
 2. Normal (H=3.3V, L=GND)

(1)>
Clutch disengaged!!!
To finish setup, start up the power supplies with command 'W'

Ready
UART>W
POWER SUPPLIES ON
Clutch engaged!!!
```

[Watch the demo on YouTube](https://youtu.be/QOJF6hAhFv4)

See below for the ASCII and Binary Logs.

[(More about Bus Pirate interfacing with UART)](http://dangerousprototypes.com/docs/UART)

## ASCII Log

ASCII Log of UART Output from IKEA Sensor...

[Watch the demo on YouTube](https://youtu.be/QOJF6hAhFv4)

```text
UART>(2)
Raw UART input
Any key to exit

<@:
   <B:
[6C:;8
[9C:;8
[12C9:7 
[16C997!
[20C987"
[24C876%
[28C876%
[32C876%
[36C866&
[40C856'
[44C745*
[48C745*
[52C745*
[56C745*
[60C735+
[64C614/
[68C614/
[72C614/
[76C614/
[79C
[37;80H 624.
[4C624.
[8C624.
[12C614/
[16C614/
[20C614/
[24C6040
[28C6040
[32C5/33
[36C5.34
[40C4,28
[44C4,28
[48C5-35
[52C5.34
[56C5.34
[60C5-35
[64C4,28
[68C2%0C
[72C2%0C
[76C2&0B
[79C
[37;80H 2'0A
[4C3)1=
[8C3*1<
[12C4+29
```

[(See the complete log)](https://gist.github.com/lupyuen/f40454dda8e3d7f279fb6ef721add465)

## Binary Log

Binary Log of UART Output from IKEA Sensor...

```text
UART>{
UART LIVE DISPLAY, } TO STOP
UART>
READ: -f 0x00
UART>
READ: -f 0x00
UART>
READ: -f 0x00
UART>
READ: 0x16
UART>
READ: 0x11
UART>
READ: 0x0B
UART>
READ: 0x00
UART>
READ: 0x00
UART>
READ: 0x00
UART>
READ: 0x3D
UART>
READ: 0x00
UART>
READ: 0x00
UART>
READ: 0x03
UART>
READ: 0x45
UART>
READ: 0x00
UART>
READ: 0x00
UART>
READ: 0x00
UART>
READ: 0x3B
UART>
READ: 0x01
UART>
READ: 0x00
UART>
READ: 0x00
UART>
```

[(See the complete log)](https://gist.github.com/lupyuen/db0c97b12bd1070e17cd2e570a5aa810)

[(Another binary log)](https://gist.github.com/lupyuen/ebe4c0628fc9ea2e124e6f00d8246b49)

# Decode Sensor Data

Based on the [PM1006 Datasheet](https://github.com/arendst/Tasmota/files/7083662/PM1006_LED_PARTICLE_SENSOR_MODULE_SPECIFICATIONS.pdf), we decode the Sensor Data (20 bytes) as follows...

```text
Header:   0x16 0x11 0x0B
Unused:   0x00 0x00
PM2.5:    0x00 0x36
Unused:   0x00 0x00 0x03 0x32 0x00 0x00
Unused:   0x00 0x34 0x01 0x00 0x00 0x00
Checksum: 0x2E
```

[(Source)](https://gist.github.com/lupyuen/db0c97b12bd1070e17cd2e570a5aa810#file-ikea-binary-log-L7705-L7743)

This gives the PM 2.5 value of 54 (`0x0036`).

To validate the Checksum, all 20 bytes must add up to 0.

[(More details)](https://github.com/arendst/Tasmota/issues/13012)

[(And this)](https://community.home-assistant.io/t/ikea-vindriktning-air-quality-sensor/324599)

[(ESPHome Source Code)](https://github.com/esphome/esphome/blob/dev/esphome/components/pm1006/pm1006.cpp#L57-L96)

[(Arduino Source Code)](https://github.com/Hypfer/esp8266-vindriktning-particle-sensor/blob/master/src/SerialCom.h#L26-L63)

# Test with Apache NuttX OS

Here's a quick way to test the IKEA Sensor with NuttX OS. Enter these NuttX commands to read the UART port and dump the data...

```text
nsh> ls /dev
/dev:
 console
 gpio0
 gpio1
 gpio2
 null
 spi0
 spitest0
 timer0
 ttyS1
 urandom
 zero
nsh> cat /dev/ttyS1

3(1>
    2'0A
        2%0C
            1$/F
                .,T
                   .,T
                      .
                       .,V
                          .,V
                             -+Y
                                -+Y
```

[Watch the demo on YouTube](https://youtu.be/iFf8_f7ExUI)

To see the binary data, modify the GPS Demo App: [gps_main.c](https://github.com/lupyuen/incubator-nuttx-apps/blob/master/examples/gps/gps_main.c)...

```c
/* Read until we complete a line */
cnt = 0;
do
  {
    read(fd, &ch, 1);
    //  Insert this line to dump the data in hex:
    printf("%02x  ", ch);
```

Build and run the modified GPS Demo App...

```text
nsh> gps
00  00  
16  11  0b  00  00  00  39  00  00  03  39  00  00  00  37  01  00  00  00  21  
16  11  0b  00  00  00  2b  00  00  03  17  00  00  00  29  01  00  00  00  5f  
16  11  0b  00  00  00  32  00  00  03  26  00  00  00  30  01  00  00  00  42 
16  11  0b  00  00  00  31  00  00  03  24  00  00  00  2f  01  00  00  00  46  
16  11  0b  00  00  00  31  00  00  03  24  00  00  00  2f  01  00  00  00  46  
16  11  0b  00  00  00  31  00  00  03  23  00  00  00  2f  01  00  00  00  47  
16  11  0b  00  00  00  31  00  00  03  22  00  00  00  2f  01  00  00  00  48  
16  11  0b  00  00  00  30  00  00  03  21  00  00  00  2e  01  00  00  00  4b  
16  11  0b  00  00  00  2f  00  00  03  1f  00  00  00  2d  01  00  00  00  4f  
16  11  0b  00  00  00  2f  00  00  03  1f  00  00  00  2d  01  00  00  00  4f  
16  11  0b  00  00  00  2f  00  00  03  1f  00  00  00  2d  01  00  00  00  4f  
16  11  0b  00  00  00  2f  00  00  03  1e  00  00  00  2d  01  00  00  00  50  
16  11  0b  00  00  00  2f  00  00  03  1e  00  00  00  2d  01  00  00  00  50  
16  11  0b  00  00  00  2f  00  00  03  1d  00  00  00  2d  01  00  00  00  51  
16  11  0b  00  00  00  2e  00  00  03  1c  00  00  00  2c  01  00  00  00  54  
16  11  0b  00  00  00  2e  00  00  03  1c  00  00  00  2c  01  00  00  00  54  
16  11  0b  00  00  00  2e  00  00  03  1c  00  00  00  2c  01  00  00  00  54
```

[Watch the demo on YouTube](https://youtu.be/TyG-dJCx8OQ)

Yep we see the 20-byte frames of Sensor Data, and the PM 2.5 encoded inside!

PM 2.5 = 46 (`0x002e`)

# Connect to LoRaWAN and The Things Network

TODO

# Visualise with Prometheus and Grafana

TODO

# References

-   ["Use an IKEA VINDRIKTNING air quality sensor in Home Assistant with ESPHome"](https://style.oversubstance.net/2021/08/diy-use-an-ikea-vindriktning-air-quality-sensor-in-home-assistant-with-esphome/)

-   [IKEA VINDRIKTNING Manual](https://www.ikea.com/us/en/manuals/vindriktning-air-quality-sensor__AA-2289325-1.pdf)
