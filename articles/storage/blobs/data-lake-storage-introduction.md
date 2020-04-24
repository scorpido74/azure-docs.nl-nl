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
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942919"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Inleiding tot Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 is een set mogelijkheden die is toegewezen aan big data Analytics, gebouwd op [Azure Blob-opslag](storage-blobs-introduction.md). Data Lake Storage Gen2 is het resultaat van het convergeren van de mogelijkheden van onze twee bestaande opslag Services, Azure Blob Storage en Azure Data Lake Storage Gen1. Functies van [Azure data Lake Storage gen1](https://docs.microsoft.com/azure/data-lake-store/index), zoals de semantiek van het bestands systeem, map en bestands niveau beveiliging en schaal, worden gecombineerd met goedkope, gelaagde opslag, hoge Beschik baarheid/herstel na nood gevallen vanuit [Azure Blob-opslag](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Ontworpen voor Enter prise big data Analytics

Data Lake Storage Gen2 maakt Azure Storage de basis voor het bouwen van zakelijke gegevens meren op Azure. Data Lake Storage Gen2 is ontworpen met het starten van meerdere PETA bytes aan gegevens terwijl er honderden gigabits van de door voer worden gehouden, en waarmee u eenvoudig enorme hoeveel heden gegevens kunt beheren.

Een fundamenteel onderdeel van Data Lake Storage Gen2 is het toevoegen van een [hiërarchische naam ruimte](data-lake-storage-namespace.md) aan Blob Storage. De hiërarchische naam ruimte organiseert objecten/bestanden in een hiërarchie van mappen voor efficiënte gegevens toegang. Een algemene naamgevings Conventie voor object opslag maakt gebruik van slashes in de naam om een hiërarchische mapstructuur te simuleren. Deze structuur wordt echt met Data Lake Storage Gen2. Bewerkingen, zoals het wijzigen van de naam of het verwijderen van een map, worden enkele atomische meta gegevens bewerkingen in de map, in plaats van dat alle objecten die het naam voorvoegsel van de directory delen, worden opgesomd en verwerkt.

Data Lake Storage Gen2 bouwt voort op Blob Storage en verbetert de prestaties, het beheer en de beveiliging op de volgende manieren:

-   De **prestaties** zijn geoptimaliseerd omdat u geen gegevens hoeft te kopiëren of transformeren als vereiste voor analyse. Vergeleken met de platte naam ruimte in Blob Storage, verbetert de hiërarchische naam ruimte de prestaties van Directory beheer-bewerkingen aanzienlijk, waardoor de algehele taak prestaties worden verbeterd.

-   **Beheer** is eenvoudiger omdat u bestanden kunt ordenen en bewerken via directory's en submappen.

-   **Beveiliging** is afdwingbaar omdat u POSIX-machtigingen kunt definiëren voor mappen of afzonderlijke bestanden.

Data Lake Storage Gen2 is ook zeer rendabel, omdat deze wordt gebouwd op basis van de voordelige [Azure Blob-opslag](storage-blobs-introduction.md). De extra functies verlagen de total cost of ownership voor het uitvoeren van big data Analytics op Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Belangrijkste functies van Data Lake Storage Gen2

-   **Hadoop-compatibele toegang**: met data Lake Storage Gen2 kunt u gegevens beheren en openen, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Het nieuwe [ABFS-stuur programma](data-lake-storage-abfs-driver.md) is beschikbaar in alle Apache Hadoop omgevingen, waaronder [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)en [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) om toegang te krijgen tot gegevens die zijn opgeslagen in data Lake Storage Gen2.

-   **Een superset van POSIX-machtigingen**: het beveiligings model voor data Lake GEN2 ondersteunt ACL-en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via Storage Explorer of door middel van Frameworks als Hive en Spark.

-   **Rendabel: data Lake Storage Gen2**biedt goedkope opslag capaciteit en-trans acties. Naarmate de volledige levens cyclus van gegevens overgaat, wijzigen de facturerings tarieven de kosten tot een minimum via ingebouwde functies zoals [Azure Blob Storage-levens cyclus](storage-lifecycle-management-concepts.md).

