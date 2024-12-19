![OpenCore logo](https://github.com/acidanthera/OpenCorePkg/raw/master/Docs/Logos/OpenCore_with_text_Small.png)

# Surface-Book-3-OpenCore
macOS on the Microsoft Surface Book 3 thanks to [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg).
  
## Software Specifications
| Software         | Version                            |
| ---------------- | ---------------------------------- |
| Target OS        | Apple macOS 15 Sequoia, 14 Sonoma and 13 Ventura |
| OpenCore         | [MOD-OC v1.0.3](https://github.com/wjz304/OpenCore_NO_ACPI_Build/releases/download/1.0.3_20b758b/OpenCore-Mod-1.0.3-RELEASE.zip) |
| SMBIOS           | MacBookPro16,2 |
| SSD format       | APFS file system, GPT partition table |

## Abstract
The `Intel 13.5-Inch and 15-Inch Surface Book 3` line of laptops runs macOS quite well. However, as a few things don't work as expected, it is hard to recommend the Surface Book 3 as a Hackintosh laptop.

Only the battery of the clipboard (tablet part of the laptop) is detected by macOS. The much larger battery in the keyboard deck is not appearing in the power settings.

The keyboard and the trackpad are unresponsive after reattaching the clipboard (tablet part of the laptop) to the keyboard deck. The touchscreen is still working, though.

The cameras are not working at all.

ACPI S3 Sleep is broken, but ACPI S4 Hibernate works great, though, and resuming from Hibernation takes around ten to fifteen seconds. The advantage Hibernate has over Sleep is that the device doesn't consume any power while in a hibernated state.

The battery runtime is around five hours.

> [!TIP]
> I recommend installing `macOS 13 Ventura` rather than the newer `macOS 14 Sonoma` or `macOS 15 Sequoia`. The builtin Intel Wireless chip works almost perfectly with Apple's iServices and Continuity features on Ventura while those features are partially broken at the moment on newer versions of macOS.

> [!IMPORTANT]
> The keyboard and trackpad are now working in the installer as well as in the installed OS, but the trackpad will be lagging/skipping every few seconds. Furthermore, the keyboard and trackpad will be unresponsive after resuming from hibernation. To fix those issues, you **MUST** downgrade the firmware of your `Surface Book 3`. To do so, [follow these instructions](https://github.com/jlempen/Surface-Book-3-OpenCore?tab=readme-ov-file#downgrading-the-uefi-firmware). Then, you **MUST** also replace `BigSurface.kext` and its dependencies with `BigSurfaceSLB3.kext` and its dependencies in your `config.plist` file by following [these instructions](https://github.com/jlempen/Surface-Book-3-OpenCore/blob/main/README.md#replacing-bigsurfacekext-with-bigsurfaceslb3kext).

> [!WARNING]
> If the display turns off right before the installer starts, simply shut down your Surface Book by pressing the power button for 15 to 20 seconds and power it back on to reboot into the installer again. It might take two or three attempts. Also, there might be a few display artifacts during the installation, but the display will work just fine in the installed system.
> 
> If this method doesn't work for you, you could also add the boot argument `-igfxvesa` to the `NVRAM` -> `7C436110-AB2A-4BBB-A880-FE41995C9F82` -> `boot-args` section of your `config.plist` file. This disables the Intel Graphics acceleration. Once macOS is installed, simply remove the `-igfxvesa` boot argument from your `config.plist` file and reboot your laptop to enjoy full graphics acceleration.

> [!CAUTION]
> At the moment, it is not possible to have Windows installed on the device at the same time as macOS, as Windows will immediately update to the latest firmware again when you boot into Windows, even if you turn off your WiFi or disable automatic Windows Updates. I haven't found a way to prevent this behaviour yet. Any kind of Linux distribution is fine, though.

## Disclaimer
This repository is neither a howto nor an installation manual. Using these files requires at least basic knowledge of [Acidanthera's OpenCore bootloader](https://github.com/acidanthera/OpenCorePkg), ACPI, UEFI and the art of hackintoshing in general. I recommend reading the excellent [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide), as well as all its linked resources. For those who wish to improve their hackintoshing knowledge, [5T33Z0's OC-Little-Translated](https://github.com/5T33Z0/OC-Little-Translated) repository is the most comprehensive resource I've found on the subject.

## Recommendations
I recommend completely erasing the device's SSD by creating a new GPT partition table before attempting to install macOS, as it makes the installation process much easier. You may use any Linux live ISO with a partitioning tool such as `GParted` or `KPartition` to erase the SSD.

For macOS to be able to boot on the Surface Book 3, the `Secure Boot` option _**must be disabled**_ in the UEFI. The boot screen will then display a large red bar with a lock icon at the top of the display when Secure Boot is disabled. This is normal.

Please be aware that all `PlatformInfo` and `SMBIOS` information was removed from the OpenCore `config.plist` file. Users will therefore need to generate their own `PlatformInfo` with [CorpNewt's GenSMBIOS tool](https://github.com/corpnewt/GenSMBIOS) before attempting to boot a Surface Book 3 with this repository's EFI folder.

`BigSurfaceSLB3.kext` is required to enable the trackpad, the keyboard, the touchscreen, the battery status, the power and volume buttons and the ambient light sensor. Updating this `kext` with the official one from [Xiashangning's BigSurface repo](https://github.com/Xiashangning/BigSurface) will most certainly break something! `BigSurfaceSLB3.kext` is a modified version of `BigSurface` v6.5 which fixes the skipping/lagging trackpad and hibernate mode 25.

An UEFI firmware released by Micro$oft in August 2023 broke the Surface Book 3's trackpad in macOS in several ways. Downgrading the UEFI firmware to the last known working version `13.101.140.0` is required to fix the trackpad and hibernate mode 25 on macOS. See the detailed instructions below to easily downgrade the UEFI firmware.

`AirportItlwm-Ventura.kext`, `AirportItlwm-Sonoma140.kext` and `AirportItlwm-Sonoma144.kext` from the [OpenIntelWireless repo](https://github.com/OpenIntelWireless/itlwm) are required to enable the Wifi chip. This EFI will dynamically load the appropriate kext for macOS Ventura or Sonoma depending on the running kernel. No need to manually replace the kext file when updating your version of macOS. As the Intel Wifi chip does not yet work with the `AirportItlwm.kext` in macOS Sequoia, you'll need to use the `Itlwm.kext` and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) to connect to a Wifi network. You'll find the latest stable `HeliPort.dmg` in the [Tools folder](https://github.com/jlempen/Surface-Book-3-OpenCore/blob/main/Tools/HeliPort.dmg) of this repo. This EFI will dynamically load the `Itlwm.kext` instead of `AirportItlwm.kext` when you boot into macOS Sequoia.

This repository uses the unofficial OpenCore_NO_ACPI_Build fork of OpenCore by [btwise](https://gitee.com/btwise/OpenCore_NO_ACPI), wich is not endorsed by Acidanthera (the dev team behind OpenCore). The main (and only) difference between this fork and the official OpenCore version is that it allows to prevent ACPI injection (e.g. patches, tables, boot parameters) into other OSes besides macOS.

Windows and Linux should be detected automagically by the OpenCore boot loader even when installed after macOS.

<details>
  <summary>Computer Specifications</summary>
  
## Computer Specifications
| Device           | Hardware                           |
| ---------------- | ---------------------------------- |
| CPU              | Intel Core i7-1065G7 or Intel Core i5-1035G7 |
| iGPU             | Intel Iris Plus Graphics |
| dGPU             | NVIDIA GeForce GTX 1650 or 1660 Ti with Max-Q Design |
| Audio            | Realtek ALC274 |
| RAM              | 8, 16 or 32 GB RAM |
| Wifi + Bluetooth | Wifi6 AX200, Bluetooth 5.0 |
| Storage          | Kioxia/Toshiba/SK Hynix PCIe SSD |
| USB Type-C 3.1 Gen 1 | Supports Power Delivery and DisplayPort |
| SDXC Card Reader | Full size SDXC UHS-I / UHS-II USB Card Reader |
| Cameras | 5 MP front and 8 MP rear cameras with 1080p HD video |
| IR camera | Intel(R) AVStream Camera 2500, ISP Interface |
| Keyboard / Trackpad | Detachable keyboard/trackpad deck |
| Display | 13.50 inch 3:2, 3000 x 2000 or 15 inch 3:2, 3240 x 2160 201 PPI |
| Touchscreen | 10-point capacitive |
| Dual batteries | One battery in the clipboard and one in the keyboard deck |
| Ambient light sensor | |
</details>

<details>
  <summary>What works</summary>
  
## What works
- [x] CPU power management
- [x] CPU SpeedStep
- [x] iGPU with full acceleration
- [x] SSD drive
- [x] USB-C port
- [x] USB-A port
- [x] USB SDXC card reader
- [x] WLAN
- [x] Bluetooth
- [x] Internal speakers, microphone and Combojack
- [x] Power, volume up and volume down buttons
- [x] Keyboard with working brightness, volume and mute keys, working caps lock light
- [x] Trackpad with native multi-touch gestures
- [x] Touchscreen
- [x] Surface Pen
- [x] Ambient light sensor
- [x] Battery percentage and cycle count
- [x] Hibernation (hibernatemode 25) - the device successfully wakes up from hibernation mode
- [x] USB Type-C to HDMI
- [x] USB Type-C to USB3 & USB2
- [x] USB Type-C Power Delivery
</details>

<details>
  <summary>What needs some more work</summary>
  
## What needs some more work
- [ ] Sleep (hibernatemode 3) - the device only turns off the display without sleeping
- [ ] The battery in the keyboard deck does not appear in the power settings
- [ ] The keyboard and the trackpad are unresponsive after the clipboard (tablet part) is reattached to the keyboard deck
</details>

<details>
  <summary>What will probably never work</summary>
  
## What will probably never work
- [ ] IR camera (Windows Hello)
- [ ] Front camera
- [ ] Rear camera
</details>

<details>
  <summary>UEFI Settings</summary>
  
## UEFI Settings
To enter the UEFI Settings, power on your Surface Book 3 and hold the `Volume Up Button` as soon as the Surface Logo is displayed on the screen.

The `Secure Boot` setting ***must be disabled to boot macOS***. I also recommend moving `USB Storage` to the top of the boot configuration list, which makes booting from an USB stick much easier.

| Security | |
| -------- | ----- |
| Secure Boot | Disabled |

| Boot configuration | |
| -------- | ----- |
| USB Storage | Move the item to the top of the list |
</details>

<details>
  <summary>Downgrading the UEFI firmware</summary>

## Downgrading the UEFI firmware
In order to fix the skipping/lagging trackpad in macOS and make the trackpad and keyboard work after hibernation, you must downgrade your UEFI firmware to the last known working version `13.101.140.0`.

1. Boot with a Linux Live USB stick, preferably a Debian, Arch or Fedora based distribution (I use the Arch-based Manjaro).
2. Download and unzip the compressed firmware archive [SurfaceBook3_FW_13.101.140.0.zip](https://github.com/jlempen/Surface-Book-3-OpenCore/blob/main/UEFI%20Firmware/SurfaceBook3_FW_13.101.140.0.zip) from this repository.
3. Add the line `OnlyTrusted=false` to the `/etc/fwupd/daemon.conf` config file. On some Linux distros such as Arch, endeavourOS and Manjaro, the config file to change is `/etc/fwupd/fwupd.conf`:
```
sudo nano /etc/fwupd/daemon.conf
```
or
```
sudo nano /etc/fwupd/fwupd.conf
```
4. Open a terminal and navigate to the folder where you extracted the firmware files.
5. Connect your Surface device to a power supply.
6. Copy the following lines and paste them into the terminal:
```
for f in *; do 
  sudo fwupdmgr install --allow-older --allow-reinstall --no-reboot-check "$f"
done
```
7. Close the terminal and reboot the computer.

For some firmware files, the `fwupdmgr` tool may complain that it is unable to find a matching device. This is normal, as not all Surface Book 3 models use the exact same hardware, thus the compressed firmware archive contains all the required files for all models.

The Surface Book 3 will reboot and downgrade all UEFI firmwares at once, which takes around 10 minutes. You'll see progress bars with different colours depending on which type of firmware is being flashed.
Once the process is done, your laptop will restart a few times and seem to hang on the Surface logo for 20 or 30 seconds each time, this is normal. Then it will restart for good to your OpenCore picker.

Now restart while holding the F4/Volume Up key to check the firmware version in the UEFI. In the Firmware section, `System UEFI` should now show `13.101.140`.

Reboot and you're done.

If you are using Windows on the laptop, you'll have to find a way to prevent Windows Update from automatically updating the firmware to the latest firmware version again on the next reboot into Windows! I haven't found a way to prevent this yet. Any kind of Linux distribution is fine, though.
</details>

<details>
  <summary>Replacing BigSurface.kext with BigSurfaceSLB3.kext</summary>
  
## Replacing BigSurface.kext with BigSurfaceSLB3.kext
Additionally, to fix the skipping/lagging trackpad in macOS and make the trackpad and keyboard work after hibernation, you also have to replace the official `BigSurface.kext` with the `BigSurfaceSLB3.kext`. 

In the `Kernel` -> `Add` section of your `config.plist` file, disable or delete the following kexts:
```
BigSurface.kext/Contents/PlugIns/VoodooGPIO.kext
BigSurface.kext/Contents/PlugIns/VoodooSerial.kext
BigSurface.kext/Contents/PlugIns/VoodooInput.kext
BigSurface.kext
BigSurface.kext/Contents/PlugIns/BigSurfaceHIDDriver.kext
```
Then enable the following kexts:
```
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooGPIO.kext
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooSerial.kext
BigSurfaceSLB3.kext/Contents/PlugIns/VoodooInput.kext
BigSurfaceSLB3.kext
BigSurfaceSLB3.kext/Contents/PlugIns/BigSurfaceHIDDriver.kext
```
Save your `config.plist` file and reboot. Your trackpad should now be buttery smooth before and after resuming from hibernation. The keyboard will now also work after resuming from hibernation.
</details>

<details>
  <summary>Disabling Sleep and enabling Hibernate</summary>
  
## Disabling Sleep and enabling Hibernate
As we still haven't found a solution for the Sleep/Wake issues on the Surface Book 3, disable Sleep altogether and use Hibernate for now. Open the `Terminal` and enter the following commands, then reboot for the changes to take effect:
```
sudo pmset restoredefaults
sudo pmset -a hibernatemode 25
```
If for whatever reason Hibernate is not working on your system, you should reset the `Power Management` settings and rebuild the `sleepimage` file. To do so, open the `Terminal` and enter the following commands, then reboot for the changes to take effect:
```
sudo rm /Library/Preferences/com.apple.PowerManagement*
sudo rm /var/vm/sleepimage
sudo pmset hibernatefile /var/vm/sleepimage
```
Once you are back in macOS, disable Sleep and enable Hibernate again, then reboot:
```
sudo pmset restoredefaults
sudo pmset -a hibernatemode 25
```
</details>

<details>
  <summary>Fix broken Bluetooth on Wake from Hibernation</summary>
  
## Fix broken Bluetooth on Wake from Hibernation
After the device wakes up from Hibernation, Bluetooth may be broken / unable to connect.

A very simple fix for this issue is to [download and install Bluesnooze](https://github.com/odlp/bluesnooze). Launch the app, enable `Launch at login` and you're done!
</details>

<details>
  <summary>Enabling native HiDPI display settings in macOS</summary>
  
## Enabling native HiDPI display settings in macOS
I recommend downloading and installing [BetterDisplay](https://github.com/waydabber/BetterDisplay) to change and manage the display resolutions on the Surface Book 3.
</details>

<details>
  <summary>Installing the IPTSDaemon to enable the touchscreen</summary>
  
## Installing the IPTSDaemon to enable the touchscreen
The [IPTSDaemon](https://github.com/Xiashangning/IPTSDaemon) is a tool made by the author of BigSurface [Xiashangning](https://github.com/Xiashangning). It enables the touchscreen on Surface devices running macOS.

1. Download the [IPTSDaemon](https://github.com/jlempen/Surface-Book-3-OpenCore/blob/main/Tools/IPTSDaemon.zip)
2. Unzip the downloaded file
3. Open a `Terminal` and navigate to the `IPTSDaemon` folder:
```
cd /Downloads/IPTSDaemon/IPTSDaemon
```
4. Run the `install_daemon.sh` file:
```
sudo bash install_daemon.sh
```
5. Enter your password to install the daemon

Now you'll see a nasty popup window:

![Nasty popup window](https://github.com/user-attachments/assets/eacbfe79-04a4-4bd8-b851-ba83cd55e9b6)

This is actually macOS's way of telling you that Apple considers that the software is from an untrusted source because it is unsigned. But it's actually very easy to tell it to open the file anyway.

Click on the "Show in Finder" button of this popup window, then right-click on the `libinih.0.dylib` file and select the first option, "Open". Nothing will happen, but the annoying popup window will not show anymore for this file. Now repeat the same procedure for the `libfmt.9.dylib` file and you're done.

Perhaps you'll need to repeat this a few times, as the popup window appearing for one file will block the "Open" popup window for the other file and vice versa. Basically, once there's no warning popup appearing anymore, both files were registered and started.

You may now verify that the multitouch gestures are working on your touchscreen by playing around with the standard macOS multitouch gestures you're used to on your trackpad, but the same gestures now work on the touchscreen as well :-)

These instructions are confirmed working on SL3 and SB3 running macOS Ventura and Sonoma. On macOS Sequoia, the procedure is pretty much similar, but there won't be a "Show in Finder" button in the popup window. To open the dylib files, you'll have to go to the `System Settings` -> `Privacy and Security` -> `Security` section and open the files from there.
</details>

<details>
  <summary>Fixing broken Apple Messages and FaceTime</summary>
  
## Fixing broken Apple Messages and FaceTime
To fix issues with Apple Messages and FaceTime related to the [Intel Wireless driver](https://github.com/OpenIntelWireless/itlwm) on macOS Sonoma, disable all `AirportItlwm-***.kext` entries under `Kernel -> Add` in your `config.plist` file and use the [itlwm_v2.3.0_stable.kext.zip](https://github.com/OpenIntelWireless/itlwm/releases/download/v2.3.0/itlwm_v2.3.0_stable.kext.zip) and its companion app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases/download/v1.5.0/HeliPort.dmg) instead.
The latest version 2.3.0 of itlwm.kext is already included in the Kext folder and `config.plist` file.
</details>

<details>
  <summary>Related repositories</summary>
  
## Related repositories
* https://github.com/jc-bao/surface-laptop3-ventura
* https://github.com/Xiashangning/BigSurface
* https://github.com/Xiashangning/IPTSDaemon
</details>
