---
title: Interactie met IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing - Java | Microsoft Docs
description: Gebruik Java om verbinding te maken met een IoT Plug en Play Preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 752d1d925c55ed79c7eb1673c6602adb9c2371fb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320983"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Quickstart: Interactie met een IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug en Play Preview vereenvoudigt IoT door u te laten communiceren met de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u Java gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

## <a name="prerequisites"></a>Vereisten

Als u deze quickstart wilt uitvoeren, moet Java SE 8 op uw ontwikkelcomputer zijn geïnstalleerd. U moet ook Maven 3 installeren.

Zie [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) in de Microsoft Azure IoT Device SDK for Java voor meer informatie over het instellen van deze vereisten.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub (dit is voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u een voorbeeld van een omgevingssensor die in Java is geschreven als IoT Plug en Play-apparaat. De volgende instructies laten zien hoe u het apparaat installeert en uitvoert:

1. Open een terminalvenster in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT Samples for Java](https://github.com/Azure-Samples/azure-iot-samples-java) te klonen op deze locatie:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Dit terminalvenster wordt nu gebruikt als uw _apparaat_terminal. Ga naar de map van de gekloonde opslagplaats en ga naar de map **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample**. Om de vereiste bibliotheken te installeren en de toepassing voor het gesimuleerde apparaat te compileren, voert u de volgende opdracht uit:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configureer de _device connection string_ (verbindingsreeks voor het apparaat):

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. ZVoer dan de volgende opdracht uit om het voorbeeld uit de apparaatmap uit te voeren.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. U ziet berichten met de mededeling dat het apparaat is verbonden, dat er verschillende installatiestappen worden uitgevoerd, dat er wordt gewacht op service-updates, gevolgd door telemetrielogboeken. Dit geeft aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen. Sluit deze terminal niet. U hebt deze later nodig om te bevestigen dat de servicevoorbeelden werkten.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een IoT-voorbeeldoplossing in Java om te communiceren met het voorbeeld-apparaat.

1. Open een ander terminalvenster (dit is uw _service_terminal). Ga naar de map van de gekloonde opslagplaats en ga naar de map **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample**.

1. Om de vereiste bibliotheken te installeren en het servicevoorbeeld te compileren, voert u de volgende opdracht uit:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configureer de _IoT-hub-verbindingsreeks_ en _apparaat-ID_ zodat de service verbinding kan maken met het volgende:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de terminal hebt aangesloten, is een van de uitvoerberichten het volgende bericht om de online status aan te geven. De eigenschap `state`, die wordt gebruikt om aan te geven of het apparaat online is, is _true_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Ga naar de terminal _service_ en gebruik de volgende opdracht om het servicevoorbeeld voor het lezen van apparaatgegevens uit te voeren:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Ga in de uitvoer van de terminal _service_ naar het onderdeel `environmentalSensor`. U ziet dat de eigenschap `state` is gerapporteerd als _true_:
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Een schrijfbare eigenschap bijwerken

1. Ga naar de terminal _service_ en stel de volgende variabelen in om op te geven welke eigenschap moet worden bijgewerkt:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Gebruik de volgende opdracht om het servicevoorbeeld voor het bijwerken van de eigenschap uit te voeren:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. De uitvoer van de terminal _service_ toont de bijgewerkte apparaatgegevens. Ga naar het onderdeel `environmentalSensor` om de nieuwe helderheidswaarde van 42 weer te geven.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Ga naar uw terminal _apparaat_, u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Ga terug naar de terminal _service_ en voer de onderstaande opdracht uit om de apparaatgegevens opnieuw op te halen om te bevestigen dat de eigenschap is bijgewerkt.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. In de uitvoer van de terminal _service_ onder het onderdeel `environmentalSensor` ziet u dat de bijgewerkte helderheidswaarde is gerapporteerd. Opmerking: het kan enige tijd duren voordat het apparaat de update heeft voltooid. U kunt deze stap herhalen totdat het apparaat daadwerkelijk de update van de eigenschap heeft verwerkt.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

1. Ga naar de terminal _service_ en stel de volgende variabelen in om op te geven welke opdracht moeten worden aangeroepen:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Gebruik de volgende opdracht om het servicevoorbeeld voor het aanroepen van de opdracht uit te voeren:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. De uitvoer van de terminal _service_ moet de volgende bevestiging weergeven:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Ga naar het terminal _apparaat_, u ziet dat de opdracht is bevestigd:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-oplossing. Als u meer wilt weten over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten, leest u:

> [!div class="nextstepaction"]
> [Uitleg: Verbinding maken en werken met een apparaat](howto-develop-solution.md)
