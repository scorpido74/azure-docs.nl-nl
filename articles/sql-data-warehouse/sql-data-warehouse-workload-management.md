---
title: Werklastbeheer
description: Richt lijnen voor het implementeren van workload Management in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 66edde9591d4491fa630772f99372f9901319b2f
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940584"
---
# <a name="what-is-workload-management"></a>Wat is werkbelasting beheer?

Het uitvoeren van gemengde werk belastingen kan problemen met bronnen op drukke systemen opleveren.  Oplossingen voor de oplossing van de klassieke gegevensopslag activiteiten (zoals het laden, transformeren en doorzoeken van gegevens) kunnen worden gebruikt om ervoor te zorgen dat er voldoende resources zijn om te voldoen aan de Sla's.  

Het isoleren van fysieke servers kan leiden tot een plek in de vorm van een infra structuur die te weinig wordt gebruikt, wordt overboekt of een status waarin caches worden uitgevoerd met hardware die wordt gestart en gestopt.  Een succesvol Management schema voor werk belasting beheert resources effectief, zorgt voor zeer efficiënt resource gebruik en maximaliseert het rendement op investeringen (ROI).

Een werk belasting van een Data Warehouse verwijst naar alle bewerkingen die in verband staan met een Data Warehouse. De diepte en breedte van deze onderdelen zijn afhankelijk van het niveau van de verval datum van het Data Warehouse.  De werk belasting van het Data Warehouse omvat: 
- Het hele proces van het laden van gegevens in het magazijn 
- Data Warehouse-analyse en-rapportage uitvoeren
- Gegevens in het Data Warehouse beheren 
- Gegevens exporteren uit het Data Warehouse

De capaciteit van de prestaties van een Data Warehouse wordt bepaald door de [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md).
- Zie [geheugen en gelijktijdigheids limieten] geheugen-gelijktijdigheids limits.md) als u de resources wilt weer geven die voor alle prestatie profielen zijn toegewezen.
- Als u de capaciteit wilt aanpassen, kunt u [omhoog of omlaag schalen](quickstart-scale-compute-portal.md).


## <a name="workload-management-concepts"></a>Concepten van workload Management
In het verleden heeft u de query prestaties op SQL Data Warehouse via [resource klassen](resource-classes-for-workload-management.md)beheerd.  Resource klassen die zijn toegestaan om geheugen toe te wijzen aan een query op basis van het lidmaatschap van de rol.  De primaire uitdaging met resource klassen is dat na de configuratie geen governance of de mogelijkheid is om de werk belasting te beheren.  

Als u bijvoorbeeld het lidmaatschap van een ad-hoc gebruikersrol wilt verlenen aan smallrc, mag die gebruiker 100% van het geheugen op het systeem gebruiken.  Met resource klassen is er geen manier om te reserveren en om ervoor te zorgen dat resources beschikbaar zijn voor kritieke werk belastingen.

Werkbelasting beheer op SQL Data Warehouse bestaat uit drie concepten op hoog niveau: [werk belasting classificatie](sql-data-warehouse-workload-classification.md), [urgentie van werk](sql-data-warehouse-workload-importance.md) belasting en isolatie van [werk belastingen](sql-data-warehouse-workload-isolation.md).  Deze mogelijkheden bieden u meer controle over de manier waarop uw werk belasting gebruikmaakt van systeem bronnen.

Classificatie van werk belasting is het concept van het toewijzen van een aanvraag aan een werkbelasting groep en het instellen van urgentie niveaus.  Deze toewijzing is historisch uitgevoerd via het lidmaatschap van de rol met behulp van [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Dit kan nu worden gedaan via de [werk belasting CLASSIFER maken](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  De classificatie mogelijkheid biedt een uitgebreidere set opties zoals label, sessie en tijd voor het classificeren van aanvragen.

De werkbelasting urgentie is van invloed op de volg orde waarin een aanvraag toegang krijgt tot resources.  Een aanvraag met een hogere prioriteit heeft in een systeem voor het eerst toegang tot resources.  De urgentie kan ook de bestelde toegang tot vergren delingen garanderen. 

Met werkbelasting isolatie worden resources voor een werkbelasting groep gereserveerd.  Resources die in een werkbelasting groep zijn gereserveerd, worden uitsluitend voor die werkbelasting groep bewaard om te worden uitgevoerd.  Met werkbelasting groepen kunt u ook de hoeveelheid resources definiëren die per aanvraag worden toegewezen, net als bij resource klassen.  Werkbelasting groepen bieden u de mogelijkheid om de hoeveelheid resources die een set aanvragen kan verbruiken, te reserveren of in te stellen.  Tot slot zijn werkbelasting groepen een mechanisme voor het Toep assen van regels, zoals querytime-out, op aanvragen.  


## <a name="next-steps"></a>Volgende stappen

- Zie [workload classificatie](sql-data-warehouse-workload-classification.md)voor meer informatie over de classificatie van werk belastingen.  
- Zie [isolatie van werk belasting](sql-data-warehouse-workload-isolation.md)voor meer informatie over isolatie van werk belastingen.  
- Zie urgentie van het [werk belasting](sql-data-warehouse-workload-importance.md)voor meer informatie over het belang van de werk belasting.  
- Zie voor meer informatie over bewaking van workload Management [werk belasting Beheerportal bewaking](sql-data-warehouse-workload-management-portal-monitor.md).  