---
title: Sluit IoT DevKit AZ3166 aan op een Azure IoT Hub
description: In deze zelfstudie leert u hoe u IoT DevKit AZ3166 instellen en verbinden met Azure IoT Hub, zodat het gegevens naar het Azure-cloudplatform kan verzenden.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 631a20c7bf73aa2af363fdc0019ef24cccc58f9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258589"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 aansluiten op Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

U de [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) gebruiken om IoT-oplossingen (Internet of Things) te ontwikkelen en te prototypen die profiteren van Microsoft Azure-services. Het bevat een Arduino-compatibel bord met rijke randapparatuur en sensoren, een open-source board pakket, en een rijke [sample gallery](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Wat u leert

* Zo maak je een IoT-hub en registreer je een apparaat voor de MXChip IoT DevKit.
* De IoT DevKit verbinden met Wi-Fi en de IDo-hub-verbindingstekenreeks configureren.
* Hoe u de telemetriegegevens van de DevKit-sensor naar uw IoT-hub verzenden.
* Hoe de ontwikkelomgeving voor te bereiden en toepassingen te ontwikkelen voor de IoT DevKit.

Heb je nog geen DevKit? Probeer de [DevKit simulator](https://azure-samples.github.io/iot-devkit-web-simulator/) of [koop een DevKit](https://aka.ms/iot-devkit-purchase).

U vindt de broncode voor alle DevKit tutorials van [code monsters galerij](https://docs.microsoft.com/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Wat u nodig hebt

* Een MXChip IoT DevKit bord met een Micro-USB kabel. [Haal het nu.](https://aka.ms/iot-devkit-purchase)
* Een computer met Windows 10, macOS 10.10+ of Ubuntu 18.04+.
* Een actief Azure-abonnement. [Activeer een gratis Microsoft Azure-account van 30 dagen.](https://azureinfo.microsoft.com/us-freetrial.html)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Bereid uw hardware voor

Sluit de volgende hardware aan op uw computer:

* DevKit-bord
* Micro-USB-kabel

![Vereiste hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Voer de volgende stappen uit om de DevKit op uw computer aan te sluiten:

1. Sluit het USB-uiteinde aan op uw computer.

2. Sluit het Micro-USB-uiteinde aan op de DevKit.

3. De groene LED voor stroom bevestigt de verbinding.

   ![Hardwareverbindingen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Snelstart: telemetrie van DevKit naar een IoT-hub verzenden

De quickstart maakt gebruik van vooraf gecompileerde DevKit-firmware om de telemetrie naar de IoT Hub te verzenden. Voordat u deze uitvoert, maakt u een IoT-hub en registreert u een apparaat met de hub.

### <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaatidentiteit te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyNodeDevice**: de naam van het apparaat dat u registreert. Gebruik **MyNodeDevice**, zoals weergegeven. Als u een andere naam voor het apparaat kiest, moet u deze naam in de rest van dit artikel gebruiken, en moet u de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Als er een `device-identity`fout wordt uitgevoerd, installeert u de [Azure IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IoT-extensie voegt opdrachten toe die specifiek zijn voor IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS) aan Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Voer de volgende opdrachten uit in Azure Cloud Shell om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

### <a name="send-devkit-telemetry"></a>DevKit-telemetrie verzenden

De DevKit maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verzendt telemetrie over temperatuur en vochtigheid.

1. Download de nieuwste versie van [GetStarted-firmware](https://aka.ms/devkit/prod/getstarted/latest) voor IoT DevKit.

1. Zorg ervoor dat IoT DevKit verbinding maakt met uw computer via USB. Open File Explorer is er een USB-massaopslagapparaat genaamd **AZ3166**.

    ![Windows Verkenner openen](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Sleep en drop de firmware net gedownload in de massa-opslag apparaat en het zal automatisch knipperen.

    ![Firmware kopiëren](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Houd op de DevKit ingedrukt op knop **B,** druk op de **knop Opnieuw instellen** en laat vervolgens knop **B**los. Uw DevKit gaat in de AP-modus. Om dit te bevestigen, geeft het scherm de serviceset-id (SSID) van de DevKit en het IP-adres van de configuratieportal weer.

    ![Knop Opnieuw instellen, knop B en SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![AP-modus instellen](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Gebruik een webbrowser op een ander wi-fi-apparaat (computer of mobiele telefoon) om verbinding te maken met de IoT DevKit SSID die in de vorige stap werd weergegeven. Als het vraagt om een wachtwoord, laat het leeg.

    ![SSID verbinden](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Open **192.168.0.1** in de browser. Selecteer de Wi-Fi waarmee u de Verbinding met IoT DevKit wilt verbinden, typ het Wi-Fi-wachtwoord en plak vervolgens de tekenreeks van de apparaatverbinding waaru eerder nota van hebt genomen. Klik vervolgens op Save.

    ![Configuratie-gebruikersinterface](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > De IoT DevKit ondersteunt slechts 2,4 GHz-netwerk. Check [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) voor meer details.

1. De wifi-informatie en de apparaatverbindingsreeks worden opgeslagen in de IoT DevKit wanneer u de resultaatpagina ziet.

    ![Configuratieresultaat](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Nadat Wi-Fi is geconfigureerd, blijven uw referenties voor die verbinding op het apparaat staan, zelfs als het apparaat is losgekoppeld.

1. De IoT DevKit wordt in een paar seconden opnieuw opgestart. Op het DevKit-scherm ziet u het IP-adres voor de DevKit volgt door de telemetriegegevens, inclusief temperatuur- en vochtigheidswaarde, waarbij het aantal berichten naar Azure IoT Hub wordt verzonden.

    ![WiFi-IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Gegevens verzenden](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Voer de volgende opdracht uit in Azure Cloud Shell als u de telemetriegegevens wilt verifiëren die naar Azure zijn verzonden:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Volg de volgende stappen om de ontwikkelomgeving voor te bereiden op de DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Visual Studio Code installeren met azure IoT Tools-uitbreidingspakket

1. Installeer [Arduino IDE](https://www.arduino.cc/en/Main/Software). Het biedt de nodige toolchain voor het samenstellen en uploaden van Arduino-code.
    * **Windows**: Windows-versie gebruiken. Niet installeren vanuit de App Store.
    * **macOS:** Sleep en zet de uitgepakte `/Applications` **Arduino.app** in map.
    * **Ubuntu:** Unzip het in map, zoals`$HOME/Downloads/arduino-1.8.8`

2. Installeer [Visual Studio Code](https://code.visualstudio.com/), een cross-platform broncode editor met krachtige intellisense, code voltooiing en debugging ondersteuning evenals rijke extensies kunnen worden geïnstalleerd vanaf marktplaats.

3. Start VS Code, zoek naar **Arduino** in de extensie marktplaats en installeer het. Deze extensie biedt verbeterde ervaringen voor het ontwikkelen op Arduino-platform.

    ![Arduino installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Zoek naar [Azure IoT-hulpprogramma's](https://aka.ms/azure-iot-tools) in de extensiemarktplaats en installeer deze.

    ![Azure IoT-hulpprogramma's installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Of gebruik deze directe link:
    > [!div class="nextstepaction"]
    > [Uitbreidingspakket Azure IoT-hulpprogramma's installeren](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Het Azure IoT Tools extension pack bevat de [Azure IoT Device Workbench](https://aka.ms/iot-workbench) die wordt gebruikt om te ontwikkelen en te debuggen op verschillende IoT devkit-apparaten. De [Azure IoT Hub-extensie](https://aka.ms/iot-toolkit), die ook is opgenomen in het Azure IoT Tools-uitbreidingspakket, wordt gebruikt voor het beheren en communiceren met Azure IoT-hubs.

5. VS-code configureren met Arduino-instellingen.

    Klik in Visual Studio Code op **> voorkeuren bestand > Instellingen** (op macOS, Code > voorkeuren > **Instellingen).** Klik vervolgens op het **JSON-pictogram (Openen>)** in de rechterbovenhoek van de pagina *Instellingen.*

    ![Azure IoT-hulpprogramma's installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Voeg de volgende regels toe om Arduino te configureren, afhankelijk van uw platform: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Vervang de tijdelijke aanduiding **{gebruikersnaam}** hieronder door uw gebruikersnaam.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klik `F1` hier om het opdrachtpalet te openen, typ en selecteer **Arduino: Board Manager**. Zoek naar **AZ3166** en installeer de nieuwste versie.

    ![DevKit SDK installeren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>ST-Link-stuurprogramma's installeren

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) is de USB-interface die IoT DevKit gebruikt om te communiceren met uw ontwikkelmachine. U moet het op Windows installeren om de gecompileerde apparaatcode naar de DevKit te flashen. Volg de os-specifieke stappen om de machine toegang te geven tot uw apparaat.

* **Windows**: Download en installeer USB driver van [STMicroelectronics website.](https://www.st.com/en/development-tools/stsw-link009.html)
* **macOS**: Er is geen stuurprogramma vereist voor macOS.
* **Ubuntu**: Voer de opdrachten in de terminal uit en meld u af en meld u aan voor de groepswijziging:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nu bent u helemaal klaar met het voorbereiden en configureren van uw ontwikkelomgeving. Laten we het GetStarted monster bouwen dat je net hebt uitgevoerd.

## <a name="build-your-first-project"></a>Bouw je eerste project

### <a name="open-sample-code-from-sample-gallery"></a>Voorbeeldcode openen uit voorbeeldgalerie

De IoT DevKit bevat een rijke galerij met voorbeelden die u gebruiken om de DevKit te verbinden met verschillende Azure-services.

1. Zorg ervoor dat uw IoT DevKit **niet is verbonden met** uw computer. Start VS Code eerst en sluit de DevKit aan op uw computer.

1. Klik `F1` hierom het opdrachtpalet te openen, de **Werkbank voor Azure IoT-apparaat te**typen en te selecteren: Voorbeelden openen... . Selecteer vervolgens **IoT DevKit** als bord.

1. Zoek op de pagina IoT Workbench Examples **aan de slag** en klik op Voorbeeld **openen**. Hiermee selecteert u het standaardpad om de voorbeeldcode te downloaden.

    ![Voorbeeld openen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Azure IoT-hub en -apparaat inrichten

In plaats van Azure IoT Hub en apparaat in te richten vanuit de Azure-portal, u dit doen in de VS-code zonder de ontwikkelomgeving te verlaten.

1. Klik in het nieuwe `F1` geopende projectvenster om het opdrachtpalet te openen, de **Werkbank voor Azure IoT-apparaat te**typen en te selecteren: Azure Services inrichten... . Volg de stapsgewijze handleiding om de inrichting van uw Azure IoT Hub en het maken van het IoT Hub-apparaat te voltooien.

    ![Voorziening, opdracht](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Als u zich niet hebt aangemeld bij Azure. Volg de pop-upmelding voor het aanmelden.

1. Selecteer het abonnement dat u wilt gebruiken.

    ![Sub selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Selecteer of maak vervolgens een nieuwe [resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Resourcegroep selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Volg de handleiding om een nieuwe Azure IoT Hub te selecteren of te maken in de door u opgegeven brongroep.

    ![IoT-hubstappen selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT-hub selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Geselecteerde IoT-hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. In het uitvoervenster ziet u de Azure IoT Hub ingericht.

    ![IoT-hub ingericht](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecteer of maak een nieuw apparaat in Azure IoT Hub dat u hebt ingericht.

    ![De stappen voor IoT-apparaten selecteren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecteer Ingerichte IoT-apparaat](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Nu is Azure IoT Hub ingericht en er is een apparaat in gemaakt. Ook wordt de tekenreeks voor apparaatverbinding opgeslagen in VS Code voor het later configureren van de IoT DevKit.

    ![Voorziening gedaan](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Apparaatcode configureren en compileren

1. Controleer in de rechtsonder-statusbalk of de **MXCHIP AZ3166** wordt weergegeven als geselecteerde board en seriële poort met **STMicroelectronics** wordt gebruikt.

    ![Selecteer bord en COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Klik `F1` hierom het opdrachtpalet te openen, de **Werkbank voor Azure IoT-apparaat te**typen en te selecteren: Apparaatinstellingen configureren... en selecteer vervolgens **Config-apparaatverbindingstekenreeks > Selecteer De verbindingsreeks voor IoT-hubapparaten**.

1. Houd op DevKit **de knop A**ingedrukt, druk op de **resetknop** en laat vervolgens **de knop A**los. Uw DevKit voert de configuratiemodus in en slaat de verbindingstekenreeks op.

    ![Verbindingsreeks](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klik `F1` nogmaals, typ en selecteer **Azure IoT Device Workbench: Device Code uploaden**. Het begint met compileren en uploaden van de code naar DevKit.

    ![Arduino uploaden](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

De DevKit start opnieuw en begint met het uitvoeren van de code.

> [!NOTE]
> Als er fouten of onderbrekingen zijn, u altijd herstellen door de opdracht opnieuw uit te voeren.

## <a name="test-the-project"></a>Test het project

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>De telemetrie weergeven die naar Azure IoT Hub wordt verzonden

Klik op het pictogram van de stekker op de statusbalk om de seriële monitor te openen:

![Seriële monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

De voorbeeldtoepassing wordt uitgevoerd wanneer u de volgende resultaten ziet:

* In de seriële monitor wordt het bericht weergegeven dat naar de IoT-hub wordt verzonden.
* De LED op de MXChip IoT DevKit knippert.

![Seriële monitoruitvoer](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> U een fout tegenkomen tijdens het testen waarbij de LED niet knippert, de Azure-portal geen binnenkomende gegevens van het apparaat weergeeft, maar het OLED-scherm van het apparaat wordt weergegeven als **Actief...**. Als u het probleem wilt oplossen, gaat u in de Azure-portal naar het apparaat in de IoT-hub en stuurt u een bericht naar het apparaat. Als u de volgende reactie ziet in de seriële monitor in VS Code, is het mogelijk dat directe communicatie vanaf het apparaat wordt geblokkeerd op routerniveau. Controleer firewall- en routerregels die zijn geconfigureerd voor de verbindingsapparaten. Zorg er ook voor dat uitgaande poort 1833 open is.
> 
> FOUT: mqtt_client.c (ln 454): Fout: storingsopening verbinding met eindpunt  
> INFO: >>>Verbindingsstatus: verbinding verbroken  
> FOUT: tlsio_mbedtls.c (ln 604): Onderliggende IO-open is mislukt  
> FOUT: mqtt_client.c (ln 1042): Fout: io_open is mislukt  
> FOUT: iothubtransport_mqtt_common.c (ln 2283): niet-verbinding maken met atcsliothub.azure-devices.net.  
> INFO: >>>Opnieuw verbinden.  
> INFO: IoThub-versie: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>De telemetrie bekijken die wordt ontvangen door Azure IoT Hub

U [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) gebruiken om D2C-berichten (Device-to-cloud) te controleren in IoT Hub.

1. Meld u aan bij [Azure portal,](https://portal.azure.com/)zoek de IoT-hub die u hebt gemaakt.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Klik in het deelvenster **Beleid voor gedeelde toegang** op het beleid voor **iothub-eigenaar**en noteer de verbindingstekenreeks van uw IoT-hub.

    ![Azure IoT Hub-verbindingstekenreeks](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Klik in VS-code op `F1`Azure IoT Hub, typ en selecteer **deze: IoT-hubverbindingstekenreeks instellen**. Kopieer de verbindingstekenreeks erin.

    ![Verbindingstekenreeks Azure IoT-hub instellen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Vouw het azure **iot HUB-apparatenvenster** aan de rechterkant uit, klik met de rechtermuisknop op de apparaatnaam die u hebt gemaakt en selecteer **Ingebouwde gebeurteniseindpunt starten.**

    ![D2C-bericht controleren](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. In het deelvenster **UITVOER** ziet u de binnenkomende D2C-berichten naar de IoT-hub.

    ![D2C-bericht](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>De code bekijken

Het `GetStarted.ino` is de belangrijkste Arduino schets bestand.

![D2C-bericht](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Als u wilt zien hoe apparaattelemetrie naar `utility.cpp` de Azure IoT Hub wordt verzonden, opent u het bestand in dezelfde map. Bekijk [API-verwijzing](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) voor meer informatie over het gebruik van sensoren en randapparatuur op IoT DevKit.

De `DevKitMQTTClient` gebruikte is een wrapper van de **iothub_client** van de [Microsoft Azure IoT SDKs en bibliotheken voor C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) om te communiceren met Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, u in de Veelgestelde vragen van [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) op zoek naar een oplossing of contact met ons opnemen vanuit [Gitter.](https://gitter.im/Microsoft/azure-iot-developer-kit) U ons ook feedback geven door een reactie achter te laten op deze pagina.

## <a name="next-steps"></a>Volgende stappen

U hebt met succes een MXChip IoT DevKit aangesloten op uw IoT-hub en u hebt de vastgelegde sensorgegevens naar uw IoT-hub gestuurd.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
