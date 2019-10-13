---
title: Azure Data Lake Storage Gen2 Inleiding
description: Biedt een overzicht van Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d843e288297db656cca6e2a07f2e1f3322ebfa89
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299648"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Inleiding tot Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 is een set mogelijkheden die is toegewezen aan big data Analytics, gebouwd op [Azure Blob-opslag](storage-blobs-introduction.md). Data Lake Storage Gen2 is het resultaat van het convergeren van de mogelijkheden van onze twee bestaande opslag Services, Azure Blob Storage en Azure Data Lake Storage Gen1. Functies van [Azure data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/index), zoals de semantiek van het bestands systeem, map en bestands niveau beveiliging en schaal, worden gecombineerd met goedkope, gelaagde opslag, hoge Beschik baarheid/herstel na nood gevallen vanuit [Azure Blob-opslag](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Ontworpen voor Enter prise big data Analytics

Data Lake Storage Gen2 maakt Azure Storage de basis voor het bouwen van zakelijke gegevens meren op Azure. Data Lake Storage Gen2 is ontworpen met het starten van meerdere PETA bytes aan gegevens terwijl er honderden gigabits van de door voer worden gehouden, en waarmee u eenvoudig enorme hoeveel heden gegevens kunt beheren.

Een fundamenteel onderdeel van Data Lake Storage Gen2 is het toevoegen van een [hiërarchische naam ruimte](data-lake-storage-namespace.md) aan Blob Storage. De hiërarchische naam ruimte organiseert objecten/bestanden in een hiërarchie van mappen voor efficiënte gegevens toegang. Een algemene naamgevings Conventie voor object opslag maakt gebruik van slashes in de naam om een hiërarchische mapstructuur te simuleren. Deze structuur wordt echt met Data Lake Storage Gen2. Bewerkingen, zoals het wijzigen van de naam of het verwijderen van een map, worden enkele atomische meta gegevens bewerkingen in de map, in plaats van dat alle objecten die het naam voorvoegsel van de directory delen, worden opgesomd en verwerkt.

In het verleden moesten cloud-gebaseerde analyses in het gedrang komen op het gebied van prestaties, beheer en beveiliging. Data Lake Storage Gen2 verhelpt elk van deze aspecten op de volgende manieren:

-   De **prestaties** zijn geoptimaliseerd omdat u geen gegevens hoeft te kopiëren of transformeren als vereiste voor analyse. Met de hiërarchische naam ruimte worden de prestaties van Directory beheer-bewerkingen aanzienlijk verbeterd, waardoor de algehele taak prestaties worden verbeterd.

-   **Beheer** is eenvoudiger omdat u bestanden kunt ordenen en bewerken via directory's en submappen.

-   **Beveiliging** is afdwingbaar omdat u POSIX-machtigingen kunt definiëren voor mappen of afzonderlijke bestanden.

-   De effectiviteit van de **kosten** wordt mogelijk gemaakt als data Lake Storage Gen2 is gebaseerd op de voordelige [Azure Blob-opslag](storage-blobs-introduction.md). De extra functies verlagen de total cost of ownership voor het uitvoeren van big data Analytics op Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Belangrijkste functies van Data Lake Storage Gen2

-   **Hadoop-compatibele toegang**: met data Lake Storage Gen2 kunt u gegevens beheren en openen, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Het nieuwe [ABFS-stuur programma](data-lake-storage-abfs-driver.md) is beschikbaar in alle Apache Hadoop omgevingen, waaronder [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)en [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) om toegang te krijgen tot gegevens die zijn opgeslagen in data Lake Storage Gen2.

-   **Een superset van POSIX-machtigingen**: het beveiligings model voor data Lake GEN2 ondersteunt ACL-en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via Storage Explorer of door middel van Frameworks als Hive en Spark.

-   **Rendabel: data Lake Storage Gen2**biedt goedkope opslag capaciteit en-trans acties. Naarmate de volledige levens cyclus van gegevens overgaat, wijzigen de facturerings tarieven de kosten tot een minimum via ingebouwde functies zoals [Azure Blob Storage-levens cyclus](storage-lifecycle-management-concepts.md).

