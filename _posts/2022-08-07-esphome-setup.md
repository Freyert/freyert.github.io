---
layout: default
author: Fulton Byrne
title: "ESPHome Setup"
---

I've been trying to understand why my home ocassionally experiences high humidity. Consumer IoT sensors have helped
me understand the problem much better, but the more I use these consumer products the more issues I find:

1. Sensors can be expensive. Most humidity sensors aren't expensive, but when you want a _lot_ of sensors it can get pricey.
2. Consumer sensors typically sandbox the sensor data inside a propietary app with poor data visualization capabilities.
3. The data sandbox also makes it difficult to see trends across multiple sensors.

A coworker told me about [homeassistant] as a solution to some of these issues. Homeassistant provides functionality
akin to Apple Homekit or Google Nest, but with the ability to self host. Plus, if there is a smart device out there, someone
has attempted at least a basic integration with homeassistant.


homeassistant made it trivial to build a _single_ dashboard with my consumer smart thermostat, and consumer air quality sensors.
Now what if there was a way to avoid buying expensive, branded IoT sensors? Well there is! Using the [ESPHome] platform I can
use [ESP8266]/[ESP32] microcontrollers plus a wide array of sensors to build cheap custom IoT sensors suited to my needs.

## Tooling

ESPHome provides a homeassistant plugin that can drive the whole lifecycle of a sensor, but I found it much easier
to use the `esphome` CLI directly. I recommend running the following commands on Mac to get started:

* `brew install esphome`
  + Main tool for working with `esphome` devices.
* `brew install platformio`
  + Adds some capabilities for working with microcontrollers, and `esphome` _may_ use the tool if needed.


## Configuration

I built the ESPHome configuration using the homeassistant plugin, and then would copy the configuration locally 
to use with the CLI. Here is my configuration with the sensitive bits hidden:


```yaml
esphome:
  name: "SCD41-basement"
  platformio_options:
    board_build.flash_mode: dio
esp32:
  board: esp32-c3-devkitm-1
  variant: ESP32C3
  framework:
    type: esp-idf

# Enable logging
logger:

# Enable Home Assistant API
api:
  encryption:
    key: !secret SCD41-key

ota:
  password: !secret SCD41-ota

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  # Enable fallback hotspot in case wifi connection fails
  ap:
    ssid: "SCD41-Basement"
    password: !secret SCD41-ap

i2c:
  - id: bus_a
    sda: 1
    scl: 0
    scan: true
sensor:
  - platform: scd4x
    id: scd41
    i2c_id: bus_a
    co2:
      name: "SCD41 CO2"
    temperature:
      name: "SCD41 Temperature"
    humidity:
      name: "SCD41 Humidity"
```

