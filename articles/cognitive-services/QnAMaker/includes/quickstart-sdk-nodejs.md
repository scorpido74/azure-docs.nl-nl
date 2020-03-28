---
title: 'Snelstart: QnA Maker-clientbibliotheek voor Node.js'
description: Deze quickstart laat zien hoe je aan de slag met de QnA Maker clientbibliotheek voor Node.js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021195"
---
Gebruik de QnA Maker-clientbibliotheek voor Node.js om:

* Een kennisdatabase maken
* Een kennisdatabase bijwerken
* Een kennisdatabase publiceren
* Gepubliceerde eindpuntsleutel opmaken
* Wachten op langlopende taak
* Kennisbank verwijderen

[Voorbeeld van referentiedocumentatie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [Maak er gratis een](https://azure.microsoft.com/free/)
* De huidige versie van [Node.js](https://nodejs.org).

## <a name="setting-up"></a>Instellen

### <a name="create-a-qna-maker-azure-resource"></a>Een Azure-bron van QnA Maker maken

Azure Cognitive Services worden vertegenwoordigd door Azure-resources waarop u zich abonneert. Maak een bron voor QnA Maker met behulp van de [Azure-portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) of [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) op uw lokale machine.

Nadat u de sleutel en het eindpunt uit uw resource hebt opgehaald, haalt u de waarden uit de Azure-portal voor uw nieuwe bron op de quickstart-pagina.

[Omgevingsvariabelen maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication), `QNAMAKER_AUTHORING_KEY` `QNAMAKER_ENDPOINT`benoemd en . U het [bestand .env.sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) kopiëren naar `.env` en de omgevingsvariabelen in dat bestand gebruiken.

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor uw app en navigeer ernaar.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Voer `npm init -y` de opdracht uit om een `package.json` knooppunttoepassing met een bestand te maken.

```console
npm init -y
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer de vereiste en optionele NPM-pakketten:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

Het bestand `package.json` van uw app wordt bijgewerkt met de afhankelijkheden. Het `dotenv` is optioneel en wordt gebruikt om u in staat te stellen de omgevingsvariabelen in een tekstbestand in te stellen. Controleer de `.env` in uw bron controle niet.


## <a name="object-model"></a>Objectmodel

De QnA Maker-client is een [QnAMakerClient-object](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) dat zich verifieert naar Azure met behulp van ServiceClientCredentials, dat uw sleutel bevat.

Zodra de client is gemaakt, gebruikt u de [eigenschap Knowledge base](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) die uw kennisbank maakt, beheert en publiceert.

Beheer uw kennisbank door een JSON-object te verzenden. Voor onmiddellijke bewerkingen retourneert een methode meestal een JSON-object dat de status aangeeft. Voor langlopende bewerkingen is het antwoord de bewerkings-ID. Bel de [klant. Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) methode met de bewerkings-ID om de [status van de aanvraag](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)te bepalen .


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende doen met de QnA Maker-clientbibliotheek voor Node.js:

* [Een kennisdatabase maken](#create-a-knowledge-base)
* [Een kennisdatabase bijwerken](#update-a-knowledge-base)
* [Een kennisdatabase publiceren](#publish-a-knowledge-base)
* [Een knowledge base verwijderen](#delete-a-knowledge-base)
* [Gepubliceerd eindpunt oppakken](#get-published-endpoint)
* [Status van een bewerking opdoen](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Maak een bestand met de naam `index.js`. Voeg de QnA Maker-bibliotheek en de afhankelijkheden toe aan het bestand.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Maak variabelen voor het Azure-eindpunt en de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell sluiten en opnieuw openen om toegang te krijgen tot de variabele.

|Omgevingsvariabele|Variabele Node.js|Voorbeeld|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|De sleutel is een tekenreeks met 32 tekens en is beschikbaar in de Azure-portal, op de QnA Maker-bron, op de quickstart-pagina. Dit is niet hetzelfde als de voorspellingeindpuntsleutel.|
|`QNAMAKER_ENDPOINT`|`endpoint`| Het ontwerpeindpunt van uw ontwerp, in de opmaak van `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`, bevat uw **bronnaam**. Dit is niet dezelfde URL die wordt gebruikt om het voorspellingseindpunt op te vragen.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak vervolgens een ApiKeyCredentials-object met uw sleutel en gebruik het met uw eindpunt om een [QnAMakerClient-object](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) te maken. Gebruik het clientobject om een [clientobject van](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) een kennisbank te krijgen.


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Een kennisdatabase maken

Een kennisbank slaat vraag- en antwoordparen op voor het [Object CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) uit drie bronnen:

* Gebruik **voor redactionele inhoud**het [QnADTO-object.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest)
* Voor **bestanden**gebruikt u het object [FileDTO.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest)
* Voor **URL's**gebruikt u een lijst met tekenreeksen.

Roep de [maakmethode](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) aan met de kennisbankinformatie. De knowledge base informatie is eigenlijk een JSON object.

Wanneer de methode voor maken terugkeert, geeft u de geretourneerde bewerkings-id door aan de [wait_for_operation](#get-status-of-an-operation) methode om te peilen naar status. De wait_for_operation methode wordt geretourneerd wanneer de bewerking is voltooid. Ontleden `resourceLocation` de kopwaarde van de geretourneerde bewerking om de nieuwe knowledge base ID te krijgen.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Zorg ervoor dat [`wait_for_operation`](#get-status-of-an-operation) de functie, waarnaar in de bovenstaande code wordt verwezen, wordt opgenomen om met succes een kennisbank te maken.

## <a name="update-a-knowledge-base"></a>Een kennisdatabase bijwerken

U een kennisbank bijwerken door de knowledge base-id en een [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) met [add,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add) [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)en [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO-objecten in te voeren aan de [updatemethode.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) De DTO's zijn ook in principe JSON objecten. Gebruik de [methode wait_for_operation](#get-status-of-an-operation) om te bepalen of de update is geslaagd.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Zorg ervoor dat [`wait_for_operation`](#get-status-of-an-operation) de functie, waarnaar in de bovenstaande code wordt verwezen, wordt opgenomen om een kennisbank met succes bij te werken.

## <a name="publish-a-knowledge-base"></a>Een kennisdatabase publiceren

Publiceer de kennisbank met behulp van de [publicatiemethode.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) Dit neemt het huidige opgeslagen en getrainde model, waarnaar wordt verwezen door de knowledge base ID, en publiceert dat op een eindpunt. Controleer de HTTP-antwoordcode om te valideren publiceren geslaagd.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Gepubliceerd eindpunt oppakken

Zodra de kennisbank is gepubliceerd, u toegang krijgen tot de gepubliceerde kennisbank via de runtime'generateAnswer API voor queryvoorspelling. Om dit te doen, hebt u de eindpuntsleutel van de runtime nodig. Dit is anders dan de ontwerpsleutel.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

Twee eindpuntsleutels worden geretourneerd uit het gesprek. Er is er slechts één nodig om toegang te krijgen tot het eindpunt runtime.

## <a name="delete-a-knowledge-base"></a>Een knowledge base verwijderen

Verwijder de kennisbank met behulp van de [verwijdermethode](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) met een parameter van de knowledge base ID.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Status van een bewerking opdoen

Sommige methoden, zoals maken en bijwerken, kunnen voldoende tijd in beslag nemen dat in plaats van te wachten tot het proces is voltooid, een [bewerking](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) wordt geretourneerd. Gebruik de [bewerkings-id](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) van de bewerking naar de poll (met logica opnieuw proberen) om de status van de oorspronkelijke methode te bepalen.

De _delayTimer-aanroep_ in het volgende codeblok wordt gebruikt om de logica voor opnieuw proberen te simuleren. Vervang dit door uw eigen logica voor nieuwe try.Replace this with your own retry logic.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de `node index.js` toepassing met opdracht uit uw toepassingsmap uit.

Alle codefragmenten in dit artikel zijn [beschikbaar](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) en kunnen als één bestand worden uitgevoerd.

```console
node index.js
```

Het programma drukt de status af op de console:

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een abonnement voor Cognitive Services wilt opschonen en verwijderen, u de bron- of brongroep verwijderen. Als u de brongroep verwijdert, worden ook andere bronnen verwijderd die eraan zijn gekoppeld.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)