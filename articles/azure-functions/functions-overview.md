---
title: Overzicht van Azure Functions | Microsoft Docs
description: Informatie over het gebruik van Azure Functions voor het optimaliseren van asynchrone workloads.
documentationcenter: na
author: mattchenderson
manager: jeconnoc
keywords: Azure-functies, functies, gebeurtenisverwerking, webhooks, dynamisch berekenen, architectuur zonder server
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: azure-functions
ms.topic: overview
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f3fc7691fc3afa3a1fe886655353d9ed41f631cc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096082"
---
# <a name="an-introduction-to-azure-functions"></a>Een inleiding tot Azure Functions  
Azure Functions is een oplossing voor het eenvoudig uitvoeren van kleine stukjes code, ofwel ‘functies’, in de cloud. U hoeft alleen de code te schrijven die u op dat moment nodig hebt, zonder dat u een complete toepassing of de bijbehorende infrastructuur nodig hebt. Met functies kunnen ontwikkel aars nog productiever worden en kunt u de gewenste programmeer taal gebruiken, zoals C#Java, java script, Power shell en python. U betaalt alleen voor de tijd dat uw code wordt uitgevoerd en Azure zorgt het eventuele schalen. Met Azure Functions kunt u [serverloze](https://azure.microsoft.com/solutions/serverless/) toepassingen ontwikkelen in Microsoft Azure.

Dit onderwerp bevat een globaal overzicht van Azure Functions. Als u meteen aan de slag wilt met Functions, kunt u beginnen met [Uw eerste Azure-functie maken](functions-create-first-azure-function.md). Als u behoefte hebt aan meer technische informatie over Functions, raadpleegt u de [naslaginformatie voor ontwikkelaars](functions-reference.md).

## <a name="features"></a>Functies
Hier volgen een aantal belangrijke kenmerken van Functions:

