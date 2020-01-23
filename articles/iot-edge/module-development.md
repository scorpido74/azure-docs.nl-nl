---
title: Modules ontwikkelen voor Azure IoT Edge | Microsoft Docs
description: Aangepaste modules ontwikkelen voor Azure IoT Edge die met de runtime en IoT-Hub communiceren kan
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548625"
---
# <a name="develop-your-own-iot-edge-modules"></a>Uw eigen IoT Edge-modules ontwikkelen

Azure IoT Edge modules kunnen verbinding maken met andere Azure-Services en bijdragen aan uw grotere gegevens pijplijn voor de Cloud. In dit artikel wordt beschreven hoe u modules kunt ontwikkelen om te communiceren met de IoT Edge runtime en IoT Hub, en dus ook de rest van de Azure-Cloud.

## <a name="iot-edge-runtime-environment"></a>IoT Edge-runtime-omgeving

IoT Edge-runtime biedt de infrastructuur voor het integreren van de functionaliteit van meerdere IoT Edge-modules en deze te implementeren op IoT Edge-apparaten. Elk programma kan worden verpakt als een IoT Edge module. Een programma dat in een module wordt uitgevoerd, kan de Azure IoT Device SDK gebruiken om verbinding te maken met de lokale IoT Edge hub om optimaal te profiteren van IoT Edge communicatie-en beheer functionaliteit.

## <a name="using-the-iot-edge-hub"></a>Met behulp van de IoT Edge hub

De IoT Edge hub biedt twee belangrijke functies: proxy naar IoT Hub en lokale communicatie.

### <a name="iot-hub-primitives"></a>IoT Hub primitieven

IoT Hub ziet u een exemplaar van de module analogously aan een apparaat, in de zin dat:

* Er is een moduledubbel van die is uniek en geïsoleerd van de [apparaatdubbel](../iot-hub/iot-hub-devguide-device-twins.md) en de andere moduledubbels van het apparaat;
* Er kunnen worden verzonden [apparaat-naar-cloud-berichten](../iot-hub/iot-hub-devguide-messaging.md);
* kan de ontvangen [directe methoden](../iot-hub/iot-hub-devguide-direct-methods.md) speciaal bedoeld voor de identiteit.

Momenteel kunnen modules geen Cloud-naar-apparaat-berichten ontvangen of de functie voor het uploaden van bestanden gebruiken.

Wanneer u een module schrijft, kunt u de [Azure IOT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) gebruiken om verbinding te maken met de IOT Edge hub en de bovenstaande functionaliteit te gebruiken zoals u zou doen wanneer u IOT hub gebruikt met een apparaat-app. Het enige verschil tussen IoT Edge modules en IoT Device-toepassingen is dat u moet verwijzen naar de module-identiteit in plaats van de apparaat-id.

### <a name="device-to-cloud-messages"></a>Apparaat-naar-cloud-berichten

IoT Edge hub biedt declaratieve route ring van berichten tussen modules en tussen modules en IoT Hub om complexe verwerking van apparaat-naar-Cloud-berichten mogelijk te maken. Declaratieve routering kunt modules worden onderschept en verwerken van berichten verzonden door andere modules en het doorgeven ervan in complexe pijplijnen. Zie [modules implementeren en routes instellen in IOT Edge](module-composition.md)voor meer informatie.

Een IoT Edge module, in tegens telling tot een normale IoT Hub apparaat-toepassing, kan apparaat-naar-Cloud-berichten ontvangen die via de lokale IoT Edge hub worden verzonden om ze te verwerken.

IoT Edge hub worden de berichten door gegeven aan uw module op basis van declaratieve routes die in het [implementatie manifest](module-composition.md)worden beschreven. Bij het ontwikkelen van een IoT Edge-module, kunt u deze berichten ontvangen door in te stellen bericht handlers.

IoT Edge voegt het concept van module- *invoer* -en *uitvoer* eindpunten toe om het maken van routes te vereenvoudigen. Een module kan ontvangen van alle apparaat-naar-cloud-berichten doorgestuurd naar het zonder invoer op te geven en apparaat-naar-cloud-berichten kunt verzenden zonder eventuele uitvoer op te geven. Met behulp van expliciete invoer en uitvoer, maar dan wel maakt regels voor doorsturen eenvoudiger om te begrijpen.

Ten slotte worden verwerkt door de Edge hub apparaat-naar-cloud-berichten factureringslabel voor de volgende eigenschappen:

| Eigenschap | Beschrijving |
| -------- | ----------- |
| $connectionDeviceId | De apparaat-ID van de client die het bericht heeft verzonden |
| $connectionModuleId | De module-ID van de module die het bericht heeft verzonden |
| $inputName | De invoer die dit bericht ontvangen. Kan niet leeg zijn. |
| $outputName | De uitvoer die wordt gebruikt voor het verzenden van het bericht. Kan niet leeg zijn. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Verbinding maken met IoT Edge hub vanuit een module

Verbinding maken met de lokale IoT Edge hub vanuit een module bestaat uit twee stappen:

1. Maak een ModuleClient-exemplaar in uw toepassing.
2. Zorg ervoor dat uw toepassing accepteert het certificaat dat door de IoT Edge hub op het apparaat.

Maak een ModuleClient-exemplaar om uw module te koppelen aan de IoT Edge hub die op het apparaat wordt uitgevoerd, vergelijkbaar met de manier waarop DeviceClient-instanties IoT-apparaten verbinden met IoT Hub. Voor meer informatie over de ModuleClient-klasse en de communicatie methoden raadpleegt u de API-verwijzing voor uw voorkeurs [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)taal voor SDK:, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)of [node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Ondersteuning voor taal en architectuur

IoT Edge ondersteunt meerdere besturings systemen, architecturen en ontwikkelings talen, zodat u het scenario kunt bouwen dat aan uw behoeften voldoet. Gebruik deze sectie om inzicht te krijgen in uw opties voor het ontwikkelen van aangepaste IoT Edge-modules. U vindt meer informatie over hulp middelen en vereisten voor elke taal in [het voorbereiden van uw ontwikkel-en test omgeving voor IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

IoT Edge ondersteunt de ontwikkeling voor AMD64-en ARM32 Linux-apparaten voor alle talen in de volgende tabel.

| Ontwikkeltaal | Ontwikkelhulpprogramma’s |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Ondersteuning voor het ontwikkelen en opsporen van fouten voor ARM64 Linux-apparaten is beschikbaar in de [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie voor meer informatie [ARM64-modules ontwikkelen en fouten opsporen IOT Edge in Visual Studio code (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

IoT Edge ondersteunt de ontwikkeling van AMD64 Windows-apparaten voor alle talen in de volgende tabel.

| Ontwikkeltaal | Ontwikkelhulpprogramma’s |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio code (geen mogelijkheden voor fout opsporing)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Volgende stappen

[Bereid uw ontwikkel-en test omgeving voor op IoT Edge](development-environment.md)

[Visual Studio gebruiken voor het C# ontwikkelen van modules voor IOT Edge](how-to-visual-studio-develop-module.md)

[Visual Studio code gebruiken voor het ontwikkelen van modules voor IoT Edge](how-to-vs-code-develop-module.md)

[Azure IoT Hub Sdk's begrijpen en gebruiken](../iot-hub/iot-hub-devguide-sdks.md)
