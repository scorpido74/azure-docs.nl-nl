---
title: 'Snelstartgids: Apache Spark cluster maken met sjabloon-Azure HDInsight'
description: In deze Quick start ziet u hoe u Resource Manager-sjabloon gebruikt om een Apache Spark cluster te maken in azure HDInsight en een Spark SQL-query uit te voeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/05/2020
ms.openlocfilehash: a4c207cdbe4bbd0fdef5e1da8da0f4b582702308
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78932792"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snelstartgids: Apache Spark cluster in azure HDInsight maken met Resource Manager-sjabloon

In deze Quick Start gebruikt u een Azure Resource Manager sjabloon voor het maken van een Apache Spark cluster in azure HDInsight. Vervolgens maakt u een Jupyter-notebook en gebruikt u dit om Spark SQL-query's uit te voeren op Apache Hive tabellen. Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Met het Apache Spark-Framework voor HDInsight kunt u snel gegevens analyses en cluster computing gebruiken met in-Memory verwerking. Met Jupyter notebook kunt u communiceren met uw gegevens, code combi neren met tekst van de prijs opgave en eenvoudige visualisaties.

[Overzicht: Apache Spark in azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter notebook](https://jupyter.org/) | [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular) start-sjablonen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-apache-spark-cluster"></a>Een Apache Spark-cluster maken

Maak een Apache Spark-cluster in HDInsight met behulp van een Azure Resource Manager sjabloon. U vindt de sjabloon in [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Raadpleeg [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters) voor de JSON-syntaxis en de eigenschappen van het cluster.

Het cluster maakt gebruik van Azure Storage Blobs als de clusteropslag. Zie [Snelstart: Clusters instellen in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) voor meer informatie over het gebruik van Data Lake Storage Gen2.

> [!IMPORTANT]  
> HDInsight-clusters worden pro rato per minuut gefactureerd, ongeacht of u er wel of niet gebruik van maakt. Verwijder uw cluster daarom als u er klaar mee bent. Zie voor meer informatie de sectie [Resources opschonen](#clean-up-resources) van dit artikel.

1. Selecteer de link koppeling om de sjabloon te openen in een nieuw browsertabblad in Azure Portal:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">Implementeren naar Azure</a>

2. Voer de volgende waarden in:

    | Eigenschap | Waarde |
    |---|---|
    |Abonnement|Selecteer het Azure-abonnement dat is gebruikt om dit cluster te maken. |
    | Resourcegroep|Maak een resourcegroep of selecteer een bestaande resourcegroep. Deze resourcegroep wordt gebruikt om Azure-resources voor uw projecten te beheren. De naam van de nieuwe resourcegroep die we gebruiken voor deze snelstart is **myspark20180403rg**.|
    | Locatie|Selecteer een locatie voor de resourcegroep. De sjabloon gebruikt deze locatie voor het maken van het cluster en de standaard cluster opslag. De locatie voor deze snelstart is **VS - oost 2**.|
    | ClusterName|Voer een naam in voor het cluster dat u wilt maken. De naam voor het nieuwe cluster dat we gebruiken voor deze snelstart is **myspark20180403**.|
    | Aanmeldings naam en wacht woord voor het cluster|De standaard aanmeldings naam is admin. Kies een wacht woord voor de cluster aanmelding. De aanmeldingsnaam voor deze snelstart is **admin**.|
    | Gebruikers naam en wacht woord voor SSH|Kies een wachtwoord voor de SSH-gebruiker. De SSH-gebruikersnaam voor deze snelstart is **sshuser**.|

    ![Een Spark-cluster maken in HDInsight met behulp van Azure Resource Manager-sjabloon](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Een Spark-cluster maken in HDInsight met behulp van een Azure Resource Manager sjabloon")

3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**. U ziet een nieuwe tegel met de titel **Implementatie van sjabloonimplementatie**. Het duurt ongeveer 20 minuten om het cluster te maken. Het cluster moet zijn gemaakt voordat u verder kunt gaan met de volgende sessie.

Als u een probleem ondervindt met het maken van HDInsight-clusters, is het mogelijk dat u niet over de juiste machtigingen beschikt. Zie [Vereisten voor toegangsbeheer](../hdinsight-hadoop-customize-cluster-linux.md#access-control) voor meer informatie.

## <a name="install-intellijeclipse-for-spark-applications"></a>IntelliJ/eclips installeren voor Spark-toepassingen

Gebruik de invoeg toepassing Azure-toolkit voor IntelliJ/eclips om Spark-toepassingen te ontwikkelen die zijn geschreven in [scala](https://www.scala-lang.org/)en verzend deze vervolgens rechtstreeks vanuit de IntelliJ/eclips Integrated Development Environment (IDE) naar een Azure HDInsight-cluster. Zie [IntelliJ gebruiken om Spark-toepassing te schrijven/verzenden](./apache-spark-intellij-tool-plugin.md) en [Eclipse gebruiken om Spark-toepassing te schrijven/verzenden](./apache-spark-eclipse-tool-plugin.md).

## <a name="install-vscode-for-pysparkhive-applications"></a>VSCode voor PySpark/Hive-toepassingen installeren

Leer hoe u Azure HDInsight-hulpprogramma's voor Visual Studio Code (VSCode) kunt gebruiken om Hive-batchtaken, interactieve Hive-query's, PySpark-batch- en interactieve PySpark-scripts te maken en verzenden. De Azure HDInsight-hulpprogramma's kunnen worden geïnstalleerd op de platforms die worden ondersteund door VSCode. Zoals onder andere Windows, Linux en macOS. Zie [VSCode gebruiken om PySpark-toepassing te schrijven/verzenden](../hdinsight-for-vscode.md) voor meer informatie.

## <a name="create-a-jupyter-notebook"></a>Een Jupyter-notebook maken

[Jupyter Notebook](https://jupyter.org/) is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Met het notitie blok kunt u communiceren met uw gegevens, code combi neren met tekst verlaging en eenvoudige visualisaties uitvoeren.

1. Open de [Azure-portal](https://portal.azure.com).

2. Selecteer **HDInsight-clusters** en selecteer vervolgens het cluster dat u hebt gemaakt.

    ![HDInsight-cluster in de Azure Portal openen](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. Selecteer in de portal in het gedeelte **cluster dashboards** de optie **Jupyter notebook**. Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

   ![Open Jupyter Notebook om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook om de interactieve Spark SQL-query uit te voeren")

4. Selecteer **Nieuw** > **PySpark** om een notebook te maken.

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

    Wanneer u een Jupyter Notebook gebruikt in uw HDInsight-cluster, krijgt u een vooraf ingestelde `spark` sessie die u kunt gebruiken om Hive-query's uit te voeren met Spark SQL. `%%sql` instrueert Jupyter Notebook om de vooraf ingestelde `spark`-sessie te gebruiken voor het uitvoeren van de Hive-query. De query haalt de bovenste tien rijen op uit een Hive-tabel (**hivesampletable**) die standaard worden meegeleverd met alle HDInsight-clusters. De eerste keer dat u de query verzendt, wordt met Jupyter een Spark-toepassing gemaakt voor het notitie blok. Dit duurt ongeveer 30 seconden. Zodra de Spark-toepassing gereed is, wordt de query in ongeveer een seconde uitgevoerd en worden de resultaten gegenereerd. De uitvoer ziet er als volgt uit:

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

Met HDInsight worden uw gegevens en Jupyter-notebooks opgeslagen in Azure Storage of Azure Data Lake Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt. Als u direct verder wilt met de zelfstudie die wordt vermeld bij [Volgende stappen](#next-steps), is het beter om het cluster te behouden.

Ga terug naar Azure Portal en selecteer **Verwijderen**.

![Een HDInsight-cluster Azure Portal verwijderen](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de resource groep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaard opslag account.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Apache Spark cluster maakt in HDInsight en een eenvoudige Spark SQL-query uitvoert. Ga naar de volgende zelf studie voor meer informatie over het gebruik van een HDInsight-cluster voor het uitvoeren van interactieve query's op voorbeeld gegevens.

> [!div class="nextstepaction"]
> [Interactieve query's uitvoeren in Apache Spark](./apache-spark-load-data-run-query.md)
