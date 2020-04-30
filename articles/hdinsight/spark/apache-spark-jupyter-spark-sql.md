---
title: 'Snelstartgids: Apache Spark cluster maken met sjabloon-Azure HDInsight'
description: In deze Quick start ziet u hoe u Resource Manager-sjabloon gebruikt om een Apache Spark cluster te maken in azure HDInsight en een Spark SQL-query uit te voeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: a92e1410b688dc3117cf28fee2ba9231641b65bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81616855"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snelstartgids: Apache Spark cluster in azure HDInsight maken met Resource Manager-sjabloon

In deze Quick Start gebruikt u een Azure Resource Manager sjabloon voor het maken van een [Apache Spark](./apache-spark-overview.md) cluster in azure HDInsight. Vervolgens maakt u een Jupyter-notebook en gebruikt u dit om Spark SQL-query's uit te voeren op Apache Hive tabellen. Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Met het Apache Spark-Framework voor HDInsight kunt u snel gegevens analyses en cluster computing gebruiken met in-Memory verwerking. Met Jupyter notebook kunt u communiceren met uw gegevens, code combi neren met tekst van de prijs opgave en eenvoudige visualisaties.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-apache-spark-cluster"></a>Een Apache Spark-cluster maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-spark-linux)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json" range="1-143":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [Micro soft. Storage/Storage accounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): een Azure Storage-account maken.
* [Micro soft. HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): een HDInsight-cluster maken.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de onderstaande knop **implementeren naar Azure** om u aan te melden bij Azure en de Resource Manager-sjabloon te openen.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer in de vervolg keuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster.|
    |Resourcegroep|Selecteer in de vervolg keuzelijst de bestaande resource groep of selecteer **nieuwe maken**.|
    |Locatie|De waarde wordt automatisch ingevuld met de locatie die wordt gebruikt voor de resource groep.|
    |Clusternaam|Voer een wereldwijd unieke naam in. Voor deze sjabloon gebruikt u alleen kleine letters en cijfers.|
    |Gebruikersnaam voor clusteraanmelding|Geef de gebruikers naam op. de standaard instelling is **admin**.|
    |Wachtwoord voor clusteraanmelding|Geef een wacht woord op. Het wacht woord moet uit minstens tien tekens bestaan en moet ten minste één cijfer, één hoofd letter en één kleine letters, één niet-alfanumeriek teken (behalve de tekens ' "') bevatten. |
    |SSH-gebruikers naam|Geef de gebruikers naam op. de standaard waarde is **sshuser**|
    |SSH-wacht woord|Geef het wacht woord op.|

    ![Een Spark-cluster maken in HDInsight met behulp van Azure Resource Manager-sjabloon](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Een Spark-cluster maken in HDInsight met behulp van een Azure Resource Manager sjabloon")

1. Bekijk de **voor waarden**. Selecteer **Ik ga akkoord met de bovenstaande voor waarden en**klik vervolgens op **kopen**. U ontvangt een melding dat uw implementatie wordt uitgevoerd. Het duurt ongeveer 20 minuten om een cluster te maken.

Als u een probleem ondervindt met het maken van HDInsight-clusters, is het mogelijk dat u niet over de juiste machtigingen beschikt. Zie [Vereisten voor toegangsbeheer](../hdinsight-hadoop-customize-cluster-linux.md#access-control) voor meer informatie.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Zodra het cluster is gemaakt, ontvangt u een melding over **implementatie geslaagd** met een koppeling **naar de resource** . Op de pagina Resourcegroep worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster heeft een [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) account of een afhankelijkheid van [Azure data Lake Storage-account](../hdinsight-hadoop-use-data-lake-store.md) . Dit wordt aangeduid als het standaard opslag account. Het HDInsight-cluster en het standaard opslag account moeten zich in dezelfde Azure-regio bevinden. Als u clusters verwijdert, wordt het opslag account niet verwijderd.

## <a name="create-a-jupyter-notebook"></a>Een Jupyter-notebook maken

[Jupyter Notebook](https://jupyter.org/) is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Met het notitie blok kunt u communiceren met uw gegevens, code combi neren met tekst verlaging en eenvoudige visualisaties uitvoeren.

1. Open de [Azure Portal](https://portal.azure.com).

2. Selecteer **HDInsight-clusters** en selecteer vervolgens het cluster dat u hebt gemaakt.

    ![HDInsight-cluster in de Azure Portal openen](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Selecteer in de portal in het gedeelte **cluster dashboards** de optie **Jupyter notebook**. Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

   ![Open Jupyter Notebook om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook om de interactieve Spark SQL-query uit te voeren")

4. Selecteer **nieuwe** > **PySpark** om een notitie blok te maken.

   ![Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren")

   Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-instructies uitvoeren

SQL (Structured Query Language) is de meest voorkomende en gebruikte taal voor het uitvoeren van query's en het transformeren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Controleer of de kernel gereed is. Wanneer u een lege cirkel naast de naam van de kernel in de notebook ziet, is de kernel gereed. Gevulde cirkel geeft aan dat de kernel bezet is.

    ![Kernel-status](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Kernel-status")

    Wanneer u de notebook voor het eerst start, voert de kernel enkele taken in de achtergrond uit. Wacht tot de kernel gereed is.

1. Plak de volgende code in een lege cel en druk op **Shift+Enter** om de code uit te voeren. Met de opdracht worden de Hive-tabellen in het cluster weergegeven:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Wanneer u een Jupyter Notebook gebruikt in uw HDInsight-cluster, krijgt u een `spark` vooraf ingestelde sessie die u kunt gebruiken om Hive-query's uit te voeren met Spark SQL. `%%sql` instrueert Jupyter Notebook om de vooraf ingestelde `spark`-sessie te gebruiken voor het uitvoeren van de Hive-query. De query haalt de bovenste tien rijen op uit een Hive-tabel (**hivesampletable**) die standaard worden meegeleverd met alle HDInsight-clusters. De eerste keer dat u de query verzendt, wordt met Jupyter een Spark-toepassing gemaakt voor het notitie blok. Dit duurt ongeveer 30 seconden. Zodra de Spark-toepassing gereed is, wordt de query in ongeveer een seconde uitgevoerd en worden de resultaten gegenereerd. De uitvoer ziet er als volgt uit:

    ![Apache Hive query in HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-query in HDInsight")

    Telkens wanneer u in Jupyter een query uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** en de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek.

1. Voer een andere query uit om de gegevens in `hivesampletable` te zien.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Het scherm wordt vernieuwd om de query-uitvoer weer te geven.

    ![Uitvoer van Hive-query in HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Uitvoer van Hive-query in HDInsight")

1. Klik in het menu **File** van het notebook op **Close and Halt**. Als u de laptop afsluit, worden de cluster bronnen vrijgegeven, inclusief Spark-toepassingen.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

Navigeer vanuit het Azure Portal naar uw cluster en selecteer **verwijderen**.

![Een HDInsight-cluster Azure Portal verwijderen](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de resource groep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaard opslag account.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Apache Spark cluster maakt in HDInsight en een eenvoudige Spark SQL-query uitvoert. Ga naar de volgende zelf studie voor meer informatie over het gebruik van een HDInsight-cluster voor het uitvoeren van interactieve query's op voorbeeld gegevens.

> [!div class="nextstepaction"]
> [Interactieve query's uitvoeren in Apache Spark](./apache-spark-load-data-run-query.md)
