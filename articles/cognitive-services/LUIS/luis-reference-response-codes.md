---
title: API HTTP-antwoord codes-LUIS
titleSuffix: Azure Cognitive Services
description: Inzicht in welke HTTP-responscodes zijn geretourneerd door de LUIS ontwerpen en de eindpunt-API 's
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 405381a2d80ee6469b2b90bcb25923004fe55882
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270255"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Veelvoorkomende API-respons codes en hun betekenis

De [ontwerpen](https://go.microsoft.com/fwlink/?linkid=2092087) en [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) api's retour neren http-antwoord codes. Terwijl antwoordberichten informatie die specifiek zijn voor een aanvraag bevatten, is de HTTP-antwoordstatuscode algemeen.

## <a name="common-status-codes"></a>Algemene statuscodes
De volgende tabel bevat een aantal van de meest voorkomende status codes voor HTTP-antwoorden voor de api's voor [ontwerpen](https://go.microsoft.com/fwlink/?linkid=2092087) en [eind punten](https://go.microsoft.com/fwlink/?linkid=2092356) :

|Code|API|Uitleg|
|:--|--|--|
|400|Ontwerpen, eindpunt|de aanvraagparameters zijn onjuist wat betekent dat de vereiste parameters ontbreken, onjuist gevormd of te groot zijn|
|400|Ontwerpen, eindpunt|de aanvraagtekst is onjuist wat betekent dat de JSON is ontbreekt, is onjuist gevormd of te groot|
|401|Ontwerpen|eindpuntsleutel-abonnement, gebruikt in plaats van de sleutel ontwerpen|
|401|Ontwerpen, eindpunt|sleutel ongeldig, ongeldig of leeg zijn|
|401|Ontwerpen, eindpunt| sleutel komt niet overeen met de regio|
|401|Ontwerpen|u bent niet de eigenaar of de samenwerker|
|401|Ontwerpen|Ongeldige volgorde van de API-aanroepen|
|403|Ontwerpen, eindpunt|totale maandelijkse sleutel quotum overschreden|
|409|Eindpunt|toepassing is geladen.|
|410|Eindpunt|toepassing moet worden retrained en gepubliceerd|
|414|Eindpunt|query groter is dan het maximum aantal tekens|
|429|Ontwerpen, eindpunt|Limiet wordt overschreden (aanvragen/seconde)|

## <a name="next-steps"></a>Volgende stappen

* Documentatie over REST API- [ontwerpen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) en- [eind punten](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
