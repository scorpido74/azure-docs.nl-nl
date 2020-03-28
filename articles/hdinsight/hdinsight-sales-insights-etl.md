---
title: 'Zelfstudie: Maak een end-to-end ETL-pijplijn om verkoopinzichten in Azure HDInsight te verkrijgen'
description: Meer informatie over het gebruik van Create ETL-pijplijnen met Azure HDInsight om inzichten uit verkoopgegevens te halen met Spark on-demand clusters en Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247262"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Zelfstudie: Maak een end-to-end datapijplijn om verkoopinzichten in Azure HDInsight te verkrijgen

In deze zelfstudie bouwt u een end-to-end gegevenspijplijn die eTL-bewerkingen (extraheren, transformeren en laden) uitvoert. De pijplijn gebruikt [Apache Spark-](./spark/apache-spark-overview.md) en Apache Hive-clusters die worden uitgevoerd op Azure HDInsight voor het opvragen en manipuleren van de gegevens. U gebruikt ook technologieën zoals Azure Data Lake Storage Gen2 voor gegevensopslag en Power BI voor visualisatie.

Deze gegevenspijplijn combineert de gegevens uit verschillende winkels, verwijdert ongewenste gegevens, voegt nieuwe gegevens toe en laadt dit alles terug naar uw opslag om bedrijfsinzichten te visualiseren. Lees meer over ETL-pijplijnen in [Extract, transform, and load (ETL) op schaal.](./hadoop/apache-hadoop-etl-at-scale.md)

