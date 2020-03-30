---
title: IoT Plug and Play Preview-voorbeeldapparaatcode verbinden met IoT-hub | Microsoft Documenten
description: Met C# (.NET) kun je IoT Plug and Play Preview-voorbeeldapparaatcode bouwen en uitvoeren die verbinding maakt met een IoT-hub. Gebruik het hulpprogramma voor Azure IoT explorer om de informatie weer te geven die door het apparaat naar de hub wordt verzonden.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 90d39635ac6302f816f39ca19cc00a39cfbbf850
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121014"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Snelstart: een voorbeeld van een IoT-stekker en play-apparaattoepassing verbinden met IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Met deze quickstart u zien hoe u een voorbeeld van IoT Plug and Play-apparaattoepassing maken, deze aansluiten op uw IoT-hub en het azure IoT explorer-hulpprogramma gebruiken om de informatie te bekijken die naar de hub wordt verzendt. De voorbeeldtoepassing is geschreven in C# (met .NET) en wordt geleverd als onderdeel van de Azure IoT-verzameling voor C#-verzameling (.NET). Een ontwikkelaar van een oplossing kan het Azure IoT explorer-hulpprogramma gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder dat er apparaatcode hoeft te worden bekeken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, moet u .NET Core 3.0 op uw ontwikkelmachine installeren. U deze versie van de .NET Core SDK downloaden voor meerdere platforms van [Download .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

U de versie van .NET die op uw ontwikkelingsmachine staat verifiëren door de volgende opdracht in een lokaal terminalvenster uit te voeren: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT-verkenner installeren

Download en installeer de nieuwste versie van **Azure IoT explorer** van de [repository-pagina](https://github.com/Azure/azure-iot-explorer/releases) van de tool door het MSI-bestand onder 'Assets' te selecteren voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT-hubverbindingstekenreeks_ voor uw hub te krijgen (opmerking voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart bereidt u een ontwikkelomgeving voor die u gebruiken om de Azure IoT-voorbeelden voor C# (.NET) te klonen en te bouwen.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de [GitHub-opslagplaats Azure IoT-samples voor C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) op deze locatie te klonen:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="run-the-device-sample"></a>Het voorbeeld van het apparaat uitvoeren

U gebruikt de gekloonde voorbeeldcode om een toepassing te bouwen die een apparaat simuleert dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal terminalvenster naar de map van uw gekloonde opslagplaats en navigeer naar de map **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample.** 

1. De _tekenreeks voor apparaatverbinding_configureren:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Voer een voorbeeldtoepassing uit om een IoT Plug and Play-apparaat te simuleren dat telemetrie naar uw IoT-hub verzendt. Gebruik in hetzelfde terminalvenster de volgende opdracht om de benodigde pakketten te bouwen en de voorbeeldtoepassing uit te voeren:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

U ziet berichten waarin staat dat het apparaat met succes is geregistreerd en wacht op updates vanuit de cloud. Dit geeft aan dat het apparaat nu klaar is om opdrachten en eigenschapsupdates te ontvangen en is begonnen met het verzenden van telemetriegegevens naar de hub. Houd het voorbeeld actief terwijl u de volgende stappen uitvoert.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT-verkenner gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Selecteer **Instellingen**om ervoor te zorgen dat het hulpprogramma de definities van het interfacemodel van uw apparaat kan lezen. In het menu Instellingen wordt **op het aangesloten apparaat** mogelijk al weergegeven in de plug-en play-configuraties. Als dit niet het zo is, selecteert u **+ Moduledefinitiebron toevoegen** en vervolgens **op het aangesloten apparaat** om deze toe te voegen.

1. Zoek terug op de **overzichtspagina Apparaten** de apparaatidentiteit die u eerder hebt gemaakt. Controleer of de **verbindingsstatus** van het apparaat in Azure IoT-verkenner wordt gemeld als _Verbonden_ (zo niet, druk op **Vernieuwen** totdat dit het geval is). Selecteer het apparaat om meer details weer te geven.

1. Breid de interface uit met **ID-urn:csharp_sdk_sample:EnvironmentalSensor:1** om de interface en IoT Plug and Play-primitieven te onthullen: eigenschappen, opdrachten en telemetrie.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat aansluiten op een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug and Play-apparaten:

> [!div class="nextstepaction"]
> [How-to: Verbinding maken met en communiceren met een IoT Plug and Play Preview-apparaat](howto-develop-solution.md)
