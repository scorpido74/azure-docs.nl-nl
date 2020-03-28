---
title: 'Zelfstudie: On-demand clusters in Azure HDInsight met Data Factory'
description: Zelfstudie - Meer informatie over het maken van on-demand Apache Hadoop-clusters in HDInsight met Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/18/2020
ms.openlocfilehash: b184a42c52384440445181ac44c616c3139e064f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130694"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Zelfstudie: On-demand Apache Hadoop-clusters maken in HDInsight met Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In deze zelfstudie leert u hoe u een [Apache Hadoop-cluster](./hadoop/apache-hadoop-introduction.md) op aanvraag maakt in Azure HDInsight met Azure Data Factory. Vervolgens gebruikt u gegevenspijplijnen in Azure Data Factory om Hive-taken uit te voeren en het cluster te verwijderen. Tegen het einde van deze zelfstudie leert u hoe u een big data-taakkunt operationaliseren waarbij clustercreatie, taakbeheer en clusterverwijdering volgens een planning worden uitgevoerd.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een Azure-opslagaccount maken
> * Informatie over Azure Data Factory-activiteit
> * Een gegevensfabriek maken met Azure-portal
> * Gekoppelde services maken
> * Een pijplijn maken
> * Een pijplijn activeren
> * Een pijplijn bewaken
> * De uitvoer controleren

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De PowerShell [Az Module](https://docs.microsoft.com/powershell/azure/overview) geïnstalleerd.

* Een Azure Active Directory-serviceprincipal. Zodra u de serviceprincipal hebt gemaakt, moet u de **toepassings-id** en **verificatiesleutel** ophalen met behulp van de instructies in het gekoppelde artikel. U moet deze waarden later in deze zelfstudie. Zorg er ook voor dat de serviceprincipal lid is van de *rol inzender* van het abonnement of de resourcegroep waarin het cluster is gemaakt. Zie [Een Azure Active Directory-serviceprincipal maken](../active-directory/develop/howto-create-service-principal-portal.md)voor instructies om de vereiste waarden op te halen en de juiste rollen toe te wijzen.

## <a name="create-preliminary-azure-objects"></a>Voorlopige Azure-objecten maken

In deze sectie maakt u verschillende objecten die worden gebruikt voor het HDInsight-cluster dat u on-demand maakt. Het gemaakte opslagaccount bevat het voorbeeld `partitionweblogs.hql` [van HiveQL-script,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) dat u gebruikt om een voorbeeld van Apache Hive-taak te simuleren die op het cluster wordt uitgevoerd.

In deze sectie wordt een Azure PowerShell-script gebruikt om het opslagaccount te maken en de vereiste bestanden binnen het opslagaccount te kopiëren. In het voorbeeldscript azure PowerShell in deze sectie worden de volgende taken uitgevoerd:

1. Log in bij Azure.
2. Hiermee maakt u een Azure-resourcegroep.
3. Hiermee wordt een Azure Storage-account gemaakt.
4. Hiermee maakt u een Blob-container in het opslagaccount
5. Kopieert het voorbeeld HiveQL script **(partitionweblogs.hql)** de Blob container. Het script is [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)beschikbaar op . Het voorbeeldscript is al beschikbaar in een andere openbare Blob-container. Het PowerShell-script hieronder maakt een kopie van deze bestanden naar het Azure Storage-account dat het maakt.

### <a name="create-storage-account-and-copy-files"></a>Opslagaccount maken en bestanden kopiëren

> [!IMPORTANT]  
> Geef namen op voor de Azure-brongroep en het Azure-opslagaccount dat door het script wordt gemaakt.
> Schrijf **de naam van de brongroep,** **de naam van het opslagaccount**en de **opslagaccountsleutel** op die door het script zijn uitgevoerd. Je hebt ze nodig in het volgende gedeelte.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Opslagaccount verifiëren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer vanaf de linkerkant naar **Alle services** > **Algemene** > **resourcegroepen**.
1. Selecteer de naam van de resourcegroep die u in uw PowerShell-script hebt gemaakt. Gebruik het filter als er te veel resourcegroepen worden weergegeven.
1. In de **overzichtsweergave** ziet u één resource die wordt weergegeven, tenzij u de resourcegroep deelt met andere projecten. Die bron is het opslagaccount met de naam die u eerder hebt opgegeven. Selecteer de naam van het opslagaccount.
1. Selecteer de tegel **Containers.**
1. Selecteer de **adfgetstarted** container. U ziet een map genaamd **hivescripts**.
1. Open de map en zorg ervoor dat deze het voorbeeldscriptbestand bevat, **partitionweblogs.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Inzicht in de activiteit Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) orkestreert en automatiseert de beweging en transformatie van gegevens. Azure Data Factory kan just-in-time een HDInsight Hadoop-cluster maken om een invoergegevenssegment te verwerken en het cluster te verwijderen wanneer de verwerking is voltooid.

