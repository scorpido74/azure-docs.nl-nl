---
title: Toegang beperken met behulp van gedeelde toegangshandtekeningen - Azure HDInsight
description: Meer informatie over het gebruik van Gedeelde toegangshandtekeningen om hdinsight-toegang te beperken tot gegevens die zijn opgeslagen in Azure-opslagblobs.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: d68f7dc6368c2b3de7f26f2946c5fb47237a820d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313936"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Azure Storage Shared Access Signatures gebruiken om de toegang tot gegevens in HDInsight te beperken

HDInsight heeft volledige toegang tot gegevens in de Azure Storage-accounts die aan het cluster zijn gekoppeld. U Gedeelde toegangshandtekeningen in de blobcontainer gebruiken om de toegang tot de gegevens te beperken. SAS (Shared Access Signatures) is een functie van Azure-opslagaccounts waarmee u de toegang tot gegevens beperken. Bijvoorbeeld het bieden van alleen-lezen toegang tot gegevens.

> [!IMPORTANT]  
> Voor een oplossing met Apache Ranger u overwegen hdinsight met een domein te gebruiken. Zie het [HDInsight-document configureren](./domain-joined/apache-domain-joined-configure.md) voor meer informatie.

> [!WARNING]  
> HDInsight moet volledige toegang hebben tot de standaardopslag voor het cluster.

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Een bestaande [opslagcontainer](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Als u PowerShell gebruikt, hebt u de [Az-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

* Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)als u Azure CLI wilt gebruiken en u deze nog niet hebt geïnstalleerd.

* Bij gebruik van [Python,](https://www.python.org/downloads/)versie 2.7 of hoger.

* Als u C#gebruikt, moet Visual Studio versie 2013 of hoger zijn.

* Het [URI-schema](./hdinsight-hadoop-linux-information.md#URI-and-scheme) voor uw opslagaccount. Dit schema `wasb://` is bedoeld `abfs://` voor Azure Storage, Azure `adl://` Data Lake Storage Gen2 of voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure `wasbs://`Storage, is de URI . Zie ook, [veilige overdracht](../storage/common/storage-require-secure-transfer.md).

* Een bestaand HDInsight-cluster om een gedeelde toegangshandtekening aan toe te voegen. Als dit niet het zo is, u Azure PowerShell gebruiken om een cluster te maken en een gedeelde toegangshandtekening toe te voegen tijdens het maken van een cluster.

* De voorbeeldbestanden [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)van . Deze opslagplaats bevat de volgende items:

  * Een Visual Studio-project dat een opslagcontainer, opgeslagen beleid en SAS kan maken voor gebruik met HDInsight
  * Een Python-script dat een opslagcontainer, opgeslagen beleid en SAS kan maken voor gebruik met HDInsight
  * Een PowerShell-script dat een HDInsight-cluster kan maken en configureren om de SAS te gebruiken. Een bijgewerkte versie wordt hieronder verder gebruikt.
  * Een voorbeeldbestand:`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Shared Access Signatures

Er zijn twee vormen van gedeelde toegangshandtekeningen:

* `Ad hoc`: De begintijd, vervaldatum en machtigingen voor de SAS zijn allemaal opgegeven op de SAS URI.

* `Stored access policy`: Een opgeslagen toegangsbeleid wordt gedefinieerd op een resourcecontainer, zoals een blobcontainer. Een beleid kan worden gebruikt om beperkingen voor een of meer gedeelde toegangshandtekeningen te beheren. Wanneer u een SAS koppelt aan een opgeslagen toegangsbeleid, neemt de SAS de beperkingen over - de begintijd, vervaldatum en machtigingen - die zijn gedefinieerd voor het beleid voor opgeslagen toegang.

Het verschil tussen de twee formulieren is belangrijk voor één belangrijk scenario: intrekking. Een SAS is een URL, dus iedereen die de SAS verkrijgt, kan deze gebruiken. Het maakt niet uit wie er om vroeg. Als een SAS openbaar wordt gepubliceerd, kan deze door iedereen in de wereld worden gebruikt. Een SAS die wordt gedistribueerd is geldig totdat een van de vier dingen gebeurt:

1. De vervaldatum die op de SAS is opgegeven, is bereikt.

2. De vervaldatum die is opgegeven voor het beleid voor opgeslagen toegang waarnaar de SAS verwijst, is bereikt. De volgende scenario's zorgen ervoor dat de vervaldatum wordt bereikt:

    * Het tijdsinterval is verstreken.
    * Het beleid voor opgeslagen toegang is gewijzigd om een vervaldatum in het verleden te hebben. Het wijzigen van de vervaldatum is een manier om de SAS in te trekken.

3. Het door de SAS genoemde beleid voor opgeslagen toegang wordt verwijderd, wat een andere manier is om de SAS in te trekken. Als u het opgeslagen toegangsbeleid opnieuw maakt met dezelfde naam, zijn alle SAS-tokens voor het vorige beleid geldig (als de vervaldatum op de SAS nog niet is verstreken). Als u van plan bent de SAS in te trekken, moet u een andere naam gebruiken als u het toegangsbeleid opnieuw maakt met een vervaldatum in de toekomst.

4. De accountsleutel die is gebruikt om de SAS te maken, wordt geregenereerd. Als u de sleutel regenereert, worden alle toepassingen die de vorige sleutel gebruiken, mislukt. Werk alle onderdelen bij naar de nieuwe sleutel.

> [!IMPORTANT]  
> Uri met gedeelde toegangshandtekening is gekoppeld aan de accountsleutel die wordt gebruikt om de handtekening te maken en het bijbehorende beleid voor opgeslagen toegang (indien van toepassing). Als er geen opgeslagen toegangsbeleid is opgegeven, u alleen een handtekening voor gedeelde toegang intrekken door de accountsleutel te wijzigen.

We raden u aan altijd een opgeslagen toegangsbeleid te gebruiken. Wanneer u opgeslagen beleidsregels gebruikt, u handtekeningen intrekken of de vervaldatum indien nodig verlengen. De stappen in dit document gebruiken opgeslagen toegangsbeleid om SAS te genereren.

Zie Het [SAS-model begrijpen](../storage/common/storage-dotnet-shared-access-signature-part-1.md)voor meer informatie over handtekeningen voor gedeelde toegang.

## <a name="create-a-stored-policy-and-sas"></a>Een opgeslagen beleid en SAS maken

Sla het SAS-token op dat aan het einde van elke methode wordt geproduceerd. Het token lijkt op de volgende uitvoer:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>PowerShell gebruiken

Vervang `RESOURCEGROUP` `STORAGEACCOUNT`, `STORAGECONTAINER` en met de juiste waarden voor uw bestaande opslagcontainer. De parameter `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` wijzigen `-File` in of herzien om `Set-AzStorageblobcontent`het absolute pad voor . Voer de volgende PowerShell-opdracht in:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Azure CLI gebruiken

Het gebruik van variabelen in deze sectie is gebaseerd op een Windows-omgeving. Lichte variaties zullen nodig zijn voor bash of andere omgevingen.

1. Vervang `STORAGEACCOUNT`en `STORAGECONTAINER` door de juiste waarden voor uw bestaande opslagcontainer.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Stel de opgehaalde primaire sleutel in op een variabele voor later gebruik. Vervang `PRIMARYKEY` de opgehaalde waarde in de vorige stap en voer de onderstaande opdracht in:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. De parameter `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` wijzigen `--file` in of herzien om `az storage blob upload`het absolute pad voor . Voer de overige opdrachten uit:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Python gebruiken

Open `SASToken.py` het bestand `storage_account_name` `storage_account_key`en `storage_container_name` vervang , en met de juiste waarden voor uw bestaande opslagcontainer en voer het script uit.

Mogelijk moet u `pip install --upgrade azure-storage` dit uitvoeren als `ImportError: No module named azure.storage`u het foutbericht ontvangt.

### <a name="using-c"></a>C gebruiken\#

1. Open de oplossing in Visual Studio.

2. Klik in Solution Explorer met de rechtermuisknop op het **SASExample-project** en selecteer **Eigenschappen**.

3. Selecteer **Instellingen** en voeg waarden toe voor de volgende vermeldingen:

    |Item |Beschrijving |
    |---|---|
    |StorageConnectionString|De verbindingstekenreeks voor het opslagaccount waarvoor u een opgeslagen beleid en SAS wilt maken. Het formaat `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` moet `myaccount` zijn waar is de `mykey` naam van uw opslagaccount en is de sleutel voor de opslag account.|
    |ContainerName|De container in het opslagaccount waartoe u de toegang wilt beperken.|
    |SASPolicyName|De naam die u wilt gebruiken voor het opgeslagen beleid om te maken.|
    |BestandToUpload|Het pad naar een bestand dat naar de container wordt geüpload.|

4. Voer het project uit. Sla het SAS-beleidstoken, de naam van het opslagaccount en de naam van de container op. Deze waarden worden gebruikt bij het koppelen van het opslagaccount aan uw HDInsight-cluster.

## <a name="use-the-sas-with-hdinsight"></a>Gebruik de SAS met HDInsight

Wanneer u een HDInsight-cluster maakt, moet u een primair opslagaccount opgeven. U ook extra opslagaccounts opgeven. Beide methoden voor het toevoegen van opslag vereisen volledige toegang tot de opslagaccounts en containers die worden gebruikt.

Gebruik een gedeelde toegangshandtekening om containertoegang te beperken. Voeg een aangepaste vermelding toe aan de **kernsiteconfiguratie** voor het cluster. U de vermelding toevoegen tijdens het maken van het cluster met PowerShell of na het maken van een cluster met Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Een cluster maken dat de SAS gebruikt

Vervangen `CLUSTERNAME` `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER`, `STORAGEACCOUNT`, `TOKEN` , en met de juiste waarden. Voer de PowerShell-opdrachten in:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Wanneer u wordt gevraagd om de http/s- of SSH-gebruikersnaam en -wachtwoord, moet u een wachtwoord opgeven dat aan de volgende criteria voldoet:
>
> * Moet ten minste 10 tekens in lengte.
> * Moet ten minste één cijfer bevatten.
> * Moet ten minste één niet-alfanumeriek teken bevatten.
> * Moet ten minste één hoofdletter of kleine letter bevatten.

Het duurt een tijdje voor dit script te voltooien, meestal ongeveer 15 minuten. Wanneer het script zonder fouten is voltooid, is het cluster gemaakt.

### <a name="use-the-sas-with-an-existing-cluster"></a>De SAS gebruiken met een bestaand cluster

Als u een bestaand cluster hebt, u de SAS toevoegen aan de configuratie van de **kernsite** met behulp van de volgende stappen:

1. Open de gebruikersinterface van het Ambari-web voor uw cluster. Het adres voor `https://YOURCLUSTERNAME.azurehdinsight.net`deze pagina is . Wanneer u daarom wordt gevraagd, verifieert u het cluster met de beheerdersnaam (beheerder) en het wachtwoord dat u hebt gebruikt bij het maken van het cluster.

1. Navigeer naar **HDFS** > **Configs** > **Advanced** > **Custom-kernsite**.

1. Vouw de sectie **Aangepaste kernsite** uit, schuif naar het einde en selecteer **vervolgens Eigenschap toevoegen...**. Gebruik de volgende waarden voor **Sleutel** en **waarde:**

    * **Sleutel**:`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Waarde**: De SAS wordt geretourneerd door een van de eerder uitgevoerde methoden.

    Vervang `CONTAINERNAME` de containernaam die u hebt gebruikt met de C#- of SAS-toepassing. Vervang `STORAGEACCOUNTNAME` de naam van het opslagaccount die u hebt gebruikt.

    Selecteer **Toevoegen** om deze sleutel en waarde op te slaan

1. Selecteer de knop **Opslaan** om de configuratiewijzigingen op te slaan. Voeg desgevraagd een beschrijving van de wijziging toe ('SAS-opslagtoegang toevoegen' bijvoorbeeld) en selecteer **Opslaan**.

    Selecteer **OK** wanneer de wijzigingen zijn voltooid.

   > [!IMPORTANT]  
   > U moet verschillende services opnieuw starten voordat de wijziging van kracht wordt.

1. Er wordt een vervolgkeuzelijst **opnieuw starten** weergegeven. Selecteer **Alle getroffen opnieuw starten** in de vervolgkeuzelijst en bevestig Alles opnieuw __starten__.

    Herhaal dit proces voor **MapReduce2** en **YARN**.

1. Zodra de services opnieuw zijn gestart, selecteert u elk services en schakelt u de onderhoudsmodus uit in de vervolgkeuzelijst **Serviceacties.**

## <a name="test-restricted-access"></a>Beperkte toegang testen

Gebruik de volgende stappen om te controleren of u alleen items in het SAS-opslagaccount lezen en aanbieden.

1. Maak verbinding met het cluster. Vervang `CLUSTERNAME` de naam van uw cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Als u de inhoud van de container wilt weergeven, gebruikt u de volgende opdracht van de prompt:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Vervang `SASCONTAINER` de naam van de container die is gemaakt voor het SAS-opslagaccount. Vervang `SASACCOUNTNAME` de naam van het opslagaccount dat voor de SAS wordt gebruikt.

    De lijst bevat het bestand dat is geüpload wanneer de container en SAS zijn gemaakt.

3. Gebruik de volgende opdracht om te controleren of u de inhoud van het bestand lezen. Vervang `SASCONTAINER` de `SASACCOUNTNAME` en zoals in de vorige stap. Vervang `sample.log` de naam van het bestand dat in de vorige opdracht wordt weergegeven:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Deze opdracht geeft de inhoud van het bestand weer.

4. Gebruik de volgende opdracht om het bestand te downloaden naar het lokale bestandssysteem:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Met deze opdracht wordt het bestand gedownload naar een lokaal bestand met de naam **testfile.txt**.

5. Gebruik de volgende opdracht om het lokale bestand te uploaden naar een nieuw bestand met de naam **testupload.txt** op de SAS-opslag:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    U ontvangt een bericht dat lijkt op de volgende tekst:

        put: java.io.IOException

    Deze fout treedt op omdat de opslaglocatie alleen lees+lijst is. Gebruik de volgende opdracht om de gegevens op de standaardopslag voor het cluster te plaatsen, die beschrijfbaar is:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Deze keer moet de bewerking met succes worden voltooid.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u opslag met beperkte toegang toevoegt aan uw HDInsight-cluster, leert u andere manieren om met gegevens op uw cluster te werken:

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)