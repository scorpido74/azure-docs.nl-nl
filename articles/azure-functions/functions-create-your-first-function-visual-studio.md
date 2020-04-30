---
title: 'Quick Start: uw eerste functie maken in azure met behulp van Visual Studio'
description: In deze Quick Start leert u hoe u een door HTTP geactiveerde Azure function maakt en publiceert met Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: e58414fa94a4ef54eb2f288b2cd636c10611460d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81308939"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Quick Start: uw eerste functie maken in azure met behulp van Visual Studio

Met Azure Functions kunt u uw code in een serverloze omgeving uitvoeren zonder dat u eerst een virtuele machine hoeft te maken of een webtoepassing moet publiceren.

In deze Quick Start leert u hoe u Visual Studio 2019 kunt gebruiken om lokaal een ' Hallo wereld ' HTTP-geactiveerde C#-functie-app te maken en te testen, die u vervolgens naar Azure publiceert. 

![De reactie van de lokale host van de functie in de browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Deze Quick start is ontworpen voor Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, installeert u eerst [Visual Studio 2019](https://azure.microsoft.com/downloads/). Zorg ervoor dat u de werk belasting van **Azure Development** tijdens de installatie selecteert. Als u in plaats daarvan een Azure Functions project wilt maken met behulp van Visual Studio 2017, moet u eerst de [meest recente Azure functions-hulpprogram ma's](functions-develop-vs.md#check-your-tools-version)installeren.

![Visual Studio installeren met de werk belasting Azure Development](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Als u nog geen [abonnement op Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)hebt, maak dan een [gratis account](https://azure.microsoft.com/free/dotnet/) aan voordat u begint.

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio maakt een project en een klasse die standaard code voor het functie type HTTP-trigger bevatten. Het `FunctionName` methode kenmerk stelt de naam van de functie in, die standaard is `Function1`. Het `HttpTrigger` kenmerk geeft aan dat de functie wordt geactiveerd door een HTTP-aanvraag. De standaardcode verzendt een HTTP-reactie met een waarde uit de hoofdtekst van de aanvraag of uit de query-tekenreeks.

Breid de mogelijkheden van uw functie uit met invoer-en uitvoer bindingen door de juiste kenmerken toe te passen op de-methode. Zie de sectie [Triggers en bindingen](functions-dotnet-class-library.md#triggers-and-bindings) van de [Azure Functions C#-referentie voor ontwikkelaars](functions-dotnet-class-library.md) voor meer informatie.

Nu u uw functieproject en een HTTP-geactiveerde functie hebt gemaakt, kunt u deze testen op uw lokale computer.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio kan worden geïntegreerd met Azure Functions Core Tools zodat u uw functies lokaal kunt testen met behulp van de volledige Azure Functions runtime.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

Voordat u uw project kunt publiceren, moet u een functie-app in uw Azure-abonnement hebben. Visual Studio Publishing maakt een functie-app voor u de eerste keer dat u uw project publiceert.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de basis-URL van de functie-app van de pagina profiel **publiceren** . Vervang het `localhost:port` gedeelte van de URL die u hebt gebruikt om de functie lokaal te testen met de nieuwe basis-URL. Voeg de query reeks `?name=<YOUR_NAME>` toe aan deze URL en voer de aanvraag uit.

    De URL die uw HTTP-geactiveerde functie aanroept, heeft de volgende indeling:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. In de volgende afbeelding ziet u het antwoord in de browser op de externe GET-aanvraag die wordt geretourneerd door de functie:

    ![Het antwoord van de functie in de browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u Visual Studio gebruikt voor het maken en publiceren van een C#-functie-app in azure met een eenvoudige HTTP-geactiveerde functie. 

Ga naar het volgende artikel voor meer informatie over het toevoegen van een Azure Storage wachtrij binding aan uw functie:
> [!div class="nextstepaction"]
> [Een Azure Storage wachtrij binding aan uw functie toevoegen](functions-add-output-binding-storage-queue-vs.md)

