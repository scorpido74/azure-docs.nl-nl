---
title: Het Azure Blob Filesystem-stuurprogramma voor Azure Data Lake Storage Gen2
description: De ABFS Hadoop Filesystem driver
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3db039d39ef532ea51143dc9cbdb6bd5f29d6225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75970280"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Het Azure Blob Filesystem-stuurprogramma (ABFS): een speciaal Azure Storage-stuurprogramma voor Hadoop

Een van de primaire toegangsmethoden voor gegevens in Azure Data Lake Storage Gen2 is via het [Hadoop FileSystem.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html) Met Data Lake Storage Gen2 kunnen gebruikers van Azure Blob Storage toegang `ABFS`krijgen tot een nieuw stuurprogramma, het Azure Blob File System-stuurprogramma of . ABFS is onderdeel van Apache Hadoop en is opgenomen in veel van de commerciële distributies van Hadoop. Met dit stuurprogramma hebben veel toepassingen en frameworks toegang tot gegevens in Azure Blob Storage zonder dat er code is die expliciet verwijst naar Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Eerdere mogelijkheden: het Windows Azure Storage Blob-stuurprogramma

Het Windows Azure Storage Blob-stuurprogramma of [het WASB-stuurprogramma](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) biedt de oorspronkelijke ondersteuning voor Azure Blob Storage. Dit stuurprogramma heeft de complexe taak uitgevoerd om de semantiek van bestandssystemen (zoals vereist door de Hadoop FileSystem-interface) te toewijzen aan die van de interface met objectopslagstijl die wordt blootgesteld door Azure Blob Storage. Dit stuurprogramma blijft dit model ondersteunen en biedt hoge prestaties toegang tot gegevens die zijn opgeslagen in blobs, maar bevat een aanzienlijke hoeveelheid code die deze toewijzing uitvoert, waardoor het moeilijk te onderhouden is. Bovendien vereisen sommige bewerkingen, zoals [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) en [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) wanneer deze worden toegepast op mappen, het stuurprogramma een groot aantal bewerkingen uitvoeren (vanwege objectstores gebrek aan ondersteuning voor mappen), wat vaak leidt tot verminderde prestaties. De ABFS-driver is ontworpen om de inherente tekortkomingen van WASB te overwinnen.

## <a name="the-azure-blob-file-system-driver"></a>Het azure blob-bestandssysteemstuurprogramma

De [Azure Data Lake Storage REST-interface](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) is ontworpen om de semantiek van bestandssystemen via Azure Blob Storage te ondersteunen. Gezien het feit dat de Hadoop FileSystem is ook ontworpen om dezelfde semantiek te ondersteunen is er geen vereiste voor een complexe mapping in de driver. Het Azure Blob File System-stuurprogramma (of ABFS) is dus slechts een clientshim voor de REST API.

Er zijn echter enkele functies die het stuurprogramma nog moet uitvoeren:

### <a name="uri-scheme-to-reference-data"></a>URI-schema voor referentiegegevens

In overeenstemming met andere FileSystem-implementaties binnen Hadoop definieert de ABFS-driver zijn eigen URI-schema, zodat resources (mappen en bestanden) duidelijk kunnen worden aangepakt. Het URI-schema is gedocumenteerd in [Het gebruik van de Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md). De structuur van de URI is:`abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Met behulp van het bovenstaande URI-formaat kunnen standaard Hadoop-tools en frameworks worden gebruikt om naar deze bronnen te verwijzen:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Intern vertaalt het ABFS-stuurprogramma de resource(s) die in de URI is opgegeven naar bestanden en mappen en voert het met deze verwijzingen aan naar de Azure Data Lake Storage REST API.

### <a name="authentication"></a>Authentication

Het ABFS-stuurprogramma ondersteunt twee vormen van verificatie, zodat de Hadoop-toepassing veilig toegang heeft tot bronnen die zijn opgenomen in een account met Gegevensmeeropslag Gen2. De volledige details van de beschikbare verificatieschema's worden verstrekt in de [beveiligingshandleiding voor Azure Storage.](security-recommendations.md) Dit zijn:

- **Gedeelde sleutel:** Hierdoor hebben gebruikers toegang tot ALLE bronnen in het account. De sleutel wordt versleuteld en opgeslagen in hadoop configuratie.

- **Azure Active Directory OAuth-token voor drager:** Azure AD-tokens voor dragers worden door het stuurprogramma aangeschaft en vernieuwd met de identiteit van de eindgebruiker of een geconfigureerde serviceprincipal. Met behulp van dit verificatiemodel wordt alle toegang per gesprek geautoriseerd met behulp van de identiteit die is gekoppeld aan het meegeleverde token en geëvalueerd op basis van de toegewezen POSIX Access Control List (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 ondersteunt alleen Azure AD v1.0-eindpunten.

### <a name="configuration"></a>Configuratie

Alle configuratie voor het ABFS-stuurprogramma wordt opgeslagen in het <code>core-site.xml</code> configuratiebestand. Op Hadoop-distributies met [Ambari](https://ambari.apache.org/)kan de configuratie ook worden beheerd met behulp van de webportal of Ambari REST API.

Details van alle ondersteunde configuratie-items zijn gespecificeerd in de [officiële Hadoop documentatie](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Hadoop documentatie

De ABFS-chauffeur is volledig gedocumenteerd in de [officiële hadoopdocumentatie](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Volgende stappen

- [Een Azure Databricks-cluster maken](./data-lake-storage-quickstart-create-databricks-account.md)
- [URI van Azure Data Lake Storage Gen2 gebruiken](./data-lake-storage-introduction-abfs-uri.md)
