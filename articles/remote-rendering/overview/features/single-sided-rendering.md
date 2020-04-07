---
title: Eenzijdige rendering
description: Beschrijft eenzijdige rendering-instellingen en use cases
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682011"
---
# <a name="single-sided-rendering"></a>Eenzijdige rendering

De meeste renderers gebruiken [back-face ruimen](https://en.wikipedia.org/wiki/Back-face_culling) om de prestaties te verbeteren. Maar wanneer mazen worden opengesneden met [gesneden vliegtuigen,](cut-planes.md)zullen gebruikers vaak kijken naar de achterkant van driehoeken. Als die driehoeken worden weggeruimd, ziet het resultaat er niet overtuigend uit.

De manier om dit probleem betrouwbaar te voorkomen, is om driehoeken *dubbelzijdig*te maken. Omdat het niet gebruiken van back-face culling gevolgen heeft voor de prestaties, schakelt Azure Remote Rendering standaard alleen over naar dubbelzijdige rendering voor mazen die elkaar kruisen met een gesneden vlak.

Met *de eenzijdige rendering-instelling* u dit gedrag aanpassen.

> [!CAUTION]
> De eenzijdige rendering instelling is een experimentele functie. Het kan in de toekomst weer verwijderd worden. Wijzig de standaardinstelling niet, tenzij er echt een kritiek probleem in uw toepassing is opgelost.

## <a name="prerequisites"></a>Vereisten

De eenzijdige renderinginstelling heeft alleen een effect voor mazen `opaqueMaterialDefaultSidedness` die zijn `SingleSided` [geconverteerd](../../how-tos/conversion/configure-model-conversion.md) met de optie ingesteld op . Standaard is deze optie `DoubleSided`ingesteld op .

## <a name="single-sided-rendering-setting"></a>Eenzijdige renderinginstelling

Er zijn drie verschillende modi:

**Normaal:** In deze modus worden mazen altijd weergegeven terwijl ze worden omgezet. Dat betekent mazen `opaqueMaterialDefaultSidedness` omgezet `SingleSided` met set om zal altijd worden weergegeven met back-face ruimen ingeschakeld, zelfs wanneer ze elkaar kruisen een gesneden vlak.

**DynamicDoubleSiding:** In deze modus, wanneer een gesneden vlak een gaas kruist, wordt het automatisch overgeschakeld naar dubbelzijdige rendering. Deze modus is de standaardmodus.

**AlwaysDoublesided:** Dwingt alle eenzijdige geometrie te allen tijde dubbelzijdig te maken. Deze modus wordt meestal blootgesteld, zodat u de impact van de prestaties tussen eenzijdige en dubbelzijdige rendering gemakkelijk vergelijken.

Het wijzigen van de eenzijdige rendering-instellingen kan als volgt:

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

* [Vliegtuigen knippen](cut-planes.md)
* [De modelconversie configureren](../../how-tos/conversion/configure-model-conversion.md)
