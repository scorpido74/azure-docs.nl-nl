---
title: Introductie azure Data Lake Storage Gen2
description: Biedt een overzicht van Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942919"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Inleiding tot Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 is een reeks mogelijkheden die zijn toegeveld d.m.v. big data analytics, gebouwd op [Azure Blob-opslag.](storage-blobs-introduction.md) Data Lake Storage Gen2 is het resultaat van het convergeren van de mogelijkheden van onze twee bestaande opslagservices, Azure Blob-opslag en Azure Data Lake Storage Gen1. Functies van [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), zoals semantiek van bestandssystemen, directory- en bestandsniveaubeveiliging en -schaal, worden gecombineerd met low-cost, gelaagde opslag, mogelijkheden voor hoge beschikbaarheid/noodherstel van Azure [Blob-opslag](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Ontworpen voor big data-analyses van bedrijven

Data Lake Storage Gen2 maakt Azure Storage de basis voor het bouwen van bedrijfsgegevensmeren op Azure. Data Lake Storage Gen2 is vanaf het begin ontworpen om meerdere petabytes aan informatie te onderhouden en tegelijkertijd honderden gigabits doorvoer te ondersteunen, zodat u eenvoudig enorme hoeveelheden gegevens beheren.

Een fundamenteel onderdeel van Data Lake Storage Gen2 is de toevoeging van een [hiërarchische naamruimte](data-lake-storage-namespace.md) aan Blob-opslag. De hiërarchische naamruimte organiseert objecten/bestanden in een hiërarchie van mappen voor efficiënte gegevenstoegang. Een algemene conventie voor het benoemen van objectenopte goed maakt gebruik van schuine strepen in de naam om een hiërarchische mapstructuur na te bootsen. Deze structuur wordt echt met Data Lake Storage Gen2. Bewerkingen zoals het hernoemen of verwijderen van een map worden afzonderlijke atomaire metagegevensbewerkingen op de map in plaats van alle objecten die het naamvoorvoegsel van de map delen, op te sommen en te verwerken.

Data Lake Storage Gen2 bouwt voort op Blob-opslag en verbetert de prestaties, het beheer en de beveiliging op de volgende manieren:

-   **Prestaties** worden geoptimaliseerd omdat u gegevens niet hoeft te kopiëren of te transformeren als een voorwaarde voor analyse. In vergelijking met de platte naamruimte op Blob-opslag verbetert de hiërarchische naamruimte de prestaties van directorybeheerbewerkingen aanzienlijk, wat de algehele taakprestaties verbetert.

-   **Beheer** is eenvoudiger omdat u bestanden ordenen en manipuleren via mappen en submappen.

-   **Beveiliging** is afdwingbaar omdat u POSIX-machtigingen voor mappen of afzonderlijke bestanden definiëren.

Data Lake Storage Gen2 is ook zeer kosteneffectief omdat het is gebouwd bovenop de goedkope [Azure Blob-opslag.](storage-blobs-introduction.md) De extra functies verlagen de totale eigendomskosten voor het uitvoeren van big data-analyses op Azure verder.

## <a name="key-features-of-data-lake-storage-gen2"></a>Belangrijkste kenmerken van Data Lake Storage Gen2

-   **Hadoop-compatibele toegang:** Met Data Lake Storage Gen2 u gegevens beheren en openen, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Het nieuwe [ABFS-stuurprogramma](data-lake-storage-abfs-driver.md) is beschikbaar in alle Apache Hadoop-omgevingen, waaronder [Azure HDInsight,](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)en [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) om toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen2.

-   **Een superset van POSIX-machtigingen:** het beveiligingsmodel voor Data Lake Gen2 ondersteunt ACL- en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via Storage Explorer of via frameworks zoals Hive en Spark.

-   **Kosteneffectief**: Data Lake Storage Gen2 biedt goedkope opslagcapaciteit en transacties. Naarmate gegevens de volledige levenscyclus doorlopen, wijzigen factureringstarieven om de kosten tot een minimum te beperken via ingebouwde functies zoals [de levenscyclus van Azure Blob-opslag.](storage-lifecycle-management-concepts.md)

