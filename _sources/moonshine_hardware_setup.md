---
jupytext:
  cell_metadata_filter: -all
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# 5. <span style="font-variant:small-caps;">MoonShineP</span>: Hardware setup
(content:hardware:materials)=
## Materials

| Item  | SKU/model number | Company  | Quantity required | Price | Link   | Note |
|-------|------------------|----------|-------------------|-------|--------|------|
| Raspberry Pi 4 model B kit (other iteration of Pi should also work) | B0B7DFF7TY | GeeekPi | 1 | $200 | [Amazon](https://www.amazon.com/GeeekPi-Raspberry-2GB-Starter-Kit/dp/B0B7DFF7TY/ref=sr_1_5?crid=1BQNGTSE4SSDJ&keywords=raspberry+pi+4+kit&qid=1674623289&sprefix=raspberry+pi+4+kit%2Caps%2C121&sr=8-5&ufe=app_do%3Aamzn1.fos.f5122f16-c3e8-4386-bf32-63e904010ad0) | [^1] |
| RTC module (DS3231) | 3-01-1198 | HiLetgo | 1 | $19 | [Amazon](https://www.amazon.com/HiLetgo-DS3231-Precision-Arduino-Raspberry/dp/B01N1LZSK3/ref=sr_1_2?crid=33U6N5U6HSRWK&keywords=rtc+ds+3231&qid=1674685955&sprefix=rtc+ds+3231%2Caps%2C72&sr=8-2) | - |
| Generic mouse and keyboard | - | - | 1 | ~40 | - | - |
| Generic HDMI monitor | - | - | 1 | ~$100 | - | - |
| SK6812 RGBW LED strip | SK68121M144-RGBWWW65 | BTF-Lighting | At least 2 | $35 | [Amazon](https://www.amazon.com/BTF-LIGHTING-Individually-Addressable-Flexible-Waterproof/dp/B01N2PCIB9/ref=sr_1_6?crid=3OWYKHJC7QV57&keywords=sk6812%2Bwarm&qid=1674596692&sprefix=sk6812%2Bwarm%2Caps%2C104&sr=8-6&th=1) | [^2] |
| 2.54mm pitch crimp connector kit | US_CNN019 | OCR | 1 | $12 |[ Amazon ](https://www.amazon.com/OCR-Connector-Housing-Assortment-640Pcs-Set/dp/B071JLCFT6/ref=sr_1_15?crid=1RCAOTD9CWZTD&keywords=gpio+connector+kit&qid=1674669821&sprefix=gpio+connector+kit%2Caps%2C91&sr=8-15)| - |
| 3-pin extension cable (pack of 2m x 4) | HL-LED79 | Hualand | At least 1 | $12 |[ Amazon ](https://www.amazon.com/Connector-WS2812B-Symphony-connectors%EF%BC%8CSM3P-controller/dp/B07G6PRDBQ/ref=sr_1_11?keywords=jst+3+pin+connector&qid=1674670291&sprefix=JST+3+pin+conn%2Caps%2C105&sr=8-11)| [^3] |
| 5V10A DC power adapter | P5V10A | BTF-Lighting | One per LED strip | $22 |[ Amazon ](https://www.amazon.com/BTF-LIGHTING-Plastic-Adapter-Transformer-WS2812B/dp/B01D8FM71S?ref_=ast_sto_dp&th=1)| [^4] |
| Lumber boards | - | Home Depot | One set per LED strip | $17 | - | [^5] |
| Generic matte black spray paint | - | - | 1 | $6 | - | - |
| Neutral Density (ND) filter sheets | - | Lee Filters | Depends | ~$20 per diffusion box | [BarnDoor](https://www.filmandvideolighting.com/lee-neutral-denisty-nd-gel-filter-sheet-film-video-photo-lighting.html) | [^6] |
| Light meter/spectrometer | - | - | 1 | $200 to >5000 | - | [^7] |
| UPS backup battery power | BE425M | APC | 1 | $60 | [Amazon](https://www.amazon.com/APC-Battery-Protector-Back-UPS-BE425M/dp/B01HDC236Q/ref=sr_1_7?keywords=uninterruptible%2Bpower%2Bsupply&qid=1674685859&sprefix=uninter%2Caps%2C105&sr=8-7&ufe=app_do%3Aamzn1.fos.006c50ae-5d4c-4777-9bc0-4513d670b6bc&th=1) | - |
|  |  |  |  |  |  |  |
```{note}
Estimated total cost for one <span style="font-variant:small-caps;">MoonShineP</span> system (excluding the illuminance measuring device) = $~560 (Feb 2023)
```

### Tools
- Wire stripper (for small gauge wire ~22-26 AWG)
- Needle pliers
- Wood hand saw
- Electric screwdriver

### Basic supplies
- Wood screws
- Masking tape
- Foam pieces

[^1]: Any equivalent kit of the Raspberry Pi 4 model B from other companies would work. Beside the Raspberry Pi itself, the kit should include all the accessories such as HDMI cable, power supply, case, SD card, etc. <span style="font-variant:small-caps;">MoonShineP</span> only requires the 2GB RAM option, but there are options for more RAM (4GB or 8GB).
    
    Prices are for February 2023. At the time of writing this document, the price of a Raspberry Pi is much higher than the official price due to supply issues.

[^2]: We strongly recommend the following options: 1m, 144 LED, Warm White. There are IP65 water resistant and IP30 normal versions available for this model. The user can choose one suitable for the required application. We picked warm white to avoid a substantial blue spike in the white channel spectrum. A different strip length and LED number can be selected, but the 1m 144 LED is the best suited for most situations, and the default options in the <span style="font-variant:small-caps;">MoonShineR</span> suite will work for this model.

    Two LED strips are required for moonlight recreation. More can be added, in multiples of two, to daisy chain many more strips, e.g., to generate a higher illuminance when recreating sunlight.
    
    The PWM flicker rate of an SK6812 LED strip is ~1.1 kHz. This flicker frequency is much higher than the critical flicker fusion rates of animals (~400 Hz) (Inger et al., 2014), and is therefore unlikely to be detectable to animal subjects.

[^3]: Buy enough connector cable to daisy chain the LED strips on opposite sides of the room.

[^4]: To achieve maximum intensity, each LED strip requires its own 5V-DC 10A power supply.

[^5]: The dimensions of the lumber boards for building one diffusion box:
    - Base: One of 5/4" x 6" x 4' [Home depot](https://www.homedepot.com/p/WeatherShield-5-4-in-x-6-in-x-4-ft-Premium-Ground-Contact-Pressure-Treated-Decking-Board-253944/300526781) ($6)
    - Two Sides: Two of 1" x 4" x 4' [Home depot](https://www.homedepot.com/p/WeatherShield-1-in-x-4-in-x-4-ft-Appearance-Grade-Pressure-Treated-Board-275086/300573653) ($7.4)
    - End caps: Cut down a 1" x 4" x 4' [Home depot](https://www.homedepot.com/p/WeatherShield-1-in-x-4-in-x-4-ft-Appearance-Grade-Pressure-Treated-Board-275086/300573653) ($3.7)

[^6]: The ND filter required depends on the user's room and setup. It should take two or three filters per diffusion box to dim the lightbox. See {ref}`content:lightbox:calibration` to determine which ND sheet transmission % values to try out (the user may need to experiment with different combinations of ND sheets to achieve the desired illuminance).
    
    Lee ND filter sheet types:
    - Lee 298 (ND 0.15, ½ Stop) = Transmission 69.3%
    - Lee 209 (ND 0.3, 1 Stop) = Transmission 51.2%
    - Lee 210 (ND 0.6, 2 Stop) = Transmission 23.5%
    - Lee 211 (ND 0.9, 3 Stop) = Transmission 13.7%
    - Lee 299 (ND 1.2, 4 Stop) = Transmission 6.6%
    
    Rosco is another company that makes ND filter sheets.

[^7]: A light meter that measures light intensity in the intergrated measure of illuminance (in unit lux). They are sometime confusingly refered to as radiometer. Measuring the light intensity is essential for calibrating <span style="font-variant:small-caps;">MoonShineP</span>. A spectrometer also measures light but without integration (i.e., losing) of wavelength data, measuring spectral irradiance. Spectrometers are generally more expensive than meter that measures lux, especially ones sensitive to low light levels.
    
    An accurate light measuring device is critical for calibrating MoonShineP, but it can be expensive. We do not want the cost of a low-light-sensitive meter to discourage the use of <span style="font-variant:small-caps;">MoonShineP</span> for moonlight related experiments. Still, it is critical to obtain a meter that has NIST-Traceable calibration, like the International Light Technology [ILT10C](https://www.intl-lighttech.com/products/ilt10c-luxlight-meter-nist-traceable-calibration?gclid=Cj0KCQiAw8OeBhCeARIsAGxWtUzB0YaQDpYF5fXMYee-U9zSQgnbmph4LrlmD0inkbSdw4FtaJ-AjjsaApwYEALw_wcB) (lower limit of 10 lx, cost ~$350). Lower cost meters do not perform accurately at moonlight level illuminance (i.e., <0.3 lx). Where only a non low-light sensitive meter is available (able to read below 1E-2 lx with a resolution +/- 1E-2 lx or better), like the ILT10C,  <span style="font-variant:small-caps;">MoonShineP</span> can be calibrated using a comparative method. See further explanations and solutions in {ref}`content:lightbox:radiometer`.
    
    We used the International Light Technology [ILT-5000](https://www.intl-lighttech.com/products/ilt5000-researchlab-radiometer) lux meter, with a SED 100-10/U broadband silicon photosensor, WU wide angle quartz diffuser, and Y4 photopic filter. This device is sensitive to light as low as 0.0002 lx.

(content:hardware:assemble)=
## Assemble Raspberry Pi and LED strips
```{figure} /images/raspberry-pi2.png
:name: schematic

Connection diagram for the Raspberry Pi, RTC module, and LED strips. The solid color circles (red, white, green) indicate the pins connected to the moonlight array of LED strips for moonlight recreation. The open color circles are connected to the sunlight/twilight array of LED strips, for the recreation of sunlight/twilight (while simutaneously re-creating moonlight). The wire color schematic is for the SK6812, made by BTF-Lighting, Guangdong, China. The RTC module connections are indicated in magenta circles. Right click and select ‘Open image in new tab’ to enlarge figure.
```
```{figure} /images/cable.png
:name: cable

Photo of the actual connection for one array of LED strips. Right click and select ‘Open image in new tab’ to enlarge figure.
```

1. Follow the Raspberry Pi kit assembly instruction.
2. On the bare wire end of the 3-pin cable female connector that comes with the LED strip, put on the female crimp connector. See [this guide](https://www.youtube.com/watch?v=JsoqBS1-k7M) for installing crimp pins with needle nose pliers. The user can decide whether to use three single-socket crimp connectors for each wire, or one 5-socket crimp connector for all three wires.
    - Follow {numref}`schematic` (solid color circles) for the pin locations of the first array of LED strips.
3. Plug in the fully assembled female crimp connector (after Step 2 is complete) into the Raspberry Pi GPIO pin so that the wires are connected to the correct locations.
    - Red wire goes into the 5V pin.
    - White wire goes into the ground pin.
    - Green wire goes into the GPIO 18 data pin.
4. Connect the 3-pin cable female connector to the male plug of the LED strip. (Add an extension cable if more length is required). Notice the arrows on the LED strip, this indicated the direction of the data flow (from Raspberry Pi to the array of LED strips).
5. Screw in the LED voltage-adding wires (red-positive and white-negative) to the power female connector that comes with the DC power supply. Pay attention to the polarity.
6. Plug in DC power supply.
7. Connect additional LED strips to this array from the other end (away from the Raspberry Pi) of the first LED strip. Add an additional DC power supply to each subsequent LED strips. Use 3-pin extension cable when needed to extend the distance between connections.

8. Install the real time clock (RTC) module as shown in {numref}`schematic`. Make sure that it goes into the correct location, occupying five of the GPIO pins.
9. (Optional) To add a second array of LED strips for re-creating sunlight/twilight, while also running moonlight re-creation, follow {numref}`schematic` (open color circles) for the pin locations of the second LED array. Repeat Steps 2-8.

    ```{figure} /images/RTC_pic.png
    :name: RTC_pic
    :width: 500px
    The Raspberry Pi with the RTC and both LED arrays (moonlight and sunlight/twilight) connected. This matches the schemetic of {numref}`schematic`.
    ```

10. (Optional) To prevent power surge and outage event interrupting the light schedule, connect all power to an uninterrupted power supply (UPS).

## Construction of diffusion box
- Make a diffusion box for each of the two LED strips used for moonlight recreation.

```{figure} /images/diffusion_box.png
:name: box

Construction of a diffusion box housing one LED strip.
```
1. Construct the wooden box as {numref}`box` with screws. With:
    - 5/4" x 6" x 4' board as the base.
    - A 1" x 4" x 4' board on each long side.
    - Cut down a 1" x 4" x 4' to get two end caps.
2. Spray paint the interior in matte black.
3. Affix the LED strip along the center of the bottom of the lightbox; the bottom of the LED strip has built-in adhesive.
4. Drill a small hole on each end of the box for the cables. Plug the hole with small pieces of foam to prevent light leakage.
5. Cut the ND filter sheets into the correct size, and secure them on the top opening with masking tape or pins. Ensure there are no light leaks. See {ref}`content:lightbox:calibration` for instructions on how many stacked ND filter sheets to use.