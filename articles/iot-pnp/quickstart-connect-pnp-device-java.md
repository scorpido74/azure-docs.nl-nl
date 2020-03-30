---
title: IoT Plug and Play Preview-voorbeeldapparaatcode verbinden met IoT-hub | Microsoft Documenten
description: Met Java kun je IoT Plug and Play Preview-voorbeeldapparaatcode bouwen en uitvoeren die verbinding maakt met een IoT-hub. Gebruik het hulpprogramma voor Azure IoT explorer om de informatie weer te geven die door het apparaat naar de hub wordt verzonden.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: aa676dd374eccf2a4b5c4622689ed402c8679e5a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964818"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Snelstart: een voorbeeld van een IoT-toepassing voor plug- en play-voorbeeldapparaten verbinden met IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Met deze quickstart u zien hoe u een voorbeeld van IoT Plug and Play-apparaattoepassing maken, deze aansluiten op uw IoT-hub en het azure IoT explorer-hulpprogramma gebruiken om de informatie te bekijken die naar de hub wordt verzendt. De voorbeeldtoepassing is geschreven in Java en wordt geleverd als onderdeel van de Azure IoT-verzameling voor Java-monsters. Een ontwikkelaar van een oplossing kan het Azure IoT explorer-hulpprogramma gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder dat er apparaatcode hoeft te worden bekeken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, heb je Java SE 8 op je ontwikkelmachine nodig. Je moet ook Maven 3 installeren.

Zie [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) in de Microsoft Azure IoT-apparaat SDK voor Java voor meer informatie over het instellen hiervan.

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT-verkenner installeren

Download en installeer de nieuwste versie van **Azure IoT explorer** van de [repository-pagina](https://github.com/Azure/azure-iot-explorer/releases) van de tool door het MSI-bestand onder 'Assets' te selecteren voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT-hubverbindingstekenreeks_ voor uw hub te krijgen (opmerking voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart bereidt u een ontwikkelomgeving voor die u gebruiken om de Azure IoT-voorbeelden voor Java te klonen en te bouwen.

Open een terminalvenster in de map van uw keuze. Voer de volgende opdracht uit om de [Azure IoT-opslagplaats voor Java](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub naar deze locatie te klonen:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de gekloonde voorbeeldcode om een toepassing te bouwen die een apparaat simuleert dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal terminalvenster naar de map van uw gekloonde opslagplaats en navigeer naar de map **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample.** Voer vervolgens de volgende opdracht uit om de vereiste bibliotheken te installeren en de gesimuleerde apparaattoepassing te bouwen:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. De _tekenreeks voor apparaatverbinding_configureren:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Het voorbeeld van het apparaat uitvoeren

Voer een voorbeeldtoepassing uit om een IoT Plug and Play-apparaat te simuleren dat telemetrie naar uw IoT-hub verzendt. Als u de voorbeeldtoepassing wilt uitvoeren, gebruikt u de volgende opdracht:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

U ziet berichten waarin staat dat het apparaat is verbonden, verschillende installatiestappen uitvoert en wacht op service-updates, gevolgd door telemetrielogboeken. Dit geeft aan dat het apparaat nu klaar is om opdrachten en eigenschapsupdates te ontvangen en is begonnen met het verzenden van telemetriegegevens naar de hub. Houd het voorbeeld actief terwijl u de volgende stappen uitvoert.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT-verkenner gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Selecteer **Instellingen**om ervoor te zorgen dat het hulpprogramma de definities van het interfacemodel van uw apparaat kan lezen. In het menu Instellingen wordt **op het aangesloten apparaat** mogelijk al weergegeven in de plug-en play-configuraties. Als dit niet het zo is, selecteert u **+ Moduledefinitiebron toevoegen** en vervolgens **op het aangesloten apparaat** om deze toe te voegen.

1. Zoek terug op de **overzichtspagina Apparaten** de apparaatidentiteit die u eerder hebt gemaakt. Controleer of de **verbindingsstatus** van het apparaat in Azure IoT-verkenner wordt gemeld als _Verbonden_ (zo niet, druk op **Vernieuwen** totdat dit het geval is). Selecteer het apparaat om meer details weer te geven.

1. Breid de interface uit met **ID-urn:java_sdk_sample:EnvironmentalSensor:1** om de interface en IoT Plug and Play-primitieven te onthullen: eigenschappen, opdrachten en telemetrie.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat aansluiten op een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug and Play-apparaten:

> [!div class="nextstepaction"]
> [How-to: Verbinding maken met en communiceren met een IoT Plug and Play Preview-apparaat](howto-develop-solution.md)