-   **Geoptimaliseerde driver**: De ABFS driver is [speciaal geoptimaliseerd](data-lake-storage-abfs-driver.md) voor big data analytics. De bijbehorende REST API's worden `dfs.core.windows.net`opgedoken door het eindpunt .

### <a name="scalability"></a>Schaalbaarheid

Azure Storage is schaalbaar door het ontwerp, of u nu toegang hebt via Data Lake Storage Gen2 of Blob-opslaginterfaces. Het is in staat om *veel exabytes aan gegevens*op te slaan en te serveren. Deze hoeveelheid opslag is beschikbaar met doorvoer gemeten in gigabits per seconde (Gbps) bij hoge niveaus van input/output bewerkingen per seconde (IOPS). Naast alleen volharding, wordt de verwerking uitgevoerd op bijna constant latenlaten per aanvraag die worden gemeten op service-, account- en bestandsniveaus.

### <a name="cost-effectiveness"></a>Voordelig

Een van de vele voordelen van het bouwen van Data Lake Storage Gen2 bovenop Azure Blob-opslag is de lage kosten van opslagcapaciteit en transacties. In tegenstelling tot andere cloudopslagservices hoeven gegevens die zijn opgeslagen in Data Lake Storage Gen2 niet te worden verplaatst of getransformeerd voordat ze worden geanalyseerd. Zie [Azure Storage-prijzen](https://azure.microsoft.com/pricing/details/storage)voor meer informatie over prijzen.

Bovendien verbeteren functies zoals de [hiërarchische naamruimte](data-lake-storage-namespace.md) de algehele prestaties van veel analysetaken aanzienlijk. Deze prestatieverbetering betekent dat u minder rekenkracht nodig hebt om dezelfde hoeveelheid gegevens te verwerken, wat resulteert in een lagere total cost of ownership (TCO) voor de end-to-end analytics-taak.

### <a name="one-service-multiple-concepts"></a>Eén service, meerdere concepten

Data Lake Storage Gen2 is een extra mogelijkheid voor big data-analyses, gebouwd bovenop Azure Blob-opslag. Hoewel er veel voordelen zijn in het benutten van bestaande platformcomponenten van Blobs om gegevensmeren voor analytics te maken en te bedienen, leidt dit wel tot meerdere concepten die dezelfde, gedeelde dingen beschrijven.

De volgende zijn de gelijkwaardige entiteiten, zoals beschreven door verschillende concepten. Tenzij anders vermeld zijn deze entiteiten direct synoniem:

| Concept                                | Organisatie op het hoogste niveau | Organisatie op lager niveau                                            | Gegevenscontainer |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – Objectopslag voor algemeen gebruik | Container              | Virtuele directory (alleen SDK - biedt geen atoommanipulatie) | Blob           |
| Azure Data Lake Storage Gen2 – Analytics-opslag          | Container            | Directory                                                           | File           |

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob-opslagfuncties

Blob-opslagfuncties zoals [diagnostische logboekregistratie,](../common/storage-analytics-logging.md) [toegangslagen](storage-blob-storage-tiers.md)en [blobopslagbeheerbeleid](storage-lifecycle-management-concepts.md) voor de levenscyclus werken nu met accounts met een hiërarchische naamruimte. Daarom u hiërarchische naamruimten op uw Blob-opslagaccounts inschakelen zonder de toegang tot deze functies te verliezen. 

Zie [Blob Storage-functies die beschikbaar zijn in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)voor een lijst met ondersteunde Blob-opslagfuncties.

## <a name="supported-azure-service-integrations"></a>Ondersteunde Azure-service-integraties

Data Lake Storage gen2 ondersteunt verschillende Azure-services die u gebruiken om gegevens in te nemen, analyses uit te voeren en visuele weergaven te maken. Zie [Azure-services die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-azure-services.md)voor een lijst met ondersteunde Azure-services.

## <a name="supported-open-source-platforms"></a>Ondersteunde open source platforms

Verschillende open source platforms ondersteunen Data Lake Storage Gen2. Zie [Open source-platforms die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md)voor een volledige lijst.

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Toegang met meerdere protocollen op Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


