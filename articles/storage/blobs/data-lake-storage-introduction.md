---
title: Inleiding in Azure Data Lake Storage Gen2
description: Biedt een overzicht van Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: ee9f2f0366cc680c874c6748d27e02dcadb82e35
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673306"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Inleiding in Azure Data Lake Storage Gen2

‎Azure Data Lake Storage Gen2 is een reeks mogelijkheden die is toegewezen aan analyse van big data, gebouwd op [Azure Blob Storage](storage-blobs-introduction.md). Data Lake Storage Gen2 is ontstaan door de mogelijkheden van onze twee bestaande opslagservices, Azure Blob Storage en Azure Data Lake Storage Gen1, samen te voegen. Functies van [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), zoals de semantiek van het bestandssysteem, de beveiliging op bestandsniveau en de schaalgrootte, zijn gecombineerd met de mogelijkheden voor goedkope, gelaagde opslag en hoge beschikbaarheid/herstel na noodgevallen van [Azure Blob Storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Ontworpen voor big data-analyse door grote ondernemingen

Data Lake Storage Gen2 maakt van Azure Storage de basis voor het bouwen van zakelijke data lakes op Azure. Data Lake Storage Gen2 is vanaf het begin ontworpen om meerdere petabytes aan gegevens te kunnen bieden met honderden gigabits aan doorvoer en stelt u in staat om eenvoudig enorme hoeveelheden gegevens te beheren.

De toevoeging van een [hiërarchische naamruimte](data-lake-storage-namespace.md) aan Blob Storage vormt een fundamenteel onderdeel van Data Lake Storage Gen2. Met de hiërarchische naamruimte worden objecten/bestanden georganiseerd in een hiërarchie met mappen voor efficiënte toegang tot de gegevens. In een gewone naamconventie voor objectopslag wordt gebruik gemaakt van slashes om een hiërarchische mapstructuur na te bootsen. Deze structuur wordt echt in Data Lake Storage Gen2. Bewerkingen, zoals het wijzigen van de naam of het verwijderen van een map, worden enkelvoudige atomische bewerkingen van metagegevens in de map, in plaats van dat alle objecten die het naamvoorvoegsel van de structuur delen worden voorzien van een nummer en moeten worden verwerkt.

Data Lake Storage Gen2 bouwt voort op Blob Storage en verbetert de prestaties, het beheer en de beveiliging op de volgende manieren:

-   De **prestaties** worden geoptimaliseerd omdat u geen gegevens hoeft te kopiëren of transformeren als voorwaarde voor analyse. In vergelijking met de ongestructureerde naamruimte in Blob Storage verbetert de hiërarchische naamruimte de prestaties tijdens het beheren van de structuur aanzienlijk, waardoor de algehele prestaties worden verbeterd.

-   Het **beheer** is eenvoudiger omdat u bestanden kunt ordenen en bewerken met behulp van mappen en submappen.

-   De **beveiliging** is afdwingbaar omdat u POSIX-machtigingen kunt definiëren voor structuren of afzonderlijke bestanden.

Data Lake Storage Gen2 is ook heel kosteneffectief, omdat de oplossing is gebouwd op basis van de goedkope [Azure Blob-opslag](storage-blobs-introduction.md). De extra functies verlagen de totale beheerkosten voor het uitvoeren van big data-analyses in Azure nog verder.

## <a name="key-features-of-data-lake-storage-gen2"></a>Belangrijkste functies van Data Lake Storage Gen2

-   **Hadoop-compatibele toegang**: Met Data Lake Storage Gen2 kunt u gegevens op dezelfde manier beheren en openen als met een [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Het nieuwe [ABFS-stuurprogramma](data-lake-storage-abfs-driver.md) is beschikbaar in alle Apache Hadoop-omgevingen, waaronder [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) en [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics), voor toegang tot gegevens die in Data Lake Storage Gen2 zijn opgeslagen.

-   **Een superset met POSIX-machtigingen**: Het beveiligingsmodel voor Data Lake Gen2 ondersteunt ACL- en POSIX-machtigingen en extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via Storage Explorer of door middel van frameworks zoals Hive en Spark.

-   **Rendabel**: Data Lake Storage Gen2 biedt goedkope opslagcapaciteit en -transacties. Naarmate de volledige levenscyclus van gegevens vordert, veranderen de factureringstarieven tot een minimum dankzij ingebouwde functies, zoals [de levenscyclus van Azure Blob-opslag](storage-lifecycle-management-concepts.md).

-   **Geoptimaliseerd stuurprogramma**: Het ABFS-stuurprogramma is [specifiek geoptimaliseerd](data-lake-storage-abfs-driver.md) voor big data-analyse. De bijbehorende REST API's worden aan het oppervlak gebracht via het eindpunt `dfs.core.windows.net`.

### <a name="scalability"></a>Schaalbaarheid

Azure Storage is inherent schaalbaar - of u nu toegang hebt via Data Lake Storage Gen2 of via Blob Storage-interfaces. De service kan *veel exabytes aan gegevens* opslaan en verwerken. Deze hoeveelheid opslagruimte is beschikbaar met een doorvoer in gigabits per seconde (Gbps) bij hoge aantallen invoer-/uitvoerbewerkingen per seconde (IOPS). De verwerking gaat verder dan alleen persistentie en wordt uitgevoerd op bijna constante latenties per aanvraag, die worden gemeten op service-, account- en bestandsniveaus.

### <a name="cost-effectiveness"></a>Kosteneffectiviteit

Een van de vele voordelen van de ontwikkeling van Data Lake Storage Gen2 boven op Azure Blob Storage zijn de lage kosten voor opslagcapaciteit en transacties. In tegenstelling tot andere cloudopslagservices hoeven gegevens die in Data Lake Storage Gen2 zijn opgeslagen niet te worden verplaatst of getransformeerd voordat de analyse kan worden uitgevoerd. Zie de [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage) voor meer informatie over prijzen.

Daarnaast kunnen functies, zoals de [hiërarchische naamruimte](data-lake-storage-namespace.md), de algehele prestaties van veel analysetaken aanzienlijk verbeteren. Door deze prestatieverbetering hebt u minder rekenkracht nodig om dezelfde hoeveelheid gegevens te verwerken, wat resulteert in lagere totale beheerkosten voor de analysetaak van begin tot eind.

### <a name="one-service-multiple-concepts"></a>Eén service, meerdere concepten

Data Lake Storage Gen2 is een extra mogelijkheid voor analyse van big data boven op Azure Blob Storage. Hoewel het gebruik van bestaande platformonderdelen van blobs om data lakes voor analyses te maken en te gebruiken veel voordelen biedt, leidt dit tot meerdere concepten die dezelfde, gedeelde zaken beschrijven.

Hieronder ziet u de equivalente entiteiten, zoals beschreven in verschillende concepten. Tenzij anders aangegeven, zijn deze entiteiten direct synoniem:

| Concept                                | Organisatie op het hoogste niveau | Organisatie op lager niveau                                            | Gegevenscontainer |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs - Objectopslag voor algemeen gebruik | Container              | Virtuele map (alleen SDK: biedt geen atomische manipulatie) | Blob           |
| Azure Data Lake Storage Gen2 - Analytics Storage          | Container            | Directory                                                           | File           |

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob Storage-functies

Functies voor Blob-opslag, zoals [diagnostische logboekregistratie](../common/storage-analytics-logging.md),  [toegangslagen](storage-blob-storage-tiers.md) en  [beleid voor levenscyclusbeheer van Blob Storage](storage-lifecycle-management-concepts.md), werken nu met accounts met een hiërarchische naamruimte. U kunt dus hiërarchische naamruimten inschakelen op uw Blob Storage-accounts zonder toegang tot deze functies te verliezen. 

Zie [Blob Storage-functies die beschikbaar zijn in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md) voor een lijst met ondersteunde Blob Storage-functies.

## <a name="supported-azure-service-integrations"></a>Ondersteunde integraties met Azure-services

Data Lake Storage Gen2 ondersteunt verschillende Azure-services die u kunt gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weergaven te maken. Zie [Azure-services die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md) voor een lijst met ondersteunde Azure-services.

## <a name="supported-open-source-platforms"></a>Ondersteunde open source-platforms

Verschillende open source-platformen ondersteunen Data Lake Storage Gen2. Zie [Open source-platformen die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md) voor een volledig overzicht.

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Toegang met meerdere protocollen in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


