# IoT weather station

An Arduino MKR1010 reads temperature, humidity and light, smooths the readings and publishes them
to Arduino IoT Cloud, where they are plotted on a live dashboard.

Course project (TEL100, NMBU).

## Hardware

| Part | Pin | What it measures |
|---|---|---|
| Arduino MKR WiFi 1010 | — | board, WiFi over WiFiNINA |
| DHT11 | D7 | temperature and humidity |
| LDR (photoresistor) | A0 | ambient light |

## How it works

Raw sensor readings are noisy, so the sketch runs an **exponential moving average** over them
(`ALPHA = 0.2`, lower is smoother). The light sensor has no meaningful absolute scale, so the sketch
**auto-calibrates** by tracking the smallest and largest values it has seen and mapping new readings
onto that range. This means the station adapts to wherever it is placed instead of needing to be
tuned by hand.

Values are pushed to Arduino IoT Cloud through the variables declared in `thingProperties.h`, which
also holds the WiFi credentials.

## Setup

1. Create a Thing in Arduino IoT Cloud with the cloud variables used by the sketch.
2. Let the Cloud editor generate `thingProperties.h`, which contains `SECRET_SSID` and
   `SECRET_PASS`. **That file is not in this repository and should not be committed.**
3. Install the `WiFiNINA` and `DHT sensor library` libraries.
4. Flash the sketch and open the serial monitor to see the WiFiNINA firmware version and WiFi
   status while it connects.

## Note

If the readings look flat, check `DHTPIN` matches the pin you actually wired. The auto-calibration
range starts at 12-bit bounds for the MKR1010; an Uno reads 10-bit and needs `seenMin = 1023`.
