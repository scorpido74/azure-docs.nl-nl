---
title: Raspberry Pi verbinden met Azure IoT Hub met C | Microsoft Documenten
description: Meer informatie over het instellen en verbinden van Raspberry Pi met Azure IoT Hub voor Raspberry Pi om gegevens naar het Azure-cloudplatform te verzenden
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 94ac75c4165b11e343ce5c31480a511ebf978a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67838779"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi verbinden met Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In deze zelfstudie begin je met het leren van de basisprincipes van het werken met Raspberry Pi die Raspbian draait. Vervolgens leert u hoe u uw apparaten naadloos verbinden met de cloud met Azure [IoT Hub.](about-iot-hub.md) Ga voor Windows 10 IoT Core-voorbeelden naar het [Windows Dev Center](https://www.windowsondevices.com/).

Heb je nog geen kit? Probeer [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Of koop [hier](https://azure.microsoft.com/develop/iot/starter-kits)een nieuwe kit.

## <a name="what-you-do"></a>Wat je doet

* Maak een IoT-hub.

* Registreer een apparaat voor Pi in uw IoT-hub.

* Setup Raspberry Pi.

* Voer een voorbeeldtoepassing uit op Pi om sensorgegevens naar uw IoT-hub te verzenden.

Verbind Raspberry Pi met een IoT-hub die u maakt. Vervolgens voert u een voorbeeldtoepassing uit op Pi om temperatuur- en vochtigheidsgegevens te verzamelen van een BME280-sensor. Ten slotte stuurt u de sensorgegevens naar uw IoT-hub.

## <a name="what-you-learn"></a>Wat u leert

* Een Azure IoT-hub maken en uw nieuwe tekenreeks voor apparaatverbinding krijgen.

* Hoe maak je Pi verbinding met een BME280 sensor.

* Sensorgegevens verzamelen door een voorbeeldtoepassing op Pi uit te voeren.

* Sensorgegevens verzenden naar uw IoT-hub.

## <a name="what-you-need"></a>Wat u nodig hebt

![Wat u nodig hebt](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* De Raspberry Pi 2 of Raspberry Pi 3 board.

* Een actief Azure-abonnement. Als u geen Azure-account hebt, maakt u binnen enkele minuten [een gratis Azure-proefaccount.](https://azure.microsoft.com/free/)

* Een monitor, een USB-toetsenbord en muis die verbinding maken met Pi.

* Een Mac of een pc waarop Windows of Linux wordt uitgevoerd.

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
> Deze items zijn optioneel omdat het codevoorbeeld gesimuleerde sensorgegevens ondersteunt.
>

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Raspberry Pi instellen

Stel nu de Raspberry Pi in.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installeer het Raspbian-besturingssysteem voor Pi

Bereid de microSD-kaart voor op de installatie van de Raspbian-afbeelding.

1. Download Raspbian.

   1. [Download Raspbian Stretch with Desktop](https://www.raspberrypi.org/downloads/raspbian/) (het .zip-bestand).

   2. Haal de Raspbian-afbeelding naar een map op uw computer.

2. Installeer Raspbian op de microSD-kaart.

   1. [Download en installeer de Etcher SD-kaart brander nut](https://etcher.io/).

   2. Voer Etcher uit en selecteer de Raspbian-afbeelding die u in stap 1 hebt geëxtraheerd.

   3. Selecteer het microSD-kaartstation. Houd er rekening mee dat Etcher mogelijk al het juiste station heeft geselecteerd.

   4. Klik op Flash om Raspbian op de microSD-kaart te installeren.

   5. Verwijder de microSD-kaart van uw computer wanneer de installatie is voltooid. Het is veilig om de microSD-kaart direct te verwijderen omdat Etcher de microSD-kaart automatisch uitwerpt of loskoppelt na voltooiing.

   6. Plaats de microSD-kaart in Pi.

### <a name="enable-ssh-and-spi"></a>SSH en SPI inschakelen

1. Sluit Pi aan op de monitor, het toetsenbord en de `pi` muis, start `raspberry` Pi en meld je aan bij Raspbian met behulp van de gebruikersnaam en als wachtwoord.
 
2. Klik op het Raspberry-pictogram > **Voorkeuren** > **Raspberry Pi-configuratie**.

   ![Het menu Raspbian Preferences](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Stel op het tabblad **Interfaces** **SPI** en **SSH** **in op Inschakelen**en klik op **OK**. Als u geen fysieke sensoren hebt en gesimuleerde sensorgegevens wilt gebruiken, is deze stap optioneel.

   ![SPI en SSH inschakelen op Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Om SSH en SPI in te schakelen, u meer referentiedocumenten vinden op [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) en [RASPI-CONFIG.](https://www.raspberrypi.org/documentation/configuration/raspi-config.md)
>

### <a name="connect-the-sensor-to-pi"></a>Sluit de sensor aan op Pi

Gebruik de breadboard- en jumperdraden om een LED en een BME280 als volgt op Pi aan te sluiten. Als u de sensor niet hebt, [slaat u deze sectie over.](#connect-pi-to-the-network)

![De Raspberry Pi en sensoraansluiting](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

De BME280-sensor kan temperatuur- en vochtigheidsgegevens verzamelen. En de LED knippert als er een communicatie is tussen apparaat en cloud.

Gebruik voor sensorpennen de volgende bedrading:

| Start (Sensor & LED)     | Einde (Bestuur)            | Kabelkleur   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Pin 5G)         | GPIO 4 (Pin 7)         | Witte kabel   |
| LED GND (Pin 6G)         | GND (Pin 6)            | Zwarte kabel   |
| VDD (Pin 18F)            | 3.3V PWR (Pin 17)      | Witte kabel   |
| GND (Pin 20F)            | GND (Pin 20)           | Zwarte kabel   |
| SCK (Pin 21F)            | SPI0 SCLK (Pin 23)     | Oranje kabel  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Gele kabel  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Groene kabel   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Blauwe kabel    |

Klik hier om [raspberry pi 2 & 3 pintoewijzingen](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) te bekijken voor uw referentie.

Nadat u BME280 met succes hebt verbonden met uw Raspberry Pi, moet deze zijn zoals hieronder afbeelding.

![Verbonden Pi en BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Pi verbinden met het netwerk

Schakel Pi in met de micro-USB-kabel en de voeding. Gebruik de Ethernet-kabel om Pi aan te sluiten op uw bekabelde netwerk of volg de [instructies van de Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) om Pi aan te sluiten op uw draadloze netwerk. Nadat uw Pi met succes is verbonden met het netwerk, moet u een notitie maken van het [IP-adres van uw Pi.](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)

![Verbonden met bekabeld netwerk](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Een voorbeeldtoepassing uitvoeren op Pi

### <a name="sign-into-your-raspberry-pi"></a>Meld je aan bij je Raspberry Pi

1. Gebruik een van de volgende SSH-clients van uw hostcomputer om verbinding te maken met uw Raspberry Pi.
   
   **Windows-gebruikers**
   1. Download en installeer [PuTTY](https://www.putty.org/) voor Windows. 
   1. Kopieer het IP-adres van uw Pi naar de sectie Hostname (of IP-adres) en selecteer SSH als verbindingstype.
   
   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac- en Ubuntu-gebruikers**

   Gebruik de ingebouwde SSH-client op Ubuntu of macOS. Het kan nodig `ssh pi@<ip address of pi>` zijn om pi via SSH te verbinden.
   > [!NOTE]
   > De standaardgebruikersnaam `pi` is , `raspberry`en het wachtwoord is .


### <a name="configure-the-sample-application"></a>De voorbeeldtoepassing configureren

1. Kloon de voorbeeldtoepassing door de volgende opdracht uit te voeren:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Installatiescript uitvoeren:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Als u **geen fysieke BME280 hebt,** u '--gesimuleerde gegevens' gebruiken als opdrachtregelparameter om temperatuur-&vochtigheidsgegevens te simuleren. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>De voorbeeldtoepassing bouwen en uitvoeren

1. Bouw de voorbeeldtoepassing door de volgende opdracht uit te voeren:

   ```bash
   cmake . && make
   ```
   
   ![Uitvoer bouwen](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Voer de voorbeeldtoepassing uit door de volgende opdracht uit te voeren:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Zorg ervoor dat u de tekenreeks voor de apparaatverbinding kopieert en plakt in de afzonderlijke aanhalingstekens.
   >

Als het goed is, ziet u de volgende uitvoer met de sensorgegevens en de berichten die naar uw IoT-hub worden verzonden.

![Uitvoer: sensorgegevens die van Raspberry Pi naar uw IoT Hub worden verzonden](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lees de berichten die door uw hub zijn ontvangen

Een manier om berichten te controleren die door uw IoT-hub vanaf uw apparaat zijn ontvangen, is door de Azure IoT-hulpprogramma's voor Visual Studio-code te gebruiken. Zie [Azure IoT-hulpprogramma's voor Visual Studio-code gebruiken om berichten tussen uw apparaat en IoT Hub te verzenden en te ontvangen.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Ga door naar de volgende sectie voor meer manieren om gegevens te verwerken die door uw apparaat worden verzonden.

## <a name="next-steps"></a>Volgende stappen

U hebt een voorbeeldtoepassing uitgevoerd om sensorgegevens te verzamelen en naar uw IoT-hub te verzenden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
