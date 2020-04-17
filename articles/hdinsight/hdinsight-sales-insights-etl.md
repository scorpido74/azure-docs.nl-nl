---
title: 'Zelfstudie: Maak een end-to-end ETL-pijplijn om verkoopinzichten in Azure HDInsight te verkrijgen'
description: Meer informatie over het gebruik van Create ETL-pijplijnen met Azure HDInsight om inzichten uit verkoopgegevens te halen met Spark on-demand clusters en Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: c213b0089af0af295d44afd38bbc5c17b6db159d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535227"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Zelfstudie: Maak een end-to-end datapijplijn om verkoopinzichten in Azure HDInsight te verkrijgen

In deze zelfstudie bouwt u een end-to-end gegevenspijplijn die eTL-bewerkingen (extraheren, transformeren en laden) uitvoert. De pijplijn gebruikt [Apache Spark-](./spark/apache-spark-overview.md) en Apache Hive-clusters die worden uitgevoerd op Azure HDInsight voor het opvragen en manipuleren van de gegevens. U gebruikt ook technologieën zoals Azure Data Lake Storage Gen2 voor gegevensopslag en Power BI voor visualisatie.

Deze gegevenspijplijn combineert de gegevens uit verschillende winkels, verwijdert ongewenste gegevens, voegt nieuwe gegevens toe en laadt dit alles terug naar uw opslag om bedrijfsinzichten te visualiseren. Lees meer over ETL-pijplijnen in [Extract, transform, and load (ETL) op schaal.](./hadoop/apache-hadoop-etl-at-scale.md)

