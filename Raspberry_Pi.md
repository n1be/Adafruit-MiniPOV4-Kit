# Using MiniPOV4 with a Raspberry Pi

  This document explains how to install and setup the necessary software on a Raspberry Pi in order to use the Pi for uploading images to an Adafruit MiniPOV4.

  I assume that "Raspbian GNU/Linux 10 (buster)", the current release of the Raspberry Pi operating system, is already installed on the Raspberry Pi.  If not, please follow the Software page on [raspberrypi.org](https://www.raspberrypi.org/software/) before trying what is described below.

## Update to retrieve any recent changes to the Raspberry Pi operating system (RaspiOS):

  1. Open the Terminal, either from the Start menu (click the Raspberry Pi icon in the top left corner) or by pressing Control + Alt + T.

  2. Type the following command to update the local list of available software packages:

        sudo apt update

  3. When the update has finished, type this command to upgrade the locally installed packages:

        sudo apt full-upgrade

  4. Reboot the Pi now to assure that the latest software is running before going further.

        sudo reboot

  5. When the Pi has rebooted, log in and open a terminal program.

## Install avrdude

  Avrdude is a program that can write data to AVR microcontrollers like the one used in the MiniPOV4 device. Use this command to install it from the RaspiOS software repository:

        sudo apt install avrdude

## Install the Processing IDE

  The Raspberry Pi (ARM CPU) was not supported until Processing V3.0.  Therefore we need a newer version than what is recommended in the Adafruit guide.  Use this command to install the current version of Processing:

        curl https://processing.org/download/install-arm.sh | sudo sh

## Download other needed files

  These commands will make a directory for POV downloads, move to that directory and then download the files.

        mkdir -p ~/Downloads/pov
        cd ~/Downloads/pov
        wget -nv https://cdn-learn.adafruit.com/downloads/pdf/minipov4-diy-full-color-persistence-of-vision-light-painting-kit.pdf
        wget -nv https://github.com/sojamo/controlp5/releases/download/v2.2.5/controlP5-2.2.5.zip
        wget -nv -O MiniPOV4.zip https://github.com/n1be/Adafruit-MiniPOV4-Kit/archive/master.zip

## Install the controlP5 library

  The MiniPOV4 image converter is written in Processing and it uses the controlP5 graphics library.  Because we have upgraded to Processing 3+, it is also necessary to use a newer release of controlP5.  Therefore we use a newer controlP5 version than what is recommended in the Adafruit guide.

  ControlP5 was downloaded in the previous step.  Here we insert the controlP5 library into the user's directory where Processing will look for libraries.  These commands assume the working directory is still ```~/Downloads/pov``` :

        mkdir -p ~/sketchbook/libraries X
        unzip controlP5-2.2.5.zip -d X
        mv X/controlP5 ~/sketchbook/libraries
        rm -rf X

## Extract the github files needed for the image converter

  Here we copy the image converter program and relevant files to the "sketchbook" directory.  Processing programs are called sketches and kept in sketchbook folders.  These commands assume the working directory is still ```~/Downloads/pov``` :

        mkdir -p ~/sketchbook/MiniPov4_ImageConverter
        unzip MiniPOV4.zip
        mv -v A*/T*/*png A*/M*/*.{pde,conf,sh} ~/sketchbook/MiniPov4_ImageConverter

  In addition, these commands add a rule so that when the MiniPOV4 is connected to the Pi, the device permissions will allow any user to access that device.

        sudo install -g0 -o0 -m644 -p A*/M*/*.rules /etc/udev/rules.d
        sudo udevadm control --reload
        rm -rf Adafruit-MiniPOV4-*/

## (Optional) get more images

  A few additional prepared images for use with the MiniPOV4 are in halfdane's github repository.  Here we copy those images to the image converter directory.  These commands assume the working directory is still ```~/Downloads/pov``` :

        wget -nv -O images.zip https://github.com/halfdane/minipov4-images/archive/master.zip
        unzip images.zip
        mv -v minipov4-*/*.png ~/sketchbook/MiniPov4_ImageConverter
        rm -rf minipov4-images-master

## Installation is now complete.

---

# The image converter utility program

This is a quick introduction on how to start and use the image converter program.  For more detail, refer to the Adafruit PDF file that was copied to the ```MiniPov4_ImageConverter``` folder.

## Launching the image converter

  1. From The GUI, use the start menu to launch by clicking on:  ```Programming -> Processing```

  2. (I declined the offer to update Processing.)

  3. Dismiss the startup message.

  4. Open the list of sketches by clicking on ```File -> Sketchbook```

  5. Double-click on ```MiniPOV4_ImageConverter```

  6. Click on the ```run``` button.  (It has a triangle icon.)

## Using the image converter

  The image converter is used to load a different image into the MiniPOV4.  This program will read an image in jpeg or png format and download the image to the MiniPOV4.  There is a simple interface with four command buttons.

  * Click on **```[Open Image File]```** to select and load an image file into the converter. **The image must be 8 pixels high.**  Processing supports four types of images, ( .gif, .jpg, .tga, or .png) images may be loaded. 

  * Click on **```[Save as BIN File]```** to save the currently loaded image to a file in the binary format used by the MiniPOV4.

  * Click on **```[Open BIN File]```** to select and load a binary formatted image file into the converter.

  * Click on **```[Download to MiniPOV4]```** to save the currently loaded image to a temporary file in the binary format used by the MiniPOV4.  Then *avrdude* is used to send the temporary file to the EEPROM in the MiniPOV4.  Finally the temporary file is automatically deleted.

You do not need to think about binary files.  You can simply use the first button to select the image and then use the last button to send that image to the MiniPOV4.

---

# Notes on using the MiniPOV4

After downloading an image, disconnect the MiniPOV4 from the USB cable and turn it off.  This is necessary to reset the MiniPOV4.

When the MiniPOV4 is displaying an image, the bottom pixel is the LED closest to the USB connector.  Pixels of the image are displayed starting on the left and moving to the right.

---

#### [EOF]

