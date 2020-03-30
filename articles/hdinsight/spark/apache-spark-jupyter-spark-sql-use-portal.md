---
title: 'Snelstart: Spark-cluster maken in HDInsight met Azure-portal'
description: In deze snelstart wordt uitgelegd hoe u de Azure-portal gebruiken om een Apache Spark-cluster in Azure HDInsight te maken en een Spark SQL-query uit te voeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 5f4d1b8ef742a8dcafa2b8e34a6209f85ae050d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77650610"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Snelstart: Apache Spark-cluster maken in Azure HDInsight met Azure-portal

In deze quickstart gebruikt u de Azure-portal om een Apache Spark-cluster in Azure HDInsight te maken. Vervolgens maakt u een Jupyter-notitieblok en gebruikt u het om Spark SQL-query's uit te voeren tegen Apache Hive-tabellen. Azure HDInsight is een beheerde, zeer uitgebreide open-source analyseservice voor bedrijven. Het Apache Spark framework voor HDInsight maakt snelle data-analyse en clustercomputing mogelijk met behulp van in-memory processing. Met Jupyter-notitieblok u communiceren met uw gegevens, code combineren met afwaarderingstekst en eenvoudige visualisaties maken.

Zie [Clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor diepgaande uitleg over beschikbare configuraties. Zie [Clusters maken in de portal](../hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie over het gebruik van de portal om clusters te maken.

> [!IMPORTANT]  
> HDInsight-clusters worden pro rato per minuut gefactureerd, ongeacht of u er wel of niet gebruik van maakt. Verwijder uw cluster daarom als u er klaar mee bent. Zie voor meer informatie de sectie [Resources opschonen](#clean-up-resources) van dit artikel.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Een Apache Spark-cluster maken in Azure HDInsight

U gebruikt de Azure-portal om een HDInsight-cluster te maken dat Azure Storage Blobs als clusteropslag gebruikt. Zie [Snelstart: Clusters instellen in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) voor meer informatie over het gebruik van Data Lake Storage Gen2.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer in het bovenste menu **+ Een resource maken.**

    ![Azure-portal maakt een bron](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Een resource maken in Azure Portal")

1. Selecteer **Analytics** > **Azure HDInsight** om naar de **clusterpagina HDInsight maken** te gaan.

1. Geef op het tabblad **Basisbeginselen** de volgende informatie op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement  | Selecteer in de vervolgkeuzelijst het Azure-abonnement dat voor het cluster wordt gebruikt. |
    |Resourcegroep | Selecteer in de vervolgkeuzelijst uw bestaande resourcegroep of selecteer **Nieuw maken**.|
    |Clusternaam | Voer een wereldwijd unieke naam in.|
    |Regio   | Selecteer in de vervolgkeuzelijst een gebied waar het cluster wordt gemaakt. |
    |Clustertype| Selecteer Clustertype selecteren om een lijst te openen. Selecteer **Spark**in de lijst .|
    |Clusterversie|Dit veld wordt automatisch gevuld met de standaardversie zodra het clustertype is geselecteerd.|
    |Gebruikersnaam voor clusteraanmeldgegevens| Voer de gebruikersnaam voor aanmelding bij het cluster in.  De standaardnaam is **beheerder**. Je gebruikt dit account om later in de quickstart in te loggen op het Jupyter-laptop. |
    |Wachtwoord voor clusteraanmeldgegevens| Voer het wachtwoord voor aanmelding bij het cluster in. |
    |SSH-gebruikersnaam (Secure Shell)| Voer de SSH-gebruikersnaam in. De SSH-gebruikersnaam voor deze snelstartgids is **sshuser**. De standaardinstelling is dat voor dit account hetzelfde wachtwoord wordt gebruikt als voor *Gebruikersnaam voor clusteraanmeldgegevens*. |

    ![Basisconfiguraties van HDInsight-clustermaken maken](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Spark-cluster maken in HDInsight de Basisconfiguraties")

    Selecteer **Volgende: Opslag >>** om door te gaan naar de pagina **Opslag.**

1. Geef onder **Opslag** de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Type van primaire opslag|Gebruik de standaardwaarde **Azure Storage**.|
    |Selectiemethode|Gebruik de standaardwaarde **Selecteren uit lijst**.|
    |Primair opslagaccount|Gebruik de automatisch ingevulde waarde.|
    |Container|Gebruik de automatisch ingevulde waarde.|

    ![Basisconfiguraties van HDInsight-clustermaken maken](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Spark-cluster maken in HDInsight de Basisconfiguraties")

    Selecteer **Controleren + maken** om door te gaan.

1. Selecteer **onder Controleren + maken**de optie **Maken**. Het duurt ongeveer 20 minuten om het cluster te maken. Het cluster moet zijn gemaakt voordat u verder kunt gaan met de volgende sessie.

Als u een probleem ondervindt bij het maken van HDInsight-clusters, kan het zijn dat u niet over de juiste machtigingen beschikt om dit te doen. Zie [Vereisten voor toegangsbeheer](../hdinsight-hadoop-customize-cluster-linux.md#access-control) voor meer informatie.

## <a name="create-a-jupyter-notebook"></a>Een Jupyter-notebook maken

Jupyter Notebook is een interactieve notitieblokomgeving die ondersteuning biedt voor verschillende programmeertalen. Via het notitieblok kunt u interactie hebben met uw gegevens, code combineren met markdown-tekst en eenvoudige visualisaties uitvoeren.

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/jupyter`naar `CLUSTERNAME` , waar is de naam van uw cluster. Voer de aanmeldingsreferenties voor het cluster in als u daarom wordt gevraagd.

1. Selecteer **Nieuwe** > **PySpark** om een notitieblok te maken.

   ![Een Jupyter-notitieblok maken om interactieve Spark SQL-query uit te voeren](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Een Jupyter-notitieblok maken om interactieve Spark SQL-query uit te voeren")

   Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-instructies uitvoeren

SQL (Structured Query Language) is de meest voorkomende en gebruikte taal voor het uitvoeren van query's en het definiëren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Controleer of de kernel gereed is. Wanneer u een lege cirkel naast de naam van de kernel in de notebook ziet, is de kernel gereed. Gevulde cirkel geeft aan dat de kernel bezet is.

    ![Apache Hive query in HDInsight](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive query in HDInsight")

    Wanneer u de notebook voor het eerst start, voert de kernel enkele taken in de achtergrond uit. Wacht tot de kernel gereed is.

1. Plak de volgende code in een lege cel en druk op **Shift+Enter** om de code uit te voeren. Met de opdracht worden de Hive-tabellen in het cluster weergegeven:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Wanneer u een Jupyter-notitieblok met uw HDInsight-cluster gebruikt, krijgt u een voorinstelling `sqlContext` die u gebruiken om Hive-query's uit te voeren met Spark SQL. `%%sql` instrueert Jupyter Notebook gebruik te maken van de vooraf ingestelde `sqlContext` om de Hive-query uit te voeren. De query haalt de bovenste tien rijen op uit een Hive-tabel (**hivesampletable**) die standaard worden meegeleverd met alle HDInsight-clusters. Het duurt ongeveer 30 seconden om de resultaten op te halen. De uitvoer ziet er als volgt uit:

    ![Apache Hive query in HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Hive query in HDInsight")

    Telkens wanneer u in Jupyter een query uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** en de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek.

1. Voer een andere query uit om de gegevens in `hivesampletable` te zien.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Het scherm wordt vernieuwd om de query-uitvoer weer te geven.

    ![Hive query output in HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Hive query output in HDInsight")

1. Klik in het menu **File** van het notebook op **Close and Halt**. Als de notebook wordt afgesloten, komen de clusterbronnen vrij.

## <a name="clean-up-resources"></a>Resources opschonen

HDInsight slaat uw gegevens op in Azure Storage of Azure Data Lake Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn. Als u direct verder wilt met de zelfstudie die wordt vermeld bij [Volgende stappen](#next-steps), is het beter om het cluster te behouden.

Ga terug naar Azure Portal en selecteer **Verwijderen**.

![Azure-portal verwijdert een HDInsight-cluster](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "HDInsight-cluster verwijderen")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de brongroep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Apache Spark-cluster in HDInsight maken en een basisSpark SQL-query uitvoeren. Ga naar de volgende zelfstudie om te leren hoe u een HDInsight-cluster gebruiken om interactieve query's op voorbeeldgegevens uit te voeren.

> [!div class="nextstepaction"]
> [Interactieve query's uitvoeren in Apache Spark](./apache-spark-load-data-run-query.md)