In Azure Data Factory kan een gegevensfabriek een of meer gegevenspijplijnen hebben. Een gegevenspijplijn heeft een of meer activiteiten. Er zijn twee soorten activiteiten:

* [Activiteiten voor gegevensverkeer](../data-factory/copy-activity-overview.md). U gebruikt activiteiten voor gegevensverplaatsing om gegevens van een brongegevensarchief naar een doelgegevensarchief te verplaatsen.
* [Activiteiten voor gegevenstransformatie](../data-factory/transform-data.md). U gebruikt gegevenstransformatieactiviteiten om gegevens te transformeren/verwerken. HDInsight Hive Activity is een van de transformatieactiviteiten die worden ondersteund door Data Factory. U gebruikt de activiteit Hive-transformatie in deze zelfstudie.

In dit artikel configureert u de Hive-activiteit om een on-demand HDInsight Hadoop-cluster te maken. Wanneer de activiteit wordt uitgevoerd om gegevens te verwerken, gebeurt er als het gevolg:

1. Er wordt automatisch een HDInsight Hadoop-cluster gemaakt om het segment te verwerken.

2. De invoergegevens worden verwerkt door een HiveQL-script op het cluster uit te voeren. In deze zelfstudie voert het HiveQL-script dat is gekoppeld aan de hive-activiteit de volgende acties uit:

    * Gebruikt de bestaande tabel *(hivesampletable)* om een andere tabel **HiveSampleOut**te maken.
    * Hiermee wordt de **tabel HiveSampleOut** gevuld met alleen specifieke kolommen uit de oorspronkelijke *hivesampletabel*.

