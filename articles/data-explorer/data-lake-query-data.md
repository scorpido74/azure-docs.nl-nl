---
title: Query's uitvoeren op gegevens in Azure Data Lake met behulp van Azure Data Explorer
description: Meer informatie over het opvragen van gegevens in Azure Data Lake met behulp van Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161746"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Query's uitvoeren op gegevens in Azure Data Lake met behulp van Azure Data Explorer

Azure Data Lake Storage is een zeer schaal bare en kosteneffectieve data Lake-oplossing voor big data-analyses. Het combineert de kracht van een hoogwaardig bestands systeem met grote schaal en economie, zodat u sneller inzicht krijgt in uw tijd. Data Lake Storage Gen2 breidt Azure-Blob Storage mogelijkheden uit en is geoptimaliseerd voor analytische werk belastingen.
 
Azure Data Explorer integreert met Azure Blob Storage en Azure Data Lake Storage (gen1 en Gen2), waardoor snel, in de cache geplaatste en geïndexeerde toegang tot gegevens in het Lake wordt geboden. U kunt gegevens in het Lake analyseren en doorzoeken zonder voorafgaande opname in azure Data Explorer. U kunt ook een query uitvoeren in opgenomen en niet-opgenomen systeem eigen Lake data tegelijk.  

> [!TIP]
> Voor de beste query prestaties is het opnemen van gegevens in azure Data Explorer vereist. De mogelijkheid om op te vragen externe gegevens zonder voorafgaande opname moet alleen worden gebruikt voor historische gegevens of gegevens die zelden worden opgevraagd. [Optimaliseer de prestaties van uw query in het Lake voor de](#optimize-your-query-performance) beste resultaten.
 

## <a name="create-an-external-table"></a>Een externe tabel maken

 > [!NOTE]
 > Opslag accounts die momenteel worden ondersteund, zijn Azure Blob Storage of Azure Data Lake Storage (gen1 en Gen2).

1. Gebruik de `.create external table` opdracht voor het maken van een externe tabel in azure Data Explorer. Aanvullende opdrachten voor externe tabellen, zoals `.show`, `.drop`en `.alter`, worden beschreven in de [opdrachten van externe tabellen](/azure/kusto/management/externaltables).

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
    > * Er worden betere prestaties verwacht met een gedetailleerdere partitionering. Query's over externe tabellen met dagelijkse partities hebben bijvoorbeeld betere prestaties dan die query's met maandelijkse gepartitioneerde tabellen.
    > * Wanneer u een externe tabel met partities definieert, wordt de opslag structuur naar verwachting identiek.
Als de tabel bijvoorbeeld is gedefinieerd met een DateTime-partitie in JJJJ/MM/DD-indeling (standaard), moet het pad naar het URI-opslag bestand *container1/jjjj/mm/dd/all_exported_blobs*zijn. 
    > * Als de externe tabel is gepartitioneerd met een datum/tijd-kolom, neemt altijd een tijd filter op voor een gesloten bereik in uw query (bijvoorbeeld: de query-`ArchivedProducts | where Timestamp between (ago(1h) .. 10m)`-moet beter worden uitgevoerd dan het geopende bereik) één `ArchivedProducts | where Timestamp > ago(1h)`). 
    > * Alle [ondersteunde opname-indelingen](ingest-data-overview.md#supported-data-formats) kunnen worden opgevraagd met behulp van externe tabellen.

1. De externe tabel is zichtbaar in het linkerdeel venster van de Web-UI

    ![externe tabel in de Web-UI](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Een externe tabel maken met de JSON-indeling

U kunt een externe tabel maken met de JSON-indeling. Zie voor meer informatie [externe tabel opdrachten](/azure/kusto/management/externaltables)

1. Gebruik de `.create external table` opdracht om een tabel met de naam *ExternalTableJson*te maken:

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
 
1. JSON-indeling vereist een tweede stap voor het maken van een toewijzing aan kolommen, zoals hieronder wordt weer gegeven. Maak in de volgende query een specifieke JSON-toewijzing met de naam *mapping*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Machtigingen voor externe tabellen
 
* De database gebruiker kan een externe tabel maken. De maker van de tabel wordt automatisch de tabel beheerder.
* De cluster-, data base-of tabel beheerder kan een bestaande tabel bewerken.
* Elke database gebruiker of Reader kan een query uitvoeren op een externe tabel.
 
## <a name="query-an-external-table"></a>Een query uitvoeren op een externe tabel
 
Als u een query wilt uitvoeren op een externe tabel, gebruikt u de functie `external_table()` en geeft u de tabel naam op als functie argument. De rest van de query is de standaard Kusto-query taal.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> IntelliSense wordt momenteel niet ondersteund voor externe tabel query's.

### <a name="query-an-external-table-with-json-format"></a>Een externe tabel met JSON-indeling opvragen

Als u een query wilt uitvoeren op een externe tabel met JSON-indeling, gebruikt u de functie `external_table()` en geeft u zowel de tabel naam als de naam van de toewijzing op als de functie argumenten. Als er in de onderstaande query geen *eigenaartoewijzing* is opgegeven, wordt een toewijzing die u eerder hebt gemaakt, gebruikt.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Een query uitvoeren op externe en opgenomen gegevens

U kunt zowel externe tabellen als opgenomen gegevens tabellen in dezelfde query opvragen. U [`join`](/azure/kusto/query/joinoperator) of [`union`](/azure/kusto/query/unionoperator) de externe tabel met aanvullende gegevens van Azure Data Explorer, SQL-servers of andere bronnen. Gebruik een [`let( ) statement`](/azure/kusto/query/letstatement) om een steno naam toe te wijzen aan een verwijzing naar een externe tabel.

In het onderstaande voor beeld is *Products* een opgenomen gegevens tabel en *ArchivedProducts* is een externe tabel met gegevens in de Azure data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>*TaxiRides* externe tabel in het Help-cluster opvragen

De set met *TaxiRides* -voorbeeld gegevens bevat nieuwe taxi's-gegevens van de Rotterdam-stad van [NYC en de limousine-Commissie](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Externe tabel *TaxiRides* maken 

> [!NOTE]
> In deze sectie ziet u de query die wordt gebruikt om de externe tabel *TaxiRides* in het *Help* -cluster te maken. Omdat deze tabel al is gemaakt, kunt u deze sectie overs Laan en [query *TaxiRides* externe tabel gegevens](#query-taxirides-external-table-data)uitvoeren. 

1. De volgende query is gebruikt om de externe tabel *TaxiRides* in het Help-cluster te maken. 

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
1. De resulterende tabel is gemaakt in het *Help* -cluster:

    ![Externe tabel TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>*TaxiRides* externe tabel gegevens opvragen 

Meld u aan bij [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) om de externe tabel *TaxiRides* op te vragen. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>*TaxiRides* externe tabel opvragen zonder partitioneren

[Voer deze query uit](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) op de externe tabel *TaxiRides* voor een voor beeld van de onderdrukkingen voor elke dag van de week, in de hele gegevensset. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Deze query toont de drukste dag van de week. Omdat de gegevens niet zijn gepartitioneerd, kan het lang duren om de resultaten te retour neren (Maxi maal enkele minuten).

![niet-gepartitioneerde query weer geven](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>TaxiRides externe tabel met partitioneren opvragen 

[Voer deze query uit](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) op de externe tabel *TaxiRides* met de taxi-cabinetbestanden (geel of groen) die zijn gebruikt in januari 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Deze query maakt gebruik van partitionering, waarmee de query tijd en prestaties worden geoptimaliseerd. De query filtert op een gepartitioneerde kolom (pickup_datetime) en resulteert in een paar seconden.

![gepartitioneerde query weer geven](media/data-lake-query-data/taxirides-with-partition.png)
  
U kunt extra query's schrijven om uit te voeren op de externe tabel *TaxiRides* en meer te weten te komen over de gegevens. 

## <a name="optimize-your-query-performance"></a>De query prestaties optimaliseren

Optimaliseer uw query prestaties in het Lake met behulp van de volgende aanbevolen procedures voor het uitvoeren van query's op externe gegevens. 
 
### <a name="data-format"></a>Gegevensindeling
 
Gebruik een kolom indeling voor analytische query's sinds:
* Alleen de kolommen die relevant zijn voor een query kunnen worden gelezen. 
* Kolom coderings technieken kunnen de gegevens omvang aanzienlijk verminderen.  
Azure Data Explorer ondersteunt Parquet-en ORC-notaties. De Parquet-indeling wordt voorgesteld vanwege een geoptimaliseerde implementatie. 
 
### <a name="azure-region"></a>Azure-regio 
 
Controleer of de externe gegevens zich in dezelfde Azure-regio bevinden als uw Azure Data Explorer-cluster. Dit reduceert de kosten en de ophaal tijd van de gegevens.
 
### <a name="file-size"></a>Bestandsgrootte
 
De optimale bestands grootte is honderden MB (Maxi maal 1 GB) per bestand. Vermijd veel kleine bestanden waarvoor overbodige overhead nodig is, zoals een langzamere inventarisatie proces en een beperkt gebruik van de kolom indeling. Houd er rekening mee dat het aantal bestanden groter moet zijn dan het aantal CPU-kernen in uw Azure Data Explorer-cluster. 
 
### <a name="compression"></a>Compressie
 
Gebruik compressie om de hoeveelheid gegevens die uit de externe opslag wordt opgehaald, te verminderen. Gebruik voor Parquet-indeling het interne Parquet-compressie mechanisme dat kolom groepen afzonderlijk comprimeert, zodat u deze afzonderlijk kunt lezen. Als u het gebruik van het compressie mechanisme wilt valideren, controleert u of de bestanden de volgende naam hebben: '<filename>. gz. Parquet ' of '<filename>. Snappy. Parquet ' in plaats van '<filename>. parquet. gz '. 
 
### <a name="partitioning"></a>Partitionering
 
Organiseer uw gegevens met behulp van ' map-' partities waarmee de query irrelevante paden kan overs Laan. Bij het plannen van partities kunt u de bestands grootte en algemene filters in uw query's zoals tijds tempel of Tenant-ID overwegen.
 
### <a name="vm-size"></a>VM-grootte
 
Selecteer VM-Sku's met meer kernen en een hogere netwerk doorvoer (geheugen is minder belang rijk). Zie voor meer informatie [de juiste VM-SKU voor uw Azure Data Explorer-cluster selecteren](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Volgende stappen

Een query uitvoeren op uw gegevens in het Azure Data Lake met behulp van Azure Data Explorer. Meer informatie over het [schrijven van query's](write-queries.md) en het afleiden van extra inzichten van uw gegevens.
