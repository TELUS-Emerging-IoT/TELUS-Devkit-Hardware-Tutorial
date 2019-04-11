# TELUS Dekit for Secure IoT Accelerator Program

The Secure IoT Accelerator program is intended to enable Canadian Internet of Things (IoT) ventures by providing them with global market reach capability with a Secure by Design technology.

This getting started tutorial will help accelerator program participants with setting up the cellular shield, raspberry pi and installing applications required to validate devkit functions and features.

# Let's get started

Participants will receive the following hardware when they join the accelerator program:

1. TELUS Cat-M1 cellular shield
      ![alt text](images/cellular_shield_front.jpg)
      
2. Raspberry Pi and accessories
      ![alt text](images/rasp_pi.jpg)

3. TELUS Removable eSIM

## Setting up the hardware

1. Connect the LTE Cat-M1 antenna and insert removable eSIM to cellular shield as shown below indicated by red boxes. 

      Additionally, you can also connect GPS antenna if you intended to develop application that would use GPS location.

      ![alt text](images/insert_esim_and_connect_antenna.jpg)

2. Connect the cellular shield and raspberry pi.

      ![alt text](images/shield_and_pi_stacked_1.jpg)

3. Connect the USB cable as shown below.

      ![alt text](images/usb_cable_connected_2.jpg)
      ![alt text](images/usb_cable_connected_1.jpg)      

4. Connect the micro USB cable included in the package and ethernet cable.

   `This tutorial assumes that Raspian OS has been installed and configured, if not, the participant shall install and configure Raspian before continuing to the next steps.`
   
5. Power on Raspberry Pi. 
      
6. Press **Power button** on the cellular shield shown below in red circle to enable cellular connection and mount USB ports in Raspberry Pi.

   ![alt text](images/press_power_on_button.jpg) 
   
7. If you are on Raspian OS desktop, open Terminal to verify USB ports are mounted.

   Command to verify USB ports are mounted: `ls -l /dev/tty*`
   
   ![alt text](images/shield_on_and_usb_ports_mounted.png) 
   
   You will also notice that `/dev/ttyS0` (serial) is available. If you don't see `/dev/ttyS0` when you `ls -l /dev/tty*` then you need to enable Serial via [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

8. Alternatively, you can also onnect (login) to Raspberry Pi using SSH via Terminal (mac), Tera Term or Putty (windows). If SSH is not available, you can enable SSH via [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
