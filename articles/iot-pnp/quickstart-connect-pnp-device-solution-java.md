---
title: Interactie met een IoT Plug en Play preview-apparaat dat is verbonden met uw Azure IoT-oplossing | Microsoft Docs
description: Gebruik Java om verbinding te maken met een IoT Plug en Play preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76964631"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Snelstartgids: interactie met een IoT Plug en Play preview-apparaat dat is verbonden met uw oplossing (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug en Play preview vereenvoudigt IoT door u te laten communiceren met de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze Quick start ziet u hoe u Java gebruikt om verbinding te maken met en een IoT-Plug en Play apparaat te beheren dat is verbonden met uw oplossing.

## <a name="prerequisites"></a>Vereisten

Als u deze Snelstartgids wilt volt ooien, moet u Java SE 8 op uw ontwikkel computer hebben. U moet ook maven 3 installeren.

Zie [uw ontwikkel omgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) in de Microsoft Azure IOT Device SDK voor Java voor meer informatie over hoe u deze kunt instellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IOT hub-Connection String_ voor uw hub op te halen (Let op later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Het voorbeeld apparaat uitvoeren

In deze Quick Start gebruikt u een voor beeld van een omgevings sensor die in Java is geschreven als IoT Plug en Play-apparaat. De volgende instructies laten zien hoe u het apparaat installeert en uitvoert:

1. Open een Terminal venster in de gewenste map. Voer de volgende opdracht uit om de [Azure IOT-voor beelden voor Java](https://github.com/Azure-Samples/azure-iot-samples-java) github-opslag plaats naar deze locatie te klonen:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Dit Terminal venster wordt nu gebruikt als uw _apparaat_ -Terminal. Ga naar de map van uw gekloonde opslag plaats en navigeer naar de map **/Azure-IOT-samples-Java/Digital-Twin/samples/Device/JdkSample** . Installeer de vereiste bibliotheken en bouw de toepassing gesimuleerde apparaten op door de volgende opdracht uit te voeren:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. De connection string van het _apparaat_configureren:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Voer de volgende opdracht uit om het voor beeld uit te voeren vanuit de map apparaat.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Er worden berichten weer gegeven met de melding dat het apparaat is verbonden, het uitvoeren van verschillende installatie stappen en het wachten op service-updates, gevolgd door telemetrie-Logboeken. Dit geeft aan dat het apparaat nu gereed is om opdrachten en updates van eigenschappen te ontvangen en dat er telemetriegegevens worden verzonden naar de hub. Laat het voor beeld uitvoeren tijdens het uitvoeren van de volgende stappen. Sluit deze terminal niet. u hebt deze later nodig om te bevestigen dat de service voorbeelden ook hebben gewerkt.

## <a name="run-the-sample-solution"></a>De voorbeeld oplossing uitvoeren

In deze Quick Start gebruikt u een IoT-voorbeeld oplossing in Java om te communiceren met het voor beeld-apparaat.

1. Open een ander Terminal venster (dit is uw _service_ Terminal). Ga naar de map van uw gekloonde opslag plaats en navigeer naar de map **/Azure-IOT-samples-java\digital-twin\Samples\service\JdkSample** .

1. Installeer de vereiste bibliotheken en bouw het service-voor beeld door de volgende opdracht uit te voeren:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configureer de _IOT hub-Connection String_ en de _apparaat-id_ zodat de service verbinding kan maken met beide:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de Terminal hebt aangesloten, is een van de uitvoer berichten het volgende bericht om de online status aan te geven. De `state` eigenschap, die wordt gebruikt om aan te geven of het apparaat online is, is _waar_:

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Ga naar de _service_ Terminal en gebruik de volgende opdracht om het service-voor beeld voor het lezen van apparaatgegevens uit te voeren:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Schuif in de Terminal uitvoer van de _service_ naar `environmentalSensor` het onderdeel. U ziet dat de `state` eigenschap is gerapporteerd als _waar_:
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

### <a name="update-a-writable-property"></a>Een Beschrijf bare eigenschap bijwerken

1. Ga naar de _service_ Terminal en stel de volgende variabelen in om op te geven welke eigenschap moet worden bijgewerkt:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Gebruik de volgende opdracht om het service-voor beeld voor het bijwerken van de eigenschap uit te voeren:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. De _service_ Terminal uitvoer toont de bijgewerkte apparaatgegevens. Ga naar het `environmentalSensor` onderdeel om de nieuwe helderheids waarde van 42 weer te geven.

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

1. Ga naar uw _apparaat_ -Terminal, u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Ga terug naar de _service_ Terminal en voer de onderstaande opdracht uit om de apparaatgegevens opnieuw op te halen om te bevestigen dat de eigenschap is bijgewerkt.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. In de _service_ Terminal output, onder het `environmentalSensor` onderdeel, ziet u dat de bijgewerkte helderheids waarde is gerapporteerd. Opmerking: het kan enige tijd duren voordat het apparaat de update heeft voltooid. U kunt deze stap herhalen totdat het apparaat daad werkelijk de update van de eigenschap heeft verwerkt.
    
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

1. Ga naar de _service_ Terminal en stel de volgende variabelen in om op te geven welke opdracht moet worden aangeroepen:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Gebruik de volgende opdracht om het service-voor beeld uit te voeren voor het aanroepen van de opdracht:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. De uitvoer in de _service_ terminal moet de volgende bevestiging weer geven:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Naar de _apparaat_ -Terminal gaat, ziet u dat de opdracht is bevestigd:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-oplossing. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en interactie met een apparaat](howto-develop-solution.md)
