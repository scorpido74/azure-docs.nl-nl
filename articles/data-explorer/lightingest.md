---
title: LightIngest is een command-line hulpprogramma voor opname in Azure Data Explorer.
description: Meer informatie over LightIngest, een command-line hulpprogramma voor ad-hoc gegevensopname in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 99517e45892cd7a6167ae83ff3058edae1377b10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109561"
---
# <a name="install-and-use-lightingest"></a>LightIngest installeren en gebruiken

LightIngest is een command-line hulpprogramma voor ad-hoc gegevensopname in Azure Data Explorer.
Het hulpprogramma kan brongegevens ophalen uit een lokale map of uit een Azure blob-opslagcontainer.

## <a name="prerequisites"></a>Vereisten

* LightIngest - download het als onderdeel van het [Microsoft.Azure.Kusto.Tools NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)
* WinRAR - download het van [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>LightIngest installeren

1. Navigeer naar de locatie op uw computer waar u LightIngest hebt gedownload. 
1. Haal met WinRAR de *toolsdirectory* naar uw computer.

## <a name="run-lightingest"></a>Lichtingest uitvoeren

1. Navigeer naar de map uitgepakte *gereedschappen* op uw computer.
1. Verwijder de bestaande locatiegegevens van de locatiebalk.
    
      ![Locatiegegevens verwijderen](media/lightingest/lightingest-location-bar.png)

1. Voer `cmd` enter in en druk op **Enter**.
1. Voer bij de `LightIngest.exe` opdrachtprompt het argument van de relevante opdrachtregel in.

    > [!Tip]
    > Voer voor een lijst met ondersteunde `LightIngest.exe /help`opdrachtregelargumenten .
    >
    >![Help voor opdrachtregel](media/lightingest/lightingest-cmd-line-help.png)

1. Voer, `LightIngest` gevolgd door de verbindingstekenreeks, in naar het Azure Data Explorer-cluster dat de opname beheert.
    Sluit de verbindingstekenreeks in dubbele aanhalingstekens en volg de specificatie van de [Kusto-verbindingstekenreeksen](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto).

    Bijvoorbeeld:
    ```
    LightIngest "Data Source=https://{Cluster name and region}.kusto.windows.net;AAD Federated Security=True"  -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* De aanbevolen methode `LightIngest` is om te werken `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`met het innameeindpunt op . Op deze manier kan de Azure Data Explorer-service de innamebelasting beheren en u eenvoudig herstellen van tijdelijke fouten. U echter `LightIngest` ook configureren om direct met`https://{yourClusterNameAndRegion}.kusto.windows.net`het eindpunt van de motor te werken ( ).
* Voor optimale opnameprestaties is het belangrijk dat LightIngest de `LightIngest` ruwe gegevensgrootte kent en zo de niet-gecomprimeerde grootte van lokale bestanden inschat. Het `LightIngest` is echter mogelijk dat de ruwe grootte van gecomprimeerde blobs niet correct kan worden geschat zonder ze eerst te downloaden. Stel daarom bij het innemen van `rawSizeBytes` gecomprimeerde blobs de eigenschap op de blobmetagegevens in op niet-gecomprimeerde gegevensgrootte in bytes.

## <a name="general-command-line-arguments"></a>Algemene opdrachtregelargumenten

|Argumentnaam         |Korte naam   |Type    |Verplicht |Beschrijving                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |tekenreeks  |Verplicht |[Azure Data Explorer Connection String](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto) die het Kusto-eindpunt opgeeft waarmee de opname wordt verwerkt. Moet worden ingesloten in dubbele aanhalingstekens |
|-database             |-db          |tekenreeks  |Optioneel  |De naam van azure Data Explorer-database targeten |
|-tabel                |             |tekenreeks  |Verplicht |Tabelnaam Azure Data Explorer targeten |
|-sourcePath           |-bron      |tekenreeks  |Verplicht |Pad naar bronbestanden of root URI van de blobcontainer. Als de gegevens zich in blobs begeven, moet u de opslagaccountsleutel of SAS bevatten. Aanbevolen om dubbele aanhalingstekens in te sluiten |
|-voorvoegsel               |             |tekenreeks  |Optioneel  |Wanneer de brongegevens die moeten worden ingenomen zich in blobopslag bevinden, wordt dit URL-voorvoegsel gedeeld door alle blobs, met uitzondering van de containernaam. <br>Als de gegevens zich `MyContainer/Dir1/Dir2`bijvoorbeeld in de gegevens `Dir1/Dir2`begeven, moet het voorvoegsel zijn . Bijvoegen in dubbele aanhalingstekens wordt aanbevolen |
|-patroon              |             |tekenreeks  |Optioneel  |Patroon waarmee bronbestanden/blobs worden gekozen. Ondersteunt wildcards. Bijvoorbeeld `"*.csv"`. Aanbevolen om dubbele aanhalingstekens in te sluiten |
|-zipPatroon           |             |tekenreeks  |Optioneel  |Regelmatige expressie te gebruiken bij het selecteren van welke bestanden in een ZIP-archief in te nemen.<br>Alle andere bestanden in het archief worden genegeerd. Bijvoorbeeld. `"*.csv"` Het wordt aanbevolen om het te omringen in dubbele aanhalingstekens |
|-notatie               |-f           |tekenreeks  |Optioneel  |Brongegevensindeling. Moet een van de [ondersteunde formaten](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#supported-data-formats) zijn |
|-ingestionMappingPath |-mappingPath |tekenreeks  |Optioneel  |Pad naar inname kolom-mapping bestand (verplicht voor Json en Avro formaten). Bekijk [gegevenstoewijzingen](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-ingestionMappingRef  |-MappingRef  |tekenreeks  |Optioneel  |Naam van een vooraf gemaakte toewijzing van innamekolom (verplicht voor Json- en Avro-formaten). Bekijk [gegevenstoewijzingen](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-creationTimePattern  |             |tekenreeks  |Optioneel  |Wanneer ingesteld, wordt gebruikt om de eigenschap CreationTime uit het bestand of blobpad te halen. Zie [Argument CreationTimePattern gebruiken](#using-creationtimepattern-argument) |
|-ignoreFirstRow       |-ignoreFirst |Booleaanse waarde    |Optioneel  |Als deze is ingesteld, wordt de eerste record van elk bestand/blob genegeerd (bijvoorbeeld als de brongegevens kopteksten hebben) |
|-tag                  |             |tekenreeks  |Optioneel  |[Tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) om te associëren met de ingenomen gegevens. Meerdere gebeurtenissen zijn toegestaan |
|-dontWait             |             |Booleaanse waarde    |Optioneel  |Als ingesteld op 'waar', wacht dan niet op inname voltooiing. Handig bij het innemen van grote hoeveelheden bestanden/blobs |

### <a name="using-creationtimepattern-argument"></a>Argument CreationTimePattern gebruiken

Met `-creationTimePattern` het argument wordt de eigenschap CreationTime uit het bestand of blobpad gehaald. Het patroon hoeft niet het hele itempad weer te geven, alleen de sectie die de tijdstempel omsluit die u wilt gebruiken.
De waarde van het argument moet drie secties bevatten:
* Constante test onmiddellijk voorafgaand aan de tijdstempel, ingesloten in enkele aanhalingstekens
* De tijdstempelnotatie in standaard [.NET DateTime-notatie](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Constante tekst direct na de tijdstempel Als blobnamen bijvoorbeeld eindigen met 'historicalvalues19840101.parquet' (de tijdstempel is vier cijfers voor het jaar, twee `-creationTimePattern` cijfers voor de maand en twee cijfers voor de dag van de maand), is de overeenkomstige waarde voor het argument 'historicalvalues'yyyyMdd'.parket'.

### <a name="command-line-arguments-for-advanced-scenarios"></a>Opdrachtregelargumenten voor geavanceerde scenario's

|Argumentnaam         |Korte naam   |Type    |Verplicht |Beschrijving                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-compressie          |-cr          |double  |Optioneel  |Compressieverhouding hint. Handig bij het innemen van gecomprimeerde bestanden/blobs om Azure Data Explorer te helpen de ruwe gegevensgrootte te beoordelen. Berekend als oorspronkelijke grootte gedeeld door gecomprimeerde grootte |
|-limiet                |-l           |geheel getal |Optioneel  |Als u deze set stelt, beperkt u de opname tot de eerste N-bestanden |
|-listAlleen             |-lijst        |Booleaanse waarde    |Optioneel  |Als ingesteld, worden alleen de items weergegeven die zijn geselecteerd voor inname| 
|-innameTime-out        |             |geheel getal |Optioneel  |Time-out in minuten voor alle voltooiing van de innamebewerkingen. Standaardinstellingen voor`60`|
|-forceSync            |             |Booleaanse waarde    |Optioneel  |Als ingesteld, krachten synchrone inname. Standaardinstellingen voor`false` |
|-dataBatchSize        |             |geheel getal |Optioneel  |Hiermee stelt u de totale groottelimiet (MB, niet-gecomprimeerd) in van elke innamebewerking |
|-filesInBatch         |             |geheel getal |Optioneel  |Hiermee stelt u de limiet voor het aantal bestanden/blobs in van elke innamebewerking |
|-devTracing           |-trace       |tekenreeks  |Optioneel  |Als diagnostische logboeken zijn ingesteld, worden diagnostische `RollingLogs` logboeken naar een lokale map geschreven (standaard, in de huidige map of kunnen worden gewijzigd door de switchwaarde in te stellen) |

## <a name="blob-metadata-properties"></a>Eigenschappen van blobmeta's
Wanneer u wordt gebruikt `LightIngest` met Azure blobs, gebruikt u bepaalde eigenschappen van blobmetagegevens om het opnameproces te vergroten.

|Eigenschap Metagegevens                            | Gebruik                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Als ingesteld, wordt geïnterpreteerd als de niet-gecomprimeerde gegevensgrootte                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Geïnterpreteerd als UTC-tijdstempel. Als ingesteld, zal worden gebruikt om de creatie tijd in Kusto overschrijven. Handig voor navulscenario's |

## <a name="usage-examples"></a>Gebruiksvoorbeelden

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Blobs innemen met een opslagaccountsleutel of een SAS-token

* Inname van 10 blobs `ACCOUNT`onder `DIR`opgegeven opslagaccount , in map , onder container `CONT`en overeenkomen met het patroon`*.csv.gz`
* Bestemming is `DB`database, tabel `TABLE`en `MAPPING` de innametoewijzing is vooraf gemaakt op de bestemming
* De tool wacht tot de innamebewerkingen zijn voltooid
* Let op de verschillende opties voor het opgeven van de doeldatabase en opslagaccountsleutel vs. SAS-token

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Alle blobs in een container innemen, met geen koptekstrijen

* Alle blobs innemen onder `ACCOUNT`opgegeven `DIR1/DIR2`opslagaccount `CONT`, in map , onder container en overeenkomen met het patroon`*.csv.gz`
* Bestemming is `DB`database, tabel `TABLE`en `MAPPING` de innametoewijzing is vooraf gemaakt op de bestemming
* Bronblobs bevatten koptekstlijn, dus het gereedschap wordt geïnstrueerd om de eerste record van elke blob te laten vallen
* De tool plaatst de gegevens voor opname en wacht niet tot de innamebewerkingen zijn voltooid

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Alle JSON-bestanden van een pad innemen

* Alle bestanden onder `PATH`pad innemen, overeenkomend met het patroon`*.json`
* Doel is `DB`database, tabel `TABLE`en de innametoewijzing wordt gedefinieerd in lokaal bestand`MAPPING_FILE_PATH`
* De tool plaatst de gegevens voor opname en wacht niet tot de innamebewerkingen zijn voltooid

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Bestanden opnemen en diagnostische traceringsbestanden schrijven

* Alle bestanden onder `PATH`pad innemen, overeenkomend met het patroon`*.json`
* Doel is `DB`database, tabel `TABLE`en de innametoewijzing wordt gedefinieerd in lokaal bestand`MAPPING_FILE_PATH`
* De tool plaatst de gegevens voor opname en wacht niet tot de innamebewerkingen zijn voltooid
* Diagnostische traceringsbestanden worden lokaal geschreven onder map`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Changelog
|Versie        |Wijzigingen                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Toegevoegd `-zipPattern` argument</li><li>Toegevoegd `-listOnly` argument</li><li>Argumentensamenvatting wordt weergegeven voordat de run wordt gestart</li><li>CreationTime wordt gelezen uit blob metagegevens eigenschappen of `-creationTimePattern` van blob of bestandsnaam, volgens het argument</li></ul>|
