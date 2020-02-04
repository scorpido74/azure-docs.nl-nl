---
title: Interactie met een IoT Plug en Play preview-apparaat dat is verbonden met uw Azure IoT-oplossing | Microsoft Docs
description: Gebruik C# (.net) om verbinding te maken met een IOT Plug en Play preview-apparaat dat is verbonden met uw Azure IOT-oplossing.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963968"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Quick Start: interactie met een IoT Plug en Play preview-apparaat dat is verbonden met uwC#oplossing ()

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug en Play preview vereenvoudigt IoT door u te laten communiceren met de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze Quick start ziet u hoe C# u (met .net) verbinding maakt met en een IoT-Plug en Play apparaat beheert dat is verbonden met uw oplossing.

## <a name="prerequisites"></a>Vereisten

Om deze Quick Start te volt ooien, moet u .NET core (2. x. x of 3. x. x) installeren op uw ontwikkel computer. U kunt de gewenste versie van de .NET Core SDK voor meerdere platformen downloaden van [.net core](https://dotnet.microsoft.com/download/dotnet-core/).

U kunt de versie van .NET controleren die zich op uw ontwikkel computer bevindt door de volgende opdracht uit te voeren in een lokaal Terminal venster: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IOT hub-Connection String_ voor uw hub op te halen (Let op later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Het voorbeeld apparaat uitvoeren

In deze Quick Start gebruikt u een voor C# beeld van een omgevings sensor die is geschreven als IOT Plug en Play-apparaat. De volgende instructies laten zien hoe u het apparaat installeert en uitvoert:

1. Open een Terminal venster in de gewenste map. Voer de volgende opdracht uit om de [Azure IOT-voor C# beelden voor (.net)](https://github.com/Azure-Samples/azure-iot-samples-csharp) github-opslag plaats naar deze locatie te klonen:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Dit Terminal venster wordt nu gebruikt als uw _apparaat_ -Terminal. Ga naar de map van uw gekloonde opslag plaats en navigeer naar de map **/Azure-IOT-samples-csharp/digitaltwin/samples/Device/EnvironmentalSensorSample** .

1. De connection string van het _apparaat_configureren:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Bouw de benodigde pakketten en voer het voor beeld uit met de volgende opdracht:

    ```cmd\sh
        dotnet run
    ```

1. Er worden berichten weer gegeven met de melding dat het apparaat is geregistreerd en wacht op updates vanuit de Cloud. Dit geeft aan dat het apparaat nu gereed is om opdrachten en updates van eigenschappen te ontvangen en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze terminal niet. u hebt deze later nodig om te bevestigen dat de service voorbeelden ook hebben gewerkt.

## <a name="run-the-sample-solution"></a>De voorbeeld oplossing uitvoeren

In deze Snelstartgids gebruikt u een voor beeld van een IoT C# -oplossing in om te communiceren met het voor beeld-apparaat.

1. Open een ander Terminal venster (dit is uw _service_ Terminal). Ga naar de map van uw gekloonde opslag plaats en navigeer naar de map **/Azure-IOT-samples-csharp/digitaltwin/samples/service** .

1. Configureer de _IOT hub-Connection String_ en de _apparaat-id_ zodat de service verbinding kan maken met beide:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de Terminal hebt aangesloten, hebt u het volgende bericht met de online status gezien:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Ga naar de _service_ Terminal en gebruik de volgende opdrachten om het voor beeld voor het lezen van apparaatgegevens uit te voeren:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Schuif in de Terminal uitvoer van de _service_ naar het onderdeel `environmentalSensor`. U ziet dat de eigenschap `state`, die wordt gebruikt om aan te geven of het apparaat online is, is gerapporteerd als _waar_:

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

### <a name="update-a-writable-property"></a>Een Beschrijf bare eigenschap bijwerken

1. Ga naar de _service_ Terminal en stel de volgende variabelen in om op te geven welke eigenschap moet worden bijgewerkt:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Gebruik de volgende opdrachten om het voor beeld voor het bijwerken van de eigenschap uit te voeren:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. De _service_ Terminal uitvoer toont de bijgewerkte apparaatgegevens. Ga naar het onderdeel `environmentalSensor` om de nieuwe helderheids waarde van 42 weer te geven.

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

1. Ga naar uw _apparaat_ -Terminal, u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Ga terug naar de _service_ Terminal en voer de onderstaande opdrachten uit om de apparaatgegevens opnieuw op te halen om te bevestigen dat de eigenschap is bijgewerkt.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. In de _service_ Terminal output, onder het onderdeel `environmentalSensor`, ziet u dat de bijgewerkte helderheids waarde is gerapporteerd. Opmerking: het kan enige tijd duren voordat het apparaat de update heeft voltooid. U kunt deze stap herhalen totdat het apparaat daad werkelijk de update van de eigenschap heeft verwerkt.
    
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

1. Ga naar de _service_ Terminal en stel de volgende variabelen in om op te geven welke opdracht moet worden aangeroepen:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Gebruik de volgende opdrachten om het voor beeld voor het aanroepen van de opdracht uit te voeren:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. De uitvoer in de _service_ terminal moet de volgende bevestiging weer geven:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Naar de _apparaat_ -Terminal gaat, ziet u dat de opdracht is bevestigd:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-oplossing. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en interactie met een apparaat](howto-develop-solution.md)
