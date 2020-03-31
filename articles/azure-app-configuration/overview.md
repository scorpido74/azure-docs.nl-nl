---
title: Wat is Azure App Configuration?
description: Een overzicht van de service Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 1f1cec68813d33e7fa19a414a30adfc9a41df91f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77523472"
---
# <a name="what-is-azure-app-configuration"></a>Wat is Azure App Configuration?

Azure App Configuration biedt een service om de toepassingsinstellingen en functievlaggen centraal te beheren. Moderne programma's, vooral programma's die in een cloud worden uitgevoerd, hebben over het algemeen veel componenten die in de natuur zijn gedistribueerd. Het verspreiden van configuratie-instellingen over deze onderdelen kan leiden tot moeilijk oplosbare fouten tijdens de implementatie van een toepassing. Gebruik App-configuratie om alle instellingen voor uw toepassing op te slaan en hun toegangen op één plaats te beveiligen.

## <a name="why-use-app-configuration"></a>Waarom app-configuratie gebruiken?

Cloudgebaseerde toepassingen worden vaak uitgevoerd op meerdere virtuele machines of containers in meerdere regio's en gebruiken meerdere externe services. Het creëren van een robuuste en schaalbare toepassing in een gedistribueerde omgeving vormt een aanzienlijke uitdaging.

Verschillende programmeermethoden helpen ontwikkelaars om te gaan met de toenemende complexiteit van bouwtoepassingen. De [Twelve-Factor App](https://12factor.net/) beschrijft bijvoorbeeld veel goed geteste architecturale patronen en best practices voor gebruik met cloudtoepassingen. Een belangrijke aanbeveling in deze handleiding is om de configuratie van de code te scheiden. De configuratie-instellingen van een toepassing moeten buiten de uitvoerbare en ingelezen worden vanuit de runtime-omgeving of een externe bron.

Hoewel elke toepassing gebruik kan maken van app-configuratie, zijn de volgende voorbeelden de typen toepassingen die profiteren van het gebruik ervan:

* Microservices op basis van Azure Kubernetes Service, Azure Service Fabric of andere gecontaineriseerde apps die zijn geïmplementeerd in een of meer regio's
* Serverloze apps, waaronder Azure-functies of andere statusloze rekenapps die door gebeurtenissen worden geleid
* Pijplijn voor continue implementatie

App Configuration biedt de volgende voordelen:

* Een volledig beheerde service die binnen enkele minuten kan worden ingesteld
* Flexibele sleutelrepresentaties en mappings
* Tagging met labels
* Point-in-time opnieuw afspelen van instellingen
* Speciale gebruikersinterface voor functievlagbeheer
* Vergelijking van twee sets configuraties op aangepaste afmetingen
* Verbeterde beveiliging via door Azure beheerde identiteiten
* Versleuteling van gevoelige informatie in rust en tijdens het transport
* Native integratie met populaire frameworks

App-configuratie is een aanvulling op [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), dat wordt gebruikt om toepassingsgeheimen op te slaan. App-configuratie maakt het eenvoudiger om de volgende scenario's te implementeren:

* Beheer en distributie van hiërarchische configuratiegegevens voor verschillende omgevingen en regio's centraliseren
* Toepassingsinstellingen dynamisch wijzigen zonder dat u een toepassing opnieuw hoeft te implementeren of opnieuw te starten
* Beschikbaarheid van functies in realtime beheren

## <a name="use-app-configuration"></a>App-configuratie gebruiken

De eenvoudigste manier om een App Configuration Store aan uw toepassing toe te voegen, is via een clientbibliotheek van Microsoft. De volgende methoden zijn beschikbaar om verbinding te maken met uw toepassing, afhankelijk van de door u gekozen taal en kader

| Computertaal en framework | Verbinding maken |
|---|---|
| .NET Core en ASP.NET Core | App-configuratieprovider voor .NET Core |
| .NET Framework en ASP.NET | App-configuratiebouwer voor .NET |
| Java Spring | App-configuratieclient voor Spring Cloud |
| Andere | API voor app-configuratierest |

## <a name="next-steps"></a>Volgende stappen

* [ASP.NET Core snelaan de start](./quickstart-aspnet-core-app.md)
* [.NET Core snelstart](./quickstart-dotnet-core-app.md)
* [.NET Framework snelstart](./quickstart-dotnet-app.md)
* [Snel start van Azure-functies](./quickstart-azure-functions-csharp.md)
* [Java Lente quickstart](./quickstart-java-spring-app.md)
* [ASP.NET Core-functievlag snel aan de slag](./quickstart-feature-flag-aspnet-core.md)
* [Spring Boot functie vlag snelstart](./quickstart-feature-flag-spring-boot.md)
