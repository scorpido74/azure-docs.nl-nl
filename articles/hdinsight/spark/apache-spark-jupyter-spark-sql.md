---
title: 'Snelstart: Apache Spark-cluster maken met sjabloon - Azure HDInsight'
description: In deze snelstart ziet u hoe u resourcebeheersjabloon gebruikt om een Apache Spark-cluster in Azure HDInsight te maken en een Spark SQL-query uit te voeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: a92e1410b688dc3117cf28fee2ba9231641b65bb
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616855"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snelstart: Apache Spark-cluster maken in Azure HDInsight met behulp van resourcebeheersjabloon

In deze snelstart gebruikt u een Azure Resource Manager-sjabloon om een [Apache Spark-cluster](./apache-spark-overview.md) in Azure HDInsight te maken. Vervolgens maakt u een Jupyter-notitieblok en gebruikt u het om Spark SQL-query's uit te voeren tegen Apache Hive-tabellen. Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Het Apache Spark framework voor HDInsight maakt snelle data-analyse en clustercomputing mogelijk met behulp van in-memory processing. Met Jupyter-notitieblok u communiceren met uw gegevens, code combineren met afwaarderingstekst en eenvoudige visualisaties maken.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-apache-spark-cluster"></a>Een Apache Spark-cluster maken

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-spark-linux)

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json" range="1-143":::

In de sjabloon worden twee Azure-resources gedefinieerd:

