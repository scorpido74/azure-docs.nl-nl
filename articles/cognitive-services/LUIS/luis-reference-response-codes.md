---
title: API HTTP-antwoordcodes - LUIS
titleSuffix: Azure Cognitive Services
description: Begrijpen welke HTTP-antwoordcodes worden geretourneerd van de LUIS-ontwerp- en eindpunt-API's
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270255"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Gemeenschappelijke API-antwoordcodes en de betekenis ervan

De [auteurs-](https://go.microsoft.com/fwlink/?linkid=2092087) en [eindpunt-API's](https://go.microsoft.com/fwlink/?linkid=2092356) retourneren HTTP-antwoordcodes. Hoewel antwoordberichten informatie bevatten die specifiek is voor een verzoek, is de HTTP-antwoordstatuscode algemeen.

## <a name="common-status-codes"></a>Gemeenschappelijke statuscodes
In de volgende tabel worden enkele van de meest voorkomende HTTP-antwoordstatuscodes voor de [ontwerp-](https://go.microsoft.com/fwlink/?linkid=2092087) en [eindpunt-API's](https://go.microsoft.com/fwlink/?linkid=2092356) weergegeven:

|Code|API|Uitleg|
|:--|--|--|
|400|Auteur, Eindpunt|de parameters van de aanvraag onjuist zijn, wat betekent dat de vereiste parameters ontbreken, misvormd of te groot zijn|
|400|Auteur, Eindpunt|het lichaam van het verzoek onjuist is, wat betekent dat de JSON ontbreekt, misvormd of te groot is|
|401|Ontwerpen|gebruikte abonnementssleutel voor eindpunt, in plaats van de sleutel voor het schrijven|
|401|Auteur, Eindpunt|ongeldige, misvormde of lege sleutel|
|401|Auteur, Eindpunt| toets komt niet overeen met regio|
|401|Ontwerpen|u bent niet de eigenaar of medewerker|
|401|Ontwerpen|ongeldige volgorde van API-aanroepen|
|403|Auteur, Eindpunt|totale maandelijkse maximumquotumlimiet overschreden|
|409|Eindpunt|toepassing is nog steeds laden|
|410|Eindpunt|toepassing moet worden omgeschoold en opnieuw worden gepubliceerd|
|414|Eindpunt|query overschrijdt maximale tekenlimiet|
|429|Auteur, Eindpunt|Tarieflimiet wordt overschreden (aanvragen/seconde)|

## <a name="next-steps"></a>Volgende stappen

* REST [API-ontwerp-](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) en [eindpuntdocumentatie](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
