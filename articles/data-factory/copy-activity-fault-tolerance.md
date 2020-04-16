---
title: Fouttolerantie van kopieeractiviteit in Azure Data Factory
description: Meer informatie over het toevoegen van fouttolerantie om activiteit in Azure Data Factory te kopiëren door de incompatibele rijen over te slaan.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: 766520fe44047eee76029adf8ee1683c7b8008a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417858"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Fouttolerantie van kopieeractiviteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Huidige versie](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De kopieeractiviteit in Azure Data Factory biedt u twee manieren om onverenigbare rijen te verwerken bij het kopiëren van gegevens tussen bron- en sinkgegevensopslag:

- U de kopieeractiviteit afbreken en niet mislukken wanneer er incompatibele gegevens worden aangetroffen (standaardgedrag).
- U alle gegevens blijven kopiëren door fouttolerantie toe te voegen en onverenigbare gegevensrijen over te slaan. Bovendien u de incompatibele rijen in Azure Blob-opslag of Azure Data Lake Store registreren. U vervolgens het logboek onderzoeken om de oorzaak van de fout te achterhalen, de gegevens op de gegevensbron op te lossen en de kopieeractiviteit opnieuw te proberen.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
Copy Activity ondersteunt drie scenario's voor het detecteren, overslaan en registreren van incompatibele gegevens:

- **Onverenigbaarheid tussen het brongegevenstype en het native type sink**. 

    Bijvoorbeeld: Kopieer gegevens uit een CSV-bestand in Blob-opslag naar een SQL-database met een schemadefinitie die drie INT-typekolommen bevat. De CSV-bestandsrijen die numerieke gegevens bevatten, zoals 123.456.789, worden met succes gekopieerd naar het gootsteenarchief. De rijen die niet-numerieke waarden bevatten, zoals 123.456, abc, worden echter gedetecteerd als onverenigbaar en worden overgeslagen.

- **Mismatch in het aantal kolommen tussen de bron en de gootsteen**.

    Bijvoorbeeld: Kopieer gegevens uit een CSV-bestand in Blob-opslag naar een SQL-database met een schemadefinitie die zes kolommen bevat. De CSV-bestandsrijen met zes kolommen worden met succes gekopieerd naar het gootsteenarchief. De CSV-bestandsrijen die meer dan zes kolommen bevatten, worden gedetecteerd als onverenigbaar en worden overgeslagen.

- **Primaire sleutelschending bij het schrijven naar SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Bijvoorbeeld: Kopieer gegevens van een SQL-server naar een SQL-database. Een primaire sleutel wordt gedefinieerd in de SINK SQL-database, maar een dergelijke primaire sleutel wordt niet gedefinieerd in de bron SQL-server. De gedupliceerde rijen die in de bron aanwezig zijn, kunnen niet naar de gootsteen worden gekopieerd. Kopieer activiteit kopieert alleen de eerste rij van de brongegevens in de gootsteen. De volgende bronrijen die de gedupliceerde primaire sleutelwaarde bevatten, worden gedetecteerd als onverenigbaar en worden overgeslagen.

>[!NOTE]
>- Voor het laden van gegevens in SQL Data Warehouse met PolyBase configureert u de native fouttolerantieinstellingen van PolyBase door het afwijsbeleid op te geven via "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" in kopieeractiviteit. U het omleiden van PolyBase-incompatibele rijen naar Blob of ADLS nog steeds als normaal inschakelen, zoals hieronder wordt weergegeven.
>- Deze functie is niet van toepassing wanneer kopieeractiviteit is geconfigureerd om [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)aan te roepen.
>- Deze functie is niet van toepassing wanneer kopieeractiviteit is geconfigureerd om een [opgeslagen procedure van een SQL-sink](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink)aan te roepen.

## <a name="configuration"></a>Configuratie
In het volgende voorbeeld wordt een JSON-definitie gegeven om het overslaan van de incompatibele rijen in Kopieeractiviteit te configureren:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Hiermee geeft u op of incompatibele rijen tijdens het kopiëren moeten worden overgeslagen of niet. | True<br/>False (standaard) | Nee
redirectIncompatibleRowSettings | Een groep eigenschappen die kan worden opgegeven wanneer u de incompatibele rijen wilt registreren. | &nbsp; | Nee
linkedServiceName | De gekoppelde service van [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) of Azure Data [Lake Store](connector-azure-data-lake-store.md#linked-service-properties) om het logboek op te slaan dat de overgeslagen rijen bevat. | De naam `AzureStorage` van `AzureDataLakeStore` een of type gekoppelde service, die verwijst naar de instantie die u wilt gebruiken om het logboekbestand op te slaan. | Nee
path | Het pad van het logboekbestand dat de overgeslagen rijen bevat. | Geef het pad op dat u wilt gebruiken om de incompatibele gegevens aan te melden. Als u geen pad opgeeft, maakt de service een container voor u. | Nee

## <a name="monitor-skipped-rows"></a>Overgeslagen rijen controleren
Nadat de kopieeractiviteit is voltooid, u het aantal overgeslagen rijen in de uitvoer van de kopieeractiviteit zien:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Als u configureert om de incompatibele rijen aan te `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`melden, u het logboekbestand op dit pad vinden:. 

De logbestanden kunnen alleen de csv-bestanden zijn. De oorspronkelijke gegevens die worden overgeslagen, worden indien nodig geregistreerd met komma als kolomscheidingsteken. We voegen nog twee kolommen ErrorCode en ErrorMessage toe aan de oorspronkelijke brongegevens in het logboekbestand, waar u de hoofdoorzaak van de onverenigbaarheid zien. De ErrorCode en ErrorMessage worden geciteerd door dubbele aanhalingstekens. 

Een voorbeeld van de inhoud van het logboekbestand is als volgt:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen Copy Activity:

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Prestaties van kopieeractiviteit](copy-activity-performance.md)


