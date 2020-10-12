---
title: Aanbevolen procedures voor SQL op aanvraag (preview)
description: Aanbevelingen en aanbevolen procedures voor het werken met SQL op aanvraag (preview).
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 7bebfeba6da1493557d51777ba8438747e160750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476271"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Aanbevolen procedures voor SQL on-demand (preview) in azure Synapse Analytics

In dit artikel vindt u een verzameling aanbevolen procedures voor het gebruik van SQL on-demand (preview). SQL on demand is een resource in azure Synapse Analytics.

## <a name="general-considerations"></a>Algemene overwegingen

Met SQL on-Demand kunt u bestanden in uw Azure Storage-accounts opvragen. Het bevat geen lokale opslag-of opname mogelijkheden. Alle bestanden die de query doelen zijn, zijn dus extern voor SQL op aanvraag. Alles wat verband houdt met het lezen van bestanden uit de opslag kan gevolgen hebben voor de prestaties van query's.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Uw Azure-opslag account en SQL on-demand vinden

Als u de latentie wilt minimaliseren, gaat u naar uw Azure Storage-account en uw SQL on-demand-eind punt. Opslag accounts en eind punten die zijn ingericht tijdens het maken van de werk ruimte bevinden zich in dezelfde regio.

Voor optimale prestaties kunt u, als u toegang krijgt tot andere opslag accounts met SQL on-demand, ervoor zorgen dat ze zich in dezelfde regio bevinden. Als ze zich niet in dezelfde regio bevinden, wordt de latentie verhoogd voor de netwerk overdracht van de gegevens tussen de externe regio en de regio van het eind punt.

## <a name="azure-storage-throttling"></a>Azure Storage beperking

Meerdere toepassingen en services kunnen toegang krijgen tot uw opslag account. Opslag beperking treedt op wanneer de gecombineerde IOPS of door Voer die worden gegenereerd door toepassingen, services en SQL on-demand-werk belasting de limieten van het opslag account overschrijden. Als gevolg hiervan krijgt u een aanzienlijk negatief effect op de query prestaties.

Wanneer beperking wordt gedetecteerd, heeft SQL op aanvraag ingebouwde verwerking om het probleem op te lossen. Op aanvraag van SQL on-demand worden de opslag ruimte in een langzamer tempo opgeslagen totdat de beperking is opgelost.

> [!TIP]
> Voor een optimale uitvoering van query's kunt u het opslag account niet onder andere workloads belasten tijdens het uitvoeren van query's.

## <a name="prepare-files-for-querying"></a>Bestanden voorbereiden voor het uitvoeren van query's

Als dat mogelijk is, kunt u bestanden voorbereiden voor betere prestaties:

