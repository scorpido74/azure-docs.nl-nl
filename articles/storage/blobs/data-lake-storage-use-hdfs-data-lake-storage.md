---
title: De HDFS CLI gebruiken met Azure Data Lake Storage Gen2
description: Inleiding tot HDFS CLI voor Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992223"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>De HDFS CLI gebruiken met Data Lake Storage Gen2

U de gegevens in uw opslagaccount openen en beheren met behulp van een opdrachtregelinterface, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) In dit artikel vindt u enkele voorbeelden waarmee u aan de slag.

HDInsight biedt toegang tot de gedistribueerde container die lokaal is gekoppeld aan de compute nodes. U toegang krijgen tot deze container met behulp van de shell die rechtstreeks interageert met de HDFS en de andere bestandssystemen die Hadoop ondersteunt.

Zie voor meer informatie over HDFS CLI de [officiële documentatie](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) en de [HDFS-machtigingengids](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Als u Azure Databricks gebruikt in plaats van HDInsight en u wilt communiceren met uw gegevens met behulp van een opdrachtregelinterface, u de Databricks CLI gebruiken om te communiceren met het Databricks-bestandssysteem. Zie [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Gebruik de HDFS CLI met een HDInsight Hadoop cluster op Linux

Ten eerste, vast te stellen [toegang op afstand tot diensten](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Als u [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) kiest, ziet de PowerShell-code er als volgt uit:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
De verbindingstekenreeks is te vinden op het gedeelte 'SSH + Cluster login' van het HDInsight-clusterblad in Azure-portal. SSH-referenties zijn opgegeven op het moment van het maken van het cluster.

>[!IMPORTANT]
>HDInsight-clusterfacturering wordt gestart nadat een cluster is gemaakt en stopt wanneer het cluster wordt verwijderd. De facturering wordt pro-rato per minuut berekend, dus u moet altijd uw cluster verwijderen wanneer het niet meer wordt gebruikt. Zie ons artikel over het [onderwerp](../../hdinsight/hdinsight-delete-cluster.md)voor meer informatie over het verwijderen van een cluster. Gegevens die zijn opgeslagen in een opslagaccount met Data Lake Storage Gen2 ingeschakeld, blijven echter bestaan, zelfs nadat een HDInsight-cluster is verwijderd.

## <a name="create-a-container"></a>Een container maken

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Vervang `<container-name>` de tijdelijke aanduiding door de naam die u uw container wilt geven.

* Vervang de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

## <a name="get-a-list-of-files-or-directories"></a>Een lijst met bestanden of mappen opvragen

    hdfs dfs -ls <path>

Vervang `<path>` de tijdelijke aanduiding door de URI van de container of containermap.

Bijvoorbeeld: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Een map maken

    hdfs dfs -mkdir [-p] <path>

Vervang `<path>` de tijdelijke aanduiding door de naam van de hoofdcontainer of een map in uw container.

Bijvoorbeeld: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Een bestand of map verwijderen

    hdfs dfs -rm <path>

Vervang `<path>` de tijdelijke aanduiding door de URI van het bestand of de map die u wilt verwijderen.

Bijvoorbeeld: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>De Toegangscontrolelijsten (ACL's) van bestanden en mappen weergeven

    hdfs dfs -getfacl [-R] <path>

Voorbeeld:

`hdfs dfs -getfacl -R /dir`

Zie [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>ACL's van bestanden en mappen instellen

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Voorbeeld:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Zie [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>De eigenaar van bestanden wijzigen

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Zie [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Groepskoppeling van bestanden wijzigen

    hdfs dfs -chgrp [-R] <group> <URI>

Zie [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>De machtigingen voor bestanden wijzigen

    hdfs dfs -chmod [-R] <mode> <URI>

Zie [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

U de volledige lijst met opdrachten bekijken op de [Apache Hadoop 2.4.1 File System Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) Website.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Data Lake Storage Gen2-account gebruiken in Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Meer informatie over toegangscontrolelijsten voor bestanden en mappen](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
