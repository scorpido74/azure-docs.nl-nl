---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379285"
---
Controleer `"confidence"` de waarden voor elk sleutel-/waarderesultaat onder het `"pageResults"` knooppunt. U moet ook kijken naar `"readResults"` de betrouwbaarheidsscores in het knooppunt, die overeenkomen met de tekstleesbewerking. Het vertrouwen van de leesresultaten heeft geen invloed op het vertrouwen van de belangrijkste/waardeextractieresultaten, dus u moet beide controleren.
* Als de betrouwbaarheidsscores voor de leesbewerking laag zijn, probeert u de kwaliteit van uw invoerdocumenten te verbeteren (zie [Invoervereisten).](../overview.md#input-requirements)
* Als de betrouwbaarheidsscores voor de toets-/waardeextractiebewerking laag zijn, moet u ervoor zorgen dat de documenten die worden geanalyseerd van hetzelfde type zijn als documenten die in de trainingsset worden gebruikt. Als de documenten in de trainingsset variaties in uiterlijk hebben, u overwegen deze in verschillende mappen op te splitsen en één model voor elke variant op te zetten.

De vertrouwensscores die u target, zijn afhankelijk van uw use case, maar over het algemeen is het een goede gewoonte om een score van 80% of hoger te targeten. Voor meer gevoelige gevallen, zoals het lezen van medische dossiers of factureringsverklaringen, wordt een score van 100% aanbevolen.