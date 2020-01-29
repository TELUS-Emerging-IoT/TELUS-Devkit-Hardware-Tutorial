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

This version of the shield also has an embedded SIM which can be used as the default by sliding the **SIM <> eSIM** switch to **eSIM**

![alt text](images/KNOLL.jpg)
![alt text](images/RASPBERRY.jpg)

## Setting up the Hardware

1. Connect the LTE Cat-M1 antenna and insert removable eSIM to cellular shield as shown below indicated by red boxes. 

      Additionally, you can also connect GPS antenna if you intended to develop application that would use GPS location.

      ![alt text](images/COMPONENTSASSEMBLE!.jpg)

2. Connect the cellular shield and raspberry pi.

      ![alt text](images/SIDEBYSIDE.jpg)
      ![alt text](images/ATTACH.jpg)

3. Connect the USB cable as shown below.

      ![alt text](images/CABLE.jpg)
      ![alt text](images/CABLE2.jpg)      

4. Connect the micro USB cable as power source (included in the package) and ethernet cable for internet connection, optionally, you can also use Wifi for internet connection.

   `This tutorial assumes that Raspian OS has been installed and configured ahead of time, if not, the participant shall install and configure Raspian OS before continuing to the next steps.`
   
5. Power on Raspberry Pi, the shield will also automatically power on as well with a blue indicator light flashing. 
   
   ![alt text](images/BLUE.jpg) 
      
6. Toggle the **SIM <> eSIM** switch on the cellular shield shown below to chose which sim to read from. **SIM** reads the removeable SIM while **eSIM** reads the embedded SIM. 

   ![alt text](images/SIMMUX.jpg) 
   
7. If you are on Raspian OS desktop, open Terminal to verify USB ports are mounted.

   Command to verify USB ports are mounted: `ls -l /dev/tty*`
   
   ![alt text](images/shield_on_and_usb_ports_mounted.png) 
   
   You will also notice that `/dev/ttyS0` (serial) is available. If you don't see `/dev/ttyS0` when you `ls -l /dev/tty*` then you need to enable Serial via [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

