---
title: 'Quickstart: QnA Maker-client bibliotheek voor .NET'
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met de QnA Maker-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: 1724e2d81f1a18da5a200637cd25636166d5cb37
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70206751"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Quickstart: QnA Maker-client bibliotheek voor .NET

Ga aan de slag met de QnA Maker-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen. 

Gebruik de QnA Maker-client bibliotheek voor .NET voor het volgende:

* Een kennisdatabase maken 
* Een kennis database beheren
* Een kennisdatabase publiceren
* Een antwoord genereren op basis van de Knowledge Base

[](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [NuGet-](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  voor[ beeldenC# ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp) ([Source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | package) voor referentie documentatie

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-qna-maker-azure-resource"></a>Een QnA Maker Azure-resource maken

Azure-Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een resource voor QnA Maker met behulp van de [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine. 

Nadat u een sleutel van uw resource hebt opgehaald, [maakt u een omgevings variabele](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor `QNAMAKER_SUBSCRIPTION_KEY`de sleutel met de naam.

### <a name="create-a-new-c-application"></a>Een nieuwe C# toepassing maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE. 

Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht DotNet `new` om een nieuwe console-app met de naam `qna-maker-quickstart`te maken. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron `Program.cs`bestand:. 

```console
dotnet new console -n qna-maker-quickstart
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>De SDK installeren

Installeer de QnA Maker-client bibliotheek voor .NET in de toepassingsmap met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Als u de Visual Studio IDE gebruikt, is de client bibliotheek beschikbaar als een downloadbaar NuGet-pakket.


## <a name="object-model"></a>Object model

De QnA Maker-client is een [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) -object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw sleutel bevat.

Nadat de client is gemaakt, gebruikt u de [Knowledge Base](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) -eigenschap uw kennis database maken, beheren en publiceren. 

Uw Knowledge Base beheren door een JSON-object te verzenden. Voor directe bewerkingen retourneert een methode meestal een JSON-object dat de status aangeeft. Voor langlopende bewerkingen is het antwoord de bewerkings-ID. Roep de [client aan. Methode Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) met de bewerkings-id om de [status van de aanvraag](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)te bepalen. 

 
## <a name="code-examples"></a>Code voorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de QnA Maker-client bibliotheek voor .NET:

* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisdatabase bijwerken](#update-a-knowledge-base)
* [Een Knowledge base downloaden](#download-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een Knowledge Base verwijderen](#delete-a-knowledge-base)
* [Status van een bewerking ophalen](#get-status-of-an-operation)
* [Een antwoord genereren op basis van de Knowledge Base](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het **Program.cs** -bestand in de map van het project in uw voorkeurs editor of IDE. Vervang de bestaande `using` code door de volgende `using` instructies:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De client verifiëren voor het schrijven van de Knowledge Base

Maak in de methode **Main** een variabele voor de Azure-sleutel van uw resource die wordt opgehaald uit een `QNAMAKER_SUBSCRIPTION_KEY`omgevings variabele met de naam. Als u de omgevings variabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die deze uitvoert, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

Maak vervolgens een [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) -object met uw sleutel en gebruik dit met uw eind punt om een [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) -object te maken.

Als uw sleutel zich niet in de `westus` regio bevindt, zoals in deze voorbeeld code wordt weer gegeven, wijzigt u de locatie voor de **eindpunt** variabele. Deze locatie is te vinden op de overzichts pagina voor uw QnA Maker-resource in de Azure Portal.

[!code-csharp[Authorization to resource key](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Authorization)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime verifiëren voor het genereren van een antwoord

Maak in de methode **Main** een variabele voor de Azure-sleutel van uw resource die wordt opgehaald uit een `QNAMAKER_ENDPOINT_HOSTNAME` omgevings variabele met de naam en. `QNAMAKER_ENDPOINT_KEY` Wanneer u uw Knowledge Base publiceert, worden deze waarden geretourneerd. Nadat u hebt gepubliceerd, kunt u deze instellingen vinden op de pagina **instellingen** van de QnA Maker Portal. 

Maak een [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) om de Knowledge Base te doorzoeken om een antwoord of Train van actief leren te genereren.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en antwoord paren voor het [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) -object uit drie bronnen opgeslagen:

* Gebruik het [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) -object voor **redactionele inhoud**.
* Voor **bestanden**gebruikt u het [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) -object. 
* Gebruik voor **url's**een lijst met teken reeksen.

Roep de [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) -methode aan en geef vervolgens de geretourneerde bewerkings-id door aan de methode [MonitorOperation](#get-status-of-an-operation) om de status te controleren. 

De laatste regel van de volgende code retourneert de Knowledge Base-ID uit het antwoord van MonitorOoperation. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door door te geven in de Knowledge Base-ID en een [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) met DTO-objecten [toevoegen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [bijwerken](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)en [verwijderen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) voor de [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) -methode. Gebruik de methode [MonitorOperation](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

## <a name="download-a-knowledge-base"></a>Een Knowledge base downloaden

Gebruik de methode [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) om de data base te downloaden als een lijst met [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Dit is _niet_ gelijk aan de export van de QnA Maker portal vanaf de pagina **instellingen** omdat het resultaat van deze methode geen TSV-bestand is.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de methode [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) . Dit maakt het huidige opgeslagen en getrainde model, waarnaar wordt verwezen door de Knowledge Base-ID en publiceert die op een eind punt. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren op basis van de Knowledge Base

Genereer een antwoord op basis van een gepubliceerde kennis database met behulp van de [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). Methode [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) . Deze methode accepteert de Knowledge Base-ID en de [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Krijg toegang tot extra eigenschappen van de QueryDTO, zoals de [boven](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) -en- [context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) , die u kunt gebruiken in uw chat-bot. 

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de methode [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) met een para meter van de Knowledge Base-id. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, kunnen voldoende tijd in beslag nemen, in plaats van te wachten tot het proces is voltooid, een [bewerking](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) wordt geretourneerd. Gebruik de [bewerkings-id](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) van de bewerking om te pollen (met pogings logica) om de status van de oorspronkelijke methode te bepalen. 

De _lus_ en _taak. delay_ in het volgende code blok worden gebruikt voor het simuleren van de logica voor opnieuw proberen. Deze moeten worden vervangen door uw eigen logica voor nieuwe pogingen. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de `run` opdracht DotNet in de map van de toepassing.

```console
dotnet run
```

De [bron code voor deze Quick](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) start is beschikbaar in de C# GitHub-opslag plaats QnA Maker-voor beelden.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resource groep verwijderen. Als u de resource groep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Zelfstudie: Een KB maken en beantwoorden](../tutorials/create-publish-query-in-portal.md)

* [Wat is de QnA Maker-API?](../Overview/overview.md)
* [Een kennisdatabase bewerken](../how-to/edit-knowledge-base.md)
* [Gebruiks analyse ophalen](../how-to/get-analytics-knowledge-base.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs).
