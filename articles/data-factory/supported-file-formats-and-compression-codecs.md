---
title: Ondersteunde bestandsindelingen in Azure Data Factory
description: In dit onderwerp worden de bestandsindelingen en compressiecodes beschreven die worden ondersteund door op bestanden gebaseerde connectors in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: dbcfad3dd3db9f5c9431e07d85d77a77a10283c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419031"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Ondersteunde bestandsindelingen en compressiecodecs in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Dit artikel is van toepassing op de volgende connectors: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)en [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

U de [activiteit Kopiëren](copy-activity-overview.md) gebruiken om bestanden te kopiëren tussen twee gegevensarchieven op basis van bestanden, in welk geval de gegevens efficiënt worden gekopieerd zonder serialisatie of deserialisatie. 

Daarnaast u ook ontsmet ten te gekort of bestanden van een bepaald formaat te genereren. U bijvoorbeeld het volgende uitvoeren:

* Kopieer gegevens uit een on-premises SQL Server-database en schrijf naar Azure Data Lake Storage Gen2 in parketindeling.
* Kopieer bestanden in de CSV-indeling (text) vanuit een on-premises bestandssysteem en schrijf naar Azure Blob-opslag in Avro-indeling.
* Kopieer bestanden met rits en bestanden uit een on-premises bestandssysteem, decomprimeer ze on-the-fly en schrijf geëxtraheerde bestanden naar Azure Data Lake Storage Gen2.
* Kopieer gegevens in de CSV-indeling (Gzip compressed-text) vanuit Azure Blob-opslag en schrijf deze naar Azure SQL Database.
* Veel meer activiteiten die serialisatie/deserialisatie of compressie/decompressie vereisen.

## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen Copy Activity:

- [Overzicht van activiteit kopiëren](copy-activity-overview.md)
- [Prestaties van kopieeractiviteit](copy-activity-performance.md)
