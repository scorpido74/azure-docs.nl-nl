---
title: IoT Plug en Play-brug | Microsoft Docs
description: Begrijp de IoT Plug en Play-brug en hoe u deze kunt gebruiken voor het verbinden van bestaande apparaten die zijn gekoppeld aan een Windows-of Linux-gateway als IoT Plug en Play-apparaten.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579983"
---
# <a name="iot-plug-and-play-bridge"></a>IoT Plug en Play-brug

De IoT Plug en Play Bridge is een open-source toepassing voor het verbinden van bestaande apparaten die zijn gekoppeld aan Windows of Linux gateway als IoT Plug en Play-apparaten. Nadat u de toepassing hebt geïnstalleerd en geconfigureerd op uw Windows-of Linux-computer, kunt u deze gebruiken om aangesloten apparaten te verbinden met een IoT-hub. U kunt de Bridge gebruiken om IoT Plug en Play-interfaces toe te wijzen aan de telemetrie die de aangesloten apparaten verzenden, werken met apparaateigenschappen en opdrachten aanroepen.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Aan de linkerkant zijn er een aantal bestaande Sens oren aangesloten (zowel bekabeld als draadloos) aan een Windows-of Linux-computer met IoT Plug en Play-brug. De IoT Plug en Play-brug maakt vervolgens verbinding met een IoT-hub aan de rechter kant":::

IoT Plug en Play Bridge kan worden geïmplementeerd als een zelfstandig uitvoerbaar bestand op een IoT-apparaat, een industriële PC, een server of een gateway met Windows 10 of Linux. Het kan ook worden gecompileerd in de code van uw toepassing. Een eenvoudig configuratie-JSON-bestand vertelt de IoT Plug en Play-brug waarmee aangesloten apparaten/rand apparatuur moet worden blootgesteld aan Azure.

## <a name="supported-protocols-and-sensors"></a>Ondersteunde protocollen en Sens oren

IoT Plug en Play Bridge ondersteunt standaard de volgende typen rand apparatuur, met koppelingen naar de adapter documentatie:

|Rand apparatuur|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |Ja|Nee|
|[Camera's](https://aka.ms/iot-pnp-bridge-camera)               |Ja|Nee|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Ja|Ja|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Ja|Ja|
|[Wel](https://aka.ms/iot-pnp-bridge-serial)                |Ja|Ja|
|[Windows USB-rand apparatuur](https://aka.ms/iot-pnp-bridge-usb)  |Ja|Niet van toepassing|

>[!Important]
>Ontwikkel aars kunnen de IoT Plug en Play-brug uitbreiden ter ondersteuning van aanvullende protocollen via de instructies in de **[documentatie voor IoT Plug en Play Bridge-ontwikkel aars](https://aka.ms/iot-pnp-bridge-dev-doc)**.

## <a name="prerequisites"></a>Vereisten

### <a name="os-platform"></a>OS-platform

De volgende platformen en versies van het besturings systeem worden ondersteund:

|Platform  |Ondersteunde versies  |
|---------|---------|
|Windows 10 |     Alle Windows Sku's worden ondersteund. Bijvoorbeeld: IoT Enter prise, Server, Desktop, IoT core. *Voor de functionaliteit voor camera status controle wordt 20H1 of hoger aanbevolen. Alle andere functies zijn beschikbaar op alle Windows 10-builds.*  |
|Linux     |Getest en ondersteund op Ubuntu 18,04, de functionaliteit voor andere distributies is niet getest.         |
||

### <a name="hardware"></a>Hardware

- Elk platform dat ondersteuning biedt voor de bovengenoemde Sku's en versies van het besturings systeem.
- De rand apparaten en Sens oren van serie, USB, Bluetooth en camera worden standaard ondersteund. De IoT Plug en Play-brug kan worden uitgebreid ter ondersteuning van aangepaste rand apparatuur of sensors ([Zie de sectie rand apparatuur hierboven](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>Ontwikkelomgeving

U hebt het volgende nodig om de IoT Plug en Play-brug te bouwen, uit te breiden en te ontwikkelen:  

- Een ontwikkel omgeving die ondersteuning biedt voor het compileren van C++, zoals [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/). Zorg ervoor dat u de Desktop ontwikkeling met C++-werk belasting opneemt tijdens de installatie van Visual Studio.
- [Cmake](https://cmake.org/download/) : als u cmake installeert, selecteert u de optie `Add CMake to the system PATH` .
- Als u op Windows bouwt, moet u ook de Windows 17763 SDK downloaden: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Azure IOT hub apparaat C-SDK](https://github.com/Azure/azure-iot-sdk-c). Met de meegeleverde build-scripts in deze opslag plaats wordt automatisch de vereiste Azure IoT C-SDK voor u gekloond.

### <a name="azure-iot-products-and-tools"></a>Azure IoT-producten en-Hulpprogram Ma's

- **Azure IOT hub** : u hebt een [Azure IOT-hub](https://docs.microsoft.com/azure/iot-hub/) in uw Azure-abonnement nodig om uw apparaat te verbinden met. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Als u geen IoT-hub hebt, [volgt u deze instructies om er een te maken](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> IoT Plug en Play is momenteel beschikbaar in IoT-hubs die zijn gemaakt in de regio's VS - centraal, Europa - noord en Japan - oost. IoT Plug en Play-ondersteuning is niet opgenomen in de basislaag van IoT-hubs. Als u wilt communiceren met uw IoT Plug en Play-apparaat, kunt u het hulp programma Azure IoT Explorer gebruiken. [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem.

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT Plug en Play Bridge-architectuur

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Aan de linkerkant zijn er een aantal bestaande Sens oren aangesloten (zowel bekabeld als draadloos) aan een Windows-of Linux-computer met IoT Plug en Play-brug. De IoT Plug en Play-brug maakt vervolgens verbinding met een IoT-hub aan de rechter kant":::

## <a name="download-iot-plug-and-play-bridge"></a>IoT Plug en Play-brug downloaden

U kunt een vooraf ontwikkelde versie van de brug downloaden met ondersteunde adapters in [IoT Plug en Play Bridge-releases](https://aka.ms/iot-pnp-bridge-releases) en de lijst met assets voor de meest recente release uitvouwen. Down load de meest recente versie van de toepassing voor uw besturings systeem.

U kunt ook de bron code van [IoT Plug en Play Bridge](https://aka.ms/bridge)downloaden en weer geven op github.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van de architectuur van IoT Plug en Play Bridge, moeten de volgende stappen worden uitgevoerd om meer te weten te komen over:

- [IoT Plug en Play Bridge gebruiken](./howto-use-iot-pnp-bridge.md)
- [Zie de naslag informatie voor GitHub-ontwikkel aars voor IoT Plug en Play-brug](https://aka.ms/iot-pnp-bridge-dev-doc)
- [IoT Plug en Play-brug op GitHub](https://aka.ms/iotplugandplaybridge)
