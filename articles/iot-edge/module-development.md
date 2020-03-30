---
title: Modules ontwikkelen voor Azure IoT Edge | Microsoft Documenten
description: Aangepaste modules ontwikkelen voor Azure IoT Edge die kunnen communiceren met de runtime en IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271380"
---
# <a name="develop-your-own-iot-edge-modules"></a>Ontwikkel je eigen IoT Edge modules

Azure IoT Edge-modules kunnen verbinding maken met andere Azure-services en bijdragen aan uw grotere pijplijn voor cloudgegevens. In dit artikel wordt beschreven hoe u modules ontwikkelen om te communiceren met de IoT Edge-runtime en IoT Hub, en dus de rest van de Azure-cloud.

## <a name="iot-edge-runtime-environment"></a>IoT Edge runtime-omgeving

De IoT Edge-runtime biedt de infrastructuur om de functionaliteit van meerdere IoT Edge-modules te integreren en te implementeren op IoT Edge-apparaten. Elk programma kan worden verpakt als een IoT Edge-module. Als u optimaal wilt profiteren van iot edge-communicatie- en beheerfunctionaliteiten, kan een programma dat in een module wordt uitgevoerd, de Azure IoT Device SDK gebruiken om verbinding te maken met de lokale IoT Edge-hub.

## <a name="using-the-iot-edge-hub"></a>De IoT Edge-hub gebruiken

De IoT Edge-hub biedt twee belangrijke functionaliteiten: proxy naar IoT Hub en lokale communicatie.

### <a name="iot-hub-primitives"></a>IoT Hub primitieven

IoT Hub ziet een module-exemplaar analoog aan een apparaat, in de zin dat:

* het heeft een module tweeling die is verschillend en geïsoleerd van het [apparaat tweeling](../iot-hub/iot-hub-devguide-device-twins.md) en de andere module tweelingen van dat apparaat;
* het kan [device-to-cloud berichten](../iot-hub/iot-hub-devguide-messaging.md)verzenden;
* het kan [directe methoden](../iot-hub/iot-hub-devguide-direct-methods.md) ontvangen die specifiek op zijn identiteit zijn gericht.

Momenteel kunnen modules geen cloud-to-device-berichten ontvangen of de functie voor het uploaden van bestanden gebruiken.

Bij het schrijven van een module u de [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) gebruiken om verbinding te maken met de IoT Edge-hub en de bovenstaande functionaliteit te gebruiken zoals u dat zou doen bij het gebruik van IoT Hub met een apparaattoepassing. Het enige verschil tussen IoT Edge-modules en IoT-apparaattoepassingen is dat u moet verwijzen naar de identiteit van de module in plaats van naar de apparaatidentiteit.

### <a name="device-to-cloud-messages"></a>Apparaat-naar-cloud-berichten

Om complexe verwerking van device-to-cloud-berichten mogelijk te maken, biedt iot edge-hub declaratieve routering van berichten tussen modules en tussen modules en IoT Hub. Met declaratieve routering kunnen modules berichten die door andere modules worden verzonden onderscheppen en verwerken en deze in complexe pijplijnen verspreiden. Zie [modules implementeren en routes instellen in IoT Edge](module-composition.md)voor meer informatie.

Een IoT Edge-module kan, in tegenstelling tot een normale IoT Hub-apparaattoepassing, device-to-cloud-berichten ontvangen die worden geproxieerd door de lokale IoT Edge-hub om ze te verwerken.

IoT Edge-hub propeert de berichten naar uw module op basis van declaratieve routes die in het [implementatiemanifest worden](module-composition.md)beschreven. Bij het ontwikkelen van een IoT Edge-module u deze berichten ontvangen door berichthandlers in te stellen.

Om het maken van routes te vereenvoudigen, voegt IoT Edge het concept van *moduleinvoer-* en *uitvoereindpunten* toe. Een module kan alle device-to-cloud berichten naar het bericht worden doorgestuurd zonder enige invoer op te geven, en kan device-to-cloud berichten verzenden zonder enige uitvoer op te geven. Het gebruik van expliciete ingangen en uitvoer maakt routeringsregels echter eenvoudiger te begrijpen.

Ten slotte worden device-to-cloudberichten die door de Edge-hub worden verwerkt, gestempeld met de volgende systeemeigenschappen:

| Eigenschap | Beschrijving |
| -------- | ----------- |
| $connectionDeviceId | De apparaat-id van de client die het bericht heeft verzonden |
| $connectionModuleId | De module-id van de module die het bericht heeft verzonden |
| $inputName | De input die dit bericht heeft ontvangen. Kan leeg zijn. |
| $outputName | De uitvoer die wordt gebruikt om het bericht te verzenden. Kan leeg zijn. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Verbinding maken met IoT Edge-hub vanuit een module

Verbinding maken met de lokale IoT Edge-hub vanuit een module omvat twee stappen:

1. Maak een moduleclient-exemplaar in uw toepassing.
2. Zorg ervoor dat uw toepassing het certificaat accepteert dat wordt gepresenteerd door de IoT Edge-hub op dat apparaat.

Maak een moduleclient-exemplaar om uw module aan te sluiten op de IoT Edge-hub die op het apparaat wordt uitgevoerd, vergelijkbaar met de manier waarop DeviceClient-exemplaren IoT-apparaten verbinden met IoT-hub. Zie de API-verwijzing naar de gewenste SDK-taal [c#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C,](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h) [Python,](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python) [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)of [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)voor meer informatie over de klasse ModuleClient en de bijbehorende communicatiemethoden.

## <a name="language-and-architecture-support"></a>Ondersteuning voor taal en architectuur

IoT Edge ondersteunt meerdere besturingssystemen, apparaatarchitecturen en ontwikkelingstalen, zodat u het scenario bouwen dat aan uw behoeften voldoet. Gebruik deze sectie om inzicht te krijgen in uw opties voor het ontwikkelen van aangepaste IoT Edge-modules. U meer te weten komen over ondersteuning en vereisten voor het bewerken van gereedschappen voor elke taal in [Uw ontwikkel- en testomgeving voorbereiden voor IoT Edge.](development-environment.md)

### <a name="linux"></a>Linux

Voor alle talen in de volgende tabel ondersteunt IoT Edge de ontwikkeling voor AMD64- en ARM32 Linux-apparaten.

| Ontwikkelingstaal | Ontwikkelhulpprogramma’s |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Ontwikkelen en debuggen ondersteuning voor ARM64 Linux-apparaten is in [openbare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie [ARM64 IoT Edge-modules ontwikkelen en debugen in Visual Studio Code (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)voor meer informatie.

### <a name="windows"></a>Windows

Voor alle talen in de volgende tabel ondersteunt IoT Edge de ontwikkeling voor AMD64 Windows-apparaten.

| Ontwikkelingstaal | Ontwikkelhulpprogramma’s |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (geen foutopsporingsmogelijkheden)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Volgende stappen

[Bereid uw ontwikkel- en testomgeving voor op IoT Edge](development-environment.md)

[Visual Studio gebruiken om C#-modules voor IoT Edge te ontwikkelen](how-to-visual-studio-develop-module.md)

[Visual Studio Code gebruiken om modules voor IoT Edge te ontwikkelen](how-to-vs-code-develop-module.md)

[Azure IoT Hub SDKs begrijpen en gebruiken](../iot-hub/iot-hub-devguide-sdks.md)
