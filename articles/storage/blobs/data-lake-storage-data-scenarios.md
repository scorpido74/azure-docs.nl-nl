---
title: Gegevens scenario's met betrekking tot Azure Data Lake Storage Gen2 | Microsoft Docs
description: Inzicht krijgen in de verschillende scenario's en hulpprogram ma's die gebruikmaken van welke gegevens kunnen worden opgenomen, verwerkt, gedownload en gevisualiseerd in Data Lake Storage Gen2 (voorheen bekend als Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 9eb10260573978355a3b6996a6f06c0c612abe4c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350022"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Azure Data Lake Storage Gen2 gebruiken voor big data vereisten

Er zijn vier belang rijke fasen in het big data verwerken:

> [!div class="checklist"]
> * Grote hoeveel heden gegevens opnemen in een gegevens archief, in realtime of in batches
> * De gegevens verwerken
> * De gegevens downloaden
> * De gegevens visualiseren

Maak eerst een opslag account en een container. Verleen vervolgens toegang tot de gegevens. De eerste gedeelten van dit artikel helpen u bij het uitvoeren van deze taken. In de overige secties worden de opties en hulpprogram ma's voor elke verwerkings fase gemarkeerd.

## <a name="create-a-data-lake-storage-gen2-account"></a>Een Data Lake Storage Gen2-account maken

Een Data Lake Storage Gen2 account is een opslag account met een hiërarchische naam ruimte. 

Als u er een wilt maken, raadpleegt u [Quickstart: Maak een Azure Data Lake Storage Gen2 Storage-](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)account.

## <a name="create-a-container"></a>Een container maken

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om een container voor uw bestanden te maken.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure Opslagverkenner | [Een container maken met behulp van Storage Explorer](data-lake-storage-explorer.md#create-a-container) |
|AzCopy | [Een BLOB-container of-bestands share maken met behulp van AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Hadoop-container (HDFS) opdracht regel interface (CLI) met HDInsight |[Een container maken met behulp van HDFS met HDInsight](data-lake-storage-use-hdfs-data-lake-storage.md#create-a-container) |
|Code in een Azure Databricks notitie blok|[Een opslag account container maken (scala)](data-lake-storage-quickstart-create-databricks-account.md#create-storage-account-container) <br><br> [Een container maken en koppelen (python)](data-lake-storage-use-databricks-spark.md#create-a-container-and-mount-it)|

Het is eenvoudig om bestands systemen te maken met behulp van Storage Explorer of AzCopy. Het is wat meer werk om bestands systemen te maken met behulp van HDInsight en Databricks. Als u echter van plan bent HDInsight-of Databricks-clusters te gebruiken om uw gegevens toch te verwerken, kunt u eerst uw clusters maken en de HDFS CLI gebruiken voor het maken van bestands systemen.  

## <a name="grant-access-to-the-data"></a>Toegang tot de gegevens verlenen

Stel de juiste toegangs machtigingen voor uw account en de gegevens in uw account in, voordat u begint met het opnemen van gegevens.

Er zijn drie manieren om toegang te verlenen:

* Een van deze rollen toewijzen aan een gebruiker, groep, door de gebruiker beheerde identiteit of Service-Principal:

  [Gegevens lezer van BLOB voor opslag](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Inzender voor Storage BLOB-gegevens](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Eigenaar van gegevens van opslag-BLOB](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Gebruik een Shared Access Signature-token (SAS).

* Gebruik een sleutel voor het opslag account.

In deze tabel ziet u hoe u toegang verleent voor elke Azure-service of elk hulp programma.

|Hulpprogramma | Toegang verlenen | Richtlijnen |
|---|--|---|
|Storage Explorer| Een rol toewijzen aan gebruikers en groepen | [Beheerders-en niet-beheerders rollen toewijzen aan gebruikers met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Een rol toewijzen aan gebruikers en groepen <br>**or**<br> Een SAS-token gebruiken| [Beheerders-en niet-beheerders rollen toewijzen aan gebruikers met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Maak eenvoudig een SAS om een bestand te downloaden van Azure Storage-Azure Storage Explorer gebruiken](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Een rol toewijzen aan een door de gebruiker toegewezen beheerde identiteit | [Een HDInsight-cluster met Data Lake Storage Gen2 maken](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Een rol toewijzen aan een door de gebruiker toegewezen identiteit<br>**or**<br> Een rol toewijzen aan een Service-Principal<br>**or**<br> Een sleutel voor het opslag account gebruiken | [Eigenschappen van gekoppelde service](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Een rol toewijzen aan een door de gebruiker toegewezen beheerde identiteit | [Een HDInsight-cluster met Data Lake Storage Gen2 maken](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Een rol toewijzen aan een Service-Principal | [Procedure: Gebruik de portal voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Zie deze artikelen voor meer informatie over het verlenen van toegang tot specifieke bestanden en mappen.

* [Machtigingen voor bestanden en mapniveau instellen met behulp van Azure Storage Explorer met Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Toegangs beheer lijsten voor bestanden en mappen](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Zie [Azure data Lake Storage Gen2 Security Guide (Engelstalig](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)) voor meer informatie over het instellen van andere beveiligings aspecten.

## <a name="ingest-the-data"></a>De gegevens opnemen

In deze sectie worden de verschillende gegevens bronnen en de verschillende manieren beschreven waarop de gegevens kunnen worden opgenomen in een Data Lake Storage Gen2-account.

![Gegevens opnemen in data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Gegevens opnemen in data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc gegevens

Dit vertegenwoordigt kleinere gegevens sets die worden gebruikt voor het maken van een prototype van een big data-toepassing. Er zijn verschillende manieren om ad hoc gegevens op te nemen, afhankelijk van de bron van de gegevens. 

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om ad hoc gegevens op te nemen.

| Gegevensbron | Het opnemen met |
| --- | --- |
| Lokale computer |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Hulp programma AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Hulp programma AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp uitgevoerd op HDInsight-cluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Gestreamde gegevens

Dit duidt op gegevens die kunnen worden gegenereerd door verschillende bronnen, zoals toepassingen, apparaten, Sens oren, enzovoort. Deze gegevens kunnen worden opgenomen in Data Lake Sorage Gen2 door diverse hulpprogram ma's. In deze hulpprogram ma's worden de gegevens doorgaans in realtime vastgelegd en verwerkt, en worden de gebeurtenissen in batches naar Data Lake Storage Gen2 geschreven, zodat ze verder kunnen worden verwerkt.

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om gestreamde gegevens op te nemen.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure HDInsight Storm | [Schrijven naar Apache Hadoop HDFS van Apache Storm op HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relationele gegevens

U kunt ook gegevens van relationele data bases bron. Over een bepaalde tijd verzamelen relationele data bases enorme hoeveel heden gegevens die belang rijke inzichten kunnen geven als ze worden verwerkt via een big data pijp lijn. U kunt de volgende hulpprogram ma's gebruiken om dergelijke gegevens naar Data Lake Storage Gen2 te verplaatsen.

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken voor het opnemen van relationele gegevens.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webserver logboek gegevens (uploaden met aangepaste toepassingen)

Dit type gegevensset wordt specifiek genoemd, omdat analyse van webserver logboek gegevens een veelvoorkomende use-case is voor big data toepassingen en grote hoeveel heden logboek bestanden moeten worden geüpload naar Data Lake Storage Gen2. U kunt een van de volgende hulpprogram ma's gebruiken om uw eigen scripts of toepassingen te schrijven voor het uploaden van gegevens.

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om webserver logboek gegevens op te nemen.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Voor het uploaden van webserver-logboek gegevens, en voor het uploaden van andere soorten gegevens (bijvoorbeeld Social gevoel-gegevens), is het een goede benadering om uw eigen aangepaste scripts/toepassingen te schrijven, omdat u hiermee de flexibiliteit hebt om uw gegevens te uploaden die onderdeel zijn van uw grotere big data-toepassing. In sommige gevallen kan deze code de vorm hebben van een script of een eenvoudig opdracht regel programma. In andere gevallen kan de code worden gebruikt voor de integratie van big data verwerking in een bedrijfs toepassing of oplossing.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Gegevens die zijn gekoppeld aan Azure HDInsight-clusters

De meeste HDInsight-cluster typen (Hadoop, HBase, Storm) ondersteunen Data Lake Storage Gen2 als opslag plaats voor gegevens. HDInsight-clusters hebben toegang tot gegevens van Azure Storage-blobs (WASB). Voor betere prestaties kunt u de gegevens van WASB kopiëren naar een Data Lake Storage Gen2-account dat aan het cluster is gekoppeld. U kunt de volgende hulpprogram ma's gebruiken om de gegevens te kopiëren.

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om gegevens op te nemen die zijn gekoppeld aan HDInsight-clusters.

|Hulpprogramma | Richtlijnen |
|---|--|
|Apache DistCp | [Gebruik DistCp om gegevens te kopiëren tussen Azure Storage blobs en Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Hulp programma AzCopy | [Gegevens overdragen met de AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Gegevens kopiëren van of naar Azure Data Lake Storage Gen2 met behulp van Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Gegevens die zijn opgeslagen in on-premises of IaaS Hadoop-clusters

Grote hoeveel heden gegevens kunnen worden opgeslagen in bestaande Hadoop-clusters, lokaal op computers met HDFS. De Hadoop-clusters kunnen zich in een on-premises implementatie bevinden of bevinden zich in een IaaS-cluster op Azure. Er kunnen vereisten zijn om dergelijke gegevens te kopiëren naar Azure Data Lake Storage Gen2 voor een eenmalige benadering of op een regel matige wijze. Er zijn verschillende opties die u kunt gebruiken om dit te krijgen. Hieronder vindt u een lijst met alternatieven en de bijbehorende afwegingen.

| Methode | Details | Voordelen | Overwegingen |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) gebruiken om gegevens rechtstreeks vanuit Hadoop-clusters naar Azure Data Lake Storage Gen2 te kopiëren |[ADF ondersteunt HDFS als een gegevens bron](../../data-factory/connector-hdfs.md) |ADF biedt kant-en-klare ondersteuning voor HDFS en de eerste klasse end-to-end-beheer en-bewaking |Vereist dat Data Management Gateway on-premises of in het IaaS-cluster wordt geïmplementeerd |
| Gebruik Distcp om gegevens van Hadoop naar Azure Storage te kopiëren. Kopieer vervolgens gegevens van Azure Storage naar Data Lake Storage Gen2 met behulp van het juiste mechanisme. |U kunt gegevens kopiëren van Azure Storage naar Data Lake Storage Gen2 met behulp van: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Hulp programma AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp uitgevoerd op HDInsight-clusters](data-lake-storage-use-distcp.md)</li></ul> |U kunt open source-hulpprogram ma's gebruiken. |Proces voor meerdere stappen waarbij meerdere technologieën zijn vereist |

### <a name="really-large-datasets"></a>Zeer grote gegevens sets

Voor het uploaden van gegevens sets die zich in verschillende terabytes bebereiken, kunnen de hierboven beschreven methoden langzaam en kostbaar zijn. In dergelijke gevallen kunt u Azure ExpressRoute gebruiken.  

Met Azure ExpressRoute kunt u particuliere verbindingen maken tussen Azure-data centers en infra structuur op uw locatie. Dit biedt een betrouw bare optie voor het overbrengen van grote hoeveel heden gegevens. Zie de [documentatie van Azure ExpressRoute](../../expressroute/expressroute-introduction.md)voor meer informatie.

## <a name="process-the-data"></a>De gegevens verwerken

Zodra de gegevens beschikbaar zijn in Data Lake Storage Gen2 kunt u analyses uitvoeren op die gegevens met behulp van de ondersteunde big data toepassingen. 

![Gegevens analyseren in data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "gegevens in data Lake Storage Gen2 analyseren")

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om taken voor gegevens analyse uit te voeren voor gegevens die zijn opgeslagen in Data Lake Storage Gen2.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure HDInsight | [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Snelstart: Gegevens in Azure Data Lake Storage Gen2 analyseren met behulp van Azure Databricks @ no__t-0<br><br>[Zelfstudie: Gegevens uitpakken, transformeren en laden met behulp van Azure Databricks @ no__t-0|

## <a name="visualize-the-data"></a>De gegevens visualiseren

U kunt een combi natie van Services gebruiken om visuele weer gaven te maken van gegevens die zijn opgeslagen in Data Lake Storage Gen2.

![Gegevens visualiseren in data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "gegevens visualiseren in data Lake Storage Gen2")

* U kunt beginnen met [Azure Data Factory om gegevens te verplaatsen van data Lake Storage Gen2 naar Azure SQL Data Warehouse](../../data-factory/copy-activity-overview.md)
* Daarna kunt u [Power bi integreren met Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) om een visuele weer gave van de gegevens te maken.

## <a name="download-the-data"></a>De gegevens downloaden

U kunt ook gegevens downloaden of verplaatsen van Azure Data Lake Storage Gen2 voor scenario's zoals:

* Verplaats gegevens naar andere opslag plaatsen naar de interface met uw bestaande gegevens verwerkings pijplijnen. Het is bijvoorbeeld mogelijk dat u gegevens wilt verplaatsen van Data Lake Storage Gen2 naar Azure SQL Database of een on-premises SQL Server.

* Down load gegevens naar uw lokale computer voor verwerking in IDE-omgevingen tijdens het maken van prototypen van toepassingen.

Uitgaande ![gegevens van data Lake Storage Gen2]uitgaand(./media/data-lake-storage-data-scenarios/egress-data.png "gegevens van data Lake Storage Gen2")

Hier volgt een lijst met hulpprogram ma's die u kunt gebruiken om gegevens te downloaden van Data Lake Storage Gen2.

|Hulpprogramma | Richtlijnen |
|---|--|
|Azure Data Factory | [Kopieeractiviteit in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Gebruik DistCp om gegevens te kopiëren tussen Azure Storage blobs en Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
