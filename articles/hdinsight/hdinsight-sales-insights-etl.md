---
title: 'Zelfstudie: Een end-to-end ETL-pijp lijn maken om verkoop inzichten af te leiden'
description: Meer informatie over het gebruik van ETL-pijp lijnen maken met Azure HDInsight voor het afleiden van inzichten op basis van verkoop gegevens met behulp van Spark on-demand clusters en Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 07714575bb4adfccf5d72a093874852c1bfebcc8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71843788"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Zelfstudie: Een end-to-end gegevens pijplijn maken om verkoop inzichten af te leiden

In deze zelf studie bouwt u een end-to-end gegevens pijplijn waarmee ETL-bewerkingen (extract), trans formatie en laden worden uitgevoerd. De pijp lijn gebruikt Apache Spark en Apache Hive clusters die worden uitgevoerd op Azure HDInsight voor het uitvoeren van query's en het bewerken van de gegevens. U kunt ook gebruikmaken van technologieën als Azure Data Lake Storage Gen2 voor gegevens opslag en Power BI voor visualisatie.

In deze gegevens pijplijn worden de gegevens uit verschillende winkels gecombineerd, worden alle ongewenste gegevens verwijderd, worden nieuwe gegevens toegevoegd en wordt deze weer in uw opslag geladen om zakelijke inzichten te visualiseren. Lees meer over ETL-pijp lijnen in [extract, Transform en load (ETL) op schaal](./hadoop/apache-hadoop-etl-at-scale.md).

