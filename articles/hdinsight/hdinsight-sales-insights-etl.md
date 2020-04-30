---
title: 'Zelf studie: een end-to-end ETL-pijp lijn maken om verkoop inzichten in azure HDInsight af te leiden'
description: Meer informatie over het gebruik van ETL-pijp lijnen maken met Azure HDInsight voor het afleiden van inzichten op basis van verkoop gegevens met behulp van Spark on-demand clusters en Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: c213b0089af0af295d44afd38bbc5c17b6db159d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535227"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Zelf studie: een end-to-end gegevens pijplijn maken voor het afleiden van verkoop inzichten in azure HDInsight

In deze zelf studie bouwt u een end-to-end gegevens pijplijn waarmee ETL-bewerkingen (extract), trans formatie en laden worden uitgevoerd. De pijp lijn gebruikt [Apache Spark](./spark/apache-spark-overview.md) en Apache Hive clusters die worden uitgevoerd op Azure HDInsight voor het uitvoeren van query's en het bewerken van de gegevens. U kunt ook gebruikmaken van technologieën als Azure Data Lake Storage Gen2 voor gegevens opslag en Power BI voor visualisatie.

In deze gegevens pijplijn worden de gegevens uit verschillende winkels gecombineerd, worden alle ongewenste gegevens verwijderd, worden nieuwe gegevens toegevoegd en wordt deze weer in uw opslag geladen om zakelijke inzichten te visualiseren. Lees meer over ETL-pijp lijnen in [extract, Transform en load (ETL) op schaal](./hadoop/apache-hadoop-etl-at-scale.md).

