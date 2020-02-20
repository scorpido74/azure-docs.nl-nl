---
title: Wat is Azure App Configuration?
description: Een overzicht van de service Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/24/2019
ms.openlocfilehash: 67a76d3b347ef750b639225a009cc6968cb84c6f
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467552"
---
# <a name="what-is-azure-app-configuration"></a>Wat is Azure App Configuration?

Azure-app-configuratie biedt een service voor het centraal beheren van toepassings instellingen en functie vlaggen. Moderne Program ma's, met name Program ma's die worden uitgevoerd in een Cloud, hebben doorgaans veel onderdelen die worden gedistribueerd. Het verspreiden van configuratie-instellingen over deze onderdelen kan leiden tot moeilijk oplosbare fouten tijdens de implementatie van een toepassing. Gebruik app-configuratie om alle instellingen voor uw toepassing op te slaan en de toegang op één locatie te beveiligen.

## <a name="why-use-app-configuration"></a>Waarom app-configuratie gebruiken?

Cloudgebaseerde toepassingen worden vaak uitgevoerd op meerdere virtuele machines of containers in meerdere regio's en gebruiken meerdere externe services. Het maken van een dergelijke gedistribueerde toepassing die robuust en schaalbaar is, is een uitdaging.

Verschillende programmeer methodieken helpen ontwikkel aars bij het verg Roten van de complexiteit van het bouwen van toepassingen. In de app met [twaalf factoren](https://12factor.net/) worden bijvoorbeeld veel goed geteste architectuur patronen en best practices voor gebruik met Cloud toepassingen beschreven. Een belangrijke aanbeveling in deze handleiding is om de configuratie van de code te scheiden. In dit geval moeten de configuratie-instellingen van een toepassing buiten het uitvoer bare bestand worden bewaard en worden gelezen vanuit de runtime-omgeving of een externe bron.

Hoewel een toepassing gebruik kan maken van app-configuratie, zijn de volgende voor beelden de typen toepassing die het gebruik ervan kunnen doen:

* Micro Services op basis van de Azure Kubernetes-service, Azure Service Fabric of andere apps die zijn geïmplementeerd in een of meer geographs
* Serverloze apps, die Azure Functions of andere op gebeurtenissen gebaseerde stateless Compute-apps bevatten
* Pijp lijn voor continue implementatie

App Configuration biedt de volgende voordelen:

* Een volledig beheerde service die binnen enkele minuten kan worden ingesteld
* Flexibele sleutel representaties en toewijzingen
* Labelen met labels
* Herhaling van instellingen naar een bepaald tijdstip
* Exclusieve gebruikers interface voor functie vlaggen beheer
* Vergelijking van twee sets configuraties op aangepaste dimensies
* Verbeterde beveiliging via door Azure beheerde identiteiten
* Volledige gegevens versleuteling, op rest of in door Voer
* Systeem eigen integratie met populaire Frameworks

App-configuratie complementen [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), die wordt gebruikt voor het opslaan van toepassings geheimen. Met app-configuratie kunt u de volgende scenario's eenvoudiger implementeren:

* Beheer en distributie van hiërarchische configuratie gegevens voor verschillende omgevingen en geographs centraliseren
* Toepassings instellingen dynamisch wijzigen zonder de nood zaak om een toepassing opnieuw te implementeren of opnieuw op te starten
* Beschik baarheid van functies in realtime best uren

## <a name="use-app-configuration"></a>App-configuratie gebruiken

De eenvoudigste manier om een app-configuratie archief toe te voegen aan uw toepassing, is via een client bibliotheek die door micro soft wordt verstrekt. Op basis van de programmeer taal en het Framework zijn de volgende beste methoden voor u beschikbaar.

| Computertaal en framework | Verbinding maken |
|---|---|
| .NET core en ASP.NET Core | App-configuratie provider voor .NET core |
| .NET Framework en ASP.NET | App Configuration Builder voor .NET |
| Java Spring | App-configuratie client voor lente-Cloud |
| Andere | REST API app-configuratie |

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Snelstartgids voor .NET core](./quickstart-dotnet-core-app.md)
* [Snelstartgids .NET Framework](./quickstart-dotnet-app.md)
* [Snelstartgids Azure Functions](./quickstart-azure-functions-csharp.md)
* [Snelstartgids voor Java Spring](./quickstart-java-spring-app.md)
* [Quick start voor functie vlag ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Snelstartgids voor Spring boot-functie](./quickstart-feature-flag-spring-boot.md)