* [Microsoft.Storage/storageAccounts:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)maak een Azure Storage Account.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): maak een HDInsight-cluster.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de knop **Implementeren naar Azure** hieronder om u aan te melden bij Azure en open de sjabloon Resourcebeheer.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer in de vervolgkeuzelijst het Azure-abonnement dat voor het cluster wordt gebruikt.|
    |Resourcegroep|Selecteer in de vervolgkeuzelijst uw bestaande resourcegroep of selecteer **Nieuw maken**.|
    |Locatie|De waarde wordt automatisch gevuld met de locatie die wordt gebruikt voor de resourcegroep.|
    |Clusternaam|Voer een wereldwijd unieke naam in. Gebruik voor deze sjabloon alleen kleine letters en cijfers.|
    |Gebruikersnaam voor clusteraanmelding|Geef de gebruikersnaam op, standaard is **beheerder**.|
    |Wachtwoord voor clusteraanmelding|Geef een wachtwoord op. Het wachtwoord moet ten minste tien tekens lang zijn en moet ten minste één cijfer, één hoofdletter en één kleine letter bevatten, één niet-alfanumeriek teken (met uitzondering van tekens " ). |
    |Ssh-gebruikersnaam|Geef de gebruikersnaam op, standaard is **sshuser**|
    |Ssh-wachtwoord|Geef het wachtwoord op.|

    ![Spark-cluster maken in HDInsight met azure resource manager-sjabloon](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Spark-cluster maken in HDInsight met behulp van een Azure Resource Manager-sjabloon")

1. Bekijk de **algemene voorwaarden**. Selecteer dan **ik ga akkoord met de hierboven vermelde algemene voorwaarden,** dan **Koop**. U ontvangt een melding dat uw implementatie aan de gang is. Het duurt ongeveer 20 minuten om een cluster te maken.

Als u een probleem ondervindt bij het maken van HDInsight-clusters, kan het zijn dat u niet over de juiste machtigingen beschikt om dit te doen. Zie [Vereisten voor toegangsbeheer](../hdinsight-hadoop-customize-cluster-linux.md#access-control) voor meer informatie.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Zodra het cluster is gemaakt, ontvangt u een **door implementatie geslaagde** melding met een **koppeling naar de bron ga.** Op de pagina Resourcegroep worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster heeft een [Azure Storage-account](../hdinsight-hadoop-use-blob-storage.md) of een Azure [Data Lake Storage-accountafhankelijkheid.](../hdinsight-hadoop-use-data-lake-store.md) Het wordt het standaardopslagaccount genoemd. Het HDInsight-cluster en het standaardopslagaccount moeten in dezelfde Azure-regio worden ondergebracht. Als u clusters verwijdert, wordt het opslagaccount niet verwijderd.

## <a name="create-a-jupyter-notebook"></a>Een Jupyter-notebook maken

[Jupyter Notebook](https://jupyter.org/) is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Met het notitieblok u communiceren met uw gegevens, code combineren met afwaarderingstekst en eenvoudige visualisaties uitvoeren.

1. Open de [Azure Portal](https://portal.azure.com).

2. Selecteer **HDInsight-clusters** en selecteer vervolgens het cluster dat u hebt gemaakt.

    ![HdInsight-cluster openen in de Azure-portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Selecteer **Jupyter Notebook**in de portal in de sectie **Clusterdashboards** . Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

   ![Jupyter-notitieblok openen om interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter-notitieblok openen om interactieve Spark SQL-query uit te voeren")

4. Selecteer **Nieuwe** > **PySpark** om een notitieblok te maken.

   ![Een Jupyter-notitieblok maken om interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Een Jupyter-notitieblok maken om interactieve Spark SQL-query uit te voeren")

   Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-instructies uitvoeren

SQL (Structured Query Language) is de meest voorkomende en gebruikte taal voor het uitvoeren van query's en het transformeren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Controleer of de kernel gereed is. Wanneer u een lege cirkel naast de naam van de kernel in de notebook ziet, is de kernel gereed. Gevulde cirkel geeft aan dat de kernel bezet is.

    ![Kernelstatus](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Kernelstatus")

    Wanneer u de notebook voor het eerst start, voert de kernel enkele taken in de achtergrond uit. Wacht tot de kernel gereed is.

1. Plak de volgende code in een lege cel en druk op **Shift+Enter** om de code uit te voeren. Met de opdracht worden de Hive-tabellen in het cluster weergegeven:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Wanneer u een Jupyter-notitieblok gebruikt met uw `spark` HDInsight-cluster, krijgt u een vooraf ingestelde sessie die u gebruiken om Hive-query's uit te voeren met Spark SQL. `%%sql` instrueert Jupyter Notebook om de vooraf ingestelde `spark`-sessie te gebruiken voor het uitvoeren van de Hive-query. De query haalt de bovenste tien rijen op uit een Hive-tabel (**hivesampletable**) die standaard worden meegeleverd met alle HDInsight-clusters. De eerste keer dat u de query indient, maakt Jupyter een Spark-toepassing voor het notitieblok. Dit duurt ongeveer 30 seconden. Zodra de Spark-toepassing klaar is, wordt de query in ongeveer een seconde uitgevoerd en produceert de resultaten. De uitvoer ziet er als volgt uit:

    ![Apache Hive query in HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive query in HDInsight")

    Telkens wanneer u in Jupyter een query uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** en de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek.

1. Voer een andere query uit om de gegevens in `hivesampletable` te zien.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Het scherm wordt vernieuwd om de query-uitvoer weer te geven.

    ![Hive query output in HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive query output in HDInsight")

1. Klik in het menu **File** van het notebook op **Close and Halt**. Als u het notitieblok afsluit, worden de clusterbronnen, inclusief spark-toepassing, vrijgegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Navigeer in de Azure-portal naar uw cluster en selecteer **Verwijderen**.

![Azure-portal verwijdert een HDInsight-cluster](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de brongroep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Apache Spark-cluster in HDInsight maken en een basisSpark SQL-query uitvoeren. Ga naar de volgende zelfstudie om te leren hoe u een HDInsight-cluster gebruiken om interactieve query's op voorbeeldgegevens uit te voeren.

> [!div class="nextstepaction"]
> [Interactieve query's uitvoeren in Apache Spark](./apache-spark-load-data-run-query.md)
