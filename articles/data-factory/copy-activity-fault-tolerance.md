---
title: Fouttolerantie van kopieeractiviteit in Azure Data Factory
description: Meer informatie over het toevoegen van fout tolerantie aan de Kopieer activiteit in Azure Data Factory door de niet-compatibele rijen over te slaan.
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
ms.openlocfilehash: 42c637839172dab09a8721a93a67785a748afd2f
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708899"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Fouttolerantie van kopieeractiviteit in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Huidige versie](copy-activity-fault-tolerance.md)

Met de Kopieer activiteit in Azure Data Factory kunt u op twee manieren incompatibele rijen verwerken bij het kopiëren van gegevens tussen de bron-en Sink-gegevens opslag:

- U kunt de Kopieer activiteit afbreken en mislukken wanneer incompatibele gegevens worden aangetroffen (standaard gedrag).
- U kunt door gaan met het kopiëren van alle gegevens door fout tolerantie toe te voegen en incompatibele gegevens rijen over te slaan. Daarnaast kunt u de niet-compatibele rijen registreren in Azure Blob-opslag of Azure Data Lake Store. U kunt vervolgens het logboek controleren om de oorzaak van de fout te achterhalen, de gegevens op de gegevens bron op te lossen en de Kopieer activiteit opnieuw uit te voeren.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
De Kopieer activiteit ondersteunt drie scenario's voor het detecteren, overs Laan en het vastleggen van incompatibele gegevens:

- **Incompatibiliteit tussen het type bron gegevens en het systeem eigen type Sink**. 

    Bijvoorbeeld: gegevens uit een CSV-bestand in de Blob-opslag kopiëren naar een SQL database met een schema definitie die drie typen kolommen van het type INT bevat. De rijen met het CSV-bestand die numerieke gegevens bevatten, zoals 123.456.789, worden naar de Sink Store gekopieerd. De rijen met niet-numerieke waarden, zoals 123.456, ABC worden echter gedetecteerd als incompatibel en worden overgeslagen.

- **Het aantal kolommen tussen de bron en de Sink komt niet overeen**.

    Bijvoorbeeld: gegevens uit een CSV-bestand in Blob Storage kopiëren naar een SQL database met een schema definitie die zes kolommen bevat. De rijen met het CSV-bestand die zes kolommen bevatten, worden naar de Sink-Store gekopieerd. De rijen met het CSV-bestand met meer dan zes kolommen worden gedetecteerd als incompatibel en worden overgeslagen.

- **Schending van primaire sleutel bij het schrijven naar SQL Server/Azure SQL database/Azure Cosmos DB**.

    Bijvoorbeeld: gegevens kopiëren van een SQL-Server naar een SQL database. Er wordt een primaire sleutel gedefinieerd in de Sink-SQL database, maar er is geen dergelijke primaire sleutel gedefinieerd in de SQL-bron server. De dubbele rijen die aanwezig zijn in de bron, kunnen niet naar de Sink worden gekopieerd. Met de Kopieer activiteit wordt alleen de eerste rij van de bron gegevens naar de Sink gekopieerd. De volgende bron rijen die de dubbele primaire-sleutel waarde bevatten, worden gedetecteerd als incompatibel en worden overgeslagen.

>[!NOTE]
>- Voor het laden van gegevens in SQL Data Warehouse met poly Base, configureert u de instellingen voor de systeem eigen fout tolerantie van poly Base door beleid voor afwijzen via '[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)' op te geven in de Kopieer activiteit. U kunt nog steeds niet-compatibele poly base-rijen omleiden naar BLOB of ADLS, zoals hieronder wordt weer gegeven.
>- Deze functie is niet van toepassing wanneer Kopieer activiteit is geconfigureerd voor het aanroepen van [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Deze functie is niet van toepassing wanneer Kopieer activiteit is geconfigureerd voor het aanroepen [van een opgeslagen procedure vanuit een SQL-Sink](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Configuratie
In het volgende voor beeld wordt een JSON-definitie geboden voor het overs laan van de niet-compatibele rijen in een Kopieer activiteit:

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

Eigenschap | Beschrijving | Toegestane waarden | Verplicht
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Hiermee wordt aangegeven of niet-compatibele rijen moeten worden overgeslagen tijdens het kopiëren of niet. | Waar<br/>False (standaard) | Nee
redirectIncompatibleRowSettings | Een groep eigenschappen die kan worden opgegeven wanneer u de niet-compatibele rijen wilt vastleggen. | &nbsp; | Nee
linkedServiceName | De gekoppelde service van [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) om het logboek op te slaan dat de overgeslagen rijen bevat. | De naam van een gekoppelde service van het type `AzureStorage` of `AzureDataLakeStore`, die verwijst naar het exemplaar dat u wilt gebruiken om het logboek bestand op te slaan. | Nee
Pad | Het pad naar het logboek bestand dat de overgeslagen rijen bevat. | Geef het pad op dat u wilt gebruiken om de niet-compatibele gegevens te registreren. Als u geen pad opgeeft, maakt de service een container voor u. | Nee

## <a name="monitor-skipped-rows"></a>Overgeslagen rijen bewaken
Nadat het uitvoeren van de Kopieer activiteit is voltooid, kunt u het aantal overgeslagen rijen zien in de uitvoer van de Kopieer activiteit:

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
Als u configureert om de niet-compatibele rijen te registreren, kunt u het logboek bestand vinden op het volgende pad: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

De logboek bestanden kunnen alleen de CSV-bestanden zijn. De oorspronkelijke gegevens die worden overgeslagen, worden indien nodig geregistreerd met een komma als kolom scheidings teken. We voegen nog twee meer kolommen "error code" en "ErrorMessage" toe aan de oorspronkelijke bron gegevens in het logboek bestand, waar u de hoofd oorzaak van de incompatibiliteit kunt zien. De fout code en ErrorMessage worden tussen dubbele aanhalings tekens geplaatst. 

Een voor beeld van de inhoud van het logboek bestand is als volgt:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen van de Kopieeractiviteit:

- [Overzicht kopieeractiviteit](copy-activity-overview.md)
- [Prestaties van Kopieer activiteit](copy-activity-performance.md)


