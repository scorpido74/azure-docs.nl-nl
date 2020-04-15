---
title: 'Snelstart: uw eerste functie in Azure maken met Visual Studio'
description: In deze quickstart leert u hoe u een door HTTP geactiveerde Azure-functie maakt en publiceert met Behulp van Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: e58414fa94a4ef54eb2f288b2cd636c10611460d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308939"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Snelstart: uw eerste functie in Azure maken met Visual Studio

Met Azure Functions u uw code uitvoeren in een serverloze omgeving zonder eerst een VM te hoeven maken of een webtoepassing te publiceren.

In deze quickstart leert u hoe u Visual Studio 2019 gebruiken om lokaal een HTTP-geactiveerde C#-functie-app met 'hallo wereld' te maken en te testen, die u vervolgens publiceert naar Azure. 

![De reactie van de lokale host van de functie in de browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Deze quickstart is ontworpen voor Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten

Installeer [Visual Studio 2019](https://azure.microsoft.com/downloads/)als eerste om deze zelfstudie te voltooien. Zorg ervoor dat u de **Azure-ontwikkelingswerkbelasting** selecteert tijdens de installatie. Als u een Azure Functions-project wilt maken met Behulp van Visual Studio 2017, moet u eerst de [nieuwste Azure Functions-hulpprogramma's](functions-develop-vs.md#check-your-tools-version)installeren.

![Visual Studio installeren met de Azure-ontwikkelingswerkbelasting](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)hebt, maakt u een [gratis account](https://azure.microsoft.com/free/dotnet/) voordat u begint.

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio maakt een project en klasse die ketelplaatcode bevat voor het http-triggerfunctietype. Met `FunctionName` het kenmerk Methode wordt de naam `Function1`van de functie ingesteld, die standaard is . Het `HttpTrigger` kenmerk geeft aan dat de functie wordt geactiveerd door een HTTP-aanvraag. De standaardcode verzendt een HTTP-reactie met een waarde uit de hoofdtekst van de aanvraag of uit de query-tekenreeks.

Breid de mogelijkheden van uw functie uit met invoer- en uitvoerbindingen door de juiste kenmerken op de methode toe te passen. Zie de sectie [Triggers en bindingen](functions-dotnet-class-library.md#triggers-and-bindings) van de [Azure Functions C#-referentie voor ontwikkelaars](functions-dotnet-class-library.md) voor meer informatie.

Nu u uw functieproject en een HTTP-geactiveerde functie hebt gemaakt, kunt u deze testen op uw lokale computer.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio integreert met Azure Functions Core Tools, zodat u uw functies lokaal testen met de volledige runtime van Azure Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

Voordat u uw project publiceren, moet u een functie-app in uw Azure-abonnement hebben. Visual Studio publishing maakt een functie-app voor u wanneer u uw project voor het eerst publiceert.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de basis-URL van de functie-app vanaf de pagina **Profiel publiceren.** Vervang `localhost:port` het gedeelte van de URL dat u hebt gebruikt om de functie lokaal te testen door de nieuwe basis-URL. Sluit de querytekenreeks `?name=<YOUR_NAME>` toe aan deze URL en voer het verzoek uit.

    De URL die uw HTTP-geactiveerde functie aanroept, is in de volgende indeling:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. In de volgende afbeelding wordt het antwoord in de browser weergegeven op het externe GET-verzoek dat door de functie wordt geretourneerd:

    ![Het antwoord van de functie in de browser](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Visual Studio gebruikt om een C#-functie-app in Azure te maken en te publiceren met een eenvoudige HTTP-geactiveerde functie. 

Ga naar het volgende artikel voor meer informatie over het toevoegen van een Azure Storage-wachtrijbinding aan uw functie:
> [!div class="nextstepaction"]
> [Een Azure Storage-wachtrijbinding toevoegen aan uw functie](functions-add-output-binding-storage-queue-vs.md)

