---
title: Bronnen voor ontwikkelaars - Taalbegrip
description: SDKs, REST API's, CLI, helpen u bij het ontwikkelen van Language Understanding (LUIS) apps in uw programmeertaal. Beheer uw Azure-resources en LUIS-voorspellingen.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457981"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>BRONNEN voor SDK-, REST- en CLI-ontwikkelaars voor taalbegrip (LUIS)

SDKs, REST API's, CLI, helpen u bij het ontwikkelen van Language Understanding (LUIS) apps in uw programmeertaal. Beheer uw Azure-resources en LUIS-voorspellingen.

## <a name="azure-resource-management"></a>Azure-bronbeheer

Gebruik de laag Azure Cognitive Services Management om de bron Taalbegrip of Cognitieve Service te maken, bewerken, aanbieden en verwijderen.

Zoek referentiedocumentatie op basis van het hulpprogramma:

* [Azure-CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [PowerShell voor Azure RM](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Verzoeken voor het maken van auteuren en voorspellen van talen

De taalinformatieservice is toegankelijk vanuit een Azure-bron die u moet maken. Er zijn twee bronnen:

* Gebruik de **ontwerpbron** voor training om te maken, bewerken, trainen en publiceren.
* Gebruik de **voorspelling** voor runtime om de tekst van de gebruiker te verzenden en een voorspelling te ontvangen.

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

Gebruik [voorbeeldcode van Cognitive Services](https://github.com/Azure-Samples/cognitive-services-quickstart-code) om de meest voorkomende taken te leren en te gebruiken.

### <a name="rest-specifications"></a>REST specificaties

De [LUIS REST-specificaties](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), samen met alle [Azure REST-specificaties,](https://github.com/Azure/azure-rest-api-specs)zijn openbaar beschikbaar op GitHub.

### <a name="rest-apis"></a>REST-API’s

Zowel authoring als prediction endpoint APIS zijn beschikbaar via REST API's:

|Type|Versie|
|--|--|
|Ontwerpen|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[voorbeeld v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Voorspelling|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST Eindpunten

LUIS heeft momenteel 2 soorten eindpunten:

* schrijven over het trainingseindpunt
* queryvoorspelling op het eindpunt runtime.

|Doel|URL|
|--|--|
|Schrijven op trainingseindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 Runtime - alle voorspellingen over runtime-eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 Runtime - versies voorspelling op runtime eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 Runtime - slotvoorspelling op runtime-eindpunt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

In de volgende tabel worden de parameters, aangeduid met krullende accolades, `{}`in de vorige tabel uitgelegd.

|Parameter|Doel|
|--|--|
|`your-resource-name`|Azure-bronnaam|
|`q` of `query`|utterancetekst verzonden vanuit clienttoepassing, zoals chatbot|
|`version`|Naam van 10 tekensversie|
|`slot`| `production` of `staging`|

### <a name="language-based-sdks"></a>Op taal gebaseerde SDK's

|Taal |Referentiedocumentatie|Pakket|Voorbeelden|Snelstartgidsen|
|--|--|--|--|--|
|C#|[Ontwerpen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Voorspelling](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet-auteurmaken](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet voorspelling](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK-monsters](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Een app maken en beheren](sdk-authoring.md?pivots=programming-language-csharp)<br>[Eindpunt queryvoorspelling](sdk-query-prediction-endpoint.md)|
|Aan de slag|[Auteur en voorspelling](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Ontwerpen en voorspellen met REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Auteur en voorspelling](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven authoring](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven voorspelling](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Auteur en voorspelling](luis-get-started-get-intent-from-rest.md)
|Node.js|[Ontwerpen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Voorspelling](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM-auteurmaken](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM voorspelling](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Ontwerpen en voorspellen met REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Auteur en voorspelling](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Ontwerpen](sdk-authoring.md?pivots=programming-language-python)<br>[Voorspelling met REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containers

Language Understanding (LUIS) biedt een [container](luis-container-howto.md) voor on-premises en opgenomen versies van uw app.

### <a name="export-and-import-formats"></a>Indelingen exporteren en importeren

Taalbegrip biedt de mogelijkheid om uw app en de `.LU` bijbehorende modellen te beheren in een JSON-indeling, de ([LUDown)-indeling](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)en een gecomprimeerd pakket voor de container Taalbegrip.

Het importeren en exporteren van deze indelingen is beschikbaar via de API's en vanuit de LUIS-portal. De portal biedt import en export als onderdeel van de lijst Apps en Versies.

## <a name="other-tools-and-sdks"></a>Andere tools en SDK's

Het botframework is beschikbaar als [SDK](https://github.com/Microsoft/botframework) in verschillende talen en als service met [Azure Bot Service.](https://dev.botframework.com/)

Bot framework biedt [verschillende tools](https://github.com/microsoft/botbuilder-tools) om te helpen met taalbegrip, waaronder:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - Luis-taalbegripsmodellen bouwen met afwaarderingsbestanden
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - Uw LUIS.ai toepassingen maken en beheren
* [Verzenden](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)- ouder- en onderliggende apps beheren
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - Automatisch c#/typescriptklassen genereren voor uw LUIS-intents en entiteiten.
* [Bot Framework emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) - een desktop applicatie waarmee bot ontwikkelaars te testen en debug bots gebouwd met behulp van de Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - een geïntegreerde ontwikkeltool voor ontwikkelaars en multidisciplinaire teams om bots en conversatieervaringen te bouwen met het Microsoft Bot Framework
* [microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) - Tools ter ondersteuning van continue integratie en implementatie voor NLU-diensten.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de algemene [HTTP-foutcodes](luis-reference-response-codes.md)
* [Referentiedocumentatie](https://docs.microsoft.com/azure/index) voor alle API's en SDK's
* [Botframework](https://github.com/Microsoft/botbuilder-dotnet) en [Azure Bot-service](https://dev.botframework.com/)
* [LUDown LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Cognitieve containers](../cognitive-services-container-support.md)
