---
title: Overzicht van Azure Functions
description: Informatie over het gebruik van Azure Functions voor het optimaliseren van asynchrone workloads.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621008"
---
# <a name="an-introduction-to-azure-functions"></a>Een inleiding tot Azure Functions

Met Azure Functions u kleine stukjes code uitvoeren (de zogenaamde functies) zonder u zorgen te maken over de toepassingsinfrastructuur. Met Azure Functions biedt de cloudinfrastructuur alle up-to-date servers die u nodig hebt om uw toepassing op schaal te laten draaien.

Een functie wordt "geactiveerd" door een specifiek type gebeurtenis. [Ondersteunde triggers](./functions-triggers-bindings.md) zijn onder andere reageren op wijzigingen in gegevens, reageren op berichten, draaien volgens een planning of als gevolg van een HTTP-verzoek.

Hoewel u altijd rechtstreeks coderen tegen een groot aantal services, wordt de integratie met andere services gestroomlijnd met behulp van bindingen. Bindingen geven u [declaratieve toegang tot een breed scala aan Azure- en services van derden.](./functions-triggers-bindings.md)

## <a name="features"></a>Functies

Enkele belangrijke functies van Azure-functies zijn:

- **Serverloze toepassingen:** met functies u [serverloze](https://azure.microsoft.com/solutions/serverless/) toepassingen ontwikkelen op Microsoft Azure.

- **Taalkeuze**: Schrijf functies met uw keuze uit [C#, Java, JavaScript, Python en PowerShell.](supported-languages.md)

- **Prijsmodel voor betalen per gebruik:** Betaal alleen voor de tijd die wordt besteed aan het uitvoeren van uw code. Raadpleeg de optie voor het hostingabonnement Consumption in de sectie over [prijzen](#pricing).  

- **Breng je eigen afhankelijkheden mee:** Functions ondersteunt NuGet en NPM, zodat je toegang hebt tot je favoriete bibliotheken.

- **Geïntegreerde beveiliging**: Bescherm http-geactiveerde functies met OAuth-providers zoals Azure Active Directory, Facebook, Google, Twitter en Microsoft-account.

- **Vereenvoudigde integratie:** eenvoudig te integreren met Azure-services en SaaS-aanbiedingen (software-as-a-service).

- **Flexibele ontwikkeling:** Stel continue integratie in en implementeer uw code via [GitHub,](../app-service/scripts/cli-continuous-deployment-github.md) [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md)en andere [ondersteunde ontwikkeltools.](../app-service/deploy-local-git.md)

- **Stateful serverless architectuur:** Orkestreren serverloze toepassingen met [duurzame functies](durable/durable-functions-overview.md).

- **Open-source**: De runtime van de functies is open-source en [beschikbaar op GitHub.](https://github.com/azure/azure-webjobs-sdk-script)

## <a name="what-can-i-do-with-functions"></a>Wat kan ik doen met Functions?

Functies is een geweldige oplossing voor het verwerken van bulkgegevens, het integreren van systemen, het werken met het internet-of-things (IoT) en het bouwen van eenvoudige API's en microservices.

Er is een reeks sjablonen beschikbaar om u op weg te helpen met belangrijke scenario's, waaronder:

- **HTTP**: Code uitvoeren op basis van [HTTP-aanvragen](functions-create-first-azure-function.md)

- **Timer:** code plannen om [op vooraf gedefinieerde tijden](./functions-create-scheduled-function.md) uit te voeren

- **Azure Cosmos DB:** Nieuwe [en gewijzigde Azure Cosmos DB-documenten verwerken](./functions-create-cosmos-db-triggered-function.md)

- **Blob-opslag:** [nieuwe en gewijzigde Azure Storage-blobs verwerken](./functions-create-storage-blob-triggered-function.md)

- **Wachtrijopslag:** reageren op [wachtrijberichten in Azure Storage](./functions-create-storage-queue-triggered-function.md)

- **Gebeurtenisraster:** reageren op [Azure Event Grid-gebeurtenissen via abonnementen en filters](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Gebeurtenishub:** reageren op [grote hoeveelheden Azure Event Hub-gebeurtenissen](./functions-bindings-event-hubs.md)

- **Servicebuswachtrij:** verbinding maken met andere Azure- of on-premises services [door te reageren op servicebuswachtrijberichten](./functions-bindings-service-bus.md)

- **Servicebusonderwerp:** andere Azure-services of on-premises services verbinden door [te reageren op berichten over het onderwerp ServiceBus](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Wat kost Functions?

Azure Functions heeft drie soorten prijsplannen. Kies het abonnement dat het beste aansluit bij uw behoeften:

- **Verbruiksplan**: Azure biedt alle benodigde rekenbronnen. U hoeft zich geen zorgen te maken over resourcebeheer en betaalt alleen voor de tijd dat uw code wordt uitgevoerd.

- **Premium-abonnement**: U geeft een aantal vooraf verwarmde exemplaren op die altijd online zijn en klaar zijn om onmiddellijk te reageren. Wanneer uw functie wordt uitgevoerd, biedt Azure alle extra rekenbronnen die nodig zijn. U betaalt voor de vooraf opgewarmde exemplaren die continu worden uitgevoerd en eventuele extra exemplaren die u gebruikt als Azure uw app in- en uitcheckt.

- **App Service-abonnement**: Voer uw functies uit, net als uw web-apps. Als u App Service gebruikt voor uw andere toepassingen, kunnen uw functies zonder extra kosten op hetzelfde abonnement worden uitgevoerd.

Zie [Azure Functions scale and hosting](functions-scale.md) (Azure Functions schalen en hosten) voor meer informatie over hostingabonnementen. Volledige prijsinformatie is beschikbaar op de pagina [Prijzen voor Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste Azure-functie maken](functions-create-first-function-vs-code.md)  
  Ga aan de slag met [Visual Studio Code,](functions-create-first-function-vs-code.md)de [opdrachtregel](functions-create-first-azure-function-azure-cli.md)of gebruik de [Azure-portal.](functions-create-first-azure-function.md)

- [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)  
  Biedt meer technische informatie over de Azure Functions-runtime en bevat gedetailleerde informatie over het coderen van functies en het definiëren van triggers en bindingen.

- [Azure Functions schalen](functions-scale.md)  
  Beschrijft de serviceabonnementen die beschikbaar zijn voor Azure Functions, zoals het hostingabonnement Consumption, en helpt u bij het kiezen van het juiste abonnement.

- [Meer informatie over Azure App Service](../app-service/overview.md)  
  Azure Functions maakt gebruik van Azure App Service voor kernfunctionaliteit zoals implementaties, omgevingsvariabelen en diagnostische procedures.
