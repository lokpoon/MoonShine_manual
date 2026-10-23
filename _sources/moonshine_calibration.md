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

(content:lightbox:calibration)=
# 7. <span style="font-variant:small-caps;">MoonShineP</span>: Calibration

## Moonlight re-creation
- To achieve realistic moonlight level illuminance, the LED arrays must be physically dimmed. We layer neutral density (ND) filter paper on the lightbox to reduce the light intensity, without modifying the color spectrum ({numref}`box`)), until a ‘calibration value’ is achieved.

- The user must first decide whether to re-create unobstructed ground illumination (i.e., a simulation in which there is no additional dimming), or to recreate light with a desired attenuation level so as to mimic the illumination levels of a natural habitat.

- For the re-creation of full moon illuminance with no additional dimming, we recommend a ‘calibration value’ exceeding 0.36 lx but less than approximately 0.5. In brief, this value is achieved as follows: 1)  Configure the LED array to its maximum brightness (i.e., all LEDs illuminated at their maximum value for the particular RGBW ratio used).  2) Using a low-light meter to monitor the light level at a fixed position near the experimental arena. 3) Add layers of neutral density filter papers onto the light box until the light is dimmed to > 0.36 lx but < approximately 0.5 lx when measured by the light meter at its fixed position.

- The justification for the > 0.36 < ~ 0.5 range of calibration values is that the maximum possible ground illuminance of a full moon is 0.36 lx (see Section 4.2 of the paper); most full moons are much dimmer than this. Henceforth we use the example of a calibration value of 0.4 lx. It is important to understand that a calibration value of 0.4 lx does not mean that the full moon re-created by MoonShine will be 0.4 lx. Instead, this calibration value simply ensures that any illuminance below 0.4 lx will be re-created accurately. By calibrating the lightbox to a value exceeding 0.36 lx but less than ~ 0.5 lx (the closer to 0.36 lx, the better), we ensure that <span style="font-variant:small-caps;">MoonShineP</span> can precisely re-create, with optimal accuracy, the entire range of natural moonlight illuminance predicted by <span style="font-variant:small-caps;">MoonShineR</span>. 

(content:calibration_schedule)=
## Create a calibration schedule
Use the provided `manual_scheduler.xlsx` (see {ref}`content:excel_scheduler`) to create a `LED_schedule_moon.csv` that turns on the LEDs at full intensity (with spectral control applied).
1. In `manual_scheduler.xlsx`, set theoretical_max to the calibration value (here we continue with the example of 0.4 lx). 
2. Fill the entire column of "desired illuminance" with 0.4. This tells the LEDs to produce 100% intensity (i.e., 0.4/0.4 = 100%).
3. Set the spectral control as desired (leave it as default for natural moonlight spectrum).
4. Make the schedule to last for 2 hours (or a period sufficient to perform the calibration period) for a time period in the near future, at the time the calibration will be performed.
5. Follow the rest of the instructions in {ref}`content:excel_scheduler` to save the `LED_schedule_moon.csv`
6. This schedule file will instruct <span style="font-variant:small-caps;">MoonShineP</span> to generate full intensity light for the purpose of calibration.

### Which ND filter sheets?

Lee ND filter sheets are available with multiple light transmission values. For example:
- Lee 298 (ND 0.15, ½ Stop) = Transmission 69.3%
- Lee 209 (ND 0.3, 1 Stop) = Transmission 51.2%
- Lee 210 (ND 0.6, 2 Stop) = Transmission 23.5%
- Lee 211 (ND 0.9, 3 Stop) = Transmission 13.7%
- Lee 299 (ND 1.2, 4 Stop) = Transmission 6.6%

To make an informed decision on what combination of ND filter sheets is needed for the user's specific room and setup, perform the following steps:

1. Position the lightboxes at their permanent locations. Ideally the lightboxes should be put on shelves at opposite sides of the room, so that the light is directed to the ceiling and diffusely illuminates the entire room. The room should preferably have a white or light-shaded ceiling so as to minimize the change in the light spectrum.
2. Using the previously created calibration `LED_schedule_moon.csv`, launch <span style="font-variant:small-caps;">MoonShineP</span> (see {ref}`content:launch`).
3. Using a light meter, measure the illuminance at the position of the animal enclosure. Let us say that it measures 100 lx. 
- To target a full moon illuminance > 0.36 < ~0.5 lx, begin by estimating the light value that will be produced by the lightbox using a combination of Lee filters stacked onto the opening of the lightbox.
- For instance, if two single Lee ND 299 sheet (each with 6.6% transmission) were used to filter the LEDS, the illumination would be reduced from 100 lx to approximately 0.44 lx (100 x 0.066 x 0.066). Since this value is close to the target range, this would be a good starting point.
- The above calculation is only to get a crude idea of which ND filter to buy. We recommend buying some additional sheets with high transmission percentages (i.e. sheets that block only small amounts of light) for fine-tuning purposes. Next we must calibrate <span style="font-variant:small-caps;">MoonShineP</span> by measuring the illuminance.


