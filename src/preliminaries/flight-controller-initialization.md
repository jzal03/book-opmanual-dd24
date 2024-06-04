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

## Installing Betaflight Configurator (BFC)
Betaflight Configurator is an app that allows the base station to connect directly to the Flight Controller and access its configuration interface.

Steps:

1.  Download the correct version of Betaflight Configurator v10.9.0 for your OS from [**this link**](https://github.com/betaflight/betaflight-configurator/releases/tag/10.9.0).

1. Install Betaflight Configurator on your system

1. Start Betaflight Configurator on your base station. You should see the below interface.

    ```{figure} ../_images/software-initialization/BFC_welcome_screen.png

    Betaflight Configurator (BFC) `welcome screen`
    ```

## Flashing the correct firmware

### Updating the Flight Controller firmware (Flashing the Flight Controller)

Flight Controllers might have different versions of firmware (i.e., the software that runs on the Flight Controller microcontroller) out of the factory. Normally, this only needs to be done once initially. Follow this procedure to update your firmware.

```{attention}
During flashing, do not press the `“Connect”` button in Betaflight Configurator. 
```

Our current target firmware is **BTFL v4.3.2**.

### Prepare for flashing the Flight Controller firmware

```{attention}
Regardless of the firmware version, if it is the first time setting up the Flight Controller, we recommend performing the below flashing procedure once anyways in order to start from a clean state.
```

Perform the following 2 steps with the Flight Controller **disconnected**.

*   In the default Welcome page of Betaflight Configurator, in the left sidebar, please click on the `Firmware Flasher` tab 
    ```{figure} ../_images/software-initialization/betaflight_flasher.png

    `Firmware Flasher` tab in Betaflight Configurator
    ```

*   Configure the options in the tab as below:
    ```{figure} ../_images/software-initialization/flasher_parameters.png

    `Firmware Flasher` parameters to set
    ```

## Flashing the Flight Controller

```{attention}
Normally, when connected to the base station, the Flight Controller connects in `Normal mode`.

This can be identified by:

*    Having a red LED on the Flight Controller board
*    Having **no blue blinking** LED on the Flight Controller board
*    Having an orange LED on the Flight Controller board


If this is the case during this section, unplug from base station and try to reactivate `bootloader mode` as detailed below.
```


To flash the firmware, we need the Flight Controller to be in `bootloader mode`.

1. Identify the `boot` button on the Flight Controller to the laptop and press the `Connect` button:

    ```{figure} ../_images/software-initialization/speedybee_boot_button.png

    `BOOT` button location on the SpeedyBeeF405V3 Flight Controller.
    ```
 
1. Connect the Flight Controller to the laptop while pressing the `boot` button.

Now that the Flight Controller is in `bootloader mode` you can flash the correct firmware:

1.  In the Betaflight Configurator Firmware Flasher tab, click the `Load Firmware [Cloud]` button in the bottom right.

1.  Depending on your operating system, it might be necessary to configure the OS to detect the board in DFU mode. See the [link here](https://betaflight.com/docs/development/usb-flashing) to find out how for your specific OS.

1.  Click the Flash Firmware button (bottom right) and check the progress bar.

    ```{admonition} Check
    :class: seealso
    
    The progress bar shows: `“Flashing…”` => `“Verifying…”` => `“Programming SUCCESSFUL”`
    ```

    ```{tip}
    In case the progress bar turns red, see the [Troubleshooting section below](fc_initialization_troubleshooting)
    ```

3.  If successful, without needing to reconnect the cable, the Flight Controller should go back to the `Normal mode`.

    ```{admonition} **Check**
    :class: seealso

    1. Verify the blue blinking LED is back on

    1. Click `“Connect”` and verify the firmware version is correct as detailed below
    ```

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

1.  Download [this](https://raw.githubusercontent.com/duckietown/pidrone_pkg/dd24/SPEEDYBEEF405.conf) configuration file.

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
