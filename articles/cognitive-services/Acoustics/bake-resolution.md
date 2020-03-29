---
title: Project Akoestiek Bakresolutie
titlesuffix: Azure Cognitive Services
description: Dit conceptuele overzicht beschrijft het verschil tussen grove en fijne resoluties tijdens het bakken van akoestiek.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d8eb3b2cbaf7b4e842d8338eefde756f6d381111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854352"
---
# <a name="project-acoustics-bake-resolution"></a>Project Akoestiek Bakresolutie
Dit conceptuele overzicht beschrijft het verschil tussen grove en fijne resoluties tijdens het bakken van akoestiek. U kiest deze instelling tijdens de sondestap van de bakworkflow.

## <a name="coarse-vs-fine-resolution"></a><a name="Coarse-vs-Fine-Resolution"></a>Grof versus fijne resolutie

Het enige verschil tussen de instellingen voor grof en fijn resolutie is de frequentie waarmee de simulatie wordt uitgevoerd. Fine gebruikt een frequentie die twee keer zo hoog is als grof. Dit heeft een aantal implicaties voor de akoestische simulatie:

* De golflengte voor grof is twee keer zo lang als fijn, en daarom zijn de voxels twee keer zo groot.
* De simulatie tijd is direct gerelateerd aan de voxel grootte, waardoor een grove bakken ongeveer 16 keer sneller dan een fijne bakken.
* Portals (bijvoorbeeld deuren of ramen) kleiner dan de voxelgrootte kunnen niet worden gesimuleerd. De grove instelling kan ertoe leiden dat sommige van deze kleinere portalen niet worden gesimuleerd; daarom zullen ze niet passeren geluid door tijdens runtime. U zien of dit gebeurt door het bekijken van de voxels.
* De lagere simulatiefrequentie resulteert in minder diffractie rond hoeken en randen.
* Geluidsbronnen kunnen zich niet in "gevulde" voxels bevinden (d.w.z. voxels die geometrie bevatten). Dit resulteert in geen geluid. Het is moeilijker om geluidsbronnen te plaatsen, zodat ze niet in de grotere voxels van grof dan het is bij het gebruik van de fijne instelling.
* De grotere voxels zullen meer binnendringen in portalen, zoals hieronder weergegeven. De eerste afbeelding is gemaakt met grof, terwijl de tweede is dezelfde deuropening met behulp van fijne resolutie. Zoals aangegeven door de rode markeringen, is er veel minder inbraak in de deuropening met behulp van de fijne instelling. De blauwe lijn is de deuropening zoals gedefinieerd door de geometrie, terwijl de rode lijn is de effectieve akoestische portal gedefinieerd door de voxel grootte. Hoe deze inbraak zich afspeelt in een bepaalde situatie hangt volledig af van hoe de voxels aansluiten bij de geometrie van het portaal, die wordt bepaald door de grootte en locaties van uw objecten in de scène.

![Screenshot van grove voxels die een deuropening vullen in Unreal](media/unreal-coarse-bake.png)

![Screenshot van fijne voxels in een deuropening in Unreal](media/unreal-fine-bake.png)

## <a name="next-steps"></a>Volgende stappen

Probeer zelf de grove en fijne resolutie-instellingen uit met onze [Unreal-](unreal-baking.md) of Unity-plug-ins. [Unity](unity-baking.md)
