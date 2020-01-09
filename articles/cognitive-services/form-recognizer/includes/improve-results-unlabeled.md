---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379285"
---
Controleer de `"confidence"` waarden voor elke sleutel/waarde resultaat onder het knoop punt `"pageResults"`. U moet ook de betrouwbaarheids scores in het `"readResults"` knoop punt bekijken, die overeenkomen met de Lees bewerking voor tekst. Het vertrouwen van de Lees resultaten heeft geen invloed op het vertrouwen van de extractie resultaten voor sleutel/waarde, dus u moet beide controleren.
* Als de betrouwbaarheids scores voor de Lees bewerking laag zijn, probeert u de kwaliteit van uw invoer documenten te verbeteren (Zie [invoer vereisten](../overview.md#input-requirements)).
* Als de betrouwbaarheids scores voor de extractie bewerking voor sleutel/waarde laag zijn, moet u ervoor zorgen dat de documenten die worden geanalyseerd van hetzelfde type zijn als documenten die worden gebruikt in de Trainingsset. Als de documenten in de Trainingsset variaties in de weer gave hebben, kunt u overwegen deze te splitsen in verschillende mappen en een model voor elke variatie te trainen.

De betrouwbaarheids scores die u aanwijst, zijn afhankelijk van uw use-case, maar meestal is het een goed idee om een Score van 80% of hoger te bereiken. Voor meer gevoelige gevallen, zoals het lezen van medische records of facturerings overzichten, wordt een Score van 100% aanbevolen.