---
title: Bronnen voor ontwikkel aars-Language Understanding
titleSuffix: Azure Cognitive Services
description: Ontwikkel aars hebben zowel REST-Api's als Sdk's voor Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: diberry
ms.openlocfilehash: 2f351ac570080c83e78697bbca94340bb96cbcf7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716131"
---
# <a name="developer-resources-for-language-understanding"></a>Bronnen voor ontwikkel aars voor Language Understanding

Ontwikkel aars kunnen zowel REST-Api's als Sdk's voor Language Understanding gebruiken.

## <a name="azure-resource-management"></a>Azure-resource beheer

Gebruik de Azure Cognitive Services Management-laag voor het maken, bewerken, weer geven en verwijderen van de Language Understanding of de cognitieve service resource.

Zoek naar de referentie documentatie op basis van het hulp programma:

* [Azure-CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [PowerShell voor Azure RM](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Aanvragen voor ontwerpen Language Understanding en voor spellingen

De Language Understanding-service wordt geopend vanuit een Azure-resource die u moet maken. Er zijn twee resources: ontwerp-en Voorspellings eindpunt resources. Met beide resources kunt u uw LUIS-resources beheren.

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

### <a name="rest-apis"></a>REST API's

De API'S voor ontwerpen en voor spellingen van eind punten zijn beschikbaar via REST-Api's:

|Type|Versie|
|--|--|
|Ontwerpen|[Offload](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Preview v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|voorspelling|[Offload](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>Op taal gebaseerde Sdk's

|Taal |Referentiedocumentatie|Pakket|Voorbeelden|Snelstartgidsen|
|--|--|--|--|--|
|C#|[Ontwerpen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Voorspelling](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet ontwerpen](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet-voor spelling](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Voor beelden van .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[App maken en beheren](sdk-authoring.md?pivots=programming-language-csharp)<br>[Query Voorspellings eindpunt](sdk-query-prediction-endpoint.md)|
|Go|[Ontwerpen en voors pellen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Ontwerpen en voors pellen met REST](luis-get-started-get-intent-from-rest.md)|
|Java|[Ontwerpen en voors pellen](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven ontwerpen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-voor spelling](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Ontwerpen en voors pellen](luis-get-started-get-intent-from-rest.md)
|Node.js|[Ontwerpen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Voorspelling](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM ontwerpen](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM-voor spelling](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Ontwerpen en voors pellen met REST](luis-get-started-get-intent-from-rest.md)|
|Python|[Ontwerpen en voors pellen](sdk-authoring.md?pivots=programming-language-python)|[Gooien](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Ontwerpen](sdk-authoring.md?pivots=programming-language-python)<br>[Voor spelling met REST](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containers

Language Understanding (LUIS) biedt een [container](luis-container-howto.md) om on-premises en opgenomen versies van uw app te bieden.

### <a name="export-and-import-formats"></a>Indelingen exporteren en importeren

Language Understanding biedt de mogelijkheid om uw app en de bijbehorende modellen te beheren in een JSON-indeling, de `.LU`-indeling ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) en een gecomprimeerd pakket voor de Language Understanding-container.

Het importeren en exporteren van deze indelingen is beschikbaar via de Api's en vanuit de LUIS-Portal. De portal biedt importeren en exporteren als onderdeel van de lijst met apps en versies.

## <a name="other-tools-and-sdks"></a>Andere hulpprogram ma's en Sdk's

Het bot-Framework is beschikbaar als [een SDK](https://github.com/Microsoft/botframework) in verschillende talen en als een service met behulp van [Azure bot service](https://dev.botframework.com/).

Bot Framework biedt [verschillende hulpprogram ma's](https://github.com/microsoft/botbuilder-tools) voor het language Understanding, waaronder:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -Luis language-overeenstemmings modellen bouwen met behulp van bestanden voor korting
* [Luis cli](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) : uw Luis.ai-toepassingen maken en beheren
* [Verzen ding](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch): bovenliggende en onderliggende apps beheren
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) : automatisch een back- C#up van/typescript-klassen voor uw Luis-intenties en entiteiten genereren.
* [Bot-emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) : een bureaublad toepassing die bot-ontwikkel aars in staat stelt om bots te testen en op te sporen met behulp van de bot Framework SDK


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de veelvoorkomende [HTTP-fout codes](luis-reference-response-codes.md)
* [Referentie documentatie](https://docs.microsoft.com/azure/index#pivot=sdkstools) voor alle Api's en sdk's
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) en [Azure bot service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Cognitieve containers](../cognitive-services-container-support.md)