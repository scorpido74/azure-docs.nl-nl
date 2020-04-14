---
title: 'Snelstart: gebruik cURL & REST om kennisbank te beheren - QnA Maker'
description: Deze quickstart laat u zien hoe u uw kennisbank maken, publiceren en opvragen met behulp van de REST API's.
ms.date: 04/13/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: facc45ab8f916181f7eeceb65c5102a60ae7d7e9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261700"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Snelstart: gebruik cURL en REST om kennisbank te beheren

Deze quickstart leidt u door het maken, publiceren en opvragen van uw kennisbank. Met QnA Maker worden automatisch vragen en antwoorden opgehaald uit semi-gestructureerde inhoud, zoals veelgestelde vragen, vanuit [gegevensbronnen](../Concepts/knowledge-base.md). Het model voor de knowledge base wordt gedefinieerd in de JSON die in de hoofdtekst van de API-aanvraag wordt verzonden.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* De huidige versie van [cURL](https://curl.haxx.se/). Verschillende command-line switches worden gebruikt in de quickstarts, die worden opgemerkt in de [cURL documentatie](https://curl.haxx.se/docs/manpage.html).
* U moet een [QnA Maker-bron](../How-To/set-up-qnamaker-service-azure.md)hebben om de naam van de sleutel en resource te gebruiken. U hebt de **resourcenaam** ingevoerd tijdens het maken van resources en vervolgens is de sleutel voor u gemaakt. De bronnaam wordt gebruikt als subdomein voor uw eindpunt. Als u de naam van uw sleutel en bron wilt ophalen, selecteert u **Quickstart** voor uw resource in de Azure-portal. De bronnaam is het eerste subdomein van de URL van het eindpunt:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> In de volgende BASH-voorbeelden wordt het teken van de `\` lijnvoortzetting gebruikt. Als u een ander regelvervolgteken gebruikt, gebruikt u dit teken.

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

Als u een kennisbank wilt maken met de REST API's en cURL, moet u over de volgende informatie beschikken:

|Informatie|cURL-configuratie|Doel|
|--|--|--|
|QnA Maker-bronnaam|URL|gebruikt om URL te construeren|
|QnA Maker-resourcesleutel|`-h`param `Ocp-Apim-Subscription-Key` voor koptekst|Verifiëren naar QnA Maker-service|
|JSON beschrijft kennisbank|`-d`Param|[Voorbeelden](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) van JSON|
|Grootte van de JSON in bytes|`-h`param `Content-Size` voor koptekst||

De cURL-opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resourcenaam, resourcesleutel en JSON-waarden en de grootte van JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Het cURL-antwoord van QnA Maker omvat het , dat nodig is om `operationId` de [status van de bewerking](#get-status-of-operation)te krijgen .

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Status van de bewerking opdoen

Wanneer u een kennisbasis maakt, omdat de bewerking async is, bevat het antwoord informatie om de status te bepalen.

|Informatie|cURL-configuratie|Doel|
|--|--|--|
|QnA Maker-bronnaam|URL|gebruikt om URL te construeren|
|Bedrijfs-id|URL-route|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker-resourcesleutel|`-h`param `Ocp-Apim-Subscription-Key` voor koptekst|Verifiëren naar QnA Maker-service|

De cURL-opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resourcenaam, resourcesleutel en bewerkings-id.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Het cURL-antwoord bevat de status. Als de bewerkingsstatus is geslaagd, bevat deze `resourceLocation` de kennisbasis-ID.

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

Voordat u de kennisbank opvraagt, moet u:
* Knowledge base publiceren
* De eindpunttoets runtime oppakken

Deze taak publiceert de kennisbank. Het verkrijgen van de runtime-eindpuntsleutel is een [afzonderlijke taak](#get-published-knowledge-bases-runtime-endpoint-key).

|Informatie|cURL-configuratie|Doel|
|--|--|--|
|QnA Maker-bronnaam|URL|gebruikt om URL te construeren|
|QnA Maker-resourcesleutel|`-h`param `Ocp-Apim-Subscription-Key` voor koptekst|Verifiëren naar QnA Maker-service|
|Kennisbank-id|URL-route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

De cURL-opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resourcenaam, resourcesleutel en knowledge base-id.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

De responsstatus is 204 zonder resultaat. Gebruik `-v` de parameter commandline om de uitvoer van verbose voor de cURL-opdracht te bekijken. Dit omvat de HTTP-status.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Download de runtime-eindpuntsleutel van de gepubliceerde knowledgebase

Voordat u de kennisbank opvraagt, moet u:
* Knowledge base publiceren
* De eindpunttoets runtime oppakken

Met deze taak wordt de eindpuntsleutel runtime uitgevoerd. Het publiceren van de kennisbank is een [aparte taak.](#publish-knowledge-base)

De runtime endpoint-toets is dezelfde sleutel voor alle kennisbanken met behulp van de QnA Maker-bron.

|Informatie|cURL-configuratie|Doel|
|--|--|--|
|QnA Maker-bronnaam|URL|gebruikt om URL te construeren|
|QnA Maker-resourcesleutel|`-h`param `Ocp-Apim-Subscription-Key` voor koptekst|Verifiëren naar QnA Maker-service|

De cURL-opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resourcenaam, resourcesleutel.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


Het cURL-antwoord bevat de eindpunttoetsen voor runtime. Gebruik slechts een van de sleutels bij het opvragen om een antwoord te krijgen van de kennisbank.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Vraag naar antwoord van gepubliceerde kennisbank

Het krijgen van een antwoord van de kennis wordt gedaan vanuit een aparte runtime dan het beheren van de kennisbank. Omdat het een aparte runtime is, moet u zich verifiëren met een runtime-sleutel.

|Informatie|cURL-configuratie|Doel|
|--|--|--|
|QnA Maker-bronnaam|URL|gebruikt om URL te construeren|
|Runtime-toets QnA Maker|`-h`param `Authorization` voor koptekst|De sleutel maakt deel uit van `Endpointkey `een tekenreeks die het woord bevat. Verifiëren naar QnA Maker-service|
|Kennisbank-id|URL-route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON beschrijft query|`-d`Param|[Hoofdparameters](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) en [voorbeelden](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) van JSON aanvragen|
|Grootte van de JSON in bytes|`-h`param `Content-Size` voor koptekst||

De cURL-opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resourcenaam, resourcesleutel en knowledge base-id.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Een succesvol antwoord bevat het bovenste antwoord, samen met andere informatie die een clienttoepassing, zoals een chatbot, nodig heeft om een antwoord aan de gebruiker weer te geven.

## <a name="delete-knowledge-base"></a>Kennisbank verwijderen

Wanneer u klaar bent met de kennisbank, verwijder deze dan.

|Informatie|cURL-configuratie|Doel|
|--|--|--|
|QnA Maker-bronnaam|URL|gebruikt om URL te construeren|
|QnA Maker-resourcesleutel|`-h`param `Ocp-Apim-Subscription-Key` voor koptekst|Verifiëren naar QnA Maker-service|
|Kennisbank-id|URL-route|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

De cURL-opdracht wordt uitgevoerd vanuit een BASH-shell. Bewerk deze opdracht met uw eigen resourcenaam, resourcesleutel en knowledge base-id.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

De responsstatus is 204 zonder resultaat. Gebruik `-v` de parameter commandline om de uitvoer van verbose voor de cURL-opdracht te bekijken. Dit omvat de HTTP-status.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Ontwerpen](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Referentiedocumentatie
* [Runtime](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Referentiedocumentatie
* [Voorbeeld van BASH-scripts met behulp van cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslaginformatie over REST-API voor QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