* **Keuze uit taal** : schrijf functies met behulp van C#uw keuze van, Java, java script, python en andere talen. Zie [ondersteunde talen](supported-languages.md) voor de volledige lijst.
* **Betalen per gebruik**: betaal alleen voor de tijd die nodig is voor het uitvoeren van uw code. Raadpleeg de optie voor het hostingabonnement Consumption in de sectie over [prijzen](#pricing).  
* **Breng uw eigen afhankelijkheden mee**: Functions ondersteunt NuGet en NPM, zodat u uw favoriete bibliotheken kunt gebruiken.  
* **Geïntegreerde beveiliging**: beveilig HTTP-geactiveerde functies met OAuth-providers zoals Azure Active Directory, Facebook, Google, Twitter en Microsoft-account.  
* **Eenvoudige integratie**: maak eenvoudig gebruik van Azure-services en Software-as-a-Service (SaaS)-producten. Zie de sectie [Integraties](#integrations) hieronder voor enkele voorbeelden.  
* **Flexibel ontwikkelen**: schrijf uw functies direct in de portal of stel doorlopende integratie in en implementeer uw code via [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) en andere [ondersteunde ontwikkelprogramma's](../app-service/deploy-local-git.md).  
* **Open-source**: de runtime van Functions is open-source en [beschikbaar op GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Wat kan ik doen met Functions?
Functions is de ideale oplossing voor het verwerken van gegevens, integreren van systemen, werken met IoT (Internet of Things) en het bouwen van eenvoudige API's en microservices. Overweeg om Functions te gebruiken voor taken zoals het verwerken van afbeeldingen of bestellingen, onderhouden van bestanden, of voor andere taken die u wilt uitvoeren volgens een planning. 

Functions biedt sjablonen waarmee u meteen aan de slag kunt met essentiële scenario's, zoals:

* **HTTPTrigger**: activeer de uitvoering van de code met een HTTP-aanvraag. Zie [Uw eerste functie maken](functions-create-first-azure-function.md) voor een voorbeeld.
* **TimerTrigger**: voer opschoonacties of andere batchtaken uit volgens een vooraf gedefinieerd schema. Zie [Een functie maken die wordt geactiveerd door een timer](functions-create-scheduled-function.md) voor een voorbeeld.
* **CosmosDBTrigger** -verwerk Azure Cosmos DB-documenten wanneer deze worden toegevoegd of worden bijgewerkt in verzamelingen in een NoSQL-database. Zie voor meer informatie [Gegevensbindingen in Azure Cosmos DB](functions-bindings-cosmosdb-v2.md).
* **BlobTrigger**: verwerk Azure Storage-blobs wanneer ze worden toegevoegd aan containers. Deze functie kunt u bijvoorbeeld gebruiken voor het wijzigen van de grootte van afbeeldingen. Zie [Blob Storage-bindingen](functions-bindings-storage-blob.md) voor meer informatie.
* **QueueTrigger**: reageer op berichten die binnenkomen in een Azure Storage-wachtrij. Zie voor meer informatie [Gegevensbindingen in Azure Queue-opslag](functions-bindings-storage-queue.md).
* **EventGridTrigger**: reageren op gebeurtenissen die worden geleverd aan een abonnement in Azure Event Grid. Biedt ondersteuning voor een model op basis van abonnement voor het ontvangen van gebeurtenissen, waaronder opties om te filteren. Een goede oplossing voor het bouwen van op gebeurtenissen gebaseerde architecturen. Zie [Formaat van geüploade afbeeldingen automatisch wijzigen met Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) voor een voorbeeld.
* **EventHubTrigger**: reageer op gebeurtenissen die worden geleverd aan een Azure Event Hub. Dit is met name handig voor toepassingsinstrumentatie, verwerking van gebruikerservaringen of werkstromen, en scenario's met betrekking tot IoT (Internet of Things). Zie [Event Hubs-bindingen](functions-bindings-event-hubs.md) voor meer informatie.
* **ServiceBusQueueTrigger**: koppel uw code aan andere Azure-services of on-premises services door te luisteren naar berichtenwachtrijen. Zie [Service Bus-bindingen](functions-bindings-service-bus.md) voor meer informatie.
* **ServiceBusTopicTrigger**: koppel uw code aan andere Azure-services of on-premises services met een abonnement op onderwerpen. Zie [Service Bus-bindingen](functions-bindings-service-bus.md) voor meer informatie.

Azure Functions ondersteunt *triggers*, waarmee de uitvoering van code kan worden gestart, en *bindingen*, waarmee het coderen voor invoer- en uitvoergegevens kan worden vereenvoudigd. Zie [Naslaginformatie voor ontwikkelaars over triggers en bindingen van Azure Functions](functions-triggers-bindings.md) voor een gedetailleerde beschrijving van de triggers en bindingen van Azure Functions.

## <a name="integrations"></a>Integraties
Azure Functions integreert met diverse services van Azure en derden. Deze services kunnen uw functie activeren en het uitvoeren starten, of fungeren als invoer en uitvoer voor uw code. De volgende service-integraties worden ondersteund door Azure Functions:

* Azure Cosmos DB
* Azure Event Hubs
* Azure Event Grid
* Azure Notification Hubs
* Azure Service Bus (wachtrijen en onderwerpen)
* Azure Storage (blob, wachtrijen en tabellen)
* On-premises (met Service Bus)
* Twilio (SMS-berichten)

## <a name="pricing"></a>Wat kost Functions?
Azure Functions heeft twee soorten abonnementen. Kies het abonnement dat het beste aansluit bij uw behoeften: 

* **Consumption-abonnement**: wanneer de functie wordt uitgevoerd, biedt Azure alle benodigde rekenbronnen. U hoeft u geen zorgen te maken over het beheer van bronnen en betaalt alleen voor de tijd die nodig is voor het uitvoeren van de code. 
* **App Service-plan**: voer uw functies net zo uit als uw web-apps. Wanneer u App Service al voor andere toepassingen gebruikt, voert u de functies kosteloos binnen hetzelfde abonnement uit. 

Zie [Azure Functions scale and hosting](functions-scale.md) (Azure Functions schalen en hosten) voor meer informatie over hostingabonnementen. Volledige prijsinformatie is beschikbaar op de pagina [Prijzen voor Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Volgende stappen
* [Uw eerste Azure-functie maken](functions-create-first-azure-function.md)  
  Ga meteen aan de slag en maak uw eerste functie met de Azure Functions-snelstartgids. 
* [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)  
  Biedt meer technische informatie over de Azure Functions-runtime en bevat gedetailleerde informatie over het coderen van functies en het definiëren van triggers en bindingen.
* [Azure Functions testen](functions-test-a-function.md)  
  Beschrijft de verschillende hulpprogramma’s en technieken voor het testen van uw functies.
* [Azure Functions schalen](functions-scale.md)  
  Beschrijft de serviceabonnementen die beschikbaar zijn voor Azure Functions, zoals het hostingabonnement Consumption, en helpt u bij het kiezen van het juiste abonnement. 
* [Meer informatie over Azure App Service](../app-service/overview.md)  
  Azure Functions maakt gebruik van Azure App Service voor kernfunctionaliteit zoals implementaties, omgevingsvariabelen en diagnostische procedures. 

