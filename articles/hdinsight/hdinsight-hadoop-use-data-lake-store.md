---
title: Data Lake Storage Gen1 gebruiken met Hadoop in Azure HDInsight
description: Meer informatie over het opvragen van gegevens uit Azure Data Lake Storage Gen1 en het opslaan van resultaten van uw analyse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: 3e7e5919a3f862f5cad243654972683d1879c4ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251089"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters

> [!Note]
> Implementeer nieuwe HDInsight-clusters met [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) voor verbeterde prestaties en nieuwe functies.

Als u gegevens wilt analyseren in het HDInsight-cluster, u de gegevens opslaan in [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)of Azure Data Lake Storage Gen [2](../storage/blobs/data-lake-storage-introduction.md). Met alle opslagopties u HDInsight-clusters die worden gebruikt voor berekening veilig verwijderen zonder gebruikersgegevens te verliezen.

In dit artikel leert u hoe Data Lake Storage Gen1 werkt met HDInsight-clusters. Zie [Azure Storage gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-blob-storage.md) voor informatie over de werking van Azure Storage met HDInsight-clusters. Zie [Apache Hadoop-clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een HDInsight-cluster.

> [!NOTE]  
> Data Lake Storage Gen1 is altijd toegankelijk via een `adls` beveiligd kanaal, dus er is geen bestandssysteem schema naam. U gebruikt altijd `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Beschikbaarheid voor HDInsight-clusters

Apache Hadoop ondersteunt een notie van het standaardbestandssysteem. Het standaardbestandssysteem impliceert een standaardschema en instantie. De toepassing kan ook worden gebruikt om relatieve paden om te zetten. Tijdens het proces voor het maken van HDInsight-cluster u een blobcontainer in Azure Storage opgeven als het standaardbestandssysteem of met HDInsight 3.5- en nieuwere versies, u Azure Storage of Azure Data Lake Storage Gen1 selecteren als het standaardbestandssysteem met een enkele uitzonderingen. Houd er rekening mee dat het cluster en het opslagaccount in dezelfde regio moeten worden gehost.

HDInsight-clusters kunnen Data Lake Storage Gen1 op twee manieren gebruiken:

* als standaardopslag
* als extra opslag, met Azure Storage Blob als standaardopslag.

Vanaf dit moment ondersteunen slechts enkele van de HDInsight-clustertypen/-versies het gebruik van Data Lake Storage Gen1 als standaardopslag- en extra opslagaccounts:

| HDInsight-clustertype | Data Lake Storage Gen1 als standaardopslag | Data Lake Storage Gen1 als extra opslag| Opmerkingen |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versie 4.0 | Nee | Nee |ADLS Gen1 wordt niet ondersteund met HDInsight 4.0 |
| HDInsight-versie 3.6 | Ja | Ja | Met uitzondering van HBase|
| HDInsight-versie 3.5 | Ja | Ja | Met uitzondering van HBase|
| HDInsight-versie 3.4 | Nee | Ja | |
| HDInsight-versie 3.3 | Nee | Nee | |
| HDInsight-versie 3.2 | Nee | Ja | |
| Storm | | |U Data Lake Storage Gen1 gebruiken om gegevens te schrijven van een Storm-topologie. U Data Lake Storage ook gebruiken voor referentiegegevens die vervolgens kunnen worden gelezen door een Storm-topologie.|

> [!WARNING]  
> HDInsight HBase wordt niet ondersteund met Azure Data Lake Storage Gen1

Het gebruik van Data Lake Storage Gen1 als een extra opslagaccount heeft geen invloed op de prestaties of de mogelijkheid om vanuit het cluster naar Azure-opslag te lezen of te schrijven.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1 gebruiken als standaardopslag

Wanneer HDInsight wordt geïmplementeerd met Data Lake Storage Gen1 als standaardopslag, worden de clustergerelateerde bestanden opgeslagen in `adl://mydatalakestore/<cluster_root_path>/`, waar `<cluster_root_path>` is de naam van een map die u maakt in Data Lake Storage. Door voor elk cluster een hoofdpad op te geven, u hetzelfde Data Lake Storage-account voor meer dan één cluster gebruiken. U kunt dus instellingen hebben waarbij:

* Cluster1 het pad `adl://mydatalakestore/cluster1storage` kan gebruiken
* Cluster2 het pad `adl://mydatalakestore/cluster2storage` kan gebruiken

Merk op dat beide clusters dezelfde Data Lake Storage Gen1-account **mydatalakestore**gebruiken. Elk cluster heeft toegang tot zijn eigen root bestandssysteem in Data Lake Storage. In het Azure Portal-implementatiescenario wordt u gevraagd voor het pad naar de hoofdmap een mapnaam te gebruiken zoals **/clusters/\<clusternaam>**.

Als u Data Lake Storage Gen1 als standaardopslag wilt gebruiken, moet u de serviceprincipal toegang verlenen tot de volgende paden:

* De basis van de Data Lake Storage Gen1-account.  Bijvoorbeeld: adl://mydatalakestore/.
* De map voor alle mappen van het cluster.  Bijvoorbeeld: adl://mydatalakestore/clusters.
* De map voor het cluster.  Bijvoorbeeld: adl://mydatalakestore/clusters/cluster1storage.

Zie Toegang tot Gegevensmeeropslag configureren voor meer informatie voor het maken van serviceprincipal en het verlenen van toegang tot gegevensbestanden.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Een certificaat uit Azure Keyvault extraheren voor gebruik in clustercreatie

Als u Azure Data Lake Storage Gen1 wilt instellen als uw standaardopslag voor een nieuw cluster en het certificaat voor uw serviceprincipal is opgeslagen in Azure Key Vault, zijn er een aantal aanvullende stappen nodig om het certificaat om te zetten naar de juiste indeling. In de volgende codefragmenten ziet u hoe u de conversie uitvoert.

Download eerst het certificaat uit Key `SecretValueText`Vault en haal de .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Converteer `SecretValueText` vervolgens het certificaat naar een certificaat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Vervolgens u `$identityCertificate` het implementeren van een nieuw cluster gebruiken zoals in het volgende fragment:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1 gebruiken als extra opslag

U Data Lake Storage Gen1 ook gebruiken als extra opslag voor het cluster. In dergelijke gevallen kan de standaardopslag van het cluster een Azure Storage Blob of een Data Lake Storage-account zijn. Als u HDInsight-taken uitvoert tegen de gegevens die zijn opgeslagen in Data Lake Storage als extra opslag, moet u het volledig gekwalificeerde pad naar de bestanden gebruiken. Bijvoorbeeld:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

U ziet dat er nu geen **cluster_root_path** in de URL is. Dat komt omdat Data Lake Storage in dit geval geen standaardopslag is, dus het enige wat u hoeft te doen, is het pad naar de bestanden bieden.

Als u een Data Lake Storage Gen1 als extra opslag wilt gebruiken, hoeft u de serviceprincipal alleen toegang te verlenen tot de paden waar uw bestanden worden opgeslagen.  Bijvoorbeeld:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Zie Toegang tot Gegevensmeeropslag configureren voor meer informatie voor het maken van serviceprincipal en het verlenen van toegang tot gegevensbestanden.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Meer dan één Data Lake Storage-account gebruiken

Het toevoegen van een Data Lake Storage-account als extra en het toevoegen van meer dan één Data Lake Storage-accounts wordt bereikt door het HDInsight-cluster toestemming te geven voor gegevens in één of e meer Data Lake Storage-accounts. Zie Toegang tot Gegevensmeeropslag configureren.

## <a name="configure-data-lake-storage-access"></a>Toegang tot Gegevensmeeropslag configureren

Als u toegang tot Gegevensmeeropslag wilt configureren vanuit uw HDInsight-cluster, moet u over een Azure Active Directory (Azure AD) serviceprincipal beschikken. Alleen een Azure AD-beheerder kan een service-principal maken. De service-principal moet worden gemaakt met een certificaat. Zie voor meer informatie [Snelstartgids: Clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) en [Service-principal maken met zelfondertekend certificaat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Als u Azure Data Lake Storage Gen1 gaat gebruiken als extra opslag voor hdinsight-cluster, raden we u ten zeerste aan dit te doen terwijl u het cluster maakt zoals beschreven in dit artikel. Het toevoegen van Azure Data Lake Storage Gen1 als extra opslag aan een bestaand HDInsight-cluster is geen ondersteund scenario.

Zie [Toegangsbeheer in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over de basisprincipes van het toegangscontrolemodel voor Data Lake Storage Gen1.

## <a name="access-files-from-the-cluster"></a>Bestanden openen vanuit het cluster

Er zijn verschillende manieren waarop u toegang hebt tot de bestanden in Data Lake Storage vanuit een HDInsight-cluster.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **De verkorte padnotatie gebruiken**. Met deze benadering vervangt u het pad naar de clusterroot door:

    ```
    adl:///<file path>
    ```

* **Het relatieve pad gebruiken**. Met deze methode geeft u alleen het volledige relatieve pad op naar het bestand dat u wilt openen.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Voorbeelden van gegevenstoegang

Voorbeelden zijn gebaseerd op een [ssh-verbinding](./hdinsight-hadoop-linux-use-ssh-unix.md) met het hoofdknooppunt van het cluster. De voorbeelden maken gebruik van alle drie de URI-schema's. Vervang `DATALAKEACCOUNT` `CLUSTERNAME` en met de relevante waarden.

#### <a name="a-few-hdfs-commands"></a>Een paar hdfs-opdrachten

1. Maak een eenvoudig bestand over lokale opslag.

    ```bash
    touch testFile.txt
    ```

1. Maak mappen over clusteropslag.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopieer gegevens uit lokale opslag naar clusteropslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Lijst inhoud van de map op clusteropslag.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Een Hive-tabel maken

Drie bestandslocaties worden weergegeven voor illustratieve doeleinden. Gebruik voor de daadwerkelijke uitvoering `LOCATION` slechts één van de vermeldingen.

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

## <a name="identify-storage-path-from-ambari"></a>Opslagpad identificeren vanuit Ambari

Als u het volledige pad naar het geconfigureerde standaardarchief wilt `fs.defaultFS` identificeren, navigeert u naar **HDFS** > **Configs** en voert u het filterinvoervak in.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>HDInsight-clusters maken met toegang tot Data Lake Storage Gen1

Gebruik de volgende koppelingen voor gedetailleerde instructies voor het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen1.

* [Portal gebruiken](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [PowerShell gebruiken (met Data Lake Storage Gen1 als standaardopslag)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [PowerShell gebruiken (met Data Lake Storage Gen1 als extra opslag)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sjablonen gebruiken](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Het HDInsight-certificaat voor toegang tot Data Lake Storage Gen1 vernieuwen

In het volgende voorbeeld leest PowerShell-code een certificaat uit een lokaal bestand of Azure Key Vault en wordt uw HDInsight-cluster bijgewerkt met het nieuwe certificaat om toegang te krijgen tot Azure Data Lake Storage Gen1. Geef uw eigen HDInsight-clusternaam, brongroepnaam, abonnements-ID, app-id, lokaal pad naar het certificaat op. Typ het wachtwoord in wanneer daarom wordt gevraagd.

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

In dit artikel hebt u geleerd hoe u Azure Data Lake Storage Gen1 met HDInsight gebruikt met HDFS-compatibele Azure Data Lake Storage Gen1. Zodoende kunt u een schaalbare, duurzame, archiveringsoplossing voor gegevensverzameling bouwen en HDInsight gebruiken om de informatie te ontsluiten in de opgeslagen gestructureerde en ongestructureerde gegevens.

Zie voor meer informatie:

* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Snelstartgids: Clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Een HDInsight-cluster maken om Data Lake Storage Gen1 te gebruiken met de Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Handtekeningen voor gedeelde toegang van Azure Storage gebruiken om de toegang tot gegevens te beperken met HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Zelfstudie: Gegevens extraheren, transformeren en laden met interactieve query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
