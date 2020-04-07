---
title: Vliegtuigen knippen
description: Legt uit wat gesneden vliegtuigen zijn en hoe ze te gebruiken
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681023"
---
# <a name="cut-planes"></a>Vliegtuigen knippen

Een *gesneden vlak* is een visuele functie die pixels clips aan de ene kant van een virtueel vlak, het onthullen van de binnenkant van [mazen](../../concepts/meshes.md).
De afbeelding hieronder toont het effect. Links toont het originele gaas, rechts kan men in het gaas kijken:

![Gesneden vliegtuig](./media/cutplane-1.png)

## <a name="limitations"></a>Beperkingen

* Azure Remote Rendering ondersteunt vooralsnog **maximaal acht actieve cut-vlakken.** U meer gesneden vlakonderdelen maken, maar als u meer tegelijk probeert in te schakelen, wordt de activering genegeerd. Schakel eerst andere vlakken uit als u wilt schakelen welke component van invloed moet zijn op de scène.
* Elk gesneden vlak beïnvloedt alle op afstand weergegeven objecten. Er is momenteel geen manier om specifieke objecten of mesh-onderdelen uit te sluiten.
* Gesneden vlakken zijn puur een visuele functie, ze hebben geen invloed op de uitkomst van [ruimtelijke query's.](spatial-queries.md) Als u wilt ray gegoten in een cut-open mesh, u het beginpunt van de straal aan te passen aan de cut-vlak. Op deze manier kan de straal alleen zichtbare delen raken.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Elk actief gesneden vlak maakt een kleine kosten tijdens het renderen. Schakel gesneden vlakken uit of verwijder ze wanneer ze niet nodig zijn.

## <a name="cutplanecomponent"></a>CutPlaneComponent CutPlaneComponent

U voegt een gesneden vlak toe aan de scène door een *CutPlaneComponent*te maken. De locatie en oriëntatie van het vlak wordt bepaald door de [eigenaar](../../concepts/entities.md)entiteit van het onderdeel.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Eigenschappen CutPlaneComponent

De volgende eigenschappen worden blootgesteld op een onderdeel van een gesneden vlak:

* **Ingeschakeld:** U afgesneden vlakken tijdelijk uitschakelen door het onderdeel uit te schakelen. Uitgeschakelde gesneden vliegtuigen niet leiden tot rendering overhead en ook niet meetellen voor de wereldwijde cut plane limiet.

* **Normaal:** Hiermee geeft u op welke richting (+X,-X,+Y,-Y,+Z,-Z) wordt gebruikt als normaal vlak. Deze richting is relatief ten opzichte van de oriëntatie van de eigenaarentiteit. Verplaats en draai de entiteit van de eigenaar voor de exacte plaatsing.

* **FadeColor** en **FadeLength:**

  Als de alfawaarde van *FadeColor* niet-nul is, vervagen pixels dicht bij het snijvlak naar het RGB-gedeelte van FadeColor. De sterkte van het alfakanaal bepaalt of het volledig zal vervagen naar de fade-kleur of slechts gedeeltelijk. *FadeLength* bepaalt over welke afstand deze fade zal plaatsvinden.

## <a name="next-steps"></a>Volgende stappen

* [Eenzijdige rendering](single-sided-rendering.md)
* [Ruimtelijke query's](spatial-queries.md)
