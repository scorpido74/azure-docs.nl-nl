---
title: 'Quickstart: Uw eerste functie maken in Azure met behulp van Visual Studio'
description: In deze quickstart leert u hoe u voor een Azure-functie een HTTP-trigger kunt maken en publiceren met behulp van Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: a4549bd2947332d7140f4f440a5344f417430554
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122745"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Quickstart: Uw eerste functie maken in Azure met behulp van Visual Studio

Met Azure Functions kunt u uw code in een serverloze omgeving uitvoeren zonder dat u eerst een virtuele machine moet maken of een webtoepassing moet publiceren.

In deze quickstart leert u hoe u met Visual Studio 2019 lokaal een 'Hello World' C#-functie-app voor HTTP-triggers kunt maken en testen , die u vervolgens naar Azure publiceert. 

![De reactie van de lokale host van de functie in de browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Deze quickstart is ontworpen voor Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, installeert u eerst [Visual Studio 2019](https://azure.microsoft.com/downloads/). Zorg ervoor dat u tijdens de installatie de **Azure-ontwikkeling**-werkbelasting selecteert. Als u in plaats daarvan een Azure Functions-project wilt maken met behulp van Visual Studio 2017, moet u eerst de [nieuwste hulpprogramma's van Azure Functions](functions-develop-vs.md#check-your-tools-version) installeren.

![Visual Studio installeren met de Azure-ontwikkelworkload](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/dotnet/) voordat u begint.

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio maakt een project en klasse die standaardcode voor het type HTTP-triggerfunctie bevat. Met het kenmerk van de `FunctionName`-methode wordt de naam van de functie ingesteld. Deze is standaard `Function1`. Met het kenmerk `HttpTrigger` wordt aangegeven dat de functie wordt geactiveerd door een HTTP-aanvraag. De standaardcode verzendt een HTTP-reactie met een waarde uit de hoofdtekst van de aanvraag of uit de query-tekenreeks.

U kunt de mogelijkheden van de functie uitbreiden met in- en uitvoerbindingen door de juiste kenmerken op de methode toe te passen. Zie de sectie [Triggers en bindingen](functions-dotnet-class-library.md#triggers-and-bindings) van de [Azure Functions C#-referentie voor ontwikkelaars](functions-dotnet-class-library.md) voor meer informatie.

Nu u uw functieproject en een HTTP-triggerfunctie hebt gemaakt, kunt u deze testen op uw lokale computer.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio kan worden geïntegreerd met Azure Functions Core Tools zodat u uw functies lokaal kunt testen met behulp van de volledige Azure Functions-runtime.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

Voordat u uw project kunt publiceren, moet u een functie-app in uw Azure-abonnement hebben. Visual Studio Publishing maakt een functie-app wanneer u de eerste keer uw project publiceert.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de basis-URL van de functie-app van de pagina **Profiel publiceren**. Vervang het `localhost:port`-deel van de URL dat u hebt gebruikt bij het lokaal testen van de functie door de nieuwe basis-URL. Voeg de queryreeks `?name=<YOUR_NAME>` toe aan de URL en voer de aanvraag uit.

    De URL die uw HTTP-triggerfunctie aanroept, heeft de volgende indeling:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. Hieronder ziet u het antwoord op de externe GET-aanvraag dat door de functie wordt geretourneerd, weergegeven in de browser:

    ![Het antwoord van de functie in de browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Visual Studio gebruikt om in Azure een C#-functie-app met een eenvoudige HTTP-triggerfunctie te maken en te publiceren. 

Ga naar het volgende artikel voor informatie over het toevoegen van een Azure Storage-wachtrijbinding aan uw functie:
> [!div class="nextstepaction"]
> [Een Azure Storage-wachtrijbinding aan uw functie toevoegen](functions-add-output-binding-storage-queue-vs.md)

