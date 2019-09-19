---
title: 'Zelfstudie: Apache Hadoop clusters op aanvraag in azure HDInsight-Data Factory'
description: Zelf studie-informatie over het maken van on-demand Apache Hadoop clusters in HDInsight met behulp van Azure Data Factory.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 04/18/2019
ms.openlocfilehash: 0b3062e14873ec971163c125fccd6852d8662663
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098728"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Zelfstudie: Apache Hadoop clusters op aanvraag maken in HDInsight met behulp van Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In deze zelf studie leert u hoe u op aanvraag een [Apache Hadoop](https://hadoop.apache.org/) cluster maakt in azure HDInsight met behulp van Azure Data Factory. Vervolgens gebruikt u gegevens pijplijnen in Azure Data Factory om Hive-taken uit te voeren en het cluster te verwijderen. Aan het einde van deze zelf studie leert u hoe u een big data-taak kunt uitvoeren waarbij het maken van een cluster, het uitvoeren van een taak en het verwijderen van een cluster wordt uitgevoerd op basis van een schema.

Deze zelfstudie bestaat uit de volgende taken: 

> [!div class="checklist"]
> * Een Azure-opslagaccount maken
> * Azure Data Factory activiteiten begrijpen
> * Een data factory maken met behulp van Azure Portal
> * Gekoppelde services maken
> * Een pijplijn maken
> * Een pijp lijn activeren
> * Een pijplijn bewaken
> * De uitvoer controleren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De Power shell [AZ-module](https://docs.microsoft.com/powershell/azure/overview) is geïnstalleerd.

* Een Azure Active Directory Service-Principal. Wanneer u de Service-Principal hebt gemaakt, moet u de **toepassings-id** en **verificatie sleutel** ophalen met behulp van de instructies in het gekoppelde artikel. U hebt deze waarden later in deze zelf studie nodig. Zorg er ook voor dat de service-principal lid is van de rol *Inzender* van het abonnement of de resource groep waarin het cluster is gemaakt. Zie [Create a Azure Active Directory Service Principal](../active-directory/develop/howto-create-service-principal-portal.md)(Engelstalig) voor instructies voor het ophalen van de vereiste waarden en het toewijzen van de juiste rollen.

## <a name="create-preliminary-azure-objects"></a>Voorlopige Azure-objecten maken

In deze sectie maakt u verschillende objecten die worden gebruikt voor het HDInsight-cluster dat u op aanvraag maakt. Het gemaakte opslag account bevat het voor beeld- [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) script`partitionweblogs.hql`() dat u gebruikt voor het simuleren van een voor beeld [Apache Hive](https://hive.apache.org/) -taak die wordt uitgevoerd op het cluster.

In deze sectie wordt gebruikgemaakt van een Azure PowerShell script voor het maken van het opslag account en het kopiëren van de vereiste bestanden in het opslag account. Met het voorbeeld script Azure PowerShell in deze sectie worden de volgende taken uitgevoerd:

1. Meldt zich aan bij Azure.
2. Hiermee maakt u een Azure-resourcegroep.
3. Hiermee maakt u een Azure-opslagaccount.
4. Hiermee maakt u een BLOB-container in het opslag account
5. Hiermee kopieert u het voor beeld-HiveQL-script (**partitionweblogs. HQL**) naar de BLOB-container. Het script is beschikbaar op [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Het voorbeeld script is al beschikbaar in een andere open bare BLOB-container. Het onderstaande Power shell-script maakt een kopie van deze bestanden in het Azure Storage-account dat wordt gemaakt.

> [!WARNING]  
> Het type `BlobStorage` opslag account kan niet worden gebruikt voor HDInsight-clusters.

**Een opslag account maken en de bestanden met behulp van Azure PowerShell kopiëren:**

> [!IMPORTANT]  
> Geef namen op voor de Azure-resource groep en het Azure Storage-account dat door het script wordt gemaakt.
> Noteer de **naam van de resource groep**, de naam van het **opslag account**en de sleutel van het **opslag account** die door het script wordt gegenereerd. U hebt deze nodig in de volgende sectie.

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
    -Container $sourceContainerName

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

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

**Controleren of het opslag account is gemaakt**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **resource groepen** in het linkerdeel venster.
3. Selecteer de naam van de resource groep die u in uw Power shell-script hebt gemaakt. Gebruik het filter als er te veel resource groepen worden weer gegeven.
4. Op de tegel **resources** ziet u een resource die wordt weer gegeven, tenzij u de resource groep deelt met andere projecten. Deze resource is het opslag account met de naam die u eerder hebt opgegeven. Selecteer de naam van het opslagaccount.
5. Selecteer de tegels voor **blobs** .
6. Selecteer de **adfgetstarted** -container. U ziet een map met de naam **hivescripts**.
7. Open de map en zorg ervoor dat deze het voorbeeld script bestand, **partitionweblogs. HQL**bevat.

## <a name="understand-the-azure-data-factory-activity"></a>Inzicht in de Azure Data Factory-activiteit

[Azure Data Factory](../data-factory/introduction.md) organiseert en automatiseert de verplaatsing en trans formatie van gegevens. Azure Data Factory kunt een HDInsight Hadoop-cluster just-in-time maken om een invoer gegevens segment te verwerken en het cluster te verwijderen wanneer de verwerking is voltooid. 

In Azure Data Factory kan een data factory een of meer gegevens pijplijnen hebben. Een gegevens pijplijn heeft een of meer activiteiten. Er zijn twee soorten activiteiten:

- [Activiteiten voor gegevens verplaatsing](../data-factory/copy-activity-overview.md) : u gebruikt gegevens verplaatsings activiteiten om gegevens te verplaatsen van een brongegevens archief naar een doel gegevens archief.
- [Activiteiten voor gegevens transformatie](../data-factory/transform-data.md). U gebruikt gegevens transformatie activiteiten om gegevens te transformeren/verwerken. HDInsight Hive-activiteit is een van de transformatie activiteiten die door Data Factory worden ondersteund. In deze zelf studie gebruikt u de Hive-transformatie activiteit.

In dit artikel configureert u de Hive-activiteit voor het maken van een Hadoop-cluster op aanvraag. Wanneer de activiteit wordt uitgevoerd om gegevens te verwerken, gebeurt hier het volgende:

1. An HDInsight Hadoop-cluster wordt automatisch gemaakt voor een just-in-time-out voor het verwerken van het segment. 

2. De invoer gegevens worden verwerkt door een HiveQL-script uit te voeren op het cluster. In deze zelf studie voert het HiveQL-script dat is gekoppeld aan de Hive-activiteit de volgende acties uit:

    - Maakt gebruik van de bestaande tabel (*hivesampletable*) om een andere tabel **HiveSampleOut**te maken.
    - Vult de **HiveSampleOut** -tabel met alleen specifieke kolommen van de oorspronkelijke *hivesampletable*.
    
3. Het HDInsight Hadoop-cluster wordt verwijderd nadat de verwerking is voltooid en het cluster gedurende de ingestelde tijd (timeToLive-instelling) niet actief is. Als het volgende gegevens segment beschikbaar is voor verwerking met in deze timeToLive niet-actieve tijd, wordt hetzelfde cluster gebruikt voor het verwerken van het segment.  

## <a name="create-a-data-factory"></a>Data factory maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Ga in het menu links naar **+ een resource** > **Analytics** > -**Data Factory**maken.

    ![Azure Data Factory op de portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory op de portal")

3. Typ of selecteer de volgende waarden voor de tegel **nieuw Data Factory** :

    |Eigenschap  |Value  |
    |---------|---------|
    |Name | Voer een naam in voor de data factory. Deze naam moet wereldwijd uniek zijn.|
    |Subscription | Selecteer uw Azure-abonnement. |
    |Resource group | Selecteer **bestaande gebruiken** en selecteer vervolgens de resource groep die u hebt gemaakt met behulp van het Power shell-script. |
    |Version | Verlaat **v2**. |
    |Location | De locatie wordt automatisch ingesteld op de locatie die u hebt opgegeven tijdens het maken van de resource groep. Voor deze zelf studie is de locatie ingesteld op **VS-Oost**. |

    ![Azure Data Factory maken met behulp van Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "Azure Data Factory maken met behulp van Azure Portal")

4. Selecteer **Maken**. Het maken van een data factory kan twee tot vier minuten duren.

5. Zodra de data factory is gemaakt, ontvangt u een melding over **implementatie geslaagd** met de knop **naar resource** .  Selecteer **naar resource gaan** om de Data Factory standaard weergave te openen.

6. Selecteer **auteur & monitor** om de portal voor het maken van Azure Data Factory ontwerpen en controleren te starten.

    ![Overzicht van Azure Data Factory Portal](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Overzicht van Azure Data Factory")

## <a name="create-linked-services"></a>Gekoppelde services maken

In deze sectie maakt u twee gekoppelde services in uw data factory.

- Een **gekoppelde Azure Storage-service** waarmee een Azure-opslagaccount wordt gekoppeld aan de gegevensfactory. Deze opslag wordt gebruikt voor het HDInsight-cluster op aanvraag. Het bevat ook het Hive-script dat wordt uitgevoerd op het cluster.
- Een **gekoppelde HDInsight-service op aanvraag**. Azure Data Factory maakt automatisch een HDInsight-cluster en voert het Hive-script uit. Het HDInsight-cluster wordt vervolgens verwijderd als het cluster gedurende een vooraf geconfigureerde tijd inactief is geweest.

### <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken

1. Selecteer het pictogram **Auteur** in het linkerdeel venster van de pagina **aan de slag** .

    ![Een Azure Data Factory gekoppelde service maken](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Een Azure Data Factory gekoppelde service maken")

2. Selecteer **verbindingen** in de linkerbenedenhoek van het venster en selecteer **+ Nieuw**.

    ![Verbindingen maken in azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Verbindingen maken in azure Data Factory")

3. Selecteer in het dialoog venster **nieuwe gekoppelde service** de optie **Azure Blob Storage** en selecteer vervolgens **door gaan**.

    ![Azure Storage gekoppelde service maken voor Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Azure Storage gekoppelde service maken voor Data Factory")

4. Geef de volgende waarden op voor de gekoppelde opslag service:

    |Eigenschap |Value |
    |---|---|
    |Name |Voer `HDIStorageLinkedService` in.|
    |Azure-abonnement |Selecteer uw abonnement in de vervolg keuzelijst.|
    |Naam van opslagaccount |Selecteer het Azure Storage-account dat u hebt gemaakt als onderdeel van het Power shell-script.|

    Selecteer vervolgens **Voltooien**.

    ![Geef een naam op voor de Azure Storage gekoppelde service](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Geef een naam op voor de Azure Storage gekoppelde service")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Een gekoppelde HDInsight-service op aanvraag maken

1. Selecteer nogmaals de knop **+ Nieuw** om een andere gekoppelde service te maken.

2. Selecteer in het venster **nieuwe gekoppelde service** het tabblad **Compute** .

3. Selecteer **Azure HDInsight**en selecteer **door gaan**.

    Een ![gekoppelde HDInsight-service maken voor Azure Data Factory] Een (./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "gekoppelde HDInsight-service maken voor Azure Data Factory")

4. Voer in het venster **nieuwe gekoppelde service** de volgende waarden in en laat de rest als standaard waarde:

    | Eigenschap | Value |
    | --- | --- |
    | Name | Voer `HDInsightLinkedService` in.|
    | type | Selecteer **HDInsight op aanvraag**. |
    | Een gekoppelde Azure Storage-service | Selecteer `HDIStorageLinkedService`. |
    | Clustertype | **Hadoop** selecteren |
    | Time To Live | Geef de duur op waarvoor het HDInsight-cluster beschikbaar moet zijn voordat het automatisch wordt verwijderd.|
    | Service-principal-id | Geef de toepassings-ID op van de Azure Active Directory service-principal die u hebt gemaakt als onderdeel van de vereisten. |
    | Sleutel van de Service-Principal | Geef de verificatie sleutel voor de service-principal van Azure Active Directory op. |
    | Voor voegsel van cluster naam | Geef een waarde op die wordt voorafgegaan door de cluster typen die door de data factory worden gemaakt. |
    |Subscription |Selecteer uw abonnement in de vervolg keuzelijst.|
    | Resourcegroep selecteren | Selecteer de resource groep die u hebt gemaakt als onderdeel van het Power shell-script dat u eerder hebt gebruikt.|
    |Regio selecteren | Selecteer een regio in de vervolg keuzelijst.|
    | Type besturings systeem/SSH-gebruikers naam van het cluster | Voer doorgaans `sshuser`een SSH-gebruikers naam in. |
    | Type besturings systeem/SSH-wacht woord voor het cluster | Geef een wacht woord op voor de SSH-gebruiker |
    | Type besturings systeem/cluster gebruikers naam | Voer meestal `admin`een gebruikers naam voor het cluster in. |
    | Type besturings systeem/cluster gebruikers wachtwoord | Geef een wacht woord op voor de cluster gebruiker. |

    Selecteer vervolgens **Voltooien**.

    ![Geef waarden op voor de gekoppelde HDInsight-service](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Geef waarden op voor de gekoppelde HDInsight-service")

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer de knop **+** (plus) en selecteer vervolgens **Pijplijn**.

    ![Een pijp lijn maken in azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Een pijp lijn maken in azure Data Factory")

2. Vouw in de werkset **activiteiten** het knoop punt **HDInsight**uit en sleep de **Hive** -activiteit naar het ontwerp oppervlak voor pijp lijnen. Geef op het tabblad **Algemeen** een naam op voor de activiteit.

    ![Activiteiten toevoegen aan Data Factory pijp lijn](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Activiteiten toevoegen aan Data Factory pijp lijn")

3. Zorg ervoor dat u de Hive-activiteit hebt geselecteerd, selecteer het tabblad **HDI-cluster** en selecteer in de vervolg keuzelijst **HDInsight gekoppelde service** de gekoppelde service die u eerder hebt gemaakt, **HDinightLinkedService**, voor HDInsight.

    ![Details van HDInsight-cluster voor de pijp lijn opgeven](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Details van HDInsight-cluster voor de pijp lijn opgeven")

4. Selecteer het tabblad **script** en voer de volgende stappen uit:

    1. Voor **gekoppelde script service**selecteert u **HDIStorageLinkedService** in de vervolg keuzelijst. Deze waarde is de gekoppelde opslag service die u eerder hebt gemaakt.

    1. Selecteer **voor bestandspad**bladeren in **opslag** en navigeer naar de locatie waar het script van de voorbeeld Hive beschikbaar is. Als u het Power shell-script eerder hebt uitgevoerd, is `adfgetstarted/hivescripts/partitionweblogs.hql`deze locatie.

        ![Details van Hive-script voor de pijp lijn opgeven](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Details van Hive-script voor de pijp lijn opgeven")

    1. Selecteer onder **Geavanceerde** > **para meters**de optie **automatische opvulling van script**. Met deze optie zoekt u naar para meters in het Hive-script waarvoor waarden moeten worden opgegeven tijdens runtime. Het script dat u gebruikt (**partitionweblogs. HQL**) heeft een **uitvoer** parameter. Geef de **waarde** in de notatie `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` op om te verwijzen naar een bestaande map op uw Azure Storage. Het pad is hoofdlettergevoelig. Dit is het pad waar de uitvoer van het script wordt opgeslagen. Het `wasbs` schema is nood zakelijk omdat voor opslag accounts nu beveiligde overdracht is vereist, standaard is ingeschakeld.
    
        ![Para meters opgeven voor het Hive-script](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Para meters opgeven voor het Hive-script")

1. Selecteer **valideren** om de pijp lijn te valideren. Selecteer de **>>** (pijl-rechts) om het validatievenster te sluiten.

    ![De Azure Data Factory-pijp lijn valideren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "De Azure Data Factory-pijp lijn valideren")

1. Selecteer tot slot **Alles publiceren** om de artefacten te publiceren naar Azure Data Factory.

    ![De Azure Data Factory-pijp lijn publiceren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "De Azure Data Factory-pijp lijn publiceren")

## <a name="trigger-a-pipeline"></a>Een pijp lijn activeren

1. Selecteer **trigger** > trigger toevoegen op de werk balk van het ontwerp**oppervlak.**

    ![De Azure Data Factory pijp lijn activeren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "De Azure Data Factory pijp lijn activeren")

2. Selecteer **volt ooien** in de pop-upbalk.

## <a name="monitor-a-pipeline"></a>Een pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet een pijplijn die worden uitgevoerd in de lijst **Pipeline Runs**. U ziet de status van de uitvoering onder de kolom **status** .

    ![De Azure Data Factory pijp lijn bewaken](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "De Azure Data Factory pijp lijn bewaken")

1. Selecteer **Vernieuwen** om de status te vernieuwen.

1. U kunt ook het pictogram **uitvoeringen van activiteit** selecteren om de uitvoering van de activiteit weer te geven die is gekoppeld aan de pijp lijn. In de onderstaande scherm afbeelding ziet u dat er slechts één activiteit wordt uitgevoerd omdat er slechts één activiteit is in de pijp lijn die u hebt gemaakt. Als u wilt terugkeren naar de vorige weer gave, selecteert u **pijp lijnen** in de richting van de bovenkant van de pagina.

    ![De activiteit van de Azure Data Factory-pijp lijn bewaken](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "De activiteit van de Azure Data Factory-pijp lijn bewaken")

## <a name="verify-the-output"></a>De uitvoer controleren

1. Als u de uitvoer wilt controleren, gaat u in het Azure Portal naar het opslag account dat u voor deze zelf studie hebt gebruikt. De volgende mappen of containers moeten worden weer geven:

    - U ziet een **adfgerstarted/outputfolder** die de uitvoer van het Hive-script bevat dat is uitgevoerd als onderdeel van de pijp lijn.

    - U ziet een **adfhdidatafactory\<\<-time stamp >** -container met de naam van de gekoppelde service >. Deze container is de standaard opslag locatie van het HDInsight-cluster dat is gemaakt als onderdeel van de pijplijn uitvoering.

    - U ziet een **adfjobs** -container met de Azure Data Factory-taak Logboeken.  

        ![De Azure Data Factory pijplijn uitvoer controleren](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "De Azure Data Factory pijplijn uitvoer controleren")

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u het HDInsight-cluster op aanvraag hebt gemaakt, hoeft u het HDInsight-cluster niet expliciet te verwijderen. Het cluster wordt verwijderd op basis van de configuratie die u hebt gegeven tijdens het maken van de pijp lijn. Maar zelfs nadat het cluster is verwijderd, blijven de opslag accounts die zijn gekoppeld aan het cluster bestaan. Dit gedrag is inherent aan het ontwerp, zodat u uw gegevens intact kunt houden. Als u de gegevens echter niet wilt behouden, kunt u het opslag account dat u hebt gemaakt, verwijderen.

U kunt ook de volledige resource groep verwijderen die u voor deze zelf studie hebt gemaakt. Hiermee verwijdert u het opslag account en de Azure Data Factory die u hebt gemaakt.

### <a name="delete-the-resource-group"></a>De resource groep verwijderen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groepen** in het linkerdeel venster.
1. Selecteer de naam van de resource groep die u in uw Power shell-script hebt gemaakt. Gebruik het filter als er te veel resource groepen worden weer gegeven. De resource groep wordt geopend.
1. Op de tegel **resources** hebt u het standaard opslag account en de Data Factory vermeld, tenzij u de resource groep met andere projecten deelt.
1. Selecteer **Resourcegroep verwijderen**. Hiermee verwijdert u het opslag account en de gegevens die zijn opgeslagen in het opslag account.

    ![Resource groep Azure Portal verwijderen](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Resource groep verwijderen")

1. Voer de naam van de resource groep in om het verwijderen te bevestigen en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u Azure Data Factory kunt gebruiken om een HDInsight-cluster op aanvraag te maken en [Apache Hive](https://hive.apache.org/) -taken uit te voeren. Ga naar het volgende artikel voor meer informatie over het maken van HDInsight-clusters met aangepaste configuratie.

> [!div class="nextstepaction"]
>[Azure HDInsight-clusters maken met aangepaste configuratie](hdinsight-hadoop-provision-linux-clusters.md)
