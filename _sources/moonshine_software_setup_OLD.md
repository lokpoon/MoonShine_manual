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
(content:lightbox:lednumber)=
## Setting up a new Raspberry Pi

1. On a computer go to the Raspberry Pi website https://www.raspberrypi.com/software/ to download and install the Raspberry Pi Imager.

2. Insert the micro SD card into the comptuer.

3. Select your Pi model. MoonShineP has been tested and working on Pi model 4 and 5.

4. Select the "Raspberry Pi OS (Legacy, 32-bit)" for installation.

```{figure} /images/os.jpg
:name: schematic

Raspberry Pi OS (Legacy, 32-bit)
```
5. You must enter "pi" as the username, so that all the filepaths are correct. You can choose your own password.

```{figure} /images/username.jpg
:name: schematic

Put "pi" as username.
```

6. Ignore all other configurations by pressing Next.

8. One the initialization of the SD card is complete, put it in the Pi. 

9. Connect power, monitor, mouse, and keyboard to the Raspberry Pi and boot it up.

11. Install Python 3.9.9.

    ```{attention}
    MoonShineP DOES NOT SUPPORT OTHER VERSIONS OF PYTHON
    ```
   
   Reference: [How to Update Python on Raspberry Pi](https://github.com/Crampton-Lab/MoonShine/blob/main/external_website_guide_backup/update_python_webpage.pdf)
   
   Enter the following sequence of commands in the terminal:
   
    ```
    wget https://www.python.org/ftp/python/3.9.9/Python-3.9.9.tgz
    ```
    ```{attention}
    If you recieve `The certificate of 'www\.python.org' is not trusted` after running the above line, resolve it by running:
    `sudo apt update`
    `sudo apt install --reinstall ca-certificates`
    `sudo update-ca-certificates`
    ```
    ```
    tar -zxvf Python-3.9.9.tgz
    ```

    ```
    cd Python-3.9.9
    ```

    ```
    ./configure --enable-optimizations
    ```

    ```
    sudo make altinstall
    ```

    ```
    cd /usr/bin
    ```

    ```
    sudo rm python
    ```

    ```
    sudo ln -s /usr/local/bin/python3.9 python
    ```

11. Check Python version is version (3.9.9) by entering:

   
   ```
   python --version
   ```

   ```{attention}
   It must be Python 3.9.9
   ```

11. Reboot the Raspberry Pi.
12. Modify the Raspberry Pi to run the ws2811 library. 
   
   Reference: [Connect and Control WS2812 RGB LED Strips via Raspberry Pi](https://github.com/Crampton-Lab/MoonShine/blob/main/external_website_guide_backup/LED_strip_setup.pdf)
   
   - In terminal, enter the following sequence of commands:
   
        ```
        sudo apt-get update
        ```
   
        ```
        sudo apt-get install gcc make build-essential python3-dev git scons swig
        ```
   - Confirm with Y
   
   - This following command line will open a file editor. To deactivate audio output, edit the `.conf` file by entering the following command:
     
     ```
     sudo nano /etc/modprobe.d/snd-blacklist.conf
     ```
     
   - A new terminal window will open. In this window add the following line text:
     
     ```
     blacklist snd_bcm2835
     ```
     
   - Then save file with CTRL + O.
     
   - And close the editor with CTRL + X.
   
   - We also need to edit another file. To do so, enter:
     
     ```
     sudo nano /boot/firmware/config.txt
     ```
     
   - When the editor is opened, comment out the following line with a # at the beginning:
     
     ```
     dtparam=audio=on
     ```
     
     i.e.,
     
     ```
     #dtparam=audio=on
     ```
     
     - Then save file with CTRL + O.
     
     - And close the editor with CTRL + X.
   
   - Reboot the Raspberry Pi by entering:
     
     ```
     sudo reboot
     ```

1. Download rpi_ws281x library by entering:
    ```
    cd
    ```
    
    ```
    git clone https://github.com/jgarff/rpi_ws281x
    ```

    
2. Next, compile the library for Python.
      
      In terminal, enter the following commands:
      
    ```
    cd
    ```

    ```
    cd rpi_ws281x/
    ```

    ```
    sudo apt install python3-pip
    ```
    ```
    sudo /usr/bin/python3 -m pip install --break-system-packages rpi-ws281x
    ```
   

14. Now the Raspberry Pi should be ready to control the SK6812 LED strips.

## Setup real time clock (RTC) and time

A real-time clock (RTC) module is recommended for MoonShineP, especially when operating without internet access. The RTC maintains time during power outages. We recommend disabling automatic internet time synchronization and using a fixed timezone without daylight saving time (DST) to ensure that the Raspberry Pi's clock remains consistent with the MoonShineR-generated schedules.

1. Install RTC module DS3231 as described in {ref}`content:hardware:assemble`.

2. On the task bar of the main Linux window of Raspberry Pi, go to the Start menu (top left button) and enable the RTC module by selecting the following options: > Preferences > Raspberry Pi Configuration > Interfaces > I2C **Enable** > OK

3. Open the Raspberry Pi configuration file, in terminal enter:
    ```
    sudo nano /boot/firmware/config.txt
    ```
4. Add the following lines under `[all]`, and save the file by pressing `Ctrl+O`, Enter, then `Ctrl+X`:
    ```
    dtparam=rtc=off #This line is only required for Pi 5. Older models do not need this line.
    dtoverlay=i2c-rtc,ds3231
    ```
    
5. Reboot Pi:
    ```
    sudo reboot
    ```

6. Disables automatic network time synchronization when connected to the internet:

    ```
    sudo timedatectl set-ntp false
    ```
7. Set a fixed EST timezone without DST:
    ```
    sudo timedatectl set-timezone Etc/GMT+5
    ```
8.  Set the Raspberry Pi's system clock to the current local standard time (without daylight saving time, even if DST is currently in effect) by entering the following command in the terminal:
    ```
    sudo date -s 'YYYY-MM-DD hh:mm:ss'
    ```
    - Change the above to the user's current time, for example '2022-01-01 01:01:00'.

9. Copy the time from the Raspberry Pi system to the Hardware RTC by entering:
    ```
    sudo hwclock -w
    ```
10. To check if RTC is working, enter:
    ```
    sudo hwclock -rv
    timedatectl
    cat /sys/class/rtc/rtc0/name
    ```
    - The RTC time will be reported.

11. Test whether the RTC maintains time after a power outage:

    - Disconnect the Raspberry Pi from the internet and shut it down.
    - Once the Raspberry Pi has shut down, disconnect its power supply for at least 5 minutes.
    - Reconnect the power supply and allow the Raspberry Pi to boot up, without connecting to the internet.
    - Check the system time and RTC time by entering:

    ```
    timedatectl
    sudo hwclock -rvs
    ```

(content:lightbox:lednumber2)=    
## Desktop folders

1. Download the _control_moon_ and _control_sun_ folders (download in {ref}`content:lightbox:download`).
    - _control_moon_ contains `moonshine_moon.py`
    - _control_sun_ contains `moonshine_sun.py`

2. Move the two folders to the Raspberry Pi Desktop.

    ```{note}
    In  `moonshine_moon.py`, the line of LED_PIN = 18 specifies the communication with the moonlight LED strip through the **GPIO 18**. In `moonshine_sun.py`, the line of LED_PIN = 21 controls the sunlight/twilight LED strip through **GPIO 21** instead.
    ```

1. In additional, download `clear_moon.py` and `clear_sun.py` put them directly on the Raspberry Pi Desktop.
(content:systemd)=   
## Setting up systemd service
Running <span style="font-variant:small-caps;">MoonShineP</span> using the Linux systemd service manager allows it to recover and resume light re-creation automatically, in the unlikely event of a crash.

1. Create a new service file.
   
   In terminal, enter:
   
    ```
    sudo nano /etc/systemd/system/moonshine_moon.service
    ```


2. The file editor will open up a blank file, paste in the following lines:
   
   ```
   [Unit]
   Description=relaunch moonshine_moon.py when crashed
   
   [Service]
   User=root
   Group=root
   Type=simple
   ExecStart=/usr/bin/python3 /home/pi/Desktop/control_moon/moonshine_moon.py
   Restart=always
   RestartSec=3
   
   [Install]
   WantedBy=default.target
   ```
    ```{note}
    The line of "ExecStart=..." instructs system service to locate the <span style="font-variant:small-caps;">MoonShineP</span> python script in the correct directory. Restart=always and RestartSec=3 are configured to restart `moonshine_moon.py` within 3 seconds upon closing (i.e., the script crashing).
    ```

3. Then save file with CTRL + O. CTRL + X to close the editor.
   
   When prompted for the file name, confirm that it is `moonshine_moon.service` and save.

4. Refresh the system service files. It may ask for the username and password (if so enter the username and password). Enter in terminal:
   
   ```
   sudo systemctl daemon-reload
   ```

5. (Optional) If the user is recreating sunlight and twilight as well, repeat the above steps, from 1 to 4, to make another service for _control_sun/moonshine_sun.py_
    
    - For step 1, use a different file name:
    
        ```
       sudo nano /etc/systemd/system/moonshine_sun.service
        ```
        
    - For step 2, replace the line of `ExecStart=...`  with 
        ```
        ExecStart=sudo /usr/bin/python3 /home/pi/Desktop/control_sun/moonshine_sun.py
        ```

    - For step 3, save the file with name `moonshine_sun.service`
    
       ```{note}
       Every time a `.service` file is edited, it requires a refresh (see Step 4).
       ```
(content:lightbox:lednumber3)=    
## Setting LED numbers in `moonshine_moon.py` and `moonshine_sun.py`

- Recall that in {ref}`content:MoonShineR_moon` and {ref}`content:MoonShineR_sun`, the user was required to enter the specifications of the LED array (diode_per_strip and strip_count) into the R program. This was essential for generating the schedule `.csv`. It is important that LED_COUNT (below) in <span style="font-variant:small-caps;">MoonShineP</span> python script represents the product of diode_per_strip (from <span style="font-variant:small-caps;">MoonShineR</span>) and strip_count (from <span style="font-variant:small-caps;">MoonShineR</span>).

1. To calculate the total number of LEDs, diode_per_strip should be multiplied by strip_count. E.g., 144 x 4 = 576.


2. Edit the line of "LED_COUNT" in `moonshine_moon.py` and `moonshine_sun.py` with the respective total number of LEDs for each array (see {numref}`led_count`).
    ```{figure} /images/led_count.png
    :name: led_count

    Specify the line of "LED_COUNT" in `moonshine_sun.py` to 576 when using four daisy-chained 144 LED strips.
    ```
    
3. Save the file.

    ```{note}
    When changing the number of LEDs number in an array, change the corresponding settings in <span style="font-variant:small-caps;">MoonShineR</span> schedulers and the <span style="font-variant:small-caps;">MoonShineP</span> python file. This will, of course, also require a recalibration of the illuminance.
    ```