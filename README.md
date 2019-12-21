# Step-by-step instuctions

Box Contents:
* Getting Started Guide
* Terms & Conditions of Use and Important Safety Information
* Instruction manual for Spektrum DXe Transmitter
* Pre-assembled quad-copter airframe
* 2 sets of snap-on propellers: “A” (x4) and “B” (x4)
* Spektrum* DXe Transmitter (2.4GHz DSMX*)
* 4 AA alkaline batteries for Spektrum DXe Transmitter

After flashing, you will have an updated and working Ready To Fly Drone and Compute Board.

By default the Drone is working as a:
* network: as a wifi access point, and accepting ssh connections
* login: presenting a very basic terminal on the display

_Battery not included. Recommended Battery: Li-Po, 4000 - 4500 mAh, 4S, with XT60 connector._

_Safety for the Battery: The Operator is responsible for obtaining a battery and battery charger which are certified for use in the Operator‘s country of operation. The Operator is responsible for proper use of the battery and battery charger per the manufacturer’s safety guidelines._

_The Intel Aero Ready to Fly Drone is a pre-assembled quadcopter that is purposely built for professional drone application developers to enable you to get your applications airborne quickly._

_This development platform is intended to be modified by developers according to their professional judgment. Intel has not established operating limitations for the kit or tested any configurations other than the base configuration that is shipped as configured from the factory. Developers are responsible for testing and ensuring the safety of their own configurations, and establishing the operating limits of those configurations._