![ETL-architectuur](./media/hdinsight-sales-insights-etl/architecture.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure CLI: ten minste versie 2.2.0. Zie [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

* JQ, een JSON-processor op de opdracht regel.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Een lid van de [ingebouwde rol eigenaar van Azure](../role-based-access-control/built-in-roles.md).

* Als u Power shell gebruikt om de Data Factory pijp lijn te activeren, hebt u de [AZ-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

* [Power bi Desktop](https://aka.ms/pbiSingleInstaller) Business Insights aan het einde van deze zelf studie te visualiseren.

## <a name="create-resources"></a>Resources maken

### <a name="clone-the-repository-with-scripts-and-data"></a>De opslag plaats klonen met scripts en gegevens

1. Meld u aan bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **deze** in de rechter bovenhoek van het code blok. Anders voert u de onderstaande opdracht in:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Zorg ervoor dat u lid bent van de [eigenaar](../role-based-access-control/built-in-roles.md)van de Azure-rol. Vervang `user@contoso.com` door uw account en voer de volgende opdracht in:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Als er geen record wordt geretourneerd, bent u geen lid en kunt u deze zelf studie niet volt ooien.

1. Down load de gegevens en scripts voor deze zelf studie vanuit de [ETL-opslag plaats van HDInsight Sales Insights](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Voer de volgende opdracht in:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Zorg `salesdata scripts templates` ervoor dat het is gemaakt. Controleer met de volgende opdracht:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Azure-resources implementeren die nodig zijn voor de pijp lijn

1. Voer de machtigingen voor uitvoeren voor alle scripts uit door het volgende in te voeren:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Stel de variabele in voor de resource groep. Vervang `RESOURCE_GROUP_NAME` door de naam van een bestaande of nieuwe resource groep en voer de volgende opdracht in:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Voer het script uit. Vervang `LOCATION` door een gewenste waarde en voer de volgende opdracht in:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Als u niet zeker weet welke regio u moet opgeven, kunt u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht [AZ account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list-locations) .

    Met de opdracht worden de volgende resources geïmplementeerd:

    * Een Azure Blob-opslag account. Dit account bevat de verkoop gegevens van het bedrijf.
    * Een Azure Data Lake Storage Gen2-account. Dit account wordt gebruikt als het opslag account voor beide HDInsight-clusters. Lees meer over HDInsight en Data Lake Storage Gen2 in [Azure hdinsight-integratie met data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Een door de gebruiker toegewezen beheerde identiteit. Dit account geeft de HDInsight-clusters toegang tot het Data Lake Storage Gen2-account.
    * Een Apache Spark cluster. Dit cluster wordt gebruikt om de onbewerkte gegevens op te schonen en te transformeren.
    * Een Apache Hive [interactief query](./interactive-query/apache-interactive-query-get-started.md) cluster. Met dit cluster kunt u query's uitvoeren op de verkoop gegevens en deze visualiseren met Power BI.
    * Een virtueel Azure-netwerk dat wordt ondersteund door NSG-regels (netwerk beveiligings groep). Met dit virtuele netwerk kunnen clusters communiceren en hun communicatie beveiligen.

Het maken van een cluster kan ongeveer 20 minuten duren.

Het standaard wachtwoord voor SSH-toegang tot de clusters `Thisisapassword1`is. Als u het wacht woord wilt wijzigen, gaat u naar `./templates/resourcesparameters_remainder.json` het bestand en wijzigt u het wacht `sparksshPassword`woord `sparkClusterLoginPassword`voor `llapClusterLoginPassword`de para `llapsshPassword` meters,, en.

### <a name="verify-deployment-and-collect-resource-information"></a>Implementatie controleren en resource gegevens verzamelen

1. Als u de status van uw implementatie wilt controleren, gaat u naar de resource groep op de Azure Portal. Selecteer onder **instellingen**de optie **implementaties**en vervolgens uw implementatie. Hier ziet u de resources die zijn geïmplementeerd en de resources die nog worden uitgevoerd.

1. Voer de volgende opdracht in om de namen van de clusters weer te geven:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Als u het Azure-opslag account en de toegangs sleutel wilt weer geven, voert u de volgende opdracht in:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Als u het Data Lake Storage Gen2 account en de toegangs sleutel wilt weer geven, voert u de volgende opdracht in:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

Azure Data Factory is een hulp programma waarmee Azure-pijp lijnen kunnen worden geautomatiseerd. Het is niet de enige manier om deze taken uit te voeren, maar het is een uitstekende manier om de processen te automatiseren. Raadpleeg de [Azure Data Factory documentatie](https://azure.microsoft.com/services/data-factory/)voor meer informatie over Azure Data Factory.

Deze data factory heeft één pijp lijn met twee activiteiten:

* Met de eerste activiteit worden de gegevens van Azure Blob-opslag gekopieerd naar het Data Lake Storage gen 2-opslag account om gegevens opname te simuleren.
* Met de tweede activiteit worden de gegevens in het Spark-cluster getransformeerd. Met het script worden de gegevens getransformeerd door ongewenste kolommen te verwijderen. Er wordt ook een nieuwe kolom toegevoegd die de omzet berekent die door één trans actie wordt gegenereerd.

Als u uw Azure Data Factory pijp lijn wilt instellen, voert u de onderstaande opdracht uit.  U moet nog steeds de `hdinsight-sales-insights-etl` map hebben.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Dit script doet het volgende:

1. Hiermee maakt u een service `Storage Blob Data Contributor` -principal met machtigingen voor de data Lake Storage Gen2-opslag account.
1. Hiermee wordt een verificatie token opgehaald om POST-aanvragen te autoriseren naar het [Data Lake Storage Gen2 bestandssysteem rest API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. De werkelijke naam van uw Data Lake Storage Gen2 Storage-account wordt ingevuld in `sparktransform.py` de `query.hql` bestanden en.
1. Hiermee worden opslag sleutels voor de Data Lake Storage Gen2-en Blob Storage-accounts opgehaald.
1. Hiermee maakt u een nieuwe resource-implementatie om een Azure Data Factory pijp lijn te maken, met gekoppelde services en activiteiten. De opslag sleutels worden door gegeven als para meters aan het sjabloon bestand, zodat de gekoppelde services op de juiste wijze toegang hebben tot de opslag accounts.

## <a name="run-the-data-pipeline"></a>De gegevens pijplijn uitvoeren

### <a name="trigger-the-data-factory-activities"></a>De Data Factory activiteiten activeren

Met de eerste activiteit in de Data Factory pijp lijn die u hebt gemaakt, worden de gegevens uit de Blob-opslag naar Data Lake Storage Gen2 verplaatst. Met de tweede activiteit worden de Spark-trans formaties op de gegevens toegepast en worden de getransformeerde CSV-bestanden op een nieuwe locatie opgeslagen. Het kan enkele minuten duren voordat de volledige pijp lijn is voltooid.

Voer de volgende opdracht in om de naam van de Data Factory op te halen:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Als u de pijp lijn wilt activeren, kunt u het volgende doen:

* Activeer de Data Factory-pijp lijn in Power shell. Vervang `RESOURCEGROUP`, en `DataFactoryName` met de juiste waarden, en voer vervolgens de volgende opdrachten uit:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Voer de bewerking `Get-AzDataFactoryV2PipelineRun` opnieuw uit als dat nodig is om de voortgang te bewaken.

    of

* Open de data factory en selecteer **& controle**maken. Activeer de `IngestAndTransform` pijp lijn vanuit de portal. Zie voor meer informatie over het activeren van pijp lijnen via de portal [on-demand Apache Hadoop clusters maken in HDInsight met behulp van Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Om te controleren of de pijp lijn is uitgevoerd, kunt u een van de volgende stappen uitvoeren:

* Ga naar de sectie **monitor** in uw Data Factory via de portal.
* Ga in Azure Storage Explorer naar uw Data Lake Storage gen 2-opslag account. Ga naar het `files` bestands systeem en ga naar de `transformed` map en controleer de inhoud om te zien of de pijp lijn is geslaagd.

Zie [dit artikel over het gebruik van Jupyter notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query)voor andere manieren om gegevens te transformeren met behulp van HDInsight.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Een tabel op het interactieve query cluster maken om gegevens op Power BI weer te geven

1. Kopieer het `query.hql` bestand naar het LLAP-cluster met behulp van SCP. Voer de opdracht in:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Herinnering: het standaard wachtwoord is `Thisisapassword1`.

1. Gebruik SSH om toegang te krijgen tot het LLAP-cluster. Voer de opdracht in:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Gebruik de volgende opdracht om het script uit te voeren:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Met dit script wordt een beheerde tabel gemaakt op het interactieve query cluster waartoe u toegang hebt via Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Een Power BI-dash board maken op basis van verkoop gegevens

1. Open Power BI Desktop.

1. Ga in het menu naar **gegevens** > ophalen **...**  > Interactieve **Azure** > **HDInsight-query**.

1. Selecteer **Verbinden**.

1. In het dialoog venster **interactieve query van HDInsight** :
    1. Voer in het tekstvak **Server** de naam in van uw LLAP-cluster in de indeling van `https://LLAPCLUSTERNAME.azurehdinsight.net`.
    1. Voer `default`in het tekstvak **Data Base** in.
    1. Selecteer **OK**.

1. In het dialoog venster **AzureHive** :
    1. Voer `admin`in het tekstvak **gebruikers naam** in.
    1. Voer `Thisisapassword1`in het tekstvak **wacht woord** in.
    1. Selecteer **Verbinden**.

1. Selecteer in **Navigator**de `sales`optie en/of `sales_raw` om een voor beeld van de gegevens te bekijken. Nadat de gegevens zijn geladen, kunt u experimenteren met het dash board dat u wilt maken. Raadpleeg de volgende koppelingen om aan de slag te gaan met Power BI-Dash boards:

* [Inleiding tot dashboards voor Power BI-ontwerpers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Zelf studie: aan de slag met de Power BI-service](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u alle resources met behulp van de volgende opdracht, zodat er geen kosten in rekening worden gebracht.

1. Als u de resource groep wilt verwijderen, voert u de volgende opdracht in:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Als u de Service-Principal wilt verwijderen, voert u de volgende opdrachten in:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [(ETL) uitpakken, transformeren en laden op schaal](./hadoop/apache-hadoop-etl-at-scale.md)
