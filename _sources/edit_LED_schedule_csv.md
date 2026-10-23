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

(content:edit)=
# 4. Edit LED schedule `.csv`

- One of the most powerful features of <span style="font-variant:small-caps;">MoonShineP</span> is that the user can create any light scenario by editing the `LED_schedule_moon.csv` or `LED_schedule_sun.csv`.

- While <span style="font-variant:small-caps;">MoonShineR</span> is optimized for recreating realistic illuminance (by running the `LED_schedule_moon.csv` or `LED_schedule_sun.csv` directly from <span style="font-variant:small-caps;">MoonShineR</span>: Schedulers), we expect that many experiments utilizing <span style="font-variant:small-caps;">MoonShineP</span> will require a manipulated light regime (e.g., remove a full moon night from the lunar cycle) or a purposefully standardized regime (e.g., same sunrise/set times every day).


    ```{note}
    <span style="font-variant:small-caps;">MoonShineP</span> works by finding a matching date/time in the schedule `.csv` to the current Raspberry Pi system time. This makes re-creating the moonlight schedule at the current moment straightforward; the user simply needs to enter the current time in <span style="font-variant:small-caps;">MoonShineR</span>: Schedulers to generate such a schedule `.csv`. However, this means that if the user wishes to re-create a moonlight cycle in the past or future, the schedule `.csv` is required to have LED intensity values predicted for the desired time in the past of future, but a datetime that matches the current system time. Instructions for doing so are probided below, in creating a simulation for a past or future date.
    ```

## Creating customized schedules

- To create a customized schedule `.csv`, the user can either modify an existing <span style="font-variant:small-caps;">MoonShineR</span> generated schedule `.csv` using Excel (See Option 1, below). Alternatively, the user can create the customized schedule manually, using the provided Excel spreadsheet template (`manual_scheduler.xlsx`) (See Option 2, below). The user can also combine both approaches to a create a customized `LED_schedule .csv`, because the methods are better suited for different scenarios.

- <span style="font-variant:small-caps;">MoonShineP</span> can run any schedule `.csv` when the following conditions are met:
    - The .csv file contains the nine required columns, as described below (extra columns with other headers can be present, but will be ignored):
        - The first column is '_datetime_' in YYYY-MM-DD hh:mm:ss format (see {numref}`original_datetime`)(and the user must also make sure that the schedule starts before the intended launch time, see {ref}`content:launch`).
        - The next eight columns are the For the eight RGBW LED values, i.e., 'crude' and 'fine' settings for each of white, read green, and blue. These are labeled '_crudewhite_', '_wfine_', '_crudered_', '_rfine_', '_crudegreen_', '_gfine_', '_crudeblue_', '_bfine_' (see {numref}`moon_table`). The user must ensure that:
            - Crude intensity values ranges from 0-255 (LED intensity level).
            - Fine intensity values ranges from 0 to the total number of LEDs in the array.
        - The schedule `.csv` is named either `LED_schedule_moon.csv` or `LED_schedule_sun.csv`

### Option 1: Modify an existing `LED_schedule .csv`
- This method is suited for simple modifications such as setting a period of darkness,repeating certain part of the existing schedule, or re-creating a moonlight cycle in the past or future.
- To create a novel light regime, see Option 2.

#### Steps
1. First, generate a “blueprint” schedule `.csv` for a chosen location and time period, with 1 minute intervals.
2. Then use Excel to edit the '_datetime_' and/or the LED intensity values (crude and fine) in the schedule `.csv`.

Below are examples of how the schedule `.csv` can be edited:


##### Turning off light for certain period:
```{figure} /images/zero.png
:name: zero

A dark period can be simply inserted simply by replacing the LED values with zero.
```
    
##### Copy and paste the LED intensity:
```{figure} /images/copy.jpg
:name: copy

Copy and paste the LED intensity from one row to other rows at different times. For example, the user can copy an entire night of LED intensity progression during a full moon and paste it into another night.
```
    
##### To re-create a moonlight cycle in the past or future:

1. Generate a schedule `.csv` in <span style="font-variant:small-caps;">MoonShineR</span>: Schedulers with the desired settings and with a start time in the past or future.
2. The generated schedule `.csv` will have the correct LED intensity values but a date/time that can not be launched immediately as the date is not current.
3. Therefore, the user would replace the starting date/time of the re-created illumination schedule (from a past or future date) with the current date/time for the moonlight re-creation. To do so:
    - Delete all the original entries in the '_datetime_' column except for the first two rows.
    - Edit the entries in the first two rows with the new date/time, so that the first row is the desired start time and the second row is the first row's time + 1 minute.
    - Select the first two date/time, and use the Excel ‘fill handle’ to copy a new sequence of date/time values, at 1 min intervals, through until the last row of the simulation.
    - Make sure DST is not applied to any date-time values in the edited column.
    - See the next section to save the date/time in the correct format.

