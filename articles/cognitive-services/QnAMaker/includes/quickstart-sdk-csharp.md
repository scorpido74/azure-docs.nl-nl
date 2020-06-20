---
title: 'Snelstartgids: QnA Maker-client bibliotheek voor .NET'
description: In deze Quick start ziet u hoe u aan de slag gaat met de QnA Maker-client bibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeld code voor basis taken uit te proberen.  Met QnA Maker kunt u een vraag- en antwoordservice maken op basis van uw semi-gestructureerde inhoud zoals FAQ-documenten, URL's en producthandleidingen.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 06aaf8861a263711ab3d01e6355bc161538a3311
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114520"
---
Gebruik de QnA Maker-client bibliotheek voor .NET voor het volgende:

 * Een Knowledge Base maken
 * Een Knowledge Base bijwerken
 * Een Knowledge Base publiceren
 * De Voorspellings runtime-eindpunt sleutel ophalen
 * Wachten op langlopende taken
 * Een Knowledge base downloaden
 * Antwoord ophalen
 * Een Knowledge Base verwijderen

[Referentie documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet)  |  [Bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  van bibliotheek [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart) -voor beelden

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) of de huidige versie van [.net core](https://dotnet.microsoft.com/download/dotnet-core).
* Wanneer u uw Azure-abonnement hebt, maakt u een [QnA Maker resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in de Azure Portal om uw ontwerp sleutel en resource naam op te halen. Nadat de app is geïmplementeerd, selecteert **u Ga naar resource**.
    * U hebt de sleutel en de resource naam nodig van de resource die u maakt om de toepassing te verbinden met de QnA Maker-API. U plakt uw sleutel en de resource naam later in de Quick Start.
    * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Maak met behulp van Visual Studio een .NET core-toepassing en installeer de client bibliotheek door met de rechter muisknop op de oplossing in de **Solution Explorer** te klikken en **NuGet-pakketten beheren**te selecteren. In package manager, dat wordt geopend, selecteert u **Browse** **Prerelease**en zoekt u naar `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` . Selecteer versie `2.0.0-preview.1` en vervolgens **installeren**.

#### <a name="cli"></a>[CLI](#tab/cli)

In een console venster (zoals cmd, Power shell of bash) gebruikt `dotnet new` u de opdracht om een nieuwe console-app met de naam te maken `qna-maker-quickstart` . Met deze opdracht maakt u een eenvoudig ' Hallo wereld ' C#-project met één bron bestand: *Program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

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

Installeer de QnA Maker-client bibliotheek voor .NET in de toepassingsmap met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.0-preview.1
```


---

> [!TIP]
> Wilt u het volledige Quick Start-code bestand tegelijk weer geven? U kunt het vinden op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), dat de code voorbeelden in deze Snelstartgids bevat.

Open het *Program.cs* -bestand in de projectmap en voeg de volgende `using` instructies toe:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

Voeg in de methode van de toepassing `Main` variabelen en code toe, zoals wordt weer gegeven in de volgende sectie, voor het gebruik van de algemene taken in deze Quick Start.

> [!IMPORTANT]
> Ga naar de Azure Portal en zoek de sleutel en het eind punt voor de QnA Maker resource die u hebt gemaakt in de vereisten. Ze bevinden zich op de pagina **sleutel en eind punt** van de resource, onder **resource beheer**.
> U hebt de volledige sleutel nodig om uw Knowledge Base te maken. U hebt alleen de resource naam van het eind punt nodig. De indeling is `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Vergeet niet om de sleutel uit uw code te verwijderen wanneer u klaar bent en deze nooit openbaar te plaatsen. Overweeg voor productie een veilige manier om uw referenties op te slaan en te gebruiken. [Azure Key kluis](https://docs.microsoft.com/azure/key-vault/key-vault-overview) biedt bijvoorbeeld een beveiligde sleutel opslag.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


## <a name="object-models"></a>Object modellen

QnA Maker gebruikt twee verschillende object modellen:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object voor het maken, beheren, publiceren en downloaden van de Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is het object voor het opvragen van de Knowledge Base met de GENERATEANSWER-API en voor het verzenden van nieuwe voorgestelde vragen met behulp van de Train API (als onderdeel van het [actieve Learning](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-object model

De ontwerp QnA Maker-client is een [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) -object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw sleutel bevat.

Nadat de client is gemaakt, gebruikt u de [Knowledge Base](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) -eigenschap om uw Knowledge Base te maken, beheren en publiceren.

Uw Knowledge Base beheren door een JSON-object te verzenden. Voor directe bewerkingen retourneert een methode meestal een JSON-object dat de status aangeeft. Voor langlopende bewerkingen is het antwoord de bewerkings-ID. Roep de [client aan. Methode Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) met de bewerkings-id om de [status van de aanvraag](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)te bepalen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-object model

De Voorspellings QnA Maker-client is een [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) -object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw Voorspellings runtime sleutel bevat, geretourneerd door de ontwerp-client aanroep, `client.EndpointKeys.GetKeys` nadat de Knowledge Base is gepubliceerd.

Gebruik de methode [GenerateAnswer](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswer?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_RuntimeExtensions_GenerateAnswer_Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_IRuntime_System_String_Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_) om een antwoord te krijgen van de query runtime.

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de QnA Maker-client bibliotheek voor .NET:

* [De ontwerp-client verifiëren](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisdatabase bijwerken](#update-a-knowledge-base)
* [Een Knowledge base downloaden](#download-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [Runtime sleutel voor query ophalen](#get-query-runtime-key)
* [Status van een bewerking ophalen](#get-status-of-an-operation)
* [De query runtime-client verifiëren](#authenticate-the-runtime-for-generating-an-answer)
* [Een antwoord genereren op basis van de Knowledge Base](#generate-an-answer-from-the-knowledge-base)



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De client verifiëren voor het schrijven van de Knowledge Base

Maak een exemplaar van een client object met uw sleutel en gebruik dit met uw bron om het eind punt te maken om een [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) met uw eind punt en sleutel te creëren. Maak een [ServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet) -object.

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en antwoord paren voor het [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) -object uit drie bronnen opgeslagen:

* Gebruik het [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) -object voor **redactionele inhoud**.
    * Als u meta gegevens en opvolgings prompts wilt gebruiken, gebruikt u de redactionele context, omdat deze gegevens worden toegevoegd op het afzonderlijke QnA-paar niveau.
* Voor **bestanden**gebruikt u het [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) -object. De FileDTO bevat de bestands naam en de open bare URL om het bestand te bereiken.
* Gebruik voor **url's**een lijst met teken reeksen die openbaar beschik bare url's vertegenwoordigen.

De stap voor het maken bevat ook eigenschappen voor de Knowledge Base:
* `defaultAnswerUsedForExtraction`-Wat wordt geretourneerd als er geen antwoord wordt gevonden
* `enableHierarchicalExtraction`-Er worden automatisch vraag relaties tussen geëxtraheerde QnA-paren gemaakt
* `language`-bij het maken van de eerste Knowledge Base van een resource stelt u de taal in voor gebruik in de Azure Search-index.

Roep de [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) -methode aan en geef vervolgens de geretourneerde bewerkings-id door aan de methode [MonitorOperation](#get-status-of-an-operation) om de status te controleren.

De laatste regel van de volgende code retourneert de Knowledge Base-ID uit het antwoord van MonitorOperation.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=31)]

Zorg ervoor dat u de [`MonitorOperation`](#get-status-of-an-operation) functie include gebruikt, waarnaar wordt verwezen in de bovenstaande code om een Knowledge Base te kunnen maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door door te geven in de Knowledge Base-ID en een [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) met DTO-objecten [toevoegen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [bijwerken](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)en [verwijderen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) voor de [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) -methode. Gebruik de methode [MonitorOperation](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Zorg ervoor dat u de [`MonitorOperation`](#get-status-of-an-operation) functie include gebruikt, waarnaar wordt verwezen in de bovenstaande code om een Knowledge Base te kunnen bijwerken.

## <a name="download-a-knowledge-base"></a>Een Knowledge base downloaden

Gebruik de methode [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) om de data base te downloaden als een lijst met [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Dit is _niet_ gelijk aan de export van de QnA Maker portal vanaf de pagina **instellingen** omdat het resultaat van deze methode geen bestand is.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3)]

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de methode [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) . Dit maakt het huidige opgeslagen en getrainde model, waarnaar wordt verwezen door de Knowledge Base-ID en publiceert die op uw eind punt. Dit is een nood zakelijke stap om een query uit te kunnen op uw Knowledge Base.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]



## <a name="get-query-runtime-key"></a>Runtime sleutel voor query ophalen

Zodra een Knowledge Base is gepubliceerd, hebt u de runtime sleutel voor query's nodig om de runtime op te vragen. Dit is niet dezelfde sleutel die wordt gebruikt om het oorspronkelijke client object te maken.

Gebruik de methode [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) om de klasse [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet) op te halen.

Gebruik een van de sleutel eigenschappen die zijn geretourneerd in het object voor het opvragen van de Knowledge Base.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

Er is een runtime sleutel nodig om een query uit te kunnen op uw Knowledge Base.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime verifiëren voor het genereren van een antwoord

Maak een [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) om de Knowledge Base te doorzoeken om een antwoord of Train van actief leren te genereren.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Gebruik het QnAMakerRuntimeClient om het volgende te doen:
* een antwoord verkrijgen van de Knowledge Base
* nieuwe suggesties verzenden naar de Knowledge Base voor [actief leren](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren op basis van de Knowledge Base

Genereer een antwoord op basis van een gepubliceerde Knowledge Base met behulp van de [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). Methode [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) . Deze methode accepteert de Knowledge Base-ID en de [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Krijg toegang tot extra eigenschappen van de QueryDTO, zoals de [boven](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) -en- [context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) , die u kunt gebruiken in uw chat-bot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

Dit is een eenvoudig voor beeld van het uitvoeren van een query op de Knowledge Base. Zie [andere query voorbeelden voor meer](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)informatie over geavanceerde scenario's voor query's.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de data base met behulp van de methode [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) met een para meter van de Knowledge Base-id.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, kunnen voldoende tijd in beslag nemen, in plaats van te wachten tot het proces is voltooid, een [bewerking](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) wordt geretourneerd. Gebruik de [bewerkings-id](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) van de bewerking om te pollen (met pogings logica) om de status van de oorspronkelijke methode te bepalen.

De _lus_ en _taak. delay_ in het volgende code blok worden gebruikt voor het simuleren van de logica voor opnieuw proberen. Deze moeten worden vervangen door uw eigen logica voor nieuwe pogingen.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de `dotnet run` opdracht uit de toepassingsmap.

```dotnetcli
dotnet run
```

De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).
