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

(content:launch)=
# 8. <span style="font-variant:small-caps;">MoonShineP</span>: Launch procedure

This chapter explains how to launch <span style="font-variant:small-caps;">MoonShineP</span>, using the moonlight system as an example. It uses `moonshine_moon.py` and `LED_schedule_moon.csv` in `/home/pi/Desktop/control_moon/`, together with the systemd service `moonshine_moon.service`. The same procedures apply to the sunlight/twilight system, using `moonshine_sun.py` and `LED_schedule_sun.csv` in `/home/pi/Desktop/control_sun/`, and `moonshine_sun.service`.

Complete the hardware and software setup chapters first. In particular, follow {ref}`content:systemd` to create the service files and install the `rpi-ws281x` Python package.

## Requirements

Before launching MoonShineP, check the following.

### 1. Generate a working `LED_schedule_moon.csv`

* Generate `LED_schedule_moon.csv` using {ref}`content:MoonShineR_moon` and, if necessary, edit it as described in {ref}`content:edit`.

* Place `LED_schedule_moon.csv` in `/home/pi/Desktop/control_moon/`.

* Ensure the schedule contains a row for **every minute throughout the intended simulation, starting no later than when `moonshine_moon.service` is launched**. The Python script searches for a matching `datetime` value at the start of every minute. If no matching row is found, the script will fail and stop.

* **To schedule the LEDs to turn on at a later time:** If you want to launch `moonshine_moon.service` immediately but keep the LEDs off until a later time, you must manually pad the schedule with rows containing zero LED values. For example, launching `moonshine_moon.service` at `2026-08-01 13:00:00` when the first row of the schedule is `2026-08-01 13:01:00` will cause the script to fail because it cannot find a matching row at launch. Adding a row for `2026-08-01 13:00:00` will resolve this issue. To keep the LEDs off between launch and the intended start of illumination, set **all red, green, blue, and white crude and fine LED values** to zero in every row during this period. The scheduler will continue running with the LEDs off until it reaches rows containing nonzero LED values.

* **Do not delete rows to create dark periods.** Instead, set their LED values to zero to maintain uninterrupted one-minute intervals.

* Check the format of the `datetime` column, as explained in {ref}`content:datetime`. Timestamps must be at one-minute intervals (e.g., `2026-11-05 17:30:00`, `2026-11-05 17:31:00`, `2026-11-05 17:32:00`). Verify that the datetime format is preserved in the final `.csv` schedule file.



### 2. Check the location of files

Within `/home/pi/Desktop/control_moon/`, confirm that both files are present:

- `moonshine_moon.py`
- `LED_schedule_moon.csv`



### 3. Check the LED strips and connections

Confirm that the LED power supply, ground, and data connections are secure. The LEDs may retain their last brightness levels after the service stops; **this does not mean the service is still running or that the strips are frozen**.

If an LED strip becomes unresponsive, stop the service and shut down the Raspberry Pi before checking the wiring. Disconnect the LED power supply before unplugging or reconnecting the GPIO/data cable. Do not disconnect signal wires while the system is powered.

## To run `moonshine_moon.service`

1. **Enable MoonShineP to start automatically when the Raspberry Pi boots**, by entering:

   ```bash
   sudo systemctl enable moonshine_moon.service
   ```

   This enables automatic startup at boot. **It does not start the service immediately.**

2. **Start MoonShineP immediately**, without rebooting the Pi:

   ```bash
   sudo systemctl start moonshine_moon.service
   ```

   Alternatively, to enable automatic startup **and** start the service immediately with one command, use:

   ```bash
   sudo systemctl enable --now moonshine_moon.service
   ```

   ```{note}
   The service starts immediately, but the Python scheduler updates the LEDs when it next finds a matching schedule row at the start of a minute (`:00` seconds). If the schedule specifies zero brightness, the LEDs will remain off even though the service is running.
   ```

3. **Check whether the service is running**, by entering:

   ```bash
   sudo systemctl status moonshine_moon.service --no-pager -l
   ```

   A continuously running MoonShineP service should normally show **`active (running)`**. If it shows `failed`, `inactive (dead)`, or `activating (auto-restart)`, follow the troubleshooting instructions below. Checking the service directly is more informative than using `systemctl list-units --type=service --state=active`, which omits inactive and failed services.

4. **Check the LED brightness log.** After the next whole minute, a `log.txt` file in `/home/pi/Desktop/control_moon/` records schedule matches and the calculated red, green, blue, and white values for the first three LEDs. To view the latest entries, enter:

   ```bash
   tail -n 20 /home/pi/Desktop/control_moon/log.txt
   ```

   The existing `log.txt` is cleared when the MoonShine Python script starts again. A running service and valid log entries do not necessarily mean that the LEDs should currently be illuminated; check the scheduled brightness values.

5. **Optional: Check CPU activity** by entering:

   ```bash
   htop
   ```

   The current MoonShineP script waits for 52 seconds after each LED update and then repeatedly checks the clock for the next whole minute. This can temporarily increase CPU activity shortly before each update. It is not, by itself, evidence of a service failure. Consider upgrading the cooling system (heatsink and fan) if a large light schedule causes excessive CPU load.

   ```{figure} /images/htop.png
   :name: htop

   Example of increased CPU usage while the moonlight and sunlight/twilight schedulers check the clock before updating their LEDs.
   ```

### Troubleshooting a service that does not launch

If MoonShineP does not appear to be running, **check its status and error logs before reinstalling software or changing hardware settings**:

```bash
sudo systemctl status moonshine_moon.service --no-pager -l
sudo journalctl -u moonshine_moon.service -n 30 --no-pager
```


## To end `moonshine_moon.service`

1. **Stop MoonShineP** to prevent the Python script from updating the LEDs:

   ```bash
   sudo systemctl stop moonshine_moon.service
   ```

   The LEDs may remain at their last brightness level, because stopping the service does not automatically send a command to turn them off.

2. Turn off the LEDs using the appropriate clearing script placed on the Raspberry Pi Desktop during software setup.

   To clear the moonlight LEDs:

   ```bash
   sudo /usr/bin/python3 /home/pi/Desktop/clear_moon.py
   ```

   To clear the sunlight/twilight LEDs, first stop `moonshine_sun.service`, then enter:

   ```bash
   sudo /usr/bin/python3 /home/pi/Desktop/clear_sun.py
   ```

3. **Optional: Prevent automatic startup at the next boot.** Stopping a service does not disable it. To prevent the moonlight service from launching automatically after a reboot, enter:

   ```bash
   sudo systemctl disable moonshine_moon.service
   ```

   Disabling a service does not stop it if it is currently running; use `stop` as in Step 1. To resume automatic startup in the future, use `sudo systemctl enable moonshine_moon.service`.

```{note}
All service-management commands in this chapter also apply to the sunlight/twilight system by replacing `moonshine_moon.service` with `moonshine_sun.service`. Use the corresponding sunlight/twilight Python script, schedule, and clearing script where applicable.
```
