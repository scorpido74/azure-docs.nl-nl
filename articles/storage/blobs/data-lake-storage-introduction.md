---
title: Azure Data Lake Storage Gen2 Inleiding
description: Biedt een overzicht van Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 75bd27f0945c66b9757055c0777b43a050ba67d7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920991"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Inleiding tot Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 is een set mogelijkheden die is toegewezen aan big data Analytics, gebouwd op [Azure Blob-opslag](storage-blobs-introduction.md). Data Lake Storage Gen2 is het resultaat van de mogelijkheden van onze twee bestaande storage-services, Azure-blobopslag en Azure Data Lake Storage Gen1 geconvergeerd. Functies van [Azure data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/index), zoals de semantiek van het bestands systeem, map en bestands niveau beveiliging en schaal, worden gecombineerd met goedkope, gelaagde opslag, hoge Beschik baarheid/herstel na nood gevallen vanuit [Azure Blob-opslag](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Ontworpen voor analyse van big data voor ondernemingen

Data Lake Storage Gen2 maakt Azure Storage de basis voor het enterprise datalakes bouwen op Azure. Data Lake Storage Gen2 kunt ontworpen vanaf het begin het afhandelen van meerdere petabytes aan gegevens terwijl de honderden gigabits van doorvoer, u enorme hoeveelheden gegevens eenvoudig te beheren.

Een fundamenteel onderdeel van Data Lake Storage Gen2 is het toevoegen van een [hiërarchische naam ruimte](data-lake-storage-namespace.md) aan Blob Storage. De hiërarchische naamruimte organiseert objecten/bestanden in een hiërarchie van mappen voor efficiënte toegang tot gegevens. Algemene naamconventie voor object store maakt gebruik van slashes in de naam om na te bootsen een hiërarchische mapstructuur. Deze structuur wordt echte met Data Lake Storage Gen2. Bewerkingen zoals het hernoemen of verwijderen van een directory worden één atomic metagegevens worden uitgevoerd op de map in plaats van met het inventariseren van en verwerken van alle objecten die delen van het voorvoegsel van de naam van de map.

In het verleden had cloudanalyses te boeten op het gebied van prestaties, beheer en beveiliging. Data Lake Storage Gen2 adressen elk van deze aspecten in de volgende manieren:

-   De **prestaties** zijn geoptimaliseerd omdat u geen gegevens hoeft te kopiëren of transformeren als vereiste voor analyse. De hiërarchische naamruimte wordt aanzienlijk verbetert de prestaties van directory-management-bewerkingen, wat zorgt voor betere algehele prestaties van de taak.

-   **Beheer** is eenvoudiger omdat u bestanden kunt ordenen en bewerken via directory's en submappen.

-   **Beveiliging** is afdwingbaar omdat u POSIX-machtigingen kunt definiëren voor mappen of afzonderlijke bestanden.

-   De effectiviteit van de **kosten** wordt mogelijk gemaakt als data Lake Storage Gen2 is gebaseerd op de voordelige [Azure Blob-opslag](storage-blobs-introduction.md). De aanvullende functies nog verder verlagen de totale eigendomskosten voor het uitvoeren van big data-analyses op Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Belangrijke functies van Data Lake Storage Gen2

-   **Hadoop-compatibele toegang**: met data Lake Storage Gen2 kunt u gegevens beheren en openen, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Het nieuwe [ABFS-stuur programma](data-lake-storage-abfs-driver.md) is beschikbaar in alle Apache Hadoop omgevingen, waaronder [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)en [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) om toegang te krijgen tot gegevens die zijn opgeslagen in data Lake Storage Gen2.

-   **Een superset van POSIX-machtigingen**: het beveiligings model voor data Lake GEN2 ondersteunt ACL-en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via Storage Explorer of frameworks, zoals Hive- en Spark.

-   **Rendabel: data Lake Storage Gen2**biedt goedkope opslag capaciteit en-trans acties. Naarmate de volledige levens cyclus van gegevens overgaat, wijzigen de facturerings tarieven de kosten tot een minimum via ingebouwde functies zoals [Azure Blob Storage-levens cyclus](storage-lifecycle-management-concepts.md).

