---
title: 'Snelstartgids: QnA Maker-client bibliotheek voor Node.js'
description: In deze Quick start ziet u hoe u aan de slag gaat met de QnA Maker-client bibliotheek voor Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 7b3c5e69e820951896cb00b82295dc07ba698c94
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114518"
---
Gebruik de QnA Maker-client bibliotheek voor Node.js voor het volgende:

* Een Knowledge Base maken
* Een Knowledge Base bijwerken
* Een Knowledge Base publiceren
* De Voorspellings runtime-eindpunt sleutel ophalen
* Wachten op langlopende taken
* Een Knowledge base downloaden
* Antwoord ophalen
* Knowledge Base verwijderen

[Referentie documentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest)  |  [Bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  van bibliotheek [Pakket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) -voor beelden

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org).
* Wanneer u uw Azure-abonnement hebt, maakt u een [QnA Maker resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in de Azure Portal om uw ontwerp sleutel en resource op te halen. Nadat de app is geïmplementeerd, selecteert **u Ga naar resource**.
    * U hebt de sleutel en de resource naam nodig van de resource die u maakt om de toepassing te verbinden met de QnA Maker-API. U plakt uw sleutel en de resource naam later in de Quick Start.
    * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor uw app en navigeer ernaar.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Voer de `npm init -y` opdracht uit om een knooppunt toepassing met een `package.json` bestand te maken.

```console
npm init -y
```

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Installeer de volgende NPM-pakketten:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

Het bestand van uw app `package.json` wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam index.js en importeer de volgende bibliotheken:

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

Maak een variabele voor de Azure-sleutel en resource naam van uw resource. Voor de Url's voor schrijven en voor spellingen wordt de resource naam gebruikt als subdomein.

> [!IMPORTANT]
> Ga naar de Azure Portal en zoek de sleutel en het eind punt voor de QnA Maker resource die u hebt gemaakt in de vereisten. Ze bevinden zich op de pagina **sleutel en eind punt** van de resource, onder **resource beheer**.
> U hebt de volledige sleutel nodig om uw Knowledge Base te maken. U hebt alleen de resource naam van het eind punt nodig. De indeling is `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Vergeet niet om de sleutel uit uw code te verwijderen wanneer u klaar bent en deze nooit openbaar te plaatsen. Overweeg voor productie een veilige manier om uw referenties op te slaan en te gebruiken. [Azure Key kluis](https://docs.microsoft.com/azure/key-vault/key-vault-overview) biedt bijvoorbeeld een beveiligde sleutel opslag.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

## <a name="object-models"></a>Object modellen

QnA Maker gebruikt twee verschillende object modellen:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object voor het maken, beheren, publiceren en downloaden van de Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is het object voor het opvragen van de Knowledge Base met de GENERATEANSWER-API en voor het verzenden van nieuwe voorgestelde vragen met behulp van de Train API (als onderdeel van het [actieve Learning](../concepts/active-learning-suggestions.md)).


### <a name="qnamakerclient-object-model"></a>QnAMakerClient-object model

De ontwerp QnA Maker-client is een [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) -object dat wordt geverifieerd bij Azure met behulp van uw referenties, die uw sleutel bevatten.

Nadat de client is gemaakt, gebruikt u de data [Base](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) om uw Knowledge Base te maken, beheren en publiceren.

Uw Knowledge Base beheren door een JSON-object te verzenden. Voor directe bewerkingen retourneert een methode meestal een JSON-object dat de status aangeeft. Voor langlopende bewerkingen is het antwoord de bewerkings-ID. Roep de methode [client. Operations. getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) aan met de bewerkings-id om de [status van de aanvraag](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest)te bepalen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-object model

De Voorspellings QnA Maker-client is een QnAMakerRuntimeClient-object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw Voorspellings runtime sleutel bevat die wordt geretourneerd door de ontwerp-client aanroep, [client. EndpointKeys. getKeys](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) nadat de Knowledge Base is gepubliceerd.


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

Exemplaar een client met uw eind punt en sleutel. Maak een ServiceClientCredentials-object met uw sleutel en gebruik het met uw eind punt om een [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) -object te maken.

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

In een Knowledge Base worden vraag-en antwoord paren voor het [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) -object uit drie bronnen opgeslagen:

* Gebruik het [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) -object voor **redactionele inhoud**.
    * Als u meta gegevens en opvolgings prompts wilt gebruiken, gebruikt u de redactionele context, omdat deze gegevens worden toegevoegd op het afzonderlijke QnA-paar niveau.
* Voor **bestanden**gebruikt u het [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) -object. De FileDTO bevat de bestands naam en de open bare URL om het bestand te bereiken.
* Gebruik voor **url's**een lijst met teken reeksen die openbaar beschik bare url's vertegenwoordigen.

De stap voor het maken bevat ook eigenschappen voor de Knowledge Base:
* `defaultAnswerUsedForExtraction`-Wat wordt geretourneerd als er geen antwoord wordt gevonden
* `enableHierarchicalExtraction`-Er worden automatisch vraag relaties tussen geëxtraheerde QnA-paren gemaakt
* `language`-bij het maken van de eerste Knowledge Base van een resource stelt u de taal in voor gebruik in de Azure Search-index.

Roep de [Create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) -methode aan met de Knowledge Base-informatie. De Knowledge Base-informatie is in principe een JSON-object.

Wanneer de methode Create retourneert, geeft u de geretourneerde bewerkings-ID door aan de methode [wait_for_operation](#get-status-of-an-operation) om de status te controleren. De wait_for_operation-methode retourneert wanneer de bewerking is voltooid. Parser de `resourceLocation` header waarde van de geretourneerde bewerking om de nieuwe Knowledge Base-id op te halen.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod&highlight=39,46)]

Zorg ervoor dat u de [`wait_for_operation`](#get-status-of-an-operation) functie include gebruikt, waarnaar wordt verwezen in de bovenstaande code om een Knowledge Base te kunnen maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door door te geven in de Knowledge Base-ID en een [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) met DTO-objecten [toevoegen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [bijwerken](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)en [verwijderen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) voor de [Update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) methode. De DTOs zijn ook in principe JSON-objecten. Gebruik de methode [wait_for_operation](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod&highlight=74,81)]

Zorg ervoor dat u de [`wait_for_operation`](#get-status-of-an-operation) functie include gebruikt, waarnaar wordt verwezen in de bovenstaande code om een Knowledge Base te kunnen bijwerken.

## <a name="download-a-knowledge-base"></a>Een Knowledge base downloaden

Gebruik de [Download](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-) methode om de data base te downloaden als een lijst met [QnADocumentsDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest). Dit is _niet_ gelijk aan de export van de QnA Maker portal vanaf de pagina **instellingen** omdat het resultaat van deze methode geen TSV-bestand is.

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB&highlight=2)]


## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de methode [Publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) . Dit maakt het huidige opgeslagen en getrainde model, waarnaar wordt verwezen door de Knowledge Base-ID en publiceert die op een eind punt. Controleer de HTTP-antwoord code om te valideren dat de publicatie is voltooid.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB&highlight=3)]


## <a name="get-query-runtime-key"></a>Runtime sleutel voor query ophalen

Zodra een Knowledge Base is gepubliceerd, hebt u de runtime sleutel voor query's nodig om de runtime op te vragen. Dit is niet dezelfde sleutel die wordt gebruikt om het oorspronkelijke client object te maken.

Gebruik de methode [EndpointKeys. getKeys](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest) om de [EndpointKeysDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest) -klasse op te halen.

Gebruik een van de sleutel eigenschappen die zijn geretourneerd in het object voor het opvragen van de Knowledge Base.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey&highlight=4)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime verifiëren voor het genereren van een antwoord

Maak een QnAMakerRuntimeClient om de Knowledge Base te doorzoeken om een antwoord of Train van actief leren te genereren.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Gebruik de QnAMakerRuntimeClient om een antwoord te krijgen van de kennis of om nieuwe voorgestelde vragen te verzenden naar de Knowledge Base voor het [actief leren](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren op basis van de Knowledge Base

Genereer een antwoord op basis van een gepubliceerde Knowledge Base met behulp van de methode RuntimeClient. runtime. generateAnswer. Deze methode accepteert de Knowledge Base-ID en de QueryDTO. Krijg toegang tot extra eigenschappen van de QueryDTO, zoals de boven-en-context, die u kunt gebruiken in uw chat-bot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer&highlight=3)]

Dit is een eenvoudig voor beeld van het uitvoeren van een query op de Knowledge Base. Zie [andere query voorbeelden voor meer](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)informatie over geavanceerde scenario's voor query's.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de methode [Delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) met een para meter van de Knowledge Base-id.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, kunnen voldoende tijd in beslag nemen, in plaats van te wachten tot het proces is voltooid, een [bewerking](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) wordt geretourneerd. Gebruik de [bewerkings-id](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) van de bewerking om te pollen (met pogings logica) om de status van de oorspronkelijke methode te bepalen.

De _delayTimer_ -aanroep in het volgende code blok wordt gebruikt om de pogings logica te simuleren. Vervang dit door uw eigen logica voor opnieuw proberen.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation&highlight=8)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de `node index.js` opdracht uit de toepassingsmap.

```console
node index.js
```

De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).