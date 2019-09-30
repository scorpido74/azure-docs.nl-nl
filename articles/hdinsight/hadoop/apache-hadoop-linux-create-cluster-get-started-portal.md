---
title: 'Quickstart: Apache Hadoop en Apache Hive in HDInsight met behulp van de Azure Portal'
description: In deze Quick Start gebruikt u de Azure Portal voor het maken van een HDInsight Hadoop-cluster
keywords: aan de slag met hadoop, hadoop linux, hadoop quickstart, aan de slag met hive, hive quickstart
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: 5d87cc7fdcd9c8065c2a9886b970b406df0d8fc8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677912"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Quickstart: Apache Hadoop cluster maken in azure HDInsight met behulp van Azure Portal

In dit artikel leert u hoe u [Apache Hadoop](https://hadoop.apache.org/)-clusters maakt in HDInsight met behulp van de Azure-portal en vervolgens Apache Hive-taken uitvoert in HDInsight. De meeste Hadoop-taken zijn batchtaken. U maakt een cluster, voert enkele taken uit en verwijdert het cluster vervolgens. In dit artikel gaat u al deze drie taken uitvoeren.

In deze snelstartgids gebruikt u Azure Portal voor het maken van een Hadoop-cluster in HDInsight. U kunt ook een cluster maken met behulp van een [Azure Resource Manager-sjabloon](apache-hadoop-linux-tutorial-get-started.md).

Op dit moment wordt HDInsight geleverd met [zeven verschillende clustertypen](../hdinsight-overview.md#cluster-types-in-hdinsight). Elk clustertype ondersteunt een andere set onderdelen. Alle clustertypen ondersteunen Hive. Zie [Wat is er nieuw in de Apache Hadoop-clusterversies geleverd door HDInsight?](../hdinsight-component-versioning.md) voor een lijst met ondersteunde onderdelen in HDInsight.  

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster maken

In deze sectie maakt u een Hadoop-cluster in HDInsight met behulp van Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Ga vanuit het Azure Portal naar **een resource** > **Analytics** > **HDInsight**maken.

    ![Een HDInsight-cluster voor resources maken](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Een HDInsight-cluster voor resources maken")

1. Typ of Selecteer onder **basis beginselen**de volgende waarden:

    |Eigenschap  |Description  |
    |---------|---------|
    |Subscription    |  Selecteer uw Azure-abonnement. |
    |Resource group     | Maak een resourcegroep of selecteer een bestaande resourcegroep.  Een resourcegroep is een container met Azure-onderdelen.  In dit geval bevat de resourcegroep het HDInsight-cluster en het afhankelijke Azure Storage-account. |
    |Clusternaam   | Voer een naam in voor het Hadoop-cluster. Omdat alle clusters in HDInsight dezelfde DNS-naamruimte delen, moet deze naam uniek zijn. De naam mag Maxi maal 59 tekens bevatten, inclusief letters, cijfers en afbreek streepjes. De eerste en laatste tekens van de naam mogen geen streepjes zijn. |
    |Location    | Selecteer een Azure-locatie waar u het cluster wilt maken.  Kies een locatie zo dicht mogelijk bij u in de buurt voor betere prestaties. |
    |Clustertype| Selecteer **cluster type selecteren**. Selecteer vervolgens **Hadoop** als het cluster type.|
    |Version|De standaard versie voor het cluster type wordt opgegeven. Selecteer in de vervolg keuzelijst de optie als u een andere versie wilt opgeven.|
    |Gebruikers naam en wacht woord voor cluster aanmelding    | De standaardaanmeldingsnaam is **admin**. Het wachtwoord moet uit minstens tien tekens bestaan en moet minstens één cijfer, één hoofdletter, één kleine letter en één niet-alfanumeriek teken bevatten (uitgezonderd ' " ` \). Zorg ervoor dat u **geen makkelijk te raden** wachtwoorden gebruikt, zoals 'Pass@word1'.|
    |SSH-gebruikersnaam (Secure Shell) | De standaardgebruikersnaam is **sshuser**.  U kunt hier echter een andere naam opgeven als u dat wilt. |
    |Wacht woord voor cluster aanmelding gebruiken voor SSH| Schakel dit selectie vakje in om hetzelfde wacht woord voor SSH-gebruiker te gebruiken als de gebruiker die u hebt opgegeven voor de aanmelding van het cluster.|

    ![HDInsight Linux aan de slag basiswaarden voor cluster opgeven](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "Geef basiswaarden op voor het maken van een HDInsight-cluster")

    Selecteer de **Next: Opslag > >**  om door te gaan naar de opslag instellingen.

1. Geef op het tabblad **opslag** de volgende waarden op:

    |Eigenschap  |Description  |
    |---------|---------|
    |Type van primaire opslag|Gebruik de standaard waarde **Azure Storage**.|
    |Selectiemethode|Gebruik de standaard waarde **uit de lijst**.|
    |Primair opslagaccount|Gebruik de vervolg keuzelijst om een bestaand opslag account te selecteren of selecteer **nieuwe maken**. Als u een nieuw account maakt, moet de naam tussen de 3 en 24 tekens lang zijn en mag alleen cijfers en kleine letters bevatten|
    |Container|De automatisch gevulde waarde gebruiken.|

    ![HDInsight Linux aan de slag waarden voor clusteropslag opgeven](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage-blank.png "Geef opslagwaarden op voor het maken van een HDInsight-cluster")

    Selecteer het tabblad **controleren + maken** .

1. Controleer op het tabblad **controleren en maken** de waarden die u hebt geselecteerd in de vorige stappen.

    ![HDInsight Linux aan de slag cluster overzicht](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux aan de slag cluster overzicht")

1. Selecteer **Maken**. Het duurt ongeveer 20 minuten om een cluster te maken.

Zodra het cluster is gemaakt, ziet u de overzichtspagina van het cluster in Azure Portal.

![Aan de slag met clusterinstellingen in HDInsight op basis van Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "HDInsight-clustereigenschappen")

Elk cluster is afhankelijk van een [Azure Storage-account](../hdinsight-hadoop-use-blob-storage.md) of een [Azure Data Lake-account](../hdinsight-hadoop-use-data-lake-store.md). Dit wordt het standaardopslagaccount genoemd. Het HDInsight-cluster en het standaard opslag account moeten zich in dezelfde Azure-regio bevinden. Het opslagaccount wordt niet verwijderd wanneer er clusters worden verwijderd.

> [!NOTE]  
> Zie [HDInsight-clusters maken](../hdinsight-hadoop-provision-linux-clusters.md)voor andere methoden voor het maken van een cluster en over de eigenschappen die worden gebruikt in deze Quick Start.

## <a name="run-apache-hive-queries"></a>Apache Hive-query's uitvoeren

[Apache Hive](hdinsight-use-hive.md) is het meest populaire onderdeel dat in HDInsight wordt gebruikt. Er zijn veel manieren om Hive-taken uit te voeren in HDInsight. In deze Quick Start gebruikt u de Ambari Hive-weer gave van de portal. Voor andere methoden voor het indienen van Hive-taken raadpleegt u [Hive gebruiken in HDInsight](hdinsight-use-hive.md).

1. Als u Ambari wilt openen, selecteert u **Clusterdashboard** in de vorige schermafbeelding.  U kunt ook bladeren naar `https://ClusterName.azurehdinsight.net`, waar `ClusterName` het cluster is dat u in de vorige sectie hebt gemaakt.

    ![HDInsight Linux aan de slag clusterdashboard](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux aan de slag clusterdashboard")

2. Voer de gebruikersnaam en het wachtwoord voor Hadoop in die u hebt opgegeven tijdens het maken van het cluster. De standaardgebruikersnaam **admin**.

3. Open **Hive-weergave** zoals weergegeven in de volgende schermafbeelding:

    ![Hive-weer gave van Ambari selecteren](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menu viewer van HDInsight-Hive")

4. Plak in het tabblad **QUERY** de volgende HiveQL-instructies in het werkblad:

    ```sql
    SHOW TABLES;
    ```

    ![Query-editor voor HDInsight Hive-weer gave](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Query-editor voor HDInsight Hive-weer gave")

5. Selecteer **Uitvoeren**. Er wordt een tabblad **RESULTATEN** weergegeven onder het tabblad **QUERY** met informatie over de taak. 

    Nadat de query is voltooid, worden de resultaten van de bewerking weergegeven op het tabblad **QUERY**. U ziet één tabel met de naam **hivesampletable**. Deze Hive-voorbeeldtabel is bij alle HDInsight-clusters inbegrepen.

    ![Resultaten van HDInsight Apache Hive weer geven](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Resultaten van HDInsight Apache Hive weer geven")

6. Herhaal stap 4 en 5 om de volgende query uit te voeren:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. U kunt de resultaten van de query ook opslaan. Selecteer de menuknop aan de rechterkant en geef aan of u de resultaten wilt downloaden als een CSV-bestand of deze wilt opslaan in het opslagaccount dat aan het cluster is gekoppeld.

    ![Resultaat van Apache Hive query opslaan](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Resultaat van Apache Hive query opslaan")

Nadat u een Hive-taak hebt voltooid, kunt u [de resultaten exporteren naar een Azure SQL-database of een SQL Server-database](apache-hadoop-use-sqoop-mac-linux.md). U kunt ook [de resultaten weergeven in Excel](apache-hadoop-connect-excel-power-query.md). Zie [Apache Hive en HiveQL gebruiken met Apache Hadoop in HDInsight voor het analyseren van een voorbeeldbestand van de Apache-log4j](hdinsight-use-hive.md) voor meer informatie over het gebruik van Hive in HDInsight.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

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
