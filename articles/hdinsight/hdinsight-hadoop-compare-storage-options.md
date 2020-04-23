---
title: Opslagopties vergelijken voor gebruik met Azure HDInsight-clusters
description: Biedt een overzicht van opslagtypen en hoe ze werken met Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869847"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Opslagopties vergelijken voor gebruik met Azure HDInsight-clusters

U kiezen uit een aantal verschillende Azure-opslagservices bij het maken van HDInsight-clusters:

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

Dit artikel geeft een overzicht van deze opslagtypen en hun unieke functies.

## <a name="storage-types-and-features"></a>Opslagtypen en -functies

In de volgende tabel worden de Azure Storage-services samengevat die worden ondersteund met verschillende versies van HDInsight:

| Opslagservice | Accounttype | Type naamruimte | Ondersteunde services | Ondersteunde prestatielagen | Ondersteunde toegangsniveaus | HDInsight-versie | Clustertype |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| V2 voor algemene doeleinden | Hiërarchisch (bestandssysteem) | Blob | Standard | Hot, Cool, Archief | 3,6+ | Alle behalve Spark 2.1 en 2.2|
|Azure Storage| V2 voor algemene doeleinden | Object | Blob | Standard | Hot, Cool, Archief | 3,6+ | Alle |
|Azure Storage| V1 voor algemeen gebruik | Object | Blob | Standard | N.v.t. | Alle | Alle |
|Azure Storage| Blob-opslag** | Object | Blob blokkeren | Standard | Hot, Cool, Archief | Alle | Alle |
|Azure Data Lake Storage Gen1| N.v.t. | Hiërarchisch (bestandssysteem) | N.v.t. | N.v.t. | N.v.t. | 3.6 Alleen | Alles behalve HBase |

**Voor HDInsight-clusters kunnen alleen secundaire opslagaccounts van het type BlobStorage zijn en is Page Blob geen ondersteunde opslagoptie.

Zie Overzicht van [Azure Storage-account voor](../storage/common/storage-account-overview.md) meer informatie over Azure Storage-accounttypen

Zie [Azure Blob-opslag: Premium (voorbeeld), Hot, Cool en Archive storagelagen](../storage/blobs/storage-blob-storage-tiers.md) voor meer informatie over azure storage-toegangslagen.

U clusters maken met combinaties van services voor primaire en optionele secundaire opslag. In de volgende tabel worden de clusteropslagconfiguraties samengevat die momenteel worden ondersteund in HDInsight:

| HDInsight-versie | Primaire opslag | Secundaire opslag | Ondersteund |
|---|---|---|---|
| 3.6 & 4.0 | Algemeen doel V1, Algemeen Doel V2 | Algemene bestemming V1, Algemeen doel V2, BlobStorage (Blokblobs) | Ja |
| 3.6 & 4.0 | Algemeen doel V1, Algemeen Doel V2 | Data Lake Storage Gen2 | Nee |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | Ja |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Algemene bestemming V1, Algemeen doel V2, BlobStorage (Blokblobs) | Ja |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nee |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | Algemene bestemming V1, Algemeen doel V2, BlobStorage (Blokblobs) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nee |
| 4,0 | Data Lake Storage Gen1 | Alle | Nee |
| 4,0 | Algemeen doel V1, Algemeen Doel V2 | Data Lake Storage Gen1 | Nee |

*=Dit kunnen een of meerdere Data Lake Storage Gen2-accounts zijn, zolang ze allemaal zijn ingesteld om dezelfde beheerde identiteit te gebruiken voor clustertoegang.

> [!NOTE]
> Primaire opslag van Data Lake Storage Gen2 wordt niet ondersteund voor Spark 2.1- of 2.2-clusters.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Storage](./overview-azure-storage.md)
* [Overzicht van Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Overzicht van Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
