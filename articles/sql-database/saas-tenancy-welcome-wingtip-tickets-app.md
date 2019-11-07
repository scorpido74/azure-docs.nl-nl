---
title: 'Welkom bij Wingtips app-Azure SQL Database '
description: Meer informatie over data base-pacht modellen en over de voor beeld-Wingtips SaaS-toepassing voor Azure SQL Database in de cloud omgeving.
keywords: zelfstudie sql-database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 6b8cb9bed2f5fdd0b246e73c84731081d0f2e41e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691822"
---
# <a name="the-wingtip-tickets-saas-application"></a>De SaaS-toepassing Wingtip tickets

Dezelfde *Wingtip tickets* SaaS-toepassing wordt in elk van de drie steek proeven geïmplementeerd. De app is een eenvoudige gebeurtenis vermelding en ticketing SaaS-app die gericht is op kleine locaties, theaters, klaveren enzovoort. Elke locatie is een Tenant van de app en heeft zijn eigen gegevens: locatie Details, lijsten met gebeurtenissen, klanten, ticket orders, enzovoort.  De app, samen met de beheer scripts en zelf studies, demonstreert een end-to-end SaaS-scenario. Dit omvat het inrichten van tenants, het bewaken en beheren van prestaties, schema beheer en het rapporteren van multi tenants en analyses.

## <a name="three-saas-application-and-tenancy-patterns"></a>Drie SaaS-toepassingen en pacht patronen

Er zijn drie versies van de app beschikbaar; elke verkennen een ander data base-pacht patroon op Azure SQL Database.  De eerste gebruikt een zelfstandige toepassing per Tenant met een eigen data base. De tweede maakt gebruik van een multi tenant-app met een Data Base per Tenant. Het derde voor beeld maakt gebruik van een multi tenant-app met Shard multi tenant-data bases.

![Drie pacht patronen][image-three-tenancy-patterns]

 Elk voor beeld bevat de toepassings code plus beheer scripts en zelf studies die een bereik van ontwerp-en beheer patronen verkennen.  Elk voor beeld implementeert in minder dan vijf minuten.  Alle drie kunnen naast elkaar worden geïmplementeerd, zodat u de verschillen in het ontwerp en het beheer kunt vergelijken.

## <a name="standalone-application-per-tenant-pattern"></a>Zelfstandige toepassing per Tenant patroon

De zelfstandige app per Tenant patroon gebruikt één Tenant toepassing met een Data Base voor elke Tenant. De app van elke Tenant, met inbegrip van de bijbehorende data base, wordt geïmplementeerd in een afzonderlijke Azure-resource groep. De resource groep kan worden geïmplementeerd in het abonnement van de service provider of het abonnement van de Tenant en worden beheerd door de provider namens de tenants. De zelfstandige app per Tenant patroon biedt de grootste isolatie van tenants, maar is doorgaans de duur van het delen van resources tussen meerdere tenants.  Dit patroon is zeer geschikt voor toepassingen die ingewik kelder zijn en die worden geïmplementeerd op kleinere aantallen tenants.  Met zelfstandige implementaties kan de app gemakkelijker worden aangepast voor elke Tenant dan bij andere patronen.  

Bekijk de [zelf studies][docs-tutorials-for-wingtip-sa] en code op github [. ../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Data base per Tenant patroon

De data base per Tenant patroon is van kracht voor service providers die zich bij Tenant isolatie bevinden en willen een gecentraliseerde service uitvoeren die de kosten efficiënt gebruik van gedeelde resources mogelijk maakt. Er wordt een Data Base gemaakt voor elke locatie of Tenant, en alle data bases worden centraal beheerd. Data bases kunnen worden gehost in elastische Pools om rendabel en eenvoudig beheer van prestaties te bieden, die gebruikmaken van de onvoorspelbare werkbelasting patronen van de tenants. Een catalogus database bevat de toewijzing tussen tenants en hun data bases. Deze toewijzing wordt beheerd met behulp van de Shard-kaart beheer functies van de [Elastic database-client bibliotheek](sql-database-elastic-database-client-library.md), die efficiënt verbindings beheer biedt voor de toepassing.

Bekijk de [zelf studies][docs-tutorials-for-wingtip-dpt] en code op github [. ../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Patroon van Shard multi tenant-data base

Multi tenant-data bases zijn effectief voor service providers die op zoek zijn naar lagere kosten per Tenant en kunnen goed werken met beperkte Tenant isolatie. Met dit patroon kan een groot aantal tenants in een afzonderlijke Data Base worden verpakt, waardoor de kosten per Tenant lager zijn. De bijna oneindige schaal is mogelijk door de tenants in meerdere data bases te sharding. Een catalogus database wijst tenants toe aan data bases.  

Dit patroon biedt ook een *hybride* model waarin u kunt optimaliseren voor kosten met meerdere tenants in een Data Base, of om te optimaliseren voor isolatie met één Tenant in hun eigen data base. De keuze kan per Tenant worden gemaakt, hetzij wanneer de Tenant is ingericht of op een later tijdstip, zonder dat dit van invloed is op de toepassing.  Dit model kan effectief worden gebruikt wanneer groepen tenants anders moeten worden behandeld. Er kunnen bijvoorbeeld goedkope tenants worden toegewezen aan gedeelde data bases, terwijl Premium-tenants kunnen worden toegewezen aan hun eigen data bases. 

Bekijk de [zelf studies][docs-tutorials-for-wingtip-mt] en code op github [. ../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Volgende stappen

#### <a name="conceptual-descriptions"></a>Conceptuele beschrijvingen

- Een meer gedetailleerde uitleg van de toepassingsman-patronen is beschikbaar in [multi tenant SaaS data base pacht-patronen][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Zelf studies en code

- Zelfstandige app per Tenant:
    - [Zelf studies voor zelfstandige app][docs-tutorials-for-wingtip-sa].
    - [Code voor zelfstandige app op github][github-code-for-wingtip-sa].

- Data base per Tenant:
    - [Zelf studies voor data base per Tenant][docs-tutorials-for-wingtip-dpt].
    - [Code voor de data base per Tenant op github][github-code-for-wingtip-dpt].

- Shard multi tenant:
    - [Zelf studies voor Shard multi tenant][docs-tutorials-for-wingtip-mt].
    - [Code voor Shard multi-tenant op github][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Drie pacht-patronen."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

