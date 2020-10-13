---
author: baanders
description: bestand insluiten met een beschrijving van het valideren van Azure Digital Apparaatdubbels-modellen
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: fc7c31d1ab82e2e7edc74fb57233e8a0891fd113
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91974958"
---
> [!TIP]
> Nadat u een model hebt gemaakt, is het raadzaam om uw modellen offline te valideren voordat u ze uploadt naar uw Azure Digital Apparaatdubbels-exemplaar.

Er is een taal-neutraal-voor beeld beschikbaar voor het valideren van model documenten om ervoor te zorgen dat de DTDL juist is. Deze bevindt zich hier: [**DTDL validator**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)-voor beeld.

Het DTDL-voor beeld van de validatie functie is gebaseerd op een .NET DTDL-parser-bibliotheek, die beschikbaar is op NuGet als een bibliotheek aan de client zijde: [**micro soft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). U kunt de bibliotheek ook rechtstreeks gebruiken om uw eigen validatie oplossing te ontwerpen. Wanneer u de parser-bibliotheek gebruikt, moet u ervoor zorgen dat u een versie gebruikt die compatibel is met de versie die door Azure Digital Apparaatdubbels wordt uitgevoerd. Tijdens de preview is dit versie *3.12.4*.

Meer informatie over het voor beeld van de validator en de parser-bibliotheek, inclusief gebruiks voorbeelden, vindt u in [*procedures: modellen parseren en valideren*](../articles/digital-twins/how-to-parse-models.md).