I'm using an [SCD41] True CO2, Temperature, and Humidity Sensor. From https://esphome.io/ you can find ESPHome's documentation for integrating with any [SCD4x] sensor.
[SCD41] is a bit pricey if I'm only interested in temperature and humidity. In the future I would use a [DHT] family sensor which can be acquired for around $5 ([DHT11]).
Adafruit has a [good guide comparing the DHT family sensors](https://learn.adafruit.com/dht). Putting it into perspective, I could have deployed ~10 DHT11 sensors for the price
I paid for the SCD41.

The SCD41 also supports inter integrated circuit ([i2c]). When shopping on Sparkfun look [qwiic] boards or  [STEMMA/STEMMAQT](https://learn.adafruit.com/introducing-adafruit-stemma-qt)
on Adafruit. I plugged a [Qwiic Cable - Breadboard Jumper (4-pin)](https://www.sparkfun.com/products/14425) into the SCD41's i2c port and then connected the Serial Clock cable (yellow)
to GPIO0 on the ESP32, Serial Data cable (blue) to GPIO1 on the ESP32, the Ground (black) to a GND on the ESP32, and finally the 3.3V cable (red) to a 3.3V port on the ESP32. I did this
all with a breadboard so mistakes were easier to deal with.

Refer back to the configuration and you will see that I defined an `i2c` block with the `i2c` bus `bus_a` with `sda` connected to the ESP's GPIO1 and `scl` attached to the ESP's GPI0.
Then when defining the sensor I indicate the `i2c` bus the sensor is attached to. The really cool thing about `i2c` is I could chain multiple sensors together instead of connecting them
all directly to the ESP. If had multiple sensors chained together I would just indicate that they all used the same `i2c` bus and theoretically it should just work!

Pretty simple!


## Compiling and Installing

The next step with `esphome` is to compile the configuration. I copied the configuration from homeassistant, plus a `secrets.yaml` file containing all the sensitive information.

```console
esphome compile humidity-bedroom.yaml secrets.yaml
```

This outputs a _lot_ of compilation information. I really appreciated this over the homeassistant UI's spinner without logs because I could tell what was happening.

Once the compilation completed, I connected the ESP32 to my computer via a USB cable and ran the following:


`esphome upload humidity-bedroom.yaml secrets.yaml`

I then chose the method to upload. 1/2 are equivalent and 3 only works if the device has been configured once. Over the air
configuration is really helpful if you have some minor configuration changes and don't want move the device.

```
INFO Reading configuration humidity-bedroom.yaml...
Found multiple options, please choose one:
  [1] /dev/cu.SLAB_USBtoUART (CP2102N USB to UART Bridge Controller)
  [2] /dev/cu.usbserial-1460 (CP2102N USB to UART Bridge Controller)
  [3] Over The Air (scd43-basement.local)
(number): 1
esptool.py v3.3
Serial port /dev/cu.SLAB_USBtoUART
Connecting....
Chip is ESP32-C3 (revision 3)
Features: Wi-Fi
Crystal is 40MHz
MAC: 84:f7:03:5f:ce:a0
Uploading stub...
Running stub...
Stub running...
Changing baud rate to 460800
Changed.
Configuring flash size...
Auto-detected Flash size: 4MB
Flash will be erased from 0x00010000 to 0x000d4fff...
Flash will be erased from 0x00000000 to 0x00005fff...
Flash will be erased from 0x00008000 to 0x00008fff...
Flash will be erased from 0x0000d000 to 0x0000efff...
Compressed 804560 bytes to 488148...
Wrote 804560 bytes (488148 compressed) at 0x00010000 in 14.5 seconds (effective 443.9 kbit/s)...
Hash of data verified.
Compressed 20672 bytes to 12201...
Wrote 20672 bytes (12201 compressed) at 0x00000000 in 0.7 seconds (effective 251.0 kbit/s)...
Hash of data verified.
Compressed 3072 bytes to 129...
Wrote 3072 bytes (129 compressed) at 0x00008000 in 0.1 seconds (effective 355.3 kbit/s)...
Hash of data verified.
Compressed 8192 bytes to 31...
Wrote 8192 bytes (31 compressed) at 0x0000d000 in 0.1 seconds (effective 468.4 kbit/s)...
Hash of data verified.
```


## Troubleshooting

The first few flashes I noticed that the device failed to come online in the esphome dashboard.

Using `esphome logs $CONFIGURATION_YAML` I was able to see the following log repeating endlessely:


```
[12:33:54]ESP-ROM:esp32c3-api1-20210207
[12:33:54]Build:Feb  7 2021
[12:33:54]rst:0x7 (TG0WDT_SYS_RST),boot:0xc (SPI_FAST_FLASH_BOOT)
[12:33:54]Saved PC:0x40049a42
[12:33:54]SPIWP:0xee
[12:33:54]mode:QIO, clock div:1
[12:33:54]load:0x3fcd6100,len:0x17f4
[12:33:54]ets_loader.c 78
```

It turned out the solution was to download `platformio`, upgrade to the latest version, and then 
add the following stanza to the configuration:

```yaml
esphome:
  #...
  platformio_options:
    board_build.flash_mode: dio

```

([esphome/issues#2931](https://github.com/esphome/issues/issues/2931))

Afterwards I was rewarded with some really robust logs showing that the ESP had connected to the WiFI,
it's sensors were configured, and every few minutes I would receive a log indicating new datapoints.


## Conclusion

Once I found my footing, ESPHome was a real delight. I now have access to high fidelity data about my home on my terms.
In order to improve the setup in the future I'm looking at a few items:

1. A better home router that supports VLANs so I can isolate the ESP devices from the rest of my network.
2. Finding a more thrifty ESP unit.
3. Using the DHT11 (thrifty).
4. Investigating battery power source options.
5. Leaving the breadboard behind.



## Resources

* [Homeassistant Forum ESP Board Recommendations](https://community.home-assistant.io/t/whats-your-favourite-esp32-board-best-good-cheap-quality-reliable/380023)
* [Reddit ESPHome Board Recommendations](https://www.reddit.com/r/Esphome/comments/oz6lfk/whats_your_favorite_esphome_hardware/)
  + [LOLIN D1 Mini Pro](https://www.aliexpress.com/item/2251832538377762.html?gatewayAdapt=4itemAdapt)




[homeassistant]: https://www.home-assistant.io/
[ESPHome]: https://esphome.io/
[ESP8266]: https://www.espressif.com/en/products/socs/esp8266
[ESP32]: https://www.espressif.com/en/products/socs/esp32
[SCD41]: https://www.adafruit.com/product/5190?gclid=Cj0KCQjwxb2XBhDBARIsAOjDZ34OatzGjL5zPYbnxxurjllqlZWZp5O59181n_1t5AAP6d7poojXYHkaAnCUEALw_wcB
[SCD4x]: https://esphome.io/components/sensor/scd4x.html
[DHT]: https://esphome.io/components/sensor/dht.html
[DHT11]: https://www.adafruit.com/product/386
[i2c]: https://en.wikipedia.org/wiki/I%C2%B2C
[qwiic]: https://www.sparkfun.com/qwiic