(content:datetime)=
#### Problem with Excel datetime
- When opening a newly created schedule `.csv` in R, or with a text editor (e.g. Microsoft Notepad/Notepad++ in Microsoft Windows or TextEdit in Mac), the '_datetime_' column is presented in the following correct format: 
    - YYYY-MM-DD hh:mm:ss

```{figure} /images/original_datetime.png
:name: original_datetime

The correct date/time format when opened with a text editor.
```

- However, when the same schedule `.csv` is opened in Excel, the date/time will automatically reformat to the Excel's default format (this default format may depends on the user's computer's country setting). If the user then saves the schedule `.csv`  in Excel, the date/time will be saved in an erroneous format that <span style="font-variant:small-caps;">MoonShineP</span> will not recognize.

```{figure} /images/excel_datetime.png
:name: excel_datetime

The wrong date/time format is displayed when schedule `.csv` is opened in Excel. The order of year, month, and day is incorrect, the number of digits is incorrect, and the seconds are missing.
```

- To remedy this problem, the date/time format needs to be specified every time the user opens and then saves schedule `.csv` in Excel. To do so, the user must follow the following steps.
    1. Right click at the '_datetime_' column header, and click **Format Cells...**:
        ![format_column.jpg](./images/format_column.jpg "format_column.jpg")
        <p>&nbsp;</p>
    
    2. Click **Custom**, and paste YYYY-MM-DD hh:mm:ss in **Type:**. Then click **OK**.
        ![format_cells.jpg](./images/format_cells.jpg "format_cells.jpg")
        <p>&nbsp;</p>
    
    3. Now the date/time is correct, and the schedule `.csv` is ready for saving.
        ![fixed_time.jpg](./images/fixed_time.jpg "fixed_time.jpg")
        <p>&nbsp;</p>
    
    4. This problem will reappear if the corrected schedule `.csv` file is then reopened in Excel. If the intention is to edit and re-save the `LED_schedule.csv` file in Excel, this date/time correction procedure must be repeated every time it is opened in Excel.

(content:excel_scheduler)=
### Option 2: Create LED schedule manually using `manual_scheduler.xlsx` 

- This method allows the assembly of a fully customized LED schedule. The `manual_scheduler.xlsx` file (download in {ref}`content:lightbox:download`) functions as a template to convert a list of desired illuminances into LED intensity values. Using this template, the user can re-create any novel illuminance schedule as desired.

#### Steps
```{figure} /images/manualexcel.jpg
:name: manualexcel

The layout of `manual_scheduler.xlsx`. '_datetime_' (red box), '_desired illuminance_' (orange box), RGBW LED intensity values (green), LED settings (blue), and preview plot (purple). In this example, the desired illuminance list is a linear function, increasing 0.5 lx every minute until it reaches 200 lx in 6 hours and 40 minutes.
```

```{figure} /images/manualexp.jpg
:name: manualexp

A second example of a manual schedule showing only the preview plot. The desired illuminance list consists of a natural exponent function at the start and end of the sequence, and a plateau of illuminance in the center. The LED intensity values are calculated automatically based on the desired illuminance list. <span style="font-variant:small-caps;">MoonShineP</span> will convert these values into accurate illumination in the room.
```

1. Set the LED settings in the upper right panel ({numref}`manualexcel`). The theoretical_max is the calibration illuminance, see {ref}`content:lightbox:calibration`.
2. Fill in the '_datetime_' column. This must be at 1 minute intervals.
3. Create the list of desired illuminances in lux. See ({numref}`manualexcel`) for an example.
4. The LED intensity values (crude and fine) will be updated automatically.
5. Save this `.xlsx` file first because this will preserve the formatting (graph and equations).
6. Then save this file as a separate `.csv` file, using "save as".
7. Rename this newly created `.csv` file to `LED_schedule_moon.csv` or `LED_schedule_sun.csv` depending on what the user is re-creating in <span style="font-variant:small-caps;">MoonShineP</span>.
8. Open this `.csv` file in Excel to correct the '_datetime_' into the correct format for <span style="font-variant:small-caps;">MoonShineP</span>, as described above.
9. Remove the unnecessary columns (i.e., column L and beyond).
10. Save changes.
11. The user can run this finalized `.csv` with <span style="font-variant:small-caps;">MoonShineP</span>, or append it to an existing schedule `.csv` by matching the corresponding columns.

