---
title: Voorbeeld van multitenant-app - Wingtip SaaS
description: Meer informatie met behulp van een voorbeeld van multitenant-toepassing die Azure SQL Database gebruikt, het voorbeeld van Wingtip SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 46cdcd5f768278dbc729f48e450c68a63be604be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256495"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Inleiding tot een Multitenant SaaS-app die het database-per-tenant-patroon met SQL Database gebruikt

De Wingtip SaaS-toepassing is een voorbeeld multitenant-app. De app gebruikt het saas-toepassingspatroon database per tenant om meerdere tenants te bedienen. De app toont functies van Azure SQL Database waarmee SaaS-scenario's kunnen worden gebruikt met behulp van verschillende SaaS-ontwerp- en beheerpatronen. Om snel aan de slag te gaan, wordt de Wingtip SaaS-app in minder dan vijf minuten geïmplementeerd.

Toepassingsbroncode en beheerscripts zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Raadpleeg voordat u begint de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de beheerscripts van Wingtip Tickets te downloaden en te deblokkeren.

## <a name="application-architecture"></a>Toepassingsarchitectuur

De Wingtip SaaS-app maakt gebruik van het database-per-tenant-model. Het maakt gebruik van SQL elastische pools om de efficiëntie te maximaliseren. Voor het inrichten en toewijzen van tenants aan hun gegevens wordt een catalogusdatabase gebruikt. De kern Wingtip SaaS-toepassing maakt gebruik van een pool met drie voorbeeldtenants, plus de catalogusdatabase. De catalogus- en tenantservers zijn ingericht met DNS-aliassen. Deze aliassen worden gebruikt om een verwijzing te behouden naar de actieve resources die worden gebruikt door de Wingtip-toepassing. Deze aliassen worden bijgewerkt om te wijzen op herstelbronnen in de zelfstudies voor noodherstel. Het voltooien van veel van de Wingtip SaaS-zelfstudies resulteert in add-ons voor de eerste implementatie. Add-ons zoals analytische databases en cross-database schemabeheer worden geïntroduceerd.


![Wingtip SaaS architectuur](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Terwijl u door de zelfstudies gaat en met de app werkt, richt u zich op de SaaS-patronen die betrekking hebben op de gegevenslaag. Met andere woorden, focus op de gegevenslaag en analyseer de app zelf niet. Inzicht in de implementatie van deze SaaS-patronen is essentieel voor het implementeren van deze patronen in uw toepassingen. Houd ook rekening met eventuele noodzakelijke wijzigingen voor uw specifieke zakelijke vereisten.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS-zelfstudies

Nadat u de app hebt geïmplementeerd, onderzoekt u de volgende zelfstudies die voortbouwen op de eerste implementatie. In deze zelfstudies worden veelvoorkomende SaaS-patronen onderzocht die profiteren van ingebouwde functies van SQL Database, Azure SQL Data Warehouse en andere Azure-services. Tutorials bevatten PowerShell-scripts met gedetailleerde uitleg. De uitleg vereenvoudigt het begrijpen en implementeren van dezelfde SaaS-beheerpatronen in uw toepassingen.


| Zelfstudie | Beschrijving |
|:--|:--|
| [Richtlijnen en tips voor het voorbeeld van de SQL Database multitenant SaaS-app](saas-tenancy-wingtip-app-guidance-tips.md) | PowerShell-scripts downloaden en uitvoeren om delen van de toepassing voor te bereiden. |
|[De Wingtip SaaS-toepassing implementeren en verkennen](saas-dbpertenant-get-started-deploy.md)|  Implementeer en verken de Wingtip SaaS-toepassing met uw Azure-abonnement. |
|[Huurders van inrichten en catalogiseren](saas-dbpertenant-provision-and-catalog.md)| Ontdek hoe de toepassing verbinding maakt met tenants met behulp van een catalogusdatabase en hoe de catalogus tenants aan hun gegevens in kaart brengt. |
|[Prestaties bewaken en beheren](saas-dbpertenant-performance-monitoring.md)| Meer informatie over het gebruik van bewakingsfuncties van SQL Database en het instellen van waarschuwingen wanneer prestatiedrempels worden overschreden. |
|[Bewaken met Azure Monitor-logboeken](saas-dbpertenant-log-analytics.md) | Meer informatie over het gebruik van [Azure Monitor-logboeken](../log-analytics/log-analytics-overview.md) om grote hoeveelheden resources in meerdere groepen te controleren. |
|[Een individuele tenant terugzetten](saas-dbpertenant-restore-single-tenant.md)| Meer informatie over het herstellen van een tenantdatabase naar een eerder tijdstip. Lees ook hoe u herstellen naar een parallelle database, die de bestaande tenantdatabase online laat. |
|[Tenantdatabaseschema beheren](saas-tenancy-schema-management.md)| Meer informatie over het bijwerken van schema's en het bijwerken van referentiegegevens in alle tenantdatabases. |
|[Gedistribueerde query's met meerdere tenant uitvoeren](saas-tenancy-cross-tenant-reporting.md) | Maak een ad hoc analytics-database en voer realtime gedistribueerde query's uit over alle tenants.  |
|[Analyses uitvoeren op geëxtraheerde tenantgegevens](saas-tenancy-tenant-analytics.md) | Pisgegevens extraheren in een analysedatabase of gegevensmagazijn voor offline analytics-query's. |


## <a name="next-steps"></a>Volgende stappen

- [Algemene richtlijnen en tips bij het implementeren en gebruiken van het voorbeeld van de Wingtip Tickets SaaS-app](saas-tenancy-wingtip-app-guidance-tips.md)
- [De SaaS-toepassing wingtip implementeren](saas-dbpertenant-get-started-deploy.md)
