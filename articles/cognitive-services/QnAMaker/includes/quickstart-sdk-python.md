---
title: 'Snelstartgids: QnA Maker-client bibliotheek voor python'
description: In deze Quick start ziet u hoe u aan de slag gaat met de QnA Maker-client bibliotheek voor python.
ms.topic: include
ms.date: 06/18/2020
ms.openlocfilehash: 1b6990589663655c5b4518d55e42838775889671
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114517"
---
Gebruik de QnA Maker-client bibliotheek voor python voor het volgende:

* Een Knowledge Base maken
* Een Knowledge Base bijwerken
* Een Knowledge Base publiceren
* De Voorspellings runtime-eindpunt sleutel ophalen
* Wachten op langlopende taken
* Een Knowledge base downloaden
* Antwoord ophalen
* Knowledge Base verwijderen

[Referentie documentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python)  |  [Bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  van bibliotheek [Pakket (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/)  |  [Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) -voor beelden

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)
* Wanneer u uw Azure-abonnement hebt, maakt u een [QnA Maker resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) in de Azure Portal om uw ontwerp sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, selecteert **u Ga naar resource**.
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de QnA Maker-API. U plakt uw sleutel en het eind punt in de onderstaande code verderop in de Quick Start.
    * U kunt de gratis prijs categorie ( `F0` ) gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De client bibliotheek installeren

Na de installatie van python kunt u de client bibliotheek installeren met:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

### <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuw python-bestand `quickstart-file.py` met de naam en importeer de volgende bibliotheken.

[!code-python[Dependencies](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Dependencies&highlight=4,5)]

Maak variabelen voor het Azure-eind punt en de sleutel van uw resource.

> [!IMPORTANT]
> Ga naar de Azure Portal en zoek de sleutel en het eind punt voor de QnA Maker resource die u hebt gemaakt in de vereisten. Ze bevinden zich op de pagina **sleutel en eind punt** van de resource, onder **resource beheer**.
> U hebt de volledige sleutel nodig om uw Knowledge Base te maken. U hebt alleen de resource naam van het eind punt nodig. De indeling is `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Vergeet niet om de sleutel uit uw code te verwijderen wanneer u klaar bent en deze nooit openbaar te plaatsen. Overweeg voor productie een veilige manier om uw referenties op te slaan en te gebruiken. [Azure Key kluis](https://docs.microsoft.com/azure/key-vault/key-vault-overview) biedt bijvoorbeeld een beveiligde sleutel opslag.

[!code-python[Resource variables](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=Resourcevariables)]

## <a name="object-models"></a>Object modellen

[QnA Maker](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) Maker gebruikt twee verschillende object modellen:
* **[QnAMakerClient](#qnamakerclient-object-model)** is het object voor het maken, beheren, publiceren en downloaden van de Knowledge Base.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** is het object voor het opvragen van de Knowledge Base met de GENERATEANSWER-API en voor het verzenden van nieuwe voorgestelde vragen met behulp van de Train API (als onderdeel van het [actieve Learning](../concepts/active-learning-suggestions.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-object model

De ontwerp QnA Maker-client is een [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) -object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw sleutel bevat.

Nadat de client is gemaakt, gebruikt u de [Knowledge Base](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python) -eigenschap om uw Knowledge Base te maken, beheren en publiceren.

Uw Knowledge Base beheren door een JSON-object te verzenden. Voor directe bewerkingen retourneert een methode meestal een JSON-object dat de status aangeeft. Voor langlopende bewerkingen is het antwoord de bewerkings-ID. Roep de [bewerkingen. get_details](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.operations(class)?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) methode aan met de bewerkings-id om de [status van de aanvraag](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation(class)?view=azure-python)te bepalen.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-object model

De Voorspellings QnA Maker-client is een [QnAMakerRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.qnamakerruntimeclient?view=azure-python) -object dat wordt geverifieerd bij Azure met behulp van micro soft. rest. ServiceClientCredentials, dat uw Voorspellings runtime sleutel bevat die wordt geretourneerd door de ontwerp-client aanroep, [client. EndpointKeysOperations. get_keys](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) nadat de Knowledge Base is gepubliceerd.

Gebruik de methode [generate_answer](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.operations.runtimeoperations?view=azure-python#generate-answer-kb-id--generate-answer-payload--custom-headers-none--raw-false----operation-config-) om een antwoord te krijgen van de query runtime.


## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>De client verifiëren voor het schrijven van de Knowledge Base

Exemplaar een client met uw eind punt en sleutel. Maak een CognitiveServicesCredentials-object met uw sleutel en gebruik het met uw eind punt om een [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.qnamakerclient?view=azure-python) -object te maken.

[!code-python[Authorization to resource key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

 Gebruik het client object om een [Knowledge Base-bewerkings](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python) object op te halen.

In een Knowledge Base worden vraag-en antwoord paren voor het [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.create_kb_dto?view=azure-python) -object uit drie bronnen opgeslagen:

* Gebruik het [QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adto?view=azure-python) -object voor **redactionele inhoud**.
    * Als u meta gegevens en opvolgings prompts wilt gebruiken, gebruikt u de redactionele context, omdat deze gegevens worden toegevoegd op het afzonderlijke QnA-paar niveau.
* Voor **bestanden**gebruikt u het [FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.file_dto?view=azure-python) -object. De FileDTO bevat de bestands naam en de open bare URL om het bestand te bereiken.
* Gebruik voor **url's**een lijst met teken reeksen die openbaar beschik bare url's vertegenwoordigen.

Roep de [Create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) -methode aan en geef de geretourneerde bewerkings-id door aan de methode [Operations. getDetails](#get-status-of-an-operation) om de status te controleren.

De laatste regel van de volgende code retourneert de Knowledge Base-ID uit het antwoord van MonitorOperation.

[!code-python[Create knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=CreateKBMethod&highlight=36,38)]

Zorg ervoor dat u de [`_monitor_operation`](#get-status-of-an-operation) functie include gebruikt, waarnaar wordt verwezen in de bovenstaande code om een Knowledge Base te kunnen maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U kunt een Knowledge Base bijwerken door door te geven in de Knowledge Base-ID en een [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto?view=azure-python) met DTO-objecten [toevoegen](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_add?view=azure-python), [bijwerken](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_update?view=azure-python)en [verwijderen](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_delete?view=azure-python) voor de [Update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) methode. Gebruik de methode [Operation. getDetail](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

[!code-python[Update a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=UpdateKBMethod&highlight=68,69)]

Zorg ervoor dat u de [`_monitor_operation`](#get-status-of-an-operation) functie include gebruikt, waarnaar wordt verwezen in de bovenstaande code om een Knowledge Base te kunnen bijwerken.

## <a name="download-a-knowledge-base"></a>Een Knowledge base downloaden

Gebruik de [Download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) methode om de data base te downloaden als een lijst met [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qnadocumentsdto?view=azure-python). Dit is _niet_ gelijk aan de export van de QnA Maker portal vanaf de pagina **instellingen** omdat het resultaat van deze methode geen TSV-bestand is.

[!code-python[Download a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de Knowledge Base met behulp van de methode [Publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) . Dit maakt het huidige opgeslagen en getrainde model, waarnaar wordt verwezen door de Knowledge Base-ID en publiceert die op een eind punt.

[!code-python[Publish a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=PublishKB&highlight=2)]

## <a name="get-query-runtime-key"></a>Runtime sleutel voor query ophalen

Zodra een Knowledge Base is gepubliceerd, hebt u de runtime sleutel voor query's nodig om de runtime op te vragen. Dit is niet dezelfde sleutel die wordt gebruikt om het oorspronkelijke client object te maken.

Gebruik de methode [EndpointKeysOperations. get_keys](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.endpointkeysoperations?view=azure-python#get-keys-custom-headers-none--raw-false----operation-config-) om de [EndpointKeysDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.endpointkeysdto?view=azure-python) -klasse op te halen.

Gebruik een van de sleutel eigenschappen die zijn geretourneerd in het object voor het opvragen van de Knowledge Base.

[!code-python[Get query runtime key](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GetQueryEndpointKey&highlight=2)]


## <a name="authenticate-the-runtime-for-generating-an-answer"></a>De runtime verifiëren voor het genereren van een antwoord

Maak een [QnAMakerRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.runtime.qnamakerruntimeclient?view=azure-python) om de Knowledge Base te doorzoeken om een antwoord of Train van actief leren te genereren.

[!code-python[Authenticate the runtime](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=AuthorizationQuery)]

Gebruik de QnAMakerRuntimeClient om een antwoord te krijgen van de kennis of om nieuwe voorgestelde vragen te verzenden naar de Knowledge Base voor het [actief leren](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Een antwoord genereren op basis van de Knowledge Base

Genereer een antwoord op basis van een gepubliceerde Knowledge Base met behulp van de methode RuntimeClient. runtime. generateAnswer. Deze methode accepteert de Knowledge Base-ID en de QueryDTO. Krijg toegang tot extra eigenschappen van de QueryDTO, zoals de boven-en-context, die u kunt gebruiken in uw chat-bot.

[!code-python[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=GenerateAnswer&highlight=5)]

Dit is een eenvoudig voor beeld van het uitvoeren van een query op de Knowledge Base. Zie [andere query voorbeelden voor meer](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)informatie over geavanceerde scenario's voor query's.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de Knowledge Base met behulp van de methode [Delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) met een para meter van de Knowledge Base-id.

[!code-python[Delete a knowledge base](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking ophalen

Sommige methoden, zoals maken en bijwerken, kunnen voldoende tijd in beslag nemen, in plaats van te wachten tot het proces is voltooid, een [bewerking](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation.operation?view=azure-python) wordt geretourneerd. Gebruik de bewerkings-ID van de bewerking om te pollen (met pogings logica) om de status van de oorspronkelijke methode te bepalen.

De _setTimeout_ -aanroep in het volgende code blok wordt gebruikt voor het simuleren van asynchrone code. Vervang dit door de logica voor opnieuw proberen.

[!code-python[Monitor an operation](~/cognitive-services-quickstart-code/python/QnAMaker/sdk/quickstart.py?name=MonitorOperation&highlight=7)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht python in uw Quick Start-bestand.

```console
python quickstart-file.py
```

De bron code voor dit voor beeld is te vinden op [github](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/QnAMaker/sdk/quickstart.py).