3. Het HDInsight Hadoop-cluster wordt verwijderd nadat de verwerking is voltooid en het cluster is niet actief voor de geconfigureerde tijd (timeToLive-instelling). Als het volgende gegevenssegment beschikbaar is voor verwerking met in deze timeToLive-inactieve tijd, wordt hetzelfde cluster gebruikt om het segment te verwerken.  

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Navigeer in het linkermenu naar **+ Een resource** > **Analytics** > **Data Factory maken**.

    ![Azure Data Factory op de portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory op de portal")

3. Voer de volgende waarden in of selecteer voor de tegel **Nieuwe gegevensfabriek:**

    |Eigenschap  |Waarde  |
    |---------|---------|
    |Name | Voer een naam in voor de gegevensfabriek. Deze naam moet wereldwijd uniek zijn.|
    |Versie | Vertrek bij **V2**. |
    |Abonnement | Selecteer uw Azure-abonnement. |
    |Resourcegroep | Selecteer de resourcegroep die u hebt gemaakt met het PowerShell-script. |
    |Locatie | De locatie wordt automatisch ingesteld op de locatie die u hebt opgegeven terwijl u de resourcegroep eerder maakt. Voor deze zelfstudie is de locatie ingesteld op **Oost-VS.** |
    |GIT inschakelen|Schakel dit selectievakje uit.|

    ![Azure Data Factory maken met Azure-portal](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Azure Data Factory maken met Azure-portal")

4. Selecteer **Maken**. Het maken van een gegevensfabriek kan tussen de 2 en 4 minuten duren.

5. Zodra de gegevensfabriek is gemaakt, ontvangt u een **door Implementatie geslaagde** melding met de knop **Ga naar bron.**  Selecteer **Ga naar resource om** de standaardweergave Gegevensfabriek te openen.

6. Selecteer **Auteur &-monitor** om de ontwerp- en bewakingsportal van Azure Data Factory te starten.

    ![Overzicht van Azure Data Factory-portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Overzicht van Azure Data Factory")

## <a name="create-linked-services"></a>Gekoppelde services maken

In deze sectie maakt u twee gekoppelde services in uw gegevensfabriek.

* Een **azure storage-gekoppelde service** die een Azure-opslagaccount koppelt aan de gegevensfabriek. Deze opslag wordt gebruikt voor het HDInsight-cluster op aanvraag. Het bevat ook het Hive-script dat op het cluster wordt uitgevoerd.
* Een **on-demand HDInsight gekoppelde service.** Azure Data Factory maakt automatisch een HDInsight-cluster en voert het Hive-script uit. Het HDInsight-cluster wordt vervolgens verwijderd als het cluster gedurende een vooraf geconfigureerde tijd inactief is geweest.

### <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

1. Selecteer in het linkerdeelvenster van de pagina **Laten we aan de slag** het pictogram **Auteur.**

    ![Een gekoppelde Azure Data Factory-service maken](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Een gekoppelde Azure Data Factory-service maken")

2. Selecteer **Verbindingen** in de linkerbenedenhoek van het venster en selecteer **+Nieuw**.

    ![Verbindingen maken in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Verbindingen maken in Azure Data Factory")

3. Selecteer azure **blob-opslag** in het dialoogvenster **Nieuwe gekoppelde service** en selecteer **Doorgaan**.

    ![Gekoppelde Azure Storage-service voor gegevensfabriek maken](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Gekoppelde Azure Storage-service voor gegevensfabriek maken")

4. Geef de volgende waarden op voor de gekoppelde opslagservice:

    |Eigenschap |Waarde |
    |---|---|
    |Name |Voer `HDIStorageLinkedService` in.|
    |Azure-abonnement |Selecteer uw abonnement in de vervolgkeuzelijst.|
    |Naam van opslagaccount |Selecteer het Azure Storage-account dat u hebt gemaakt als onderdeel van het PowerShell-script.|

    Selecteer **Verbinding testen** en selecteer vervolgens **Maken**als deze is geslaagd .

    ![Naam opgeven voor gekoppelde Azure Storage-service](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Naam opgeven voor gekoppelde Azure Storage-service")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Een gekoppelde HDInsight-service op aanvraag maken

1. Selecteer nogmaals de knop **+ Nieuw** om een andere gekoppelde service te maken.

2. Selecteer in het venster **Nieuwe gekoppelde service** het tabblad **Gegevensberekenen.**

3. Selecteer **Azure HDInsight**en selecteer **Doorgaan**.

    ![HDInsight-gekoppelde service maken voor Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "HDInsight-gekoppelde service maken voor Azure Data Factory")

4. Voer in het venster **Nieuwe gekoppelde service** de volgende waarden in en laat de rest als standaard:

    | Eigenschap | Waarde |
    | --- | --- |
    | Name | Voer `HDInsightLinkedService` in.|
    | Type | Selecteer **On-demand HDInsight**. |
    | Een gekoppelde Azure Storage-service | Selecteer `HDIStorageLinkedService`. |
    | Clustertype | **Hadoop selecteren** |
    | Time To Live | Geef de duur op waarvoor u het HDInsight-cluster beschikbaar wilt hebben voordat het automatisch wordt verwijderd.|
    | Servicehoofd-id | Geef de toepassings-id op van de Azure Active Directory-serviceprincipal die u hebt gemaakt als onderdeel van de vereisten. |
    | Serviceprincipal-sleutel | Geef de verificatiesleutel op voor de azure Active Directory-serviceprincipal. |
    | Voorvoegsel clusternaam | Geef een waarde op die vooraf wordt bevestigd aan alle clustertypen die door de gegevensfabriek zijn gemaakt. |
    |Abonnement |Selecteer uw abonnement in de vervolgkeuzelijst.|
    | Resourcegroep selecteren | Selecteer de resourcegroep die u hebt gemaakt als onderdeel van het PowerShell-script dat u eerder hebt gebruikt.|
    | OOS-type/Cluster SSH-gebruikersnaam | Voer vaak een SSH-gebruikersnaam in. `sshuser` |
    | Besturingssysteemtype/Cluster SSH-wachtwoord | Geef een wachtwoord op voor de SSH-gebruiker |
    | Besturingssysteemtype/clustergebruikersnaam | Voer een clustergebruikersnaam `admin`in, meestal . |
    | Besturingssysteemtype/clusterwachtwoord | Geef een wachtwoord op voor de clustergebruiker. |

    Selecteer vervolgens **Maken**.

    ![Waarden bieden voor hdinsight-gekoppelde service](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Waarden bieden voor hdinsight-gekoppelde service")

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer **+** de knop (plus) en selecteer **Vervolgens Pijplijn**.

    ![Een pijplijn maken in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Een pijplijn maken in Azure Data Factory")

1. Vouw hdinsight uit en sleep in de **gereedschapsset Activiteiten** **HDInsight**en sleep de **Hive-activiteit** naar het oppervlak van de pijplijnontwerper. Geef op het tabblad **Algemeen** een naam voor de activiteit op.

    ![Activiteiten toevoegen aan de pijplijn Gegevensfabriek](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Activiteiten toevoegen aan de pijplijn Gegevensfabriek")

1. Zorg ervoor dat u de Hive-activiteit hebt geselecteerd, selecteer het tabblad **HDI-cluster** en selecteer in de vervolgkeuzelijst **HDInsight Linked Service** de gekoppelde service die u eerder hebt gemaakt, **HDInsightLinkedService**, voor HDInsight.

    ![HDInsight-clusterdetails voor de pijplijn bieden](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "HDInsight-clusterdetails voor de pijplijn bieden")

1. Selecteer het tabblad **Script** en voer de volgende stappen uit:

    1. Selecteer **HDIStorageLinkedService** voor **Script Linked Service**in de vervolgkeuzelijst. Deze waarde is de opslaggekoppelde service die u eerder hebt gemaakt.

    1. Selecteer bij **Bestandspad**de optie **Opslag bladeren** en navigeer naar de locatie waar het voorbeeldvan hive-script beschikbaar is. Als u het PowerShell-script eerder hebt `adfgetstarted/hivescripts/partitionweblogs.hql`uitgevoerd, moet deze locatie .

        ![Hive-scriptgegevens opgeven voor de pijplijn](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Hive-scriptgegevens opgeven voor de pijplijn")

    1. Selecteer **onder Geavanceerde** > **parameters**de optie Automatisch aanvullen **uit script**. Met deze optie wordt gezocht naar parameters in het Hive-script waarvoor waarden nodig zijn tijdens runtime.

    1. Voeg in het **tekstvak waarde** de `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/`bestaande map toe in de indeling . Het pad is hoofdlettergevoelig. Dit is het pad waar de uitvoer van het script wordt opgeslagen. Het `wasbs` schema is nodig omdat opslagaccounts nu standaard beveiligde overdracht hebben ingeschakeld.

        ![Parameters opgeven voor het Hive-script](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Parameters opgeven voor het Hive-script")

1. Selecteer **Valideren** om de pijplijn te valideren. Selecteer **>>** de knop (pijl-rechts) om het validatievenster te sluiten.

    ![De Azure Data Factory-pijplijn valideren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "De Azure Data Factory-pijplijn valideren")

1. Selecteer Ten slotte **Alles publiceren** om de artefacten te publiceren naar Azure Data Factory.

    ![De Azure Data Factory-pijplijn publiceren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "De Azure Data Factory-pijplijn publiceren")

## <a name="trigger-a-pipeline"></a>Een pijplijn activeren

1. Selecteer**Trigger nu** **toevoegen** > op de werkbalk op het ontwerpoppervlak .

    ![De Azure Data Factory-pijplijn activeren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "De Azure Data Factory-pijplijn activeren")

2. Selecteer **OK** in de pop-upzijbalk.

## <a name="monitor-a-pipeline"></a>Een pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet een pijplijn die worden uitgevoerd in de lijst **Pipeline Runs**. Let op de status van de run onder de kolom **Status.**

    ![De Azure Data Factory-pijplijn controleren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "De Azure Data Factory-pijplijn controleren")

1. Selecteer **Vernieuwen** om de status te vernieuwen.

1. U ook het pictogram **Activiteitsruns weergeven** selecteren om de activiteitsrun te zien die aan de pijplijn is gekoppeld. In de onderstaande schermafbeelding ziet u slechts één activiteit worden uitgevoerd, omdat er slechts één activiteit in de pijplijn zit die u hebt gemaakt. Als u wilt teruggaan naar de vorige weergave, selecteert u **Pijplijnen** naar de bovenkant van de pagina.

    ![De pijplijnactiviteit Azure Data Factory bewaken](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "De pijplijnactiviteit Azure Data Factory bewaken")

## <a name="verify-the-output"></a>De uitvoer controleren

1. Als u de uitvoer wilt verifiëren, navigeert u in de Azure-portal naar het opslagaccount dat u voor deze zelfstudie hebt gebruikt. U ziet de volgende mappen of containers:

    * U ziet een **adfgerstarted/outputmap** die de uitvoer bevat van het Hive-script dat is uitgevoerd als onderdeel van de pijplijn.

    * U ziet een **adfhdidatafactory-linked-service-naam\<>-\<tijdstempel>** container. Deze container is de standaardopslaglocatie van het HDInsight-cluster dat is gemaakt als onderdeel van de pijplijnuitvoering.

    * U ziet een **adfjobs-container** met de taaklogboeken van Azure Data Factory.  

        ![De pijplijnuitvoer van Azure Data Factory controleren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "De pijplijnuitvoer van Azure Data Factory controleren")

## <a name="clean-up-resources"></a>Resources opschonen

Met het on-demand HDInsight-cluster maken, hoeft u het HDInsight-cluster niet expliciet te verwijderen. Het cluster wordt verwijderd op basis van de configuratie die u hebt opgegeven tijdens het maken van de pijplijn. Zelfs nadat het cluster is verwijderd, blijven de opslagaccounts die aan het cluster zijn gekoppeld, bestaan. Dit gedrag is door het ontwerp, zodat u uw gegevens intact houden. Als u de gegevens echter niet wilt blijven gebruiken, u het opslagaccount dat u hebt gemaakt verwijderen.

U ook de hele brongroep verwijderen die u voor deze zelfstudie hebt gemaakt. Hiermee worden het opslagaccount en de Azure Data Factory die u hebt gemaakt, verwijderd.

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkerdeelvenster.
1. Selecteer de naam van de resourcegroep die u in uw PowerShell-script hebt gemaakt. Gebruik het filter als er te veel resourcegroepen worden weergegeven. Hiermee wordt de resourcegroep geopend.
1. Op de tegel **Resources** hebt u het standaardopslagaccount en de gegevensfabriek vermeld, tenzij u de brongroep deelt met andere projecten.
1. Selecteer **Resourcegroep verwijderen**. Hiermee worden het opslagaccount en de gegevens die zijn opgeslagen in het opslagaccount verwijderd.

    ![Brongroep Azure-portal verwijderen](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Resourcegroep verwijderen")

1. Voer de naam van de brongroep in om verwijdering te bevestigen en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Azure Data Factory gebruiken om on-demand HDInsight-cluster te maken en [Apache Hive-taken](https://hive.apache.org/) uit te voeren. Ga naar het volgende artikel voor meer informatie over het maken van HDInsight-clusters met aangepaste configuratie.

> [!div class="nextstepaction"]
> [Azure HDInsight-clusters maken met aangepaste configuratie](hdinsight-hadoop-provision-linux-clusters.md)
