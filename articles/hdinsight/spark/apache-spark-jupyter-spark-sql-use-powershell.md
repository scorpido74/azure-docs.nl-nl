---
title: 'Snelstartgids: Apache Spark cluster maken in azure HDInsight met Azure PowerShell'
description: Deze snelstartgids laat zien hoe u met Azure PowerShell een Apache Spark-cluster maakt in HDInsight en hoe u een eenvoudige Spark SQL-query uitvoert.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: 14340138a221dd00aa10651da9e209468b3c8550
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891709"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Snelstartgids: Apache Spark cluster maken in azure HDInsight met behulp van Power shell

In deze Quick Start gebruikt u Azure PowerShell voor het maken van een Apache Spark cluster in azure HDInsight. Vervolgens maakt u een Jupyter-notebook en gebruikt u dit om Spark SQL-query's uit te voeren op Apache Hive tabellen. Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Met het Apache Spark-Framework voor Azure HDInsight kunt u snel gegevens analyses en cluster computing gebruiken met in-Memory verwerking. Met Jupyter notebook kunt u communiceren met uw gegevens, code combi neren met tekst van de prijs opgave en eenvoudige visualisaties.

[Overzicht: Apache Spark in azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter notebook](https://jupyter.org/)

Als u meerdere clusters tegelijk gebruikt, wilt u een virtueel netwerk maken. Als u een Spark-cluster gebruikt, wilt u ook de Hive-Warehouse connector gebruiken. Zie voor meer informatie [een virtueel netwerk voor Azure HDInsight plannen](../hdinsight-plan-virtual-network-deployment.md) en [Apache Spark en Apache Hive integreren met de Hive-Warehouse connector](../interactive-query/apache-hive-warehouse-connector.md).

## <a name="prerequisite"></a>Vereiste

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- De [Power shell AZ-module](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Een Apache Spark-cluster maken in Azure HDInsight

> [!IMPORTANT]  
> HDInsight-clusters worden pro rato per minuut gefactureerd, ongeacht of u er wel of niet gebruik van maakt. Verwijder uw cluster daarom als u er klaar mee bent. Zie voor meer informatie de sectie [Resources opschonen](#clean-up-resources) van dit artikel.

Het maken van een HDInsight-cluster omvat het maken van de volgende Azure objecten en -resources:

- Een Azure-resourcegroep. Een Azure-resourcegroep is een container voor Azure-resources.
- Een Azure-opslagaccount of Azure Data Lake Storage.  Elk HDInsight-cluster vereist een eigen gegevensopslag. In deze Quick Start maakt u een cluster dat Azure Storage blobs gebruikt als cluster opslag. Zie [Snelstart: Clusters instellen in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) voor meer informatie over het gebruik van Data Lake Storage Gen2.
- Een cluster met verschillende cluster typen op HDInsight.  In deze snelstartgids maakt u een cluster van Spark 2.3.

U gebruikt een PowerShell-script om de resources te maken. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wanneer u het Power shell-script uitvoert, wordt u gevraagd de volgende waarden in te voeren:

|Parameter|Waarde|
|------|------|
|Naam van Azure-resourcegroep | Geef een unieke naam op voor de resourcegroep.|
|Locatie| Geef de Azure-regio op, bijvoorbeeld 'VS - centraal'. |
|Standaardnaam van opslagaccount | Geef een unieke naam op voor het opslagaccount. |
|Clusternaam | Geef een unieke naam op voor het HDInsight-cluster.|
|Referenties voor clusteraanmelding | U gebruikt dit account om later in de snelstartgids verbinding te maken met het clusterdashboard.|
|Referenties van SSH-gebruiker | De SSH-clients kunnen worden gebruikt voor het maken van een externe opdracht regel sessie met de clusters in HDInsight.|

1. Selecteer **deze** in de rechter bovenhoek van het volgende code blok proberen om [Azure Cloud shell](../../cloud-shell/overview.md)te openen en volg de instructies om verbinding te maken met Azure.

2. Kopieer en plak het volgende Power shell-script in de Cloud Shell.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
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

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   Het duurt ongeveer 20 minuten om het cluster te maken. Het cluster moet zijn gemaakt voordat u verder kunt gaan met de volgende sessie.

Als u een probleem ondervindt met het maken van HDInsight-clusters, is het mogelijk dat u niet over de juiste machtigingen beschikt. Zie [Vereisten voor toegangsbeheer](../hdinsight-hadoop-customize-cluster-linux.md#access-control) voor meer informatie.

## <a name="create-a-jupyter-notebook"></a>Een Jupyter-notebook maken

[Jupyter Notebook](https://jupyter.org/) is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Via het notitieblok kunt u interactie hebben met uw gegevens, code combineren met markdown-tekst en eenvoudige visualisaties uitvoeren.

1. In de [Azure Portal](https://portal.azure.com)zoekt en selecteert u **HDInsight-clusters**.
   
   ![HDInsight-cluster in de Azure Portal openen](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png)
   
1. Selecteer in de lijst het cluster dat u hebt gemaakt.
   
   ![HDInsight-cluster in de Azure Portal openen](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png)
   
1. Selecteer **cluster dashboards**op de pagina cluster **overzicht** en selecteer vervolgens **Jupyter notebook**. Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

   ![Open Jupyter Notebook om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook om de interactieve Spark SQL-query uit te voeren")

1. Selecteer **nieuwe** > **PySpark** om een notitie blok te maken.

   ![Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren")

   Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-instructies uitvoeren

SQL (Structured Query Language) is de meest voorkomende en gebruikte taal voor het uitvoeren van query's en het definiëren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Controleer of de kernel gereed is. Wanneer u een lege cirkel naast de naam van de kernel in de notebook ziet, is de kernel gereed. Gevulde cirkel geeft aan dat de kernel bezet is.

    ![kernel-status](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "kernel-status")

    Wanneer u de notebook voor het eerst start, voert de kernel enkele taken in de achtergrond uit. Wacht tot de kernel gereed is. 
1. Plak de volgende code in een lege cel en druk op **Shift+Enter** om de code uit te voeren. Met de opdracht worden de Hive-tabellen in het cluster weergegeven:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Wanneer u een Jupyter Notebook met uw Spark-cluster in HDInsight gebruikt, krijgt u een `sqlContext` vooraf ingestelde voor instelling die u kunt gebruiken om Hive-query's uit te voeren met Spark SQL. `%%sql` instrueert Jupyter Notebook gebruik te maken van de vooraf ingestelde `sqlContext` om de Hive-query uit te voeren. De query haalt de bovenste tien rijen op uit een Hive-tabel (**hivesampletable**) die standaard worden meegeleverd met alle HDInsight-clusters. Het duurt ongeveer 30 seconden om de resultaten op te halen. De uitvoer ziet er als volgt uit:

    ![Query Apache Hive in Spark in HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png "Hive-query in HDInsight Spark")

    Telkens wanneer u in Jupyter een query uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** en de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek.

1. Voer een andere query uit om de gegevens in `hivesampletable` te zien.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Het scherm wordt vernieuwd om de query-uitvoer weer te geven.

    ![Uitvoer van Hive-query in HDInsight](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png "Uitvoer van Hive-query in HDInsight")

1. Klik in het menu **File** van het notebook op **Close and Halt**. Als de notebook wordt afgesloten, komen de clusterbronnen vrij.

## <a name="clean-up-resources"></a>Resources opschonen

Met HDInsight worden uw gegevens opgeslagen in Azure Storage of Azure Data Lake Storage, zodat u een cluster veilig kunt verwijderen wanneer dit niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt. Als u direct verder wilt met de zelfstudie die wordt vermeld bij [Volgende stappen](#next-steps), is het beter om het cluster te behouden.

Ga terug naar Azure Portal en selecteer **Verwijderen**.

![Een HDInsight-cluster Azure Portal verwijderen](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de resource groep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaard opslag account.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>Stuksgewijs opschonen met Power shell AZ-module

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Apache Spark cluster maakt in HDInsight en een eenvoudige Spark SQL-query uitvoert. Ga naar de volgende zelf studie voor meer informatie over het gebruik van een HDInsight-cluster voor het uitvoeren van interactieve query's op voorbeeld gegevens.

> [!div class="nextstepaction"]
> [Interactieve query's uitvoeren in Apache Spark](./apache-spark-load-data-run-query.md)
