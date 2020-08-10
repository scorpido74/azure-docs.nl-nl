---
title: Wat is nieuw? Vernieuwde preview voor IoT Plug en Play | Microsoft Docs
description: Meer informatie over wat er nieuw is in de vernieuwde preview voor IoT Plug en Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: 60ad7f5df4f13d626d7a2c24990c7f48db28d01d
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475223"
---
# <a name="iot-plug-and-play-preview-refresh"></a>Vernieuwde preview voor IoT Plug en Play

In dit artikel worden de belangrijkste wijzigingen in de SDK'S, bibliotheken, hulpprogramma's en services in de vernieuwde preview voor IoT Plug en Play van juli 2020 beschreven. De eerdere preview van IoT Plug en Play is uitgegeven in augustus 2019.

## <a name="digital-twins-definition-language-dtdl"></a>DTDL (Digital Twins Definition Language)

Deze release voegt ondersteuning toe voor [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl). [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview) wordt afgeschaft.

In de volgende lijst worden de belangrijkste verschillen tussen DTDL v1 en DTDL v2 weergegeven. In DTDL v2:

- Model-id's hebben het voor voegsel `dtmi` in plaats van `urn`. DTMI’s (Digital Twin Model Identifiers) vervangen de Digital Twin-id’s met het voorvoegsel `urn` die worden gebruikt in DTDL v1. De versie wordt nu voorafgegaan door een `;`. Als u bijvoorbeeld eerder `urn:CompanyName:Model:1` gebruikte, gebruikt u nu `dtmi:CompanyName:Model;1`.
- Stel de `@context` in op `dtmi:dtdl:context;2` in plaats van `http://azureiot.com/v1/contexts/IoTModel.json`.
- Gebruik het type **Interface** in plaats van het type **CapabilityModel** om een apparaat te modelleren.
- De instanties **Onderdelen** vervangen de instanties **Interface**. U kunt **Relaties** en **Onderdelen** definiëren als onderdeel van de inhoud van een **Interface**.
- Een **Interface** kan zaken overnemen van een andere **Interface**.
- U kunt DTDL uitbreiden met behulp van _uitbreidbare semantische typen_. Dit uitbreidbare typesysteem biedt meer flexibiliteit dan de in code vastgelegde semantische typen, zoals temperatuur en vochtigheid, in DTDL v1.
- De eigenschap **displayUnit** is verwijderd.
- U kunt geen voorafgaande of navolgende onderstrepingstekens gebruiken in een naam. Voorafgaande onderstrepingstekens in een naam zijn gereserveerd voor systeemgebruik.

Als u met DTDL v1 wilt werken, moet u de vorige versie van de SDK en Azure IoT Explorer 0.10.x gebruiken. Als u met DTDL v2 wilt werken, hebt u de nieuwste versie van de SDK en Azure IoT Explorer 0.11.x nodig.

## <a name="no-component-and-multiple-component-implementations"></a>Implementaties zonder en met meerdere onderdelen

Eenvoudige apparaten met een aantal telemetriegegevens, opdrachten of eigenschappen kunnen worden beschreven in een enkele interface zonder onderdelen te gebruiken. Elk bestaand apparaat kan een IoT-Plug en Play worden door de **Model-id** aan te kondigen zonder wijzigingen in de bestaande implementatie van het apparaat.

Complexere apparaten kunnen telemetrie, opdrachten en eigenschappen op verschillende interfaces groeperen om de complexiteit te beheren en het gebruik van meerdere apparaten mogelijk te maken. Deze apparaten moeten worden bijgewerkt zodat ze een set eenvoudige regels volgen die zijn gedefinieerd in de [berichtconventies voor de preview van IoT-Plug en Play](concepts-convention.md).

## <a name="registration-and-discovery"></a>Registratie en detectie

In deze release kondigen apparaten hun **Model-id** aan met IoT Hub voor elke verbinding. IoT Hub slaat de **Model-id** op in de cache waardoor back-end-oplossingen de **Model-id** kunnen ophalen met de eigenschap `modelId` van de apparaatdubbel. De **Model-id** kan ook worden opgehaald uit `$metadata.$model` in de Digital Twin.

