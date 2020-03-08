---
title: 'Snelstartgids: gebruik krul & REST om de Knowledge Base te beheren QnA Maker'
description: In deze Quick start ziet u hoe u uw Knowledge Base maakt, publiceert en er query's op uitvoert met behulp van de REST Api's.
ms.date: 02/27/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 00ec52fe20fb0e6a976f3e7142386e835713c98c
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851204"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Snelstartgids: gebruik krul en REST om de Knowledge Base te beheren

In deze Snelstartgids leert u hoe u uw Knowledge Base maakt, publiceert en er query's op uitvoert. Met QnA Maker worden automatisch vragen en antwoorden opgehaald uit semi-gestructureerde inhoud, zoals veelgestelde vragen, vanuit [gegevensbronnen](../Concepts/knowledge-base.md). Het model voor de knowledge base wordt gedefinieerd in de JSON die in de hoofdtekst van de API-aanvraag wordt verzonden.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* De huidige versie van [krul](https://curl.haxx.se/). In de Quick starts worden verschillende opdracht regel opties gebruikt, die in de [krul documentatie](https://curl.haxx.se/docs/manpage.html)worden vermeld.
* U moet een [QnA Maker resource](../How-To/set-up-qnamaker-service-azure.md)hebben. Als u de sleutel en de resource naam wilt ophalen, selecteert u **Quick** start voor uw resource in het Azure Portal. De resource naam is het eerste deel van de eind punt-URL:

    `https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> De volgende BASH-voor beelden gebruiken het `\` regel voortzettings teken. Als u de console of Terminal een ander regel vervolg teken gebruikt, gebruikt u dit teken.

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

Als u een Knowledge Base wilt maken met de REST Api's en krul, hebt u de volgende informatie nodig:

|Informatie|Krul configuratie|Doel|
|--|--|--|
|Resource naam QnA Maker|URL|gebruikt voor het maken van een URL|
|Bron sleutel QnA Maker|`-h` para meter voor `Ocp-Apim-Subscription-Key` koptekst|Verifiëren bij QnA Maker-service|
|JSON met een beschrijving van de kennis database|`-d` param|[Voor beelden](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) van JSON|
|Grootte van de JSON in bytes|`-h` para meter voor `Content-Size` koptekst||

De krul opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resource naam, resource sleutel en JSON-waarden en grootte van JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

De krul reactie van QnA Maker bevat de `operationId`, die vereist is om de [status van de bewerking](#get-status-of-operation)op te halen.

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>De status van de bewerking ophalen

Wanneer u een Knowledge Base maakt, omdat de bewerking async is, bevat het antwoord informatie om de status te bepalen.

|Informatie|Krul configuratie|Doel|
|--|--|--|
|Resource naam QnA Maker|URL|gebruikt voor het maken van een URL|
|Bewerkings-id|URL-route|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Bron sleutel QnA Maker|`-h` para meter voor `Ocp-Apim-Subscription-Key` koptekst|Verifiëren bij QnA Maker-service|

De krul opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resource naam, resource sleutel en bewerkings-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Het krul antwoord bevat de status. Als de bewerkings status is geslaagd, bevat de `resourceLocation` de Knowledge Base-ID.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Knowledge base publiceren

Voordat u een query uitvoert op de Knowledge Base, moet u het volgende doen:
* Knowledge base publiceren
* De runtime-eindpunt sleutel ophalen

Met deze taak wordt de Knowledge Base gepubliceerd. Het ophalen van de runtime-eindpunt sleutel is een [afzonderlijke taak](#get-published-knowledge-bases-runtime-endpoint-key).

|Informatie|Krul configuratie|Doel|
|--|--|--|
|Resource naam QnA Maker|URL|gebruikt voor het maken van een URL|
|Bron sleutel QnA Maker|`-h` para meter voor `Ocp-Apim-Subscription-Key` koptekst|Verifiëren bij QnA Maker-service|
|Knowledge Base-id|URL-route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

De krul opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resource naam, resource sleutel en Knowledge Base-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

De antwoord status is 204 zonder resultaten. Gebruik de opdracht regel parameter `-v` om uitgebreide uitvoer voor de opdracht krul weer te geven. Dit geldt ook voor de HTTP-status.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>De runtime-eindpunt sleutel van de gepubliceerde kennis basis ophalen

Voordat u een query uitvoert op de Knowledge Base, moet u het volgende doen:
* Knowledge base publiceren
* De runtime-eindpunt sleutel ophalen

Met deze taak wordt de runtime-eindpunt sleutel opgehaald. Het publiceren van de Knowledge Base is een [afzonderlijke taak](#publish-knowledge-base).

De runtime-eindpunt sleutel is dezelfde sleutel voor alle kennis grondslagen met behulp van de QnA Maker resource.

|Informatie|Krul configuratie|Doel|
|--|--|--|
|Resource naam QnA Maker|URL|gebruikt voor het maken van een URL|
|Bron sleutel QnA Maker|`-h` para meter voor `Ocp-Apim-Subscription-Key` koptekst|Verifiëren bij QnA Maker-service|

De krul opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resource naam, resource sleutel.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Het krul antwoord bevat de runtime-eindpunt sleutels. Gebruik slechts één van de sleutels bij het opvragen van een antwoord uit de Knowledge Base.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Query voor antwoord van gepubliceerde kennis basis

Het verkrijgen van een antwoord van de kennis geschiedt vanuit een afzonderlijke runtime dan het beheer van de Knowledge Base. Omdat het een afzonderlijke runtime is, moet u verifiëren met een runtime sleutel.

|Informatie|Krul configuratie|Doel|
|--|--|--|
|Resource naam QnA Maker|URL|gebruikt voor het maken van een URL|
|Runtime sleutel QnA Maker|`-h` para meter voor `Authorization` koptekst|De sleutel maakt deel uit van een teken reeks die het woord `Endpointkey `bevat. Verifiëren bij QnA Maker-service|
|Knowledge Base-id|URL-route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON-beschrijving van query|`-d` param|[Aanvraag hoofdtekst parameters](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) en [voor beelden](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) van JSON|
|Grootte van de JSON in bytes|`-h` para meter voor `Content-Size` koptekst||

De krul opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resource naam, resource sleutel en Knowledge Base-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Een geslaagd antwoord omvat het beste antwoord samen met andere informatie over een client toepassing, zoals een bot, moet een antwoord op de gebruiker weer geven.

## <a name="delete-knowledge-base"></a>Knowledge Base verwijderen

Wanneer u klaar bent met de Knowledge Base, verwijdert u deze.

|Informatie|Krul configuratie|Doel|
|--|--|--|
|Resource naam QnA Maker|URL|gebruikt voor het maken van een URL|
|Bron sleutel QnA Maker|`-h` para meter voor `Ocp-Apim-Subscription-Key` koptekst|Verifiëren bij QnA Maker-service|
|Knowledge Base-id|URL-route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

De krul opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resource naam, resource sleutel en Knowledge Base-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

De antwoord status is 204 zonder resultaten. Gebruik de opdracht regel parameter `-v` om uitgebreide uitvoer voor de opdracht krul weer te geven. Dit geldt ook voor de HTTP-status.

## <a name="additional-resources"></a>Aanvullende resources

* [Ontwerpen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Referentie documentatie
* [Runtime](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Referentie documentatie
* [Voor beelden van BASH-scripts met krul](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
