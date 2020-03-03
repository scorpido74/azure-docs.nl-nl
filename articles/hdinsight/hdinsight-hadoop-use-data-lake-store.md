---
title: Data Lake Storage Gen1 gebruiken met Hadoop in azure HDInsight
description: Meer informatie over het opvragen van gegevens uit Azure Data Lake Storage Gen1 en voor het opslaan van resultaten van de analyse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: fddffee001266b96bc341738293bbdb42115a978
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228192"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters

> [!Note]
> Implementeer nieuwe HDInsight-clusters met behulp van [Azure data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) voor verbeterde prestaties en nieuwe functies.

Als u gegevens in een HDInsight-cluster wilt analyseren, kunt u de gegevens opslaan in [Azure Storage](../storage/common/storage-introduction.md), [Azure data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)of [Azure data Lake Storage gen 2](../storage/blobs/data-lake-storage-introduction.md). Met alle opslag opties kunt u HDInsight-clusters die worden gebruikt voor berekeningen, veilig verwijderen zonder dat er gebruikers gegevens verloren gaan.

In dit artikel leert u hoe Data Lake Storage Gen1 werkt met HDInsight-clusters. Zie [Azure Storage gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-blob-storage.md) voor informatie over de werking van Azure Storage met HDInsight-clusters. Zie [Apache Hadoop clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een HDInsight-cluster.

> [!NOTE]  
> Data Lake Storage Gen1 wordt altijd geopend via een beveiligd kanaal, zodat er geen `adls` naam van het bestandssysteem schema is. U gebruikt altijd `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Beschik baarheid voor HDInsight-clusters

Apache Hadoop ondersteunt een principe van het standaard bestandssysteem. Het standaardbestandssysteem impliceert een standaardschema en instantie. De toepassing kan ook worden gebruikt om relatieve paden om te zetten. Tijdens het maken van het HDInsight-cluster kunt u een BLOB-container opgeven in Azure Storage als het standaard bestandssysteem of met HDInsight 3,5 en nieuwere versies, kunt u Azure Storage of Azure Data Lake Storage Gen1 als standaard bestanden systeem selecteren met een enkele uitzonde ringen. Houd er rekening mee dat het cluster en het opslag account moeten worden gehost in dezelfde regio.

HDInsight-clusters kunnen Data Lake Storage Gen1 op twee manieren gebruiken:

* als standaardopslag
* als extra opslag, met Azure Storage Blob als standaardopslag.

Vanaf nu kunnen slechts enkele typen HDInsight-clusters en-versies worden ondersteund met Data Lake Storage Gen1 als standaard opslag en extra opslag accounts:

| HDInsight-clustertype | Data Lake Storage Gen1 als standaard opslag | Data Lake Storage Gen1 als extra opslag ruimte| Opmerkingen |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight-versie 4,0 | Nee | Nee |ADLS Gen1 wordt niet ondersteund met HDInsight 4,0 |
| HDInsight-versie 3.6 | Ja | Ja | Met uitzondering van HBase|
| HDInsight-versie 3.5 | Ja | Ja | Met uitzondering van HBase|
| HDInsight-versie 3.4 | Nee | Ja | |
| HDInsight-versie 3.3 | Nee | Nee | |
| HDInsight-versie 3.2 | Nee | Ja | |
| Storm | | |U kunt Data Lake Storage Gen1 gebruiken om gegevens van een storm-topologie te schrijven. U kunt Data Lake Storage ook gebruiken voor referentie gegevens die vervolgens kunnen worden gelezen door een storm-topologie.|

> [!WARNING]  
> HDInsight HBase wordt niet ondersteund met Azure Data Lake Storage Gen1

Het gebruik van Data Lake Storage Gen1 als een extra opslag account heeft geen invloed op de prestaties of de mogelijkheid om te lezen van of te schrijven naar Azure Storage vanuit het cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1 als standaard opslag gebruiken

Wanneer HDInsight met Data Lake Storage Gen1 als standaard opslag is geïmplementeerd, worden de aan het cluster gerelateerde bestanden opgeslagen in `adl://mydatalakestore/<cluster_root_path>/`, waarbij `<cluster_root_path>` de naam is van een map die u in Data Lake Storage maakt. Als u voor elk cluster een pad naar een hoofdmap opgeeft, kunt u hetzelfde Data Lake Storage-account voor meer dan één cluster gebruiken. U kunt dus instellingen hebben waarbij:

* Cluster1 het pad `adl://mydatalakestore/cluster1storage` kan gebruiken
* Cluster2 het pad `adl://mydatalakestore/cluster2storage` kan gebruiken

U ziet dat beide clusters gebruikmaken van hetzelfde Data Lake Storage Gen1 account **mydatalakestore**. Elk cluster heeft toegang tot een eigen hoofd bestandssysteem in Data Lake Storage. In het Azure Portal-implementatiescenario wordt u gevraagd voor het pad naar de hoofdmap een mapnaam te gebruiken zoals **/clusters/\<clusternaam>** .

Om Data Lake Storage Gen1 als standaard opslag te kunnen gebruiken, moet u de Service-Principal toegang verlenen tot de volgende paden:

* De hoofdmap van het Data Lake Storage Gen1-account.  Bijvoorbeeld: adl://mydatalakestore/.
* De map voor alle mappen van het cluster.  Bijvoorbeeld: adl://mydatalakestore/clusters.
* De map voor het cluster.  Bijvoorbeeld: adl://mydatalakestore/clusters/cluster1storage.

Zie Data Lake Storage toegang configureren voor meer informatie over het maken van een Service-Principal en het verlenen van toegang.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Een certificaat uit Azure-sleutel kluis extra heren voor het maken van een cluster

Als u Azure Data Lake Storage Gen1 wilt instellen als uw standaard opslag voor een nieuw cluster en het certificaat voor uw service-principal is opgeslagen in Azure Key Vault, zijn er enkele extra stappen vereist om het certificaat te converteren naar de juiste indeling. De volgende code fragmenten laten zien hoe u de conversie kunt uitvoeren.

Down load eerst het certificaat van Key Vault en pak de `SecretValueText`uit.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Converteer vervolgens de `SecretValueText` naar een certificaat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Vervolgens kunt u de `$identityCertificate` gebruiken om een nieuw cluster te implementeren, zoals in het volgende code fragment:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1 als extra opslag gebruiken

U kunt Data Lake Storage Gen1 ook gebruiken als extra opslag ruimte voor het cluster. In dergelijke gevallen kan de standaard opslag van het cluster een Azure Storage Blob of een Data Lake Storage-account zijn. Als u HDInsight-taken uitvoert voor gegevens die zijn opgeslagen in Data Lake Storage als extra opslag ruimte, moet u het volledige pad naar de bestanden gebruiken. Bijvoorbeeld:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

U ziet dat er nu geen **cluster_root_path** in de URL is. Dat komt omdat Data Lake Storage in dit geval geen standaard opslag is. u hoeft alleen maar het pad naar de bestanden op te geven.

Als u een Data Lake Storage Gen1 als extra opslag ruimte wilt gebruiken, hoeft u alleen de Service-Principal toegang te verlenen tot de paden waar uw bestanden worden opgeslagen.  Bijvoorbeeld:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Zie Data Lake Storage toegang configureren voor meer informatie over het maken van een Service-Principal en het verlenen van toegang.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Meer dan één Data Lake Storage accounts gebruiken

Het toevoegen van een Data Lake Storage-account als extra en het toevoegen van meer dan een Data Lake Storage accounts wordt bereikt door het HDInsight-cluster toestemming te geven voor gegevens in een of meer Data Lake Storage-accounts. Zie Data Lake Storage toegang configureren.

## <a name="configure-data-lake-storage-access"></a>Data Lake Storage toegang configureren

Als u Data Lake Storage toegang vanaf uw HDInsight-cluster wilt configureren, moet u een service-principal voor Azure Active Directory (Azure AD) hebben. Alleen een Azure AD-beheerder kan een service-principal maken. De service-principal moet worden gemaakt met een certificaat. Zie voor meer informatie [Snelstartgids: Clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) en [Service-principal maken met zelfondertekend certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Als u Azure Data Lake Storage Gen1 als extra opslag voor HDInsight-cluster gaat gebruiken, raden we u ten zeerste aan dit te doen tijdens het maken van het cluster zoals beschreven in dit artikel. Het is niet mogelijk om Azure Data Lake Storage Gen1 als extra opslag toe te voegen aan een bestaand HDInsight-cluster.

Zie voor meer informatie over de basis principes van het toegangs beheer model voor Data Lake Storage Gen1 [Access Control in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Bestanden openen vanuit het cluster

Er zijn verschillende manieren om toegang te krijgen tot de bestanden in Data Lake Storage vanuit een HDInsight-cluster.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **De verkorte padnotatie gebruiken**. Met deze methode vervangt u het pad naar de hoofdmap van het cluster met:

    ```
    adl:///<file path>
    ```

* **Het relatieve pad gebruiken**. Met deze methode geeft u alleen het volledige relatieve pad op naar het bestand dat u wilt openen.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Voor beelden van gegevens toegang

Voor beelden zijn gebaseerd op een [SSH-verbinding](./hdinsight-hadoop-linux-use-ssh-unix.md) met het hoofd knooppunt van het cluster. De voor beelden gebruiken alle drie de URI-schema's. Vervang `DATALAKEACCOUNT` en `CLUSTERNAME` door de relevante waarden.

#### <a name="a-few-hdfs-commands"></a>Enkele hdfs-opdrachten

1. Maak een eenvoudig bestand op lokale opslag.

    ```bash
    touch testFile.txt
    ```

1. Maak mappen in de cluster opslag.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Gegevens uit de lokale opslag kopiëren naar de cluster opslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Mapinhoud weer geven in cluster opslag.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Een Hive-tabel maken

Er worden drie bestands locaties weer gegeven voor illustratie doeleinden. Gebruik slechts één van de `LOCATION` vermeldingen voor daad werkelijke uitvoering.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Pad naar opslag van Ambari identificeren

Als u het volledige pad naar het geconfigureerde standaard archief wilt identificeren, gaat u naar **HDFS** > **configuraties** en voert u `fs.defaultFS` in het vak invoer filteren.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>HDInsight-clusters maken met toegang tot Data Lake Storage Gen1

Gebruik de volgende koppelingen voor gedetailleerde instructies over het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen1.

* [Portal gebruiken](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Power shell gebruiken (met Data Lake Storage Gen1 als standaard opslag)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Power shell gebruiken (met Data Lake Storage Gen1 als extra opslag)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sjablonen gebruiken](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Het HDInsight-certificaat voor toegang tot Data Lake Storage Gen1 vernieuwen

In het volgende voor beeld van een Power shell-code wordt een certificaat uit een lokaal bestand of Azure Key Vault gelezen en wordt uw HDInsight-cluster bijgewerkt met het nieuwe certificaat voor toegang tot Azure Data Lake Storage Gen1. Geef uw eigen HDInsight-cluster naam, de naam van de resource groep, de abonnements-ID, de App-ID, het lokale pad naar het certificaat op. Typ het wacht woord wanneer u daarom wordt gevraagd.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u HDFS-compatibele Azure Data Lake Storage Gen1 met HDInsight kunt gebruiken. Zodoende kunt u een schaalbare, duurzame, archiveringsoplossing voor gegevensverzameling bouwen en HDInsight gebruiken om de informatie te ontsluiten in de opgeslagen gestructureerde en ongestructureerde gegevens.

Ga voor meer informatie naar:

* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Snelstartgids: Clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Een HDInsight-cluster maken om Data Lake Storage Gen1 te gebruiken met behulp van de Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Azure Storage-hand tekeningen voor gedeelde toegang gebruiken om de toegang tot gegevens te beperken met HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Zelf studie: gegevens uitpakken, transformeren en laden met interactieve Query's in azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
