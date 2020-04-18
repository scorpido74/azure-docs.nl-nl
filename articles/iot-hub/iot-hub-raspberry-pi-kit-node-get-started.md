---
title: Raspberry Pi verbinden met Azure IoT Hub in de cloud (Node.js)
description: Lees in deze zelfstudie hoe u Raspberry Pi instelt en verbindt met Azure IoT Hub voor Raspberry Pi om gegevens naar het Azure-cloudplatform te verzenden.
author: wesmc7777
manager: eliotgra
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi send data to cloud, raspberry pi to cloud
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.openlocfilehash: 3175956e35603cc4ad3a938f3d316c0af8f2d227
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640536"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry Pi verbinden met Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In deze zelfstudie begin je met het leren van de basisprincipes van het werken met Raspberry Pi die Raspbian draait. Vervolgens leert u hoe u uw apparaten naadloos verbinden met de cloud met Azure [IoT Hub.](about-iot-hub.md) Ga voor Windows 10 IoT Core-voorbeelden naar het [Windows Dev Center](https://www.windowsondevices.com/).

Heb je nog geen kit? Probeer [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Of koop [hier](https://azure.microsoft.com/develop/iot/starter-kits)een nieuwe kit.

## <a name="what-you-do"></a>Wat je doet

* Maak een IoT-hub.

* Registreer een apparaat voor Pi in uw IoT-hub.

* Raspberry Pi instellen.

* Voer een voorbeeldtoepassing uit op Pi om sensorgegevens naar uw IoT-hub te verzenden.

## <a name="what-you-learn"></a>Wat u leert

* Een Azure IoT-hub maken en uw nieuwe tekenreeks voor apparaatverbinding krijgen.

* Hoe maak je Pi verbinding met een BME280 sensor.

* Sensorgegevens verzamelen door een voorbeeldtoepassing op Pi uit te voeren.

* Sensorgegevens verzenden naar uw IoT-hub.

## <a name="what-you-need"></a>Wat u nodig hebt

![Wat u nodig hebt](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Een Raspberry Pi 2 of Raspberry Pi 3 board.

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Een monitor, een USB-toetsenbord en muis die verbinding maakt met Pi.

* Een Mac of pc waarop Windows of Linux wordt uitgevoerd.

* Een internetverbinding.

* Een microSD-kaart van 16 GB of hoger.

* Een USB-SD-adapter of microSD-kaart om de afbeelding van het besturingssysteem op de microSD-kaart te branden.

* Een 5-volt 2-amps voeding met de 6-voet micro USB-kabel.

De volgende items zijn optioneel:

* Een geassembleerde Adafruit BME280 temperatuur-, druk- en vochtigheidssensor.

* Een broodplank.

* 6 F/M jumper draden.

* Een diffuus 10 mm LED.

> [!NOTE]
> Als u de optionele items niet hebt, u gesimuleerde sensorgegevens gebruiken.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi instellen

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installeer het Raspbian-besturingssysteem voor Pi

Bereid de microSD-kaart voor op de installatie van de Raspbian-afbeelding.

1. Download Raspbian.

   a. [Raspbian Buster met desktop](https://www.raspberrypi.org/downloads/raspbian/) (het .zip-bestand).

   b. Haal de Raspbian-afbeelding naar een map op uw computer.

2. Installeer Raspbian op de microSD-kaart.

   a. [Download en installeer de Etcher SD-kaart brander nut](https://etcher.io/).

   b. Voer Etcher uit en selecteer de Raspbian-afbeelding die u in stap 1 hebt geëxtraheerd.

   c. Selecteer het microSD-kaartstation. Etcher kan al de juiste schijf hebben geselecteerd.

   d. Klik op Flash om Raspbian op de microSD-kaart te installeren.

   e. Verwijder de microSD-kaart van uw computer wanneer de installatie is voltooid. Het is veilig om de microSD-kaart direct te verwijderen omdat Etcher de microSD-kaart automatisch uitwerpt of loskoppelt na voltooiing.

   f. Plaats de microSD-kaart in Pi.

### <a name="enable-ssh-and-i2c"></a>SSH en I2C inschakelen

1. Sluit Pi aan op de monitor, het toetsenbord en de muis.

2. Start Pi en meld je `pi` aan bij Raspbian met de gebruikersnaam en `raspberry` als wachtwoord.

3. Klik op het Raspberry-pictogram > **Voorkeuren** > **Raspberry Pi-configuratie**.

   ![Het menu Raspbian Preferences](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Stel op het tabblad **Interfaces** **I2C** en **SSH** **in op Inschakelen**en klik op **OK**. Als u geen fysieke sensoren hebt en gesimuleerde sensorgegevens wilt gebruiken, is deze stap optioneel.

   ![I2C en SSH inschakelen op Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Om SSH en I2C in te schakelen, u meer referentiedocumenten vinden op [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) en [Adafruit.com.](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c)

### <a name="connect-the-sensor-to-pi"></a>Sluit de sensor aan op Pi

Gebruik de breadboard- en jumperdraden om een LED en een BME280 als volgt op Pi aan te sluiten. Als u de sensor niet hebt, [slaat u deze sectie over.](#connect-pi-to-the-network)

![De Raspberry Pi en sensoraansluiting](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

De BME280-sensor kan temperatuur- en vochtigheidsgegevens verzamelen. De LED knippert wanneer het apparaat een bericht naar de cloud stuurt.

Gebruik voor sensorpennen de volgende bedrading:

| Start (Sensor & LED)     | Einde (Bestuur)            | Kabelkleur   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3.3V PWR (Pin 1)       | Witte kabel   |
| GND (Pin 7G)             | GND (Pin 6)            | Bruine kabel   |
| SDI (Pin 10G)            | I2C1 SDA (Pin 3)       | Rode kabel     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Oranje kabel  |
| LED VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Witte kabel   |
| LED GND (Pin 17F)        | GND (Pin 20)           | Zwarte kabel   |

Klik hier om [Raspberry Pi 2 & 3-pins toewijzingen](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) te bekijken voor uw referentie.

Nadat u BME280 met succes hebt verbonden met uw Raspberry Pi, moet deze zijn zoals hieronder afbeelding.

![Verbonden Pi en BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Pi verbinden met het netwerk

Schakel Pi in met de micro-USB-kabel en de voeding. Gebruik de Ethernet-kabel om Pi aan te sluiten op uw bekabelde netwerk of volg de [instructies van de Raspberry Pi Foundation](https://www.raspberrypi.org/documentation/configuration/wireless/) om Pi aan te sluiten op uw draadloze netwerk. Nadat uw Pi met succes is verbonden met het netwerk, moet u een notitie maken van het [IP-adres van uw Pi.](https://www.raspberrypi.org/documentation/remote-access/ip-address.md)

![Verbonden met bekabeld netwerk](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Zorg ervoor dat Pi is verbonden met hetzelfde netwerk als uw computer. Als uw computer bijvoorbeeld is verbonden met een draadloos netwerk terwijl Pi is verbonden met een bekabeld netwerk, ziet u mogelijk het IP-adres niet in de devdisco-uitvoer.

## <a name="run-a-sample-application-on-pi"></a>Een voorbeeldtoepassing uitvoeren op Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Voorbeeldtoepassing klonen en de vereistepakketten installeren

1. Maak verbinding met uw Raspberry Pi met een van de volgende SSH-clients vanaf uw hostcomputer:

   **Windows-gebruikers**

   a. Download en installeer [PuTTY](https://www.putty.org/) voor Windows.

   b. Kopieer het IP-adres van uw Pi naar de sectie Hostname (of IP-adres) en selecteer SSH als verbindingstype.

   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac- en Ubuntu-gebruikers**

   Gebruik de ingebouwde SSH-client op Ubuntu of macOS. Het kan nodig `ssh pi@<ip address of pi>` zijn om pi via SSH te verbinden.

   > [!NOTE]
   > De standaardgebruikersnaam `pi` is en `raspberry`het wachtwoord is .

2. Installeer Node.js en NPM op je Pi.

   Controleer eerst je Node.js versie.

   ```bash
   node -v
   ```

   Als de versie lager is dan 10.x of als er geen Node.js op uw Pi staat, installeert u de nieuwste versie.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Kloon de voorbeeldtoepassing.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Installeer alle pakketten voor het monster. De installatie omvat Azure IoT-apparaat SDK, BME280 Sensor-bibliotheek en Wiring Pi-bibliotheek.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Het kan enkele minuten duren voordat dit installatieproces is voltooid, afhankelijk van uw netwerkverbinding.

### <a name="configure-the-sample-application"></a>De voorbeeldtoepassing configureren

1. Open het config-bestand door de volgende opdrachten uit te voeren:

   ```bash
   nano config.json
   ```

   ![Config-bestand](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Er zijn twee items in dit bestand die u configureren. De eerste `interval`is , die het tijdsinterval (in milliseconden) tussen berichten die naar de cloud worden verzonden, definieert. De tweede `simulatedData`is , dat is een Booleaanse waarde voor het al dan niet gebruiken van gesimuleerde sensorgegevens.

   Als u **de sensor niet hebt,** stelt u de `simulatedData` waarde in om de voorbeeldtoepassing gesimuleerde sensorgegevens te `true` laten maken en te gebruiken.

   *Opmerking: Het i2c-adres dat in deze zelfstudie wordt gebruikt, is standaard 0x77. Afhankelijk van uw configuratie kan het ook 0x76 zijn: als u een i2c-fout tegenkomt, probeert u de waarde te wijzigen in 118 en kijk of dat beter werkt. Om te zien welk adres wordt `sudo i2cdetect -y 1` gebruikt door uw sensor, draait u in een shell op de raspberry pi*

2. Opslaan en afsluiten door Control-O > Enter > Control-X te typen.

### <a name="run-the-sample-application"></a>De voorbeeldtoepassing uitvoeren

Voer de voorbeeldtoepassing uit door de volgende opdracht uit te voeren:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Zorg ervoor dat u de tekenreeks voor de apparaatverbinding kopieert en plakt in de afzonderlijke aanhalingstekens.

Als het goed is, ziet u de volgende uitvoer met de sensorgegevens en de berichten die naar uw IoT-hub worden verzonden.

![Uitvoer: sensorgegevens die van Raspberry Pi naar uw IoT Hub worden verzonden](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lees de berichten die door uw hub zijn ontvangen

Een manier om berichten te controleren die door uw IoT-hub vanaf uw apparaat zijn ontvangen, is door de Azure IoT-hulpprogramma's voor Visual Studio-code te gebruiken. Zie [Azure IoT-hulpprogramma's voor Visual Studio-code gebruiken om berichten tussen uw apparaat en IoT Hub te verzenden en te ontvangen.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Ga door naar de volgende sectie voor meer manieren om gegevens te verwerken die door uw apparaat worden verzonden.

## <a name="next-steps"></a>Volgende stappen

U hebt een voorbeeldtoepassing uitgevoerd om sensorgegevens te verzamelen en naar uw IoT-hub te verzenden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
