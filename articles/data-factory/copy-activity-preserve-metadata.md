---
title: Meta gegevens en Acl's behouden met Kopieer activiteit in Azure Data Factory
description: Meer informatie over het bewaren van meta gegevens en Acl's tijdens het kopiëren met de Kopieer activiteit in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260837"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Meta gegevens en Acl's behouden met Kopieer activiteit in Azure Data Factory

Wanneer u Azure Data Factory Kopieer activiteit gebruikt om gegevens te kopiëren van bron naar sink, kunt u in de volgende scenario's ook de meta gegevens en Acl's behouden.

## <a name="preserve-metadata"></a>Meta gegevens voor Lake Migration behouden

Wanneer u gegevens migreert van de ene data Lake naar een andere, met inbegrip van [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md)en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), kunt u ervoor kiezen om de meta gegevens van het bestand te behouden, samen met de gegevens.

De Kopieer activiteit biedt ondersteuning voor het behouden van de volgende kenmerken tijdens het kopiëren van de gegevens:

- **Alle door de klant opgegeven meta gegevens** 
- En de volgende **vijf ingebouwde systeem eigenschappen van het gegevens archief**: `contentType`, `contentLanguage` (behalve voor Amazon S3), `contentEncoding`, `contentDisposition``cacheControl`.

Wanneer u bestanden kopieert als-afkomstig van Amazon S3/Azure Data Lake Storage Gen2/Azure Blob naar Azure Data Lake Storage Gen2/Azure Blob met binaire indeling, kunt u de optie **behouden** vinden op het tabblad **Kopieer activiteit** > **instellingen** voor het ontwerpen van activiteiten of de pagina **instellingen** in gegevens kopiëren hulp programma.

![De meta gegevens van de Kopieer activiteit behouden](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Hier volgt een voor beeld van de JSON-configuratie van de Kopieer activiteit (Zie `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls"></a>Acl's van Data Lake Storage Gen1 op Gen2 bewaren

Wanneer u een upgrade uitvoert van Azure Data Lake Storage Gen1 naar Gen2, kunt u ervoor kiezen om de POSIX Access Control Lists (Acl's) samen met gegevens bestanden te bewaren. Zie voor meer informatie over toegangs beheer [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md) en [toegangs beheer in azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

De Kopieer activiteit ondersteunt het behouden van de volgende typen Acl's tijdens het kopiëren van de gegevens. U kunt een of meer typen selecteren:

- **ACL**: de POSIX-toegangs beheer lijsten voor bestanden en mappen kopiëren en bewaren. Hiermee worden de volledige bestaande Acl's van de bron naar de Sink gekopieerd. 
- **Eigenaar**: de gebruiker die eigenaar is van bestanden en mappen kopiëren en bewaren. Super gebruikers hebben toegang tot Sink Data Lake Storage Gen2 vereist.
- **Groep**: Kopieer en bewaar de groep die eigenaar is van bestanden en mappen. Super gebruikers hebben toegang tot Sink Data Lake Storage Gen2 of de gebruiker die eigenaar is (als de gebruiker die eigenaar is ook lid is van de doel groep) is vereist.

Als u opgeeft dat u wilt kopiëren vanuit een map, Data Factory repliceert de Acl's voor die betreffende map en de bestanden en mappen daaronder als `recursive` is ingesteld op True. Als u opgeeft dat u vanuit één bestand wilt kopiëren, worden de Acl's voor dat bestand gekopieerd.

>[!NOTE]
>Wanneer u ADF gebruikt voor het behouden van de Acl's van Data Lake Storage Gen1 naar Gen2, worden de bestaande Acl's op Gen2's bijbehorende map/bestanden overschreven.

>[!IMPORTANT]
>Wanneer u ervoor kiest om Acl's te behouden, moet u ervoor zorgen dat u Maxi maal voldoende machtigingen voor Data Factory verleent voor het uitvoeren van uw Sink Data Lake Storage Gen2-account. Gebruik bijvoorbeeld account sleutel verificatie of wijs de rol Storage BLOB data owner toe aan de service-principal of beheerde identiteit.

Wanneer u bron configureert als Data Lake Storage Gen1 met binaire indeling of de optie voor binaire kopieën, en u de optie Sink als Data Lake Storage Gen2 met binaire indeling of binaire kopie hebt ingesteld, kunt u de optie **behouden** vinden op de pagina **instellingen** in gegevens kopiëren hulp programma of op het tabblad **Kopieer activiteit** > **instellingen** voor het ontwerpen van een activiteit.

![Data Lake Storage Gen1 Gen2-ACL behouden](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Hier volgt een voor beeld van de JSON-configuratie van de Kopieer activiteit (Zie `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen van de Kopieeractiviteit:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Prestaties van Kopieer activiteit](copy-activity-performance.md)
