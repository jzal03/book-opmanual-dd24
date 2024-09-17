# Initializing the Flight Controller

```{admonition} What you will need
:class: note

* A base station computer
* Flight Controller
* USB to USB-C cable
```


```{admonition} What you will get

* An up-to-date, initialized Flight Controller
```

The Flight Controller (FC) implements several low-level behaviors, e.g., stabilizing the Duckiedrone around roll, pitch, and yaw through three different PID controllers. Correctly configuring the Flight Controller is critical for flying safely.  

## Installing Dependencies and Flashing Betaflight Firmware

### 1. Install dfu-util and screen:

- Connect to the Raspberry Pi through ssh.
- Update the package list:

```bash
sudo apt update
```

- Install `dfu-util` and `screen`:

```bash
sudo apt install dfu-util screen
```

### 2. Connect to the serial Console:

- Make sure your SpeedyBee flight controller is connected to the Raspberry Pi via a USB cable.
- Identify the serial port your flight controller is connected to by running `ls /dev/ttyACM*`, it's commonly listed as `/dev/ttyACM0`, but it might differ.

- In the terminal, run the following command to connect to the serial console using `screen`:

    ```bash
    screen /dev/ttyACM0 115200
    ```

    Replace `/dev/ttyACM0` with the actual serial port of your flight controller if it's different.


### 3. Entering DFU Mode:

- Once connected through `screen`, press the `#` key on your keyboard to enter the Betaflight command-line interface (CLI).
- In the Betaflight CLI, type the command `bl` and press enter. This will reboot the board and put it into DFU (Device Firmware Update) mode.

### 4. Download and Flash the Firmware:

Run the following command:

```bash
wget https://github.com/duckietown/duckiedrone-ardupilot-driver/blob/366b41b08dfdb905e40bb2c91e57c9704a313500/assets/arducopter_with_bl_v4_5_5.bin?raw=true
```

- Run `dfu-util` to flash the firmware:

```bash
sudo dfu-util -a 0 -s 0x08000000:leave -d 0483:df11 -D arducopter_with_bl_v4_5_5.bin
```

```{pro-tip}
Explanation of the `dfu-util` command:

* `sudo`: Grants administrative privileges to run the command.
* `dfu-util`: The name of the utility used for flashing firmware.
* `-a 0`: Selects the first DFU device (adjust if there are multiple).
* `-s 0x08000000:leave`: Sets the address and size for the new firmware.
* `-d 0483:df11`: Defines the vendor ID (0483) and product ID (df11) for Betaflight devices.
* `-D arducopter_with_bl_v4_5_5.bin`: Specifies the filename of the firmware to be flashed.
```

### 5. Exit and Disconnect:

- The flashing process might take a few minutes. Once finished, the flight controller will reboot.

## Connecting to the Flight Controller

1.  Unplug the battery from your drone

    ```{attention}
    Double-check the battery is unplugged
    ```

1.  Connect the USB-C cable from your Flight Controller to your base station

1. Identify the `"Connect"` button in the top right corner (right now it will be green as the Flight Controller is not connected yet)
    ```{figure} ../_images/software-initialization/BFC_connect_button.png

    `"Connect"` button in the top-right corner of Betaflight Configurator

1. Select the correct connection port

    ```{tip}
    Details might vary depending on available connections on your base station. 
    
    The correct port should start with: 

    *   Linux/macOS
        *   `/dev/tty.usbserial*`
        *   `/dev/cu.usbserial*` 
        *   `/dev/ttyUSB*`
    *   Windows
        *   `COM*`
    ```
1. Leave the baud rate (number) at the default value of `115200`

1.  Press the `“Connect”` button and a `“Setup”` page should greet you with a rendering of your drone (see figure below).

```{figure} ../_images/software-initialization/BFC_setup_screen.png

Betaflight Configurator `"Setup"` page
```

```{attention}
Please check below that you have the correct versions of both:

*   Configurator: `10.9.0`
*   Firmware:   `BTFL 4.3.2`
```

### Checking the firmware version

On the top left of the Betaflight Configurator interface, one could check for the Firmware version. For example, in the figure below, the firmware version of the Flight Controller is `BTFL 4.3.2`.

```{figure} ../_images/software-initialization/betaflight_firmware_version.png

