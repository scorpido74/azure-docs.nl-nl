---
title: Metagegevens en ACL's behouden met kopieeractiviteit in Azure Data Factory
description: Meer informatie over het bewaren van metagegevens en ACL's tijdens het kopiëren met kopieeractiviteit in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: b73cd73a18d286f221c7be2c624719e1d23d7c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153825"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Metagegevens en ACL's behouden met kopieeractiviteit in Azure Data Factory

Wanneer u Azure Data Factory activiteit kopieert om gegevens van bron naar gootsteen te kopiëren, u in de volgende scenario's ook de metagegevens en ACL's behouden.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>Metagegevens bewaren voor migratie van meer

Wanneer u gegevens migreert van het ene gegevensmeer naar het andere, waaronder [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob](connector-azure-blob-storage.md)en Azure Data Lake [Storage Gen2,](connector-azure-data-lake-storage.md)u ervoor kiezen om de bestandsmetagegevens samen met gegevens te bewaren.

Kopieeractiviteit ondersteunt het behouden van de volgende kenmerken tijdens het kopiëren van gegevens:

- **Alle door de klant opgegeven metagegevens** 
- En de volgende vijf data store `contentType` **ingebouwde systeemeigenschappen:**, `contentEncoding` `contentLanguage` `contentDisposition`(met uitzondering van Amazon S3), , , . `cacheControl`

Wanneer u bestanden as-is kopieert van Amazon S3/Azure Data Lake Storage Gen2/Azure Blob naar Azure Data Lake Storage Gen2/Azure Blob met binaire indeling, u de optie **Behouden** vinden op het tabblad **Activiteitsinstellingen** > **Settings** kopiëren voor het maken van activiteiten of de pagina **Instellingen** in Kopieergegevenshulpprogramma.

![Metagegevens voor het behouden van activiteiten kopiëren](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Hier is een voorbeeld van kopieeractiviteit `preserve`JSON configuratie (zie): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>ACL's behouden van Data Lake Storage Gen1/Gen2 tot Gen2

Wanneer u een upgrade uitvoert van Azure Data Lake Storage Gen1 naar Gen2 of gegevens kopieert tussen ADLS Gen2, u ervoor kiezen om de POSIX access control lists (ACL's) samen met gegevensbestanden te behouden. Zie [Toegangsbeheer in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) en [Toegangsbeheer in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md)voor meer informatie over toegangsbeheer.

Kopieeractiviteit ondersteunt het behouden van de volgende typen ACL's tijdens het kopiëren van gegevens. U een of meer typen selecteren:

- **ACL**: PoSIX-toegangscontrolelijsten kopiëren en bewaren op bestanden en mappen. Het kopieert de volledige bestaande ACL's van bron naar gootsteen. 
- **Eigenaar**: Kopieer en bewaar de eigenaar van bestanden en mappen. Super-user toegang tot sink Data Lake Storage Gen2 is vereist.
- **Groep**: Kopieer en bewaar de groep bestanden en mappen. Super-user toegang tot sink Data Lake Storage Gen2 of de eigenaar gebruiker (als de eigenaar gebruiker is ook een lid van de doelgroep) is vereist.

Als u opgeeft te kopiëren uit een map, repliceert Data Factory de ACL's voor die map en de bestanden en mappen eronder, als `recursive` deze is ingesteld op true. Als u opgeeft dat u uit één bestand wilt kopiëren, worden de ACL's in dat bestand gekopieerd.

>[!NOTE]
>Wanneer u ADF gebruikt om ACL's te behouden van Data Lake Storage Gen1/Gen2 tot Gen2, worden de bestaande ACL's op de bijbehorende map/bestanden van Sink Gen2 overschreven.

>[!IMPORTANT]
>Wanneer u ervoor kiest acl.n. te behouden, moet u ervoor zorgen dat u voldoende machtigingen verleent voor Data Factory om te werken tegen uw sink Data Lake Storage Gen2-account. Gebruik bijvoorbeeld accountsleutelverificatie of wijs de rol Storage Blob Data Owner toe aan de serviceprincipal of beheerde identiteit.

Wanneer u bron configureert als Data Lake Storage Gen1/Gen2 met binaire indeling of de binaire kopieeroptie en zinkt als Data Lake Storage Gen2 met binaire indeling of de binaire kopieoptie, u de optie **Behouden** vinden op de pagina **Instellingen** in Kopieergegevensgereedschap of op het tabblad **Activiteitsinstellingen** > **Settings** kopiëren voor activiteitenontwerpen.

![Data Lake Storage Gen1/Gen2 naar Gen2 Behouden ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Hier is een voorbeeld van kopieeractiviteit `preserve`JSON configuratie (zie): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
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
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
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

Zie de andere artikelen Copy Activity:

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Activiteitsprestaties kopiëren](copy-activity-performance.md)
