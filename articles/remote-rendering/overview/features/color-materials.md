---
title: Kleurmaterialen
description: Beschrijft het kleurmateriaaltype.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681075"
---
# <a name="color-materials"></a>Kleurmaterialen

*Kleurmaterialen* zijn een van de ondersteunde [materiaaltypen](../../concepts/materials.md) in Azure Remote Rendering. Ze worden gebruikt voor [mazen](../../concepts/meshes.md) die geen enkele vorm van verlichting moeten ontvangen, maar te allen tijde volledige helderheid moeten hebben. Dit kan het geval zijn voor 'gloeiende' materialen, zoals dashboards van auto's, gloeilampen of voor gegevens die al statische verlichting bevatten, zoals modellen die zijn verkregen door [middel van fotogrammetrie.](https://en.wikipedia.org/wiki/Photogrammetry)

Kleurmaterialen zijn efficiënter te renderen dan [PBR-materialen](pbr-materials.md) vanwege hun eenvoudigere arceringsmodel. Ze ondersteunen ook verschillende transparantiemodi.

## <a name="common-material-properties"></a>Algemene materiaaleigenschappen

Deze eigenschappen zijn gemeenschappelijk voor alle materialen:

* **albedoColor:** Deze kleur wordt vermenigvuldigd met andere kleuren, zoals de *albedoMap-* of *hoekpuntkleuren.* Als *transparantie* is ingeschakeld op een materiaal, wordt het alfakanaal `1` gebruikt om `0` de dekking aan te passen, met betekenis volledig ondoorzichtig en betekenis volledig transparant. Standaard is wit.

  > [!NOTE]
  > Omdat kleurmaterialen de omgeving niet weerspiegelen, wordt een volledig transparant kleurmateriaal onzichtbaar. Dit is anders voor [PBR materialen.](pbr-materials.md)

* **albedoMap:** Een [2D-textuur](../../concepts/textures.md) voor albedowaarden per pixel.

* **alphaClipEnabled** en **alphaClipThreshold:** Als *alphaClipEnabled* waar is, worden alle pixels waarbij de alfawaarde van albedo lager is dan *alphaClipThreshold* niet getekend. Alpha clipping kan zelfs worden gebruikt zonder transparantie en is veel sneller te renderen. Alpha geknipte materialen zijn nog steeds langzamer te renderen dan volledig ondoorzichtige materialen, dat wel. Standaard is alpha clipping uitgeschakeld.

* **textureCoordinateScale** en **textureCoordinateOffset:** De schaal wordt vermenigvuldigd in de UV-structuurcoördinaten, de verschuiving wordt eraan toegevoegd. Kan worden gebruikt om de texturen uit te rekken en te verschuiven. De standaardschaal is (1, 1) en offset is (0, 0).

* **useVertexColor:** Als het net hoekpuntkleuren bevat en deze optie is ingeschakeld, worden de hoekpuntkleuren van de mazen vermenigvuldigd tot de *albedoColor* en *albedoMap.* Standaard zijn vertex-kleuren uitgeschakeld.

* **isDoubleSided:** Als dubbelzijdigheid is ingesteld op true, driehoeken met dit materiaal worden weergegeven, zelfs als de camera kijkt naar hun achterste gezichten. Standaard is deze optie uitgeschakeld. Zie ook [Single-sided rendering](single-sided-rendering.md).

## <a name="color-material-properties"></a>Eigenschappen van kleurmateriaal

De volgende eigenschappen zijn specifiek voor kleurmaterialen:

* **vertexMix:** Deze waarde `0` `1` tussen en geeft aan hoe sterk de hoekpuntkleur in een [net](../../concepts/meshes.md) bijdraagt aan de uiteindelijke kleur. Bij de standaardwaarde van 1 wordt de hoekpuntkleur volledig vermenigvuldigd tot de albedo-kleur. Met een waarde van 0 worden de hoekpuntkleuren volledig genegeerd.

* **transparantieMode:** In tegenstelling tot [PBR-materialen](pbr-materials.md)maken kleurmaterialen onderscheid tussen verschillende transparantiemodi:

  1. **Ondoorzichtig:** De standaardmodus schakelt transparantie uit. Alpha clipping is nog steeds mogelijk, hoewel, en moet de voorkeur hebben, indien voldoende.
  
  1. **AlphaBlended:** Deze modus is vergelijkbaar met de transparantiemodus voor PBR-materialen. Het moet worden gebruikt voor doorzichtige materialen zoals glas.

  1. **Toevoegingsmiddel:** Deze modus is de eenvoudigste en meest efficiënte transparantiemodus. De bijdrage van het materiaal wordt toegevoegd aan de gerenderde afbeelding. Deze modus kan worden gebruikt om gloeiende (maar nog steeds transparante) objecten te simuleren, zoals markeringen die worden gebruikt voor het markeren van belangrijke objecten.

## <a name="next-steps"></a>Volgende stappen

* [PBR-materialen](pbr-materials.md)
* [Texturen](../../concepts/textures.md)
* [Netten](../../concepts/meshes.md)
