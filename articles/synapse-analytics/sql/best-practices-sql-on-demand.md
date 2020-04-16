---
title: Aanbevolen procedures voor SQL on-demand (preview) in Azure Synapse Analytics
description: Aanbevelingen en aanbevolen procedures die u moet kennen terwijl u met SQL on-demand werkt (preview).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429068"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Aanbevolen procedures voor SQL on-demand (preview) in Azure Synapse Analytics

In dit artikel vindt u een verzameling aanbevolen procedures voor het gebruik van SQL on-demand (preview). SQL on-demand is een extra bron binnen Azure Synapse Analytics.

## <a name="general-considerations"></a>Algemene overwegingen

Met SQL on-demand u bestanden opvragen in uw Azure-opslagaccounts. Het heeft geen lokale opslag- of opnamemogelijkheden. Als zodanig zijn alle bestanden die de querydoelen buiten SQL on-demand zijn. Alles met betrekking tot het lezen van bestanden uit de opslag kan een impact hebben op de queryprestaties.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure Storage-account en SQL on-demand colocate

Als u de latentie wilt minimaliseren, moet u uw Azure-opslagaccount en uw SQL-eindpunt voor on-demand co-lokaliseren. Opslagaccounts en eindpunten die tijdens het maken van werkruimtes zijn ingericht, bevinden zich in dezelfde regio.

Als u voor optimale prestaties toegang hebt tot andere opslagaccounts met SQL on-demand, controleert u of ze zich in dezelfde regio bevinden. Als ze zich niet in dezelfde regio bevinden, wordt de latentie verhoogd voor de netwerkoverdracht van de gegevens tussen de regio's van het afstandsbediening en eindpunt.

## <a name="azure-storage-throttling"></a>Beperking van Azure-opslag

Meerdere toepassingen en services hebben toegang tot uw opslagaccount. Opslagbeperking treedt op wanneer de gecombineerde IOPS- of doorvoergegenereerd door toepassingen, services en SQL on-demand workload de limieten van het opslagaccount overschrijdt. Als gevolg hiervan ervaart u een aanzienlijk negatief effect op de queryprestaties.

Zodra throttling is gedetecteerd, sql on-demand heeft ingebouwde behandeling van dit scenario. SQL on-demand zal aanvragen voor opslag in een langzamer tempo totdat throttling is opgelost.

> [!TIP]
> Voor een optimale queryuitvoering moet u het opslagaccount niet benadrukken met andere workloads tijdens queryuitvoering.

## <a name="prepare-files-for-querying"></a>Bestanden voorbereiden voor query's

Indien mogelijk u bestanden voorbereiden op betere prestaties:

- CSV converteren naar parket - Parket is kolomformaat. Omdat het is gecomprimeerd, zijn de bestandsgroottes kleiner dan CSV-bestanden met dezelfde gegevens. SQL on-demand heeft minder tijd en opslagaanvragen nodig om het te kunnen lezen.
- Als een query zich richt op één groot bestand, profiteert u ervan het bestand op te splitsen in meerdere kleinere bestanden.
- Probeer de grootte van uw CSV-bestand onder de 10 GB te houden.
- Het is beter om bestanden van gelijke grootte te hebben voor één OPENROWSET-pad of een externe tabelLOCATIE.
- Partitie van uw gegevens door partities op te slaan in verschillende mappen of bestandsnamen - controleer [bestandsnaam en bestandspadfuncties gebruiken om specifieke partities te targeten.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Bestandsinfo- en bestandspadfuncties gebruiken om specifieke partities te targeten

Gegevens worden vaak georganiseerd in partities. U SQL on-demand instrueren om bepaalde mappen en bestanden op te vragen. Deze functie vermindert het aantal bestanden en de hoeveelheid gegevens die de query moet lezen en verwerken. Een extra bonus is dat je betere prestaties te bereiken.

Voor meer informatie, controleer [bestandsnaam](develop-storage-files-overview.md#filename-function) en [bestandspad](develop-storage-files-overview.md#filepath-function) functies en voorbeelden over hoe [u specifieke bestanden opvraagt](query-specific-files.md).

Als uw opgeslagen gegevens niet zijn verdeeld, u deze partitioneren, zodat u deze functies gebruiken om query's te optimaliseren die zijn gericht op die bestanden. Wanneer de query partitiesparktabellen op SQL on-demand [opvraagt,](develop-storage-files-spark-tables.md) wordt de query automatisch alleen op de benodigde bestanden gericht.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CetAS gebruiken om de queryprestaties en joins te verbeteren

[CETAS](develop-tables-cetas.md) is een van de belangrijkste functies die beschikbaar zijn in SQL on-demand. CETAS is een parallelle bewerking die externe tabelmetagegevens maakt en de select-queryresultaten exporteert naar een set bestanden in uw opslagaccount.

U CETAS gebruiken om veelgebruikte delen van query's, zoals samengevoegde referentietabellen, op te slaan in een nieuwe set bestanden. Vervolgens u deelnemen aan deze ene externe tabel in plaats van gemeenschappelijke joins in meerdere query's te herhalen.

Aangezien CETAS parketbestanden genereert, worden statistieken automatisch gemaakt wanneer de eerste query zich richt op deze externe tabel, wat resulteert in betere prestaties.

## <a name="next-steps"></a>Volgende stappen

Bekijk het artikel [Probleemoplossing](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor veelvoorkomende problemen en oplossingen. Als u werkt met SQL-pool in plaats van SQL on-demand, raadpleegt u het [artikel Aanbevolen procedures voor SQL-pool](best-practices-sql-pool.md) voor specifieke richtlijnen.
