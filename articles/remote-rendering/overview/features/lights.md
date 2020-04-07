---
title: Lampen
description: Beschrijving en eigenschappen van lichtbron
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680945"
---
# <a name="lights"></a>Lampen

Standaard worden de op afstand gerenderde objecten verlicht met behulp van een [hemellicht.](sky.md) Voor de meeste toepassingen is dit al voldoende, maar u nog meer lichtbronnen toevoegen aan de scène.

> [!IMPORTANT]
> Alleen [PBR-materialen](pbr-materials.md) worden beïnvloed door lichtbronnen. [Kleurmaterialen](color-materials.md) lijken altijd volledig helder.

> [!NOTE]
> Het gieten van schaduwen wordt momenteel niet ondersteund. Azure Remote Rendering is geoptimaliseerd om enorme hoeveelheden geometrie weer te geven, waarbij indien nodig gebruik wordt gemaakt van meerdere GPU's. Traditionele benaderingen voor schaduwcasting werken niet goed in dergelijke scenario's.

## <a name="common-light-component-properties"></a>Eigenschappen van algemene lichtcomponent

Alle lichttypen zijn afgeleid `LightComponent` van de abstracte basisklasse en delen deze eigenschappen:

* **Kleur:** De kleur van het licht in [gammaruimte.](https://en.wikipedia.org/wiki/SRGB) Alpha wordt genegeerd.

* **Intensiteit:** De helderheid van het licht. Voor punt- en spotlichten bepaalt de intensiteit ook hoe ver het licht schijnt.

## <a name="point-light"></a>Puntlicht

In Azure Remote `PointLightComponent` Rendering kan het niet alleen licht uitzenden vanaf een enkel punt, maar ook vanuit een kleine bol of een kleine buis, om zachtere lichtbronnen te simuleren.

### <a name="pointlightcomponent-properties"></a>PointLightComponent-eigenschappen

* **Straal:** De standaardstraal is nul, in welk geval het licht fungeert als puntlicht. Als de straal groter is dan nul, fungeert deze als sferische lichtbron, wat het uiterlijk van spiegelende hooglichten verandert.

* **Lengte:** Als `Length` beide `Radius` en niet-nul, het licht fungeert als een buis licht. Dit kan worden gebruikt om neonbuizen te simuleren.

* **DempingCutoff:** Als het aan (0,0) wordt gelaten, hangt de `Intensity`demping van het licht alleen af van zijn . U echter aangepaste min/max-afstanden bieden waarover de intensiteit van het licht lineair wordt geschaald tot 0. Deze functie kan worden gebruikt om een kleiner bereik van invloed van een specifiek licht af te dwingen.

* **Geprojecteerde Cubemap:** Als deze is ingesteld op een geldige [kubuskaart,](../../concepts/textures.md)wordt de textuur geprojecteerd op de omringende geometrie van het licht. De kleur van de kubuskaart is gemoduleerd met de kleur van het licht.

## <a name="spot-light"></a>Spotlicht

Het `SpotLightComponent` is vergelijkbaar `PointLightComponent` met de maar het licht is beperkt tot de vorm van een kegel. De oriëntatie van de kegel wordt bepaald door de *negatieve z-as*van de eigenaar entiteit .

### <a name="spotlightcomponent-properties"></a>Eigenschappen spotlightcomponent

* **Straal:** Hetzelfde als `PointLightComponent`voor de .

* **SpotAngleDeg:** Dit interval definieert de binnen- en buitenhoek van de kegel, gemeten in mate. Alles binnen de binnenhoek wordt verlicht met volledige helderheid. Een falloff wordt toegepast op de buitenste hoek die een penumbra-achtig effect genereert.

* **FalloffExponent:** Hiermee bepaalt u hoe scherp de valovergangen tussen de binnenste en de buitenste kegelhoek worden gemaakt. Een hogere waarde resulteert in een scherpere overgang. De standaardwaarde van 1.0 resulteert in een lineaire overgang.

* **DempingCutoff:** Hetzelfde als `PointLightComponent`voor de .

* **Projected2dTexture:** Als deze is ingesteld op een geldige [2D-structuur,](../../concepts/textures.md)wordt het beeld geprojecteerd op de geometrie waarop het licht schijnt. De kleur van de textuur is gemoduleerd met de kleur van het licht.

## <a name="directional-light"></a>Richtingslicht

Het `DirectionalLightComponent` simuleert een lichtbron die oneindig ver weg is. Het licht schijnt in de richting van de *negatieve z-as van de eigenaar entiteit*. De positie van de entiteit wordt genegeerd.

Er zijn geen extra eigenschappen.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Lichtbronnen hebben een aanzienlijke invloed op de renderprestaties. Gebruik ze zorgvuldig en alleen indien vereist door de toepassing. Elke statische globale lichttoestand, inclusief een statische directionele component, kan worden bereikt met een [aangepaste hemeltextuur,](sky.md)zonder extra renderingkosten.

## <a name="next-steps"></a>Volgende stappen

* [Materialen](../../concepts/materials.md)
* [Sky](sky.md)
