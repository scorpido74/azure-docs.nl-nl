---
title: Extra Azure Storage-accounts toevoegen aan HDInsight
description: Meer informatie over het toevoegen van extra Azure Storage-accounts aan een bestaand HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206704"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Extra opslagaccounts toevoegen aan HDInsight

Meer informatie over het gebruik van scriptacties om extra Azure *Storage-accounts* toe te voegen aan HDInsight. De stappen in dit document voegen een *opslagaccount* toe aan een bestaand HDInsight-cluster. Dit artikel is van toepassing op *opslagaccounts* (niet het standaard clusteropslagaccount) en niet op extra opslag zoals [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) en Azure Data Lake Storage [Gen2.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

> [!IMPORTANT]  
> De informatie in dit document gaat over het toevoegen van extra opslagaccount(s) aan een cluster nadat het is gemaakt. Zie [Clusters instellen in HDInsight met Apache Hadoop, Apache Spark, Apache Kafka en meer voor](hdinsight-hadoop-provision-linux-clusters.md)informatie over het toevoegen van opslagaccounts tijdens het maken van clusters in HDInsight.

## <a name="prerequisites"></a>Vereisten

* Een Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Naam en sleutel van het opslagaccount. Zie [Toegangssleutels voor opslagaccount beheren](../storage/common/storage-account-keys-manage.md).
* Als u PowerShell gebruikt, hebt u de AZ-module nodig.  Zie [Overzicht van Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Hoe werkt het?

Tijdens de verwerking voert het script de volgende acties uit:

* Als het opslagaccount al bestaat in de configuratie core-site.xml voor het cluster, worden de scriptuitgangen en worden er geen verdere acties uitgevoerd.

* Controleert of het opslagaccount bestaat en toegankelijk is met de sleutel.

* Versleutelt de sleutel met behulp van de clusterreferentie.

* Hiermee voegt u het opslagaccount toe aan het bestand core-site.xml.

* Stopt en herstart de [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), Apache [Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)en [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) services. Met het stoppen en starten van deze services kunnen ze het nieuwe opslagaccount gebruiken.

> [!WARNING]  
> Het gebruik van een opslagaccount op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

## <a name="add-storage-account"></a>Opslagaccount toevoegen

Gebruik [Scriptactie](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) om de wijzigingen met de volgende overwegingen toe te passen:

|Eigenschap | Waarde |
|---|---|
|Bash script URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Knooppunttype(s)|Head|
|Parameters|`ACCOUNTNAME``ACCOUNTKEY` `-p` (facultatief)|

* `ACCOUNTNAME`is de naam van het opslagaccount dat moet worden toegevoegd aan het HDInsight-cluster.
* `ACCOUNTKEY`is de toegangssleutel voor `ACCOUNTNAME`.
* `-p` is optioneel. Indien opgegeven, wordt de sleutel niet versleuteld en wordt deze opgeslagen in het bestand core-site.xml als platte tekst.

## <a name="verification"></a>Verificatie

Wanneer u het HDInsight-cluster in de Azure-portal bekijkt, selecteert u de vermelding __Opslagaccounts__ onder __Eigenschappen__ geen opslagaccounts die via deze scriptactie zijn toegevoegd. Azure PowerShell en Azure CLI geven het extra opslagaccount ook niet weer. De opslaggegevens worden niet weergegeven omdat het script `core-site.xml` alleen de configuratie voor het cluster wijzigt. Deze informatie wordt niet gebruikt bij het ophalen van de clustergegevens met Azure-beheer-API's.

Gebruik een van de onderstaande methoden om de extra opslag te verifiëren:

### <a name="powershell"></a>PowerShell

Het script retourneert de naam(en) van het opslagaccount die aan het opgegeven cluster is gekoppeld. Vervang `CLUSTERNAME` de werkelijke clusternaam en voer het script uit.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Navigeer naar **HDFS** > **Configs** > **Advanced** > **Custom-kernsite**.

1. Let op de `fs.azure.account.key`toetsen die beginnen met . De accountnaam maakt deel uit van de sleutel zoals te zien is in deze voorbeeldafbeelding:

   ![verificatie via Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Opslagaccount verwijderen

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Navigeer naar **HDFS** > **Configs** > **Advanced** > **Custom-kernsite**.

1. Verwijder de volgende toetsen:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Nadat u deze sleutels hebt verwijderd en de configuratie hebt opgeslagen, moet u Oozie, Yarn, MapReduce2, HDFS en Hive één voor één opnieuw starten.

## <a name="known-issues"></a>Bekende problemen

### <a name="storage-firewall"></a>Opslagfirewall

Als u ervoor kiest uw opslagaccount te beveiligen met de beperkingen **firewalls en virtuele netwerken** op **geselecteerde netwerken,** moet u de uitzondering **Vertrouwde Microsoft-services toestaan inschakelen...** zodat HDInsight toegang heeft tot uw opslagaccount.

### <a name="unable-to-access-storage-after-changing-key"></a>Geen toegang tot opslag na het wijzigen van de sleutel

Als u de sleutel voor een opslagaccount wijzigt, heeft HDInsight geen toegang meer tot het opslagaccount. HDInsight gebruikt een in de cache opgeslagen kopie van de sleutel in de core-site.xml voor het cluster. Deze kopie in de cache moet worden bijgewerkt om overeen te komen met de nieuwe sleutel.

Als u de scriptactie opnieuw uitvoert, wordt de toets __niet__ bijgewerkt, omdat het script controleert of er al een vermelding voor het opslagaccount bestaat. Als een item al bestaat, worden er geen wijzigingen aangebracht.

Ga als volgt te werk om dit probleem op te lossen:  
1. Verwijder het opslagaccount.
1. Voeg het opslagaccount toe.

> [!IMPORTANT]  
> Het roteren van de opslagsleutel voor het primaire opslagaccount dat aan een cluster is gekoppeld, wordt niet ondersteund.

### <a name="poor-performance"></a>Slechte prestaties

Als het opslagaccount zich in een andere regio bevindt dan het HDInsight-cluster, u slechte prestaties ervaren. Toegang tot gegevens in een andere regio verzendt netwerkverkeer buiten het regionale Azure-datacenter en via het openbare internet, wat latentie kan introduceren.

### <a name="additional-charges"></a>Extra kosten

Als het opslagaccount zich in een andere regio bevindt dan het HDInsight-cluster, worden mogelijk extra kosten in rekening gebracht voor uw Azure-facturering. Er wordt een uitgangstoeslag toegepast wanneer gegevens een regionaal datacenter verlaten. Deze kosten worden toegepast, zelfs als het verkeer is bestemd voor een ander Azure-datacenter in een andere regio.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u extra opslagaccounts toevoegt aan een bestaand HDInsight-cluster. Zie [Linux-gebaseerde HDInsight-clusters aanpassen met scriptactie voor](hdinsight-hadoop-customize-cluster-linux.md) meer informatie over scriptacties.
