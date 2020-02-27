---
title: 'Snelstartgids: Apache Hadoop, Apache Hive & Azure HDInsight-Portal'
description: In deze Quick Start gebruikt u de Azure Portal voor het maken van een HDInsight Hadoop-cluster
keywords: aan de slag met hadoop, hadoop linux, hadoop quickstart, aan de slag met hive, hive quickstart
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 7d2dd65224aad231db9574aa5a1fa4a00b328da8
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623540"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Snelstartgids: Apache Hadoop cluster maken in azure HDInsight met behulp van Azure Portal

In dit artikel leert u hoe u Apache Hadoop clusters in HDInsight maakt met behulp van Azure Portal en vervolgens Apache Hive taken in HDInsight uitvoert. De meeste Hadoop-taken zijn batchtaken. U maakt een cluster, voert enkele taken uit en verwijdert het cluster vervolgens. In dit artikel gaat u al deze drie taken uitvoeren. Zie [clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor gedetailleerde uitleg van de beschik bare configuraties. Zie [clusters maken in de portal](../hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie over het gebruik van de portal om clusters te maken.

In deze snelstartgids gebruikt u Azure Portal voor het maken van een Hadoop-cluster in HDInsight. U kunt ook een cluster maken met behulp van een [Azure Resource Manager-sjabloon](apache-hadoop-linux-tutorial-get-started.md).

Op dit moment wordt HDInsight geleverd met [zeven verschillende cluster typen](../hdinsight-overview.md#cluster-types-in-hdinsight). Elk clustertype ondersteunt een andere set onderdelen. Alle clustertypen ondersteunen Hive. Zie [Wat is er nieuw in de Apache Hadoop-clusterversies geleverd door HDInsight?](../hdinsight-component-versioning.md) voor een lijst met ondersteunde onderdelen in HDInsight.  

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster maken

In deze sectie maakt u een Hadoop-cluster in HDInsight met behulp van Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Selecteer in het bovenste menu de optie **+ een resource maken**.

    ![Een HDInsight-cluster voor resources maken](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Een HDInsight-cluster voor resources maken")

1. Selecteer **Analytics** > **Azure HDInsight** om naar de pagina **HDInsight-cluster maken** te gaan.

1. Geef op het tabblad **basis beginselen** de volgende informatie op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement    |  Selecteer in de vervolg keuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster. |
    |Resourcegroep     | Selecteer in de vervolg keuzelijst de bestaande resource groep of selecteer **nieuwe maken**.|
    |Clusternaam   | Voer een wereld wijd unieke naam in. De naam mag Maxi maal 59 tekens bevatten, inclusief letters, cijfers en afbreek streepjes. De eerste en laatste tekens van de naam mogen geen afbreek streepjes zijn. |
    |Regio    | Selecteer in de vervolg keuzelijst een regio waar het cluster wordt gemaakt.  Kies een locatie zo dicht mogelijk bij u in de buurt voor betere prestaties. |
    |Cluster type| Selecteer **cluster type selecteren**. Selecteer vervolgens **Hadoop** als het cluster type.|
    |Version|Selecteer een **versie**in de vervolg keuzelijst. Gebruik de standaard versie als u niet weet wat u moet kiezen.|
    |Gebruikers naam en wacht woord voor cluster aanmelding    | De standaard aanmeldings naam is **admin**. Het wacht woord moet uit minstens tien tekens bestaan en moet ten minste één cijfer, één hoofd letter en één kleine letters bevatten, één niet-alfanumeriek teken (met uitzonde ring van de tekens ' ") \). Zorg ervoor dat u **geen makkelijk te raden** wachtwoorden gebruikt, zoals 'Pass@word1'.|
    |SSH-gebruikersnaam (Secure Shell) | De standaardgebruikersnaam is **sshuser**.  U kunt hier echter een andere naam opgeven als u dat wilt. |
    |Wacht woord voor cluster aanmelding gebruiken voor SSH| Schakel dit selectie vakje in om hetzelfde wacht woord voor SSH-gebruiker te gebruiken als de gebruiker die u hebt opgegeven voor de aanmelding van het cluster.|

    ![Aan de slag met HDInsight Linux kunt u basis waarden van het cluster opgeven](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "Basis waarden opgeven voor het maken van een HDInsight-cluster")

    Selecteer de **volgende: opslag > >** om door te gaan naar de opslag instellingen.

1. Geef op het tabblad **opslag** de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Primair opslag type|Gebruik de standaard waarde **Azure Storage**.|
    |Selectie methode|Gebruik de standaard waarde **uit de lijst**.|
    |Primair opslagaccount|Gebruik de vervolg keuzelijst om een bestaand opslag account te selecteren of selecteer **nieuwe maken**. Als u een nieuw account maakt, moet de naam tussen de 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten|
    |Container|De automatisch gevulde waarde gebruiken.|

    ![HDInsight Linux aan de slag waarden voor cluster opslag opgeven](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Opslag waarden opgeven voor het maken van een HDInsight-cluster")

    Elk cluster is afhankelijk van een [Azure Storage-account](../hdinsight-hadoop-use-blob-storage.md) of een [Azure Data Lake-account](../hdinsight-hadoop-use-data-lake-store.md). Dit wordt aangeduid als het standaard opslag account. Het HDInsight-cluster en het standaard opslag account moeten zich in dezelfde Azure-regio bevinden. Als u clusters verwijdert, wordt het opslag account niet verwijderd.

    Selecteer het tabblad **controleren + maken** .

1. Controleer op het tabblad **controleren en maken** de waarden die u hebt geselecteerd in de vorige stappen.

    ![Cluster samenvatting aan de slag met HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "Cluster samenvatting aan de slag met HDInsight Linux")

1. Selecteer **Maken**. Het duurt ongeveer 20 minuten om een cluster te maken.

    Zodra het cluster is gemaakt, ziet u de overzichtspagina van het cluster in Azure Portal.

    ![Cluster instellingen aan de slag met HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Eigenschappen van HDInsight-cluster")

## <a name="run-apache-hive-queries"></a>Apache Hive-query's uitvoeren

[Apache Hive](hdinsight-use-hive.md) is het meest populaire onderdeel dat in HDInsight wordt gebruikt. Er zijn veel manieren om Hive-taken uit te voeren in HDInsight. In deze Quick Start gebruikt u de Ambari Hive-weer gave van de portal. Voor andere methoden voor het indienen van Hive-taken raadpleegt u [Hive gebruiken in HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive weer gave is niet beschikbaar in HDInsight 4,0.

1. Als u Ambari wilt openen, selecteert u **Clusterdashboard** in de vorige schermafbeelding.  U kunt ook bladeren naar `https://ClusterName.azurehdinsight.net` waarbij `ClusterName` het cluster is dat u in de vorige sectie hebt gemaakt.

    ![Cluster dashboard aan de slag met HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Cluster dashboard aan de slag met HDInsight Linux")

2. Voer de gebruikersnaam en het wachtwoord voor Hadoop in die u hebt opgegeven tijdens het maken van het cluster. De standaardgebruikersnaam **admin**.

3. Open **Hive-weergave** zoals weergegeven in de volgende schermafbeelding:

    ![Hive-weer gave van Ambari selecteren](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menu viewer van HDInsight-Hive")

4. Plak in het tabblad **QUERY** de volgende HiveQL-instructies in het werkblad:

    ```sql
    SHOW TABLES;
    ```

    ![Query-Editor voor HDInsight Hive-weer gave](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Query-Editor voor HDInsight Hive-weer gave")

5. Selecteer **Uitvoeren**. Er wordt een tabblad **RESULTATEN** weergegeven onder het tabblad **QUERY** met informatie over de taak. 

    Nadat de query is voltooid, worden de resultaten van de bewerking weergegeven op het tabblad **QUERY**. U ziet één tabel met de naam **hivesampletable**. Deze Hive-voorbeeldtabel is bij alle HDInsight-clusters inbegrepen.

    ![Resultaten van HDInsight Apache Hive weer geven](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Resultaten van HDInsight Apache Hive weer geven")

6. Herhaal stap 4 en 5 om de volgende query uit te voeren:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. U kunt de resultaten van de query ook opslaan. Selecteer de menuknop aan de rechterkant en geef aan of u de resultaten wilt downloaden als een CSV-bestand of deze wilt opslaan in het opslagaccount dat aan het cluster is gekoppeld.

    ![Resultaat van Apache Hive query opslaan](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Resultaat van Apache Hive query opslaan")

Nadat u een Hive-taak hebt voltooid, kunt u [de resultaten exporteren naar Azure SQL database-of SQL Server-Data Base](apache-hadoop-use-sqoop-mac-linux.md), kunt u [de resultaten ook visualiseren met Excel](apache-hadoop-connect-excel-power-query.md). Zie [Apache Hive en HiveQL gebruiken met Apache Hadoop in HDInsight voor het analyseren van een voorbeeldbestand van de Apache-log4j](hdinsight-use-hive.md) voor meer informatie over het gebruik van Hive in HDInsight.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

> [!NOTE]  
> Als u *direct* verdergaat met het volgende artikel voor meer informatie over het uitvoeren van ETL-bewerkingen met behulp van Hadoop op HDInsight, wilt u het cluster mogelijk blijven uitvoeren. De reden hiervoor is dat u in de zelf studie een Hadoop-cluster opnieuw moet maken. Als u het volgende artikel echter niet meteen gaat, moet u het cluster nu verwijderen.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Het cluster en/of het standaardopslagaccount verwijderen

1. Ga terug naar het browsertabblad voor Azure Portal. U komt terecht op de overzichtspagina voor het cluster. Selecteer **Verwijderen** als u alleen het cluster wilt verwijderen maar het standaardopslagaccount wilt behouden.

    ![Azure HDInsight-cluster verwijderen](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Azure HDInsight-cluster verwijderen")

2. Als u het cluster en het standaardopslagaccount wilt verwijderen, selecteert u de naam van de resourcegroep (gemarkeerd in de vorige schermafbeelding) om de pagina van de resourcegroep te openen.

3. Selecteer **Resourcegroep verwijderen** om de resourcegroep te verwijderen. De groep bevat zowel het cluster als het standaardopslagaccount. Als u de resourcegroep verwijdert, wordt ook het opslagaccount verwijderd. Als u het opslagaccount wilt behouden, verwijdert u alleen het cluster.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een HDInsight-cluster op basis van Linux maakt met behulp van een resource manager-sjabloon en hoe u eenvoudige Hive-query's uitvoert. In het volgende artikel leert u hoe u een ETL-bewerking (Extraction, Transformation, Loading) uitvoert met behulp van Hadoop in HDInsight.

> [!div class="nextstepaction"]
> [Gegevens uitpakken, transformeren en laden met interactieve Query's op HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
