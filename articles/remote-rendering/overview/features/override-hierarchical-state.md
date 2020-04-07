---
title: Hiërarchische status overschrijven
description: Hiermee wordt het concept van hiërarchische statusoverschrijvencomponenten uitgelegd.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680789"
---
# <a name="hierarchical-state-override"></a>Hiërarchische status overschrijven

In veel gevallen is het noodzakelijk om het uiterlijk van onderdelen van een [model](../../concepts/models.md)dynamisch te veranderen, bijvoorbeeld het verbergen van subgrafen of het overschakelen van onderdelen naar transparante weergave. Het veranderen van de materialen van elk betrokken onderdeel is niet praktisch, omdat het vereist om te herhalen over de hele scène grafiek, en het beheer van materiaal klonen en toewijzing op elk knooppunt.

Gebruik de `HierarchicalStateOverrideComponent`. Deze component implementeert hiërarchische statusupdates op willekeurige takken van de scènegrafiek. Dat betekent dat een status kan worden gedefinieerd op elk niveau in de scènegrafiek en door de hiërarchie sijpelt totdat deze wordt overschreven door een nieuwe status of wordt toegepast op een bladobject.

Als voorbeeld, overweeg het model van een auto en u wilt de hele auto te schakelen om transparant te zijn, met uitzondering van de binnenste motor deel. Deze use case omvat slechts twee exemplaren van de component:

* Het eerste onderdeel wordt toegewezen aan het hoofdknooppunt van het model en schakelt transparante weergave in voor de hele auto.
* Het tweede onderdeel wordt toegewezen aan het hoofdknooppunt van de engine en overschrijft de status opnieuw door de doorzichtingsmodus expliciet uit te schakelen.

## <a name="features"></a>Functies

De vaste set van staten die kunnen worden overschreven zijn:

* **Verborgen**: De respectieve mazen in de scènegrafiek worden verborgen of getoond.
* **Tintkleur:** een gerenderde object kan kleurkleur krijgen met zijn individuele tintkleur en tintgewicht. De afbeelding hieronder toont kleur tinting de velg van een wiel.
  
  ![Kleurtint](./media/color-tint.png)

* **Doorzien**: De geometrie wordt semi-transparant weergegeven, bijvoorbeeld om de binnenste delen van een object te onthullen. De volgende afbeelding toont de hele auto wordt weergegeven in see-through mode, met uitzondering van de rode remklauw:

  ![See-through](./media/see-through.png)

  > [!IMPORTANT]
  > Het doorzichtingseffect werkt alleen wanneer de [TileBasedComposition-weergavemodus](../../concepts/rendering-modes.md) wordt gebruikt. *TileBasedComposition*

* **Geselecteerd**: De geometrie wordt weergegeven met een [selectieoverzicht](outlines.md).

  ![Selectieoverzicht](./media/selection-outline.png)

* **DisableCollision**: De geometrie is vrijgesteld van [ruimtelijke query's.](spatial-queries.md) De **verborgen** vlag schakelt botsingen niet uit, dus deze twee vlaggen worden vaak samen ingesteld.

## <a name="hierarchical-overrides"></a>Hiërarchische overschrijvingen

Het `HierarchicalStateOverrideComponent` kan worden bevestigd op meerdere niveaus van een objecthiërarchie. Aangezien er slechts één component van elk type `HierarchicalStateOverrideComponent` op een entiteit kan zijn, beheert elk de statussen voor verborgen, doorzichtige, geselecteerde, kleurtint en botsing.

Daarom kan elke staat worden ingesteld op een van:

* `ForceOn`- de status is ingeschakeld voor alle mazen op en onder dit knooppunt
* `ForceOff`- de staat is uitgeschakeld voor alle mazen op en onder dit knooppunt
* `InheritFromParent`- de status wordt niet beïnvloed door deze override component

U de status `SetState` direct of via de functie wijzigen:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>Tint-kleur

De tint kleur overschrijven is iets speciaal in dat er zowel een aan / uit / erven staat en een tint kleur eigenschap. Het alfagedeelte van de tintkleur definieert het gewicht van het kleureffect: Als ingesteld op 0,0 is er geen tintkleur zichtbaar en als het object is ingesteld op 1.0, wordt het object weergegeven met een zuivere tintkleur. Voor tussenwaarden wordt de uiteindelijke kleur gemengd met de tintkleur. De tintkleur kan per frame worden gewijzigd om een kleuranimatie te bereiken.

## <a name="performance-considerations"></a>Prestatieoverwegingen

Een instantie `HierarchicalStateOverrideComponent` van zichzelf voegt niet veel runtime overhead toe. Het is echter altijd een goede gewoonte om het aantal actieve componenten laag te houden. Wanneer u bijvoorbeeld een selectiesysteem implementeert dat het gekozen object markeert, wordt aanbevolen de component te verwijderen wanneer de markering wordt verwijderd. Het houden van de componenten rond met neutrale functies kan snel optellen.

Transparante rendering zorgt voor meer werkbelasting op de GPU's van de server dan op standaardrendering. Als grote delen van de scènegrafiek worden overgeschakeld op *doorzichting*, waarbij veel geometrielagen zichtbaar zijn, kan dit een prestatieknelpunt worden. Hetzelfde geldt voor objecten met [selectiecontouren.](../../overview/features/outlines.md#performance)

## <a name="next-steps"></a>Volgende stappen

* [Schetst](../../overview/features/outlines.md)
* [Weergavemodi](../../concepts/rendering-modes.md)
* [Ruimtelijke query's](../../overview/features/spatial-queries.md)