-   **Geoptimaliseerd stuur programma**: het ABFS-stuur programma is speciaal voor Big Data Analytics [geoptimaliseerd](data-lake-storage-abfs-driver.md) . De bijbehorende REST Api's worden opgehaald via het eind punt `dfs.core.windows.net`.

### <a name="scalability"></a>Schaalbaarheid

Azure Storage is schaalbaar door te ontwerpen of u toegang hebt via Data Lake Storage Gen2 of Blob Storage-interfaces. Het kan *veel Exabyte aan gegevens*opslaan en verwerken. Deze hoeveelheid opslag ruimte is beschikbaar met door Voer gemeten in gigabits per seconde (Gbps) op hoge niveaus van invoer/uitvoer-bewerkingen per seconde (IOPS). Na alleen persistentie wordt de verwerking uitgevoerd in bijna constante per aanvraag vertraging die wordt gemeten op het niveau van de service, het account en de bestanden.

### <a name="cost-effectiveness"></a>Voordelig

Een van de vele voor delen van het bouwen van Data Lake Storage Gen2 boven aan Azure Blob Storage is de lage kosten voor opslag capaciteit en trans acties. In tegens telling tot andere Cloud Storage-services hoeven gegevens die zijn opgeslagen in Data Lake Storage Gen2 niet te worden verplaatst of getransformeerd voordat de analyse wordt uitgevoerd. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage)voor meer informatie over prijzen.

Daarnaast verbeteren functies zoals de [hiërarchische naam ruimte](data-lake-storage-namespace.md) de algehele prestaties van veel analyse taken aanzienlijk. Deze verbetering van de prestaties houdt in dat u minder reken kracht nodig hebt om dezelfde hoeveelheid gegevens te verwerken, wat resulteert in een lagere total cost of ownership (TCO) voor de end-to-end analyse taak.

### <a name="one-service-multiple-concepts"></a>Eén service, meerdere concepten

Data Lake Storage Gen2 is een extra mogelijkheid voor big data Analytics, gebouwd op basis van Azure Blob-opslag. Hoewel er veel voor delen bestaan in het gebruik van bestaande platform onderdelen van blobs om gegevens-meren voor analyses te maken en te gebruiken, leidt dit tot meerdere concepten die dezelfde, gedeelde dingen beschrijven.

Hieronder ziet u de equivalente entiteiten, zoals beschreven in verschillende concepten. Tenzij anders aangegeven, zijn deze entiteiten direct synoniem:

| Concept                                | Organisatie op het hoogste niveau | Organisatie op lager niveau                                            | Gegevens container |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs-object opslag voor algemeen gebruik | Container              | Virtuele map (alleen SDK: biedt geen Atomic-manipulatie) | Blob           |
| Azure Data Lake Storage Gen2-Analytics-opslag          | Container            | Directory                                                           | File           |

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob Storage-functies

Blob-opslag functies, zoals [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en [Blob Storage levenscyclus beheer beleid](storage-lifecycle-management-concepts.md) , werken nu met accounts die een hiërarchische naam ruimte hebben. Daarom kunt u hiërarchische naam ruimten inschakelen op uw Blob Storage-accounts zonder de toegang tot deze functies te verliezen. 

Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)voor een lijst met ondersteunde functies voor Blob-opslag.

## <a name="supported-azure-service-integrations"></a>Ondersteunde Azure-service-integraties

Data Lake Storage Gen2 ondersteunt verschillende Azure-Services die u kunt gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weer gaven te maken. Zie [Azure-Services die ondersteuning bieden voor Azure data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)voor een lijst met ondersteunde Azure-Services.

## <a name="supported-open-source-platforms"></a>Ondersteunde open source-platforms

Verschillende open source-platforms ondersteunen Data Lake Storage Gen2. Zie voor een volledige lijst [open-source platforms die Azure data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Toegang tot meerdere protocollen op Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


