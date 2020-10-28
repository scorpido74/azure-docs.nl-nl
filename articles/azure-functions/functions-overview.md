---
title: Overzicht van Azure Functions
description: Informatie over het gebruik van Azure Functions voor het optimaliseren van asynchrone workloads.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 0583b68df603b04d47ac6104f0cf127b3c4bedd0
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173720"
---
# <a name="an-introduction-to-azure-functions"></a>Een inleiding tot Azure Functions

Met Azure Functions kunt u kleine stukjes code ('functies' genoemd) uitvoeren zonder dat u zich zorgen hoeft te maken over de infrastructuur van de toepassing. Dankzij Azure Functions biedt de cloudinfrastructuur alle actuele servers die u nodig hebt om ervoor te zorgen dat uw toepassing op schaal wordt uitgevoerd.

Een functie wordt 'geactiveerd' door een specifiek type gebeurtenis. [Ondersteunde triggers](./functions-triggers-bindings.md) zijn onder meer reacties op wijzigingen in gegevens, antwoorden op berichten, een tijdschema of een HTTP-aanvraag.

Hoewel u altijd voor talloze services rechtstreeks code kunt gebruiken, wordt de integratie met andere services gestroomlijnd door middel van bindingen. Met bindingen hebt u [declaratieve toegang tot een groot aantal services van Azure en van derden](./functions-triggers-bindings.md).

## <a name="features"></a>Functies

Enkele essentiële functies van Azure Functions zijn:

- **Serverloze toepassingen** : met Functions kunt u [serverloze](https://azure.microsoft.com/solutions/serverless/) toepassingen ontwikkelen in Microsoft Azure.

- **Taalkeuze** : schrijf functies in de taal van uw voorkeur: [C#, Java, JavaScript, Python en PowerShell](supported-languages.md).

- **Betalen per gebruik** : betaal alleen voor de tijd die nodig is voor het uitvoeren van de code. Raadpleeg de optie voor het hostingabonnement Consumption in de sectie over [prijzen](#pricing).  

- **Uw eigen afhankelijkheden** : Functions biedt ondersteuning voor NuGet en NPM, zodat u toegang hebt tot uw favoriete bibliotheken.

- **Geïntegreerde beveiliging** : beveilig door HTTP-geactiveerde functies met OAuth-providers als Azure Active Directory, Facebook, Google, Twitter en een Microsoft-account.

- **Eenvoudige integratie** : maak eenvoudig gebruik van Azure-services en SaaS-producten (software-als-een-dienst).

- **Flexibel ontwikkelen** : stel continue integratie in en implementeer uw code via [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) en andere [ondersteunde ontwikkelprogramma's](../app-service/deploy-local-git.md).

- **Stateful, serverloze architectuur** : serverloze toepassingen indelen met [Durable Functions](durable/durable-functions-overview.md).

- **Opensource** : de runtime van Functions is opensource en [beschikbaar op GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Wat kan ik doen met Functions?

Functions is de ideale oplossing voor het verwerken van bulkgegevens, het integreren van systemen, het werken met IoT (Internet of Things) en het bouwen van eenvoudige API's en microservices.

Er is een reeks sjablonen beschikbaar, zodat u met belangrijke scenario's aan de slag kunt, waaronder:

- **HTTP** : code uitvoeren op basis van [HTTP-aanvragen](functions-create-first-azure-function.md)

- **Timer** : code plannen om [uit te laten voeren op vooraf gedefinieerde tijdstippen](./functions-create-scheduled-function.md)

- **Azure Cosmos DB** : [nieuwe en gewijzigde Azure Cosmos DB documenten](./functions-create-cosmos-db-triggered-function.md) verwerken

- **Blob Storage** : [nieuwe en gewijzigde Azure Storage-blobs](./functions-create-storage-blob-triggered-function.md) verwerken

- **Queue Storage** : reageren op [Azure Storage-wachtrijberichten](./functions-create-storage-queue-triggered-function.md)

- **Event Grid** : reageren op [Azure Event Grid-gebeurtenissen via abonnementen en filters](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Event Hub** : reageren op [hoge volumes van Azure Event Hub-gebeurtenissen](./functions-bindings-event-hubs.md)

- **Service Bus-wachtrij** : verbinding maken met andere Azure- of on-premises services door [Service Bus-wachtrijberichten te beantwoorden](./functions-bindings-service-bus.md)

- **Service Bus-onderwerp** : verbinding maken met andere Azure- of on-premises services door [Service Bus-onderwerpberichten te beantwoorden](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Wat kost Functions?

Azure Functions kent drie soorten abonnementen. Kies het abonnement dat het beste aansluit bij uw behoeften:

- **Verbruiksabonnement** : Azure biedt alle benodigde rekenresources. U hoeft u geen zorgen te maken over het beheer van resources en betaalt alleen voor de tijd die nodig is voor het uitvoeren van de code.

- **Premium-abonnement** : U geeft een aantal voorbereide instanties op die altijd online zijn en onmiddellijk kunnen reageren. Wanneer uw functie wordt uitgevoerd, kunt u zo nodig gebruikmaken van extra rekenresources. U betaalt voor de voorbereide instanties die continu worden uitgevoerd en voor extra instanties die u gebruikt naarmate de schaal van uw app wordt aangepast.

- **App Service-plan** : Voer uw functies net zo uit als uw web-apps. Als u App Service voor andere toepassingen gebruikt, kunnen de functies kosteloos binnen hetzelfde abonnement worden uitgevoerd.

Zie [Azure Functions scale and hosting](functions-scale.md) (Azure Functions schalen en hosten) voor meer informatie over hostingabonnementen. Volledige prijsinformatie is beschikbaar op de pagina [Prijzen voor Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste Azure-functie maken](functions-create-first-function-vs-code.md)  
  Aan de slag met [Visual Studio Code](functions-create-first-function-vs-code.md) of de [opdrachtregel](functions-create-first-azure-function-azure-cli.md), of maak gebruik van [Azure Portal](functions-create-first-azure-function.md).

- [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)  
  Biedt meer technische informatie over de Azure Functions-runtime en bevat gedetailleerde informatie over het coderen van functies en het definiëren van triggers en bindingen.

- [Azure Functions schalen](functions-scale.md)  
  Beschrijft de serviceabonnementen die beschikbaar zijn voor Azure Functions, zoals het hostingabonnement Consumption, en helpt u bij het kiezen van het juiste abonnement.

- [Meer informatie over Azure App Service](../app-service/overview.md)  
  Azure Functions maakt gebruik van Azure App Service voor kernfunctionaliteit zoals implementaties, omgevingsvariabelen en diagnostische procedures.