(content:lightbox:radiometer)=
###  Calibrating <span style="font-variant:small-caps;">MoonShineP</span> with a light meter
- Using the previously created calibration `LED_schedule_moon.csv`, launch <span style="font-variant:small-caps;">MoonShineP</span> (see {ref}`content:launch`).
- Next, the calibration method depends on whether a low light sensitive meter (able to accurately measure 0.01 lx) is available.
    - If available,
        1. Layer filters on the lightbox until the light meter sensor placed on the ground of the animal housing reads a target illuminance between 0.36 lx and ~0.5 lx. Because every additional ND filter layers attenuate illuminance in a certain fraction, it can be difficult to attenuate the illuminance to within this narrow range. We recommend purchasing multiple filter papers with small light-attenuation percentages to make this process easier. 
        2. Once a value between 0.36 and ~0.5 lx is achieved, record this illuminance measurement. This is the calibration value.
        3. Put in this calibration value as the theoretical_max value in <span style="font-variant:small-caps;">MoonShineR</span>: Moonlight led scheduler_ when generating a `LED_schedule_moon.csv`. The <span style="font-variant:small-caps;">MoonShineP</span> system is now calibrated.
    - If only a non-low light sensitive meter is available (i.e., one incapable of reading lower than 0.1 lx, but has a resolution of +/- 1 lx or better):
        1. Instead measure the room illuminance with no filter applied. Then repeat the measurement but now with the light meter pointing directly at the LED light at a close distance.
        2. The ratio between the room illuminance and direct illuminance should remain relatively constant as more filters are applied.
        3. In this manner estimate how many layers of filter are required to reduce the illuminance to reach the target.
        4. Then follow the above concepts described for calibrating the system described for the low light sensitive meter.

```{figure} /images/10days.png
:name: 10days

<span style="font-variant:small-caps;">MoonShineP</span>'s performance in re-creating moonlight illuminance level after calibration. MoonShineR prediction = black line. Light meter measurements of the re-created illuminance = red line. Note that the lines are very close. This test was performed in a lab setting, running a simulated LED schedule for 9 nights around a full moon.
```

```{note}
If the user intends to heavily modify the color spectrum of the LED arrays, for example to recreate a color-shifted habitat, light meter measured illuminance (in lx) is not an appropriate unit. This is because photopic illuminance measurements assume a "natural light spectrum" (i.e. light similar to natural sunlight). In these cases the user should instead measure light level and calibrate <span style="font-variant:small-caps;">MoonShineP</span> in spectral irradiance (`W m⁻² nm⁻¹`, or `photons m⁻² s⁻¹ nm⁻¹`) using a spectrometer.
```

(content:lightbox:sun_calibration)=
## Sunlight and twilight re-creation

- To achieve sunlight level illuminance, dimming of the LEDs is not required.
- The user must first decide what level of sunlight illuminance they require. Direct overhead sunlight can be over 100,000 lx. It is probably impractical and unnecessary to re-create such high levels of illuminance.
- Nonetheless, we recommend the re-creation of sunlight at well over 200 lx, since 200 lx is approximately the illuminance when the sun is at the horizon. This means that if the light cannot achieve 200 lx, <span style="font-variant:small-caps;">MoonShineP</span> is not even re-creating the full range of twilight illuminance.
- The LED strip can be adhered to the ceiling, with the LEDs pointing downward, to illuminate the room like a fluorescent light fixture, or it could be adhered directly above the animal enclosure to provide a much stronger light intensity. As a reference, the illuminance measurement at 50cm from a warm white 144-LED SK6812 LED strip is around 1000 lx. 
- Although it is nearly impossible to re-create full intensity sunlight using <span style="font-variant:small-caps;">MoonShineP</span> (i.e., the illuminance level would shortly plateau after sunrise), the sunlight LED array still need to be calibrated to ensure correct illuminance levels before the plateau occurs (twilight and the short period after sunrise). To calibrate the sunlight LED array, follow these steps:
    1. Instruct the sunlight LED array to produce light at full intensity. See {ref}`content:calibration_schedule` to create a sunlight version of the calibration schedule (`LED_schedule_sun.csv`).
    2. Measure the illuminance on the level of the animal enclosure with a light meter. This is the calibration point for the sunlight/twilight LED array.
    3. Use this calibration point as the theoretical_max value in <span style="font-variant:small-caps;">MoonShineR</span>: Sunlight/twilight LED scheduler_.

### Using more LED strips
- Two LED strips may not be enough to re-create the desired illuminance, but more LED strips can be daisy chained together, as follows:
    - Connect additional LED strips by following {numref}`schematic`
    - Add LED strips **in multiple of two**.
    - See {ref}`content:lightbox:lednumber3` for how to update the number of LEDs in the software.