---
title: Overzicht van Azure Functions
description: Informatie over het gebruik van Azure Functions voor het optimaliseren van asynchrone workloads.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621008"
---
# <a name="an-introduction-to-azure-functions"></a>Een inleiding tot Azure Functions

Met Azure Functions kunt u kleine stukjes code (' functies ' genoemd) uitvoeren zonder dat u zich zorgen hoeft te maken over de infra structuur van de toepassing. Met Azure Functions biedt de Cloud infrastructuur alle actuele servers die u nodig hebt om ervoor te zorgen dat uw toepassing op schaal wordt uitgevoerd.

Een functie wordt ' geactiveerd ' door een specifiek type gebeurtenis. [Ondersteunde triggers](./functions-triggers-bindings.md) zijn het reageren op wijzigingen in gegevens, het reageren op berichten, het uitvoeren op basis van een schema of als resultaat van een HTTP-aanvraag.

Hoewel u altijd rechtstreeks code kunt gebruiken voor een talloze Services, wordt de integratie met andere Services gestroomlijnd door middel van bindingen. Bindingen bieden u [declaratieve toegang tot een groot aantal services van Azure en van](./functions-triggers-bindings.md)derden.

## <a name="features"></a>Functies

Enkele belang rijke functies van Azure Functions zijn:

- **Serverloze toepassingen**: met functies kunt u op Microsoft Azure [serverloze](https://azure.microsoft.com/solutions/serverless/) toepassingen ontwikkelen.

- **Keuze van taal**: schrijf functies met behulp van uw keuze van [ C#, Java, java script, python en Power shell](supported-languages.md).

- **Prijs model voor betalen per gebruik**: Betaal alleen voor de tijd die nodig is om uw code uit te voeren. Raadpleeg de optie voor het hostingabonnement Consumption in de sectie over [prijzen](#pricing).  

- Maak **uw eigen afhankelijkheden**: functies bieden ondersteuning voor NUGET en NPM, zodat u toegang hebt tot uw favoriete bibliotheken.

- **Geïntegreerde beveiliging**: Beveilig http-geactiveerde functies met OAuth-providers zoals Azure Active Directory, Facebook, Google, Twitter en micro soft-account.

- **Vereenvoudigde integratie**: u kunt eenvoudig integreren met Azure-Services en software-as-a-Service (SaaS)-aanbiedingen.

- **Flexibele ontwikkeling**: Stel continue integratie in en implementeer uw code via [github](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md)en andere [ondersteunde ontwikkel Programma's](../app-service/deploy-local-git.md).

- **Stateful serverloze architectuur**: serverloze toepassingen organiseren met [Durable functions](durable/durable-functions-overview.md).

- **Open-source**: de runtime van functions is open-source en [beschikbaar op github](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Wat kan ik doen met Functions?

Functions is een fantastische oplossing voor het verwerken van grote hoeveel heden gegevens, het integreren van systemen, het werken met het Internet der dingen (IoT) en het bouwen van eenvoudige Api's en micro Services.

Er is een reeks sjablonen beschikbaar om aan de slag te gaan met belang rijke scenario's, waaronder:

- **Http**: code uitvoeren op basis van [HTTP-aanvragen](functions-create-first-azure-function.md)

- **Timer**: schema code die moet worden [uitgevoerd op vooraf gedefinieerde tijdstippen](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: [nieuwe en gewijzigde Azure Cosmos DB documenten](./functions-create-cosmos-db-triggered-function.md) verwerken

- **Blob-opslag**: [nieuwe en gewijzigde blobs verwerken Azure Storage](./functions-create-storage-blob-triggered-function.md)

- **Wachtrij opslag**: reageren op [Azure Storage wachtrij berichten](./functions-create-storage-queue-triggered-function.md)

- **Event grid**: reageren op [Azure Event grid gebeurtenissen via abonnementen en filters](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Event hub**: reageren op [hoge volumes van Azure Event hub-gebeurtenissen](./functions-bindings-event-hubs.md)

- **Service Bus wachtrij**: verbinding maken met andere Azure-of on-premises services door [Service Bus wachtrij berichten te beantwoorden](./functions-bindings-service-bus.md)

- **Service Bus onderwerp**: verbinding maken tussen andere Azure-Services of on-premises Services door [te reageren op berichten van service bus onderwerp](./functions-bindings-service-bus.md)

## <a name="pricing"></a>Wat kost Functions?

Azure Functions heeft drie soorten prijs plannen. Kies het abonnement dat het beste aansluit bij uw behoeften:

- **Verbruiks plan**: Azure biedt alle benodigde reken bronnen. U hoeft zich geen zorgen te maken over het beheer van resources en betaalt alleen voor de tijd die uw code uitvoert.

- **Premium-abonnement**: u kunt een aantal vooraf gewarmde instanties opgeven die altijd online zijn en klaar zijn om direct te reageren. Wanneer uw functie wordt uitgevoerd, biedt Azure extra reken kundige resources die nodig zijn. U betaalt voor de vooraf gewarmde instanties die voortdurend worden uitgevoerd en alle extra exemplaren die u gebruikt als Azure schaalt uw app in en uit.

- **App service plan**: Voer uw functies uit op dezelfde manier als uw web-apps. Als u App Service gebruikt voor uw andere toepassingen, kunnen uw functies zonder extra kosten op hetzelfde abonnement worden uitgevoerd.

Zie [Azure Functions scale and hosting](functions-scale.md) (Azure Functions schalen en hosten) voor meer informatie over hostingabonnementen. Volledige prijsinformatie is beschikbaar op de pagina [Prijzen voor Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste Azure-functie maken](functions-create-first-function-vs-code.md)  
  Ga aan de slag met [Visual Studio code](functions-create-first-function-vs-code.md), de [opdracht regel](functions-create-first-azure-function-azure-cli.md)of gebruik de [Azure Portal](functions-create-first-azure-function.md).

- [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)  
  Biedt meer technische informatie over de Azure Functions-runtime en bevat gedetailleerde informatie over het coderen van functies en het definiëren van triggers en bindingen.

- [Azure Functions schalen](functions-scale.md)  
  Beschrijft de serviceabonnementen die beschikbaar zijn voor Azure Functions, zoals het hostingabonnement Consumption, en helpt u bij het kiezen van het juiste abonnement.

- [Meer informatie over Azure App Service](../app-service/overview.md)  
  Azure Functions maakt gebruik van Azure App Service voor kernfunctionaliteit zoals implementaties, omgevingsvariabelen en diagnostische procedures.
