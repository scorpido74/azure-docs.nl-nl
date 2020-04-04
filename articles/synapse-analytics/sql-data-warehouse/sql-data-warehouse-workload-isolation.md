---
title: Isolatie van workloads
description: Richtlijnen voor het instellen van workloadisolatie met workloadgroepen in Azure Synapse Analytics.
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
ms.openlocfilehash: c3fcbf69e7dae14ccd2114a14c685b0443f70fef
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632442"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Celisolatie azure Synapse Analytics-werkbelastinggroep (voorbeeld)

In dit artikel wordt uitgelegd hoe werkbelastinggroepen kunnen worden gebruikt om werkbelastingisolatie te configureren, resources te bevatten en runtime-regels toe te passen voor queryuitvoering.

## <a name="workload-groups"></a>Werkbelastinggroepen

Workloadgroepen zijn containers voor een reeks aanvragen en vormen de basis voor de manier waarop workloadmanagement, inclusief workloadisolation, is geconfigureerd op een systeem.  Workloadgroepen worden gemaakt met de syntaxis [VAN DE WERKBELASTINGGROEP MAKEN.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Een eenvoudige configuratie van het werkbelastingbeheer kan gegevenstaken en gebruikersquery's beheren.  Een workloadgroep met `wgDataLoads` de naam definieert bijvoorbeeld workload-aspecten voor gegevens die in het systeem worden geladen. Een workloadgroep met `wgUserQueries` de naam definieert ook workloadaspecten voor gebruikers die query's uitvoeren om gegevens uit het systeem te lezen.

In de volgende secties wordt uitgelegd hoe werkbelastinggroepen de mogelijkheid bieden om isolatie, containment, definitie van resources en uitvoeringsregels te definiëren.

## <a name="workload-isolation"></a>Isolatie van workloads

Werkbelastingisolatie betekent dat resources uitsluitend zijn gereserveerd voor een workloadgroep.  Workloadisolatie wordt bereikt door de parameter MIN_PERCENTAGE_RESOURCE te configureren tot groter dan nul in de syntaxis [VAN DE WERKBELASTINGGROEP MAKEN.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Voor continue uitvoeringsworkloads die moeten voldoen aan krappe SLA's, zorgt isolatie ervoor dat resources altijd beschikbaar zijn voor de workloadgroep.

Het configureren van workloadisolatie definieert impliciet een gegarandeerd niveau van gelijktijdigheid. Een werkbelastinggroep met `MIN_PERCENTAGE_RESOURCE` een ingesteld op `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 30% en ingesteld op 2% is bijvoorbeeld gegarandeerd 15 gelijktijdigheid.  Het niveau van gelijktijdigheid is gegarandeerd omdat 15-2% slots van resources te allen `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` tijde binnen de werkbelastinggroep zijn gereserveerd (ongeacht hoe is geconfigureerd).  Als `REQUEST_MAX_RESOURCE_GRANT_PERCENT` groter `REQUEST_MIN_RESOURCE_GRANT_PERCENT` is `CAP_PERCENTAGE_RESOURCE` dan `MIN_PERCENTAGE_RESOURCE` en groter is dan extra resources worden toegevoegd per aanvraag.  Als `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` en gelijk `CAP_PERCENTAGE_RESOURCE` zijn `MIN_PERCENTAGE_RESOURCE`en groter is dan , is extra gelijktijdigheid mogelijk.  Overweeg de onderstaande methode voor het bepalen van gegarandeerde gelijktijdigheid:

[Gegarandeerde gelijktijdigheid] =`MIN_PERCENTAGE_RESOURCE`[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> Er zijn specifieke serviceniveau minimale haalbare waarden voor min_percentage_resource.  Zie [Effectieve waarden](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) voor meer informatie voor meer informatie.

Bij afwezigheid van werkbelastingisolatie werken aanvragen in de [gedeelde groep](#shared-pool-resources) resources.  Toegang tot bronnen in de gedeelde groep is niet gegarandeerd en wordt toegewezen op basis [van belang.](sql-data-warehouse-workload-importance.md)

Het configureren van workloadisolatie moet met de nodige voorzichtigheid worden uitgevoerd, omdat de resources worden toegewezen aan de werkbelastinggroep, zelfs als er geen actieve aanvragen in de werkbelastinggroep zijn. Over-configureren isolatie kan leiden tot verminderde algehele systeemgebruik.

Gebruikers moeten voorkomen dat een oplossing voor werkbelastingbeheer wordt geconfigureerd die 100% workload isolation configureert: 100% isolatie wordt bereikt wanneer de som van min_percentage_resource geconfigureerd voor alle workloadgroepen 100% bedraagt.  Dit type configuratie is te restrictief en rigide, waardoor er weinig ruimte overblijft voor resourceaanvragen die per ongeluk verkeerd zijn geclassificeerd. Er is een bepaling die een aanvraag kan uitvoeren vanuit werkbelastinggroepen die niet zijn geconfigureerd voor isolatie. De middelen die aan dit verzoek worden toegewezen, worden weergegeven als een nul in de systemen DMVs en lenen een smallrc-niveau van middelensubsidie uit systeemgereserveerde resources.

> [!NOTE]
> Om een optimaal gebruik van resources te garanderen, overweeg dan een oplossing voor werkbelastingbeheer die gebruikmaakt van een isolatie om ervoor te zorgen dat SLA's worden nageleefd en worden gemengd met gedeelde resources die worden benaderd op basis van [het belang van de werkbelasting.](sql-data-warehouse-workload-importance.md)

## <a name="workload-containment"></a>Werkbelastinginsbeperking

Workloadcontainment verwijst naar het beperken van de hoeveelheid resources die een workloadgroep kan verbruiken.  Workloadcontainment wordt bereikt door de parameter CAP_PERCENTAGE_RESOURCE te configureren tot minder dan 100 in de syntaxis van de [werkbelastinggroep MAKEN.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Denk aan het scenario waarbij gebruikers leestoegang tot het systeem nodig hebben, zodat ze een wat-als-analyse kunnen uitvoeren via ad-hocquery's.  Dit soort aanvragen kan een negatief effect hebben op andere workloads die op het systeem worden uitgevoerd.  Het configureren van containment zorgt ervoor dat de hoeveelheid resources beperkt is.

Het configureren van workloadcontainment definieert impliciet een maximaal niveau van gelijktijdigheid.  Met een CAP_PERCENTAGE_RESOURCE ingesteld op 60% en een REQUEST_MIN_RESOURCE_GRANT_PERCENT ingesteld op 1%, is tot een niveau van 60 gelijktijdigheid toegestaan voor de werkbelastinggroep.  Overweeg de onderstaande methode voor het bepalen van de maximale gelijktijdigheid:

[Max Concurrency]`CAP_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> De effectieve CAP_PERCENTAGE_RESOURCE van een werkbelastinggroep zal niet 100% bereiken wanneer workloadgroepen met MIN_PERCENTAGE_RESOURCE op een niveau van meer dan nul worden gemaakt.  Zie [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) voor effectieve runtime-waarden.

## <a name="resources-per-request-definition"></a>Definitie van resources per aanvraag

Werkbelastinggroepen bieden een mechanisme om de min- en maximumhoeveelheid resources te definiëren die per aanvraag worden toegewezen met de REQUEST_MIN_RESOURCE_GRANT_PERCENT- en REQUEST_MAX_RESOURCE_GRANT_PERCENT parameters in de [syntaxis VAN DE WERKBELASTINGGROEP MAKEN.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Bronnen in dit geval zijn CPU en geheugen.  Het configureren van deze waarden bepaalt hoeveel resources en welk niveau van gelijktijdigheid kan worden bereikt op het systeem.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT is een optionele parameter die standaard wordt ingesteld op dezelfde waarde die is opgegeven voor REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Net als het kiezen van een resourceklasse, stelt u REQUEST_MIN_RESOURCE_GRANT_PERCENT de waarde in voor de resources die door een aanvraag worden gebruikt.  Het bedrag van de middelen aangegeven door de ingestelde waarde is gegarandeerd voor toewijzing aan de aanvraag voordat deze met de uitvoering begint.  Voor klanten die migreren van resourceklassen naar werkbelastinggroepen, u het artikel [How To](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) volgen om van resourcesklassen naar werkbelastinggroepen als uitgangspunt te brengen.

Door REQUEST_MAX_RESOURCE_GRANT_PERCENT te configureren naar een waarde die groter is dan REQUEST_MIN_RESOURCE_GRANT_PERCENT, kan het systeem meer resources per aanvraag toewijzen.  Tijdens het plannen van een aanvraag bepaalt het systeem de werkelijke toewijzing van resources aan de aanvraag, die tussen REQUEST_MIN_RESOURCE_GRANT_PERCENT en REQUEST_MAX_RESOURCE_GRANT_PERCENT ligt, op basis van de beschikbaarheid van resources in de gedeelde groep en de huidige belasting van het systeem.  De resources moeten bestaan in de [gedeelde groep](#shared-pool-resources) resources wanneer de query is gepland.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT en REQUEST_MAX_RESOURCE_GRANT_PERCENT hebben effectieve waarden die afhankelijk zijn van de effectieve MIN_PERCENTAGE_RESOURCE en CAP_PERCENTAGE_RESOURCE waarden.  Zie [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) voor effectieve runtime-waarden.

## <a name="execution-rules"></a>Uitvoeringsregels

Op ad-hocrapportagesystemen kunnen klanten per ongeluk op hol geslagen query's uitvoeren die de productiviteit van anderen ernstig beïnvloeden.  Systeembeheerders worden gedwongen om tijd te besteden aan het doden van op hol geslagen query's om systeembronnen vrij te maken.  Werkbelastinggroepen bieden de mogelijkheid om een time-outregel voor query-uitvoering te configureren om query's te annuleren die de opgegeven waarde hebben overschreden.  De regel wordt geconfigureerd `QUERY_EXECUTION_TIMEOUT_SEC` door de parameter in te stellen in de syntaxis VAN DE [WERKGROEP MAKEN.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="shared-pool-resources"></a>Bronnen voor gedeelde groep

Gedeelde groepbronnen zijn de resources die niet zijn geconfigureerd voor isolatie.  Werkbelastinggroepen met een MIN_PERCENTAGE_RESOURCE ingesteld op nul hefboombronnen in de gedeelde groep om aanvragen uit te voeren.  Workloadgroepen met een CAP_PERCENTAGE_RESOURCE groter dan MIN_PERCENTAGE_RESOURCE gebruikten ook gedeelde resources.  De hoeveelheid resources die beschikbaar zijn in de gedeelde groep wordt als volgt berekend.

[Gedeelde groep] = 100 `MIN_PERCENTAGE_RESOURCE` - [som van alle werkbelastinggroepen]

Toegang tot bronnen in de gedeelde pool wordt toegewezen op basis [van belang.](sql-data-warehouse-workload-importance.md)  Aanvragen met hetzelfde belangniveau hebben toegang tot gedeelde poolbronnen op een eerste in/first-outbasis.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: werkbelastingsisolatie configureren](quickstart-configure-workload-isolation-tsql.md)
- [WORKLOADGROEP MAKEN](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Resourceklassen converteren naar werkbelastinggroepen](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Monitoring van workloadmanagementportal .](sql-data-warehouse-workload-management-portal-monitor.md)  
