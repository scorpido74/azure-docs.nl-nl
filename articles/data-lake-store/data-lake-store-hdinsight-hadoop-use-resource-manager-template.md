---
title: Azure-sjablonen gebruiken om HDInsight te maken met Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Azure Resource Manager-sjablonen gebruiken om HDInsight-clusters te maken en te gebruiken met Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b09ca2cc358107c5f95fe3426351d380380db3c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161375"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Een HDInsight-cluster maken met Azure Data Lake Storage Gen1 met azure resource manager-sjabloon
> [!div class="op_single_selector"]
> * [Portal gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell gebruiken (voor standaardopslag)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell gebruiken (voor extra opslag)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resourcebeheer gebruiken](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Meer informatie over het gebruik van Azure PowerShell om een HDInsight-cluster met Azure Data Lake Storage Gen1 te configureren **als extra opslag.**

Voor ondersteunde clustertypen kan Data Lake Storage Gen1 worden gebruikt als standaardopslag- of extra opslagaccount. Wanneer Data Lake Storage Gen1 wordt gebruikt als extra opslag, is het standaardopslagaccount voor de clusters nog steeds Azure Storage Blobs (WASB) en worden de clustergerelateerde bestanden (zoals logboeken, enz.) nog steeds naar de standaardopslag geschreven, terwijl de gegevens die u wilt proces kan worden opgeslagen in een Data Lake Storage Gen1-account. Het gebruik van Data Lake Storage Gen1 als een extra opslagaccount heeft geen invloed op de prestaties of de mogelijkheid om te lezen/schrijven naar de opslag van het cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Data Lake Storage Gen1 gebruiken voor HDInsight-clusteropslag

Hier volgen enkele belangrijke overwegingen voor het gebruik van HDInsight met Data Lake Storage Gen1:

* Optie om HDInsight-clusters te maken met toegang tot Data Lake Storage Gen1 als standaardopslag is beschikbaar voor HDInsight-versie 3.5 en 3.6.

* Optie om HDInsight-clusters te maken met toegang tot Data Lake Storage Gen1, omdat extra opslag beschikbaar is voor HDInsight-versies 3.2, 3.4, 3.5 en 3.6.

In dit artikel voorzien we een Hadoop-cluster met Data Lake Storage Gen1 als extra opslag. Zie [Een HDInsight-cluster](data-lake-store-hdinsight-hadoop-use-portal.md)maken met Data Lake Storage Gen1 met Azure Portal voor instructies over het maken van een Hadoop-cluster met Data Lake Storage Gen1.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 of hoger**. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview).
* **Azure Active Directory Service Principal**. Stappen in deze zelfstudie geven instructies voor het maken van een serviceprincipal in Azure AD. U moet echter een Azure AD-beheerder zijn om een serviceprincipal te kunnen maken. Als u een Azure AD-beheerder bent, u deze vereiste overslaan en doorgaan met de zelfstudie.

    **Als u geen Azure AD-beheerder bent,** u niet de stappen uitvoeren die nodig zijn om een serviceprincipal te maken. In een dergelijk geval moet uw Azure AD-beheerder eerst een serviceprincipal maken voordat u een HDInsight-cluster maken met Data Lake Storage Gen1. Ook moet de serviceprincipal worden gemaakt met behulp van een certificaat, zoals beschreven bij [Een serviceprincipal maken met certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Een HDInsight-cluster maken met Data Lake Storage Gen1
De sjabloon Resource Manager en de vereisten voor het gebruik van de sjabloon zijn beschikbaar op GitHub bij [Een HDInsight Linux-cluster implementeren met nieuwe Data Lake Storage Gen1.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) Volg de instructies op deze link om een HDInsight-cluster te maken met Data Lake Storage Gen1 als extra opslag.

De instructies op de bovenstaande link vereisen PowerShell. Voordat u met deze instructies begint, moet u zich aanmelden bij uw Azure-account. Open vanaf uw bureaublad een nieuw Azure PowerShell-venster en voer de volgende fragmenten in. Wanneer u wordt gevraagd om in te loggen, moet u zich aanmelden als een van de beheerders/eigenaren van het abonnement:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

De sjabloon implementeert deze resourcetypen:

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Voorbeeldgegevens uploaden naar Data Lake Storage Gen1
Met de sjabloon ResourceManager wordt een nieuw Data Lake Storage Gen1-account gemaakt en wordt deze gekoppeld aan het HDInsight-cluster. U moet nu enkele voorbeeldgegevens uploaden naar Data Lake Storage Gen1. U hebt deze gegevens later in de zelfstudie nodig om taken uit te voeren vanuit een HDInsight-cluster die toegang heeft tot gegevens in het Data Lake Storage Gen1-account. Zie [Een bestand uploaden naar uw Data Lake Storage Gen1-account](data-lake-store-get-started-portal.md#uploaddata)voor instructies over het uploaden van gegevens. Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken.

## <a name="set-relevant-acls-on-the-sample-data"></a>Relevante ACL's instellen op de voorbeeldgegevens
Om ervoor te zorgen dat de voorbeeldgegevens die u uploadt, toegankelijk zijn vanuit het HDInsight-cluster, moet u ervoor zorgen dat de Azure AD-toepassing die wordt gebruikt om de identiteit tussen het HDInsight-cluster en Data Lake Storage Gen1 vast te stellen, toegang heeft tot het bestand/de map die u probeert te openen. Voer hiervoor de volgende stappen uit.

1. Zoek de naam van de Azure AD-toepassing die is gekoppeld aan het HDInsight-cluster en het Data Lake Storage Gen1-account. Een manier om naar de naam te zoeken, is door het HDInsight-clusterblad te openen dat u hebt gemaakt met de sjabloon Resourcebeheer, op het tabblad **Cluster-AAD-identiteit** te klikken en te zoeken naar de waarde van **serviceprincipalweergavenaam**.
2. Geef nu toegang tot deze Azure AD-toepassing in het bestand/de map die u vanuit het HDInsight-cluster wilt openen. Zie Gegevens beveiligen in Data Lake Storage Gen1 als u de juiste ACL's in het bestand/map wilt instellen in Data Lake Storage [Gen1.](data-lake-store-secure-data.md#filepermissions)

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Testtaken uitvoeren op het HDInsight-cluster om Data Lake Storage Gen1 te gebruiken
Nadat u een HDInsight-cluster hebt geconfigureerd, u testtaken op het cluster uitvoeren om te testen of het HDInsight-cluster toegang heeft tot Data Lake Storage Gen1. Hiervoor voeren we een voorbeeldhive-taak uit die een tabel maakt met behulp van de voorbeeldgegevens die u eerder hebt geüpload naar uw Data Lake Storage Gen1-account.

In deze sectie wordt u SSH in een HDInsight Linux cluster en voer de a sample Hive query. Als u een Windows-client gebruikt, raden we u aan [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) **PuTTY**te gebruiken, die kan worden gedownload van .

Zie [SSH gebruiken met Het op Linux gebaseerde Hadoop op HDInsight van Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)voor meer informatie over het gebruik van PuTTY.

1. Start de Hive CLI na verbinding met de volgende opdracht:

   ```
   hive
   ```
2. Voer met de CLI de volgende instructies in om een nieuwe tabel met de naam **voertuigen** te maken met behulp van de voorbeeldgegevens in Data Lake Storage Gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Toegang tot Data Lake Storage Gen1 met HDFS-opdrachten
Zodra u het HDInsight-cluster hebt geconfigureerd om Data Lake Storage Gen1 te gebruiken, u de opdrachten voor HDFS-shell gebruiken om toegang te krijgen tot het archief.

In deze sectie wordt u ssh in een HDInsight Linux cluster en voer de HDFS commando's. Als u een Windows-client gebruikt, raden we u aan [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) **PuTTY**te gebruiken, die kan worden gedownload van .

Zie [SSH gebruiken met Het op Linux gebaseerde Hadoop op HDInsight van Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)voor meer informatie over het gebruik van PuTTY.

Gebruik, eenmaal verbonden, de volgende opdracht HDFS-bestandssysteem om de bestanden in het Data Lake Storage Gen1-account weer te geven.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Hier vindt u een overzicht van het bestand dat u eerder hebt geüpload naar Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

U de `hdfs dfs -put` opdracht ook gebruiken om bepaalde bestanden te `hdfs dfs -ls` uploaden naar Data Lake Storage Gen1 en vervolgens te controleren of de bestanden zijn geüpload.


## <a name="next-steps"></a>Volgende stappen
* [Gegevens uit Azure Storage Blobs kopiëren naar Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
