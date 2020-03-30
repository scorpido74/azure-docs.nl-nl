---
title: Welkom bij Wingtips app
description: Meer informatie over databasehuurmodellen en over de voorbeeld-Wingtips SaaS-toepassing voor Azure SQL Database in de cloudomgeving.
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
ms.openlocfilehash: 4e0b3afe51ac7c7a6b9213fcee79af57cbbd8197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818315"
---
# <a name="the-wingtip-tickets-saas-application"></a>De Wingtip Tickets SaaS applicatie

Dezelfde *Wingtip Tickets* SaaS applicatie wordt geïmplementeerd in elk van de drie monsters. De app is een eenvoudige evenement lijst en ticketing SaaS app gericht op kleine locaties - theaters, clubs, enz. Elke locatie is een huurder van de app, en heeft zijn eigen gegevens: locatie details, lijsten van evenementen, klanten, ticket orders, enz.  De app, samen met de beheerscripts en tutorials, toont een end-to-end SaaS-scenario. Dit omvat het inrichten van tenants, het bewaken en beheren van prestaties, schemabeheer en rapportage en analyse van meerdere tenants.

## <a name="three-saas-application-and-tenancy-patterns"></a>Drie SaaS applicatie- en huurpatronen

Er zijn drie versies van de app beschikbaar; elk verkent een ander databasehuurpatroon in Azure SQL Database.  De eerste maakt gebruik van een standalone applicatie per tenant met een eigen database. De tweede maakt gebruik van een multi-tenant app met een database per tenant. Het derde voorbeeld maakt gebruik van een multi-tenant app met geshard multi-tenant databases.

![Drie huurpatronen][image-three-tenancy-patterns]

 Elk voorbeeld bevat de toepassingscode, plus beheerscripts en zelfstudies die een reeks ontwerp- en beheerpatronen verkennen.  Elk monster wordt in minder dan vijf minuten geïmplementeerd.  Alle drie kunnen naast elkaar worden ingezet, zodat u de verschillen in ontwerp en beheer vergelijken.

## <a name="standalone-application-per-tenant-pattern"></a>Zelfstandige toepassing per tenantpatroon

De zelfstandige app per tenantpatroon maakt gebruik van één tenanttoepassing met een database voor elke tenant. De app van elke tenant, inclusief de database, wordt geïmplementeerd in een afzonderlijke Azure-brongroep. De resourcegroep kan worden geïmplementeerd in het abonnement van de serviceprovider of het abonnement van de huurder en namens de provider door de provider worden beheerd. De zelfstandige app per tenantpatroon biedt de grootste tenantisolatie, maar is meestal de duurste omdat er geen mogelijkheid is om resources tussen meerdere tenants te delen.  Dit patroon is zeer geschikt voor toepassingen die mogelijk complexer zijn en die worden geïmplementeerd in kleinere aantallen tenants.  Met zelfstandige implementaties kan de app gemakkelijker voor elke tenant worden aangepast dan in andere patronen.  

Bekijk de [tutorials][docs-tutorials-for-wingtip-sa] en code op GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Database per tenantpatroon

De database per tenantpatroon is effectief voor serviceproviders die zich bezighouden met tenantisolatie en een gecentraliseerde service willen uitvoeren die kostenefficiënt gebruik van gedeelde resources mogelijk maakt. Er wordt een database gemaakt voor elke locatie of tenant en alle databases worden centraal beheerd. Databases kunnen worden gehost in elastische pools om kostenefficiënt en eenvoudig prestatiebeheer te bieden, wat gebruik maakt van de onvoorspelbare werkbelastingpatronen van de tenants. Een catalogusdatabase bevat de toewijzing tussen tenants en hun databases. Deze toewijzing wordt beheerd met behulp van de shard map beheer functies van de [Elastic Database Client Library](sql-database-elastic-database-client-library.md), die efficiënt verbindingsbeheer biedt aan de toepassing.

Bekijk de [tutorials][docs-tutorials-for-wingtip-dpt] en code op GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Geshard databasepatroon met meerdere tenant

Multi-tenant databases zijn effectief voor dienstverleners op zoek naar lagere kosten per tenant en oke met verminderde tenant isolatie. Dit patroon maakt het mogelijk om grote aantallen tenants in een individuele database te verpakken, waardoor de kosten per huurder omlaag gaan. Bijna oneindige schaal is mogelijk door het sharden van de tenants in meerdere databases. Een catalogusdatabase brengt tenants in kaart aan databases.  

Dit patroon maakt het ook mogelijk een *hybride* model waarin u optimaliseren voor kosten met meerdere tenants in een database, of optimaliseren voor isolatie met een enkele tenant in hun eigen database. De keuze kan worden gemaakt op tenant-by-tenant basis, hetzij wanneer de huurder is ingericht of later, zonder gevolgen voor de toepassing.  Dit model kan effectief worden gebruikt wanneer groepen huurders anders moeten worden behandeld. Goedkope tenants kunnen bijvoorbeeld worden toegewezen aan gedeelde databases, terwijl premium tenants kunnen worden toegewezen aan hun eigen databases. 

Bekijk de [tutorials][docs-tutorials-for-wingtip-mt] en code op GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Volgende stappen

#### <a name="conceptual-descriptions"></a>Conceptuele beschrijvingen

- Een meer gedetailleerde uitleg van de sollicitatiehuurpatronen is beschikbaar bij [Multi-tenant SaaS database huurpatronen][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Zelfstudies en code

- Zelfstandige app per tenant:
    - [Tutorials voor standalone app][docs-tutorials-for-wingtip-sa].
    - [Code voor standalone app, op GitHub][github-code-for-wingtip-sa].

- Database per tenant:
    - [Zelfstudies voor database per tenant][docs-tutorials-for-wingtip-dpt].
    - [Code voor database per tenant, op GitHub][github-code-for-wingtip-dpt].

- Geshard multi-tenant:
    - [Tutorials voor geshard multi-tenant][docs-tutorials-for-wingtip-mt].
    - [Code voor geshard multi-tenant, op GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Drie huurpatronen."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

