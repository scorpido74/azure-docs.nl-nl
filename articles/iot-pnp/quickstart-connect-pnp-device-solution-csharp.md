---
title: Interactie met een IoT Plug and Play Preview-apparaat dat is aangesloten op uw Azure IoT-oplossing | Microsoft Documenten
description: Gebruik C# (.NET) om verbinding te maken met en te communiceren met een IoT Plug and Play Preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76963968"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Snelstart: interactie met een IoT Plug and Play Preview-apparaat dat is aangesloten op uw oplossing (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview vereenvoudigt IoT doordat u communiceren met de mogelijkheden van een apparaat zonder kennis te hebben van de onderliggende apparaatimplementatie. Deze quickstart laat zien hoe u C# (met .NET) gebruiken om verbinding te maken met een IoT Plug and Play-apparaat dat is verbonden met uw oplossing en bestuurt.

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, moet u .NET Core (2.x.x of 3.x.x) op uw ontwikkelingsmachine installeren. U de gewenste versie van de .NET Core SDK downloaden voor meerdere platforms van [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

U de versie van .NET die op uw ontwikkelingsmachine staat verifiëren door de volgende opdracht in een lokaal terminalvenster uit te voeren: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT-hubverbindingstekenreeks_ voor uw hub te krijgen (opmerking voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u een voorbeeldomgevingssensor die in C# is geschreven als het IoT Plug and Play-apparaat. In de volgende instructies ziet u hoe u het apparaat installeert en uitvoert:

1. Open een terminalvenster in de map van uw keuze. Voer de volgende opdracht uit om de [GitHub-opslagplaats Azure IoT-samples voor C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) op deze locatie te klonen:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Dit terminalvenster wordt nu gebruikt als _terminal voor uw apparaat._ Ga naar de map van uw gekloonde opslagplaats en navigeer naar de map **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample.**

1. De _tekenreeks voor apparaatverbinding_configureren:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Bouw de benodigde pakketten en voer het voorbeeld uit met de volgende opdracht:

    ```cmd\sh
        dotnet run
    ```

1. U ziet berichten waarin staat dat het apparaat met succes is geregistreerd en wacht op updates vanuit de cloud. Dit geeft aan dat het apparaat nu klaar is om opdrachten en eigenschapsupdates te ontvangen en is begonnen met het verzenden van telemetriegegevens naar de hub. Sluit deze terminal niet, u hebt deze later nodig om te bevestigen dat de servicemonsters ook werkten.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een voorbeeld van IoT-oplossing in C# om te communiceren met het voorbeeldapparaat.

1. Open een ander terminalvenster (dit wordt uw _serviceterminal)._ Ga naar de map van uw gekloonde opslagplaats en navigeer naar de **map /azure-iot-samples-csharp/digitaltwin/Samples/service.**

1. Configureer de Reeks voor de Verbinding van de _IoT-hub_ en _de apparaat-id_ zodat de service verbinding kan maken met beide:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de terminal hebt aangesloten, zag u het volgende bericht met de onlinestatus:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Ga naar de _serviceterminal_ en gebruik de volgende opdrachten om het voorbeeld uit te voeren voor informatie over het leesapparaat:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Schuif in de uitvoer van `environmentalSensor` de _serviceterminal_ naar de component. U ziet `state` dat de eigenschap, die wordt gebruikt om aan te geven of het apparaat online is, is gemeld als _waar:_

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
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

1. Gebruik de volgende opdrachten om het voorbeeld uit te voeren voor het bijwerken van de eigenschap:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. De uitvoer van de _serviceterminal_ toont de bijgewerkte apparaatinformatie. Schuif naar `environmentalSensor` de component om de nieuwe helderheidswaarde van 42 te zien.

    ```json
        "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

1. Ga naar de terminal van uw _apparaat,_ u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Ga terug naar uw _serviceterminal_ en voer de onderstaande opdrachten uit om de apparaatgegevens opnieuw te krijgen, om te bevestigen dat de accommodatie is bijgewerkt.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. In de uitvoer van `environmentalSensor` de _serviceterminal,_ onder het onderdeel, ziet u dat de bijgewerkte helderheidswaarde is gerapporteerd. Opmerking: het kan even duren voordat het apparaat de update heeft voltooid. U deze stap herhalen totdat het apparaat de eigenschapupdate daadwerkelijk heeft verwerkt.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

1. Ga naar de _serviceterminal_ en stel de volgende variabelen in om te bepalen welke opdracht u moet aanroepen:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Gebruik de volgende opdrachten om het voorbeeld uit te voeren voor het aanroepen van de opdracht:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. De uitvoer in de _serviceterminal_ moet de volgende bevestiging weergeven:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Ga naar de terminal van het _apparaat,_ u ziet dat de opdracht is erkend:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat aansluiten op een IoT-oplossing. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug and Play-apparaten:

> [!div class="nextstepaction"]
> [How-to: Verbinding maken met en communiceren met een apparaat](howto-develop-solution.md)