![ETL-architectuur](./media/hdinsight-sales-insights-etl/architecture.png)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure CLI. Zie [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Een lid van de [azure ingebouwde rol - eigenaar](../role-based-access-control/built-in-roles.md).

* [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) om zakelijke inzichten aan het einde van deze zelfstudie te visualiseren.

## <a name="create-resources"></a>Resources maken

### <a name="clone-the-repository-with-scripts-and-data"></a>Kloon de repository met scripts en gegevens

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Open Azure Cloud Shell via de bovenste menubalk. Selecteer uw abonnement voor het maken van een bestandsshare als Cloud Shell u daarom vraagt.

   ![Azure Cloud Shell openen](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. Kies **Bash**in het vervolgkeuzemenu **Select-omgeving.**

1. Controleer of u lid bent van de [Azure-roleigenaar](../role-based-access-control/built-in-roles.md). Vervang `user@contoso.com` uw account en voer de opdracht in:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Als er geen record wordt geretourneerd, bent u geen lid en u deze zelfstudie niet voltooien.

1. Uw abonnementen aanbieden die de opdracht invoeren:

    ```azurecli
    az account list --output table
    ```

    Let op de id van het abonnement dat u voor dit project gebruikt.

1. Stel het abonnement in dat u voor dit project gebruikt. Vervang `SUBSCRIPTIONID` door de werkelijke waarde en voer de opdracht in.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. Maak een nieuwe resourcegroep voor het project. Vervang `RESOURCEGROUP` door de gewenste naam en voer de opdracht in.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. Download de gegevens en scripts voor deze zelfstudie uit de [HDInsight sales insights ETL repository.](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)  Voer de volgende opdracht in:

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

1. Voer het script uit. Vervang `RESOURCE_GROUP_NAME` `LOCATION` en met de relevante waarden en voer de opdracht in:

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    De opdracht implementeert de volgende bronnen:

    * Een Azure Blob-opslagaccount. Deze rekening houdt de verkoopgegevens van het bedrijf in.
    * Een Azure Data Lake Storage Gen2-account. Dit account zal dienen als opslagaccount voor beide HDInsight-clusters. Lees meer over HDInsight en Data Lake Storage Gen2 in [Azure HDInsight-integratie met Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Een door de gebruiker toegewezen beheerde identiteit. Dit account geeft de HDInsight-clusters toegang tot het Data Lake Storage Gen2-account.
    * Een Apache Spark cluster. Dit cluster wordt gebruikt om de ruwe gegevens op te schonen en te transformeren.
    * Een cluster [interactieve query van](./interactive-query/apache-interactive-query-get-started.md) Apache Hive. Met dit cluster kunnen de verkoopgegevens worden opgevraagd en gevisualiseren met Power BI.
    * Een Virtueel Azure-netwerk dat wordt ondersteund door NSG-regels (Network Security Group). Dit virtuele netwerk stelt de clusters in staat om te communiceren en hun communicatie te beveiligen.

Het maken van clusteren kan ongeveer 20 minuten duren.

Het `resources.sh` script bevat de volgende opdrachten. U hoeft deze opdrachten niet uit te voeren als u het script al in de vorige stap hebt uitgevoerd.

* `az group deployment create`- Met deze opdracht wordt`resourcestemplate.json`een Azure Resource Manager-sjabloon ( ) gebruikt om de opgegeven resources met de gewenste configuratie te maken.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch`- Met deze opdracht worden de csv-bestanden met deze opdracht geüpload naar het nieuw gemaakte Blob-opslagaccount:

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

Het standaardwachtwoord voor SSH-toegang `Thisisapassword1`tot de clusters is . Als u het wachtwoord wilt wijzigen, gaat u `resourcesparameters.json` naar het bestand en wijzigt u het `sparksshPassword`wachtwoord voor de `sparkClusterLoginPassword`, , `llapClusterLoginPassword`en `llapsshPassword` parameters.

### <a name="verify-deployment-and-collect-resource-information"></a>Implementatie verifiëren en brongegevens verzamelen

1. Als u de status van uw implementatie wilt controleren, gaat u naar de brongroep op de Azure-portal. Selecteer **Implementaties** onder **Instellingen**. Selecteer de naam van `ResourcesDeployment`uw implementatie. Hier ziet u de resources die zijn geïmplementeerd en de resources die nog in uitvoering zijn.

1. Voer de volgende opdracht in om de namen van de clusters weer te geven:

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Voer de volgende opdracht in om het Azure-opslagaccount en de toegangssleutel weer te geven:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Voer de volgende opdracht in om het Gen2-account en de toegangssleutel voor Gegevensmeeropslag te bekijken:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

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

Voer de volgende opdracht uit om de azure data factory-pijplijn in te stellen:

```bash
./scripts/adf.sh
```

Dit script doet de volgende dingen:

1. Hiermee maakt u `Storage Blob Data Contributor` een serviceprincipal met machtigingen op het Data Lake Storage Gen2-opslagaccount.
1. Verkrijg een verificatietoken om POST-aanvragen te autoriseren in de [Rest API voor datalakeopslag Gen2-bestandssysteem.](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)
1. Hiermee vult u de werkelijke naam van uw `sparktransform.py` Data `query.hql` Lake Storage Gen2-opslagaccount in en bestanden in.
1. Verkrijg opslagsleutels voor de Opslagaccounts Data Lake Storage Gen2 en Blob.
1. Hiermee maakt u een andere bronimplementatie om een Azure Data Factory-pijplijn te maken, met de bijbehorende gekoppelde services en -activiteiten. Het geeft de opslagsleutels als parameters door aan het sjabloonbestand, zodat de gekoppelde services de opslagaccounts correct kunnen openen.

De pijplijn Gegevensfabriek wordt geïmplementeerd via de volgende opdracht:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>De gegevenspijplijn uitvoeren

### <a name="trigger-the-data-factory-activities"></a>De activiteiten van de Data Factory activeren

De eerste activiteit in de datafabriek-pijplijn die u hebt gemaakt, verplaatst de gegevens van Blob-opslag naar Data Lake Storage Gen2. De tweede activiteit past de Spark-transformaties toe op de gegevens en slaat de getransformeerde CSV-bestanden op een nieuwe locatie op. Het kan enkele minuten duren voordat de hele pijplijn is voltooid.

Als u de pijplijnen wilt activeren, u het andere doen:

* Activeer de Data Factory-pijplijnen in PowerShell. Vervang `DataFactoryName` de naam van de werkelijke gegevensfabriek en voer vervolgens de volgende opdrachten uit:

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    of

* Open de gegevensfabriek en selecteer **Auteur & Monitor**. Activeer de kopiepijplijn en vervolgens de Spark-pijplijn van de portal. Zie [On-demand Apache Hadoop-clusters maken in HDInsight met Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)voor informatie over het activeren van pijplijnen via de portal.

Als u wilt controleren of de pijplijnen zijn uitgevoerd, u een van de volgende stappen uitvoeren:

* Ga via de portal naar de sectie **Monitor** in uw gegevensfabriek.
* Ga in Azure Storage Explorer naar uw Data Lake Storage Gen 2-opslagaccount. Ga naar `files` het bestandssysteem en `transformed` ga vervolgens naar de map en controleer de inhoud ervan om te zien of de pijplijn is geslaagd.

Zie dit artikel over het gebruik [van Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query)voor andere manieren om gegevens te transformeren met HDInsight.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Een tabel maken in het cluster Interactieve query om gegevens over Power BI weer te geven

1. Kopieer `query.hql` het bestand naar het LLAP-cluster met behulp van SCP. Vervang `LLAPCLUSTERNAME` door de werkelijke naam en voer de opdracht in:

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Gebruik SSH om toegang te krijgen tot het LLAP-cluster. Vervang `LLAPCLUSTERNAME` door de werkelijke naam en voer de opdracht in. Als u het `resourcesparameters.json` bestand niet hebt gewijzigd, is `Thisisapassword1`het wachtwoord .

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Gebruik de volgende opdracht om het script uit te voeren:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Met dit script wordt een beheerde tabel gemaakt in het cluster Interactieve query die u vanuit Power BI openen.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Een Power BI-dashboard maken op basis van verkoopgegevens

1. Open Power BI Desktop.
1. Selecteer **Gegevens ophalen**.
1. Zoeken naar **hdinsight interactive query cluster**.
1. Plak daar de URI voor uw cluster. Gebruik hierbij de notatie `https://LLAPCLUSTERNAME.azurehdinsight.net`.

   Voer `default` voor de database in.
1. Voer de gebruikersnaam en het wachtwoord in die u gebruikt om toegang te krijgen tot het cluster.

Nadat de gegevens zijn geladen, u experimenteren met het dashboard dat u wilt maken. Bekijk de volgende koppelingen om aan de slag te gaan met Power BI-dashboards:

* [Inleiding tot dashboards voor Power BI-ontwerpers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Zelfstudie: Aan de slag met de Power BI-service](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet blijft gebruiken, verwijdert u alle bronnen met behulp van de volgende opdracht, zodat er geen kosten voor in rekening worden gebracht.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ETL op schaal extraheren, transformeren en laden (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
