---
title: Werklastbeheer
description: Richtlijnen voor het implementeren van workloadbeheer in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: dd867d4aa9a9ef5ed73e78a46826a8cd5239039b
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744234"
---
# <a name="what-is-workload-management"></a>Wat is workloadmanagement?

Het uitvoeren van gemengde workloads kan resourceuitdagingen opleveren op drukke systemen.  Solution Architects zoekt naar manieren om klassieke activiteiten voor gegevensopslag (zoals laden, transformeren en opvragen van gegevens) te scheiden om ervoor te zorgen dat er voldoende resources zijn om SLA's te raken.  

Fysieke serverisolatie kan leiden tot zakken met infrastructuur die onderbenut, overboekt of in een staat verkeren waarin caches voortdurend worden voorbereid met hardware die start en stopt.  Een succesvol workloadmanagementschema beheert effectief resources, zorgt voor een zeer efficiënt gebruik van resources en maximaliseert het rendement op investeringen (ROI).

Een werkbelasting voor een gegevensmagazijn verwijst naar alle bewerkingen die zich voordoen met betrekking tot een gegevensmagazijn. De diepte en breedte van deze componenten zijn afhankelijk van het volwassenheidsniveau van het datawarehouse.  De werkbelasting van het datawarehouse omvat:

- Het hele proces van het laden van gegevens in het magazijn
- Analyse en rapportage van datawarehouses uitvoeren
- Gegevens beheren in het datawarehouse
- Gegevens exporteren uit het datawarehouse

De prestatiecapaciteit van een datawarehouse wordt bepaald door de [datawarehouse-eenheden.](what-is-a-data-warehouse-unit-dwu-cdwu.md)

- Zie [Geheugen- en gelijktijdigheidslimieten](memory-concurrency-limits.md)voor alle prestatieprofielen om de resources te bekijken die zijn toegewezen aan alle prestatieprofielen.
- Als u de capaciteit wilt aanpassen, u [op- of omlaag schalen.](quickstart-scale-compute-portal.md)

## <a name="workload-management-concepts"></a>Concepten voor werkbelastingbeheer

In het verleden hebt u voor SQL Analytics in Azure Synapse de queryprestaties beheerd via [resourceklassen.](resource-classes-for-workload-management.md)  Resourceklassen toegestaan voor het toewijzen van geheugen aan een query op basis van het lidmaatschap van een rol.  De primaire uitdaging met resources klassen is dat, eenmaal geconfigureerd, was er geen governance of de mogelijkheid om de werkbelasting te controleren.  

Door bijvoorbeeld een ad-hoc gebruikersrollidmaatschap toe te kennen aan smallrc, kon die gebruiker 100% van het geheugen op het systeem verbruiken.  Met resourceklassen is er geen manier om resources te reserveren en ervoor te zorgen dat resources beschikbaar zijn voor kritieke workloads.

Synapse SQL-poolworkloadbeheer in Azure Synapse bestaat uit drie concepten op hoog niveau: [Workload Classification,](sql-data-warehouse-workload-classification.md) [Workload Importance](sql-data-warehouse-workload-importance.md) en Workload [Isolation](sql-data-warehouse-workload-isolation.md).  Deze mogelijkheden geven u meer controle over hoe uw werkbelasting gebruik maakt van systeembronnen.

Workloadclassificatie is het concept om een aanvraag toe te stellen aan een werkbelastinggroep en belangrijke niveaus in te stellen.  Historisch gezien werd deze opdracht gedaan via rollidmaatschap met behulp van [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Dit kan nu via de [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  De classificatiemogelijkheid biedt een uitgebreidere set opties, zoals label, sessie en tijd om aanvragen te classificeren.

Het werkgebied beïnvloedt de volgorde waarin een aanvraag toegang krijgt tot resources.  Op een bezet systeem heeft een aanvraag met een groter belang eerst toegang tot bronnen.  Belang kan ook zorgen voor geordende toegang tot sloten.

Workload isolation reserveert resources voor een workloadgroep.  Resources die zijn gereserveerd in een werkbelastinggroep, worden uitsluitend voor die werkbelastinggroep bewaard om de uitvoering te garanderen.  Met werkbelastinggroepen u ook de hoeveelheid resources definiëren die per aanvraag zijn toegewezen, net zoals resourceklassen dat doen.  Workloadgroepen geven u de mogelijkheid om de hoeveelheid resources te reserveren of te beperken die een set aanvragen kan verbruiken.  Ten slotte zijn werkbelastinggroepen een mechanisme om regels, zoals een time-out voor query's, toe te passen op aanvragen.  

## <a name="next-steps"></a>Volgende stappen

- Zie [Workloadclassificatie](sql-data-warehouse-workload-classification.md)voor meer informatie over workloadclassificatie.  
- Zie [Werkbelastingisolatie](sql-data-warehouse-workload-isolation.md)voor meer informatie over werkbelastingisolatie.  
- Zie [Werkbelastingbelang](sql-data-warehouse-workload-importance.md)voor meer informatie over het belang van de werkbelasting.  
- Zie Workload Management Portal Monitoring voor meer informatie over [workloadmanagementmonitoring.](sql-data-warehouse-workload-management-portal-monitor.md)  
