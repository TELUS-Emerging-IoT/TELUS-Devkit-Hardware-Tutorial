# Secure IoT Accelerator Program

The Secure IoT Accelerator program is intended to enable Canadian Internet of Things (IoT) ventures by providing them with global market reach capability with a Secure by Design technology.

For more information about the accelerator program, visit: https://www.l-spark.com/press-release/l-spark-telus-blackberry-and-solace-launch-accelerator-for-emerging-canadian-iot-ventures/

Youtube: https://www.youtube.com/watch?v=ZL2ADLHl-XQ&feature=youtu.be

IoT technical information session webinar: https://zoom.us/recording/share/PWJ3D-qfgsRB5TrQGfzrThJIY4mG5bUspvZ49OwiZNywIumekTziMw

# Let's get started

This getting started tutorial will help accelerator program participants with setting up the cellular shield, raspberry pi and installing applications required to validate devkit functions and features.

Participants will receive the following hardware when they join the accelerator program:

1. TELUS Cat-M1 cellular shield
2. Raspberry Pi and accessories
3. TELUS Removable eSIM

![alt text](images/cellular_shield_front.jpg)
![alt text](images/rasp_pi.jpg)

## Setting up the Hardware

1. Connect the LTE Cat-M1 antenna and insert removable eSIM to cellular shield as shown below indicated by red boxes. 

      Additionally, you can also connect GPS antenna if you intended to develop application that would use GPS location.

      ![alt text](images/insert_esim_and_connect_antenna.jpg)

2. Connect the cellular shield and raspberry pi.

      ![alt text](images/shield_and_pi_stacked_1.jpg)

3. Connect the USB cable as shown below.

      ![alt text](images/usb_cable_connected_2.jpg)
      ![alt text](images/usb_cable_connected_1.jpg)      

4. Connect the micro USB cable as power source (included in the package) and ethernet cable for internet connection, optionally, you can also use Wifi for internet connection.

   `This tutorial assumes that Raspian OS has been installed and configured ahead of time, if not, the participant shall install and configure Raspian OS before continuing to the next steps.`
   
5. Power on Raspberry Pi. 
      
6. Press **Power button** on the cellular shield shown below in red circle to enable cellular connection and mount USB ports in Raspberry Pi.

   ![alt text](images/press_power_on_button.jpg) 
   
7. If you are on Raspian OS desktop, open Terminal to verify USB ports are mounted.

   Command to verify USB ports are mounted: `ls -l /dev/tty*`
   
   ![alt text](images/shield_on_and_usb_ports_mounted.png) 
   
   You will also notice that `/dev/ttyS0` (serial) is available. If you don't see `/dev/ttyS0` when you `ls -l /dev/tty*` then you need to enable Serial via [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

