---
title: Gegevensscenario's met betrekking tot Data Lake Storage Gen1 | Microsoft Documenten
description: Inzicht krijgen in de verschillende scenario's en hulpprogramma's waarmee gegevens kunnen worden ingenomen, verwerkt, gedownload en gevisualiseerd in Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: a8f9b66bf9a301888f2371fb1c58a4845c2232b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536153"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Azure Data Lake Storage Gen1 gebruiken voor big data-vereisten

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Er zijn vier belangrijke fasen in de verwerking van big data:

* Inname van grote hoeveelheden gegevens in een gegevensarchief, in realtime of in batches
* Verwerking van de gegevens
* De gegevens downloaden
* De gegevens visualiseren

In dit artikel bekijken we deze fasen met betrekking tot Azure Data Lake Storage Gen1 om inzicht te krijgen in de opties en hulpprogramma's die beschikbaar zijn om aan uw big data-behoeften te voldoen.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Gegevens opnemen in Data Lake Storage Gen1
In dit gedeelte worden de verschillende bronnen van gegevens en de verschillende manieren waarop die gegevens kunnen worden opgenomen in een Data Lake Storage Gen1-account belicht.

![Gegevens opnemen in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Gegevens opnemen in Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Ad hoc-gegevens
Dit vertegenwoordigt kleinere gegevenssets die worden gebruikt voor het prototypen van een big data-toepassing. Er zijn verschillende manieren om ad hoc gegevens in te nemen, afhankelijk van de bron van de gegevens.

| Gegevensbron | Innemen met behulp van het met behulp van |
| --- | --- |
| Lokale computer |<ul> <li>[Azure-portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure-CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Data Lake-hulpprogramma's gebruiken voor Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure-gegevensfabriek](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy, gereedschap](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp draait op HDInsight-cluster](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Gestreamde gegevens
Dit vertegenwoordigt gegevens die kunnen worden gegenereerd door verschillende bronnen, zoals toepassingen, apparaten, sensoren, enz. Deze gegevens kunnen worden ingenomen in Data Lake Storage Gen1 door een verscheidenheid aan tools. Deze tools zullen de gegevens meestal per gebeurtenis per gebeurtenis vastleggen en verwerken in realtime en vervolgens de gebeurtenissen in batches in Data Lake Storage Gen1 schrijven, zodat ze verder kunnen worden verwerkt.

Hieronder volgen tools die u gebruiken:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) - Gebeurtenissen die zijn opgenomen in gebeurtenishubs kunnen worden geschreven naar Azure Data Lake Storage Gen1 met behulp van een Azure Data Lake Storage Gen1-uitvoer.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) - U gegevens rechtstreeks naar Data Lake Storage Gen1 schrijven vanuit het cluster Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – U gebeurtenissen van Event Hubs ontvangen en deze vervolgens naar Data Lake Storage Gen1 schrijven met behulp van de [Data Lake Storage Gen1 .NET SDK.](data-lake-store-get-started-net-sdk.md)

