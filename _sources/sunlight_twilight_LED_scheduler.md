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
(content:MoonShineR_sun)=
# 3. <span style="font-variant:small-caps;">MoonShineR</span>: Sunlight/twilight scheduler

<span style="font-variant:small-caps;">MoonShineR</span>: Sunlight/twilight scheduler is a stand-alone R script designed for use with <span style="font-variant:small-caps;">MoonShineP</span> to re-create sunlight and twilight cycles in laboratory environments.

The scheduler uses the same underlying sunlight and twilight illuminance calculations as the <span style="font-variant:small-caps;">MoonShineR</span> package available on CRAN, but provides additional functionality for generating LED control schedules. Its output file, `LED_schedule_sun.csv`, contains predicted illuminance values converted into RGBW LED intensity settings at one-minute intervals. <span style="font-variant:small-caps;">MoonShineP</span> reads this file to control the sunlight/twilight LED array.

**The Sunlight/twilight scheduler is not included in the CRAN package.** It must be downloaded separately from the [main MoonShine GitHub repository](https://github.com/Crampton-Lab/MoonShine). (The latest scheduler script version is `moonshineR_sunlight_twilight_scheduler_(ver2025oct).R`)

Users should read {ref}`content:MoonShineR_moon` before proceeding, as many of the operations are similar. This chapter focuses on the settings and procedures specific to sunlight and twilight re-creation.

## Key features

- Recreates full daylight and twilight regimes in a laboratory or other indoor environment.
- Allows user to specify a geographical location and time period so that the re-created twilight and sunlight reflects real daily and seasonal changes.
- Provides option to re-create spectral change according to the sun altitude. The LED will be more red-shifted near sunrise/set, and progressively more blue-shifted during twilight.
- Provides option to adjust the LED light spectrum by controlling the relative intensity of each RGBW channel. This can be useful in approximating the color shift of certain habitats (e.g., the blue shift in deep clear ocean or lake water, or the red shift of sodium vapor street lighting).
- Re-creates realistic absolute twilight illuminances but recreates sunlight absolute illuminances up to a plateau, with the upper level dependent on the LED array size. 

##  Workflow

```{attention}
MoonShineR: Sunlight/twilight scheduler misses some features found in the moonlight scheduler, including elevation effect, horizontal obstruction, and cloud simulation. For sunlight, we believe that applying these features to sunlight is not useful since we cannot foresee the user recreating full sunlight intensity. Therefore, applying these features would not make the re-created lighting more realistic. For twilight, while these features might be applicable to an extent, they are not implemented because twilight is a diffuse light source, unlike direct moonlight, and hence behaves very differently (Seidelmann 1992).
```
    
1. Refer to {ref}`content:MoonShineR_moon` to load packages and then perform steps (1-7) as described to set the user-definable settings (location, time period, etc.) The time_interval_minutes is constrained to a value of one and cannot be changed. The following instructions will cover the settings and functions specific to <span style="font-variant:small-caps;">MoonShineR</span>: Sunlight & twilight scheduler.

    
2. Set the calibration illuminance point. See {ref}`content:lightbox:sun_calibration`.
   
   ```
   theoretical_max <- 600 # (!) Define an intensity upper limit (in lux)
   ```
   
    ```{note}
    Even when using numerous daisy-chained LED strips , the re-created sunlight will reach a plateau of maximum illuminance below the intensity of typical natural sunlight not long after/before dawn/dusk. A user could supplement our sunlight and twilight recreation with a powerful commercial sunlight LED array system that starts ramping soon after sunrise and soon before sunset. LED systems with adequate programming of dawn and dusk fading are sold for the poultry and aquaculture industries. 
    ```

3. Specify the number of LEDs per SK6812 LED strip and the number of daisy-chained strips.

   ```
    diode_per_strip <- 144 # (!) Number of LEDs per strip
    strip_count <- 4 # (!) Number of daisy-chained LED strips
   ```



   
4. Specify whether to re-create the spectral change based on the sun altitude, a feature termed 'realistic sunlight'. By enabling this feature, as the sun is low in altitude (around sunrise and sunset), the light will be more red shifted. Shortly after sunset (or before sunrise), the light spectra will quickly become blue shifted and continue this trend progressively as the sun altitude decreases. If realistic sunlight is enabled, it will override and ignore the settings in the next step (step 5) for applying a constant spectral shift.
   
   ```
   realistic_sunlight <- TRUE # (!)
   ```

    ```{note}
    By turning realistic sunlight on, a fraction value (0-1) for each RGBW channel intensity is applied based on the sun altitude at the time. We formulated a function of sun altitude ~ LED intensity for each RGBW channel. These functions are formulated based on our own measured ground spectral irradiance of several sunsets, and Palmer & Johnsen's (2015) twilight spectra. The code that plots these four functions, for visualization purposes, is commented out by default. If the user would like to visualize how the RGBW channels are adjusted based on the sun altitude, run those codes that are near the beginning of the section "GENERATE twilight_sun_output.csv"; remove the commenting prompts (# symbols) at the beginning of the line to activate the commented-out lines of code.
    ```
5. If realistic sunlight is disabled, determine the LED color spectrum by specifying the intensity output (0-1) of each RGBW channel, a feature termed 'spectral control' (i.e., same feature found in <span style="font-variant:small-caps;">MoonShineR</span>: Moonlight scheduler). To approximate a natural sunlight spectrum (same setting as moonlight spectrum because the sunlight and moonlight spectra are very similar [Johnsen 2012]), leave this setting at its default value. Note that these default values are intended for the warm white SK6812 LED strips made by BTF lightning. (see {ref}`content:hardware:materials`).

   ```
    white_fraction <- 1.0 # (!) White. Default = 1.0
    red_fraction <- 0.08 # (!) Red. Default = 0.08
    green_fraction <- 0.36 # (!) Green. Default = 0.36
    blue_fraction <- 0.19 # (!) Blue. Default = 0.19
   ```

   Go to {ref}`content:rgbw` for details on how to adjust the RGBW intensity output for approximating the spectral-shift characteristics of certain habitats.

    
6. Run the code in the section “START OF ILLUMINATION COMPUTATION” through to “END OF ILLUMINATION COMPUTATION”. 
7. Generate the sunlight/twilight schedule file LED\_schedule\_sun.csv by running the code in the section headed GENERATE LED\_schedule\_sun.csv. Then run the script's plotting section to display the predicted illuminance over time.