8. Alternatively, you can also onnect (login) to Raspberry Pi using SSH via Terminal (mac), Tera Term or Putty (windows). If SSH is not available, you can enable SSH via [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

## Connecting the Modem

Since we are using Sixfab cellular shield, we are going to head to [Sixfab Tutorial](https://sixfab.com/tutorials/) page and follow the instruction on how to install [PPP dialer](https://sixfab.com/ppp-installer-for-sixfab-shield/) with some modifications and configurations specific to TELUS Cat-M1 network.

PPP installer for Sixfab Shield: https://sixfab.com/ppp-installer-for-sixfab-shield/

You may refer to the following video or work through the steps below:
[![Modem Guide](images/play.png)](https://youtu.be/HkHLNoD_Zog)

1. Get install.sh file using the terminal.

      Command: `wget https://raw.githubusercontent.com/sixfab/Sixfab_PPP_Installer/master/ppp_installer/install.sh`
      
2. Change the properties of the script so that it is executable.

      Command: `chmod +x install.sh`
      
3. Install the script: install.sh.

      Command: `sudo ./install.sh`

4. After the command has run, a series of questions will needed to be answered.

      a. Select **Cellular IoT App Shield** for Sixfab shield/HAT

      ![alt text](images/install_sh_step3.png)
      
      b. Select **Cat-M1**

      ![alt text](images/install_sh_step4.png)
 
      c. Type **y** to answer "Do you have updated kernel"
 
      ![alt text](images/select3.png)

5. Set APN to **pp.telus.com**. 
      No username and password are needed so please answer **n**. 
      Device communication port can be **ttyS0** or **ttyUSB3**.

      ![alt text](images/select4-5-6.png)
      
6. Please answer **n** for the next question.

      ![alt text](images/select7.png)
      
      
7. Press **ENTER** key and wait for a couple of minutes for the Raspberry Pi to reboot.

      ![alt text](images/enter.png)
       
8. After the Pi reboot, open the terminal again and edit **/etc/chatscripts/chat-connect**.

      Command: `sudo nano /etc/chatscripts/chat-connect`
      
      ![alt text](images/edit1.png)
      
      ![alt text](images/edit2.png)
      
      Replace the content of **/etc/chatscripts/chat-connect** with this:
      
      ```
      
      # /etc/chatscripts/chat-connect
      ABORT "BUSY"
      ABORT "NO CARRIER"
      ABORT "NO DIALTONE"
      ABORT "ERROR"
      ABORT "NO ANSWER"
      TIMEOUT 180
      "" AT
      OK ATE0
      OK AT+QCFG="band",F,80A,80,1
      OK AT+QCFG="nwscanseq",00,1
      OK AT+QCFG="nwscanmode",0,1
      OK AT+QCFG="iotopmode",0,1

      OK AT+COPS=?

      OK AT+CPSMS=0
      OK ATI;+QGMR;+CIMI;+QCCID;+CSUB;+CSQ;+COPS?;+CGREG?;&D2
      OK AT+CGDCONT=?
      OK AT+CGDCONT?
      OK AT+CGDCONT=1,"IP","\T","0.0.0.0",0,0
      OK ATD*99#
      CONNECT
      
      ```

9. If you want to change the port (**ttyS0** or **ttyUSB3**) that PPP dialer use, you can edit **/etc/ppp/peers/provider**.

      ![alt text](images/peers_providers.png)

10. To connect, the command to use is `sudo pon` and you will see similar to below once modem successfully connected to TELUS Cat-M1 network.

      ![alt text](images/sudo_pon_success.png)
      
11. There might be some issues with routing. To view the routing table:

      Command: `route -n`
      
      ![alt text](images/route.png)
      
      In the table, it is noticeable that the WIFT is the main destination used. To use the modem instead, type the command `sudo ip route add default via 10.64.64.64`. Note that the IP of the modem (10.64.64.64) may be different in your computer.
      
      ![alt text](images/add_default.png)
      
12. To confirm that the modem is used, attempt `ping google.com`. You can see that the latency is longer than typical WIFI (business WIFI is usually around 10ms).
      
      ![alt text](images/verify.png)
      
### (Optional) Connect using QMI, alternative to PPP dialer

If you want to use QMI, head back to [Sixfab Tutorial](https://sixfab.com/tutorials/) page and follow the instruction on how to install QMI [here](https://sixfab.com/qmi-interface-with-3g-4g-lte-base-shield-v2/), though its for 3G/4G it seems to be working for Cat-M1.

https://sixfab.com/qmi-interface-with-3g-4g-lte-base-shield-v2/

After installation, the command to connect is: `sudo ./quectel-CM -s pp.telus.com`, below is the output of the command. Notice at the bottom there is a **Too few arguments** messages. I am not sure what is causing this error (have to check with Sixfab) but regardless the modem is connected.

![alt text](images/qmi_connect.png)

## Hardware Information

Sixfab cellular shield information can be found here: https://sixfab.com/product/raspberry-pi-cellular-iot-application-hat/

**Layout**

![alt text](https://sixfab-com.exactdn.com/wp-content/uploads/2018/10/rpi_cellulariot_application_shield_layout-1.png?strip=all&ssl=1)

**Electrical Pinout**

![alt text](https://sixfab-com.exactdn.com/wp-content/uploads/2018/10/rpi_cellulariot_app_shield_pinout-e1545901898330.png?strip=all&w=1170&ssl=1)

**Pin Descriptions**

![alt text](images/shield_pin_description.png)

**Electrical Characteristics of Pins**

![alt text](images/shield_characteristics_pin.png)

**Schematic and BG96 documents**

You can download the schematic of Raspberry Pi Cellular IoT Application Shield from here: https://github.com/sixfab/Sixfab_RPi_CellularIoT_Library/tree/master/documents

### We hope this helps you get started with the Raspberry Pi Starter Kit.