### <a name="relational-data"></a>Relationele gegevens
U ook gegevens uit relationele databases bron. Over een periode van tijd verzamelen relationele databases enorme hoeveelheden gegevens die belangrijke inzichten kunnen bieden als ze worden verwerkt via een big data-pijplijn. U de volgende tools gebruiken om dergelijke gegevens naar Data Lake Storage Gen1 te verplaatsen.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure-gegevensfabriek](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webserverlogboekgegevens (uploaden met aangepaste toepassingen)
Dit type gegevensset wordt specifiek genoemd omdat analyse van webserverlogboekgegevens een veelvoorkomende use case is voor big data-toepassingen en grote hoeveelheden logbestanden vereist die moeten worden geüpload naar Data Lake Storage Gen1. U een van de volgende tools gebruiken om uw eigen scripts of toepassingen te schrijven om dergelijke gegevens te uploaden.

* [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure-gegevensfabriek](../data-factory/copy-activity-overview.md)

Voor het uploaden van loggegevens van webservers, en ook voor het uploaden van andere soorten gegevens (bijvoorbeeld gegevens over sociale sentimenten), is het een goede aanpak om uw eigen aangepaste scripts/applicaties te schrijven, omdat het u de flexibiliteit geeft om uw component voor het uploaden van gegevens op te nemen als onderdeel van uw grotere big data-toepassing. In sommige gevallen kan deze code de vorm aannemen van een script of eenvoudige opdrachtregelhulpprogramma. In andere gevallen kan de code worden gebruikt om big data processing te integreren in een zakelijke applicatie of oplossing.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Gegevens die zijn gekoppeld aan Azure HDInsight-clusters
De meeste HDInsight-clustertypen (Hadoop, HBase, Storm) ondersteunen Data Lake Storage Gen1 als een opslagopslagplaats voor gegevensopslag. HDInsight-clusters hebben toegang tot gegevens van Azure Storage Blobs (WASB). Voor betere prestaties u de gegevens van WASB kopiëren naar een Data Lake Storage Gen1-account dat is gekoppeld aan het cluster. U de volgende tools gebruiken om de gegevens te kopiëren.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy-service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure-gegevensfabriek](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Gegevens die zijn opgeslagen in on-premises of IaaS Hadoop-clusters
Grote hoeveelheden gegevens kunnen worden opgeslagen in bestaande Hadoop-clusters, lokaal op machines met HDFS. De Hadoop-clusters bevinden zich mogelijk in een on-premises implementatie of bevinden zich mogelijk binnen een IaaS-cluster op Azure. Er kunnen vereisten zijn om dergelijke gegevens te kopiëren naar Azure Data Lake Storage Gen1 voor een eenmalige benadering of op een terugkerende manier. Er zijn verschillende opties die u gebruiken om dit te bereiken. Hieronder vindt u een lijst met alternatieven en de bijbehorende trade-offs.

| Methode | Details | Voordelen | Overwegingen |
| --- | --- | --- | --- |
| Gebruik Azure Data Factory (ADF) om gegevens rechtstreeks van Hadoop-clusters naar Azure Data Lake Storage Gen1 te kopiëren |[ADF ondersteunt HDFS als gegevensbron](../data-factory/connector-hdfs.md) |ADF biedt kant-en-klare ondersteuning voor HDFS en eersteklas end-to-end beheer en monitoring |Vereist dat De Gateway voor gegevensbeheer on-premises of in het IaaS-cluster wordt geïmplementeerd |
| Exporteer gegevens van Hadoop als bestanden. Kopieer de bestanden vervolgens naar Azure Data Lake Storage Gen1 met behulp van het juiste mechanisme. |U bestanden kopiëren naar Azure Data Lake Storage Gen1 met: <ul><li>[Azure PowerShell voor Windows OS](data-lake-store-get-started-powershell.md)</li><li>[Azure-CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Aangepaste app met behulp van een Data Lake Storage Gen1 SDK</li></ul> |Snel aan de slag. Aangepaste uploads kunnen doen |Proces in meerdere stappen waarbij meerdere technologieën betrokken zijn. Management en monitoring zullen in de loop van de tijd uitgroeien tot een uitdaging, gezien het aangepaste karakter van de |
| Gebruik Distcp om gegevens van Hadoop naar Azure Storage te kopiëren. Kopieer vervolgens gegevens van Azure Storage naar Data Lake Storage Gen1 met behulp van het juiste mechanisme. |U gegevens uit Azure Storage kopiëren naar Data Lake Storage Gen1 met: <ul><li>[Azure-gegevensfabriek](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy, gereedschap](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp draait op HDInsight clusters](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |U open-source tools gebruiken. |Proces in meerdere stappen waarbij meerdere technologieën betrokken zijn |

### <a name="really-large-datasets"></a>Echt grote datasets
Voor het uploaden van gegevenssets die variëren in meerdere terabytes, kan het gebruik van de hierboven beschreven methoden soms traag en duur zijn. In dergelijke gevallen u de onderstaande opties gebruiken.

* **Azure ExpressRoute gebruiken**. Met Azure ExpressRoute u privéverbindingen maken tussen Azure-datacenters en infrastructuur op uw locatie. Dit biedt een betrouwbare optie voor het overbrengen van grote hoeveelheden gegevens. Zie [Azure ExpressRoute-documentatie](../expressroute/expressroute-introduction.md)voor meer informatie.
* **"Offline" uploaden van gegevens**. Als het gebruik van Azure ExpressRoute om welke reden dan ook niet haalbaar is, u [azure import/export-service](../storage/common/storage-import-export-service.md) gebruiken om harde schijven met uw gegevens naar een Azure-datacenter te verzenden. Uw gegevens worden eerst geüpload naar Azure Storage Blobs. U vervolgens [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) of [AdlCopy-tool](data-lake-store-copy-data-azure-storage-blob.md) gebruiken om gegevens van Azure Storage Blobs naar Data Lake Storage Gen1 te kopiëren.

  > [!NOTE]
  > Tijdens het gebruik van de import/exportservice mogen de bestandsgroottes op de schijven die u naar Azure-datacenter verzendt, niet groter zijn dan 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Gegevens verwerken die zijn opgeslagen in Data Lake Storage Gen1
Zodra de gegevens beschikbaar zijn in Data Lake Storage Gen1 u analyses uitvoeren op die gegevens met behulp van de ondersteunde big data-toepassingen. Momenteel u Azure HDInsight en Azure Data Lake Analytics gebruiken om gegevensanalysetaken uit te voeren op de gegevens die zijn opgeslagen in Data Lake Storage Gen1.

![Gegevens analyseren in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Gegevens analyseren in Data Lake Storage Gen1")

U de volgende voorbeelden bekijken.

* [Een HDInsight-cluster maken met Data Lake Storage Gen1 als opslag](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Gegevens downloaden van Data Lake Storage Gen1
U ook gegevens downloaden of verplaatsen van Azure Data Lake Storage Gen1 voor scenario's zoals:

* Verplaats gegevens naar andere opslagplaatsen om te communiceren met uw bestaande gegevensverwerkingspijplijnen. U bijvoorbeeld gegevens verplaatsen van Data Lake Storage Gen1 naar Azure SQL Database of on-premises SQL Server.
* Download gegevens naar uw lokale computer voor verwerking in IDE-omgevingen tijdens het bouwen van prototypes van toepassingen.

![Egress-gegevens van Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Egress-gegevens van Data Lake Storage Gen1")

In dergelijke gevallen u een van de volgende opties gebruiken:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure-gegevensfabriek](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

U ook de volgende methoden gebruiken om uw eigen script/toepassing te schrijven om gegevens van Data Lake Storage Gen1 te downloaden.

* [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Gegevens visualiseren in Data Lake Storage Gen1
U een mix van services gebruiken om visuele weergaven te maken van gegevens die zijn opgeslagen in Data Lake Storage Gen1.

![Gegevens visualiseren in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Gegevens visualiseren in Data Lake Storage Gen1")

* U beginnen met [Azure Data Factory om gegevens van Data Lake Storage Gen1 naar Azure SQL Data Warehouse te verplaatsen](../data-factory/copy-activity-overview.md)
* Daarna u [Power BI integreren met Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) om visuele weergave van de gegevens te maken.
