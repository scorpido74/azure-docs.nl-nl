---
title: Azure Data Lake Storage Gen2 overzicht in HDInsight
description: Overzicht van Data Lake Storage Gen2 in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873340"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Azure Data Lake Storage Gen2 overzicht in HDInsight

Azure Data Lake Storage Gen2 neemt kernfuncties over van Azure Data Lake Storage Gen1 en integreert deze in Azure Blob-opslag. Deze functies omvatten een bestandssysteem dat compatibel is met Hadoop, Azure Active Directory (Azure AD) en POSIX-gebaseerde toegangscontrolelijsten (ACL's). Met deze combinatie u profiteren van de prestaties van Azure Data Lake Storage Gen1. Terwijl u ook gebruik maakt van het tiering- en datalife-cyclebeheer van Blob-opslag.

Zie Inleiding tot Azure Data Lake Storage Gen2 voor meer informatie over Azure Data Lake Storage [Gen2.](../storage/blobs/data-lake-storage-introduction.md)

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Kernfunctionaliteit van Azure Data Lake Storage Gen2

* **Toegang die compatibel is met Hadoop:** In Azure Data Lake Storage Gen2 u gegevens beheren en openen, net zoals u dat zou doen met een Hadoop Distributed File System (HDFS). Het ABFS-stuurprogramma (Azure Blob File System) is beschikbaar binnen alle Apache Hadoop-omgevingen, inclusief Azure HDInsight en Azure Databricks. Gebruik ABFS om toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen2.

* **Een superset van POSIX-machtigingen:** Het beveiligingsmodel voor Data Lake Gen2 ondersteunt ACL- en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via beheertools of frameworks zoals Apache Hive en Apache Spark.

* **Kosteneffectiviteit:** Data Lake Storage Gen2 biedt goedkope opslagcapaciteit en transacties. De levenscyclus van Azure Blob-opslaghelpt de kosten te verlagen door de factureringspercentages aan te passen naarmate gegevens door de levenscyclus van de gegevens worden verplaatst.

* **Compatibiliteit met blob-opslaghulpprogramma's, frameworks en apps:** Data Lake Storage Gen2 blijft werken met een breed scala aan tools, frameworks en toepassingen voor Blob-opslag.

* **Geoptimaliseerde driver:** De ABFS driver is speciaal geoptimaliseerd voor big data analytics. De bijbehorende REST API's worden opgedoken via het DFS-eindpunt (distributed file system), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Nieuwe informatie voor Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Beheerde identiteiten voor beveiligde bestandstoegang

Azure HDInsight gebruikt beheerde identiteiten om clustertoegang tot bestanden in Azure Data Lake Storage Gen2 te beveiligen. Beheerde identiteiten zijn een functie van Azure Active Directory die Azure-services voorziet van een set automatisch beheerde referenties. Deze referenties kunnen worden gebruikt om te verifiëren voor elke service die Active Directory-verificatie ondersteunt. Als u beheerde identiteiten gebruikt, hoeft u referenties niet op te slaan in code- of configuratiebestanden.

Zie [Beheerde identiteiten voor Azure-bronnen voor](../active-directory/managed-identities-azure-resources/overview.md)meer informatie.

### <a name="azure-blob-file-system-driver"></a>Azure Blob-bestandssysteemstuurprogramma

Apache Hadoop-toepassingen verwachten native gegevens uit lokale schijfopslag te lezen en te schrijven. Met een Hadoop-bestandssysteemstuurprogramma zoals ABFS kunnen Hadoop-toepassingen werken met cloudopslag. Werkt door het emuleren van reguliere Hadoop bestandssysteem bewerkingen. Het stuurprogramma zet de opdrachten die van de toepassing zijn ontvangen om in bewerkingen die het werkelijke cloudopslagplatform begrijpt.

Voorheen heeft het hadoop-bestandssysteemstuurprogramma alle bestandssysteembewerkingen geconverteerd naar Azure Storage REST API-aanroepen aan de clientzijde. En vervolgens een beroep gedaan op de REST API. Deze client-side conversie resulteerde echter in meerdere REST API-oproepen voor één bestandssysteembewerking, zoals de naamswijziging van een bestand. ABFS heeft de hadoop-bestandssysteemlogica verplaatst van de clientkant naar de serverzijde. De Azure Data Lake Storage Gen2 API wordt nu parallel uitgevoerd met de Blob API. Deze migratie verbetert de prestaties omdat nu veelvoorkomende Hadoop-bestandssysteembewerkingen kunnen worden uitgevoerd met één REST API-aanroep.

Zie [Het Azure Blob Filesystem-stuurprogramma (ABFS): een speciaal Azure Storage-stuurprogramma voor Hadoop voor](../storage/blobs/data-lake-storage-abfs-driver.md)meer informatie.

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema voor Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen2 maakt gebruik van een nieuw URI-schema om toegang te krijgen tot bestanden in Azure Storage vanuit HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Het URI-schema biedt SSL-versleutelde toegang.

`<FILE_SYSTEM_NAME>`identificeert het pad van het bestandssysteem Data Lake Storage Gen2.

`<ACCOUNT_NAME>`hiermee wordt de naam van het Azure Storage-account geïdentificeerd. Een FQDN (Fully Qualified Domain Name) is vereist.

`<PATH>`is de naam van het bestand of de map HDFS-pad.

Als waarden `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` voor en niet zijn opgegeven, wordt het standaardbestandssysteem gebruikt. Gebruik voor de bestanden op het standaardbestandssysteem een relatief pad of een absoluut pad. Het `hadoop-mapreduce-examples.jar` bestand dat wordt geleverd met HDInsight-clusters kan bijvoorbeeld worden verwezen met behulp van een van de volgende paden:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> De bestandsnaam `hadoop-examples.jar` bevindt zich in HDInsight-versies 2.1 en 1.6 clusters. Wanneer u werkt met bestanden buiten HDInsight, zult u merken dat de meeste hulpprogramma's het ABFS-formaat `example/jars/hadoop-mapreduce-examples.jar`niet herkennen, maar in plaats daarvan een basispadindeling verwachten, zoals .

Zie [De Azure Data Lake Storage Gen2 URI gebruiken](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
* [Overzicht van Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)