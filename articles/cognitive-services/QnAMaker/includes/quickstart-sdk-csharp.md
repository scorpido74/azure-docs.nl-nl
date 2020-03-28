---
title: 'Snelstart: QnA Maker-clientbibliotheek voor .NET'
description: Deze quickstart laat zien hoe je aan de slag met de QnA Maker-clientbibliotheek voor .NET. Volg deze stappen om het pakket te installeren en probeer de voorbeeldcode voor basistaken uit.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 2911c74226c3b682b75e8d10b0b4b7617a48ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946339"
---
Gebruik de qnA Maker-clientbibliotheek voor .NET om:

* Een kennisdatabase maken
* Een kennisbank beheren
* Een kennisdatabase publiceren
* Een antwoord genereren vanuit de kennisbank

[Referentiedocumentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# Voorbeelden](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Instellen

### <a name="create-a-qna-maker-azure-resource"></a>Een Azure-bron van QnA Maker maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor QnA Maker met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine.

Nadat u een sleutel en eindpunt voor uw resource hebt `QNAMAKER_SUBSCRIPTION_KEY`opgemaakt, maakt u [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de sleutel met de naam . De naam van de resource wordt gebruikt als onderdeel van de URL van het eindpunt.

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw voorkeurseditor of IDE.

Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app met de naam `qna-maker-quickstart`te maken. Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

```dotnetcli
dotnet build
```

De buildoutput mag geen waarschuwingen of fouten bevatten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>De SDK installeren

Installeer in de toepassingsmap de qnA Maker-clientbibliotheek voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Als u de Visual Studio IDE gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.


## <a name="object-model"></a>Objectmodel

De QnA Maker-client is een [QnAMakerClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) dat zich verifieert naar Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Zodra de client is gemaakt, gebruikt u de eigenschap [Knowledge base](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) om uw kennisbank te maken, te beheren en te publiceren.

Beheer uw kennisbank door een JSON-object te verzenden. Voor onmiddellijke bewerkingen retourneert een methode meestal een JSON-object dat de status aangeeft. Voor langlopende bewerkingen is het antwoord de bewerkings-ID. Bel de [klant. Operations.GetDetailsAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) met de bewerkings-id om de [status van de aanvraag](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)te bepalen .


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de QnA Maker-clientbibliotheek voor .NET:

* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisdatabase bijwerken](#update-a-knowledge-base)
* [Download een kennisbank](#download-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [Status van een bewerking opdoen](#get-status-of-an-operation)
* [Een antwoord genereren vanuit de kennisbank](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open in de projectmap het **Program.cs** bestand in uw voorkeurseditor of IDE. Vervang de `using` bestaande code `using` door de volgende richtlijnen:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De klant verifiëren voor het schrijven van de kennisbank

Maak in de **hoofdmethode** een variabele voor de Azure-sleutel `QNAMAKER_SUBSCRIPTION_KEY`van uw resource die is opgehaald uit een omgevingsvariabele met de naam . Als u de omgevingsvariabele hebt gemaakt nadat de toepassing is gestart, moet de editor, IDE of shell die wordt uitgevoerd, worden gesloten en opnieuw worden geladen om toegang te krijgen tot de variabele. De methoden worden later gemaakt.

Maak vervolgens een [ApiKeyServiceClientCredentials-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) met uw sleutel en gebruik het met uw eindpunt om een [QnAMakerClient-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) te maken.

|Omgevingsvariabele|Variabele|Voorbeeld|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|De sleutel is een tekenreeks met 32 tekens en is beschikbaar in de Azure-portal, op de QnA Maker-bron, op de quickstart-pagina. Dit is niet hetzelfde als de voorspellingeindpuntsleutel.|
|`QNAMAKER_HOST`|`Endpoint`| Het ontwerpeindpunt van uw ontwerp, in de opmaak van `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, bevat uw **bronnaam**. Dit is niet dezelfde URL die wordt gebruikt om het voorspellingseindpunt op te vragen.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime verifiëren voor het genereren van een antwoord

Maak in de **hoofdmethode** een variabele voor de verificatie van `QNAMAKER_ENDPOINT_HOSTNAME` uw `QNAMAKER_ENDPOINT_KEY`resource die is opgehaald uit een omgevingsvariabelen met de naam en . Wanneer u uw kennisbank publiceert, worden deze waarden geretourneerd. Nadat u hebt gepubliceerd, u deze instellingen vinden op de pagina **Instellingen** van de QnA Maker-portal.

Maak een [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) om de kennisbank op te vragen om een antwoord te genereren of te trainen vanuit actief leren.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

Een kennisbank slaat vraag- en antwoordparen op voor het [Object CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) uit drie bronnen:

* Gebruik **voor redactionele inhoud**het [QnADTO-object.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet)
* Voor **bestanden**gebruikt u het object [FileDTO.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet)
* Voor **URL's**gebruikt u een lijst met tekenreeksen.

Roep de [CreateAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) aan en geef de geretourneerde bewerkings-id door aan de [methode MonitorOperation](#get-status-of-an-operation) om te peilen naar status.

De laatste regel van de volgende code retourneert de knowledge base ID van het antwoord van MonitorOoperation.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

Zorg ervoor dat [`MonitorOperation`](#get-status-of-an-operation) de functie, waarnaar in de bovenstaande code wordt verwezen, wordt opgenomen om met succes een kennisbank te maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U een kennisbank bijwerken door de knowledge base-id en een [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) met [add,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet) [update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)en [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO-objecten door te geven aan de [UpdateAsync-methode.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) Gebruik de [methode MonitorOperation](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Zorg ervoor dat [`MonitorOperation`](#get-status-of-an-operation) de functie, waarnaar in de bovenstaande code wordt verwezen, wordt opgenomen om een kennisbank met succes bij te werken.

## <a name="download-a-knowledge-base"></a>Download een kennisbank

Gebruik de [Methode DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) om de database te downloaden als een lijst van [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Dit is _niet_ gelijk aan de export van de QnA Maker-portal vanaf de pagina **Instellingen,** omdat het resultaat van deze methode geen TSV-bestand is.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de kennisbank met behulp van de [publishasync-methode.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) Dit neemt het huidige opgeslagen en getrainde model, waarnaar wordt verwezen door de knowledge base ID, en publiceert dat op een eindpunt.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren vanuit de kennisbank

Genereer een antwoord vanuit een gepubliceerde kennisbank met behulp van de [RuntimeClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) methode. Deze methode accepteert de knowledge base ID en de [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Toegang tot extra eigenschappen van de QueryDTO, zoals een [top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) en [context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) te gebruiken in uw chat bot.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de kennisbank met behulp van de [DeleteAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) met een parameter van de knowledge base-id.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking opdoen

Sommige methoden, zoals maken en bijwerken, kunnen voldoende tijd in beslag nemen dat in plaats van te wachten tot het proces is voltooid, een [bewerking](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) wordt geretourneerd. Gebruik de [bewerkings-id](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) van de bewerking naar de poll (met logica opnieuw proberen) om de status van de oorspronkelijke methode te bepalen.

De _lus_ en _Taak.Vertraging_ in het volgende codeblok worden gebruikt om logica voor opnieuw proberen te simuleren. Deze moeten worden vervangen door uw eigen logica voor nieuwe try.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing `dotnet run` uit met de opdracht uit uw toepassingsmap.

Alle codefragmenten in dit artikel zijn [beschikbaar](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) en kunnen als één bestand worden uitgevoerd.

```dotnetcli
dotnet run
```

De [broncode voor deze quickstart](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) is beschikbaar in de QnA Maker C# samples GitHub repository.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)