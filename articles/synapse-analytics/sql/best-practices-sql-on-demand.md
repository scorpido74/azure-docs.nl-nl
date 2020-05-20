---
title: Aanbevolen procedures voor SQL on-demand (preview) in azure Synapse Analytics
description: Aanbevelingen en aanbevolen procedures die u moet weten tijdens het werken met SQL op aanvraag (preview).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0792dd98bb554b7b430676472c27274f3312d381
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701363"
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

- Conversie van CSV en JSON naar Parquet-Parquet is kolom indeling. Omdat het gecomprimeerd is, zijn de bestands grootten kleiner dan CSV-of JSON-bestanden met dezelfde gegevens. SQL on-demand heeft minder tijd-en opslag aanvragen nodig om het te lezen.
- Als een query is gericht op één groot bestand, kunt u deze in meerdere kleinere bestanden splitsen.
- Bewaar de grootte van het CSV-bestand onder 10 GB.
- Het is beter om even grote bestanden te hebben voor één OPENROWSET-pad of een externe tabel locatie.
- Uw gegevens partitioneren door partities op te slaan in verschillende mappen of bestands namen. Controleer de [functies bestands naam en bestandspad gebruiken om specifieke partities te bereiken](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-path"></a>Joker tekens pushen naar lagere niveaus in het pad

U kunt joker tekens in het pad gebruiken om [meerdere bestanden en mappen](develop-storage-files-overview.md#query-multiple-files-or-folders)op te vragen. SQL op aanvraag geeft een lijst van bestanden in uw opslag account aan vanaf de eerste * met behulp van Storage API en worden bestanden geëlimineerd die niet overeenkomen met het opgegeven pad. Het verminderen van de eerste lijst met bestanden kan de prestaties verbeteren als er veel bestanden zijn die overeenkomen met het opgegeven pad tot het eerste Joker teken.

## <a name="use-appropriate-data-types"></a>De juiste gegevens typen gebruiken

De gegevens typen die u in de query gebruikt, zijn van invloed op de prestaties. U kunt betere prestaties krijgen als u: 

- Gebruik de kleinste gegevens grootte die geschikt is voor de grootste mogelijke waarde.
  - Gebruik het gegevens type Character van lengte 30 als de waarde voor de maximum lengte van tekens 30 tekens is.
  - Gebruik char of NCHAR als alle waarden van een teken kolom een vaste grootte hebben. Gebruik anders varchar of nvarchar.
  - Als het maximum aantal kolom waarden 500 is, gebruikt u smallint zoals het kleinste gegevens type is dat deze waarde kan bevatten. U kunt [hier](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)waarden voor het gegevens type geheel getal vinden.
- Gebruik, indien mogelijk, varchar en char in plaats van nvarchar en NCHAR.
- Gebruik, indien mogelijk, gegevens typen op basis van een geheel getal. Sorteren, samen voegen en groeperen op bewerkingen worden sneller uitgevoerd op gehele getallen dan op gegevens over tekens.
- Als u schema-deferrometalie gebruikt, [controleert u het uitgestelde gegevens type](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Instelde gegevens typen controleren

Met [schema-deinterferentie](query-parquet-files.md#automatic-schema-inference) kunt u snel query's schrijven en gegevens verkennen zonder dat u het bestands schema kent. Dit comfort is ten koste van de kosten van niet-uitgestelde gegevens typen die groter zijn dan werkelijk. Dit gebeurt wanneer er onvoldoende gegevens aanwezig zijn in de bron bestanden om ervoor te zorgen dat het juiste gegevens type wordt gebruikt. Parquet-bestanden bevatten bijvoorbeeld geen meta gegevens over de maximale teken kolom lengte en SQL op aanvraag wordt als varchar (8000) toegewezen. 

U kunt de resulterende gegevens typen van de query controleren met behulp van [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15).

In het volgende voor beeld ziet u hoe u de verstelde gegevens typen kunt optimaliseren. De procedure wordt gebruikt om instelde gegevens typen weer te geven. 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Dit is de resultatenset.

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|DATETIME2 (7)|8|
|0|3|passenger_count|int|4|

Zodra we de gegevens typen hebben vastgesteld voor de query, kunnen we de juiste gegevens typen opgeven:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>De functies file info en filepath gebruiken om specifieke partities te bereiken

Gegevens zijn vaak ingedeeld in partities. U kunt SQL op aanvraag een instructie geven om specifieke mappen en bestanden op te vragen. Met deze functie wordt het aantal bestanden en de hoeveelheid gegevens beperkt die de query moet lezen en verwerken. Een extra bonus is dat u betere prestaties krijgt.

Controleer voor meer informatie de functies [filename](develop-storage-files-overview.md#filename-function) en [filepath](develop-storage-files-overview.md#filepath-function) en voor beelden voor het [uitvoeren van query's op specifieke bestanden](query-specific-files.md).

> [!TIP]
> Converteer het resultaat van filepath-en file info-functies altijd naar de juiste gegevens typen. Als u teken gegevens typen gebruikt, moet u ervoor zorgen dat de juiste lengte wordt gebruikt.

> [!NOTE]
> Functies die worden gebruikt voor de partitie-eliminatie, filepath en file info, worden momenteel niet ondersteund voor externe tabellen die niet automatisch worden gemaakt voor elke tabel die is gemaakt in Apache Spark voor Azure Synapse Analytics.

Als uw opgeslagen gegevens niet zijn gepartitioneerd, kunt u de partities partitioneren zodat u de functies voor het optimaliseren van query's die zijn gericht op die bestanden. Bij het uitvoeren van een [query op gepartitioneerde Apache Spark voor Azure Synapse-tabellen](develop-storage-files-spark-tables.md) vanuit SQL op aanvraag, worden alleen de benodigde bestanden automatisch door de query gericht.

## <a name="use-parser_version-20-for-querying-csv-files"></a>PARSER_VERSION 2,0 gebruiken voor het uitvoeren van query's in CSV-bestanden

U kunt de geoptimaliseerde prestatie-parser gebruiken bij het uitvoeren van een query op CSV-bestanden. Raadpleeg [PARSER_VERSION](develop-openrowset.md) voor meer informatie.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS gebruiken om de query prestaties te verbeteren en samen te voegen

[CETAS](develop-tables-cetas.md) is een van de belangrijkste functies die beschikbaar zijn in SQL on-demand. CETAS is een parallelle bewerking die meta gegevens van externe tabellen maakt en de selectie query resultaten exporteert naar een set bestanden in uw opslag account.

U kunt CETAS gebruiken voor het opslaan van veelgebruikte delen van query's, zoals gekoppelde referentie tabellen, naar een nieuwe set bestanden. Vervolgens kunt u deel nemen aan deze afzonderlijke externe tabel in plaats van de algemene samen voegingen in meerdere query's te herhalen.

Omdat CETAS Parquet-bestanden genereert, worden er automatisch statistieken gemaakt wanneer de eerste query de externe tabel bedoelt, wat resulteert in betere prestaties.

## <a name="aad-pass-through-performance"></a>Prestaties van AAD Pass-Through

Met SQL on-Demand kunt u toegang krijgen tot bestanden in de opslag met AAD Pass-Through of SAS-referentie. Mogelijk ondervindt u tragere prestaties met AAD Pass-Through vergeleken met SAS. 

Als u betere prestaties nodig hebt, kunt u SAS-referenties gebruiken om toegang te krijgen tot opslag totdat AAD Pass-Through-prestaties zijn verbeterd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel [over probleem oplossing](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor veelvoorkomende problemen en oplossingen. Als u werkt met een SQL-pool in plaats van SQL op aanvraag, raadpleegt u de [Best Practices for SQL pool](best-practices-sql-pool.md) -artikelen voor specifieke richt lijnen.
