---
title: Gegevensscenario's met Azure Data Lake Storage Gen2 | Microsoft Documenten
description: Inzicht krijgen in de verschillende scenario's en hulpprogramma's waarmee gegevens kunnen worden ingenomen, verwerkt, gedownload en gevisualiseerd in Data Lake Storage Gen2 (voorheen bekend als Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b0ebe6cb505fa2a145dd3cbb94398912f2933a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77369713"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Azure Data Lake Storage Gen2 gebruiken voor big data-vereisten

Er zijn vier belangrijke fasen in de verwerking van big data:

> [!div class="checklist"]
> * Inname van grote hoeveelheden gegevens in een gegevensarchief, in realtime of in batches
> * Verwerking van de gegevens
> * De gegevens downloaden
> * De gegevens visualiseren

In dit artikel worden de opties en hulpprogramma's voor elke verwerkingsfase belicht.

Zie [Azure Data Lake Storage integreren met Azure-services](data-lake-storage-integrate-with-azure-services.md) voor een volledige lijst met Azure-services die u gebruiken met Azure Data Lake Storage

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>De gegevens innemen in Data Lake Storage Gen2

In dit gedeelte worden de verschillende bronnen van gegevens en de verschillende manieren waarop die gegevens kunnen worden opgenomen in een Data Lake Storage Gen2-account belicht.

![Gegevens opnemen in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Gegevens opnemen in Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc-gegevens

Dit vertegenwoordigt kleinere gegevenssets die worden gebruikt voor het prototypen van een big data-toepassing. Er zijn verschillende manieren om ad hoc gegevens in te nemen, afhankelijk van de bron van de gegevens. 

Hier is een lijst met tools die u gebruiken om ad hoc-gegevens in te nemen.

| Gegevensbron | Innemen met behulp van het met behulp van |
| --- | --- |
| Lokale computer |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure-CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy, gereedschap](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure-gegevensfabriek](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy, gereedschap](../common/storage-use-azcopy-v10.md)<br><br>[DistCp draait op HDInsight-cluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Gestreamde gegevens

Dit vertegenwoordigt gegevens die kunnen worden gegenereerd door verschillende bronnen, zoals toepassingen, apparaten, sensoren, enz. Deze gegevens kunnen worden opgenomen in Data Lake Storage Gen2 door een verscheidenheid aan tools. Deze tools zullen de gegevens meestal per gebeurtenis per gebeurtenis vastleggen en verwerken in realtime en vervolgens de gebeurtenissen in batches in Data Lake Storage Gen2 schrijven, zodat ze verder kunnen worden verwerkt.

Hier is een lijst met tools die u gebruiken om gestreamde gegevens in te nemen.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure Stream Analytics|[Snelstart: Een Stream Analytics-taak maken via Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Uitgang naar Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Azure HDInsight Storm | [Schrijf naar Apache Hadoop HDFS van Apache Storm op HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relationele gegevens

U ook gegevens uit relationele databases bron. Over een periode van tijd verzamelen relationele databases enorme hoeveelheden gegevens die belangrijke inzichten kunnen bieden als ze worden verwerkt via een big data-pijplijn. U de volgende tools gebruiken om dergelijke gegevens naar Data Lake Storage Gen2 te verplaatsen.

Hier is een lijst met tools die u gebruiken om relationele gegevens in te nemen.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webserverlogboekgegevens (uploaden met aangepaste toepassingen)

Dit type gegevensset wordt specifiek genoemd omdat analyse van webserverlogboekgegevens een veelvoorkomende use case is voor big data-toepassingen en grote hoeveelheden logbestanden vereist die moeten worden geüpload naar Data Lake Storage Gen2. U een van de volgende tools gebruiken om uw eigen scripts of toepassingen te schrijven om dergelijke gegevens te uploaden.

Hier is een lijst met hulpprogramma's die u gebruiken om serverloggegevens in te nemen.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |
|Azure-CLI|[Azure-CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Voor het uploaden van loggegevens van webservers, en ook voor het uploaden van andere soorten gegevens (bijvoorbeeld gegevens over sociale sentimenten), is het een goede aanpak om uw eigen aangepaste scripts/applicaties te schrijven, omdat het u de flexibiliteit geeft om uw component voor het uploaden van gegevens op te nemen als onderdeel van uw grotere big data-toepassing. In sommige gevallen kan deze code de vorm aannemen van een script of eenvoudige opdrachtregelhulpprogramma. In andere gevallen kan de code worden gebruikt om big data processing te integreren in een zakelijke applicatie of oplossing.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Gegevens die zijn gekoppeld aan Azure HDInsight-clusters

De meeste HDInsight-clustertypen (Hadoop, HBase, Storm) ondersteunen Data Lake Storage Gen2 als een opslagopslagplaats voor gegevensopslag. HDInsight-clusters hebben toegang tot gegevens van Azure Storage Blobs (WASB). Voor betere prestaties u de gegevens van WASB kopiëren naar een Data Lake Storage Gen2-account dat is gekoppeld aan het cluster. U de volgende tools gebruiken om de gegevens te kopiëren.

Hier is een lijst met tools die u gebruiken om gegevens in te nemen die zijn gekoppeld aan HDInsight-clusters.

|Hulpprogramma | Richtlijnen |
|---|--|
|Apache DistCp | [DistCp gebruiken om gegevens te kopiëren tussen Azure Storage Blobs en Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy, gereedschap | [Gegevens overbrengen met de AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Gegevens kopiëren naar of van Azure Data Lake Storage Gen2 met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Gegevens die zijn opgeslagen in on-premises of IaaS Hadoop-clusters

Grote hoeveelheden gegevens kunnen worden opgeslagen in bestaande Hadoop-clusters, lokaal op machines met HDFS. De Hadoop-clusters bevinden zich mogelijk in een on-premises implementatie of bevinden zich mogelijk binnen een IaaS-cluster op Azure. Er kunnen vereisten zijn om dergelijke gegevens te kopiëren naar Azure Data Lake Storage Gen2 voor een eenmalige benadering of op een terugkerende manier. Er zijn verschillende opties die u gebruiken om dit te bereiken. Hieronder vindt u een lijst met alternatieven en de bijbehorende trade-offs.

| Methode | Details | Voordelen | Overwegingen |
| --- | --- | --- | --- |
| Gebruik Azure Data Factory (ADF) om gegevens rechtstreeks van Hadoop-clusters naar Azure Data Lake Storage Gen2 te kopiëren |[ADF ondersteunt HDFS als gegevensbron](../../data-factory/connector-hdfs.md) |ADF biedt kant-en-klare ondersteuning voor HDFS en eersteklas end-to-end beheer en monitoring |Vereist dat De Gateway voor gegevensbeheer on-premises of in het IaaS-cluster wordt geïmplementeerd |
| Gebruik Distcp om gegevens van Hadoop naar Azure Storage te kopiëren. Kopieer vervolgens gegevens van Azure Storage naar Data Lake Storage Gen2 met behulp van het juiste mechanisme. |U gegevens uit Azure Storage kopiëren naar Data Lake Storage Gen2 met: <ul><li>[Azure-gegevensfabriek](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy, gereedschap](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp draait op HDInsight clusters](data-lake-storage-use-distcp.md)</li></ul> |U open-source tools gebruiken. |Proces in meerdere stappen waarbij meerdere technologieën betrokken zijn |

### <a name="really-large-datasets"></a>Echt grote datasets

Voor het uploaden van gegevenssets die variëren in meerdere terabytes, kan het gebruik van de hierboven beschreven methoden soms traag en duur zijn. In dergelijke gevallen u Azure ExpressRoute gebruiken.  

Met Azure ExpressRoute u privéverbindingen maken tussen Azure-datacenters en infrastructuur op uw locatie. Dit biedt een betrouwbare optie voor het overbrengen van grote hoeveelheden gegevens. Zie [Azure ExpressRoute-documentatie](../../expressroute/expressroute-introduction.md)voor meer informatie.

## <a name="process-the-data"></a>De gegevens verwerken

Zodra de gegevens beschikbaar zijn in Data Lake Storage Gen2 u analyses uitvoeren op die gegevens met behulp van de ondersteunde big data-toepassingen. 

![Gegevens analyseren in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Gegevens analyseren in Data Lake Storage Gen2")

Hier is een lijst met hulpprogramma's die u gebruiken om gegevensanalysetaken uit te voeren op gegevens die zijn opgeslagen in Data Lake Storage Gen2.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure HDInsight | [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Snelstart: gegevens analyseren in Azure Data Lake Storage Gen2 met Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Zelfstudie: Gegevens extraheren, transformeren en laden met Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>De gegevens visualiseren

Gebruik de Power BI-connector om visuele weergaven te maken van gegevens die zijn opgeslagen in Data Lake Storage Gen2. Zie [Gegevens analyseren in Azure Data Lake Storage Gen2 met Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>De gegevens downloaden

U ook gegevens downloaden of verplaatsen van Azure Data Lake Storage Gen2 voor scenario's zoals:

* Verplaats gegevens naar andere opslagplaatsen om te communiceren met uw bestaande gegevensverwerkingspijplijnen. U bijvoorbeeld gegevens verplaatsen van Data Lake Storage Gen2 naar Azure SQL Database of on-premises SQL Server.

* Download gegevens naar uw lokale computer voor verwerking in IDE-omgevingen tijdens het bouwen van prototypes van toepassingen.

![Egress-gegevens van Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Egress-gegevens van Data Lake Storage Gen2")

Hier is een lijst met hulpprogramma's die u gebruiken om gegevens van Data Lake Storage Gen2 te downloaden.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [DistCp gebruiken om gegevens te kopiëren tussen Azure Storage Blobs en Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Azure Opslagverkenner|[Azure Storage Explorer gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|AzCopy, gereedschap|[Gegevens overbrengen met AzCopy- en Blob-opslag](../common/storage-use-azcopy-blobs.md)|
