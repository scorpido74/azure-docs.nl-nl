---
title: URI van Azure Data Lake Storage Gen2 gebruiken
description: URI van Azure Data Lake Storage Gen2 gebruiken
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855565"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>URI van Azure Data Lake Storage Gen2 gebruiken

Het [Hadoop Filesystem-stuurprogramma](https://www.aosabook.org/en/hdfs.html) dat compatibel is met Azure Data `abfs` Lake Storage Gen2 staat bekend onder de schema-id (Azure Blob File System). In overeenstemming met andere Hadoop Filesystem-stuurprogramma's maakt de ABFS-stuurprogramma gebruik van een URI-indeling om bestanden en mappen aan te pakken binnen een Account met Data Lake Storage Gen2.

## <a name="uri-syntax"></a>URI: SYNTAXIS

De syntaxis van URI voor Data Lake Storage Gen2 is afhankelijk van het feit of uw opslagaccount is ingesteld om Data Lake Storage Gen2 als standaardbestandssysteem te hebben.

Als het account dat geschikt is voor Gegevenslake storage Gen2 die u wilt **aangeven, niet is** ingesteld als het standaardbestandssysteem tijdens het maken van een account, is de syntaxis van de steno-URI:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Scheme identifier** `abfs` : Het protocol wordt gebruikt als de regeling identifier. U hebt de mogelijkheid om verbinding te maken met of zonder ssl-verbinding (Secure Socket Layer). Maak `abfss` verbinding met een beveiligde verbinding met de socketlaag.

2. **Bestandssysteem:** de bovenliggende locatie die de bestanden en mappen bevat. Dit is hetzelfde als containers in de Azure Storage Blobs-service.

3. **Accountnaam**: De naam die tijdens het maken aan uw opslagaccount wordt gegeven.

4. **Paden:** een voorwaartse`/`slash die wordt afgebakend ( ) weergave van de directorystructuur.

5. **Bestandsnaam:** de naam van het afzonderlijke bestand. Deze parameter is optioneel als u een map adressert.

Als het account dat u wilt aangeven echter is ingesteld als het standaardbestandssysteem tijdens het maken van een account, is de syntaxis van steno-URI:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Pad:** een voorwaartse`/`slash heeft de weergave van de mapstructuur afgebakend.

2. **Bestandsnaam:** de naam van het afzonderlijke bestand.


## <a name="next-steps"></a>Volgende stappen

- [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