8. Alternatively, you can also connect (login) to Raspberry Pi using SSH via Terminal (mac), Tera Term or Putty (windows). If SSH is not available, you can enable SSH via [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

## Connecting the Modem

Since we are using a customized cellular IoT shield, we will need to install the appropriate dialer. If this is for the L-Spark Accelerator you can get the script from Watchdox 
[telus_azure_example.zip](https://blackberry.watchdox.com/ngdox/download/7c8ee964-e4d7-4155-bf23-38543cfb3446) 

For other cases you can get the installation script by issuing the following command
```
wget https://raw.githubusercontent.com/sixfab/Sixfab_PPP_Installer/master/ppp_installer/install.sh
```

### Downloading the PPP dialer:
If you are not part of the L-Spark Accelerator ignore step 1.

1. After you have aqcuired the telus_azure_example.zip, you will need to extract it to a local directory.

2. Enter the telus_azure_example/Sixfab_PPP_Installer/ppp_installer/ folder 
```
cd telus_azure_example/Sixfab_PPP_Installer_telus/ppp_installer/
```
and execute the install.sh file as root.

```
sudo chmod +x install.sh
sudo ./install.sh
```
3. This will begin a series of installation prompts

### Installing the PPP dialer:

You may refer to the following video or work through the steps below:
[![Modem Guide](images/play.png)](https://youtu.be/HkHLNoD_Zog)

1. For the first prompt select **Cellular IoT App Shield Telus Edition** then select **Cat-M1** as the LTE technology. 

 ![alt text](images/SEL.png)

2. Set APN to **pp.telus.com**

3. Device communication port can be **ttyS0** or **ttyUSB3**

 ![alt_text](images/kernel_and_apn_settings.png)
      
 ![alt_text](images/serial_port.png)      
       
4. Edit **/etc/chatscripts/chat-connect**

      Command: `sudo nano /etc/chatscripts/chat-connect`
      
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

5. If you want change the port (**ttyS0** or **ttyUSB3**) that PPP dialer use, you can edit **/etc/ppp/peers/provider**

      ![alt text](images/peers_providers.png)

6. To connect, the command to use is `sudo pon` and you will see similar to below once modem successfully connected to TELUS Cat-M1 network.

      ![alt text](images/peers_providers-2.png)
      ![alt text](images/peers_providers-1.png)
      
7. Once you have an ip address you must set the default connector to be the ppp0 adapter, enter the following into the console
```
sudo ip route add default via 10.64.64.64
```
This will make the ppp0 modem the default network device

8. You can test the connection speed by pinging google servers or opening a browser
```
ping google.com
```
### (Optional) Connect using QMI, alternative to PPP dialer

If you want to use QMI, head back to [Sixfab Tutorial](https://sixfab.com/tutorials/) page and follow the instruction on how to install QMI [here](https://sixfab.com/qmi-interface-with-3g-4g-lte-base-shield-v2/), though its for 3G/4G it seems to be working for Cat-M1.

https://sixfab.com/qmi-interface-with-3g-4g-lte-base-shield-v2/

After installation, the command to connect is: `sudo ./quectel-CM -s pp.telus.com`, below is the output of the command. Notice at the bottom there is a **Too few arguments** messages. I am not sure what is causing this error (have to check with Sixfab) but regardless the modem is connected.

![alt text](images/qmi_connect.png)

## Hardware Information

**Layout**

![alt text](images/Pinout.png)

**Electrical Pinout**

![alt text](images/TelusPinout.jpg)

**Pin Descriptions**

| Pin Number    | BCM Pin       | Pin Name  | Description|
| ------------- |:-------------| :-----|-------------|
| 2 | 5V | 5V PWR |This pin is connected to the 5V power net|
| 3 | GPIO 2 | SDA |I2C Serial Data|
| 4 | 5V | 5V PWR |This pin is connected to the 5V power net|
| 5 | GPIO 3 |  SCL | I2C Serial Clock|
| 7 | GPIO 4 |  1-WIRE | Data line for 1-Wire sensors.|
| 8 | UART RX |  BG96 TX | This pin functions as the serial data input to the module for UART communication.|
| 10 | UART TX |  BG96 RX | This pin functions as the serial data output to the module for UART communication. |
| 12 | GPIO 18 | BG96 POWER ENABLE | BG96 3.8V Power regulator control. Normally pulled-up, when this pin is driven LOW, BG96's power will cut off.|
| 13 | GPIO 27 |  USER LED | Active HIGH, to switch on the USER LED, pin state must be HIGH. |
| 15 | GPIO 22 |  BG96 STATUS | The STATUS pin is used to indicatr the operation status of the BG96 module. It will output High level when the module is powered on. |
| 16| GPIO 23 | BG96 RI | When BG96 had URC to report, RI signal will wake up host. Please refer to Chapter 3.14 of the BG96 Datasheet for details about RI behaviour. |
| 18 | GPIO 24 |  BG96 APREADY | AP_READY will detect the sleep state of the host (can be configured to HIGH level or LOW level detection). Please refer to AT+QCFG="apready" command for details.|
| 22 | GPIO 25 |USER BUTTON | This pin is pulled-up by default. when the button is pressed, the pin is switched to LOW. |
| 29 | GPIO 5 |  BG96 PWRKEY | The module can be turned on by driving the pin BG96 PWRKEY to a HIGH-level voltage for more than 500 ms then pulling it down. You can apply the same process to power down the module if already powered up.|
| 31 | GPIO 6 |  RELAY | Relay control pin. This relay is active high.|
| 32 | GPIO 12 | USER LED | User LED (blue)|
| 33 | GPIO 13 | OPTO #2 | When the voltage in the range 3.3-1.2V is applied from the IN-2 input, this pin goes to a LOW state. Default state is HIGH.|
| 35 | GPIO 19 | OPTO #1 | When the voltage in the range 3.3-1.2V is applied from the IN-1 input, this pin goes to a LOW state. Default state is HIGH.|
| 6,9,14,20,25,30,34,39 | GND |  GND | These pins are connected to ground |


**Electrical Characteristics of Pins**


| Pin Number    | BCM Pin       | Pin Name  | Description|Min|Typical|Max|Unit|
| ------------- |:-------------| :-----|-------------|-----|-------|---|----|
| 2 | 5V | 5V PWR |Power Supply|4.8|5|5.25|V|
| 3 | GPIO 2 | SDA |I2C Data|3|3.3|3.6|V|
| 4 | 5V | 5V PWR |Power Supply|3|3.3|3.6|V|
| 5 | GPIO 3 |  SCL | I2C Clock |3|3.3|3.6|V|
| 7 | GPIO 4 |  1-WIRE |1-Wire Data|3|3.3|3.6|V|
| 8 | UART RX |  BG96 TX | UART|3|3.3|3.6|V|
| 10 | UART TX |  BG96 RX | UART|3|3.3|3.6|V|
| 12 | GPIO 18 | BG96 POWER ENABLE | Output|3|3.3|3.6|V|
| 13 | GPIO 27 |  USER LED | Output|3|3.3|3.6|V|
| 15 | GPIO 22 |  BG96 STATUS |Input |3|3.3|3.6|V|
| 16| GPIO 23 | BG96 RI | Input |3|3.3|3.6|V|
| 18 | GPIO 24 |  BG96 APREADY | Input|3|3.3|3.6|V|
| 22 | GPIO 25 |USER BUTTON | Input|3|3.3|3.6|V|
| 29 | GPIO 5 |  BG96 PWRKEY | Output|3|3.3|3.6|V|
| 31 | GPIO 6 |  RELAY | Output |3|3.3|3.6|V|
| 33 | GPIO 13 | OPTO #2 | Input|3|3.3|3.6|V|
| 35 | GPIO 19 | OPTO #1 | Input|3|3.3|3.6|V|

**Schematic and BG96 documents**

You can download the schematic of Raspberry Pi Cellular IoT Application Shield from here: https://github.com/sixfab/Sixfab_RPi_CellularIoT_Library/tree/master/documents

### Hope this helps.