![ETL-architectuur](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

Down load [Power bi Desktop](https://www.microsoft.com/download/details.aspx?id=45331) om zakelijke inzichten te visualiseren aan het eind van deze zelf studie.

## <a name="create-resources"></a>Resources maken

### <a name="clone-the-repository-with-scripts-and-data"></a>De opslag plaats klonen met scripts en gegevens

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Open Azure Cloud Shell in de bovenste menu balk. Selecteer uw abonnement voor het maken van een bestands share als Cloud Shell u hierom vraagt.

   ![Azure Cloud Shell openen](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Kies in het vervolg keuzemenu **omgeving selecteren** de optie **bash**.
1. Meld u aan bij uw Azure-account en stel het abonnement in. 
1. Stel de resource groep voor het project in.
   1. Kies een unieke naam voor de resourcegroep.
   1. Voer het volgende code fragment in Cloud Shell in om variabelen in te stellen die in latere stappen worden gebruikt:

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Down load de gegevens en scripts voor deze zelf studie in de [ETL-opslag plaats van HDInsight Sales](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) door de volgende opdrachten in Cloud shell in te voeren:

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Voer `ls` bij de shell-prompt in om te zien dat de volgende bestanden en mappen zijn gemaakt:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Azure-resources implementeren die nodig zijn voor de pijp lijn 

1. Voer uitvoerings machtigingen voor het script `chmod +x scripts/*.sh` toe.
1. Gebruik de opdracht `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` om het script uit te voeren om de volgende resources in azure te implementeren:

   1. Een Azure Blob-opslag account. Dit account bevat de verkoop gegevens van het bedrijf.
   2. Een Azure Data Lake Storage Gen2-account. Dit account wordt gebruikt als het opslag account voor beide HDInsight-clusters. Lees meer over HDInsight en Data Lake Storage Gen2 in [Azure hdinsight-integratie met data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
   3. Een door de gebruiker toegewezen beheerde identiteit. Dit account geeft de HDInsight-clusters toegang tot het Data Lake Storage Gen2-account.
   4. Een Apache Spark cluster. Dit cluster wordt gebruikt om de onbewerkte gegevens op te schonen en te transformeren.
   5. Een Apache Hive interactief query cluster. Met dit cluster kunt u query's uitvoeren op de verkoop gegevens en deze visualiseren met Power BI.
   6. Een virtueel Azure-netwerk dat wordt ondersteund door NSG-regels (netwerk beveiligings groep). Met dit virtuele netwerk kunnen clusters communiceren en hun communicatie beveiligen. 

Het maken van een cluster kan ongeveer 20 minuten duren.

Het script `resources.sh` bevat de volgende opdracht. Met deze opdracht wordt een Azure Resource Manager sjabloon (`resourcestemplate.json`) gebruikt om de opgegeven resources met de gewenste configuratie te maken.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

Het `resources.sh`-script uploadt ook de CSV-bestanden met verkoop gegevens naar het zojuist gemaakte Blob Storage-account met behulp van deze opdracht:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

Het standaard wachtwoord voor SSH-toegang tot de clusters is `Thisisapassword1`. Als u het wacht woord wilt wijzigen, gaat u naar het bestand `resourcesparameters.json` en wijzigt u het wacht woord voor de para meters `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` en `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Implementatie controleren en resource gegevens verzamelen

1. Als u de status van uw implementatie wilt controleren, gaat u naar de resource groep op de Azure Portal. Selecteer **implementaties** onder **instellingen**. Selecteer de naam van uw implementatie, `ResourcesDeployment`. Hier ziet u de resources die zijn geïmplementeerd en de resources die nog worden uitgevoerd.
1. Nadat de implementatie is voltooid, gaat u naar de Azure Portal > **resource groepen** > < RESOURCE_GROUP_NAME->.
1. Zoek het nieuwe Azure-opslag account dat is gemaakt voor het opslaan van de verkoop bestanden. De naam van het opslag account begint met `blob` en bevat een wille keurige teken reeks. Ga als volgt te werk:
   1. Noteer de naam van het opslag account voor later gebruik.
   1. Selecteer de naam van het Blob Storage-account.
   1. Selecteer aan de linkerkant van de portal onder **instellingen**de optie **toegangs sleutels**.
   1. Kopieer de teken reeks in het vakje **key1** en sla deze op voor later gebruik.
1. Zoek het Data Lake Storage Gen2-account dat is gemaakt als opslag voor de HDInsight-clusters. Dit account bevindt zich in dezelfde resource groep als het Blob Storage-account, maar begint met `adlsgen2`. Ga als volgt te werk:
   1. Noteer de naam van het Data Lake Storage Gen2-account.
   1. Selecteer de naam van het Data Lake Storage Gen2-account.
   1. Selecteer aan de linkerkant van de portal onder **instellingen**de optie **toegangs sleutels**.
   1. Kopieer de teken reeks in het vakje **key1** en sla deze op voor later gebruik.

> [!Note]
> Nadat u de namen van de opslag accounts weet, kunt u de account sleutels ophalen met behulp van de volgende opdracht bij de Azure Cloud Shell prompt:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Data factory maken

Azure Data Factory is een hulp programma waarmee Azure-pijp lijnen kunnen worden geautomatiseerd. Het is niet de enige manier om deze taken uit te voeren, maar het is een uitstekende manier om de processen te automatiseren. Raadpleeg de [Azure Data Factory documentatie](https://azure.microsoft.com/en-us/services/data-factory/)voor meer informatie over Azure Data Factory. 

Deze data factory heeft één pijp lijn met twee activiteiten: 

- Met de eerste activiteit worden de gegevens van Azure Blob-opslag gekopieerd naar het Data Lake Storage gen 2-opslag account om gegevens opname te simuleren.
- Met de tweede activiteit worden de gegevens in het Spark-cluster getransformeerd. Met het script worden de gegevens getransformeerd door ongewenste kolommen te verwijderen. Er wordt ook een nieuwe kolom toegevoegd die de omzet berekent die door één trans actie wordt gegenereerd.

Als u uw Azure Data Factory pijp lijn wilt instellen, voert u het `adf.sh`-script uit:

1. Gebruik `chmod +x adf.sh` om uitvoer machtigingen voor het bestand toe te voegen.
1. Gebruik `./adf.sh` om het script uit te voeren. 

Dit script doet het volgende:

1. Hiermee maakt u een service-principal met `Storage Blob Data Contributor`-machtigingen voor het Data Lake Storage Gen2-opslag account.
1. Hiermee wordt een verificatie token opgehaald om POST-aanvragen te autoriseren naar het [Data Lake Storage Gen2 bestandssysteem rest API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Hiermee wordt de werkelijke naam van uw Data Lake Storage Gen2-opslag account in de `sparktransform.py`-en `query.hql`-bestanden ingevuld.
1. Hiermee worden opslag sleutels voor de Data Lake Storage Gen2-en Blob Storage-accounts opgehaald.
1. Hiermee maakt u een nieuwe resource-implementatie om een Azure Data Factory pijp lijn te maken, met gekoppelde services en activiteiten. De opslag sleutels worden door gegeven als para meters aan het sjabloon bestand, zodat de gekoppelde services op de juiste wijze toegang hebben tot de opslag accounts.

De Data Factory-pijp lijn wordt geïmplementeerd met de volgende opdracht:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>De gegevens pijplijn uitvoeren

### <a name="trigger-the-data-factory-activities"></a>De Data Factory activiteiten activeren

Met de eerste activiteit in de Data Factory pijp lijn die u hebt gemaakt, worden de gegevens uit de Blob-opslag naar Data Lake Storage Gen2 verplaatst. Met de tweede activiteit worden de Spark-trans formaties op de gegevens toegepast en worden de getransformeerde CSV-bestanden op een nieuwe locatie opgeslagen. Het kan enkele minuten duren voordat de volledige pijp lijn is voltooid.

Als u de pijp lijnen wilt activeren, kunt u het volgende doen:

- Voer de volgende opdrachten uit om de Data Factory-pijp lijnen in Power shell te activeren: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Open de data factory en selecteer **& controle**maken. Activeer de Kopieer pijplijn en vervolgens de Spark-pijp lijn vanuit de portal. Zie voor meer informatie over het activeren van pijp lijnen via de portal [on-demand Apache Hadoop clusters maken in HDInsight met behulp van Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Om te controleren of de pijp lijnen zijn uitgevoerd, kunt u een van de volgende stappen uitvoeren:

- Ga naar de sectie **monitor** in uw Data Factory via de portal.
- Ga in Azure Storage Explorer naar uw Data Lake Storage gen 2-opslag account. Ga naar het bestands systeem van @no__t 0, ga naar de map `transformed` en controleer de inhoud ervan om te zien of de pijp lijn is geslaagd.

Zie [dit artikel over het gebruik van Jupyter notebook](/spark/apache-spark-load-data-run-query.md)voor andere manieren om gegevens te transformeren met behulp van HDInsight.

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Een tabel op het interactieve query cluster maken om gegevens op Power BI weer te geven

1. Kopieer het bestand `query.hql` naar het LLAP-cluster met behulp van SCP:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Gebruik SSH om toegang te krijgen tot het LLAP-cluster met behulp van de volgende opdracht en voer uw wacht woord in. Als u het `resourcesparameters.json`-bestand niet hebt gewijzigd, wordt het wacht woord `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Gebruik de volgende opdracht om het script uit te voeren:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Met dit script wordt een beheerde tabel gemaakt op het interactieve query cluster waartoe u toegang hebt via Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Een Power BI-dash board maken op basis van verkoop gegevens

1. Open Power BI Desktop.
1. Selecteer **Gegevens ophalen**.
1. Zoeken naar een **HDInsight-interactief query cluster**.
1. Plak de URI voor het cluster daar. Gebruik hierbij de notatie `https://<LLAP CLUSTER NAME>.azurehdinsight.net`.

   Voer `default` in voor de data base.
1. Voer de gebruikers naam en het wacht woord in die u gebruikt voor toegang tot het cluster.

Nadat de gegevens zijn geladen, kunt u experimenteren met het dash board dat u wilt maken. Raadpleeg de volgende koppelingen om aan de slag te gaan met Power BI-Dash boards:

* [Inleiding tot Dash boards voor Power BI-ontwerpers](https://docs.microsoft.com/power-bi/service-dashboards)
* [Zelfstudie: Aan de slag met de Power BI-service @ no__t-0

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet meer wilt gebruiken, verwijdert u alle resources met behulp van de volgende opdracht, zodat er geen kosten in rekening worden gebracht.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [(ETL) uitpakken, transformeren en laden op schaal](./hadoop/apache-hadoop-etl-at-scale.md)
