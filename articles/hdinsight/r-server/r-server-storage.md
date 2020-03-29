---
title: Azure-opslagoplossingen voor ML Services op HDInsight - Azure
description: Meer informatie over de verschillende opslagopties die beschikbaar zijn met ML Services op HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660236"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure-opslagoplossingen voor ML-services op Azure HDInsight

ML Services op HDInsight kunnen verschillende opslagoplossingen gebruiken om gegevens, code of objecten die resultaten uit analyse bevatten, te blijven bestaan. Deze oplossingen omvatten de volgende opties:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure-bestandsopslag](https://azure.microsoft.com/services/storage/files/)

U hebt ook de mogelijkheid om toegang te krijgen tot meerdere Azure-opslagaccounts of -containers met uw HDInsight-cluster. Azure File storage is een handige optie voor gegevensopslag voor gebruik op het randknooppunt waarmee u een Azure-opslagbestandsshare monteren op bijvoorbeeld het Linux-bestandssysteem. Maar Azure File-shares kunnen worden gemonteerd en gebruikt door elk systeem dat een ondersteund besturingssysteem heeft, zoals Windows of Linux.

Wanneer u een Apache Hadoop-cluster maakt in HDInsight, geeft u een **Azure Storage-account** of **Data Lake Storage op.** Een specifieke opslagcontainer van dat account bevat het bestandssysteem voor het cluster dat u maakt (bijvoorbeeld het Hadoop Distributed File System). Zie voor meer informatie en begeleiding:

- [Azure Storage gebruiken met HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Data Lake Storage gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Azure Blob-opslagaccounts gebruiken met het ML-cluster

Als u meer dan één opslagaccount hebt opgegeven bij het maken van uw ML Services-cluster, wordt in de volgende instructies uitgelegd hoe u een secundair account gebruikt voor gegevenstoegang en bewerkingen in een ML Services-cluster. Ga uit van de volgende opslagaccounts en container: **opslag1** en een standaardcontainer met de naam **container1**en **opslag2** met **container2**.

> [!WARNING]  
> Voor prestatiedoeleinden wordt het HDInsight-cluster gemaakt in hetzelfde datacenter als het primaire opslagaccount dat u opgeeft. Het gebruik van een opslagaccount op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>De standaardopslag gebruiken met ML Services op HDInsight

1. Maak met behulp van een SSH-client verbinding met het randknooppunt van uw cluster. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor informatie over het gebruik van SSH met HDInsight.
  
2. Kopieer een voorbeeldbestand, mysamplefile.csv, naar de map /share.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Schakel over naar R Studio of een andere R-console en schrijf R-code om het naamknooppunt in te stellen op **standaard** en locatie van het bestand dat u wilt openen.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Alle directory- en bestandsverwijzingen verwijzen `wasbs://container1@storage1.blob.core.windows.net`naar het opslagaccount . Dit is het **standaardopslagaccount** dat is gekoppeld aan het HDInsight-cluster.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Gebruik de extra opslag met ML Services op HDInsight

Stel dat u een bestand wilt verwerken dat mysamplefile1.csv wordt genoemd en dat zich in de /private directory van **container2** in **opslagbevindt2**.

Wijs in uw R-code de verwijzing naar het **opslagaccount** van het naamknooppunt.

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Alle directory- en bestandsverwijzingen verwijzen nu `wasbs://container2@storage2.blob.core.windows.net`naar het opslagaccount . Dit is het **naamknooppunt** dat u hebt opgegeven.

Configureer `/user/RevoShare/<SSH username>` de map op **storage2** als volgt:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>Azure Data Lake Storage gebruiken met het ML Services-cluster

Als u Data Lake Storage wilt gebruiken met uw HDInsight-cluster, moet u uw cluster toegang geven tot elke Azure Data Lake-opslag die u wilt gebruiken. Zie [Een HDInsight-cluster](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)maken met Data Lake Storage met Azure Portal voor instructies over het gebruik van de Azure Portal met een Azure Data Lake Storage-account als standaardopslag of als extra opslag.

U gebruikt vervolgens de opslag in uw R-script, net zoals u een secundair Azure-opslagaccount hebt gebruikt zoals beschreven in de vorige procedure.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Clustertoegang toevoegen aan uw Azure Data Lake-opslag

U hebt toegang tot Data Lake Storage met behulp van een Azure Active Directory (Azure AD) Service Principal die is gekoppeld aan uw HDInsight-cluster.

1. Wanneer u uw HDInsight-cluster maakt, selecteert u **Cluster AAD-identiteit** op het tabblad **Gegevensbron.**

2. Selecteer in het dialoogvenster **Cluster-AAD-identiteit** onder **AD-serviceprincipal selecteren**de optie **Nieuw maken**.

Nadat u de Service Principal een naam hebt gegeven en er een wachtwoord voor hebt gemaakt, klikt u op **ADLS-toegang beheren** om de Service Principal te koppelen aan uw Data Lake Storage.

Het is ook mogelijk om clustertoegang toe te voegen aan een of meer Data Lake-opslagaccounts na het maken van een cluster. Open de Azure-portalvermelding voor een Data Lake-opslag en ga naar **Data Explorer > Access > Toevoegen**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Toegang tot Data Lake Storage Gen1 vanuit ML Services op HDInsight

Zodra u toegang hebt gegeven tot Data Lake Storage Gen1, u de opslag in het ML Services-cluster op HDInsight gebruiken zoals u een secundair Azure-opslagaccount zou maken. Het enige verschil is dat het voorvoegsel **wasbs://** als volgt in **adl://** verandert:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

De volgende opdrachten worden gebruikt om het Data Lake Storage Gen1-account te configureren met de RevoShare-map en het voorbeeld .csv-bestand uit het vorige voorbeeld toe te voegen:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Azure-bestandsopslag gebruiken met ML-services op HDInsight

Er is ook een handige optie voor gegevensopslag voor gebruik op het randknooppunt genaamd [Azure Files.](https://azure.microsoft.com/services/storage/files/) Hiermee u een Azure Storage-bestandsshare aan het Linux-bestandssysteem monteren. Deze optie kan handig zijn voor het opslaan van gegevensbestanden, R-scripts en resultaatobjecten die later nodig kunnen zijn, vooral wanneer het zinvol is om het native bestandssysteem op het randknooppunt te gebruiken in plaats van HDFS.

Een groot voordeel van Azure Files is dat de bestandsshares kunnen worden gemonteerd en gebruikt door elk systeem dat een ondersteund besturingssysteem heeft, zoals Windows of Linux. Het kan bijvoorbeeld worden gebruikt door een ander HDInsight-cluster dat u of iemand in uw team heeft, door een Azure-vm of zelfs door een on-premises systeem. Zie voor meer informatie:

- [Azure-bestandsopslag gebruiken met Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Azure-bestandsopslag gebruiken in Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van het ML Services-cluster op HDInsight](r-server-overview.md)
- [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
- [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