# 1. Power Supply
If you use the wall socket power adapter, connect the cable as described in [the first page](https://github.com/intel-aero/meta-intel-aero/wiki/01-About-Intel-Aero#wall-socket-power-adapter).
If you use a LiPo battery, refer to the manual of your charger and battery to safely charge your LiPo battery. Then connect the battery to Intel Aero XT60 connector.
A short press of the power button should switch on Intel Aero. You'll hear a beep and see light coming from the adjacent LEDs.

### Connecting cables
You have the choice between 2 options:

_Recommended method_: Connect all devices to flash the system and have full local graphical login
* OTG USB cable to Aero ([check the cable reference notes](https://github.com/intel-aero/meta-intel-aero/wiki/08-Aero-Network-and-System-Administration#sysadmin-ubuntu-installation))
* USB hub to OTG cable (optional)
* Keyboard, mouse and USB thumb drive
* Micro HDMI cable to Aero

Alternate method: Connect a USB thumb only
* OTG USB cable to Aero
* USB thumb drive

### Plugging / Unplugging the fan

If you use the Intel Aero Ready to Fly Drone, a fan is included. If you plan to leave the drone on your desk all day and do not plan to run complex codes that would cause the board to heat, unplugging the fan may be an option. To do so, unscrew the top screws, slightly open the lid and unplug the fan connector.

# 2. Flashing
## Different parts of the drone to flash
Intel Aero Compute Board is like your laptop or server: it has an UEFI BIOS and an operating system.

But the Compute Board also has a FPGA, in charge of routing the IOs between the processor (SoC, System-on-Chip to be precise) and the motors plus flight controller. In certain cases, the FPGA will need to be flashed too.

If you have the Intel Aero Ready to Fly Drone, you also have the Intel Aero Flight Controller in it. You will have to flash it when you first use the drone and in certain cases (if you do flight controller development or want to change some FC settings). Flashing the FC means you'll have to go though the QGroundControl sensor calibrating procedure. Intel is shipping the drone with PX4 already flashed, but you can choose between PX4 and Ardupilot as both are included in the default filesystem iso image. [ArduPilot.org](http://ardupilot.org/copter/docs/common-intel-aero-rtf.html) also has some documentation.

To summarize:
1. .iso files for the Linux operating system
2. .rpm for the BIOS, part of the iso file (also available as a separate downloadable file).
3. .jam for the FPGA, part of the iso file
4. .px4 for the Flight Controller, part of the iso file (both PX4 and Ardupilot have a PX4 extension)

Where to get the files:
* [downloadcenter.intel.com page to download the .iso linux image](https://downloadcenter.intel.com/download/26389/UAV-installation-files-for-Intel-Aero-Platform?v=t).
* .rpm .jam and .px4 are part of the .iso

Checking the .iso file:

After downloading files, always check the md5 hash of the file matches the md5 published on Intel's download website. Do not flash if the hashes do not match.
To get the checksum on linux type:
```
md5sum intel-aero-image-intel-aero-1.6.iso
```

## Flashing Intel Aero Linux Distribution

### Create an USB drive to flash the Operating System
Easy method: For Linux, Windows and MacOS you can use [Etcher](https://etcher.io/).
* insert the removable/USB disk to the windows machine (it will be formated)
* launch Etcher
* select the .iso file, the USB drive and click on the "Flash" button
* wait for the image to be written and verified

Alternate method: Linux/MacOS users can use the following command in the terminal:

  ```
  sudo dd if=/path/to/your/image/intel-aero-image-intel-aero-1.6.iso of=/dev/sdX bs=1M
  ```

Where X is the letter of your USB key (be careful to format the USB key and not your main disk !). It may take a few minutes.

### Flash Intel Aero Linux distribution - recommended method

For the recommended method (HDMI screen connected):
* shut down Intel Aero (unplug-wait 5s-replug)
* press ESC to enter the BIOS
* select boot from USB key
* when booting from USB key, select "install"

### Flash Intel Aero Linux distribution - alternate method

For the alternate method (no screen attached):

Connect the USB drive to Intel Aero using the OTG cable.
If you already have a system with firmware v1.2 or more recent, type the command:
```
aero-reboot-update.py
```
If this command does not work, it means you have an older version. Type:
```
mkdir -p /tmp/{iso,newroot}
mount -o ro /dev/sda1 /tmp/iso
mount -o loop,ro /tmp/iso/rootfs.img /tmp/newroot
/tmp/newroot/usr/sbin/aero-reboot-update.py
```
The commands above will verify you have a valid USB drive connected, do some sanity checks and reboot into the update image.

Here's how to follow the flash progress during the alternate method flash:

If you have the Aero RTF Drone, the blue LEDs next to the power button to convey feedback about the update progress. The RGB LED may be difficult to see on the RTF since it's inside the case. You can look to the hole through each the FAN cable is passing in order to check the color of the RGB LED.

LED colors and frequencies:
1. By default the LED is green
1. Before rebooting the RGB LED will become yellow: this means the board started the reboot progress. It takes ~20 seconds. If the BIOS was not updated yet, the reboot command may get stuck. You can remove power and power it on again.
1. When board is rebooted the RGB LED becomes green again.
1. During the update process the RGB LED and the LEDs next to the power button (on RTF) will blink slowly at 1Hz. The time taken by the update process may vary, but should take around 3 minutes.
1. When finished the LEDs will blink fast at 10Hz for 5 seconds and reboot. After that the update process is done and you should be greeted with the new version.

### Flashing the BIOS
Although the BIOS file (.rpm) is part of the Aero image (.iso), always download the latest BIOS from the [Intel Download Center](https://downloadcenter.intel.com/download/27399/Intel-Aero-Platform-for-UAVs-Installation-Files?v=t ) and copy onto the Aero disk space.  

To install, first remove the previous version (v1.00.13) and then install the latest (v1.00.16)

```
  rpm -ev aero-bios-01.00.13-r1.corei7_64
  rpm -i aero-bios-01.00.16-r1.corei7_64.rpm
  aero-bios-update
```

Then reboot, the UEFI BIOS will detect the files and update. You should see (if you have a screen connected):
  ```
  GoTo FOTA Process Begin.
  Back up BIOS for seamless recovery. File size is: 0x3C9000
  CHTT_X64_RELEASE_RECOVERY.ROM written onto eMMC. Continue to update Firmware.
  Set FOTA Process Step:  21
  Start to update firmware
  Updating firmware… <n%> Completed.
  Flash Update Complete Status Success. Ready to reset…
  Set FOTA Process Step:  FF
  AFU: Delete File CHTT_X64_RELEASE_RECOVERY.ROM on eMMC.
  AFU: Delete File BIOSUpdate.FV on eMMC.
  Finish FOTA Capsule Update Process.
  ```

If the Intel Aero Compute Board reboots automatically and endlessly after the completion of the BIOS update process, a cold reboot is required.

Check the BIOS version:
  ```
  aero-get-version.py
  ```
  You should see a message like this (or newer):
  ```
  BIOS_VERSION = Aero-01.00.13
  OS_VERSION = Poky Aero (Intel Aero linux distro) v1.6.0 (pyro)
  AIRMAP_VERSION = 1.8
  FPGA_VERSION = 0xc2
  Aero FC Firmware Version = 1.6.5
  ```

### Flashing the FPGA

Aero comes with 3 FPGA firmwares that can be used, all of them under the `/etc/fpga/` directory.
* `aero-rtf.jam`: this is for use with RTF
* `aero-rtf-recovery.jam`: this is for use with the RTF under special circunstances: it allows Aero to instruct the flight controller to stop on bootloader so we can flash new versions of the firmware even if the firmware stops responding due to a bad update
* `aero-compute-board.jam`: this should be selected if using only the compute board. Note that the labels that accompany the compute board have no meaning. Check the official documentation for each pin.

Flash the new FPGA firmware:
```
cd /etc/fpga/
jam -aprogram <firmware>.jam
```

In which `<firmware>` is one of the firmwares above.

### Flashing the Flight Controller

A Flight Controller is included in the Intel Aero Ready to Fly Drone and this FC needs to be updated too. The current release of Intel Aero includes the recommended version for PX4. The firmwares are located on `/etc/px4-fw/` directory. To update the flight controller use the following command:
```
cd /etc/aerofc/px4/
aerofc-update.sh nuttx-aerofc-v1-default.px4
```
_**Note:** The script will try all the possible serial port speeds. It is usually able to sync and flash within 10 seconds, but can take longer in some cases. If it does not, let it try for a few minutes and try again._

You can also flash a new version of the firmware directly from the [PX4 repository](https://github.com/PX4/Firmware/). Follow the instructions on their [wiki](https://dev.px4.io/hardware-intel-aero.html).

_**Note:** Parallel to the Intel provided PX4 version, you can install the Ardupilot version:_
```
cd /etc/aerofc/ardupilot/
aerofc-update.sh arducopter-aerofc-v1.px4
```
_**Note:** The script will try all the possible serial port speeds. It is usually able to sync and flash within 10 seconds, but can take longer in some cases. If it does not, let it try for a few minutes and try again._

If you see an infinite loop you can run:
```
aerofc-force-bootloader-pin.py 1
```
to force flight stack go to bootloader and start the update. After that, run:
```
aerofc-force-bootloader-pin.py 0
```
to allow PX4/Ardupilot to boot. After updating the flight stack, make sure to go through the calibration procedures.

### Check the BIOS-FPGA-FC-OS version:
  ```
  aero-get-version.py
  ```
  You should see a message like this (or newer):
  ```
  BIOS_VERSION = Aero-01.00.13
  OS_VERSION = Poky Aero (Intel Aero linux distro) v1.6.0 (pyro)
  AIRMAP_VERSION = 1.8
  FPGA_VERSION = 0xc2
  Aero FC Firmware Version = 1.6.5
  ```

# 3. QGroundControl

The Intel Aero Ready to Fly Drone was tuned, calibrated, and flight tested before it shipped from the factory. After updating the flight stack (i.e. not using the one from factory) it is **required** to calibrate it again as the tunning parameters may have changed on the upstream repositories. Even without updating, flight performance may improve by calibrating the drone again using the [QGroundControl](http://qgroundcontrol.com) application.

**To re-calibrate the drone using QGroundControl:**

Download and install the QGroundControl software application (QGC) onto a laptop. Please read all instructions and release notes for your laptop’s operating system. The Intel Aero Ready to Fly Drone was tested with QGroundControl using a laptop running QGC for Windows. Support for the Aero Ready to Fly Drone is available in QGroundControl version 3.1.0 and newer.

Connect your device running QGC to the Intel Aero Ready to Fly Drone using WiFi:
   1. The WiFi access point SSID for the drone will appear as "Aero-xxxxx" where xxxxx will be the MAC address of the drone.
   2. The passkey for the Aero WiFi access point is “1234567890”.
   3. Disable any firewall or VPN service on the laptop which is running QGC.

**To perform the calibration process:**
   1. Once the laptop which is running QGC connects to the Intel Aero Ready to Fly Drone, select
Airframe component along the left-hand side. QGC ver 3.1.0 or later will identify the airframe as
“Intel Aero RTF” while older versions will identify as a custom airframe. Select “Apply & Reset”.
   2. After QGC resumes from reset and reconnects to the Intel Aero Ready to Fly Drone, go through
the sensor and radio calibration process.
   3. After calibration, the Summary screen should show green status indicators in each tile.

If you wish to monitor the drone’s battery status in QGC, you will need to update the drone’s battery characteristics in the QGC application. **This will require a connection to the drone using a USB cable.**

   _Find detailed instructions how to use the QGroundControl application on the [qgroundcontrol.com](http://qgroundcontrol.com) website._

**Modifying Flight Modes**

   The Flight Mode toggle switch (Figure 4) is pre-programmed to

   _0: Manual 1: Altitude Hold 2: Position Hold (GPS)_

   These modes are recognized by the PX4 flight control software. It is recommended to use __Position Hold (GPS)__ flight mode (outdoors only) until you become familiar with the flight characteristics of the Intel Aero Ready to Fly Drone. For indoor flights where GPS is not available, the Flight Mode switch must be set to Manual flight mode. Other PX4 flight modes can be programmed using the QGroundControl application (qgroundcontrol.com).

 _**Note:** Once you have made any modifications to the Flight Mode settings, the default features and factory settings which are assumed in this guide will no longer apply._

Navigate to Flight Modes and set the following 3 modes:

  * _Flight Mode 1 – Position_

  * _Flight Mode 4 – Altitude_

  * _Flight Mode 5 – Manual_

When this procedure is finished, you should be able to get data in QGC from the drone (ex: GPS coordinates) but also act on the drone (ex: ARM motors, without propellers first).

### Flight logs

On the Intel Aero RTF, the flight controller does not have an interface for flight logging to a sdcard. Instead, the flight logs are stored on the compute board by a daemon called mavlink-router.

Logs are automatically saved on boot in /var/lib/mavlink-router/ (.ulg for PX4 or .bin for ArduPilot)

To learn more about configuring mavlink-router, please visit [https://github.com/intel/mavlink-router](https://github.com/intel/mavlink-router)

# 4. First flight with the remote control transmitter

You should already have experience flying aircrafts via remote control using **Manual** flight mode which requires control of the drone with a two-gimbal joystick controller similar to the one included with the Intel Aero Ready to Fly Drone.

**Read the _Terms & Conditions of Use and Important Safety Information_ document before operating the drone.**

### Attach a Battery
1. Insert a battery inside the center cavity of the drone’s airframe with cables extending out toward the
rear of the drone as shown in Figure 1.
2. Tighten the restraining strap around the battery to secure the battery as close to the center of the
airframe as possible.
3. Connect the battery cable to the drone using the XT60 connector

### Set up the GPS Antenna

 Raise the GPS antenna mast 90 deg. until vertical then tighten the antenna base cover

 ### Attach the propellers
1. Identify propellers marked as “A” and “B” on top of the propeller blade.
2. Attach each propeller to its corresponding labeled motor by pressing down and twisting __counter-clockwise__ (for “A” propellers) and __clockwise__ (for “B” propellers) until each propeller latches securely.
3. To detach the propeller, press the center hub of the propeller while twisting the propeller blade in the opposite direction used when attaching.

### Prepare the remote control transmitter
1. Install the 4 AA alkaline batteries in the transmitter.
2. Flip all toggle switches UP or AWAY from the pilot. The Flight Mode switch should be set to __Manual__ (mode 0) until you arm the motors in Step 3.iv.
3. Turn ON the transmitter by sliding its power switch UP (located between the two gimbal sticks) The transmitter’s power LED will turn to steady orange and a two-tone “rising chirp” will be heard.

### Prepare to Fly
Perform all pre-flight safety procedures as follows: __Pre-Flight Safety Check.__ Perform this safety check before each flight of the drone (also referred to as UAV). Verify the following:

1. The flight zone is clear with enough open space outdoors for take-off and landing (no people, animals, or obstacles within a radius of 10 meters).
2. Propellers are in working condition and attached securely to the motors.
3. All drone components and wires are securely attached to the airframe and do not impede the path of the
propellers.
4. The drone battery is properly charged. Connect the battery to the UAV circuitry only when in use.
5. The drone battery is secured to the airframe.
6. The battery for the remote control transmitter is properly charged.
7. Communication between the remote control transmitter and the drone is functioning properly. (step c, below)
8. __Arm the motors only after people, animals, and obstacles are at a safe distance.__

__WARNING:__ Check airworthiness of the drone before each flight, especially after any incident like a hard landing. Do not fly the drone if any part of the aircraft appears to be damaged or malfunctioning. Always perform the Pre-Flight Safety Check before each flight.

### Turn ON the Drone
Press the power button on the drone (Figure 5) for about 1 second. The two blue LEDs immediately adjacent to the power button will illuminate a steady blue and the cooling fan will spin.

  After turning ON the drone, wait at least 30 seconds before attempting to turn OFF the drone. This includes turning OFF the drone either by pressing the power button or by disconnecting the battery. By waiting for at least 30 seconds, the system should have enough time to load all software components, reducing the risks associated with an unexpected shutdown.

### Verify the remote control transmitter is paired to the drone

Verify the orange LED on the remote control receiver located on the bottom side of the drone (Figure 6) is illuminated and steady orange. _If the LED is blinking or not ON, please check the troubleshooting tips._

### Arming and Disarming the Motors

The following instructions assume default factory settings. If you modify the Flight Mode configurations, these instructions may not apply.

1. Press the left gimbal stick toward the bottom right. Hold for about 2 seconds. The motors will start spinning.

_**CAUTION – MOTORS WILL SPIN – STAY CLEAR OF THE PROPELLERS**_

2. To disarm the motors, press the left gimbal stick toward the bottom left. Hold for about 2 seconds. The motors will stop spinning.

_**Note:** When flying indoors, or areas where there is a weak GPS signal, use Manual flight mode (mode 0)._

### Choose the appropriate Flight Mode.

Mode numbers below correspond to the default mode assignment. Always have a GCS connected and check modes are as expected before flight. QGroundControl is the recommended GCS.

1. INDOOR FLIGHTS: use __Manual__ (mode 0) or __Altitude Hold__ (mode 1).
 _Manual flight mode requires reasonable piloting skills. Until you are experienced, use caution while operating in this mode._
2. OUTDOOR FLIGHTS: use any of the 3 modes: __Manual__ (mode 0), __Altitude Hold__ (mode 1) or __Position Hold (GPS)__ (mode 2).
 _It is recommended to use __Position Hold (GPS)__ (mode 2) when flying outdoors for greater flight stability. Note that this mode relies upon the drone locking onto a GPS signal. For best performance, place the drone in an area that has clear line of sight to the sky and wait for 3D lock and low HDOP before attempting to fly in __Position Hold (GPS)__ mode. Connect a GCS to the drone to check everything is ok (see instructions on page 7 “For Developers and Advanced Users” to install and use the QGroundControl application)._

### Takeoff
 Slowly increase throttle by pushing the left gimbal stick upward. When flying in Manual flight mode, be especially prepared to compensate for any drift by controlling pitch and roll stick. On __Altitude Hold__ or __Position Hold (GPS)__ the drone will only takeoff after the throttle stick (left one) is more or less above the middle level.

### Land the Drone
It is easier to land the drone using __Position Hold (GPS)__ flight mode, which allows for a more controlled rate of decent and stability. Landing safely in __Manual__ flight mode requires reasonable piloting skills. Be prepared to control the rate of decent to minimize hard impact with the landing surface.

### Automated Landing (Optional)
The drone is pre-programmed at the factory to RETURN TO LAUNCH if communication between the remote control transmitter and the drone is lost. RETURN TO LAUNCH is an automated landing procedure that will fly the drone from wherever it is when the transmitter’s signal was lost back to the location from where it first took off, subject to remaining battery power.
You can force this procedure by turning OFF the remote control transmitter while the drone is in flight. Turning ON the transmitter will allow you to regain communications with the drone. This feature may not be available if you make changes to the Flight Modes programmed at the factory.

_**Note:** Other failsafe options like commanding the drone to land rather than returning to launch can be selected on the GCS_

### Turn OFF / Power Down
**Always** turn OFF the drone before turning off the transmitter.

Press and hold the power button (Figure 5) on the drone for approximately 5 seconds until the blue LEDs blink (3 times). About 10 seconds later, the drone will power OFF. During these 10 seconds, the embedded Linux operating system is performing a graceful shutdown. You may now turn OFF the remote control transmitter.

### Troubleshooting

* _The GPS antenna mast cannot be raised 90 deg. (vertical):_

 The antenna hinge may be stiff. Be gentle and apply steady pressure to lift the antenna mast. Be sure to slide the antenna base cover away from the base when attempting to raise the antenna mast so that it does not interfere with the hinge.

* _The orange LED on the remote control receiver is blinking:_

 This indicates the remote control transmitter and receiver are not properly bound (paired) or that at any moment after turining the receiver on it lost connection to the trasmitter. This can happen if the receiver is turned on before the transmitter or if they are in fact not bound. To bind the transmitter and receiver, turn ON the transmitter while holding down the black panic/bind/trainer button that is immediately adjacent to the Flight Mode toggle switch on the top-left of the transmitter (see Figure 4). If the binding process is successful, the receiver’s orange LED will have a steady orange glow. The binding may fail if there are other nearby transmitters of the same mode. This binding will be remembered in the future as long as the transmitter is always turned on before the drone is turned on. To avoid having to bind it again when you the RTF was turned on before the transmitter, you can just turn everything off and on again on the correct order. More information on the binding process can be found in the user guide for the [Spektrum SPM4648 DXMX receiver, found online](https://www.spektrumrc.com/ProdInfo/Files/SPM4648-Manual-EN.pdf)

* _The orange LED on the remote control receiver is ON and steady, but the transmitter does not appear to control the receiver:_

 It is possible the receiver is bound to a different transmitter. Follow the instructions for binding (pairing) the transmitter and receiver. Please refer to the troubleshooting tip above.

* _The motors do not arm:_

 Check if the remote control transmitter and receiver are properly bound (paired). If not, please refer to troubleshooting tip above.

* _The motors arm but the gimbal stick does not control motor throttle:_

 Check if the “Throttle Enable” toggle switch on the remote control transmitter is in the “Arm” position. If it is in the “Disarm” position, throttling will have no effect.

* _Pressing and holding the drone’s power button for 5 seconds does not shutdown the drone:_

 Disconnect the battery. Wait at least 30 seconds. Then reconnect the battery.


 For additional troubleshooting tips, please visit the [Intel Aero Platform Community Support Forum](https://communities.intel.com/community/tech/intel-aero)

# 5. Installing Ubuntu on Intel Aero

## Choice of Operating Systems

Intel is shipping Intel Aero (RTF Drone and Compute Board) with Yocto linux preinstalled and you should download and flash the [updated version of this image](02-Initial-Setup) to update the hardware when you receive it. This Yocto build is preconfigured and highly customized for Intel Aero.

In parallel to this Intel supported Yocto image, full native Ubuntu with Intel drivers are now verified by Intel (as user installation). Intel is providing documentation and packages to let you install Ubuntu yourself, manually. Compared to Yocto, Ubuntu can be interesting for rapid prototyping and software development.

Yocto is aimed for a Ready-To-Fly experience, whereas Ubuntu for prototyping and development.

### Upgrade Yocto first

To keep it simple, [please upgrade to the latest version of Yocto](02-Initial-Setup) and flash the BIOS, FPGA, Flight Controller. Check everything works, then install Ubuntu. It will replace Yocto and keep the BIOS, FPGA and Flight Controller.

### Operating System

1. Download [Ubuntu 16.04.3 x64 Desktop](http://old-releases.ubuntu.com/releases/16.04.3/).
_**Note:** Do not try a more recent version of Ubuntu._
2. Create a bootable disk (refer to the Ubuntu documentation)
3. Plug Intel Aero to the wall power supply, the USB-OTG adapter, bootable USB key, hub, keyboard and mouse.
Power on
4. Type ESC to enter the BIOS
5. Select boot manager, select your USB key and press Enter
6. Install Ubuntu as you would on a computer

In terms of options, my preferences are:
  * Full disk install (erase Yocto and use all space for Ubuntu)
  * Do not install third party proprietary software (flash, mp3)
  * Have my session open automatically (with networking consequences).

### Intel Aero repository

1. Connect Intel Aero to the network with internet access (using Ubuntu's network manager)
2. Open a terminal
3. Add the Intel Aero repository (and key) to your sources:

```
echo 'deb https://download.01.org/aero/deb xenial main' | sudo tee /etc/apt/sources.list.d/intel-aero.list
wget -qO - https://download.01.org/aero/deb/intel-aero-deb.key | sudo apt-key add -
sudo apt-get update
sudo apt-get upgrade
sudo apt-get -y install gstreamer-1.0 libgstreamer-plugins-base1.0-dev libgstrtspserver-1.0-dev gstreamer1.0-vaapi gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-libav ffmpeg v4l-utils python-pip
sudo pip install pymavlink
sudo apt-get -y install aero-system
sudo reboot
```

## Intel Aero Maintenance tools

### QGroundControl

QGroundControl is required to calibrate the Flight Controller and useful to pilot the drone.

When you're using Yocto, the drone is a DHCP server (and WiFi hotspot). So the drone knows the IP of your laptop and is able to send the MAVLink telemetry feed to the right IP. But if you're using Ubuntu, you are probably connected to the same network with variable IPs. Get your laptop IP and run on Aero:
1. `sudo mkdir /etc/mavlink-router/config.d`
2. create this file: `sudo gedit /etc/mavlink-router/config.d/qgc.conf` and fill it with:

```
[UdpEndpoint wifi]
Mode = Normal
Address = 192.168.1.147
```
(my laptop's IP is 192.168.1.147 but your IP will be different)
3. restart the router: `sudo systemctl restart mavlink-router`
4. launch QGroundControl on your laptop. It should receive automatically the telemetry feed from the drone.
5. follow the setup procedure to calibrate your Flight Controller, unless you've done it already.

### Get Version

```
sudo aero-get-version.py
```
will return BIOS, FPGA and OS information, but not yet the Flight Controller version. We'll update the tool very soon.
Airmap is not installed yet, so version not reported is expected.

### BIOS

To flash the BIOS that's part of the Aero repo, type

```
sudo aero-bios-update
sudo reboot
```

to flash the latest BIOS release from the [Intel Download Center] (https://downloadcenter.intel.com/download/27399/Intel-Aero-Platform-for-UAVs-Installation-Files), download the RPM and extract it.

```
  rpm2cpio aero-bios-01.00.16-r1.corei7_64.rpm | cpio -idmv
  sudo mv BIOSUPDATE.fv /boot
  sudo reboot
```

### FPGA

Similar to Yocto: `sudo jam -aprogram /etc/fpga/aero-rtf.jam` (`aero-rtf.jam` if you are using the RTF drone, `aero-compute-board.jam` if you are using the board only).

### Flight Controller

```
cd /etc/aerofc/px4/
sudo aerofc-update.sh nuttx-aerofc-v1-default.px4
```

## Intel RealSense SDK

If you have the Intel Aero Ready To Fly Drone, you have Intel RealSense R200 Camera included.

Different libraries: The R200 requires the `legacy` branch of the SDK, and D435 the main branch.

Different kernel drivers: the kernel driver for Intel RealSense R200 is already included in the Intel Aero repository. Nothing else to do. Do NOT try to install the module from the SDK or with snap.

### Intel RealSense SDK - legacy (for R200)
```
cd
sudo apt-get -y install git libusb-1.0-0-dev pkg-config libgtk-3-dev libglfw3-dev cmake
git clone -b legacy --single-branch https://github.com/IntelRealSense/librealsense.git
cd librealsense
mkdir build && cd build
cmake ../ -DBUILD_EXAMPLES=true -DBUILD_GRAPHICAL_EXAMPLES=true
make
sudo make install
```
### Intel Camera Streaming Daemon

Camera streaming daemon gets installed with `aero-system`.
To verify, check:
```
systemctl status csd
```
You should see something like:
```
csd.service - Camera Streaming Daemon
   Loaded: loaded (/lib/systemd/system/csd.service; disabled; vendor preset: enabled)
   Active: active (running) since Wed 2017-11-08 17:28:00 PST; 6min ago
 Main PID: 14616 (csd)
   CGroup: /system.slice/csd.service
           └─14616 /usr/bin/csd

Nov 08 17:28:00 frelon systemd[1]: Started Camera Streaming Daemon.
Nov 08 17:28:00 frelon csd[14616]: Could not open conf file '/etc/csd/main.conf' (No such file or directory)
Nov 08 17:28:01 frelon csd[14616]: Failed to connect to Mir: Failed to connect to server socket: No such file or directory
Nov 08 17:28:01 frelon csd[14616]: Unable to init server: Could not connect: Connection refused
Nov 08 17:28:01 frelon csd[14616]: (gst-plugin-scanner:14618): Clutter-CRITICAL **: Unable to initialize Clutter: Could not initialize
Nov 08 17:28:01 frelon csd[14616]: (gst-plugin-scanner:14618): Clutter-Gst-CRITICAL **: Unable to initialize Clutter
Nov 08 17:28:02 frelon csd[14616]: AVAHI START
```
We have started CSD without configuring it manually: it will find the video devices automatically and propose feeds. As a test, use VLC on your computer and open the video2 feed `rtsp://192.168.1.4:8554/video13`. On my network, Intel Aero has IP 192.168.1.4 but yours will be different of course (type `ifconfig wlp1s0` to see your IP).

For more understanding, visit [https://github.com/01org/camera-streaming-daemon](https://github.com/01org/camera-streaming-daemon)

### Intel Optical Flow
Optical flow requires you buy and plug a vertical range meter, like a vertical laser. For more details, visit: [https://github.com/intel-aero/aero-optical-flow](https://github.com/intel-aero/aero-optical-flow).

Optical Flow also requires a software running on linux. This package gets installed with `aero-system`. Optical flow service is disabled by default, enable using:
```
systemctl enable aero-optical-flow
systemctl start aero-optical-flow
```
_**Note:** Do **NOT** enable the service if you have not installed the vertical range finder (laser)._

Check status:
```
systemctl status aero-optical-flow
```
The output would look like:
```
aero-optical-flow.service - Aero optical flow
   Loaded: loaded (/lib/systemd/system/aero-optical-flow.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2018-01-03 11:32:35 IST; 1s ago
 Main PID: 2629 (aero-optical-fl)
   CGroup: /system.slice/aero-optical-flow.service
           └─2629 /usr/bin/aero-optical-flow

Jan 03 11:32:35 aero-CherryTrail systemd[1]: Started Aero optical flow.
```

## Checks

### Kernel version

Check the kernel version with `uname -a`, you should see:
```
Linux frelon 4.4.76-aero-1.2 #1 SMP PREEMPT Mon Nov 6 19:42:57 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```
_**Hint:** It should have aero in the kernel name._

### Number of video devices

To check if the camera drivers are correctly installed, list the video devices with
```
ls /dev/video*
```
You should see a list like `/dev/video0  /dev/video1  /dev/video2 ...` up to 13. If less than 13, there is a problem.

_**Hint:** If you see only 3 video devices, it means you booted the wrong kernel._

The order of devices is also important. Check the details with
```
sudo v4l2-ctl --list-devices
```

You should see a long listing, including:
```
Intel RealSense 3D Camera R200 (usb-0000:00:14.0-4):
	/dev/video11
	/dev/video12
	/dev/video13
```
R200 should have the devices 11, 12, 13.

### MAVLink router

Check if the system is listening for MAVLink messages on port 5760 thanks to MAVLink router (for the Ready To Fly Drone only):
```
netstat -l|grep 5760
```
You should see:
```
tcp        0      0 *:5760                  *:*                     LISTEN     
```
If not, there's a problem.

_**Hint:** You may have booted the wrong kernel, or something else is wrong._

### RealSense

_**Test:** run `cpp-enumerate-devices` to see if your camera is detected._

On the Ready To Fly Drone, you should see:
```
Device 0 - Intel RealSense R200:
 Serial number: 2481009843
 Firmware version: 1.0.71.06
 USB Port ID: 2-4
 Camera info:
    DEVICE_NAME         : 	Intel RealSense R200
    DEVICE_SERIAL_NUMBER: 	2481009843
    CAMERA_FIRMWARE_VERSION: 	1.0.71.06
    CAMERA_TYPE         : 	PRQ-Ready
    OEM_ID              : 	OEM None
    ISP_FW_VERSION      : 	0x0
    CONTENT_VERSION     : 	12
    MODULE_VERSION      : 	4.2.5.0
    IMAGER_MODEL_NUMBER : 	31
    CALIBRATION_DATE    : 	2014-07-04 08:18:35 UTC
    EMITTER_TYPE        : 	Laser Driver 3
    FOCUS_VALUE         : 	0
    LENS_TYPE           : 	Newmax 58.9 x 45.9 degs in VGA
    3RD_LENS_TYPE       : 	Newmax 71.7 x 44.2 degs in 1080p
    LENS_COATING__TYPE  : 	Visible-light block / IR pass 43 nm width
    3RD_LENS_COATING_TYPE: 	IR coating
    NOMINAL_BASELINE    : 	70 mm
    3RD_NOMINAL_BASELINE: 	58 mm
 Supported options:                                    min        max       step  default  
    COLOR_BACKLIGHT_COMPENSATION                       : 0    ... 4           1     1         
    COLOR_BRIGHTNESS                                   : 0    ... 255         1     56        
...
```

To see the output of a camera, use the SDK tools.

_**Examples:**_
* `cpp-tutorial-1-depth` (command line)
* `cpp-capture` (graphical).

### CSD

Check the port 8554 is listening with `netstat -l|grep 8554`.

You should see:
```
tcp        0      0 *:8554                  *:*                     LISTEN
```

*You now have a working Ubuntu setup working on Intel Aero.*

# 6. Networking: WiFi

### With Yocto

By default, Intel Aero is shipping with Yocto installed and is proposing an **access point** with SSID _**Aero - < MAC >**_ (where < MAC > is the MAC address of Intel Aero) and password _**1234567890**_.

The default IP of Intel Aero is 192.168.8.1.

You can **ssh** to this IP address with login _**root**_ and _**no**_ password.

If you want Intel Aero to join a WiFi network in client mode, here is the procedure:
```
nmcli c down hotspot
nmcli c modify hotspot connection.autoconnect no
# scan wifi networks
nmcli dev wifi
# connect
nmcli dev wifi connect <network name> password <password>
```

### With Ubuntu

 If you install Ubuntu manually on Intel Aero, it will work in client mode by default. You can join a WiFi network using the typical network manager tools.

### Networking: Access Point Wifi

By default, Intel's Yocto for Aero is broadcasting a WiFi access point SSID _**Aero - < MAC >**_ and password _**1234567890**_.

The default IP of Intel Aero is 192.168.8.1.

You can **ssh** to this IP address with login _**aero**_ and password _**root**_.

Launch a terminal and connect to aero via SSH as follows:

```
ssh root@192.168.8.1
```

After connecting to Aero it's advised to change the passkey and ssid by using the following commands:
```
nmcli c modify hotspot 802-11-wireless-security.psk <new passkey>
nmcli c modify hotspot 802-11-wireless.ssid <new ssid>
```

Need to restart NetworkManager with below command to take effect of new SSID
```
systemctl restart NetworkManager
```
You can also edit the configuration file in /etc/NetworkManager/system-connections/hotspot.

On macOS and Linux it's possible to use the `link-local` name rather than fixed IP 192.168.8.1. Refer to the documentation of your distro on how to enable it; it's enabled by default on Fedora and [Archlinux's documentation](https://wiki.archlinux.org/index.php/avahi#Hostname_resolution)
works on several Linux distributions and macOS:
```
ssh root@intel-aero.local
```

### Networking: Internet Access

In order to have internet access on Aero board it's possible to switch it to client mode so it connects to your Access Point instead of being an Access Point itself. We'll use the standard connman.

Here's the procedure:
1. First you need to disable AP mode which is the default one:


```
nmcli c down hotspot
Connection 'hotspot' successfully deactivated (D-Bus active path: ...
nmcli c modify hotspot connection.autoconnect no
```
2. Scan WiFi networks with nmcli dev wifi command.

_**Example:**_
```
nmcli dev wifi
   ABCD-C072-5         Infra  157   54 Mbit/s  79      ***   WPA1 WPA2
   MYOFFICEWLAN        Infra  44    54 Mbit/s  75      ***   WPA1 WPA2 802.1X
   LabWLAN             Infra  44    54 Mbit/s  75      ***   WPA2 802.1X
   AndroidHotspot      Infra  44    54 Mbit/s  74      ***   WPA2 802.1X
   OfficeWLAN2         Infra  1     54 Mbit/s  74      ***   WPA2 802.1X
```
3. Connect to one network by giving the desired ssid and passkey.

From the _**example**_ above we can connect to ABCD-C072-5 with:
```
nmcli dev wifi connect ABCD-C072-5 password 12mysecretpassword34
```
_**Note:** With this configuration it will try to autoconnect to this network every time you boot Intel Aero board._

### Switch WiFi back to Access Point mode

In case you switched the interface to work in client (e.g. to connect to the Internet) mode you may get it back in AP mode with the following commands:
```
nmcli c up hotspot
Connection successfully activated (D-Bus active path: ...
nmcli c modify hotspot connection.autoconnect yes
```

### WiFi factory reset

If you changed the WiFi configuration and don't remember the passkey, or would just like to reset to the initial configuration given by the OS, you can use the following commands:

```
rm /etc/sysconfig/networkmanager
reboot
```

The board will reboot and re-configure itself with default configuration.

### UART speed between Compute Board and Flight Controller

This changes the baudrate that communicates between the Intel Aero Flight Controller and the Intel Aero Computer Board. In order to get a reliable communication there are some changes on the Linux side that's done here and will be part of a new version in future. Without upgrading the distro/packages these are the equivalent instructions:

1. Add this line to the `/lib/systemd/system/mavlink-router.service` under `[Service]` section:
```
ExecStartPre=/bin/ -c 'echo 4 > /sys/class/tty/ttyS1/rx_trig_bytes'
```
2. Change baudrate of the uart port for mavlink-router in `/etc/mavlink-router/main.conf`
3. Change baudrate of the update script in `/usr/sbin/aerofc-update.sh`

# 7. Video streaming

### RTSP Streaming with the Camera Streaming Daemon
Camera Streaming Daemon will stream the video feeds over the network with RTSP, a standard protocol. RTSP video can be received by QGroundControl, VLC and typical video players on most platforms. It includes the RGB sensor of Intel RealSense, but also the black and white downward facing camera, the depth and Infrared sensors.
* If you're using the Yocto build provided by Intel (v1.6 or newer), the Camera Streaming Daemon is already installed and configured.
* If you're using Ubuntu, you'll need to install it manually.

On Yocto (v1.6 minimum), Intel Aero is proposing 5 RTSP video feeds:
* RealSense R200, HD camera: `rtsp://192.168.8.1:8554/video13`
* RealSense R200, depth sensor: `rtsp://192.168.8.1:8554/rsdepth`
* RealSense R200, infrared first camera: `rtsp://192.168.8.1:8554/rsir`
* RealSense R200, infrared second camera: `rtsp://192.168.8.1:8554/rsir2`
* Bottom facing black and white global shutter: `rtsp://192.168.8.1:8554/bottom`

On Ubuntu, Intel Aero is proposing 5 RTSP video feeds:
* RealSense R200, HD camera: `rtsp://AERO_IP_ON_YOUR_NETWORK:8554/video13`
* RealSense R200, depth sensor: `rtsp://AERO_IP_ON_YOUR_NETWORK:8554/rsdepth`
* RealSense R200, infrared first camera: `rtsp://AERO_IP_ON_YOUR_NETWORK:8554/rsir`
* RealSense R200, infrared second camera: `rtsp://AERO_IP_ON_YOUR_NETWORK:8554/rsir2`
* Bottom facing black and white global shutter: `rtsp://AERO_IP_ON_YOUR_NETWORK:8554/bottom`

### RTSP video streaming from Yocto using QGroundControl

Procedure:
1. Following the calibration procedure, you should have QGroundControl already installed and connected to Intel Aero.
2. Make sure you have disabled any sort of firewall/VPN on your Host PC.
3. Launch QGroundControl on Host PC. It will open into the flight path mode.
4. You need to click on the QGC Icon to see the configuration and scroll down:

    Video settings: **RTSP**

    URL on Yocto: `rtsp://192.168.8.1:8554/video13`

    URL on Ubuntu: `rtsp://AERO_IP_ON_YOUR_NETWORK:8554/video13`

5. Go back to the flight view. The bottom left view should show the video.

_**Note:** It may be possible that video stream is blocked by firewall/VPN. In that case, you may want to disable the firewall/VPN on the host PC._

### RTSP video streaming from Yocto using Gstreamer

You can access the RTSP feeds from QGroundControl or any compatible video player.

If you have a linux PC, try _**gstreamer**_ with the `latency=0` setting to receive video from CSD running on Intel Aero:
```
gst-launch-1.0 rtspsrc location=rtsp://192.168.8.1:8554/bottom latency=0 ! decodebin ! autovideosink
```

_**Note:** It may be possible that video stream is blocked by firewall/VPN. In that case, you may want to disable the firewall/VPN on the host PC._

## RTSP video streaming from Yocto using VLC
Using Yocto, open the network video `rtsp://192.168.8.1:8554/video13`.

_**Note:** It may be possible that video stream is blocked by firewall/VPN. In that case, you may want to disable the firewall/VPN on the host PC._

### Network streaming with gstreamer
You can also use _**gstreamer**_ directly to encode (using the GPU) and send video over the network. This is not as easy to use as CSD, but it is very flexible.

Here is an _**example using Ubuntu**_, launching a gstreamer command to:
* get video from the **RGB sensor of the R200 camera** `(/dev/video13)`
* select a VGA resolution at **15fps**
* use the onboard hardware accelerated **h264 encoder**
* send it to my **laptop's IP** (192.168.1.147 and I have a RTSP software ready to receive the video on this machine).

```
sudo gst-launch-1.0 v4l2src  device=/dev/video13 do-timestamp=true ! video/x-raw, format=YUY2, width=640, height=480, framerate=15/1 ! autovideoconvert ! vaapih264enc ! rtph264pay !  udpsink host=192.168.1.147 port=5600
```
_**Note:** This is an advanced topic posted for reference, your mileage may vary._

## Linux setup
Let's focus on the Intel RealSense R200 camera included in the Intel Aero Ready-To-Fly Drone. A single R200 camera has 3 feeds. The first two are for depth, the third for RGB. Other than that, it's a standard video device on linux, and the R200 feeds are `/dev/video[11-13]`.

### Take a RGB photo
gstreamer is a great tool to work with video devices:
```
sudo gst-launch-1.0 v4l2src device=/dev/video13 num-buffers=1 ! jpegenc ! filesink location="rs.jpg"
identify rs.jpg
```
should see a photo file created with the following FullHD specs:
```
rs.jpg JPEG 1920x1080 1920x1080+0+0 8-bit sRGB 50.4KB 0.000u 0:00.000
```

### Take a photo with the black and white camera

You can access the camera with `/dev/video2` but can't stream this device with gstreamer. Instead, you can download a sample code from https://github.com/intel-aero/sample-apps/tree/master/capturev4l2
to see how you can capture a few frames to raw format, then output jpg frames:
```
git clone https://github.com/intel-aero/sample-apps.git
cd sample-apps/capturev4l2/
make
C=10 INPUT=1 MODE=PREVIEW ./capture_v4l2 --userp -d /dev/video2
ffmpeg -f rawvideo -s 640x480 -pix_fmt yuv420p -i Image-video<*> test.jpg
```
Typical use for the camera include [optical flow](https://github.com/intel-aero/aero-optical-flow) and [Camera Streaming Daemon](https://github.com/intel/camera-streaming-daemon/blob/master/src/stream_aero_bottom.cpp)

_**Notes:**_
* Sensor is an [Omnivision OV7251 CMOS](http://www.ovt.com/sensors/OV7251).
* Driver is not fully v4l2 compliant. Using opencv or gstreamer with the camera device node to grab camera frames will not work. The sequence of operation as shown in the sample needs to be done. Polling on device file descriptor times out after certain interval. This requires the device to be restarted.
* Works on Yocto and Ubuntu
* _How can I use the camera using GStreamer?_

 Capture frame from VGA Camera as shown in examples. Captured frames can be used by appsrc element of gstreamer. V4L2 Source plugin of gstreamer cannot be used for capturing frames from VGA Camera
* _Is there any APIs to use the camera?_

  System Calls and IOCTLs as shown in examples must be used
* _Which device corresponds to the camera?_

  `/dev/video2`

### Take a depth/infrared photo

You can use the sample tools from the Intel RealSense SDK
```
cpp-headless
```
It will create 4 files from the R200 Camera: color, depth, infrared left, infrared right.
```
There are 1 connected RealSense devices.

Using device 0, an Intel RealSense R200
    Serial number: 2481009843
    Firmware version: 1.0.71.06
Writing cpp-headless-output-DEPTH.png, 480 x 360 pixels
Writing cpp-headless-output-COLOR.png, 640 x 480 pixels
Writing cpp-headless-output-INFRARED.png, 480 x 360 pixels
Writing cpp-headless-output-INFRARED2.png, 480 x 360 pixels
wrote frames to current working directory.
```

### Record video
Let's use vaapih264enc to take advantage of the hardware accelerated video encoding:
```
sudo gst-launch-1.0 -e v4l2src device=/dev/video13 num-buffers=2000 ! autovideoconvert format=i420 width=1920 height=1080 framerate=30/1 ! vaapih264enc rate-control=cbr tune=high-compression ! qtmux ! filesink location=encoded_video.mp4
ffmpeg -i encoded_video.mp4
```
You should see as output the specs of the FullHD video encoded:
```
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'encoded_video.mp4':
  Metadata:
    major_brand     : qt  
    minor_version   : 537199360
    compatible_brands: qt  
    creation_time   : 2017-11-09 02:01:08
  Duration: 00:00:05.70, start: 0.000000, bitrate: 11712 kb/s
    Stream #0:0(eng): Video: h264 (High) (avc1 / 0x31637661), yuv420p, 1920x1080 [SAR 1:1 DAR 16:9], 11705 kb/s, 30 fps, 30 tbr, 3k tbn, 60 tbc (default)
    Metadata:
      creation_time   : 2017-11-09 02:01:08
      handler_name    : VideoHandler
```

### Control the camera settings
On Ubuntu, we'll use the standard V4L2 tools: ``sudo v4l2-ctl --list-devices`` :
```
Intel RealSense 3D Camera R200 (usb-0000:00:14.0-4):
	/dev/video11
	/dev/video12
	/dev/video13
```

List possible controls with ``sudo v4l2-ctl --list-ctrls -d /dev/video13``:
```
                     brightness (int)    : min=0 max=255 step=1 default=56 value=56
                       contrast (int)    : min=16 max=64 step=1 default=32 value=32
                     saturation (int)    : min=0 max=255 step=1 default=128 value=128
                            hue (int)    : min=-2200 max=2200 step=1 default=0 value=0
 white_balance_temperature_auto (bool)   : default=1 value=1
                          gamma (int)    : min=100 max=280 step=1 default=220 value=220
                           gain (int)    : min=0 max=256 step=1 default=32 value=32
           power_line_frequency (menu)   : min=0 max=2 default=0 value=0
      white_balance_temperature (int)    : min=2000 max=8000 step=1 default=6500 value=6500 flags=inactive
                      sharpness (int)    : min=0 max=7 step=1 default=0 value=0
         backlight_compensation (int)    : min=0 max=4 step=1 default=1 value=1
                  exposure_auto (menu)   : min=0 max=3 default=3 value=3
              exposure_absolute (int)    : min=1 max=666 step=1 default=1 value=1 flags=inactive
```
And change a setting, as a test:
```
sudo v4l2-ctl -d /dev/video13 -c brightness=56
```

### gstreamer graphical output
If you are connected to Intel Aero graphically with a HDMI cable, you can run a graphical tool.

The simplest tool is _**gstreamer**_:
```
sudo gst-launch-1.0 v4l2src device=/dev/video13 ! xvimagesink
```
You can play around with the settings, like changing to VGA, 30fps:
```
sudo gst-launch-1.0 v4l2src device=/dev/video13 ! video/x-raw,width=640,height=480,framerate=30/1  ! xvimagesink
```

### guvcview
For a more user friendly tool, install guvcview with ```sudo apt install guvcview``` on Ubuntu.

Remember, there's 3 feeds for the R200 Camera. Select the third one for the color output.

### Camera choice
To plug another camera, you have to consider:
* Type of IO and the drivers.
* Possible ports, including: SPI, CAN, USB2 or USB3.
* Intel Aero is linux based, you should target cameras with drivers for these linux distributions. Typically this means UVC, USB Video Class.

_**Note:** most action cameras do NOT have linux drivers publicly available._

### Connecting and first camera shot
Here's an example with e-con systems [See3Cam USB3 camera](https://www.e-consystems.com/UltraHD-USB-Camera.asp).
It is not an Intel recommended/supported hardware. The documentation is provided as a typical example of linux camera integration.

Plug the camera on the USB3 with the USB3-OTG cable (sold by Intel as an accessory).

_**Note:** We're working with Ubuntu 16.04.3 in this example._

Run ``lsusb``:
```
Bus 002 Device 002: ID 8086:0a80 Intel Corp.
Bus 002 Device 003: ID 2560:c1d0  
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```
There's a new line (2nd line) corresponding to the camera, showing it's correctly connected as a USB device.

Run ``dmesg``:
```
...
[ 2580.288919] usb 2-1: new SuperSpeed USB device number 3 using xhci_hcd
[ 2580.302449] usb 2-1: LPM exit latency is zeroed, disabling LPM.
[ 2580.305441] uvcvideo: Found UVC 1.00 device See3CAM_CU130 (2560:c1d0)
[ 2580.307009] uvcvideo 2-1:1.0: Entity type for entity Extension 3 was not initialized!
[ 2580.307023] uvcvideo 2-1:1.0: Entity type for entity Processing 2 was not initialized!
[ 2580.307032] uvcvideo 2-1:1.0: Entity type for entity Camera 1 was not initialized!
[ 2580.307580] input: See3CAM_CU130 as /devices/pci0000:00/0000:00:14.0/usb2/2-1/2-1:1.0/input/input5
[ 2580.310773] hid-generic 0003:2560:C1D0.0001: hiddev0: USB HID v1.11 Device [e-con systems See3CAM_CU130] on usb-0000:00:14.0-1/input2
```
The camera is recognized as a UVC device, as expected (and as a HID device, too).

Run ``ls /dev/video*`` to list the video devices:
```
/dev/video0  /dev/video1  /dev/video2  /dev/video3 ...
```
Before connecting the camera, we could see video0,1,2 corresponding to the Intel RealSense R200 camera (depth and RGB sensors). Plus other cameras listed. We now have a new video device `/dev/video14`. Depending on how the device is found by linux, it may have a different index number. Use `sudo v4l2-ctl --list-devices` to get the details about the camera-number association.

_**Note:** This number may change at the next boot._

This means that the camera is seen as a video device by linux and can be used for video or pictures.

To capture a frame from the `/dev/video14` device, gstreamer is incredibly versatile:
```
sudo gst-launch-1.0 v4l2src device=/dev/video14 num-buffers=1 ! jpegenc ! filesink location="test.jpg"
```

A 13Mpixels, a 2.4 Mb jpg file is created.

Here are the ``identify test.jpg`` results:
```
test.jpg JPEG 4224x3156 4224x3156+0+0 8-bit sRGB 2.398MB 0.000u 0:00.000
```
The quality of the photo may not be optimal, as we have not played with the camera settings yet.

### Camera settings
As with Intel RealSense R200, you can use guvcview and v4l2-ctl.
`sudo v4l2-ctl --list-devices`:
```
See3CAM_CU130 (usb-0000:00:14.0-1):
	/dev/video14
...
Intel RealSense 3D Camera R200 (usb-0000:00:14.0-4):
	/dev/video11
	/dev/video12
	/dev/video13
```

List possible controls with ``sudo v4l2-ctl --list-ctrls -d /dev/video14``:
```
                     brightness (int)    : min=-15 max=15 step=1 default=0 value=0
                       contrast (int)    : min=0 max=30 step=1 default=10 value=10
                     saturation (int)    : min=0 max=60 step=1 default=16 value=16
 white_balance_temperature_auto (bool)   : default=1 value=1
                          gamma (int)    : min=40 max=500 step=1 default=220 value=220
                           gain (int)    : min=0 max=100 step=1 default=0 value=0
      white_balance_temperature (int)    : min=1000 max=10000 step=50 default=5000 value=5000 flags=inactive
                      sharpness (int)    : min=0 max=127 step=1 default=16 value=16
                  exposure_auto (menu)   : min=0 max=3 default=1 value=0
              exposure_absolute (int)    : min=1 max=10000 step=1 default=156 value=312 flags=inactive
                   pan_absolute (int)    : min=-648000 max=648000 step=3600 default=0 value=0
                  tilt_absolute (int)    : min=-648000 max=648000 step=3600 default=0 value=0
                  zoom_absolute (int)    : min=100 max=800 step=1 default=100 value=100
```
And change one, as a test:
```
sudo v4l2-ctl -d /dev/video14 -c brightness=56
```

# 8. Networking: LTE

_**Note:** You will need firmware 1.6.0 or newer with **ALL PARTS FLASHED** (BIOS, FPGA, ...)._

With the addition of LTE wireless connectivity, the Intel Aero platform enables a broader range of use cases. Your drone can now be controlled remotely anywhere there is a cellular signal and data can be transmitted over the LTE wireless link to provide continuous connectivity.

Starting with version 1.5.1, LTE modem capabilities are enabled via the M.2 connector located on the top-side of the Intel Aero Compute Board. The v1.5.1 release requires updates to both the BIOS and BSP. The updated BSP contains new modem management software and the updated BIOS enables the M.2 connector.

We recommend using LTE modems based on Intel’s XMM 7160 chipset. Products include the Telit LN930 and Sierra Wireless AirPrime EM7345 which are certified for use across the globe. Please refer to the modem manufacturer’s product literature for  specific capabilities.

_**Note:** It is the pilot’s responsibility to obtain the necessary permits from both the government regulatory agencies as well as the cellular service providers._

## Installation

### Installation Overview

1. Purchase all necessary items listed in the next section "Detailed Hardware Installation"  
2. ~Repurpose the original WiFi antennas for use with the LTE modem~  
3. Insert the LTE modem into the M.2 connector  
4. Secure the LTE modem using the 2mm hex nut  
5. Insert the SIM card into the SIM card tray and slide it into the SIM slot slowly  
6. Download and install v1.5.1 software update  
  * Update the BIOS  
  * Update the BSP  
7. Configure Modem Manager to establish connectivity

### Detailed Hardware Installation
To preform the LTE modem hardware installation, you will need to purchase the four items shown in Figure 1:
* LTE modem module
* Nano SIM card
* Tray to hold the nano SIM card
* 2mm hex nut

The LTE modem will be installed into the M.2 connector which is located on the top side of the Aero Compute Board, adjacent to the 80 pin I/O Expansion Connector. Use the 2mm hex nut to secure the LTE modem module to the mounting post near the heat sink. Insert modem into M.2 and secure it with the 2mm hex nut  

### Connecting the Antennas
When installing the LTE modem, two antennas are required for proper operation. Both the Intel Aero Ready to Fly Drone and the Intel Compute Board are shipped with two WiFi antenna. These two antennas appear as "wings" attached to the sides of the plastic enclosure. The WiFi antennas will be re-purposed for use with the LTE modem.

~If WiFi communication is not needed, then carefully disconnect the antennas micro SMA connectors from the WiFi module on the bottom of the Aero Compute Board and connect them to the two micro SMA connectors on the LTE modem. These are very small connectors. The antennas will "pop" off. Be gentle and careful when removing them. After connecting them to the LTE modem, verify a good connection is made by rotating the antenna on the micro SMA connector to verify that it can rotate freely. If the antenna does not rotate freely, then the antenna is not properly seated on the micro SMA connector.

If both WiFi and LTE modem functions are required, we recommend replacing the Intel Aero Ready to Fly Drone's WiFi antennas by following our "[Instructable](http://www.instructables.com/id/Intel-Aero-Drone-Extending-Wifi-Range/)" which will extend the range of the drone's WiFi radio. This will allow you to then use the original WiFi antennas for the LTE modem as described above.

### Installing the nano SIM card
The SIM card slot is located on the bottom side of the Compute Board underneath the 80 pin I/O Expansion Connector. You will notice that when the SIM card is inserted, the SIM tray sticks out more than allowed by the enclosure which prevents the  Compute Board from being fully seated. To accommodate the SIM tray, the opening for the SIM card must be widened. This  requires a modification to the bottom half of the plastic enclosure.

Using a tool of your choice (X-Acto knife works well), make an opening in the plastic enclosure for the SIM tray that is approximately 16mm x 5mm. This will allow enough room for the SIM tray to sit comfortably within the enclosure and also allow easy access for extracting the SIM card.

## Software Configuration

### Install Necessary Software
Both the BSP and BIOS must be updated to at least the following versions
* BSP v1.5.1
* BIOS v01.00.13  

_**Note:** The Aero Ready to Fly Drone will also require the flight controller firmware to be updated as well._

### Verify Hardware is Recognized
After updating, open up a SSH terminal by connecting to Aero's WiFi Access Point. Modem Manager (replaces Connection Manager on previous Aero  BSP releases) should automatically detect the installed LTE modem and enumerate it as Modem 0. Query the modem info below.

    # mmcli -m 0
<img src="https://raw.githubusercontent.com/intel-aero/Documents/master/doc_photos/lte_status.png?raw=true" /> <br>

If the LTE modem is not detected, please double check that the modem is seated securely and the SIM card is properly inserted. Take precautions as the SIM card can potentially slide out due to airframe vibrations while in flight.

This is a good point to verify that the SIM card is properly recognized and is not locked. This can be found in the Status section reported above. If the SIM card is locked, contact your carrier for unlock instructions.

    lock: `none`
    state: `registered`

_**Note:** An APN must be set correctly to access the public internet._

 Below are a few carriers in the U.S. and their corresponding APN.
* AT&T - "phone"
* Verizon - "vzwinternet"
* Tmobile - "fast.t-mobile.com"
* Sprint - "cinet.spcs"

APN name can be updated using the command below.

    # nmcli con modify modem gsm.apn <name>

### Verify Connectivity
Reboot the system so that the configuration will take effect. With the previous "mmcli -m 0" command, find the bearer number from the output. Execute the following to show the details of the IP addresses.

    # mmcli -b <bearer number>

Below you can see the device "cdc-wdm0" with connection name "modem" is in the connected state.

    # nmcli device

Verify internet connectivity by pinging your favorite website.

    # ping http://nam.ece.upatras.gr

## QGroundControl over LTE
By default, QGC communicates directly with Aero when connected to Aero's  WiFi Access Point. However, when the drone is on LTE, a direct connection is non-trivial as both the drone and PC can be behind multiple layers of NATs and firewalls.

There are several ways to circumvent this. In the options described below, a cloud server is used to help route packets between the drone and PC. These servers are inexpensive and easy to set up, like Amazon’s [AWS](https://aws.amazon.com/) and Google’s [Compute Engine](https://cloud.google.com/compute/).

### Option 1: Server running mavlink-router
On the Intel Aero Ready to Fly Drone (aka Aero RTF), mavlink-router runs locally to handle routing packets between the flight controller and different IP endpoints. But we can also deploy another instance of mavlink-router in the cloud to handle routing just IP traffic. Both the drone(s) an QGC are then connected to this cloud based mavlink-router and communication is established. One benefit of using this method is flight logs are stored automatically in the cloud, so in case of fly-aways a  copy of the logs can be retrieved. This method also scales well with one-to-many use cases.

<img src="https://raw.githubusercontent.com/intel-aero/Documents/master/doc_photos/lte_cloud_chart.png?raw=true" />

1. Obtain access to a Linux based cloud server and note its external IP
2. Configure the server's firewall to allow TCP traffic on port 5760
3. Access the server through a terminal. Build and deploy [mavlink-router](https://github.com/01org/mavlink-router)
4. On the Aero RTF, edit /etc/mavlink-router/main.conf and append the following line:
    [TcpEndpoint LTE]
    Address = XX.XX.XX.XX (replace with server ip)
    Port = 5760
5. Reboot the drone. Mavlink-router will automatically try to connect to the server
6. In QGC, under the Comm Links tab add an additional TCP connection with the server IP and port number
7. Click connect. You should now be communicating over LTE!

### Option 2: Server with SSH tunneling
This method is simpler to deploy and requires no additional software on the server. The server effectively acts as a bridge between the PC and drone.Steps below assumes AWS cloud service.

1. Obtain access to a Linux based cloud server and note its external IP
2. Configure the server's firewall to allow TCP traffic on port 5760

On PC execute:

    # ssh -i .keys/AmazonCloud.pem -L 5760:localhost:5760 -N <AWS User>@<AWS Instance>.compute.amazonaws.com
On Aero RTF execute:

    # ssh -i /home/root/.keys/AmazonCloud.pem -R 5760:localhost:5760 -N -f -T <AWS User>@<AWS Instance>.compute.amazonaws.com>/var/log/aero.log 2>&1

# 9. SysAdmin: Docker
If you are using Yocto but would like to deploy a separate OS on top of Yocto, you can use a docker image of this OS. But remember you can also use Ubuntu and install it manually on Intel Aero. Docker is an advanced and complex topic, use at your risk.

Intel Aero is shipped with a Yocto Linux build: Yocto is great as an embedded build system for professionals, including in the aeronautics sector where it is widely used, but it is not very friendly for rapid prototyping: Some may prefer an OS like Ubuntu.

There are several possible mechanisms to create this separation and choice of OS for deployment. On Intel Aero we support containers. A container is a set of resources (cpu, memory ...) allocated to a set of processes. It’s a lot like a virtual machine but much lighter and efficient as it works at the process level and not the OS level.
It is very popular in the server world, where the toolbox Docker is allowing you to create development environments on your station (Linux, Mac, Windows) and migrate them to servers seamlessly.
To summarize, containers behave like a virtual machine but also like a way to package and deploy apps.

Here's how to get started:

The `docker` command line tool allow the management of containers.
The `run` command with parameters `--interactive (-i)` and `--tty (-t)` opens a console session within
the container environment. The run command requires a docker image name in order to create
a new container. The following example shows the execution of a container based on Ubuntu*
16.04:

```
docker run --privileged -it ubuntu:16.04
```

The parameter `--privileged` grants full access to host devices.
Notice the first execution of run will download a docker image to satisfy the request for a new
container.
After that, an Ubuntu* 16.04 environment will be available, providing commands such as apt for
package management, etc.
To exit a container, simply use Ctrl+d or execute the exit ​command. When you exit a container,
all of the modifications you have done inside *will be lost* unless you explicitly save the state
(more info below).
To find a list of docker images available, check https://hub.docker.com/search/.
Useful commands:
* `docker images` will list existing docker images;
* `docker ps` will list the containers currently in execution. The values in column
“CONTAINER ID” can be used to execute other docker commands
* `docker commit <CONTAINER ID>` will save the existing state of a running container
into a new image, this needs to be run outside of a docker instance, so if you are already
inside of one you can ssh back into the drone by running: ssh 172.17.0.1. You can also
have multiple ssh connections to aero, leaving one inside the docker container and
another on the host. The screen command on the host also works, so you can multiplex
a single connection
* `docker exec -it <CONTAINER ID> ` can be used to have multiple terminals inside
the container - or you can install screen inside the container and multiplex a single
terminal. Note the screen shortcuts for screen will conflict if you use both on host and
container

* `docker commit <CONTAINER ID> <CUSTOM_IMG_NAME>` allows you to store the current state of a container. It generates a docker image that can be used for next executions with all customization you did.

* `docker save CUSTOM_IMG_NAME > customimgname.tar` This command exports the docker image named CUSTOM_IMG_NAME to a tar file, allowing that docker image to be transferred to another system, where it can be loaded using `docker load customimgname.tar`.

### Container network connectivity

If you are using Yocto but would like to deploy a separate OS on top of Yocto, you can use a docker image of this OS. But remember you can also use Ubuntu* and install it manually on Intel Aero. Docker is an advanced and complex topic, use at your risk.

To allow docker to forward IPv4 and thus allow a container to communicate with the network, you can choose between:

1. **enable ip_forward by default** so all containers will have access to the network: `sysctl -w net.ipv4.ip_forward=1` in the root console

2. **Keep ip_forward disabled by default** and use the parameter `--net=host` in your `docker run` calls for containers that should have access to the network.

Also, you might want to allow other machines in the **local network to have access to a container**. With `docker run`, you can use either the `-p PORT` parameter, which allows you to specify a port/list of ports to be exposed, or `-P`  which exposes all ports. For more details about these parameters, check [Bind container ports to the host](https://docs.docker.com/engine/userguide/networking/default_network/binding/)


## Docker Network Server

As seen in the previous example, you can instantiate a live image from a downloaded image and modify it on the fly. But you can also define one programmatically.
In this example, I'll build an image based on Ubuntu 16.04, will add openssh-server and set a default password.
I will then instantiate this image with the right settings to listen to a network port on Intel Aero. Users will then be able to access the docker running on Intel Aero from the network with ssh. Check the course to understand the details.

First, create a file called `Dockerfile`:
```
FROM ubuntu:16.04
MAINTAINER Paul Guermonprez <paul.guermonprez@intel.com>
RUN apt-get update && apt-get install -y openssh-server iputils-ping net-tools
RUN mkdir /var/run/sshd
RUN echo 'root:password' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile
CMD ["/usr/sbin/sshd", "-D"]
```

Then launch the creation process:
```
docker build -t "guermonprez/aero-demo" .
```
It will download the Ubuntu* docker, if needed. Then run `apt-get install` and all the commands.
Instantiate the container, listening on port 2222:
```
docker run -d --name aero-demo -h aero-demo -p 2222:22 guermonprez/aero-demo
```
Open a direct  on the image:
```
docker exec -i -t aero-demo
```
Or a ssh connection from your station, first to the Yocto layer:
(change the IP to reflect the drone's IP on YOUR NETWORK)
`ssh root@192.168.0.13`
Then to the docker instance (here ubuntu*):
```
ssh root@192.168.0.13 -p 2222
```

To list container instances, and stop ours:
```
docker ps
docker stop aero-demo
```
List images, and delete our instance and image created:
```
docker images
docker rm aero-demo
docker rmi guermonprez/aero-demo
```

## Docker ROS

We'll run ROS from a reference docker image called `ros`. It is based on Ubuntu.
```
docker run -it --name aero-ros -h aero-ros --privileged ros
```
Then install the necessary packages:
```
apt-get update
apt-get install ros-kinetic-mavros iproute2 ros-kinetic-image-view openssh-client
apt-get install ros-kinetic-librealsense ros-kinetic-realsense-camera
# it may take a while ...
```
Open a second terminal on Intel Aero Yocto layer. When the previous `apt-get install` command is finished, commit the changes to create a new personalized local image:
```
docker commit aero-ros guermonprez/aero-ros
docker images
```

When this image is saved, we can launch ROS.
In the first terminal (running ubuntu-ros), launch:
```
roscore
```

In the second terminal (running yocto), launch a  to the `ubuntu-ros` layer:
```
docker exec -i -t aero-ros /ros_entrypoint.sh
```
And launch the `mavros` ROS module. The only specific argument is the URL to access the flight controller:
```
rosrun mavros mavros_node _fcu_url:=tcp://172.17.0.1:5760 _system_id:=2
```
You now have a working ROS installation, with modules:
* MAVROS for the flight controller access
* Intel RealSense for the 3D sensor access
* ROS modules like `image-view` to get images from cameras

To test it, let's look at the topics available.
Let's launch a third terminal on yocto, and a connection to the ubuntu-ros layer:
```
docker exec -i -t aero-ros /ros_entrypoint.sh
```

Then list the topics, and get the battery level:
```
rostopic list
rostopic echo /mavros/battery
```

Now let's get a RGB image from the Intel RealSense sensor:
```
rosrun image_view image_saver image:=/camera/color/image_raw
# close with ctrl-c after a second, as it's running for ever
```

Then copy the files to your computer for display:
(change the IP with your development station's IP on YOUR NETWORK)
```
scp left00* paulguermonprez@192.168.0.12:~/
```
And display the image in your home folder.

We now have a working ROS container, with Intel RealSense working.

## Why ROS

ROS, or Robotic Operating System, is an open source stack for robotics.
It runs on multiple OSes, but we'll cover installing ROS-kinetic on Ubuntu.

After that, you're in familiar territory. You can run ROS commands as you're used to. Intel Aero is using 4 ROS nodes of interest:
* communicate with the flight controller using the [MAVROS node](http://wiki.ros.org/mavros). MAVROS is designed to work with MAVLINK compatible flight controllers, like Intel Aero Flight Controller with PX4 or Arudpilot stacks.
* interact with the Intel Real Sense R200 camera using [RealSense node](http://wiki.ros.org/RealSense).
* as an alternative to the MAVROS node, you can access the IMU with a [dedicated node imu_driver](https://github.com/intel/imu_driver). MAVROS will give you access to the entire MAVLink protocol (IMU included), imu_driver will give you access to the IMU only in a simpler way.
* in addition to the Intel Real Sense node, you can use the (Intel Aero camera node)[https://github.com/intel/camera_driver].

A good intro on MAVROS with Gazebo simulator is kindly provided by [University of California - Merced course UCM-ME190](https://github.com/UCM-ME190/MavRos-takeoff-n-land)

### Installation

```
sudo add-apt-repository http://packages.ros.org/ros/ubuntu
sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net --recv-key 0xB01FA116
sudo apt-get update

sudo apt -y install ros-kinetic-desktop-full ros-kinetic-rqt python-rosinstall ros-kinetic-realsense-camera ros-kinetic-mavros ros-kinetic-web-video-server ros-kinetic-visp-tracker ros-kinetic-visp-camera-calibration ros-kinetic-vision-visp ros-kinetic-vision-opencv ros-kinetic-video-stream-opencv ros-kinetic-uvc-camera ros-kinetic-usb-cam ros-kinetic-test-mavros ros-kinetic-rviz-visual-tools ros-kinetic-rostopic ros-kinetic-roslaunch python-rosinstall python-rosinstall-generator python-wstool build-essential ros-kinetic-pyros python-rosdep

sudo rosdep init
rosdep update
echo "source /opt/ros/kinetic/setup." >> ~/.rc
source ~/.rc
sudo geographiclib-get-geoids egm96-5
```
### Launching

```
roscore &
roscd realsense_camera
roslaunch realsense_camera r200_nodelet_rgbd.launch &
rosrun mavros mavros_node _fcu_url:=tcp://127.0.0.1:5760 _system_id:=2 &
```

If you ask for the list of topics, with `rostopic list`, you'll get a long list including:
```
/camera/color/camera_info
/camera/color/image_raw/compressed
/camera/depth/image_raw/compressed
/camera/ir/image_raw/compressed
/camera/ir2/image_raw/compressed
/mavlink/from
/mavlink/to
/mavros/actuator_control
/mavros/adsb/send
/mavros/adsb/vehicle
/mavros/altitude
/mavros/battery
```
it means you have both RealSense and MAVlink topics available.

### Visualization with rviz
After you launched ROS, use rossun rviz to see the RealSense output:
```
roscd realsense_camera
rosrun rviz rviz -d rviz/realsense_rgbd_pointcloud.rviz
```
you can also try realsenseRvizConfiguration2.rviz and realsenseRvizConfiguration3.rviz.

A point cloud is the sum of 3D and depth data, both coming from the R200 camera.
In this screenshot, you can see the 2 chairs from the camera's point of view, but also from the side to show you it's in 3D.

![](https://raw.githubusercontent.com/intel-aero/Documents/master/doc_photos/ros_pointcloud.png)

### SLAM with RTAB
A SLAM library is provided by [RTAB-Map](http://wiki.ros.org/rtabmap_ros).
```
roscd realsense_camera
roslaunch realsense_camera r200_nodelet_rgbd.launch &
roslaunch rtabmap_ros rgbd_mapping.launch rtabmap_args:="--delete_db_on_start" depth_registered_topic:=/camera/depth_registered/sw_registered/image_rect_raw
```

When you move, odometry is computed visually and a larger 3D model of the room is built. If the screen goes red, it means the odometry is lost and the model building process is stopped.

![](https://raw.githubusercontent.com/intel-aero/Documents/master/doc_photos/ros_slam.png)

### Computer Vision with VISP

You have access to the camera and depth data, you're free to develop your own code.
If you want to go higher, use OpenCV.
And if you need a very high level computer vision library, use VISP. With VISP, you can perform tasks like model based tracking.

[Demo videos are available on their video feed](https://www.youtube.com/user/VispTeam).


# 10. Rebuild Yocto

## Why rebuild Yocto

Intel Aero comes with a custom build of Linux Yocto flashed from the factory, and you should upgrade it to the latest release.

You can use this preconfigured-precompiled linux system to perform task such as fly or stream video to QGroundControl on your laptop.

In the yocto project concept, you often choose to rebuild your full system from scratch. You compile your app with your OS to have full control over the total software stack. This page will show you how.

_**Note:** the process will take hours and tens of gigabits (really) of disk space._


* You can also access the system running on Intel Aero with ssh as root and start coding. Editors like vi, C/C++ compiler and scripting engines such as python are included. It's very quick and simple.
* You can edit your files on your development station with your favorite IDE and transfer your files to the system for execution of compilation.
* You could even edit and compile on your station and transfer the binary.

### Linux Distribution

Yocto Project validates its tools against a list of distributions and versions, as [described here](http://www.yoctoproject.org/docs/2.3/ref-manual/ref-manual.html#detailed-supported-distros) and replicated as follows:
* Ubuntu 14.04 (LTS), 14.10, 15.04, 15.10, **16.04  (LTS)** - environment used by the development team CI system
* Fedora release 22, 23, 24
* CentOS release 7.x
* Debian GNU/Linux 8.x (Jessie)
* openSUSE 13.2, 42.1

### Dependencies

The Yocto documentation also provides [details and commands to install packages required in each supported distribution](http://www.yoctoproject.org/docs/2.3/ref-manual/ref-manual.html#required-packages-for-the-host-development-system). For Ubuntu 16.04:

```
apt-get update
apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib \
     build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
     xz-utils debianutils iputils-ping
```

### Download and add repo to PATH

```
mkdir ~/bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
export PATH=$HOME/bin:$PATH
```

Note that `~/bin` is just a suggestion. If you choose a different path, adjust instructions accordingly.

## Build

### Get sources

```
mkdir ~/intel_aero && cd ~/intel_aero;
```
Note that `~/intel_aero` is just a suggestion. If you choose a different path, adjust instructions accordingly.

To fetch the source code matching a specific release, run:

```
repo init -u https://github.com/intel-aero/intel-aero-manifest.git -m v1.6.xml -b master
```

To fetch a the version currently under development, run:

```
repo init -u https://github.com/intel-aero/intel-aero-manifest.git -m default.xml -b master
```

With the repository initialized, run the command that actually fetches the layers and recipes:

```
repo sync -j4
```

### Initialize the environment

```
cd ~/intel_aero/poky
export TEMPLATECONF=../meta-intel-aero/conf/
source oe-init-build-env
```
`oe-init-build-env` configures environment variables, create config files based on templates and takes the  to `~/intel_aero/poky/build` directory, where bitbake can be executed:

```
bitbake intel-aero-image
```

Upon build completion, the image will be available at `~/intel_aero/poky/build/tmp/deploy/images/intel-aero/`

Refer to the setup page for instructions on how to flash this image.
