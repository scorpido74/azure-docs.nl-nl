---
title: Aanbevolen procedures voor SQL on-demand (preview) in azure Synapse Analytics
description: Aanbevelingen en aanbevolen procedures die u moet weten tijdens het werken met SQL op aanvraag (preview).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429068"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Aanbevolen procedures voor SQL on-demand (preview) in azure Synapse Analytics

In dit artikel vindt u een verzameling aanbevolen procedures voor het gebruik van SQL on-demand (preview). SQL on-demand is een extra bron in azure Synapse Analytics.

## <a name="general-considerations"></a>Algemene overwegingen

Met SQL on-Demand kunt u bestanden in uw Azure Storage-accounts opvragen. Het bevat geen lokale opslag-of opname mogelijkheden. Als zodanig zijn alle bestanden waarvan de query doelen extern zijn, op aanvraag van SQL. Alles wat verband houdt met het lezen van bestanden uit de opslag kan gevolgen hebben voor de prestaties van query's.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure Storage account en SQL on-demand zoeken

Als u de latentie wilt minimaliseren, gaat u naar uw Azure Storage-account en uw SQL on-demand-eind punt. Opslag accounts en eind punten die zijn ingericht tijdens het maken van de werk ruimte bevinden zich in dezelfde regio.

Voor optimale prestaties kunt u, als u toegang krijgt tot andere opslag accounts met SQL on-demand, ervoor zorgen dat ze zich in dezelfde regio bevinden. Als ze zich niet in dezelfde regio bevinden, wordt de latentie verhoogd voor de netwerk overdracht van de gegevens tussen de regio's van het externe en het eind punt.

## <a name="azure-storage-throttling"></a>Azure Storage beperking

Meerdere toepassingen en services kunnen toegang krijgen tot uw opslag account. Opslag beperking treedt op wanneer de gecombineerde IOPS of door Voer die worden gegenereerd door toepassingen, services en SQL on-demand-werk belasting de limieten van het opslag account overschrijden. Als gevolg hiervan krijgt u een aanzienlijk negatief effect op de query prestaties.

Zodra de beperking is gedetecteerd, heeft SQL op aanvraag ingebouwde verwerking van dit scenario. Op aanvraag van SQL on-demand worden de opslag ruimte in een langzamer tempo opgeslagen totdat de beperking is opgelost.

> [!TIP]
> Voor een optimale uitvoering van query's moet u het opslag account met andere werk belastingen tijdens de uitvoering van de query raden.

## <a name="prepare-files-for-querying"></a>Bestanden voorbereiden voor het uitvoeren van query's

Als dat mogelijk is, kunt u bestanden voorbereiden voor betere prestaties:

- Conversie van CSV naar Parquet-Parquet is kolom indeling. Omdat het gecomprimeerd is, zijn de bestands grootten kleiner dan CSV-bestanden met dezelfde gegevens. SQL on-demand heeft minder tijd-en opslag aanvragen nodig om het te lezen.
- Als een query is gericht op één groot bestand, kunt u deze in meerdere kleinere bestanden splitsen.
- Bewaar de grootte van het CSV-bestand onder 10 GB.
- Het is beter om even grote bestanden te hebben voor één OPENROWSET-pad of een externe tabel locatie.
- Uw gegevens partitioneren door partities op te slaan in verschillende mappen of bestands namen. Controleer de [functies bestands naam en bestandspad gebruiken om specifieke partities te bereiken](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>De functies file info en filepath gebruiken om specifieke partities te bereiken

Gegevens zijn vaak ingedeeld in partities. U kunt SQL op aanvraag een instructie geven om specifieke mappen en bestanden op te vragen. Met deze functie wordt het aantal bestanden en de hoeveelheid gegevens beperkt die de query moet lezen en verwerken. Een extra bonus is dat u betere prestaties krijgt.

Controleer voor meer informatie de functies [filename](develop-storage-files-overview.md#filename-function) en [filepath](develop-storage-files-overview.md#filepath-function) en voor beelden voor het [uitvoeren van query's op specifieke bestanden](query-specific-files.md).

Als uw opgeslagen gegevens niet zijn gepartitioneerd, kunt u de partities partitioneren zodat u de functies voor het optimaliseren van query's die zijn gericht op die bestanden. Bij het uitvoeren van een [query op gepartitioneerde Spark-tabellen](develop-storage-files-spark-tables.md) vanuit SQL op aanvraag, worden alleen de benodigde bestanden automatisch door de query gericht.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS gebruiken om de query prestaties te verbeteren en samen te voegen

[CETAS](develop-tables-cetas.md) is een van de belangrijkste functies die beschikbaar zijn in SQL on-demand. CETAS is een parallelle bewerking die meta gegevens van externe tabellen maakt en de selectie query resultaten exporteert naar een set bestanden in uw opslag account.

U kunt CETAS gebruiken voor het opslaan van veelgebruikte delen van query's, zoals gekoppelde referentie tabellen, naar een nieuwe set bestanden. Vervolgens kunt u deel nemen aan deze afzonderlijke externe tabel in plaats van de algemene samen voegingen in meerdere query's te herhalen.

Omdat CETAS Parquet-bestanden genereert, worden er automatisch statistieken gemaakt wanneer de eerste query de externe tabel bedoelt, wat resulteert in betere prestaties.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel [over probleem oplossing](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor veelvoorkomende problemen en oplossingen. Als u werkt met een SQL-pool in plaats van SQL op aanvraag, raadpleegt u de [Best Practices for SQL pool](best-practices-sql-pool.md) -artikelen voor specifieke richt lijnen.
