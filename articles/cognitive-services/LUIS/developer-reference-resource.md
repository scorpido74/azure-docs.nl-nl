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
ms.date: 09/18/2019
ms.author: diberry
ms.openlocfilehash: 97684a4668c1d495fa690674f7bd94e064737bd4
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273953"
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

### <a name="rest-apis"></a>REST-API’s

De API'S voor ontwerpen en voor spellingen van eind punten zijn beschikbaar via REST-Api's:

* [Naslag documentatie](https://go.microsoft.com/fwlink/?linkid=2092087) voor het ontwerpen
* [Naslag documentatie](https://go.microsoft.com/fwlink/?linkid=2092356) voor de runtime van de voor spelling

### <a name="language-based-sdks"></a>Op taal gebaseerde Sdk's

|Taal |Referentiedocumentatie|Pakket|Voorbeelden|Snelstartgidsen|
|--|--|--|--|--|
|C#|[Ontwerpen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Voorspelling](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet ontwerpen](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet-voor spelling](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Voor beelden van .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[App maken en beheren](sdk-csharp-quickstart-authoring-app.md)<br>[Query Voorspellings eindpunt](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Aan de slag|[Ontwerpen en voors pellen](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Ontwerpen met behulp van REST](luis-get-started-go-add-utterance.md)<br>[Voor spelling met REST](luis-get-started-go-get-intent.md)|
|Java|[Ontwerpen en voors pellen](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven ontwerpen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-voor spelling](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Ontwerpen](luis-get-started-java-add-utterance.md)<br>[Voorspelling](luis-get-started-java-get-intent.md)
|Node.js|[Ontwerpen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Voorspelling](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM ontwerpen](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM-voor spelling](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Voorspelling](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Ontwerpen met behulp van REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[Voor spelling met REST](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Ontwerpen en voors pellen](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/?view=azure-python)|[Gooien](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Ontwerpen](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Ontwerpen](sdk-python-quickstart-authoring-app.md)<br>[Voor spelling met REST](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>Andere hulpprogram ma's en Sdk's

Het bot-Framework is beschikbaar als [een SDK](https://github.com/Microsoft/botframework) in verschillende talen en als een service met behulp van [Azure bot service](https://dev.botframework.com/). 

Bot Framework biedt [verschillende hulpprogram ma's](https://github.com/microsoft/botbuilder-tools) voor het language Understanding, waaronder:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -Luis language-overeenstemmings modellen bouwen met behulp van bestanden voor korting
* [Luis cli](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) : uw Luis.ai-toepassingen maken en beheren
* [Verzen ding](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch): bovenliggende en onderliggende apps beheren
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) : automatisch een back- C#up van/typescript-klassen voor uw Luis-intenties en entiteiten genereren.
* [Bot-emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) : een bureaublad toepassing die bot-ontwikkel aars in staat stelt om bots te testen en op te sporen met behulp van de bot Framework SDK


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de veelvoorkomende [HTTP-fout codes](luis-reference-response-codes.md)