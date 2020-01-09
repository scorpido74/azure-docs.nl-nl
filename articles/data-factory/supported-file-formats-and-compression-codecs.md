---
title: Ondersteunde bestands indelingen in Azure Data Factory
description: Dit onderwerp beschrijft de bestandsindelingen en compressie-codes die worden ondersteund door de connectors op basis van bestanden in Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439575"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Ondersteunde indelingen en codecs voor de compressie in Azure Data Factory

*Dit artikel is van toepassing op de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

U kunt de [Kopieer activiteit](copy-activity-overview.md) gebruiken om bestanden te kopiëren tussen twee op bestanden gebaseerde gegevens archieven. in dat geval worden de gegevens efficiënt gekopieerd zonder serialisatie of deserialisatie. 

Daarnaast kunt u ook bestanden met een bepaalde indeling parseren of genereren. U kunt bijvoorbeeld het volgende doen:

* Gegevens kopiëren van een on-premises SQL Server Data Base en schrijven naar Azure Data Lake Storage Gen2 in de Parquet-indeling.
* Kopieer bestanden in de tekst indeling (CSV) van een on-premises bestands systeem en schrijf naar Azure Blob-opslag in de Avro-indeling.
* Kopieer gezipte bestanden van een on-premises bestands systeem, Decomprimeer ze op de vlucht en schrijf uitgepakte bestanden naar Azure Data Lake Storage Gen2.
* Kopieer gegevens in de indeling van de tekst in het type gzip (gecomprimeerd) in de vorm van Azure Blob-opslag en schrijf deze naar Azure SQL Database.
* Veel meer activiteiten waarvoor serialisatie/deserialisatie of compressie/decompressie nodig is.

## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen van de Kopieeractiviteit:

- [Overzicht kopieeractiviteit](copy-activity-overview.md)
- [Prestaties van Kopieer activiteit](copy-activity-performance.md)