-   **Geoptimaliseerd stuur programma**: het ABFS-stuur programma is speciaal voor Big Data Analytics [geoptimaliseerd](data-lake-storage-abfs-driver.md) . De bijbehorende REST Api's worden geoppereerd via het eindpunt `dfs.core.windows.net`.

### <a name="scalability"></a>Schaalbaarheid

Azure Storage is schaalbaar standaard of u toegang via Data Lake Storage Gen2 of Blob storage-interfaces hebt. Het kan *veel Exabyte aan gegevens*opslaan en verwerken. Deze hoeveelheid opslag is beschikbaar met doorvoer, gemeten in gigabits per seconde (Gbps) op hoog niveau van invoer/uitvoer-bewerkingen per seconde (IOPS). Verwerking wordt dan alleen persistentie uitgevoerd op in de buurt van constante per aanvraag latenties die worden gemeten op service-, account- en bestandsniveau.

### <a name="cost-effectiveness"></a>Kosteneffectiviteit

Een van de vele voordelen van het bouwen van Data Lake Storage Gen2 boven op Azure Blob-opslag is de lage kosten van opslagcapaciteit en transacties. In tegenstelling tot andere cloud storage services, zijn gegevens die zijn opgeslagen in Data Lake Storage Gen2 is niet vereist om te worden verplaatst of getransformeerd vóór het uitvoeren van analyses. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage)voor meer informatie over prijzen.

Daarnaast verbeteren functies zoals de [hiërarchische naam ruimte](data-lake-storage-namespace.md) de algehele prestaties van veel analyse taken aanzienlijk. Deze verbetering in de prestaties van betekent dat u nodig minder rekenkracht voor het verwerken van de dezelfde hoeveelheid gegevens hebt, wat resulteert in een lagere totale eigendomskosten (TCO) voor de end-to-end-analytics-taak.

### <a name="one-service-multiple-concepts"></a>Een service, meerdere concepten

Data Lake Storage Gen2 is een aanvullende mogelijkheden voor analyse van big data, gebouwd op Azure Blob-opslag. Er zijn tal van voordelen in gebruik te maken van bestaande platformonderdelen van Blobs maken en gebruiken van datalakes voor analyses, dit leiden tot meerdere concepten met een beschrijving van de dingen die dezelfde, gedeeld.

Hier volgen de equivalente entiteiten, zoals beschreven door andere concepten. Tenzij anders opgegeven deze entiteiten rechtstreeks synoniem zijn:

| Concept                                | Bovenste niveau organisatie | Organisatie van lager niveau                                            | Gegevenscontainer |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOBs-opslag voor algemeen gebruik-object | Container              | Virtuele map (SDK alleen – geeft geen atomaire bewerking) | Blob           |
| Azure Data Lake Storage Gen2-Analytics-opslag          | Container            | Directory                                                           | File           |

## <a name="supported-blob-storage-features"></a>Ondersteunde functies van Blob-opslag

Blob-opslag functies, zoals [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en [Blob Storage levenscyclus beheer beleid](storage-lifecycle-management-concepts.md) , werken nu met accounts die een hiërarchische naam ruimte hebben. Daarom kunt u hiërarchische naam ruimten inschakelen op uw Blob Storage-accounts zonder de toegang tot deze functies te verliezen. 

Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)voor een lijst met ondersteunde functies voor Blob-opslag.

## <a name="supported-azure-service-integrations"></a>Ondersteunde Azure-service-integraties

Data Lake Storage Gen2 ondersteunt verschillende Azure-Services die u kunt gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weer gaven te maken. Zie [Azure-Services die ondersteuning bieden voor Azure data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)voor een lijst met ondersteunde Azure-Services.

## <a name="supported-open-source-platforms"></a>Open source-platforms ondersteund

Data Lake Storage Gen2 ondersteuning voor verschillende open source-platforms. Zie voor een volledige lijst [open-source platforms die Azure data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Toegang tot meerdere protocollen op Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


