---
ms.openlocfilehash: 03aa3919e1da982bb8a8c235bb598f5b94df1ebf
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986752"
---

Gebruik de QnA Maker-clientbibliotheek voor python om:

* Een kennisdatabase maken
* Een kennisbank beheren
* Een kennisdatabase publiceren

[Referentiedocumentatie](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [(pypi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python-voorbeelden](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instellen

### <a name="create-a-qna-maker-azure-resource"></a>Een Azure-bron van QnA Maker maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor QnA Maker met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine.

Nadat u een sleutel uit uw resource hebt opgehaald, `QNAMAKER_HOST`maakt u [omgevingsvariabelen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) voor de benoemde `QNAMAKER_KEY` resource en . Gebruik de sleutel- en eindpuntwaarden in de Azure-portal.

### <a name="install-the-python-library-for-qna-maker"></a>De python-bibliotheek voor QnA Maker installeren

Na het installeren van Python u de clientbibliotheek installeren met:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>Objectmodel

Maak een [Object CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) met uw sleutel en gebruik het met uw eindpunt om een [QnAMakerClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python) te maken.

Zodra de client is gemaakt, gebruikt u de [knowledge base](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) om uw kennisbank te maken, te beheren en te publiceren.

Voor onmiddellijke bewerkingen retourneert een methode meestal een JSON-object dat de status aangeeft. Voor langlopende bewerkingen is het antwoord de bewerkings-ID. Bel de [klant. Operations.getDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.operations%28class%29?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) methode met de bewerkings-ID om de [status van de aanvraag](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python)te bepalen .


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de QnA Maker-clientbibliotheek voor python:

* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisdatabase bijwerken](#update-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Download een kennisbank](#download-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [Status van een bewerking opdoen](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>Een nieuwe python-toepassing maken

Maak een nieuwe Python-toepassing in uw voorkeurseditor of IDE. Importeer vervolgens de volgende bibliotheken.

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

Maak variabelen voor het Azure-eindpunt en de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

|Omgevingsvariabele|Variabele|Voorbeeld|
|--|--|--|
|`QNAMAKER_KEY`|`subscription_key`|De sleutel is een tekenreeks met 32 tekens en is beschikbaar in de Azure-portal, op de QnA Maker-bron, op de quickstart-pagina. Dit is niet hetzelfde als de voorspellingeindpuntsleutel.|
|`QNAMAKER_HOST`|`host`| Het ontwerpeindpunt van uw ontwerp, in de opmaak van `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, bevat uw **bronnaam**. Dit is niet dezelfde URL die wordt gebruikt om het voorspellingseindpunt op te vragen.|

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak vervolgens een object CognitiveServicesCredentials met uw sleutel en gebruik het met uw eindpunt om een [QnAMakerClient-object](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python) te maken.


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

 Gebruik het clientobject om een [object knowledge base operations](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) te krijgen.

Een kennisbank slaat vraag- en antwoordparen op voor het [Object CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) uit drie bronnen:

* Gebruik **voor redactionele inhoud**het [QnADTO-object.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python)
* Voor **bestanden**gebruikt u het object [FileDTO.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python)
* Voor **URL's**gebruikt u een lijst met tekenreeksen.

Roep de [maakmethode](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) aan en geef de geretourneerde bewerkings-id door aan de methode [Operations.getDetails](#get-status-of-an-operation) om te peilen naar status.

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]

Zorg ervoor dat [`_monitor_operation`](#get-status-of-an-operation) de functie, waarnaar in de bovenstaande code wordt verwezen, wordt opgenomen om met succes een kennisbank te maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U een kennisbank bijwerken door de knowledge base-id en een [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) met [add,](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python) [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python)en [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) DTO-objecten in te voeren aan de [updatemethode.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) Gebruik de methode [Operation.getDetail](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

Zorg ervoor dat [`_monitor_operation`](#get-status-of-an-operation) de functie, waarnaar in de bovenstaande code wordt verwezen, wordt opgenomen om een kennisbank met succes bij te werken.

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de kennisbank met behulp van de [publicatiemethode.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) Dit neemt het huidige opgeslagen en getrainde model, waarnaar wordt verwezen door de knowledge base ID, en publiceert dat op een eindpunt.

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>Download een kennisbank

Gebruik de [downloadmethode](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) om de database te downloaden als een lijst van [QnADocumentsDTO.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python) Dit is _niet_ gelijk aan de export van de QnA Maker-portal vanaf de pagina **Instellingen,** omdat het resultaat van deze methode geen TSV-bestand is.

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de kennisbank met behulp van de [verwijdermethode](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) met een parameter van de knowledge base ID.

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking opdoen

Sommige methoden, zoals maken en bijwerken, kunnen voldoende tijd in beslag nemen dat in plaats van te wachten tot het proces is voltooid, een [bewerking](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation%28class%29?view=azure-python) wordt geretourneerd. Gebruik de bewerkings-id van de bewerking naar de poll (met logica opnieuw proberen) om de status van de oorspronkelijke methode te bepalen.

De _setTime-out-aanroep_ in het volgende codeblok wordt gebruikt om asynchrone code te simuleren. Vervang dit door logica voor opnieuw proberen.

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de `python knowledgebase_quickstart.py` toepassing met opdracht uit uw toepassingsmap uit.

Alle codefragmenten in dit artikel zijn [beschikbaar](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) en kunnen als één bestand worden uitgevoerd.

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)
