---
title: Meer informatie over hoe modules logica uitvoeren op uw apparaten - Azure IoT Edge | Microsoft Documenten
description: Azure IoT Edge-modules zijn gecontaineriseerde eenheden logica die op afstand kunnen worden geïmplementeerd en beheerd, zodat u bedrijfslogica uitvoeren op IoT Edge-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c625e628f53d156ad56a1c69df1c23aec9120ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548710"
---
# <a name="understand-azure-iot-edge-modules"></a>Informatie over Azure IoT Edge-modules

Met Azure IoT Edge u bedrijfslogica op de rand implementeren en beheren in de vorm van *modules.* Azure IoT Edge-modules zijn de kleinste rekeneenheid die wordt beheerd door IoT Edge en kunnen Azure-services (zoals Azure Stream Analytics) of uw eigen oplossingsspecifieke code bevatten. Om te begrijpen hoe modules worden ontwikkeld, geïmplementeerd en onderhouden, moet u rekening houden met de vier conceptuele elementen van een module:

* Een **moduleafbeelding** is een pakket met de software die een module definieert.
* Een **module-instantie** is de specifieke rekeneenheid waarop de moduleafbeelding wordt uitgevoerd op een IoT Edge-apparaat. De module-instantie wordt gestart door de Runtime van IoT Edge.
* Een **module-identiteit** is een stukje informatie (inclusief beveiligingsreferenties) dat is opgeslagen in IoT Hub, dat is gekoppeld aan elke module-instantie.
* Een **module-tweeling** is een JSON-document dat is opgeslagen in IoT Hub, dat statusgegevens bevat voor een module-instantie, inclusief metagegevens, configuraties en voorwaarden.

## <a name="module-images-and-instances"></a>Moduleafbeeldingen en -instanties

IoT Edge-moduleafbeeldingen bevatten toepassingen die profiteren van de beheer-, beveiligings- en communicatiefuncties van de IoT Edge-runtime. U uw eigen moduleafbeeldingen ontwikkelen of deze exporteren vanuit een ondersteunde Azure-service, zoals Azure Stream Analytics.
De afbeeldingen bestaan in de cloud en kunnen worden bijgewerkt, gewijzigd en geïmplementeerd in verschillende oplossingen. Een module die bijvoorbeeld machine learning gebruikt om de productie van productielijnen te voorspellen, bestaat als een afzonderlijk beeld dan een module die computervision gebruikt om een drone te besturen.

Telkens wanneer een moduleafbeelding wordt geïmplementeerd op een apparaat en wordt gestart met de Runtime van IoT Edge, wordt een nieuw exemplaar van die module gemaakt. Twee apparaten in verschillende delen van de wereld kunnen dezelfde moduleafbeelding gebruiken. Elk apparaat zou echter zijn eigen module-instantie hebben wanneer de module op het apparaat wordt gestart.

![Diagram - Moduleafbeeldingen in de cloud, module-exemplaren op apparaten](./media/iot-edge-modules/image_instance.png)

In de implementatie bestaan modulesafbeeldingen als containerafbeeldingen in een opslagplaats en module-exemplaren zijn containers op apparaten.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Moduleidentiteiten

Wanneer een nieuwe module-instantie wordt gemaakt door de Runtime van IoT Edge, krijgt deze een bijbehorende module-identiteit. De module-identiteit wordt opgeslagen in IoT Hub en wordt gebruikt als adresserings- en beveiligingsbereik voor alle lokale en cloudcommunicatie voor dat module-exemplaar.

De identiteit die is gekoppeld aan een module-instantie is afhankelijk van de identiteit van het apparaat waarop de instantie wordt uitgevoerd en de naam die u aan die module in uw oplossing verstrekt. Als u bijvoorbeeld `insight` een module aanroept die een Azure Stream Analytics `Hannover01`gebruikt en deze implementeert op een `/devices/Hannover01/modules/insight`apparaat dat wordt genoemd, maakt de IoT Edge-runtime een overeenkomstige module-identiteit genaamd .

Het is duidelijk dat in scenario's waarin u één moduleafbeelding meerdere keren op hetzelfde apparaat moet implementeren, u dezelfde afbeelding meerdere keren met verschillende namen implementeren.

![Diagram - Moduleidentiteiten zijn uniek binnen apparaten en op verschillende apparaten](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>De tweelingen van de module

Elke moduleinstantie heeft ook een bijbehorende moduletwin die u gebruiken om de module-instantie te configureren. De instantie en de tweeling worden met elkaar verbonden via de module-identiteit.

Een moduletwin is een JSON-document dat module-informatie en configuratie-eigenschappen opslaat. Dit concept loopt parallel met het [concept](../iot-hub/iot-hub-devguide-device-twins.md) van IoT Hub. De structuur van een module twin is hetzelfde als een apparaat twin. De API's die worden gebruikt om te interageren met beide soorten tweelingen zijn ook hetzelfde. Het enige verschil tussen de twee is de identiteit die wordt gebruikt om de client SDK te instantiëren.

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

Azure IoT Edge-modules kunnen voor onbepaalde tijd offline werken nadat ze ten minste één keer met IoT Hub zijn gesynchroniseerd. IoT Edge-apparaten kunnen deze offline mogelijkheid ook uitbreiden naar andere IoT-apparaten. Zie [Uitgebreide offlinemogelijkheden voor IoT Edge-apparaten, -modules en onderliggende apparaten begrijpen voor](offline-capabilities.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Inzicht in de vereisten en tools voor het ontwikkelen van IoT Edge-modules](module-development.md)
* [Inzicht in de runtime en de architectuur van Azure IoT Edge](iot-edge-runtime.md)
