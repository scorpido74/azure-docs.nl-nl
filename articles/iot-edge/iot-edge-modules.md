---
title: Informatie over hoe modules logische uitvoeren op uw apparaten - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge-modules zijn containers eenheden van de logica die kan worden geïmplementeerd en extern worden beheerd, zodat u bedrijfslogica op IoT Edge apparaten uitvoeren kunt
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c625e628f53d156ad56a1c69df1c23aec9120ac
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548710"
---
# <a name="understand-azure-iot-edge-modules"></a>Informatie over Azure IoT Edge-modules

Azure IoT Edge kunt u implementeren en beheren van zakelijke logica aan de rand in de vorm van *modules*. Azure IoT Edge-modules zijn de kleinste rekeneenheid beheerd door IoT Edge en Azure-services (zoals Azure Stream Analytics) of uw eigen oplossingsspecifieke code kunnen bevatten. Als u wilt weten hoe modules worden ontwikkeld, geïmplementeerd en onderhouden, moet u rekening houden met de vier conceptuele elementen van een module:

* Een **installatiekopie module** is een pakket met de software die een module definieert.
* Een **module-exemplaar** is de specifieke rekeneenheid waarop u de installatiekopie van de module wordt uitgevoerd op een IoT Edge-apparaat. De module-exemplaar wordt gestart door de IoT Edge-runtime.
* Een **module identiteit** is een stukje informatie (met inbegrip van beveiligingsreferenties) die zijn opgeslagen in IoT-Hub die is gekoppeld aan elk exemplaar van de module.
* Een **moduledubbel** is een JSON-document dat is opgeslagen in IoT Hub, met informatie over de status voor een module-exemplaar, met inbegrip van metagegevens, configuraties en voorwaarden.

## <a name="module-images-and-instances"></a>Module-afbeeldingen en instanties

IoT Edge module afbeeldingen bevatten toepassingen die van het beheer, beveiliging en functies voor communicatie van de IoT Edge-runtime profiteren. U kunt uw eigen installatiekopieën module ontwikkelen of exporteren van een ondersteunde Azure-service, zoals Azure Stream Analytics.
De afbeeldingen aanwezig zijn in de cloud en ze kunnen worden bijgewerkt, gewijzigd en geïmplementeerd in verschillende oplossingen. Bijvoorbeeld, bestaat een module die maakt gebruik van machine learning om te voorspellen van de uitvoer van de productielijn als een afzonderlijke installatiekopie dan een module die gebruikmaakt van computer vision voor het beheren van een drone.

Telkens wanneer de installatiekopie van een module is geïmplementeerd op een apparaat en aan de slag door de IoT Edge-runtime, is een nieuw exemplaar van die module gemaakt. Twee apparaten in verschillende delen van de wereld kunnen dezelfde module installatie kopie gebruiken. Elk apparaat heeft echter een eigen module-exemplaar wanneer de module op het apparaat wordt gestart.

![Diagram - Module afbeeldingen in de cloud, module-exemplaren op apparaten](./media/iot-edge-modules/image_instance.png)

In uitvoering, modules installatiekopieën bestaan als containerinstallatiekopieën in een opslagplaats en module-exemplaren zijn containers op apparaten.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Module-id 's

Wanneer een nieuw module-exemplaar wordt gemaakt door de IoT Edge runtime, wordt er een bijbehorende module-id opgehaald. De module-identiteit wordt opgeslagen in IoT Hub en wordt gebruikt als het bereik van de adressen en de beveiliging voor alle lokale en Cloud communicatie voor dat module-exemplaar.

De identiteit die is gekoppeld aan een module-exemplaar afhankelijk is van de identiteit van het apparaat op waarop het exemplaar wordt uitgevoerd en de naam die u voor die module in uw oplossing opgeeft. Bijvoorbeeld, als u aanroepen `insight` een module die gebruikmaakt van een Azure Stream Analytics, en u deze implementeren op een apparaat met de naam `Hannover01`, IoT Edge-runtime maakt u een overeenkomstige module-id met de naam `/devices/Hannover01/modules/insight`.

Duidelijk in scenario's kunt als u wilt implementeren een installatiekopie van de module meerdere keren op hetzelfde apparaat, u implementeren dezelfde installatiekopie van meerdere keren met verschillende namen.

![Diagram - Module-id's zijn uniek binnen apparaten en alle apparaten](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Moduledubbels

Elk exemplaar van de module heeft ook een bijbehorende moduledubbel die u gebruiken kunt voor het configureren van de module-exemplaar. Het exemplaar en het dubbele zijn gekoppeld met elkaar via de identiteit van de module.

Een moduledubbel is een JSON-document waarin de eigenschappen van de informatie en configuratie van de module. Dit concept werkt parallel de [apparaatdubbel](../iot-hub/iot-hub-devguide-device-twins.md) concept van IoT Hub. De structuur van een module dubbele is dezelfde als die van een apparaat. De API's gebruikt om te communiceren met beide typen dubbels zijn ook hetzelfde. Het enige verschil tussen de twee is de identiteit die wordt gebruikt voor het starten van de client-SDK.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Functionaliteiten offline

Azure IoT Edge modules kunnen voor onbepaalde tijd offline worden gebruikt na synchronisatie met IoT Hub ten minste één keer. Met IoT Edge apparaten kan deze offline-functionaliteit ook worden uitgebreid naar andere IoT-apparaten. Zie voor meer informatie, [begrijpen uitgebreid offline-mogelijkheden voor IoT Edge-apparaten, modules en onderliggende apparaten](offline-capabilities.md).

## <a name="next-steps"></a>Volgende stappen

* [Informatie over de vereisten en hulpprogramma's voor het ontwikkelen van IoT Edge-modules](module-development.md)
* [De Azure IoT Edge-runtime en de bijbehorende architectuur begrijpen](iot-edge-runtime.md)
