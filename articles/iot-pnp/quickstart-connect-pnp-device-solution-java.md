---
title: Interactie met een IoT Plug and Play Preview-apparaat dat is aangesloten op uw Azure IoT-oplossing | Microsoft Documenten
description: Gebruik Java om verbinding te maken met en te communiceren met een IoT Plug and Play Preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964631"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Snelstart: interactie met een IoT Plug and Play Preview-apparaat dat is verbonden met uw oplossing (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview vereenvoudigt IoT doordat u communiceren met de mogelijkheden van een apparaat zonder kennis te hebben van de onderliggende apparaatimplementatie. Deze quickstart laat zien hoe je Java gebruiken om verbinding te maken met een IoT Plug and Play-apparaat dat is verbonden met je oplossing.

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, heb je Java SE 8 op je ontwikkelmachine nodig. Je moet ook Maven 3 installeren.

Zie [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) in de Microsoft Azure IoT-apparaat SDK voor Java voor meer informatie over het instellen hiervan.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT-hubverbindingstekenreeks_ voor uw hub te krijgen (opmerking voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u een voorbeeldomgevingssensor die in Java is geschreven als het IoT Plug and Play-apparaat. In de volgende instructies ziet u hoe u het apparaat installeert en uitvoert:

1. Open een terminalvenster in de map van uw keuze. Voer de volgende opdracht uit om de [Azure IoT-opslagplaats voor Java](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub naar deze locatie te klonen:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. Dit terminalvenster wordt nu gebruikt als _terminal voor uw apparaat._ Ga naar de map van uw gekloonde opslagplaats en navigeer naar de map **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample.** Installeer de vereiste bibliotheken en bouw de gesimuleerde apparaattoepassing door de volgende opdracht uit te voeren:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. De _tekenreeks voor apparaatverbinding_configureren:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Voer de volgende opdracht uit om het voorbeeld uit de apparaatmap uit te voeren.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. U ziet berichten waarin staat dat het apparaat is verbonden, verschillende installatiestappen uitvoert en wacht op service-updates, gevolgd door telemetrielogboeken. Dit geeft aan dat het apparaat nu klaar is om opdrachten en eigenschapsupdates te ontvangen en is begonnen met het verzenden van telemetriegegevens naar de hub. Houd het voorbeeld actief terwijl u de volgende stappen uitvoert. Sluit deze terminal niet, u hebt deze later nodig om te bevestigen dat de servicemonsters ook werkten.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een voorbeeld van IoT-oplossing in Java om te communiceren met het voorbeeldapparaat.

1. Open een ander terminalvenster (dit wordt uw _serviceterminal)._ Ga naar de map van uw gekloonde opslagplaats en navigeer naar de map **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample.**

1. Installeer de vereiste bibliotheken en bouw het servicevoorbeeld door de volgende opdracht uit te voeren:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Configureer de Reeks voor de Verbinding van de _IoT-hub_ en _de apparaat-id_ zodat de service verbinding kan maken met beide:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de terminal aansloot, was een van de uitvoerberichten het volgende bericht om de online status aan te geven. De `state` eigenschap, die wordt gebruikt om aan te geven of het apparaat online is, is _waar:_

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Ga naar de _serviceterminal_ en gebruik de volgende opdracht om het servicevoorbeeld uit te voeren voor informatie over het lezen van apparaten:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. Schuif in de uitvoer van `environmentalSensor` de _serviceterminal_ naar de component. U ziet `state` dat de eigenschap is gemeld als _waar:_
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

### <a name="update-a-writable-property"></a>Een beschrijfbare eigenschap bijwerken

1. Ga naar de _serviceterminal_ en stel de volgende variabelen in om te bepalen welke eigenschap moet worden bijgewerkt:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Gebruik de volgende opdracht om het servicevoorbeeld uit te voeren voor het bijwerken van de eigenschap:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. De uitvoer van de _serviceterminal_ toont de bijgewerkte apparaatinformatie. Schuif naar `environmentalSensor` de component om de nieuwe helderheidswaarde van 42 te zien.

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

1. Ga naar de terminal van uw _apparaat,_ u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Ga terug naar uw _serviceterminal_ en voer de onderstaande opdracht uit om de apparaatgegevens opnieuw te krijgen, om te bevestigen dat de accommodatie is bijgewerkt.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. In de uitvoer van `environmentalSensor` de _serviceterminal,_ onder het onderdeel, ziet u dat de bijgewerkte helderheidswaarde is gerapporteerd. Opmerking: het kan even duren voordat het apparaat de update heeft voltooid. U deze stap herhalen totdat het apparaat de eigenschapupdate daadwerkelijk heeft verwerkt.
    
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

1. Ga naar de _serviceterminal_ en stel de volgende variabelen in om te bepalen welke opdracht u moet aanroepen:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Gebruik de volgende opdracht om het servicevoorbeeld uit te voeren voor het aanroepen van de opdracht:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. De uitvoer in de _serviceterminal_ moet de volgende bevestiging weergeven:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Ga naar de terminal van het _apparaat,_ u ziet dat de opdracht is erkend:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat aansluiten op een IoT-oplossing. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug and Play-apparaten:

> [!div class="nextstepaction"]
> [How-to: Verbinding maken met en communiceren met een apparaat](howto-develop-solution.md)