- Converteer CSV en JSON naar Parquet. Parquet is een kolom indeling. Omdat het gecomprimeerd is, zijn de bestands grootten kleiner dan CSV-of JSON-bestanden die dezelfde gegevens bevatten. SQL on-demand heeft minder tijd en minder opslag aanvragen nodig om het te lezen.
- Als een query is gericht op één groot bestand, kunt u deze in meerdere kleinere bestanden splitsen.
- Probeer de grootte van het CSV-bestand onder 10 GB te laten staan.
- Het is beter om even grote bestanden te hebben voor één OPENROWSET-pad of een externe tabel locatie.
- Partitioneer uw gegevens door partities op te slaan in verschillende mappen of bestands namen. Zie [functies filename en filepath gebruiken om specifieke partities te bereiken](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>Joker tekens pushen naar lagere niveaus in het pad

U kunt joker tekens in het pad gebruiken om [meerdere bestanden en mappen](query-data-storage.md#query-multiple-files-or-folders)op te vragen. SQL on-demand-lijst bestanden in uw opslag account, beginnend bij de eerste * met behulp van Storage API. Hiermee worden bestanden geëlimineerd die niet overeenkomen met het opgegeven pad. Het verminderen van de eerste lijst met bestanden kan de prestaties verbeteren als er veel bestanden zijn die overeenkomen met het opgegeven pad tot het eerste Joker teken.

## <a name="use-appropriate-data-types"></a>De juiste gegevens typen gebruiken

De gegevens typen die u in uw query gebruikt, zijn van invloed op de prestaties. U kunt betere prestaties krijgen als u deze richt lijnen volgt: 

- Gebruik de kleinste gegevens grootte die geschikt is voor de grootste mogelijke waarde.
  - Als de Maxi maal toegestane teken waarde 30 tekens lang is, gebruikt u een teken gegevens type van lengte 30.
  - Gebruik **char** of **NCHAR**als alle waarden van een teken kolom een vaste grootte hebben. Gebruik anders **varchar** of **nvarchar**.
  - Als de maximum waarde voor de kolom integer 500 is, gebruikt u **smallint** omdat het het kleinste gegevens type is dat deze waarde kan bevatten. In [dit artikel](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)vindt u bereiken met een geheel getal.
- Gebruik, indien mogelijk, **varchar** en **char** in plaats van **nvarchar** en **NCHAR**.
- Gebruik, indien mogelijk, gegevens typen op basis van een geheel getal. SORTEREN, samen voegen en groeperen op bewerkingen zijn sneller voltooid op gehele getallen dan bij teken gegevens.
- Als u schema-deferrometalie gebruikt, [controleert u de instelde gegevens typen](#check-inferred-data-types).

## <a name="check-inferred-data-types"></a>Instelde gegevens typen controleren

Met [schema-deinterferentie](query-parquet-files.md#automatic-schema-inference) kunt u snel query's schrijven en gegevens verkennen zonder dat u de bestands schema's kent. De kosten voor dit gemak zijn dat uitgestelde gegevens typen groter zijn dan de werkelijke gegevens typen. Dit gebeurt wanneer er onvoldoende gegevens aanwezig zijn in de bron bestanden om ervoor te zorgen dat het juiste gegevens type wordt gebruikt. Parquet-bestanden bevatten bijvoorbeeld geen meta gegevens over de maximale teken kolom lengte. SQL op aanvraag leidt er toe als varchar (8000).

U kunt [sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) gebruiken om de resulterende gegevens typen van uw query te controleren.

In het volgende voor beeld ziet u hoe u de verstelde gegevens typen kunt optimaliseren. Deze procedure wordt gebruikt om de uitgestelde gegevens typen weer te geven: 
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

Dit is de resultatenset:

|is_hidden|column_ordinal|naam|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|DATETIME2 (7)|8|
|0|3|passenger_count|int|4|

Nadat u de voorziene gegevens typen voor de query weet, kunt u de juiste gegevens typen opgeven:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>De functies filename en filepath gebruiken om specifieke partities te bereiken

Gegevens zijn vaak ingedeeld in partities. U kunt SQL op aanvraag een instructie geven om specifieke mappen en bestanden op te vragen. Als u dit doet, vermindert het aantal bestanden en de hoeveelheid gegevens die de query moet lezen en verwerken. Een extra bonus is dat u betere prestaties krijgt.

Meer informatie over de functies [filename](query-data-storage.md#filename-function) en [filepath](query-data-storage.md#filepath-function) vindt u in de voor beelden voor het [uitvoeren van query's op specifieke bestanden](query-specific-files.md).

> [!TIP]
> Converteer de resultaten van het bestandspad en de bestands namen altijd naar de juiste gegevens typen. Als u teken gegevens typen gebruikt, moet u ervoor zorgen dat u de juiste lengte gebruikt.

> [!NOTE]
> Functies die worden gebruikt voor de partitie-eliminatie, het bestandspad en de bestands naam, worden momenteel niet ondersteund voor externe tabellen, behalve die die automatisch worden gemaakt voor elke tabel die is gemaakt in Apache Spark voor Azure Synapse Analytics.

Als uw opgeslagen gegevens niet zijn gepartitioneerd, kunt u het partitioneren. Op die manier kunt u deze functies gebruiken om query's te optimaliseren die op deze bestanden zijn gericht. Wanneer u een [query uitvoert op gepartitioneerde Apache Spark voor Azure Synapse-tabellen](develop-storage-files-spark-tables.md) vanuit SQL op aanvraag, worden alleen de benodigde bestanden automatisch door de query gericht.

## <a name="use-parser_version-20-to-query-csv-files"></a>PARSER_VERSION 2,0 gebruiken voor het opvragen van CSV-bestanden

U kunt een met prestaties geoptimaliseerde parser gebruiken wanneer u CSV-bestanden opvraagt. Zie [PARSER_VERSION](develop-openrowset.md)voor meer informatie.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS gebruiken om de query prestaties te verbeteren en samen te voegen

[CETAS](develop-tables-cetas.md) is een van de belangrijkste functies die beschikbaar zijn in SQL on-demand. CETAS is een parallelle bewerking die meta gegevens van externe tabellen maakt en de selectie query resultaten exporteert naar een set bestanden in uw opslag account.

U kunt CETAS gebruiken voor het opslaan van veelgebruikte delen van query's, zoals gekoppelde referentie tabellen, naar een nieuwe set bestanden. U kunt vervolgens deel nemen aan deze afzonderlijke externe tabel in plaats van het herhalen van algemene samen voegingen in meerdere query's.

Omdat CETAS Parquet-bestanden genereert, worden er automatisch statistieken gemaakt wanneer de eerste query de externe tabel bedoelt, wat resulteert in betere prestaties.

## <a name="azure-ad-pass-through-performance"></a>Pass-Through Azure AD-prestaties

Met SQL on-Demand kunt u toegang krijgen tot bestanden in de opslag met behulp van de Pass-Through-of SAS-referenties van Azure Active Directory (Azure AD). U kunt tragere prestaties bieden met Azure AD Pass-Through dan met SAS.

Als u betere prestaties nodig hebt, kunt u het beste SAS-referenties gebruiken om toegang te krijgen tot opslag totdat Azure AD Pass-Through-prestaties zijn verbeterd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het artikel [over probleem oplossing](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor oplossingen voor veelvoorkomende problemen. Zie [Aanbevolen procedures voor SQL-groepen](best-practices-sql-pool.md) voor specifieke richt lijnen als u werkt met SQL-groepen in plaats van SQL op aanvraag.
