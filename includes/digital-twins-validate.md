---
author: baanders
description: bestand insluiten met een beschrijving van het valideren van Azure Digital Apparaatdubbels-modellen
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985870"
---
> [!TIP]
> Nadat u een model hebt gemaakt, is het raadzaam om uw modellen offline te valideren voordat u ze uploadt naar uw Azure Digital Apparaatdubbels-exemplaar.

Er is een taal-neutraal-voor beeld beschikbaar voor het valideren van model documenten om ervoor te zorgen dat de DTDL juist is. Deze bevindt zich hier: [**DTDL validator**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)-voor beeld.

Het DTDL-voor beeld van de validatie functie is gebaseerd op een .NET DTDL-parser-bibliotheek, die beschikbaar is op NuGet als een bibliotheek aan de client zijde: [**micro soft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). U kunt de bibliotheek ook rechtstreeks gebruiken om uw eigen validatie oplossing te ontwerpen. Wanneer u de parser-bibliotheek gebruikt, moet u ervoor zorgen dat u een versie gebruikt die compatibel is met de versie die door Azure Digital Apparaatdubbels wordt uitgevoerd. Tijdens de preview is dit versie *3.7.0*.

Meer informatie over het voor beeld van de validator en de parser-bibliotheek, inclusief gebruiks voorbeelden, vindt u in [*procedures: modellen parseren en valideren*](../articles/digital-twins/how-to-parse-models.md).