---
title: Interactie met IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing - C# | Microsoft Docs
description: Gebruik C# (.NET) om verbinding te maken met een IoT Plug en Play Preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 226acdda94eb88825d60e35d48bfdd476ad1339c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044094"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Quickstart: Interactie met een IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug en Play Preview vereenvoudigt IoT door u te laten communiceren met de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u C# (MET .NET) gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

## <a name="prerequisites"></a>Vereisten

Als u deze quickstart wilt uitvoeren, moet u .NET Core (2.x.x of 3.x.x) op uw ontwikkelcomputer installeren. U kunt uw voorkeursversie van .NET Core SDK voor meerdere platforms downloaden van [.NET Core downloaden](https://dotnet.microsoft.com/download/dotnet-core/).

U kunt de versie van .NET controleren die zich op uw ontwikkelcomputer bevindt door de volgende opdracht uit te voeren in een lokaal terminalvenster: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub (dit is voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u een voorbeeld van een omgevingssensor die in C# is geschreven als IoT Plug en Play-apparaat. De volgende instructies laten zien hoe u het apparaat installeert en uitvoert:

1. Open een terminalvenster in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) te klonen op deze locatie:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. Dit terminalvenster wordt nu gebruikt als uw _apparaat_terminal. Ga naar de map van de gekloonde opslagplaats en ga naar de map **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample**.

1. Configureer de _device connection string_ (verbindingsreeks voor het apparaat):

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Bouw de benodigde pakketten en voer het voorbeeld met de volgende opdracht uit:

    ```cmd\sh
        dotnet run
    ```

1. Er worden berichten weergegeven met de melding dat het apparaat is geregistreerd en wacht op updates vanuit de cloud. Dit geeft aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze terminal niet. U hebt deze later nodig om te bevestigen dat de servicevoorbeelden werkten.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een IoT-voorbeeldoplossing in C# om te communiceren met het voorbeeld-apparaat.

1. Open een ander terminalvenster (dit is uw _service_terminal). Ga naar de map van de gekloonde opslagplaats en ga naar de map **/azure-iot-samples-csharp/digitaltwin/Samples/service**.

1. Configureer de _IoT-hub-verbindingsreeks_ en _apparaat-ID_ zodat de service verbinding kan maken met het volgende:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de terminal hebt aangesloten, zag u het volgende bericht om de online status aan te geven:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Ga naar de terminal _service_ en gebruik de volgende opdracht om het voorbeeld voor het lezen van apparaatgegevens uit te voeren:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. Ga in de uitvoer van de terminal _service_ naar het onderdeel `environmentalSensor`. U ziet dat de eigenschap `state`, die wordt gebruikt om aan te geven of het apparaat online is, wordt vermeld als _true_:

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

### <a name="update-a-writable-property"></a>Een schrijfbare eigenschap bijwerken

1. Ga naar de terminal _service_ en stel de volgende variabelen in om op te geven welke eigenschap moet worden bijgewerkt:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Gebruik de volgende opdracht om het voorbeeld voor het bijwerken van de eigenschap uit te voeren:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. De uitvoer van de terminal _service_ toont de bijgewerkte apparaatgegevens. Ga naar het onderdeel `environmentalSensor` om de nieuwe helderheidswaarde van 42 weer te geven.

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

1. Ga naar uw terminal _apparaat_, u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Ga terug naar de terminal _service_ en voer de onderstaande opdrachten uit om de apparaatgegevens opnieuw op te halen om te bevestigen dat de eigenschap is bijgewerkt.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. In de uitvoer van de terminal _service_ onder het onderdeel `environmentalSensor` ziet u dat de bijgewerkte helderheidswaarde is gerapporteerd. Opmerking: het kan enige tijd duren voordat het apparaat de update heeft voltooid. U kunt deze stap herhalen totdat het apparaat daadwerkelijk de update van de eigenschap heeft verwerkt.
    
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

1. Ga naar de terminal _service_ en stel de volgende variabelen in om op te geven welke opdracht moeten worden aangeroepen:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Gebruik de volgende opdracht om het voorbeeld voor het aanroepen van de opdracht uit te voeren:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. De uitvoer van de terminal _service_ moet de volgende bevestiging weergeven:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Ga naar het terminal _apparaat_, u ziet dat de opdracht is bevestigd:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-oplossing. Als u meer wilt weten over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten, leest u:

> [!div class="nextstepaction"]
> [Uitleg: Verbinding maken en werken met een apparaat](howto-develop-solution.md)
