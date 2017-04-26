---
platform: STM32 Cube 

device: STM32 Nucleo

language: c

---

Run a simple C sample on STM32 Nucleo  
===
---

# Table of Contents
-   [Introduction](#Introduction)
-   [Step 1: Prerequisites](#Prerequisites)
-   [Step 2: Prepare your Device](#PrepareDevice)
-   [Step 3: Build and Run the Sample](#Build)
-   [Next Steps](#NextSteps)


<a name="Introduction"></a>
# Introduction

**About this document**

This document describes how to connect STM32 Nucleo based sensor node to the Microsoft Azure IoT Hub, by leveraging on Azure IoT Device SDK. This multi-step process includes:
-   Configuring Azure IoT Hub
-   Registering STM32 Nucleo to Azure IoT Hub
-   Build and deploy Azure IoT SDK on STM32 Nucleo
 

<a name="Prerequisites"></a>
# Step 1: Prerequisites

You should have the following items ready before beginning the process.

## 1.1 Development environment
- One among these three IDEs installed in your PC: [Keil MDK-ARM][lnk-ide-keil], [IAR Embedded Workbench][lnk-ide-iar], [AC6 System Workbench for STM32][lnk-ide-sw4stm32]
- A serial terminal installed in your PC (e.g. [TeraTerm][lnk-teraterm]) 
- [Setup your IoT hub][lnk-setup-iot-hub]
- [Provision your device and get its credentials][lnk-manage-iot-hub]

#### NOTE
[SystemWorkbench for STM32][lnk-ide-sw4stm32] is the free integrated development environment for STM32, and it is used as reference in this guide.

## 1.2 Hardware components
 - STM32 Nucleo development board ([NUCLEO-F401RE][lnk-nucleo-f4])
 - Wi-Fi expansion board for STM32 Nucleo ([X-NUCLEO-IDW01M1][lnk-nucleo-wifi])
 - Motion MEMS and environmental sensor expansion board for STM32 Nucleo ([X-NUCLEO-IKS01A1][lnk-nucleo-sensors] or [X-NUCLEO-IKS01A2][lnk-nucleo-sensors2]).
 - Dynamic NFC tag expansion board for STM32 Nucleo (optional, [X-NUCLEO-NFC01A1][lnk-nucleo-nfc]) 
 - When the X-NUCLEO-NFC01A1 board is mounted, an NFC enabled Android phone is necessary to set WiFi SSID and Password with a mobile application.
 

<a name="PrepareDevice"></a>
# Step 2: Prepare your Device
Combine STM32 Nucleo with expansion boards as shown in the figure below. Then connect the STM32 Nucleo board to your PC using a mini USB cable.

![][1]
 

<a name="Build"></a>
# Step 3: Build and Run the sample 

<a name="Load"></a>
## 3.1 Build SDK and sample code

1. Download [FP-CLD-AZURE1 Function Pack][lnk-nucleo-fp]. The Function Pack contains all the required drivers to use the STM32 Nucleo board with Wi-Fi, Sensors and NFC expansion boards, together with pre-integrated Microsoft Azure IoT SDK. 
2. Unzip the package and open one of the pre-configured project files available in ```AZURE1_F4_V2.0.0/Projects/Multi/Applications/HTTPS_Azure```, according to the IDE installed (for [SystemWorkbench for STM32][lnk-ide-sw4stm32] project files can be found inside folder ```SW4STM32```). In [SystemWorkbench for STM32][lnk-ide-sw4stm32] select the project from menu ```File -> Import -> Existing Projects into Workspace```; browse folders and select as root directory ```AZURE1_F4_V2.0.0/Projects/Multi/Applications/HTTPS_Azure/SW4STM32/STM32F401RE-Nucleo``` then click ```Finish```.
3. Open  file ```AzureIOTSDKConfig.h``` (inside folder ```AZURE1_F4_V2.0.0/Projects/Multi/Applications/HTTPS_Azure/Inc```) and update ```AZUREIOTHUBCONNECTIONSTRING``` with the credentials retrieved once completed device registration in IoT Hub as described in [Step 1.1][lnk-setup-iot-hub].
4. Build the project according to the selected IDE. In [SystemWorkbench for STM32][lnk-ide-sw4stm32], select the Build configuration with ```Project -> Build Configurations -> Set Active``` based on the sensors expansion board mounted (see picture below); then select ```Project -> Build All``` (or shortcut ```Ctrl+B```).
![][2]
5. Flash the binary to Nucleo board according to the selected IDE. In [SystemWorkbench for STM32][lnk-ide-sw4stm32] select ```Run -> Run Configurations```; click on ```New launch configuration``` (see below) and select available configuration (X-NUCLEO-IKS01A1 or X-NUCLEO-IKS01A2); then in text box ```C/C++ Applications``` type in ```X-NUCLEO-IKS01A1\STM32F4xx-Nucleo.elf``` or ```X-NUCLEO-IKS01A2\STM32F4xx-Nucleo.elf``` depending on the configuration built; then click on ```Apply``` and ```Run```.

![][5]

![][6]



## 3.2 Connect STM32 Nucleo board to a WiFi access point 

Configure your serial terminal (e.g. [TeraTerm][lnk-teraterm]) with the following parameters to visualize log messages from Nucleo board:
- BaudRate : 460800
- Data : 8 bit
- Parity : none
- Stop : 1 bit 
- Flow Control : none

In order to set or to overwrite WiFi access point parameters (SSID and Password), keep pressed USER button in STM32 Nucleo after RESET. 

If NFC board is mounted and if SSID and Password were previously written to NFC with a mobile application (i.e. [ST25 application][lnk-android-st25] for Android can be used), these will be read and used to connect with the Access Point. In order to copy parameters to NFC board, an Android mobile phone equipped with an NFC WriteTag application need to be used. 

Alternatively WiFi parameters can be entered using serial terminal (see below), or directly set in source code by using ```X_CLOUD_SSID```, ```X_CLOUD_KEY``` defines in file ```AzureIOTSDKConfig.h``` (```EMBEDDED_WIFI_PARAMETERS``` has also to be set to 1 in the latter case).

![][7]

After first time WiFi parameters are set, these are permanently written to FLASH memory and used each time the board is RESET, unless USER button is kept pressed (see figure below to use USER or RESET button on STM32 Nucleo). 

![][3]

LED2 onboard Nucleo is turned on once WiFi has joined AP and connection with Azure IoT Hub is established.  

![][4]

## 3.3 Send Device Events to IoT Hub

Once connected to Azure IoT Hub, the application transmits sample data read from sensor board (motion MEMS, temperature, pressure, humidity) using HTTPS protocol. If neither X-NUCLEO-IKS01A1 or X-NUCLEO-IKS01A2 are mounted, the application will transmit a fixed temperature value to IoTHub.

See [Manage IoT Hub][lnk-manage-iot-hub] to learn how to observe in DeviceExplorer the messages IoT Hub receives from STM32 Nucleo.

Messages sent to Azure IoT are also printed in serial terminal interface. 


## 3.3 Receive messages from IoT Hub

See [Manage IoT Hub][lnk-manage-iot-hub] to learn how to send cloud-to-device messages to STM32 Nucleo.
Messages received by STM32 Nucleo are printed over serial terminal interface once received. 
Some cloud-to-device messages are also interpreted by the embedded application: 
- quit : quit application 
- reset : reset board
- led on : LED2 onboard Nucleo on
- led off : LED2 off
- delay <seconds> : change samples frequency 

A QuickStart guide to setup and run the application can also be downloaded [here][lnk-quickstart-st].

<a name="NextSteps"></a>
# Next Steps

You have now learned how to run a sample application that collects sensor data and sends it to your IoT hub. To explore how to store, analyze and visualize the data from this application in Azure using a variety of different services, please click on the following lessons:

-   [Manage cloud device messaging with iothub-explorer]
-   [Save IoT Hub messages to Azure data storage]
-   [Use Power BI to visualize real-time sensor data from Azure IoT Hub]
-   [Use Azure Web Apps to visualize real-time sensor data from Azure IoT Hub]
-   [Weather forecast using the sensor data from your IoT hub in Azure Machine Learning]
-   [Remote monitoring and notifications with Logic Apps]   

[Manage cloud device messaging with iothub-explorer]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-explorer-cloud-device-messaging
[Save IoT Hub messages to Azure data storage]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-store-data-in-azure-table-storage
[Use Power BI to visualize real-time sensor data from Azure IoT Hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi
[Use Azure Web Apps to visualize real-time sensor data from Azure IoT Hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-live-data-visualization-in-web-apps
[Weather forecast using the sensor data from your IoT hub in Azure Machine Learning]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-weather-forecast-machine-learning
[Remote monitoring and notifications with Logic Apps]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps
[lnk-setup-iot-hub]: ../setup_iothub.md
[lnk-manage-iot-hub]: ../manage_iot_hub.md
[lnk-nucleo-f4]:http://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-eval-tools/stm32-mcu-eval-tools/stm32-mcu-nucleo/nucleo-f401re.html
[lnk-nucleo-wifi]:http://www.st.com/content/st_com/en/products/ecosystems/stm32-open-development-environment/stm32-nucleo-expansion-boards/stm32-ode-connect-hw/x-nucleo-idw01m1.html
[lnk-nucleo-sensors]:http://www.st.com/content/st_com/en/products/ecosystems/stm32-open-development-environment/stm32-nucleo-expansion-boards/stm32-ode-sense-hw/x-nucleo-iks01a1.html
[lnk-nucleo-sensors2]:http://www.st.com/content/st_com/en/products/ecosystems/stm32-open-development-environment/stm32-nucleo-expansion-boards/stm32-ode-sense-hw/x-nucleo-iks01a2.html
[lnk-nucleo-nfc]:http://www.st.com/content/st_com/en/products/ecosystems/stm32-open-development-environment/stm32-nucleo-expansion-boards/stm32-ode-connect-hw/x-nucleo-nfc01a1.html
[lnk-nucleo-fp]:http://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html
[lnk-ide-keil]:http://www.keil.com/
[lnk-ide-iar]:http://www.iar.com/
[lnk-ide-sw4stm32]:http://www.openstm32.org/System+Workbench+for+STM32
[lnk-teraterm]:https://ttssh2.osdn.jp
[lnk-android-st25]:https://play.google.com/store/apps/details?id=com.st.demo
[lnk-quickstart-st]:http://www.st.com/content/ccc/resource/sales_and_marketing/presentation/product_presentation/group0/1f/8c/03/3b/a4/da/49/b4/FP-CLD-AZURE1%20quick%20start%20guide/files/fp-cld-azure1_quick_start_guide.pdf/jcr:content/translations/en.fp-cld-azure1_quick_start_guide.pdf

[1]: ./media/nucleo1.png
[2]: ./media/nucleo2_setconf.png
[3]: ./media/nucleo3.png
[4]: ./media/nucleo4.png
[5]: ./media/nucleo5_runconf.png
[6]: ./media/nucleo6_runconfApply.png
[7]: ./media/nucleo6_teraTerm.png



