---
title: Extra Azure Storage-accounts toevoegen aan HDInsight
description: Meer informatie over het toevoegen van extra Azure-opslag accounts aan een bestaand HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 86b9230dbdca82c5599c1839fd64bd3df4725051
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435580"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Extra opslag accounts toevoegen aan HDInsight

Meer informatie over het gebruik van script acties om extra Azure Storage- *accounts* toe te voegen aan HDInsight. Met de stappen in dit document voegt u een opslag *account* toe aan een bestaand HDInsight-cluster op basis van Linux. Dit artikel is van toepassing op opslag *accounts* (niet op het standaard cluster-opslag account) en niet op extra opslag, zoals [Azure data Lake Storage gen1](hdinsight-hadoop-use-data-lake-store.md) en [Azure data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> De informatie in dit document is over het toevoegen van extra opslag accounts aan een cluster nadat het is gemaakt. Voor informatie over het toevoegen van opslag accounts tijdens het maken van een cluster, raadpleegt [u clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Vereisten

* Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Naam en sleutel van het opslag account. Zie [toegangs sleutels voor opslag accounts beheren](../storage/common/storage-account-keys-manage.md).
* De naam van het [juiste cluster](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name)is in gebruik.
* Als u Power shell gebruikt, hebt u de AZ-module nodig.  Zie [overzicht van Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [Azure-opdracht regel interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Als u bash of een Windows-opdracht prompt gebruikt, hebt u ook **JQ**, een opdracht regel-JSON-processor nodig.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Zie [Windows-subsysteem voor Linux-installatie handleiding voor Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)voor bash op Ubuntu in Windows 10.

## <a name="how-it-works"></a>Het werkt als volgt

Voor dit script worden de volgende para meters gebruikt:

* __Azure Storage-account naam__: de naam van het opslag account dat aan het HDInsight-cluster moet worden toegevoegd. Nadat het script is uitgevoerd, kan HDInsight gegevens lezen en schrijven die zijn opgeslagen in dit opslag account.

* __Sleutel voor het Azure-opslag account__: een sleutel die toegang verleent aan het opslag account.

* __-p__ (optioneel): als deze is opgegeven, wordt de sleutel niet versleuteld en wordt deze in het bestand bestand core-site. xml opgeslagen als tekst zonder opmaak.

Tijdens de verwerking voert het script de volgende acties uit:

* Als het opslag account al aanwezig is in de configuratie van bestand core-site. XML voor het cluster, wordt het script afgesloten en worden er geen verdere acties uitgevoerd.

* Verifieert of het opslag account bestaat en toegankelijk is via de sleutel.

* Hiermee versleutelt u de sleutel met de cluster referentie.

* Voegt het opslag account toe aan het bestand bestand core-site. XML.

* Stopt en start de [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)en [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) -services opnieuw. Als u deze services stopt en start, kunnen ze het nieuwe opslag account gebruiken.

> [!WARNING]  
> Het gebruik van een opslag account op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

## <a name="the-script"></a>Het script

__Locatie__van het Script: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Vereisten__: het script moet worden toegepast op de __hoofd knooppunten__. U hoeft dit script niet te markeren als __persistent__, omdat het de Ambari-configuratie voor het cluster direct bijwerkt.

## <a name="to-use-the-script"></a>Het script gebruiken

Dit script kan worden gebruikt vanuit de Azure PowerShell, Azure CLI of de Azure Portal.

### <a name="powershell"></a>PowerShell

Met [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Vervang `CLUSTERNAME`, `ACCOUNTNAME`en `ACCOUNTKEY` met de juiste waarden.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure-CLI

Met [AZ hdinsight script-Action Execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Vervang `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`en `ACCOUNTKEY` door de juiste waarden.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure Portal

Zie [een script actie Toep assen op een actief cluster](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Bekende problemen

### <a name="storage-firewall"></a>Opslag firewall

Als u ervoor kiest om uw opslag account te beveiligen met de **firewalls en beperkingen voor virtuele netwerken** op **geselecteerde netwerken**, moet u de uitzonde ring inschakelen **vertrouwde micro soft-Services toestaan...** zodat HDInsight toegang kan krijgen tot uw opslag account.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Opslag accounts die niet worden weer gegeven in Azure Portal of hulpprogram ma's

Bij het weer geven van het HDInsight-cluster in de Azure Portal, het selecteren van de vermelding __opslag accounts__ onder __Eigenschappen__ , worden geen opslag accounts weer gegeven die via deze script actie zijn toegevoegd. Het extra opslag account wordt niet weer gegeven in Azure PowerShell en Azure CLI.

De opslag informatie wordt niet weer gegeven omdat het script alleen de bestand core-site. xml-configuratie voor het cluster wijzigt. Deze informatie wordt niet gebruikt bij het ophalen van de cluster gegevens met behulp van Azure-beheer-Api's.

Als u informatie over het opslag account wilt weer geven die met dit script aan het cluster is toegevoegd, gebruikt u de Ambari-REST API. Gebruik de volgende opdrachten om deze informatie voor uw cluster op te halen:

### <a name="powershell"></a>PowerShell

Vervang `CLUSTERNAME` door de juiste cluster naam. Vervang `ACCOUNTNAME` door de werkelijke namen. Wanneer u hierom wordt gevraagd, voert u het wacht woord voor de cluster aanmelding in.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Vervang `CLUSTERNAME` door de juiste cluster naam. Vervang `PASSWORD` door het wacht woord van de Cluster beheerder. Vervang `STORAGEACCOUNT` door de werkelijke naam van het opslag account.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Vervang `CLUSTERNAME` door de juiste cluster naam in beide scripts. Bepaal eerst welke service-config-versie in gebruik is door de onderstaande opdracht in te voeren:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Vervang `ACCOUNTNAME` door de werkelijke naam van het opslag account. Vervang `4` door de daad werkelijke service configuratie versie en voer de volgende opdracht in:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

De gegevens die door deze opdracht worden geretourneerd, worden weer gegeven zoals in de volgende tekst:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Deze tekst is een voor beeld van een versleutelde sleutel, die wordt gebruikt voor toegang tot het opslag account.

### <a name="unable-to-access-storage-after-changing-key"></a>Kan geen toegang krijgen tot de opslag na het wijzigen van de sleutel

Als u de sleutel voor een opslag account wijzigt, heeft HDInsight geen toegang meer tot het opslag account. HDInsight maakt gebruik van een in cache opgeslagen kopie van de sleutel in de bestand core-site. XML voor het cluster. Deze kopie in de cache moet worden bijgewerkt om overeen te komen met de nieuwe sleutel.

Als de script actie opnieuw wordt uitgevoerd, wordt de sleutel __niet__ bijgewerkt, omdat er door het script wordt gecontroleerd of er al een vermelding voor het opslag account bestaat. Als er al een vermelding bestaat, worden er geen wijzigingen aangebracht.

U kunt dit probleem omzeilen door de bestaande vermelding voor het opslag account te verwijderen. Gebruik de volgende stappen om de bestaande vermelding te verwijderen:

> [!IMPORTANT]  
> Het draaien van de opslag sleutel voor het primaire opslag account dat aan een cluster is gekoppeld, wordt niet ondersteund.

1. Open in een webbrowser de Ambari-webgebruikersinterface voor uw HDInsight-cluster. De URI is `https://CLUSTERNAME.azurehdinsight.net`. Vervang `CLUSTERNAME` door de naam van uw cluster.

    Wanneer u hierom wordt gevraagd, voert u de HTTP-aanmeldings gebruiker en het wacht woord voor uw cluster in.

2. Selecteer in de lijst met Services links op de pagina __HDFS__. Selecteer vervolgens het tabblad __configuraties__ in het midden van de pagina.

3. Voer in het veld __filter...__ de waarde __FS. Azure. account__in. Dit retourneert vermeldingen voor eventuele extra opslag accounts die zijn toegevoegd aan het cluster. Er zijn twee soorten vermeldingen. __sleutel en code__. Beide bevatten de naam van het opslag account als onderdeel van de sleutel naam.

    Hieronder ziet u voor beelden van vermeldingen voor een opslag account met de naam __mijn opslag__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Nadat u de sleutels hebt geïdentificeerd voor het opslag account dat u wilt verwijderen, gebruikt u het rode-pictogram rechts van de vermelding om deze te verwijderen. Gebruik vervolgens de knop __Opslaan__ om uw wijzigingen op te slaan.

5. Nadat de wijzigingen zijn opgeslagen, gebruikt u de script actie om het opslag account en de nieuwe sleutel waarde toe te voegen aan het cluster.

### <a name="poor-performance"></a>Slechte prestaties

Als het opslag account zich in een andere regio bevindt dan het HDInsight-cluster, kan dat leiden tot slechte prestaties. Toegang tot gegevens in een andere regio verzendt netwerk verkeer buiten het regionale Azure-data centrum en op het open bare Internet, waardoor latentie kan worden geïntroduceerd.

### <a name="additional-charges"></a>Extra kosten

Als het opslag account zich in een andere regio bevindt dan het HDInsight-cluster, kunt u extra uitgangs kosten in rekening worden gebracht voor uw Azure-facturering. Er wordt een uitgangs kosten toegepast wanneer gegevens een regionaal Data Center verlaten. Deze kosten worden toegepast, zelfs als het verkeer bestemd is voor een ander Azure Data Center in een andere regio.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u extra opslag accounts toevoegt aan een bestaand HDInsight-cluster. Zie [HDInsight-clusters op basis van Linux aanpassen met script actie](hdinsight-hadoop-customize-cluster-linux.md) voor meer informatie over script acties
