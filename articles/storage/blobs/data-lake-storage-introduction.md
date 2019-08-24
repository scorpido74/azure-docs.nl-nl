---
title: Azure Data Lake Storage Gen2 Inleiding
description: Biedt een overzicht van Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: de2dc5068dc454925744688a43f49a855aac42f3
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991838"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Inleiding tot Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 is een set mogelijkheden die is toegewezen aan big data Analytics, gebouwd op [Azure Blob-opslag](storage-blobs-introduction.md). Data Lake Storage Gen2 is het resultaat van de mogelijkheden van onze twee bestaande storage-services, Azure-blobopslag en Azure Data Lake Storage Gen1 geconvergeerd. Functies van [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), zoals de semantiek van het bestandssysteem, map en Bestandsbeveiliging en schaal worden gecombineerd met lage kosten, gelaagde opslag, hoge beschikbaarheid/noodherstel herstelfuncties van [Azure Blob-opslag](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Ontworpen voor analyse van big data voor ondernemingen

Data Lake Storage Gen2 maakt Azure Storage de basis voor het enterprise datalakes bouwen op Azure. Data Lake Storage Gen2 kunt ontworpen vanaf het begin het afhandelen van meerdere petabytes aan gegevens terwijl de honderden gigabits van doorvoer, u enorme hoeveelheden gegevens eenvoudig te beheren.

Een fundamenteel onderdeel van Data Lake Storage Gen2 is de toevoeging van een [hiërarchische naamruimte](data-lake-storage-namespace.md) naar Blob-opslag. De hiërarchische naamruimte organiseert objecten/bestanden in een hiërarchie van mappen voor efficiënte toegang tot gegevens. Algemene naamconventie voor object store maakt gebruik van slashes in de naam om na te bootsen een hiërarchische mapstructuur. Deze structuur wordt echte met Data Lake Storage Gen2. Bewerkingen zoals het hernoemen of verwijderen van een directory worden één atomic metagegevens worden uitgevoerd op de map in plaats van met het inventariseren van en verwerken van alle objecten die delen van het voorvoegsel van de naam van de map.

In het verleden had cloudanalyses te boeten op het gebied van prestaties, beheer en beveiliging. Data Lake Storage Gen2 adressen elk van deze aspecten in de volgende manieren:

-   **Prestaties** is geoptimaliseerd omdat u niet nodig hebt om te kopiëren of transformatie van gegevens als een vereiste voor analyse. De hiërarchische naamruimte wordt aanzienlijk verbetert de prestaties van directory-management-bewerkingen, wat zorgt voor betere algehele prestaties van de taak.

-   **Management** is eenvoudiger omdat u kunt ordenen en manipuleren van bestanden door middel van mappen en submappen.

-   **Beveiliging** uitvoerbaar is omdat u POSIX-machtigingen op mappen of afzonderlijke bestanden kunt definiëren.

-   **Kosteneffectiviteit** wordt mogelijk gemaakt zoals Data Lake Storage Gen2 is gebaseerd op de lage kosten [Azure Blob-opslag](storage-blobs-introduction.md). De aanvullende functies nog verder verlagen de totale eigendomskosten voor het uitvoeren van big data-analyses op Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Belangrijke functies van Data Lake Storage Gen2

-   **Hadoop-compatibele toegang**: Met Data Lake Storage Gen2 kunt u gegevens beheren en openen, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). De nieuwe [ABFS stuurprogramma](data-lake-storage-abfs-driver.md) is beschikbaar in alle Apache Hadoop-omgevingen, met inbegrip van [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index), en [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) voor toegang tot gegevens die zijn opgeslagen in Data Lake Storage Gen2.

-   **Een superset van POSIX-machtigingen**: Het beveiligings model voor Data Lake Gen2 ondersteunt ACL-en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via Storage Explorer of frameworks, zoals Hive- en Spark.

-   **Kosten effectief**: Data Lake Storage Gen2 biedt goedkope opslag capaciteit en-trans acties. Als gegevens overgangen gedurende de volledige levensduur, factureringstarieven invloed op de bewaren kosten tot een minimum beperkt via de ingebouwde functies zoals [Azure Blob storage-levenscyclus](storage-lifecycle-management-concepts.md).