Top left of Betaflight Configurator, check Betaflight Configurator version and Flight Controller firmware version here
```

## Restoring the correct settings
We will restore the correct settings for the Flight Controller that reflect the setup we have on Duckiedrones (i.e. Flight Controller upside down, ESCs communication protocol, etc.)

The settings for the Flight Controller can be saved and restored through the CLI interface of Betaflight, which is akin to a shell used to interact with the firmware.

We have created a file with the required setup for you, so you will only need to restore it without having to tweak parameters through the Betaflight Configurator.

To do this:

1.  Download [this](https://raw.githubusercontent.com/duckietown/dt-duckiebot-interface/ente/packages/flight_controller_driver/config/bf_SPEEDYBEEF405.config) configuration file.

1.  Open it in the notepad app of your base station

1.  Copy all content of the file from the notepad by simply clicking on the text and using <kbd>CTRL</kbd> + <kbd>A</kbd> or <kbd>CMD</kbd> + <kbd>A</kbd>

1.  Go in the `CLI` tab of Betaflight Configurator
    ```{figure} ../_images/software-initialization/fc_cli_tab.png
    :width: 300px

    CLI tab
    ```
1.  Paste all the text you previously copied using <kbd>CTRL</kbd> + <kbd>V</kbd> or <kbd>CMD</kbd> + <kbd>V</kbd> in the text field at the bottom (the one with the text  `Write your command here. Press Tab for AutoComplete`).
    ```{figure} ../_images/software-initialization/fc_cli_interface.png

    CLI interface
    ```
1.  Press `Enter` on your keyboard to execute the commands and wait for the shell to finish (it should take a few seconds)

The Flight Controller will now reboot and reconnect.

```{todo}
Update check
```

```{admonition} Check
:class: seealso

In the `setup` tab check that the drone now results facing up when the side of the Flight Controller with the chips faces downward.
```

(fc_initialization_troubleshooting)=
## Troubleshooting

```{trouble}
The progress bar turns red and shows `"No response from the bootloader, programming:FAILED"`
---
This is likely due to the Flight Controller not being in Bootloader mode. Please double check the indicators of that mode in the section above.
```

````{trouble}
`"Flashing..."` started, but progress bar turns red with a `"Timeout"` error
---
<!-- Source: https://betaflight.com/docs/development/usb-flashing#ubuntu -->

Linux requires udev rules to allow write access to USB devices for users. An example shell command to achieve this on Ubuntu is shown here:

```bash
(echo '# DFU (Internal bootloader for STM32 and AT32 MCUs)'
 echo 'SUBSYSTEM=="usb", ATTRS{idVendor}=="2e3c", ATTRS{idProduct}=="df11", MODE="0664", GROUP="plugdev"'
 echo 'SUBSYSTEM=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="df11", MODE="0664", GROUP="plugdev"') | sudo tee /etc/udev/rules.d/45-stdfu-permissions.rules > /dev/null
```

This assigns the device to the plugdev group(a standard group in Ubuntu). To check that your account is in the plugdev group type groups in the shell and ensure plugdev is listed. If not you can add yourself as shown (replacing <username> with your username):

```bash
sudo usermod -a -G plugdev <username>
```

**If you have any other operating system** you can take a look at the OS-specific configuration at [this link](https://betaflight.com/docs/development/usb-flashing#ubuntu).

````

````{trouble}
On Linux, Betaflight Configurator doesn't connect to the Flight Controller and an error `Failed to open serial port` appears in the log 
---
This is a permission issue to access the serial port of the Flight Controller. 
First try to run `sudo usermod -a -G dialout <USERNAME> `, replacing <USERNAME> with your base station username; this will add your user to the group that has access to the serial ports. **Reboot** for the change to take effect.

If this doesn't work, the quickest solution is to run `sudo chmod 0777 /dev/tty*` while Betaflight Configurator is open, where `/dev/tty* is the port you're using to connect to the Flight Controller.

This second procedure has to be done each time the Flight Controller is reconnected to the base station.

````

```{trouble}
Other issues
---
We’re happy to support! Please contact our hardware team via email: [hardware@duckietown.com](mailto:hardware@duckietown.com)
```
