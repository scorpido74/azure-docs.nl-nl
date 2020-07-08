---
title: Vlakken knippen
description: Legt uit wat knip abonnementen zijn en hoe u deze kunt gebruiken
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 7adf9a9701eb2492f0b13a26af1dbaf8de631373
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021361"
---
# <a name="cut-planes"></a>Vlakken knippen

Een *knip vlak* is een visuele functie die pixels aan één zijde van een virtueel vlak knipt, waardoor de binnenkant van de [netten](../../concepts/meshes.md)zichtbaar wordt.
In de onderstaande afbeelding ziet u het effect. Links ziet u de oorspronkelijke mesh, aan de rechter kant kan in het net kijken:

![Vlak knippen](./media/cutplane-1.png)

## <a name="limitations"></a>Beperkingen

* Voor de periode ondersteunt Azure remote rendering een **maximum van acht actieve knip abonnementen**. U kunt meer knip vlak onderdelen maken, maar als u meerdere tegelijk probeert in te scha kelen, wordt de activering genegeerd. Schakel eerst andere abonnementen uit als u wilt overschakelen welk onderdeel van invloed op de scène moet zijn.
* Elk knip vlak is van invloed op alle extern gerenderde objecten. Er is momenteel geen manier om specifieke objecten of netonderdelen uit te sluiten.
* Knip abonnementen zijn louter een visuele functie. ze hebben geen invloed op het resultaat van [ruimtelijke query's](spatial-queries.md). Als u een straal wilt omzetten in een cut, kunt u het begin punt van de Ray aanpassen zodat deze zich op het Knip vlak bevindt. Op deze manier kan de Ray alleen zicht bare onderdelen aanraken.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Elk actief knip vlak maakt een kleine kosten tijdens de rendering. Knip abonnementen uitschakelen of verwijderen wanneer ze niet nodig zijn.

## <a name="cutplanecomponent"></a>CutPlaneComponent

U voegt een knip vlak aan de scène toe door een *CutPlaneComponent*te maken. De locatie en richting van het vlak worden bepaald door de [entiteit](../../concepts/entities.md)van de eigenaar van het onderdeel.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->Normal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->FadeColor(fadeColor);
    cutPlane->FadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>CutPlaneComponent-eigenschappen

De volgende eigenschappen worden weer gegeven op een knip vlak onderdeel:

* `Enabled`: U kunt het knippen van abonnementen tijdelijk uitschakelen door het onderdeel uit te scha kelen. Uitgeschakelde knip abonnementen zijn geen rendering-overhead en worden ook niet meegeteld op basis van de algemene grens voor het snij vlak.

* `Normal`: Hiermee geeft u op welke richting (+ X,-X, + Y,-Y, + Z,-Z) wordt gebruikt als het vlak normaal. Deze richting is relatief ten opzichte van de richting van de eigenaar van de entiteit. Verplaats en roteer de entiteit eigenaar voor een exacte plaatsing.

* `FadeColor` en `FadeLength`:

  Als de Alfa waarde van *FadeColor* niet gelijk is aan nul, wordt pixels dicht bij het Knip vlak overvloeien naar het RGB-onderdeel van FadeColor. De sterkte van het Alfa kanaal bepaalt of deze volledig naar de vervagings kleur of slechts gedeeltelijk vervaagt. *FadeLength* definieert de afstand die de vervaging zal plaatsvinden.

## <a name="next-steps"></a>Volgende stappen

* [Rendering met één zijde](single-sided-rendering.md)
* [Ruimtelijke query's](spatial-queries.md)
