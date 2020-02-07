---
title: 'Quick Start: een Spark-cluster maken in HDInsight met behulp van Azure Portal'
description: In deze Quick start ziet u hoe u Azure Portal kunt gebruiken om een Apache Spark-cluster in azure HDInsight te maken en een Spark SQL-query uit te voeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 09/27/2019
ms.custom: mvc
ms.openlocfilehash: 070f1f158ef6fb1d4b8f6a41f5b36d7cb16b24da
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048475"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Snelstartgids: Apache Spark cluster maken in azure HDInsight met behulp van Azure Portal

In deze Quick Start gebruikt u de Azure Portal voor het maken van een Apache Spark cluster in azure HDInsight. Vervolgens maakt u een Jupyter-notebook en gebruikt u dit om Spark SQL-query's uit te voeren op Apache Hive tabellen. Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Met het Apache Spark-Framework voor HDInsight kunt u snel gegevens analyses en cluster computing gebruiken met in-Memory verwerking. Met Jupyter notebook kunt u communiceren met uw gegevens, code combi neren met tekst van de prijs opgave en eenvoudige visualisaties.

[Overzicht: Apache Spark in azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [](https://jupyter.org/)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Een Apache Spark-cluster maken in HDInsight

U gebruikt de Azure Portal om een HDInsight-cluster te maken dat gebruikmaakt van Azure Storage-blobs als cluster opslag. Zie [Snelstart: Clusters instellen in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) voor meer informatie over het gebruik van Data Lake Storage Gen2.

> [!IMPORTANT]  
> HDInsight-clusters worden pro rato per minuut gefactureerd, ongeacht of u er wel of niet gebruik van maakt. Verwijder uw cluster daarom als u er klaar mee bent. Zie voor meer informatie de sectie [Resources opschonen](#clean-up-resources) van dit artikel.

1. Selecteer in Azure Portal **Een resource maken**.

    ![Azure Portal een resource maken](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create.png "Een resource maken in Azure Portal")

1. Selecteer op de pagina **Nieuw** **Analytics** > **HDInsight**.

    ![HDInsight Azure Portal maken](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight op Azure Portal")

1. Geef onder **Basis** de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement  | Kies in de vervolgkeuzelijst een Azure-abonnement voor dit cluster. Het abonnement dat voor deze Quick Start wordt gebruikt, is **Azure**. |
    |Resourcegroep | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De naam van de resource groep die voor deze Quick Start wordt gebruikt, is **myResourceGroup**. |
    |Clusternaam | Geef een naam op voor uw HDInsight-cluster. De cluster naam die voor deze Quick Start wordt gebruikt, is **myspark2019**.|
    |Locatie   | Selecteer een locatie voor de resourcegroep. De sjabloon gebruikt deze locatie voor het maken van het cluster en als standaardclusteropslag. De locatie die wordt gebruikt voor deze Quick start is **VS-Oost**. |
    |Cluster type| Selecteer **Spark** als het cluster type.|
    |Cluster versie|Dit veld wordt automatisch ingevuld met de standaard versie zodra het cluster type is geselecteerd.|
    |Gebruikersnaam voor clusteraanmeldgegevens| Voer de gebruikersnaam voor aanmelding bij het cluster in.  De standaard naam is *admin*. U kunt dit account gebruiken om u later in de Quick Start aan te melden bij de Jupyter-notebook. |
    |Wachtwoord voor clusteraanmeldgegevens| Voer het wachtwoord voor aanmelding bij het cluster in. |
    |SSH-gebruikersnaam (Secure Shell)| Voer de SSH-gebruikersnaam in. De SSH-gebruikersnaam voor deze snelstartgids is **sshuser**. De standaardinstelling is dat voor dit account hetzelfde wachtwoord wordt gebruikt als voor *Gebruikersnaam voor clusteraanmeldgegevens*. |

    ![Basis configuraties van HDInsight-clusters maken](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Een Spark-cluster maken in HDInsight de basis configuraties")

    Selecteer **volgende: opslag > >** om door te gaan naar de **opslag** pagina.

1. Geef onder **Opslag** de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Primair opslag type|Gebruik de standaard waarde **Azure Storage**.|
    |Selectie methode|Gebruik de standaard waarde **uit de lijst**.|
    |Primair opslagaccount|De automatisch ingevulde waarde gebruiken.|
    |Container|De automatisch ingevulde waarde gebruiken.|

    ![Basis configuraties van HDInsight-clusters maken](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Een Spark-cluster maken in HDInsight de basis configuraties")

    Selecteer **controleren + maken** om door te gaan.

1. Selecteer onder **controleren en maken**de optie **maken**. Het duurt ongeveer 20 minuten om het cluster te maken. Het cluster moet zijn gemaakt voordat u verder kunt gaan met de volgende sessie.

Als u een probleem ondervindt met het maken van HDInsight-clusters, is het mogelijk dat u niet over de juiste machtigingen beschikt. Zie [Vereisten voor toegangsbeheer](../hdinsight-hadoop-create-linux-clusters-portal.md) voor meer informatie.

## <a name="create-a-jupyter-notebook"></a>Een Jupyter-notebook maken

Jupyter Notebook is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Via het notitieblok kunt u interactie hebben met uw gegevens, code combineren met markdown-tekst en eenvoudige visualisaties uitvoeren.

1. Open de [Azure-portal](https://portal.azure.com).

1. Selecteer **HDInsight-clusters** en selecteer vervolgens het cluster dat u hebt gemaakt.

    ![HDInsight-cluster openen in Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

1. Selecteer **Clusterdashboard** in het portal en selecteer vervolgens **Jupyter Notebook**. Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

   ![Open Jupyter Notebook om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Open Jupyter Notebook om de interactieve Spark SQL-query uit te voeren")

1. Selecteer **Nieuw** > **PySpark** om een notebook te maken.

   ![Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Een Jupyter Notebook maken om de interactieve Spark SQL-query uit te voeren")

   Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-instructies uitvoeren

SQL (Structured Query Language) is de meest voorkomende en gebruikte taal voor het uitvoeren van query's en het definiëren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Controleer of de kernel gereed is. Wanneer u een lege cirkel naast de naam van de kernel in de notebook ziet, is de kernel gereed. Gevulde cirkel geeft aan dat de kernel bezet is.

    ![Apache Hive query in HDInsight](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive-query in HDInsight")

    Wanneer u de notebook voor het eerst start, voert de kernel enkele taken in de achtergrond uit. Wacht tot de kernel gereed is.

1. Plak de volgende code in een lege cel en druk op **Shift+Enter** om de code uit te voeren. Met de opdracht worden de Hive-tabellen in het cluster weergegeven:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Wanneer u een Jupyter Notebook gebruikt in uw HDInsight-cluster, krijgt u een vooraf ingestelde `sqlContext` die u kunt gebruiken om Hive-query's uit te voeren met Spark SQL. `%%sql` instrueert Jupyter Notebook gebruik te maken van de vooraf ingestelde `sqlContext` om de Hive-query uit te voeren. De query haalt de bovenste tien rijen op uit een Hive-tabel (**hivesampletable**) die standaard worden meegeleverd met alle HDInsight-clusters. Het duurt ongeveer 30 seconden om de resultaten op te halen. De uitvoer ziet er als volgt uit:

    ![Apache Hive query in HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-query in HDInsight")

    Telkens wanneer u in Jupyter een query uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** en de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek.

1. Voer een andere query uit om de gegevens in `hivesampletable` te zien.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Het scherm wordt vernieuwd om de query-uitvoer weer te geven.

    ![Uitvoer van Hive-query in HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Uitvoer van Hive-query in HDInsight")

1. Klik in het menu **File** van het notebook op **Close and Halt**. Als de notebook wordt afgesloten, komen de clusterbronnen vrij.

## <a name="clean-up-resources"></a>Resources opschonen

Met HDInsight worden uw gegevens opgeslagen in Azure Storage of Azure Data Lake Storage, zodat u een cluster veilig kunt verwijderen wanneer dit niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt. Als u direct verder wilt met de zelfstudie die wordt vermeld bij [Volgende stappen](#next-steps), is het beter om het cluster te behouden.

Ga terug naar Azure Portal en selecteer **Verwijderen**.

![Een HDInsight-cluster Azure Portal verwijderen](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de resource groep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaard opslag account.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Apache Spark cluster maakt in HDInsight en een eenvoudige Spark SQL-query uitvoert. Ga naar de volgende zelf studie voor meer informatie over het gebruik van een HDInsight-cluster voor het uitvoeren van interactieve query's op voorbeeld gegevens.

> [!div class="nextstepaction"]
> [Interactieve query's uitvoeren in Apache Spark](./apache-spark-load-data-run-query.md)
