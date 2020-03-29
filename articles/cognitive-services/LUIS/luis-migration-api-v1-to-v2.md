---
title: v1-v2-API-migratie
titleSuffix: Azure Cognitive Services
description: Het eindpunt van versie 1 en het schrijven van API's voor taalbegrip worden afgeschaft. Gebruik deze handleiding om te begrijpen hoe u migreren naar versie 2-eindpunt en het ontwerpen van API's.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 2f67bf0951ef8928297c71e8fc9f924cf05c63f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68932696"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>API v1-v2-migratiehandleiding voor LUIS-apps
Het [eindpunt](https://aka.ms/v1-endpoint-api-docs) van versie 1 en [het maken van](https://aka.ms/v1-authoring-api-docs) API's zijn afgeschaft. Gebruik deze handleiding om te begrijpen hoe u migreren naar versie [2-eindpunt](https://go.microsoft.com/fwlink/?linkid=2092356) en [het ontwerpen van](https://go.microsoft.com/fwlink/?linkid=2092087) API's. 

## <a name="new-azure-regions"></a>Nieuwe Azure-regio's
LUIS heeft nieuwe [regio's](https://aka.ms/LUIS-regions) voorzien voor de LUIS API's. LUIS biedt een ander portaal voor regiogroepen. De toepassing moet worden geschreven in dezelfde regio die u verwacht op te vragen. Toepassingen migreren niet automatisch regio's. U exporteert de app vanuit de ene regio en importeert vervolgens naar een andere regio om beschikbaar te zijn in een nieuwe regio.

## <a name="authoring-route-changes"></a>Wijzigingen in ontwerproute
De api-route voor het ontwerpen is gewijzigd van het gebruik van de **progroute** naar het gebruik van de **api-route.**


| versie | route |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Wijzigingen in eindpuntroute
De endpoint API heeft nieuwe querytekenreeksparameters en een andere respons. Als de breedbosevlag waar is, worden alle intenties, ongeacht de score, geretourneerd in een array met de naam intents, naast de topScoringIntent.

| versie | GET-route |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]|


v1 endpoint success response:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 endpoint success response:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Sleutelbeheer niet meer in API
De abonnementseindpuntsleutel API's worden afgeschaft en keren 410 GONE terug.

| versie | route |
|--|--|
|1|/luis/v1.0/prog/abonnementen|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

[Azure-eindpuntsleutels](luis-how-to-azure-subscription.md) worden gegenereerd in de Azure-portal. U wijst de sleutel toe aan een LUIS-app op de **[pagina Publiceren.](luis-how-to-azure-subscription.md)** U hoeft de werkelijke sleutelwaarde niet te weten. LUIS gebruikt de abonnementsnaam om de toewijzing te maken. 

## <a name="new-versioning-route"></a>Nieuwe versieroute
Het v2-model is nu opgenomen in een [versie.](luis-how-to-manage-versions.md) Een versienaam is 10 tekens in de route. De standaardversie is "0.1".

| versie | route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entiteiten|
|2|/luis/**api**/v2.0/apps/{appId}/**versies**/{versionId}/entiteiten|

## <a name="metadata-renamed"></a>Metagegevens omgedoopt
Verschillende API's die LUIS-metadata retourneren, hebben nieuwe namen.

| v1-routenaam | v2-routenaam |
|--|--|
|PersonalAssistantApps |Assistenten|
|toepassingsculturen|culturen|
|toepassingsdomeinen|Domeinen|
|toepassingsgebruiksscenario's|gebruiksscenario's|


## <a name="sample-renamed-to-suggest"></a>"Voorbeeld" omgedoopt tot "suggereren"
LUIS stelt uitingen voor van bestaande [eindpuntuitingen](luis-how-to-review-endpoint-utterances.md) die het model kunnen verbeteren. In de vorige versie werd dit **voorbeeld**genoemd. In de nieuwe versie wordt de naam gewijzigd van voorbeeld om **te suggereren**. Dit wordt **[Eindpuntuitingen controleren](luis-how-to-review-endpoint-utterances.md)** op de LUIS-website genoemd.

| versie | route |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entiteiten/{entityId}/**voorbeeld**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**voorbeeld**|
|2|/luis/**api**/v2.0/apps/{appId}/**versies**/{versionId}/entiteiten/{entityId}/**stel voor**|
|2|/luis/**api**/v2.0/apps/{appId}/**versies**/{versionId}/intents/{intentId}/**stel voor**|


## <a name="create-app-from-prebuilt-domains"></a>App maken op basis van vooraf gebouwde domeinen
[Vooraf gebouwde domeinen](luis-how-to-use-prebuilt-domains.md) bieden een vooraf gedefinieerd domeinmodel. Met vooraf gebouwde domeinen u uw LUIS-toepassing snel ontwikkelen voor algemene domeinen. Met deze API u een nieuwe app maken op basis van een vooraf gebouwd domein. Het antwoord is de nieuwe appID.

|v2-route|Werkwoord|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |krijgen, posten|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |ophalen|

## <a name="importing-1x-app-into-2x"></a>1.x-app importeren in 2.x
De JSON van de geëxporteerde 1.x-app heeft een aantal gebieden die u moet wijzigen voordat u importeert in [LUIS][LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Vooraf gemaakte entiteiten 
De [vooraf gebouwde entiteiten](luis-prebuilt-entities.md) zijn gewijzigd. Zorg ervoor dat u de V2-voorgebouwde entiteiten gebruikt. Dit omvat het gebruik van [datetimeV2](luis-reference-prebuilt-datetimev2.md), in plaats van datumtijd. 

### <a name="actions"></a>Acties
De eigenschap acties is niet langer geldig. Het moet een lege 

### <a name="labeled-utterances"></a>Gelabelde uitingen
V1 stond gelabelde uitingen toe om spaties aan het begin of einde van het woord of de woordgroep op te nemen. Verwijderde de spaties. 

## <a name="common-reasons-for-http-response-status-codes"></a>Veelvoorkomende redenen voor HTTP-antwoordstatuscodes
Zie [LUIS API-antwoordcodes](luis-reference-response-codes.md).

## <a name="next-steps"></a>Volgende stappen

Gebruik de v2-API-documentatie om bestaande REST-aanroepen bij te werken naar [LUIS-eindpunten](https://go.microsoft.com/fwlink/?linkid=2092356) en [het ontwerpen van](https://go.microsoft.com/fwlink/?linkid=2092087) API's. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