## <a name="microsoft-defined-interfaces"></a>Door Microsoft gedefinieerde interfaces

De volgende door Microsoft gedefinieerde interfaces zijn afgeschaft en worden niet gepubliceerd in de nieuwe modelopslagplaats:

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

De volgende interface wordt gepubliceerd in de nieuwe modelopslagplaats: `dtmi:azure:DeviceManagement:DeviceInformation;1` beschikbaar in de URL [https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview).

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

De gebeurtenisstructuur van de [gebeurtenisbron](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) **DigitalTwinChangeEvents** is gewijzigd en gebruikt nu de indeling **JSON-Patch**. Deze wijziging is een wijziging die fouten veroorzaakt zonder compatibiliteit met eerdere versies.

## <a name="message-routing"></a>Berichtroutering

Voor telemetriegegevens worden de volgende wijzigingen aangebracht in de verzameling [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md).

De verzameling bevat nu de eigenschap **dt-dataschema**. Dit is de **Model-id** die is geregistreerd door het apparaat.

De eigenschap **dt-subject** vertegenwoordigt het onderdeel dat een telemetriebericht verzendt.

De eigenschap **iothub-interface-name** is afgeschaft.

## <a name="device-and-service-sdks"></a>Apparaat- en service-SDK's

Er is geen compatibiliteit met eerdere preview-versies van de SDK's. U moet uw code wijzigen als u overstapt op de nieuwste previewversie van een SDK.

Met de op conventies gebaseerde aanpak hebt u geen SDK's nodig voor afzonderlijke clients. In deze previewversie zijn de bestaande **DigitalTwinClient**-bibliotheken in alle talen afgeschaft. In plaats daarvan zijn de SDK's van de IoT Hub-apparaatclient bijgewerkt zodat ze een optie voor het aankondigen van de **Model-id** hebben.

Voor apparaten die geen onderdelen gebruiken, zijn minimale codewijzigingen vereist. U hoeft alleen maar de **Model-id** aan te aankondigen. Complexere apparaten die gebruikmaken van meerdere onderdelen, hebben mogelijk enkele herbruikbare functies nodig om de [conventies](concepts-convention.md) te implementeren. De apparatenvoorbeelden zijn een set functies die u kunt gebruiken in de implementatie van uw apparaat.

### <a name="service-sdks"></a>Service-SDK's

De service-SDK is beschikbaar in [node. js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) en [Python](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md).

## <a name="vs-code-extension"></a>VS Code-extensie

De extensie [Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) biedt creatieondersteuning voor DTDL v1, integratie met de vorige versie van de modelopslagplaats en het genereren van code.

Als u creatieondersteuning nodig hebt voor DTDL v2 in VS Code, installeert u de nieuwe [DTDL-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) in VS code. De extensie biedt geen integratie met de modelopslagplaats of het genereren van code. Het beheer van modellen in de opslagplaats verloopt nu via een [web-UI](https://aka.ms/iotmodelrepo) of [CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest).

## <a name="digital-twin-service-side-rest-apis"></a>Digital Twin REST API's aan servicezijde

De [Digital Twin REST API's aan servicezijde](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) en payloads zijn gewijzigd. De ondersteunde API's zijn:

- Een Digital Twin ophalen.
- Een opdracht aanroepen.
- Een Digital Twin bijwerken met een payload van **JSON-Patch**.

De bestaande REST API's worden nog steeds ondersteund in deze release.

## <a name="model-repository"></a>Modelopslagplaats

Er is nu één modelopslagplaats met zowel openbaar gepubliceerde modellen als persoonlijke met RBAC beveiligde bedrijfsmodellen. Alle modellen hebben een unieke id en zijn onveranderbaar nadat ze zijn gemaakt.

Bestaande opslagplaatsen voor bedrijfsmodellen van de vorige release worden niet ondersteund in deze release. U kunt de website van [Microsoft Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com/products) blijven gebruiken voor het beheren van de oude DTDL v1-modellen. U kunt deze website echter niet meer gebruiken om apparaten te registreren, te testen en te certificeren.

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central wordt momenteel bijgewerkt voor ondersteuning van de vernieuwde preview van IoT Plug en Play.