![ETL-architectuur](./media/hdinsight-sales-insights-etl/architecture.png)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure CLI - ten minste versie 2.2.0. Zie [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

* jq, een command-line JSON processor.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Een lid van de [azure ingebouwde rol - eigenaar](../role-based-access-control/built-in-roles.md).

* Als u PowerShell gebruikt om de datafabriekpijplijn te activeren, hebt u de [Az-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) om zakelijke inzichten aan het einde van deze zelfstudie te visualiseren.

## <a name="create-resources"></a>Resources maken

### <a name="clone-the-repository-with-scripts-and-data"></a>Kloon de repository met scripts en gegevens

1. Meld u aan bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **Probeer deze** in de rechterbovenhoek van het codeblok. Voer anders de onderstaande opdracht in:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Controleer of u lid bent van de [Azure-roleigenaar](../role-based-access-control/built-in-roles.md). Vervang `user@contoso.com` uw account en voer de opdracht in:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Als er geen record wordt geretourneerd, bent u geen lid en u deze zelfstudie niet voltooien.

1. Download de gegevens en scripts voor deze zelfstudie uit de [HDInsight sales insights ETL repository.](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) Voer de volgende opdracht in:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Zorg `salesdata scripts templates` ervoor dat er een gemaakt. Controleer met de volgende opdracht:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Azure-resources implementeren die nodig zijn voor de pijplijn

1. Voeg uitvoermachtigingen toe voor alle scripts door het invoeren van:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Variabele instellen voor resourcegroep. Vervang `RESOURCE_GROUP_NAME` de naam van een bestaande of nieuwe resourcegroep en voer de opdracht in:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Voer het script uit. Vervang `LOCATION` door een gewenste waarde en voer de opdracht in:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Als u niet zeker weet welke regio u wilt opgeven, u een lijst met ondersteunde regio's voor uw abonnement ophalen met de opdracht lijstlocaties van [az-account.](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list-locations)

    De opdracht implementeert de volgende bronnen:

    * Een Azure Blob-opslagaccount. Deze rekening houdt de verkoopgegevens van het bedrijf in.
    * Een Azure Data Lake Storage Gen2-account. Dit account zal dienen als opslagaccount voor beide HDInsight-clusters. Lees meer over HDInsight en Data Lake Storage Gen2 in [Azure HDInsight-integratie met Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Een door de gebruiker toegewezen beheerde identiteit. Dit account geeft de HDInsight-clusters toegang tot het Data Lake Storage Gen2-account.
    * Een Apache Spark cluster. Dit cluster wordt gebruikt om de ruwe gegevens op te schonen en te transformeren.
    * Een cluster [interactieve query van](./interactive-query/apache-interactive-query-get-started.md) Apache Hive. Met dit cluster kunnen de verkoopgegevens worden opgevraagd en gevisualiseren met Power BI.
    * Een Virtueel Azure-netwerk dat wordt ondersteund door NSG-regels (Network Security Group). Dit virtuele netwerk stelt de clusters in staat om te communiceren en hun communicatie te beveiligen.

Het maken van clusteren kan ongeveer 20 minuten duren.

Het standaardwachtwoord voor SSH-toegang `Thisisapassword1`tot de clusters is . Als u het wachtwoord wilt wijzigen, gaat u `./templates/resourcesparameters_remainder.json` naar het bestand en wijzigt u het `sparksshPassword`wachtwoord voor de `sparkClusterLoginPassword`, , `llapClusterLoginPassword`en `llapsshPassword` parameters.

### <a name="verify-deployment-and-collect-resource-information"></a>Implementatie verifiëren en brongegevens verzamelen

1. Als u de status van uw implementatie wilt controleren, gaat u naar de brongroep op de Azure-portal. Selecteer **Onder Instellingen**selecteer **Implementatieen**en vervolgens uw implementatie. Hier ziet u de resources die zijn geïmplementeerd en de resources die nog in uitvoering zijn.

1. Voer de volgende opdracht in om de namen van de clusters weer te geven:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Voer de volgende opdracht in om het Azure-opslagaccount en de toegangssleutel weer te geven:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Voer de volgende opdracht in om het Gen2-account en de toegangssleutel voor Gegevensmeeropslag te bekijken:

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

Azure Data Factory is een tool waarmee Azure Pipelines kunnen worden geautomatiseerd. Het is niet de enige manier om deze taken uit te voeren, maar het is een geweldige manier om de processen te automatiseren. Zie de documentatie van Azure [Data Factory](https://azure.microsoft.com/services/data-factory/)voor meer informatie over Azure Data Factory.

Deze gegevensfabriek heeft één pijplijn met twee activiteiten:

* De eerste activiteit kopieert de gegevens van Azure Blob-opslag naar het Data Lake Storage Gen 2-opslagaccount om gegevensopname na te bootsen.
* De tweede activiteit transformeert de gegevens in het Spark-cluster. Het script transformeert de gegevens door ongewenste kolommen te verwijderen. Het voegt ook een nieuwe kolom toe die de inkomsten berekent die een enkele transactie genereert.

Voer de onderstaande opdracht uit om de Azure Data Factory-pijplijn in te stellen.  Je zou nog `hdinsight-sales-insights-etl` steeds bij de map moeten zijn.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Dit script doet de volgende dingen:

1. Hiermee maakt u `Storage Blob Data Contributor` een serviceprincipal met machtigingen op het Data Lake Storage Gen2-opslagaccount.
1. Verkrijg een verificatietoken om POST-aanvragen te autoriseren in de [Rest API voor datalakeopslag Gen2-bestandssysteem.](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)
1. Hiermee vult u de werkelijke naam van uw `sparktransform.py` Data `query.hql` Lake Storage Gen2-opslagaccount in en bestanden in.
1. Verkrijg opslagsleutels voor de Opslagaccounts Data Lake Storage Gen2 en Blob.
1. Hiermee maakt u een andere bronimplementatie om een Azure Data Factory-pijplijn te maken, met de bijbehorende gekoppelde services en -activiteiten. Het geeft de opslagsleutels als parameters door aan het sjabloonbestand, zodat de gekoppelde services de opslagaccounts correct kunnen openen.

## <a name="run-the-data-pipeline"></a>De gegevenspijplijn uitvoeren

### <a name="trigger-the-data-factory-activities"></a>De activiteiten van de Data Factory activeren

De eerste activiteit in de datafabriek-pijplijn die u hebt gemaakt, verplaatst de gegevens van Blob-opslag naar Data Lake Storage Gen2. De tweede activiteit past de Spark-transformaties toe op de gegevens en slaat de getransformeerde CSV-bestanden op een nieuwe locatie op. Het kan enkele minuten duren voordat de hele pijplijn is voltooid.

Voer de volgende opdracht in om de naam van de gegevensfabriek op te halen:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Als u de pijplijn wilt activeren, u het andere doen:

* Activeer de datafabriekpijplijn in PowerShell. Vervang `RESOURCEGROUP`en `DataFactoryName` met de juiste waarden de volgende opdrachten:

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

    Indien nodig `Get-AzDataFactoryV2PipelineRun` opnieuw uit te voeren om de voortgang te controleren.

    of

* Open de gegevensfabriek en selecteer **Auteur & Monitor**. Activeer `IngestAndTransform` de pijplijn van het portaal. Zie [On-demand Apache Hadoop-clusters maken in HDInsight met Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)voor informatie over het activeren van pijplijnen via de portal.

Als u wilt controleren of de pijplijn is uitgevoerd, u een van de volgende stappen uitvoeren:

* Ga via de portal naar de sectie **Monitor** in uw gegevensfabriek.
* Ga in Azure Storage Explorer naar uw Data Lake Storage Gen 2-opslagaccount. Ga naar `files` het bestandssysteem en `transformed` ga vervolgens naar de map en controleer de inhoud ervan om te zien of de pijplijn is geslaagd.

Zie dit artikel over het gebruik [van Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query)voor andere manieren om gegevens te transformeren met HDInsight.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Een tabel maken in het cluster Interactieve query om gegevens over Power BI weer te geven

1. Kopieer `query.hql` het bestand naar het LLAP-cluster met behulp van SCP. Voer de opdracht in:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Herinnering: Het standaardwachtwoord is `Thisisapassword1`.

1. Gebruik SSH om toegang te krijgen tot het LLAP-cluster. Voer de opdracht in:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Gebruik de volgende opdracht om het script uit te voeren:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Met dit script wordt een beheerde tabel gemaakt in het cluster Interactieve query die u vanuit Power BI openen.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Een Power BI-dashboard maken op basis van verkoopgegevens

1. Open Power BI Desktop.

1. Navigeer in het menu naar **Gegevens ophalen** > **Meer...**  >  **Interactieve query** > **azure HDInsight**.

1. Selecteer **Verbinden**.

1. In het dialoogvenster **INTERACTIEVE QUERY HDInsight:**
    1. Voer in het tekstvak **Server** de naam van uw `https://LLAPCLUSTERNAME.azurehdinsight.net`LLAP-cluster in de opmaak van .
    1. Voer in het **tekstvak van** de database . `default`
    1. Selecteer **OK**.

1. Vanuit het dialoogvenster **AzureHive:**
    1. Voer **in** het tekstvak `admin`Gebruikersnaam de invoer in .
    1. Voer in het tekstvak **Wachtwoord** de invoer in `Thisisapassword1`.
    1. Selecteer **Verbinden**.

1. Selecteer en/of `sales_raw` om een voorbeeld van de gegevens te bekijken in **Navigator.** `sales` Nadat de gegevens zijn geladen, u experimenteren met het dashboard dat u wilt maken. Bekijk de volgende koppelingen om aan de slag te gaan met Power BI-dashboards:

* [Inleiding tot dashboards voor Power BI-ontwerpers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Zelfstudie: Aan de slag met de Power BI-service](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet blijft gebruiken, verwijdert u alle bronnen met behulp van de volgende opdracht, zodat er geen kosten voor in rekening worden gebracht.

1. Als u de brongroep wilt verwijderen, voert u de opdracht in:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Voer de opdrachten in om de serviceprincipal te verwijderen:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ETL op schaal extraheren, transformeren en laden (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
