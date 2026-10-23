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

(content:lightbox:download)=
# Download files

## <span style="font-variant:small-caps;">MoonShineR</span> R package

The _<span style="font-variant:small-caps;">MoonShineR</span>_ R package, used to predict ground illuminance from moonlight, twilight, and sunlight, is available through [CRAN](https://cran.r-project.org/package=MoonShineR) and should be installed directly from R:

```
install.packages("MoonShineR")
```
For documentation and usage examples, see the [MoonShineR CRAN page](https://cran.r-project.org/package=MoonShineR) or Chapter 1 of this manual.

The source code for the CRAN package is maintained separately in the [MoonShineR_package GitHub repository](https://github.com/Crampton-Lab/MoonShineR_package).

## <span style="font-variant:small-caps;">MoonShineR</span> scheduler scripts and <span style="font-variant:small-caps;">MoonShineP</span>

The two stand-alone <span style="font-variant:small-caps;">MoonShineR</span> scheduler scripts, <span style="font-variant:small-caps;">MoonShineP</span> Python programs, and supporting files required for experimental light re-creation are available from the [main MoonShine GitHub repository](https://github.com/Crampton-Lab/MoonShine).

These include:

**MoonShineR scheduler scripts:**

- `moonshineR_moonlight_scheduler_(ver2025oct).R`
- `moonshineR_sunlight_twilight_scheduler_(ver2025oct).R`

**MoonShineP Python programs:**

- `control_moon` folder, containing `moonshine_moon.py`
- `control_sun` folder, containing `moonshine_sun.py`
- `clear_moon.py`
- `clear_sun.py`

**Supporting Excel files:**

- `RGBW_LED_spectrum.xlsx`
- `manual_scheduler.xlsx`

To download an individual file from GitHub, open the file and select the download option. Alternatively, the entire repository can be downloaded using **Code → Download ZIP**.

Detailed instructions for using these files are provided in Chapters 2–8 of this manual.