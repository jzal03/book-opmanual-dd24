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

Run `dfu-util` to flash the firmware:

```bash
sudo dfu-util -a 0 -s 0x08000000:leave -d 0483:df11 -D arducopter_with_bl_v4_5_5.bin
```

```{tip}
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

## Starting the drone software stack

Now you will need to start the drone software stack, allowing you to connect to the flight controller from your laptop.

To do so you need to use the Duckietown shell `dts`.

````{attention}
Make sure that the `dts` on your laptop is:

- Running the `ente` distribution. You can check  by running `dts profile list`
- Updated to the latest version by running:

   ```bash
   dts update
   ```
````

To start the flight software stack execute the command

   ```bash
   dts duckiebot update -t duckiedrone --distro=ente -f [ROBOT_NAME]
   ```

Wait for the command to terminate before proceeding to the next step.

## Connecting to the Flight Controller

```{attention}
Unplug the battery from your drone!
```

(qgroundcontrol-connection)=
### Installing QGroundControl and Restoring the correct parameters

By following these steps, you will be able to install QGroundControl, connect to your flight controller via TCP, and restore your vehicle’s parameters from a `.param` file. You can download the correct `.param` file from [this link](https://github.com/duckietown/duckiedrone-ardupilot-driver/blob/99b4b28c950cd4aef546dde394732961a827bbe0/assets/speedybeef405_ardupilot.params?raw=true).


1. Install QGroundControl:`
   - Go to the [QGroundControl website](http://qgroundcontrol.com/) and download the installer for your operating system (Windows, macOS, or Linux).
   - Follow the installation instructions for your OS:
     - **Windows**: Run the installer and follow the prompts.
     - **macOS**: Download the `.dmg` file, open it, and drag the QGroundControl icon into your Applications folder.
     - **Linux**: Follow the package manager or AppImage instructions provided on the QGroundControl download page.
   - Once installed, launch QGroundControl.

2. Connect to Your Vehicle via TCP:
   - Open QGroundControl on your computer.
   - Go to the **Comm Links** section by clicking on the **Q** application icon in the top left corner.
   - Select **Add** to create a new communication link.
   - Choose **TCP Link** from the dropdown.
   - Set the **Host Address** to `<robot_name>.local` and the **Port** to `5760`.
   - Click **Connect** to establish the connection with your flight controller.

3. Access the Vehicle Setup:
   - Once connected, open the menu by clicking on the by clicking on the **Q** application icon in the top left corner and open the **Vehicle Setup** page from the popup menu that appears.

4. Navigate to Parameters:
   - In the Vehicle Setup menu, select the **Parameters** tab to view the configurable parameters for your vehicle.

5. Load the `.param` File:
   - In the Parameters screen, click on the **Tools** menu in the top right corner.
   - Select **Load from file…** from the dropdown menu.
   - Browse to the location of your `.param` file on your computer, select it, and click **Open**.

6. Apply the Parameters:
   - QGroundControl will load and apply the parameters from the file to your vehicle. Progress indicators or messages will confirm that the parameters are being applied.

7. Reboot the Vehicle:
   - After loading the parameters, it is usually necessary to reboot the flight controller for changes to take effect.
   - You can reboot the vehicle by selecting **Reboot Vehicle** from the **Tools** menu.

Here is a video summing up how to restore the parameters after having connected to the Flight Controller:

```{vimeo} 1010195551
```

### Additional Tips

- **Multiple Loads:** Some parameters may require multiple loads until they are all applied correctly.
- **Check for Errors:** Ensure QGroundControl does not report any errors during the parameter loading process.

## Troubleshooting

```{trouble}
I am having issues following the instructions!
---
We’re happy to support and to hear your feedback! Please post a question on our StackOverflow, you can find the instructions on how to join it [here](https://duckietown.slack.com/archives/CHHQJ0E0H/p1670874390660429).

You can also contact us via Slack at the following channel: [duckietown-sky-help](https://duckietown.slack.com/archives/CJWNCG667)
```
