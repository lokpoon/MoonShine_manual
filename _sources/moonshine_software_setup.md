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

(content:lightbox:software)=
# 6. <span style="font-variant:small-caps;">MoonShineP</span>: Software setup

```{note}
**Compatibility Note: As of November 2026.** MoonShineP was tested successfully on **Raspberry Pi 4** running **Python 3.11.2** (pre-installed version of Python with the OS), and relying on the system-installed `rpi-ws281x` Python package latest version 5.0.0. This chapter uses **Raspberry Pi OS (Legacy, 32-bit)** as the installation target; at the time of the test the Raspberry Pi OS Legacy release is **Debian 12 (Bookworm)**.

**Hardware Note:** MoonShineP works best on **Raspberry Pi 4 Model B**, and should also work on the previous generation Pi 3 B. Raspberry Pi 5 uses a different GPIO architecture and as of November 2026, is not fully supported by the required `rpi-ws281x` Python package. The Raspberry Pi 4 Model B is expected to remain in production until at least 2034.
```

(content:lightbox:lednumber)=
## Setting up a new Raspberry Pi

1. On a computer, download and install [Raspberry Pi Imager](https://www.raspberrypi.com/software/).

2. Insert the microSD card into the computer for loading the OS.

3. Select **Raspberry Pi 4** (or Pi 3 if that is what you are using) as the model.

4. Select **Raspberry Pi OS (Legacy, 32-bit)**. As of November 2026, this is based on **Debian 12 (Bookworm)**.

   ```{figure} /images/os.jpg
   :name: schematic

   Select Raspberry Pi OS (Legacy, 32-bit).
   ```

5. It is very important to set the username to `pi` in the Imager's **OS Customisation** / **User** settings. The user then chooses their own password. The supplied MoonShineP Python scripts and systemd examples contain absolute paths beginning with `/home/pi/`. Using another username is possible, but then **all** `/home/pi/` paths in the Python scripts and service files must be updated.
   ```{figure} /images/username.jpg
   :name: moonshine_username_choice

   Enter `pi` as the username to match the paths in the supplied scripts.
   ```

6. Skip or complete any other required settings, then write the OS image to the microSD card. If you skip customisation, create the username `pi` when prompted at first boot; the OS does not automatically use `pi` as its default username.

7. When writing is complete, insert the microSD card into the Raspberry Pi. Connect power, monitor, mouse, and keyboard, then boot the Pi.

8. **Check the installed OS and Python versions.** Python is already included in Raspberry Pi OS. At the time of November 2026, Raspberry Pi OS (Legacy, 32-bit) Python 3.11.2, which works properly with MoonShineP
   ```bash
   cat /etc/os-release
   dpkg --print-architecture
   /usr/bin/python3 --version
   ```

9. **Install the Python library that controls the SK6812 LEDs.** Connect the Pi to the internet for this step, then enter:

   ```bash
   sudo apt update
   sudo apt install python3-pip python3-dev build-essential swig
   sudo /usr/bin/python3 -m pip install --break-system-packages 'rpi-ws281x==5.0.0'
   ```
   Enter Y when prompted for the installation.
   ```{attention}
   On Raspberry Pi OS Bookworm, `pip` normally prevents installations into the system Python environment. The `--break-system-packages` option deliberately overrides that protection. It is included here to allow a working **system-Python** setup on the dedicated MoonShineP Pi, and it carries a risk of conflicts with OS-managed Python packages. For general-purpose Raspberry Pis, a Python virtual environment is preferable; its interpreter path would also need to be used in the systemd service.
   ```

   Check that Python can import the `rpi_ws281x` module, and record its actual version:

   ```bash
   sudo /usr/bin/python3 -c "import rpi_ws281x; print('Success')"
   /usr/bin/python3 -m pip show rpi-ws281x
   ```

   The first command should print `Success`; the second should show `Version: 5.0.0` for the installation. `Version: 5.0.0` of rpi_ws281x has been tested to work, so it is best to keep the version specification instead of installing the latest update.

10. The Pi now has the required Python LED-control library installed. Continue with the RTC, folder, LED-count, and systemd configuration below before running MoonShineP.

## Setup real time clock (RTC) and time

A real-time clock (RTC) is recommended for MoonShineP when operating without internet access. On a **Raspberry Pi 4**, an external **DS3231** module with a working backup battery can maintain time during power outages. Disable automatic internet time synchronisation if the experimental schedule must not be adjusted by network time updates, or run the system offline (recommended).

1. Connect the DS3231 RTC module as described in {ref}`content:hardware:assemble`.

2. Enable I2C using the Raspberry Pi configuration utility:

   ```bash
   sudo raspi-config
   ```

   Select **Interface Options > I2C > Enable**, then exit. (The menu numbering can differ among OS versions.)

3. Open the boot configuration:

   ```bash
   sudo nano /boot/firmware/config.txt
   ```

   Add two new lines at the top:

   ```ini
   dtoverlay=i2c-rtc,ds3231
   ```
   ```ini
   dtparam=audio=off
   ```

   If the line `dtparam=audio=on` alreay exists within the config.txt, delete it.

   Save with `Ctrl+O`, Enter, and exit with `Ctrl+X`.

3. Reboot:

   ```bash
   sudo reboot
   ```

4. **Verify that the RTC was detected.** Enter:

   ```bash
   ls /dev/rtc*
   for f in /sys/class/rtc/rtc*/name; do echo "$f: $(cat "$f")"; done
   ```

   Identify the RTC device associated with the DS3231; on a Pi 4 with one RTC, it is typically `/dev/rtc0`. If more than one RTC exists, specify the correct one in `hwclock` commands with `--rtc /dev/rtcN`. If the module is not detected, check wiring, I2C enablement, and the device-tree overlay before setting time.

5. Disable automatic network time synchronisation:

   ```bash
   sudo timedatectl set-ntp false
   ```

6. Set a fixed time zone without daylight saving time. For example, for **EST (UTC-05:00) all year**. It does not have to be the actual geographical time zone you are located at, because we will change the clock time manually in the next step, and the .csv schedule file simply provides a time without compensation for time zone.

   ```bash
   sudo timedatectl set-timezone Etc/GMT+5
   ```

7. Set the current date and time without applying a daylight saving adjustment. Enter:

   ```bash
   sudo date -s 'YYYY-MM-DD HH:MM:SS'
   ```
   
   Replace the placeholder with the current date and standard-zone time, for example `2026-11-05 17:00:00` if that is the correct time in your selected zone.

8. Copy the system time to the RTC (stored in UTC):

   ```bash
   sudo hwclock --systohc --utc
   ```

9. Check RTC device:

    ```
    cat /sys/class/rtc/rtc0/name && sudo hwclock --show --rtc /dev/rtc0
    ```

    * Hardware connection: `rtc-ds1307 1-0068` confirms Linux detects the DS3231.

    * RTC operation: A successfully displayed date and time confirms the RTC can be read.


10. **Test timekeeping after a power outage.** Disconnect the Pi from the network, then shut it down:

    ```bash
    sudo shutdown -h now
    ```

    After shutdown completes, disconnect the power supply for a few minutes. Reconnect power and boot **without internet access**. Check:

    ```bash
    timedatectl
    sudo hwclock --show
    ```

    Confirm that the system clock has advanced properly.

(content:lightbox:lednumber2)=
## Desktop folders

1. Download the _control_moon_ and _control_sun_ folders from {ref}`content:lightbox:download`.

   - _control_moon_ contains `moonshine_moon.py`.
   - _control_sun_ contains `moonshine_sun.py`.

2. Move the folders to the Raspberry Pi Desktop so the script paths are:

   ```text
   /home/pi/Desktop/control_moon/moonshine_moon.py
   /home/pi/Desktop/control_sun/moonshine_sun.py
   ```

   Place each generated LED schedule CSV in the corresponding control folder, using the filename expected by its Python script. For example, `moonshine_moon.py` reads:

   ```text
   /home/pi/Desktop/control_moon/LED_schedule_moon.csv
   ```

   ```{note}
   In `moonshine_moon.py`, `LED_PIN = 18` sends the moonlight LED data through **GPIO 18**. In `moonshine_sun.py`, `LED_PIN = 21` is used for the sunlight/twilight LEDs through **GPIO 21**. The two scripts control separate LED outputs.
   ```

3. Download `clear_moon.py` and `clear_sun.py` from {ref}`content:lightbox:download` and place them directly on the Raspberry Pi Desktop.

(content:lightbox:lednumber3)=
## Setting LED numbers in `moonshine_moon.py` and `moonshine_sun.py`

In {ref}`content:MoonShineR_moon` and {ref}`content:MoonShineR_sun`, the user specifies the LED array using `diode_per_strip` and `strip_count` when generating the schedule CSV files. The corresponding `LED_COUNT` in the MoonShineP Python script must equal the product of these values. The Python script treats the LEDs as a one-dimensional sequence.

1. Calculate the LED count:

   ```text
   LED_COUNT = diode_per_strip * strip_count
   ```

   For example, 144 LEDs per strip multiplied by four daisy-chained strips gives **576 LEDs**.

2. Open `moonshine_moon.py` and `moonshine_sun.py`, and set `LED_COUNT` to the actual total for each independent LED array (see {numref}`led_count`).

   ```{figure} /images/led_count.png
   :name: led_count

   Set `LED_COUNT = 576` in `moonshine_sun.py` when using four daisy-chained strips of 144 LEDs each.
   ```

3. Check the LED channel order in **both Python scripts**. For the GRBW SK6812 strips used in this MoonShineP setup, the setting is:

   ```python
   LED_STRIP = ws.SK6812_STRIP_GRBW
   ```

   `GRBW` is correct for the LED strip model we recommend. The `GRBW` order differs from the conventional `RGBW`, and properly determines how the red, green, blue, and white data are assigned to the physical LED channels.

4. Save the Python scripts. Changing LED count or strip hardware also requires matching changes to the MoonShineR schedule generation and a new illuminance calibration.

(content:systemd)=
## Setting up systemd service

Using the Linux systemd service manager lets MoonShineP launch automatically at boot and restart if its continuously running Python script exits. Complete the folder and LED settings above first.

1. Create the moonlight service file:

   ```bash
   sudo nano /etc/systemd/system/moonshine_moon.service
   ```

2. Paste the **complete** contents below. Pay particular attention to the `ExecStart` line: it must contain both the Python interpreter **and** the path to `moonshine_moon.py`.

   ```ini
   [Unit]
   Description=MoonShineP moonlight scheduler

   [Service]
   User=root
   Group=root
   Type=simple
   ExecStart=/usr/bin/python3 /home/pi/Desktop/control_moon/moonshine_moon.py
   Restart=always
   RestartSec=3

   [Install]
   WantedBy=multi-user.target
   ```

   `Restart=always` restarts the script after an unexpected failure **or a normal exit**. `RestartSec=3` pauses for three seconds before restarting. The service is run as root because the LED-control library requires privileged hardware access on this configuration.

3. Save with `Ctrl+O`, Enter, and close with `Ctrl+X`.

4. If sunlight/twilight simulation is also used, create a second service:

   ```bash
   sudo nano /etc/systemd/system/moonshine_sun.service
   ```

   Paste:

   ```ini
   [Unit]
   Description=MoonShineP sunlight and twilight scheduler

   [Service]
   User=root
   Group=root
   Type=simple
   ExecStart=/usr/bin/python3 /home/pi/Desktop/control_sun/moonshine_sun.py
   Restart=always
   RestartSec=3

   [Install]
   WantedBy=multi-user.target
   ```


5. Reload the systemd unit definitions:

   ```bash
   sudo systemctl daemon-reload
   ```

   Perform this reload Whenever a `.service` file is created or edited.

   
6. Enable and start the moonshine_moon.service:

   ```bash
   sudo systemctl enable --now moonshine_moon.service
   ```

   If configured, do the same for sunlight/twilight:

   ```bash
   sudo systemctl enable --now moonshine_sun.service
   ```

   `enable` configures automatic startup at boot; `--now` also starts the service immediately. If you want to start a service just for the current session without enabling it at boot, use `sudo systemctl start moonshine_moon.service` instead.

7. Check the service state:

   ```bash
   sudo systemctl status moonshine_moon.service --no-pager -l
   ```

   A continuously running scheduler should normally show **`active (running)`**. Note also that a running scheduler does not guarantee the LEDs will be illuminated at that moment; the schedule may intentionally specify zero output.

9. If the service is `failed`, `inactive`, or repeatedly `activating (auto-restart)`, inspect its logs:

   ```bash
   sudo journalctl -u moonshine_moon.service -n 50 --no-pager
   ```

   Typical checks are:

   - **`ModuleNotFoundError: No module named 'rpi_ws281x'`:** Recheck the Python library installation using the same `/usr/bin/python3` interpreter as `ExecStart`.
   - **`can't open file ...` or a clean exit immediately after launch:** Check the full `ExecStart` line and the actual script path.
   - **`No such file or directory` for the schedule or log:** Check the control folder, CSV filename, and `/home/pi/` paths inside the Python script.
   - **`active (running)` but no light:** Check the present schedule values, `LED_COUNT`, `LED_PIN`, `LED_STRIP`, power, and connections.

10. For more information on starting and stopping `moonshine_moon.service`, see {ref}`content:launch`.
