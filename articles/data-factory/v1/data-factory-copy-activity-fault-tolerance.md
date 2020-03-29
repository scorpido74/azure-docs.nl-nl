---
title: Fouttolerantie toevoegen in azure-kopieeractiviteit voor gegevensfabrieken door incompatibele rijen over te slaan
description: Meer informatie over het toevoegen van fouttolerantie in Azure Data Factory Copy Activity door incompatibele rijen over te slaan tijdens het kopiëren
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ffaee75154fd5fe025bdb683c89f16799d6e86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926156"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Fouttolerantie toevoegen in Activiteit kopiëren door incompatibele rijen over te slaan

> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-copy-activity-fault-tolerance.md)
> * [Versie 2 (huidige versie)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de gegevensfabriekservice gebruikt, raadpleegt u [fouttolerantie in kopieeractiviteit van Gegevensfabriek](../copy-activity-fault-tolerance.md).

Azure Data Factory [Copy Activity](data-factory-data-movement-activities.md) biedt u twee manieren om onverenigbare rijen te verwerken bij het kopiëren van gegevens tussen bron- en sinkgegevensopslag:

- U de kopieeractiviteit afbreken en niet mislukken wanneer er incompatibele gegevens worden aangetroffen (standaardgedrag).
- U alle gegevens blijven kopiëren door fouttolerantie toe te voegen en onverenigbare gegevensrijen over te slaan. Bovendien u de incompatibele rijen in Azure Blob-opslag registreren. U vervolgens het logboek onderzoeken om de oorzaak van de fout te achterhalen, de gegevens op de gegevensbron op te lossen en de kopieeractiviteit opnieuw te proberen.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
Copy Activity ondersteunt drie scenario's voor het detecteren, overslaan en registreren van incompatibele gegevens:

- **Incompatibiliteit tussen het gegevenstype van de bron en het systeemeigen gegevenstype van de sink**

    Bijvoorbeeld: Kopieer gegevens uit een CSV-bestand in Blob-opslag naar een SQL-database met een schemadefinitie die drie **INT-typekolommen** bevat. De CSV-bestandsrijen die numerieke `123,456,789` gegevens bevatten, zoals met succes worden gekopieerd naar het gootsteenarchief. De rijen die niet-numerieke waarden bevatten, zoals `123,456,abc` worden echter gedetecteerd als onverenigbaar en worden overgeslagen.

- **Het aantal kolommen tussen de bron en de sink komt niet overeen**

    Bijvoorbeeld: Kopieer gegevens uit een CSV-bestand in Blob-opslag naar een SQL-database met een schemadefinitie die zes kolommen bevat. De CSV-bestandsrijen met zes kolommen worden met succes gekopieerd naar het gootsteenarchief. De CSV-bestandsrijen die meer of minder dan zes kolommen bevatten, worden gedetecteerd als onverenigbaar en worden overgeslagen.

- **Schending van primaire sleutel bij het schrijven naar SQL Server/Azure SQL Database/Azure Cosmos DB**

    Bijvoorbeeld: Kopieer gegevens van een SQL-server naar een SQL-database. Een primaire sleutel wordt gedefinieerd in de SINK SQL-database, maar een dergelijke primaire sleutel wordt niet gedefinieerd in de bron SQL-server. De gedupliceerde rijen die in de bron aanwezig zijn, kunnen niet naar de gootsteen worden gekopieerd. Kopieer activiteit kopieert alleen de eerste rij van de brongegevens in de gootsteen. De volgende bronrijen die de gedupliceerde primaire sleutelwaarde bevatten, worden gedetecteerd als onverenigbaar en worden overgeslagen.

>[!NOTE]
>Deze functie is niet van toepassing wanneer kopieeractiviteit is geconfigureerd om een extern mechanisme voor het laden van gegevens aan te roepen, waaronder [Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) of Amazon [Redshift Unload.](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift) Voor het laden van gegevens in SQL Data Warehouse met PolyBase gebruikt u de native fault tolerance-ondersteuning van PolyBase door "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" op te geven in kopieeractiviteit.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Schakel het overslaan van onverenigbare rijen tijdens het kopiëren of niet in. | True<br/>False (standaard) | Nee |
| **redirectIncompatibleRowSettings** | Een groep eigenschappen die kan worden opgegeven wanneer u de incompatibele rijen wilt registreren. | &nbsp; | Nee |
| **linkedServiceName** | De gekoppelde service van Azure Storage om het logboek op te slaan dat de overgeslagen rijen bevat. | De naam van een [gekoppelde AzureStorage-](data-factory-azure-blob-connector.md#azure-storage-linked-service) of [AzureStorageSas-service,](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) die verwijst naar de opslaginstantie die u wilt gebruiken om het logboekbestand op te slaan. | Nee |
| **Pad** | Het pad van het logboekbestand dat de overgeslagen rijen bevat. | Geef het Blob-opslagpad op dat u wilt gebruiken om de incompatibele gegevens aan te melden. Als u geen pad opgeeft, maakt de service een container voor u. | Nee |

## <a name="monitoring"></a>Bewaking
Nadat de kopieeractiviteit is voltooid, u het aantal overgeslagen rijen in de controlesectie zien:

![Monitor overgeslagen incompatibele rijen](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Als u configureert om de incompatibele rijen aan te `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` melden, u het logboekbestand op dit pad vinden: in het logboekbestand u de rijen zien die zijn overgeslagen en de hoofdoorzaak van de onverenigbaarheid.

Zowel de oorspronkelijke gegevens als de bijbehorende fout worden in het bestand geregistreerd. Een voorbeeld van de inhoud van het logboekbestand is als volgt:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Volgende stappen
Zie [Gegevens verplaatsen met Kopieeractiviteit](data-factory-data-movement-activities.md)voor meer informatie over azure-kopieeractiviteit voor gegevens uit de fabriek .
