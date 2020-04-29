---
title: Rendering met één zijde
description: Beschrijft instellingen voor rendering met één zijde en use cases
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682011"
---
# <a name="single-sided-rendering"></a>Rendering met één zijde

De meeste renderers [maken gebruik van Back-face](https://en.wikipedia.org/wiki/Back-face_culling) om de prestaties te verbeteren. Wanneer de mazen zijn geopend met [knip abonnementen](cut-planes.md), zien de gebruikers er vaak uit dat er drie hoeken worden weer gegeven. Als deze drie hoeken zijn verwijderd, is het resultaat niet goed.

U kunt dit probleem op een betrouw bare manier voor komen door drie *hoeken dubbelzijdig te renderen.* Omdat het gebruik van back-ups niet wordt geruimen, worden de prestaties van het systeem standaard alleen overgeschakeld naar de weer gave met dubbelzijdige rendering voor mazen die elkaar kruisen met een knip vlak.

Met de instelling voor *enkelvoudige rendering* kunt u dit gedrag aanpassen.

> [!CAUTION]
> De instelling voor enkelvoudige rendering is een experimentele functie. Het kan in de toekomst opnieuw worden verwijderd. Wijzig de standaard instelling niet, tenzij er een kritiek probleem in uw toepassing is opgelost.

## <a name="prerequisites"></a>Vereisten

De instelling voor de weer gave met één zijde heeft alleen invloed op de netten die zijn [geconverteerd](../../how-tos/conversion/configure-model-conversion.md) met de `opaqueMaterialDefaultSidedness` optie ingesteld op `SingleSided`. Deze optie is standaard ingesteld op `DoubleSided`.

## <a name="single-sided-rendering-setting"></a>Instelling voor weer gave op één zijde

Er zijn drie verschillende modi:

**Normaal:** In deze modus worden netten altijd weer gegeven wanneer ze worden geconverteerd. Dit betekent dat mazen die zijn `opaqueMaterialDefaultSidedness` geconverteerd met `SingleSided` ingesteld op, altijd worden gerenderd met behulp van de functie voor het afruimen van back-ups, zelfs wanneer ze een knip vlak overlappen.

**DynamicDoubleSiding:** Als in deze modus een knip vlak een net kruist, wordt dit automatisch overgeschakeld naar de rendering met dubbele weer gave. Deze modus is de standaard modus.

**AlwaysDoubleSided:** Hiermee wordt alle geometrie van één zijde geforceerd gerenderd op elk moment. Deze modus wordt meestal weer gegeven, zodat u eenvoudig de prestatie-impact kunt vergelijken tussen de rendering van enkelzijdige en tweezijdige weer gave.

Het wijzigen van de instellingen voor het weer geven van één zijde kan als volgt worden uitgevoerd:

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>Volgende stappen

* [Vlakken knippen](cut-planes.md)
* [De model conversie configureren](../../how-tos/conversion/configure-model-conversion.md)