-   **Geoptimaliseerd stuur programma**: het ABFS-stuur programma is speciaal voor Big Data Analytics [geoptimaliseerd](data-lake-storage-abfs-driver.md) . De bijbehorende REST Api's worden geoppereerd via het eind punt `dfs.core.windows.net`.

### <a name="scalability"></a>Schaalbaarheid

Azure Storage is schaalbaar door te ontwerpen of u toegang hebt via Data Lake Storage Gen2 of Blob Storage-interfaces. Het kan *veel Exabyte aan gegevens*opslaan en verwerken. Deze hoeveelheid opslag ruimte is beschikbaar met door Voer gemeten in gigabits per seconde (Gbps) op hoge niveaus van invoer/uitvoer-bewerkingen per seconde (IOPS). Na alleen persistentie wordt de verwerking uitgevoerd in bijna constante per aanvraag vertraging die wordt gemeten op het niveau van de service, het account en de bestanden.

### <a name="cost-effectiveness"></a>Kosten effectiviteit

Een van de vele voor delen van het bouwen van Data Lake Storage Gen2 boven aan Azure Blob Storage is de lage kosten voor opslag capaciteit en trans acties. In tegens telling tot andere Cloud Storage-services hoeven gegevens die zijn opgeslagen in Data Lake Storage Gen2 niet te worden verplaatst of getransformeerd voordat de analyse wordt uitgevoerd. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage)voor meer informatie over prijzen.

Daarnaast verbeteren functies zoals de [hiërarchische naam ruimte](data-lake-storage-namespace.md) de algehele prestaties van veel analyse taken aanzienlijk. Deze verbetering van de prestaties houdt in dat u minder reken kracht nodig hebt om dezelfde hoeveelheid gegevens te verwerken, wat resulteert in een lagere total cost of ownership (TCO) voor de end-to-end analyse taak.

### <a name="one-service-multiple-concepts"></a>Eén service, meerdere concepten

Data Lake Storage Gen2 is een extra mogelijkheid voor big data Analytics, gebouwd op basis van Azure Blob-opslag. Hoewel er veel voor delen bestaan in het gebruik van bestaande platform onderdelen van blobs om gegevens-meren voor analyses te maken en te gebruiken, leidt dit tot meerdere concepten die dezelfde, gedeelde dingen beschrijven.

Hieronder ziet u de equivalente entiteiten, zoals beschreven in verschillende concepten. Tenzij anders aangegeven, zijn deze entiteiten direct synoniem:

| Concept                                | Organisatie op het hoogste niveau | Organisatie op lager niveau                                            | Gegevens container |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs-object opslag voor algemeen gebruik | Container              | Virtuele map (alleen SDK: biedt geen Atomic-manipulatie) | Blob           |
| Azure Data Lake Storage Gen2-Analytics-opslag          | Container            | Directory                                                           | Bestand           |

## <a name="supported-open-source-platforms"></a>Ondersteunde open-source platforms

Verschillende open source-platforms ondersteunen Data Lake Storage Gen2. Deze platformen worden weer gegeven in de volgende tabel.

> [!NOTE]
> Alleen de versies die in deze tabel worden weer gegeven, worden ondersteund.

| Platform |  Ondersteunde versie (s) | Meer informatie |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn in HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3,2 + | [Archief met Apache Hadoop releases](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Opmerkingen bij de release van Cloudera Enter prise 6. x](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 + | [Databricks Runtime versies](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1. x + + | [Toegang tot Cloud gegevens configureren](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="supported-azure-services"></a>Ondersteunde Azure-Services

Data Lake Storage Gen2 ondersteunt verschillende Azure-Services die u kunt gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weer gaven te maken. Zie [Azure data Lake Storage integreren met Azure-Services](data-lake-store-integrate-with-azure-services.md)voor een lijst met ondersteunde Azure-Services.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen worden enkele van de belangrijkste concepten beschreven van Data Lake Storage Gen2 en gedetailleerde informatie over het opslaan, openen, beheren en verkrijgen van inzicht in uw gegevens:

- [Hiërarchische naam ruimte](data-lake-storage-namespace.md)
- [Een opslagaccount maken](data-lake-storage-quickstart-create-account.md)
- [Toegang tot meerdere protocollen op Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
- [Azure data Lake Storage integreren met Azure-Services](data-lake-store-integrate-with-azure-services.md);
