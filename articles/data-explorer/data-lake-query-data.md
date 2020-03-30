---
title: Querygegevens in Azure Data Lake met Azure Data Explorer
description: Meer informatie over het opvragen van gegevens in Azure Data Lake met Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161746"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Querygegevens in Azure Data Lake met Azure Data Explorer

Azure Data Lake Storage is een zeer schaalbare en kosteneffectieve data lake-oplossing voor big data-analyses. Het combineert de kracht van een high-performance bestandssysteem met enorme schaal en economie om u te helpen uw tijd tot inzicht te versnellen. Data Lake Storage Gen2 breidt azure blob-opslagmogelijkheden uit en is geoptimaliseerd voor analytics-workloads.
 
Azure Data Explorer integreert met Azure Blob Storage en Azure Data Lake Storage (Gen1 en Gen2), waardoor snel, in de cache en geïndexeerde toegang tot gegevens in het meer wordt geboden. U gegevens in het meer analyseren en opvragen zonder dat u eerder in Azure Data Explorer wordt opgenomen. U ook query over ingenomen en uningested native lake gegevens tegelijk.  

> [!TIP]
> De beste queryprestaties vereisen gegevensopname in Azure Data Explorer. De mogelijkheid om externe gegevens op te vragen zonder voorafgaande opname mag alleen worden gebruikt voor historische gegevens of gegevens die zelden worden opgevraagd. [Optimaliseer uw queryprestaties in het meer](#optimize-your-query-performance) voor de beste resultaten.
 

## <a name="create-an-external-table"></a>Een externe tabel maken

 > [!NOTE]
 > Momenteel ondersteunde opslagaccounts zijn Azure Blob Storage of Azure Data Lake Storage (Gen1 en Gen2).

1. Gebruik `.create external table` de opdracht om een externe tabel te maken in Azure Data Explorer. Aanvullende externe tabelopdrachten `.show`zoals `.drop`, `.alter` en worden gedocumenteerd in [opdrachten voor externe tabel](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * Er worden betere prestaties verwacht met meer gedetailleerde partitionering. Query's boven externe tabellen met dagelijkse partities hebben bijvoorbeeld betere prestaties dan query's met maandelijkse partitietabellen.
    > * Wanneer u een externe tabel met partities definieert, is de opslagstructuur naar verwachting identiek.
Als de tabel bijvoorbeeld is gedefinieerd met een DateTime-partitie in de yyyy/MM/dd-indeling (standaard), moet het URI-opslagbestandspad *container1/yyyy/MM/dd/all_exported_blobs*zijn. 
    > * Als de externe tabel wordt verdeeld door een kolom datetime, moet u altijd een tijdfilter `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` voor een gesloten bereik in uw `ArchivedProducts | where Timestamp > ago(1h)` query opnemen (bijvoorbeeld de query - - moet beter presteren dan dit (geopend bereik) één - ). 
    > * Alle [ondersteunde opname-indelingen](ingest-data-overview.md#supported-data-formats) kunnen worden opgevraagd met behulp van externe tabellen.

1. De externe tabel is zichtbaar in het linkerdeelvenster van de webgebruikersinterface

    ![externe tabel in web-gebruikersinterface](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Een externe tabel maken met json-indeling

U een externe tabel maken met json-indeling. Zie Opdrachten [voor externe tabel](/azure/kusto/management/externaltables)

1. Gebruik `.create external table` de opdracht om een tabel met de naam *ExternalTableJson*te maken:

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Json-indeling vereist een tweede stap van het maken van toewijzing aan kolommen zoals hieronder weergegeven. Maak in de volgende query een specifieke json-toewijzing met de naam *mappingName:*

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Machtigingen voor externe tabel
 
* De databasegebruiker kan een externe tabel maken. De tabelmaker wordt automatisch de tabelbeheerder.
* De cluster-, database- of tabelbeheerder kan een bestaande tabel bewerken.
* Elke databasegebruiker of -lezer kan een externe tabel opvragen.
 
## <a name="query-an-external-table"></a>Een externe tabel opvragen
 
Als u een externe `external_table()` tabel wilt opvragen, gebruikt u de functie en geeft u de tabelnaam op als het functieargument. De rest van de query is standaard Kusto query taal.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense wordt momenteel niet ondersteund op externe tabelquery's.

### <a name="query-an-external-table-with-json-format"></a>Een externe tabel met json-indeling opvragen

Als u een externe tabel met `external_table()` json-indeling wilt opvragen, gebruikt u de functie en geeft u zowel de tabelnaam als de toewijzingsnaam op als de functieargumenten. In de onderstaande query wordt een toewijzing gebruikt die u eerder hebt gemaakt als er geen *toewijzingsnaam* is opgegeven.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Externe en ingenomen gegevens samen opvragen

U zowel externe tabellen als ingenomen gegevenstabellen binnen dezelfde query opvragen. U [`join`](/azure/kusto/query/joinoperator) [`union`](/azure/kusto/query/unionoperator) of de externe tabel met aanvullende gegevens uit Azure Data Explorer, SQL-servers of andere bronnen. Gebruik [`let( ) statement`](/azure/kusto/query/letstatement) een a om een stenonaam toe te wijzen aan een externe tabelverwijzing.

In het onderstaande voorbeeld is *Producten* een opgenomen gegevenstabel en *ArchivedProducts* is een externe tabel die gegevens bevat in de Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Externe tabel *QueryTaxiRides* in het Help-cluster

De *TaxiRides* monster gegevens set bevat New York City taxi gegevens van [NYC Taxi en Limousine Commissie](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Externe tabel *TaxiRides maken* 

> [!NOTE]
> In deze sectie wordt de query weergegeven die wordt gebruikt om de externe tabel *TaxiRides* in het *helpcluster* te maken. Aangezien deze tabel al is gemaakt, u deze sectie overslaan en [externe tabelgegevens van *query's* ](#query-taxirides-external-table-data)uitvoeren. 

1. De volgende query is gebruikt om de externe tabel *TaxiRides* in het helpcluster te maken. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. De resulterende tabel is gemaakt in het *Helpcluster:*

    ![TaxiRides externe tabel](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Externe tabelgegevens *van Query TaxiRides* 

Meld u [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) aan om de externe *tabel TaxiRides* op te vragen. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Query *TaxiRides* externe tabel zonder partitionering

[Voer deze query](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) uit op de externe tabel *TaxiRides* om ritten voor elke dag van de week weer te geven, over de gehele gegevensset. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Deze query toont de drukste dag van de week. Aangezien de gegevens niet zijn verdeeld, kan het lang duren voordat deze query resultaten kan retourneren (tot enkele minuten).

![niet-gepartitioneerde query weergeven](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Query TaxiRides externe tabel met partitionering 

[Voer deze query](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) uit op de externe tabel *TaxiRides* met taxitypes (geel of groen) die in januari 2017 zijn gebruikt. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Deze query maakt gebruik van partitionering, wat de querytijd en -prestaties optimaliseert. De queryfilters op een partitiekolom (pickup_datetime) en retourneert resultaten in een paar seconden.

![partitiequery renderen](media/data-lake-query-data/taxirides-with-partition.png)
  
U aanvullende query's schrijven om te draaien op de externe tabel *TaxiRides* en meer informatie over de gegevens. 

## <a name="optimize-your-query-performance"></a>Uw queryprestaties optimaliseren

Optimaliseer uw queryprestaties in het meer met behulp van de volgende aanbevolen procedures voor het opvragen van externe gegevens. 
 
### <a name="data-format"></a>Gegevensindeling
 
Gebruik een kolomindeling voor analytische query's, omdat:
* Alleen de kolommen die relevant zijn voor een query kunnen worden gelezen. 
* Kolomcoderingstechnieken kunnen de gegevensgrootte aanzienlijk verkleinen.  
Azure Data Explorer ondersteunt indelingen voor parquet- en ORC-kolomkolommen. Parket formaat wordt voorgesteld als gevolg van geoptimaliseerde implementatie. 
 
### <a name="azure-region"></a>Azure-regio 
 
Controleer of externe gegevens zich in hetzelfde Azure-gebied bevinden als uw Azure Data Explorer-cluster. Dit vermindert de kosten en de tijd van het ophalen van gegevens.
 
### <a name="file-size"></a>Bestandsgrootte
 
Optimale bestandsgrootte is honderden Mb (tot 1 Gb) per bestand. Vermijd veel kleine bestanden die onnodige overhead vereisen, zoals een langzamer bestandsopsommingsproces en beperkt gebruik van de kolomindeling. Houd er rekening mee dat het aantal bestanden groter moet zijn dan het aantal CPU-cores in uw Azure Data Explorer-cluster. 
 
### <a name="compression"></a>Compressie
 
Gebruik compressie om de hoeveelheid gegevens die wordt opgehaald uit de externe opslag te verminderen. Gebruik voor het parketformaat het interne parketcompressiemechanisme dat kolomgroepen afzonderlijk comprimeert, zodat u ze afzonderlijk lezen. Als u het gebruik van compressiemechanisme wilt valideren,<filename>controleert u of de<filename>bestanden als volgt worden genoemd:<filename>" .gz.parquet" of " .snappy.parquet" in tegenstelling tot " .parket.gz"). 
 
### <a name="partitioning"></a>Partitionering
 
Organiseer uw gegevens met behulp van 'map'-partities waarmee de query irrelevante paden kan overslaan. Bij het plannen van partitionering overweeg je bestandsgrootte en algemene filters in je query's, zoals tijdstempel of tenant-id.
 
### <a name="vm-size"></a>VM-grootte
 
Selecteer VM SKU's met meer cores en een hogere netwerkdoorvoer (geheugen is minder belangrijk). Zie Selecteer [de juiste VM SKU voor uw Azure Data Explorer-cluster voor](manage-cluster-choose-sku.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Queryuw gegevens in het Azure Data Lake met Azure Data Explorer. Leer [query's schrijven](write-queries.md) en haal aanvullende inzichten uit uw gegevens.
