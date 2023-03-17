# Klipper Install and Config for Creality Ender 3 S1 Pro
## Install
Download latest RPi imager:

[for Windows](https://downloads.raspberrypi.org/imager/imager_latest.exe)

[for MacOS](https://downloads.raspberrypi.org/imager/imager_latest.dmg)

Choose OS > Other specific-purpose OS > 3D printing > Mainsail OS

Choose Storage (SD Card)

Setup username, password, wifi, keyboard layout, etc.

**Write** to card

## Make
Go to RPi command line via Putty:

[latest](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

```
cd ~/klipper
make menuconfig
```

Setup as in picture, then Q(uit) and Y(es) for save:

![image](https://user-images.githubusercontent.com/33594918/225705154-4801901e-5259-4c0f-91ae-55ec7f869e8b.png)
```
make
```

## Flash
Copy klipper.bin via WinSCP from `~/klipper/out` to PC.

[latest](https://winscp.net/eng/download.php)


Move klipper.bin to folder name **STM32F4_UPDATE** on SD card.

Switch off printer, put SD card to card slot and switch on printer.

## Configure
printer.cfg (for Ender 3 S1 Pro with Sprite Extruder Pro 300°C)

```
################## Creality Ender 3 S1 Pro Klipper Config ###############
# !Ender-3 S1 Pro
# printer_size: 220x220x270
# version: 3.5
# Motherboard (Late 2020/2021) as the heater pins changed.
# To use this config, during "make menuconfig" select the STM32F401
# with a "64KiB bootloader" and serial (on USART1 PA10/PA9)
# communication.

# Flash this firmware by copying "out/klipper.bin" to a SD card and
# turning on the printer with the card inserted. The firmware
# filename must end in ".bin" and must not match the last filename
# that was flashed.

# See docs/Config_Reference.md for a description of parameters.

[include mainsail.cfg]
[include shell_command.cfg]

[stepper_x]
step_pin: PC2
dir_pin: PB9
enable_pin: !PC3
rotation_distance: 40
microsteps: 16
endstop_pin: !PA5
position_min: -5
position_endstop: -5
position_max: 235
homing_speed: 80

[stepper_y]
step_pin: PB8
dir_pin: PB7
enable_pin: !PC3
rotation_distance: 40
microsteps: 16
endstop_pin: !PA6
position_min: -2
position_endstop: -2
position_max: 235
homing_speed: 80

[stepper_z]
step_pin: PB6
dir_pin: !PB5
enable_pin: !PC3
rotation_distance: 8
microsteps: 16
endstop_pin: probe:z_virtual_endstop           # enable to use bltouch
#endstop_pin: !PA15                # disable to use bltouch
#position_endstop: -0.1
position_min: -10
position_max: 275
homing_speed: 4
second_homing_speed: 1
homing_retract_dist: 2.0

[extruder]
max_extrude_only_distance: 1000.0
step_pin: PB4
dir_pin: PB3
enable_pin: !PC3
rotation_distance: 7.5
### Steps variant 1 stock for E3 S1 Pro ###
#rotation_distance: 7.5
### Steps variant 2 stock for E3 S1 ###
#rotation_distance: 7.6190
### Steps variant 3 with gear ratio ###
#gear_ratio: 3.5:1
#rotation_distance: 26.3585
### Steps variant 4 with gear ratio ###
#gear_ratio: 42:12
#rotation_distance: 26.359
microsteps: 16
nozzle_diameter: 0.400
filament_diameter: 1.750
heater_pin: PA1
sensor_type: EPCOS 100K B57560G104F
sensor_pin: PC5
control: pid
### tuned for stock hardware with 200 degree Celsius target ###
#pid_Kp: 22.429
#pid_Ki: 1.300
#pid_Kd: 96.726
### PID variant 2 ###
pid_Kp: 21.761
pid_Ki: 1.261
pid_Kd: 93.844
### PID variant 3 ###
#pid_Kp: 23.561
#pid_Ki: 1.208
#pid_Kd: 114.859
min_temp: 0
max_temp: 315
pressure_advance = 0.025
#pressure_advance = 0

[heater_bed]
heater_pin: PA7
sensor_type: EPCOS 100K B57560G104F
sensor_pin: PC4
control: pid
# tuned for stock hardware with 50 degree Celsius target
pid_Kp: 73.008
pid_Ki: 2.421
pid_Kd: 550.294
min_temp: 0
max_temp: 125

[verify_heater extruder]
check_gain_time: 200
hysteresis: 5

[fan]
pin: PA0
kick_start_time: 0.5

#set heater fan runnig with temperature over 60;
[heater_fan hotend_fan]
pin: PC0
max_power: 0.8
shutdown_speed : 0
heater:extruder
heater_temp : 60
fan_speed : 1.0

[firmware_retraction]
retract_length: 0.8
retract_speed: 40
unretract_speed: 40

[filament_switch_sensor Runout_Sensor]
pause_on_runout: true
runout_gcode: PAUSE
insert_gcode: RESUME
switch_pin: !PC15

[mcu]
serial: /dev/serial/by-id/usb-1a86_USB_Serial-if00-port0
restart_method: command

[mcu rpi]
serial: /tmp/klipper_host_mcu

[adxl345]
cs_pin: rpi:None

[resonance_tester]
accel_chip: adxl345
accel_per_hz: 70
probe_points:
    117.5,117.5,10

[input_shaper]
#shaper_type_x = 3hump_ei
#shaper_freq_x = 78.2
#shaper_type_y = mzv
#shaper_freq_y = 38.2

[respond]
#default_type: echo
#   Sets the default prefix of the "M118" and "RESPOND" output to one
#   of the following:
#       echo: "echo: " (This is the default)
#       command: "// "
#       error: "!! "
#default_prefix: echo:
#   Directly sets the default prefix. If present, this value will
#   override the "default_type".

[printer]
kinematics: cartesian
max_velocity: 300
max_accel: 5000
max_accel_to_decel: 3000
# max_accel_to_decel: 7000
max_z_velocity: 10
max_z_accel: 1000
square_corner_velocity: 5.0

[bltouch]
sensor_pin: ^PC14       # signal check port ^stand for pull up
control_pin: PC13       # singal control prot
x_offset: -30.0
y_offset: -40.0
# variant 2
#x_offset: -32
#y_offset: -41
#z_offset: 0          # z off_set configuration
speed: 20
stow_on_each_sample = false #high speed for bltoch,
samples: 3
#probe_with_touch_mode = true

[safe_z_home]
home_xy_position:145,155
speed: 200
z_hop: 10
z_hop_speed: 10

[bed_mesh]
speed: 150
mesh_min: 15,15         # need to handle head distance with bl_touch
mesh_max: 205,195       # max probe range
probe_count: 10,10
fade_start: 1
fade_end: 10
fade_target: 0
algorithm: bicubic

[bed_screws]
screw1: 25, 33
screw2: 202, 33
screw3: 202, 202
screw4: 25, 202

[temperature_sensor Raspberry_Pi]
sensor_type: temperature_host
min_temp: 0
max_temp: 100

[virtual_sdcard]
path: ~/printer_data/gcodes

[display_status]

[pause_resume]
recover_velocity: 25

[gcode_macro PAUSE]
description: Pause the actual running print
rename_existing: PAUSE_BASE
### change this if you need more or less extrusion ###
variable_extrude: 1.0
gcode:
  ##### read E from pause macro #####
  {% set E = printer["gcode_macro PAUSE"].extrude|float %}
  ##### set park positon for x and y #####
  # default is your max posion from your printer.cfg
  {% set x_park = printer.toolhead.axis_maximum.x|float - 5.0 %}
  {% set y_park = printer.toolhead.axis_maximum.y|float - 5.0 %}
  ##### calculate save lift position #####
  {% set max_z = printer.toolhead.axis_maximum.z|float %}
  {% set act_z = printer.toolhead.position.z|float %}
  {% if act_z < (max_z - 2.0) %}
      {% set z_safe = 2.0 %}
  {% else %}
      {% set z_safe = max_z - act_z %}
  {% endif %}
  ##### end of definitions #####
  PAUSE_BASE
  G91
  {% if printer.extruder.can_extrude|lower == 'true' %}
    G1 E-{E} F2100
  {% else %}
    {action_respond_info("Extruder not hot enough")}
  {% endif %}
  {% if "xyz" in printer.toolhead.homed_axes %}
    G1 Z{z_safe} F900
    G90
    G1 X{x_park} Y{y_park} F6000
  {% else %}
    {action_respond_info("Printer not homed")}
  {% endif %} 

[gcode_macro RESUME]
description: Resume the actual running print
rename_existing: RESUME_BASE
gcode:
  ##### read E from pause macro #####
  {% set E = printer["gcode_macro PAUSE"].extrude|float %}
  #### get VELOCITY parameter if specified ####
  {% if 'VELOCITY' in params|upper %}
    {% set get_params = ('VELOCITY=' + params.VELOCITY)  %}
  {%else %}
    {% set get_params = "" %}
  {% endif %}
  ##### end of definitions #####
  {% if printer.extruder.can_extrude|lower == 'true' %}
    G91
    G1 E{E} F2100
  {% else %}
    {action_respond_info("Extruder not hot enough")}
  {% endif %}  
  RESUME_BASE {get_params}

[gcode_macro CANCEL_PRINT]
description: Cancel the actual running print
rename_existing: CANCEL_PRINT_BASE
gcode:
  TURN_OFF_HEATERS
  {% if "xyz" in printer.toolhead.homed_axes %}
    G91
    G1 Z4.5 F300
    G90
  {% else %}
    {action_respond_info("Printer not homed")}
  {% endif %}
    G28 X Y
  {% set y_park = printer.toolhead.axis_maximum.y|float - 5.0 %}
    G1 Y{y_park} F2000
    M84
  CANCEL_PRINT_BASE

[gcode_macro M75]
gcode:

[gcode_macro M77]
gcode:

[gcode_macro G29]
gcode:
  G28
  bed_mesh_calibrate
  G1 X0 Y0 Z10 F4200
  # save_config

[gcode_macro _POWER_OFF_PRINTER]
gcode:
  {action_call_remote_method("set_device_power",
                             device="printer",
                             state="off")}

[gcode_macro START_PRINT]
variable_bed_temp: 60
variable_extruder_temp: 185
gcode:
    # Turn ON Power for DRYER
    POWER_ON_DRYER
    # Start bed heating
    M140 S{bed_temp}
    # Use absolute coordinates
    G90
    # Reset the G-Code Z offset (adjust Z offset if needed)
    ###SET_GCODE_OFFSET Z=0
    # Home the printer
    G28
    # Load bed level
    M420
    # Move the nozzle near the bed
    G1 Z10 F3000
    # Move the nozzle very close to the bed
    ###G1 Z0.15 F300
    # Wait for bed to reach temperature
    M190 S{bed_temp}
    # Set and wait for nozzle to reach temperature
    M109 S{extruder_temp}
    # Reset Extruder
    G92 E0
    # Move Z Axis up
    G1 Z2.0 F3000
    # Move to start position
    G1 X2.1 Y20 Z0.28 F5000.0
    # Draw the first line
    G1 X2.1 Y200.0 Z0.28 F1500.0 E15
    # Move to side a little
    G1 X2.4 Y200.0 Z0.28 F5000.0
    # Draw the second line
    G1 X2.4 Y20 Z0.28 F1500.0 E30
    # Reset Extruder
    G92 E0
    # Move Z Axis up
    G1 Z2.0 F3000
    # Print message on LCD
    M117 Printing...

[gcode_macro END_PRINT]
variable_machine_depth: 235
gcode:
    # Turn off bed, extruder, and fan
    M140 S0
    M104 S0
    M106 S0
    # Relative positionning
    G91
    # Retract a bit
    G1 E-2 F2700
    # Retract and raise Z
    G1 Z0.2 E-2 F2400
    # Wipe out
    G1 X5 Y5 F3000
    # Raise Z more
    G1 Z10
    # Absolute positionning
    G90
    # Present print
    G1 X0 Y{machine_depth}
    # Disable steppers
    M84 X Y E ;Disable all steppers but Z
    POWER_OFF_DRYER ;Turn OFF Power for DRYER
    UPDATE_DELAYED_GCODE ID=POWER_OFF_CHECK DURATION=30
    M117 Finished! Cooling down under 50°C before POWER OFF...

[gcode_macro M420]
gcode:
  BED_MESH_PROFILE LOAD=default
  
[delayed_gcode bed_mesh_init]
initial_duration: .01
gcode:
  BED_MESH_PROFILE LOAD=default

[gcode_macro AUTO_CALIBRATE_Y]
gcode:
  G28
  SHAPER_CALIBRATE AXIS=Y
  SAVE_CONFIG

[gcode_macro AUTO_CALIBRATE_X]
gcode:
  G28
  SHAPER_CALIBRATE AXIS=X
  SAVE_CONFIG

[delayed_gcode POWER_OFF_CHECK]
gcode:
  {% if printer.idle_timeout.state == "Idle" or printer.idle_timeout.state == "Ready" %}
    {% if printer.extruder.temperature < 60.0 and printer.heater_bed.temperature < 50.0 %}
        {% if printer.extruder.target == 0.0 and printer.heater_bed.target == 0.0 %}
            UPDATE_DELAYED_GCODE ID=POWER_OFF_CHECK DURATION=0
            POWER_OFF ;Turn OFF Power for printer
        {% else %}
            UPDATE_DELAYED_GCODE ID=POWER_OFF_CHECK DURATION=2
        {% endif %}
    {% else %}
        {% if printer.idle_timeout.state == "Printing" %}
            UPDATE_DELAYED_GCODE ID=POWER_OFF_CHECK DURATION=0
        {% else %}
            {% if printer.extruder.target == 0.0 and printer.heater_bed.target == 0.0 %}
                UPDATE_DELAYED_GCODE ID=POWER_OFF_CHECK DURATION=2
            {% else %}
                UPDATE_DELAYED_GCODE ID=POWER_OFF_CHECK DURATION=0
            {% endif %}
        {% endif %}
    {% endif %}
  {% endif %}

#*# <---------------------- SAVE_CONFIG ---------------------->
#*# DO NOT EDIT THIS BLOCK OR BELOW. The contents are auto-generated.
#*#
#*# [bed_mesh default]
#*# version = 1
#*# points =
#*# 	  0.208333, 0.142500, 0.140833, 0.142500, 0.065833, 0.031667, 0.093333, 0.170000, 0.135833, 0.131667
#*# 	  0.141667, 0.078333, 0.056667, 0.090000, 0.015833, -0.028333, 0.048333, 0.121667, 0.100833, 0.089167
#*# 	  0.121667, 0.050833, 0.018333, 0.053333, 0.000000, -0.034167, 0.031667, 0.078333, 0.056667, 0.054167
#*# 	  0.127500, 0.031667, -0.005833, 0.035000, 0.006667, -0.011667, 0.038333, 0.065000, 0.036667, 0.050000
#*# 	  0.102500, 0.011667, -0.025000, 0.016667, -0.008333, -0.036667, 0.031667, 0.074167, 0.038333, 0.049167
#*# 	  0.126667, 0.020000, -0.030833, 0.016667, -0.008333, -0.033333, 0.021667, 0.052500, 0.037500, 0.042500
#*# 	  0.117500, 0.029167, -0.017500, 0.031667, -0.012500, -0.045000, 0.020833, 0.052500, 0.016667, 0.035000
#*# 	  0.124167, 0.046667, -0.007500, 0.045833, 0.029167, -0.005833, 0.042500, 0.077500, 0.055000, 0.067500
#*# 	  0.157500, 0.063333, 0.021667, 0.069167, 0.040833, 0.035000, 0.089167, 0.122500, 0.068333, 0.062500
#*# 	  0.243333, 0.180000, 0.102500, 0.113333, 0.075000, 0.062500, 0.138333, 0.146667, 0.080000, 0.102500
#*# tension = 0.2
#*# min_x = 15.0
#*# algo = bicubic
#*# y_count = 10
#*# mesh_y_pps = 2
#*# min_y = 15.0
#*# x_count = 10
#*# max_y = 195.0
#*# mesh_x_pps = 2
#*# max_x = 204.99
#*#
#*# [bltouch]
#*# z_offset = 2.400
#*#
#*# [input_shaper]
#*# shaper_type_y = mzv
#*# shaper_freq_y = 39.2
#*# shaper_type_x = mzv
#*# shaper_freq_x = 109.8
```

Cura Start G-Code
```
SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=bed_temp VALUE={material_bed_temperature_layer_0}
SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=extruder_temp VALUE={material_print_temperature_layer_0}
START_PRINT
```

Cura End G-Code
```
SET_GCODE_VARIABLE MACRO=END_PRINT VARIABLE=machine_depth VALUE={machine_depth}
END_PRINT
```

PrusaSlicer Start G-Code
```
SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=bed_temp VALUE={first_layer_bed_temperature[0]}
SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=extruder_temp VALUE={first_layer_temperature[0]}
START_PRINT
```

PrusaSlicer End G-Code
```
SET_GCODE_VARIABLE MACRO=END_PRINT VARIABLE=machine_depth VALUE={print_bed_max[1]}
END_PRINT
```
### ADXL345 setup
Wiring diagram

![image](https://user-images.githubusercontent.com/33594918/225709045-1f259db4-2a5b-437c-98df-115e01f85e7c.png)

Software install via Putty
```
sudo apt update
sudo apt install python3-numpy python3-matplotlib libatlas-base-dev
~/klippy-env/bin/pip install -v numpy
```

Install rc script
```
cd ~/klipper/
sudo cp ./scripts/klipper-mcu.service /etc/systemd/system/
sudo systemctl enable klipper-mcu.service
```

Building the micro-controller code
```
cd ~/klipper/
make menuconfig
```

![image](https://user-images.githubusercontent.com/33594918/225734204-0951a155-3fd2-49bb-aa9d-1a9856ce0770.png)

```
sudo service klipper stop
make flash
sudo service klipper start
```

The following command will add the "pi" user to the tty group: (change "pi" to your real user)
```
sudo usermod -a -G tty pi
```

Optional: Identify the correct gpiochip
```
sudo apt-get install gpiod
gpiodetect
gpioinfo
```

## Plugins
### Telegram-Bot
```
cd ~
git clone https://github.com/nlef/moonraker-telegram-bot.git
cd moonraker-telegram-bot
./scripts/install.sh
```

Create telegram bot

This is done by talking to [BotFather](https://telegram.me/botfather) on telegram. Name the bot, give it a username. You will get a token to control the bot.

edit telegram.conf
```
#  Please refer to the wiki(https://github.com/nlef/moonraker-telegram-bot/wiki) for detailed information on how to configure the bot

[bot]
server: localhost
bot_token: add_your_token_here
chat_id: add_your_chat-id_here

[camera]
host: http://localhost:8080/?action=stream

[progress_notification]
percent: 5
height: 5
time: 5

#[timelapse]
#cleanup: true
#height: 0.2
#time: 5
#target_fps: 30
```

add to moonraker.conf
```
[update_manager client moonraker-telegram-bot]
type: git_repo
path: ~/moonraker-telegram-bot
origin: https://github.com/nlef/moonraker-telegram-bot.git
env: ~/moonraker-telegram-bot-env/bin/python
requirements: scripts/requirements.txt
install_script: scripts/install.sh
```
### Shell Commands
```
cd ~
git clone https://github.com/th33xitus/kiauh.git
./kiauh/kiauh.sh
```

![image](https://user-images.githubusercontent.com/33594918/225716494-316f0b8e-5122-4d17-9275-6058a1ae5968.png)

[4] Advanced > Extensions > [8 or 9] Shell Command

edit shell_command.cfg
```
# example
[gcode_shell_command print_to_file]
command: sh /home/pi/klipper_config/script.sh
timeout: 30.
verbose: True

[gcode_macro GET_TEMP]
gcode:
    {% set temp = printer.extruder.temperature %}
    { action_respond_info("%s" % (temp)) }
    RUN_SHELL_COMMAND CMD=print_to_file PARAMS={temp}
```

### Mobileraker
```
cd ~/
git clone https://github.com/Clon1998/mobileraker_companion.git
cd mobileraker_companion
./scripts/install-mobileraker-companion.sh
```

edit mobileraker.conf
```
[general]
language: en 
# one of the supported languages defined in i18n.py#languages (de,en,...)
# Default: en
timezone: Europe/Berlin 
# correct timezone e.g. Europe/Berlin for Berlin time or US/Central. 
# For more values see https://gist.github.com/heyalexej/8bf688fd67d7199be4a1682b3eec7568
# Default: Tries to use system timezone
# Optional
eta_format: %%d.%%m.%%Y, %%H:%%M:%%S
# Format used for eta and adaptive_eta placeholder variables
# For available options see https://strftime.org/
# Note that you will have to escape the % char by using a 2nd one e.g.: %d/%m/%Y -> %%d/%%m/%%Y
# Default: %%d.%%m.%%Y, %%H:%%M:%%S
# Optional

# Add a [printer ...] section for every printer you want to add
[printer <NAME OF YOUR PRINTER: optional>]
moonraker_uri: ws://127.0.0.1:7125/websocket
# Define the uri to the moonraker instance.
# Default value: ws://127.0.0.1:7125/websocket
# Optional
moonraker_api_key: False
# Moonraker API key if force_logins or trusted clients is active!
# Default value: False
# Optional
```

add to moonraker.conf
```
[update_manager mobileraker]
type: git_repo
path: ~/mobileraker_companion
origin: https://github.com/Clon1998/mobileraker_companion.git
primary_branch:main
managed_services: mobileraker
env: ~/mobileraker-env/bin/python
requirements: scripts/mobileraker-requirements.txt
install_script: scripts/install-mobileraker-companion.sh
```


# Creality Ender 3 V2 Screen kit for E3S1Pro (DWIN or DACAI)
In brief, add a line to the /boot/config.txt file to apply a Device Tree overlay.
```
dtoverlay=disable-bt
```

in `klipper.service:` add to end of `ExecStart=` 
```
-a /tmp/klippy_uds
```
or
```
```

then install libraries
```
sudo apt-get install python3-pip python3-gpiozero python3-serial git

sudo pip3 install multitimer

git clone https://github.com/RobRobM/DWIN_T5UIC1_LCD_E3S1.git
```


Wiring diagram:
![image](https://user-images.githubusercontent.com/33594918/225875818-e2fc4e3f-6ef4-445c-84de-d0467756aad5.png)

Display <-> Raspberry Pi GPIO BCM
Rx = GPIO14 (Tx)
Tx = GPIO15 (Rx)
Ent = GPIO13
A = GPIO19
B = GPIO26
Vcc = 2 (5v)
Gnd = 6 (GND)

![image](https://user-images.githubusercontent.com/33594918/225876126-a45fad86-dc6e-441b-ab64-352a526fefa7.png)
![image](https://user-images.githubusercontent.com/33594918/225876081-abb26c5c-2b2e-4337-b621-e8781de0d5f7.png)
![image](https://user-images.githubusercontent.com/33594918/225875998-b528f705-e4b6-4bee-ab1d-6f16fa9273f6.png)
![image](https://user-images.githubusercontent.com/33594918/225876025-646b4f40-90c3-49b3-89db-dc38f844dab2.png)

To get your API key run:
```
~/moonraker/scripts/fetch-apikey.sh
```
Edit the file run.py and past your API key
```
nano run.py
```

# References
Creality Sonic Pad - Printer Config for Ender 3 S1 Pro [STM32F401]

https://github.com/CrealityOfficial/Creality_Sonic_Pad/blob/main/printer_configrations/printer-Ender3s1pro-401.cfg


How to Install MainsailOS on Raspberry Pi

https://3dprintbeginner.com/how-to-install-mainsailos-on-raspberry-pi/


How to Install Klipper on Creality Ender 3 S1 Pro: Config and Setup

https://3dprintbeginner.com/install-klipper-on-ender-3-s1-pro/


Measuring Resonances via ADXL345

https://www.klipper3d.org/Measuring_Resonances.html


RPi microcontroller for linux (ADXL345)

https://www.klipper3d.org/RPi_microcontroller.html

DWIN_T5UIC1_LCD_E3S1
https://github.com/RobRobM/DWIN_T5UIC1_LCD_E3S1 (for "pi" username)
https://github.com/Buadrabas/DWIN_T5UIC1_LCD_E3S1 (for "USER" username)