-   **Geoptimaliseerd stuur programma**: Het ABFS-stuur programma is [specifiek geoptimaliseerd](data-lake-storage-abfs-driver.md) voor Big Data Analytics. De bijbehorende REST Api's worden opgehaald via het eind punt `dfs.core.windows.net`.

### <a name="scalability"></a>Schaalbaarheid

Azure Storage is schaalbaar standaard of u toegang via Data Lake Storage Gen2 of Blob storage-interfaces hebt. Wordt uitgevoerd om te slaan en te gebruiken om *vele exabytes aan gegevens*. Deze hoeveelheid opslag is beschikbaar met doorvoer, gemeten in gigabits per seconde (Gbps) op hoog niveau van invoer/uitvoer-bewerkingen per seconde (IOPS). Verwerking wordt dan alleen persistentie uitgevoerd op in de buurt van constante per aanvraag latenties die worden gemeten op service-, account- en bestandsniveau.

### <a name="cost-effectiveness"></a>Kosteneffectiviteit

Een van de vele voordelen van het bouwen van Data Lake Storage Gen2 boven op Azure Blob-opslag is de lage kosten van opslagcapaciteit en transacties. In tegenstelling tot andere cloud storage services, zijn gegevens die zijn opgeslagen in Data Lake Storage Gen2 is niet vereist om te worden verplaatst of getransformeerd vóór het uitvoeren van analyses. Zie voor meer informatie over prijzen [prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Daarnaast functies zoals de [hiërarchische naamruimte](data-lake-storage-namespace.md) aanzienlijk verbeteren de algehele prestaties van veel analytics-taken. Deze verbetering in de prestaties van betekent dat u nodig minder rekenkracht voor het verwerken van de dezelfde hoeveelheid gegevens hebt, wat resulteert in een lagere totale eigendomskosten (TCO) voor de end-to-end-analytics-taak.

### <a name="one-service-multiple-concepts"></a>Een service, meerdere concepten

Data Lake Storage Gen2 is een aanvullende mogelijkheden voor analyse van big data, gebouwd op Azure Blob-opslag. Er zijn tal van voordelen in gebruik te maken van bestaande platformonderdelen van Blobs maken en gebruiken van datalakes voor analyses, dit leiden tot meerdere concepten met een beschrijving van de dingen die dezelfde, gedeeld.

Hier volgen de equivalente entiteiten, zoals beschreven door andere concepten. Tenzij anders opgegeven deze entiteiten rechtstreeks synoniem zijn:

| Concept                                | Bovenste niveau organisatie | Organisatie van lager niveau                                            | Gegevenscontainer |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOBs-opslag voor algemeen gebruik-object | Container              | Virtuele map (SDK alleen – geeft geen atomaire bewerking) | Blob           |
| ADLS Gen2 – Storage Analytics          | Container            | Directory                                                           | File           |

## <a name="supported-open-source-platforms"></a>Open source-platforms ondersteund

Data Lake Storage Gen2 ondersteuning voor verschillende open source-platforms. Deze platforms worden weergegeven in de volgende tabel.

> [!NOTE]
> Alleen de versies die worden weergegeven in deze tabel worden ondersteund.

| Platform |  Ondersteunde versie (s) | Meer informatie |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Wat zijn de Apache Hadoop-onderdelen en versies die beschikbaar met HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3,2 + | [Apache Hadoop-versies archiveren](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Opmerkingen bij de release van de Cloudera Enterprise 6.x](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 + | [Databricks Runtime-versies](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1. x + + | [Toegang tot Cloud gegevens configureren](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen beschreven enkele van de belangrijkste concepten van Data Lake Storage Gen2 en details over het opslaan, openen, beheren en haal praktische informatie uit uw gegevens:

-   [Hiërarchische naamruimte](data-lake-storage-namespace.md)
-   [Een opslagaccount maken](data-lake-storage-quickstart-create-account.md)
-   [Een Data Lake Storage Gen2-account in Azure Databricks gebruiken](data-lake-storage-quickstart-create-databricks-account.md)
