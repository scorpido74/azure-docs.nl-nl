---
title: 'Snelstart: Apache Hadoop, Apache Hive & Azure HDInsight portal'
description: In deze quickstart gebruikt u de Azure-portal om een HDInsight Hadoop-cluster te maken
keywords: aan de slag met hadoop, hadoop linux, hadoop quickstart, aan de slag met hive, hive quickstart
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 8939d9b342094342d576c00cf02e622286c8fc0f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130586"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Snelstart: Apache Hadoop-cluster maken in Azure HDInsight met Azure-portal

In dit artikel leert u hoe u Apache Hadoop-clusters maakt in HDInsight met behulp van de Azure-portal en vervolgens Apache Hive-taken uitvoert in HDInsight. De meeste Hadoop-taken zijn batchtaken. U maakt een cluster, voert enkele taken uit en verwijdert het cluster vervolgens. In dit artikel gaat u al deze drie taken uitvoeren. Zie [Clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor diepgaande uitleg over beschikbare configuraties. Zie [Clusters maken in de portal](../hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie over het gebruik van de portal om clusters te maken.

In deze snelstartgids gebruikt u Azure Portal voor het maken van een Hadoop-cluster in HDInsight. U kunt ook een cluster maken met behulp van een [Azure Resource Manager-sjabloon](apache-hadoop-linux-tutorial-get-started.md).

Momenteel wordt HDInsight geleverd met [zeven verschillende clustertypen.](../hdinsight-overview.md#cluster-types-in-hdinsight) Elk clustertype ondersteunt een andere set onderdelen. Alle clustertypen ondersteunen Hive. Zie [Wat is er nieuw in de Apache Hadoop-clusterversies geleverd door HDInsight?](../hdinsight-component-versioning.md) voor een lijst met ondersteunde onderdelen in HDInsight.  

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster maken

In deze sectie maakt u een Hadoop-cluster in HDInsight met behulp van Azure Portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer in het bovenste menu **+ Een resource maken.**

    ![Een HDInsight-cluster voor bronnen maken](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Een HDInsight-cluster voor bronnen maken")

1. Selecteer **Analytics** > **Azure HDInsight** om naar de **clusterpagina HDInsight maken** te gaan.

1. Geef op het tabblad **Basisbeginselen** de volgende informatie op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement    |  Selecteer in de vervolgkeuzelijst het Azure-abonnement dat voor het cluster wordt gebruikt. |
    |Resourcegroep     | Selecteer in de vervolgkeuzelijst uw bestaande resourcegroep of selecteer **Nieuw maken**.|
    |Clusternaam   | Voer een wereldwijd unieke naam in. De naam kan bestaan uit maximaal 59 tekens, waaronder letters, cijfers en koppeltekens. De eerste en laatste tekens van de naam kunnen geen koppeltekens zijn. |
    |Regio    | Selecteer in de vervolgkeuzelijst een gebied waar het cluster wordt gemaakt.  Kies een locatie zo dicht mogelijk bij u in de buurt voor betere prestaties. |
    |Clustertype| Selecteer **Clustertype selecteren**. Selecteer **vervolgens Hadoop** als clustertype.|
    |Versie|Selecteer in de vervolgkeuzelijst een **versie**. Gebruik de standaardversie als u niet weet wat u moet kiezen.|
    |Gebruikersnaam/Wachtwoord voor clusteraanmeldgegevens    | De standaardinlognaam is **beheerder**. Het wachtwoord moet ten minste tien tekens lang zijn en moet ten minste één cijfer, één hoofdletter en één kleine \)letter bevatten, één niet-alfanumeriek teken (met uitzondering van tekens " " . Zorg ervoor dat u **geen makkelijk te raden** wachtwoorden gebruikt, zoals 'Pass@word1'.|
    |SSH-gebruikersnaam (Secure Shell) | De standaardgebruikersnaam is **sshuser**.  U kunt hier echter een andere naam opgeven als u dat wilt. |
    |Wachtwoord voor clusteraanmelding voor SSH gebruiken| Schakel dit selectievakje in om hetzelfde wachtwoord te gebruiken voor SSH-gebruiker als het wachtwoord dat u hebt opgegeven voor de gebruikers van het clusteraanmelding.|

    ![HDInsight Linux aan de slag bieden cluster basiswaarden](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png "Basiswaarden opgeven voor het maken van een HDInsight-cluster")

    Selecteer de **volgende: opslag >>** om door te gaan naar de opslaginstellingen.

1. Geef op het tabblad **Opslag** de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Type van primaire opslag|Gebruik de standaardwaarde **Azure Storage**.|
    |Selectiemethode|Gebruik de standaardwaarde **Selecteren uit lijst**.|
    |Primair opslagaccount|Gebruik de vervolgkeuzelijst om een bestaand opslagaccount te selecteren of selecteer **Nieuw maken**. Als u een nieuw account maakt, moet de naam tussen de 3 en 24 tekens lang zijn en kunnen er alleen cijfers en kleine letters worden vermeld|
    |Container|Gebruik de automatisch ingevulde waarde.|

    ![HDInsight Linux aan de slag bieden cluster opslag waarden](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Opslagwaarden opgeven voor het maken van een HDInsight-cluster")

    Elk cluster is afhankelijk van een [Azure Storage-account](../hdinsight-hadoop-use-blob-storage.md) of een [Azure Data Lake-account](../hdinsight-hadoop-use-data-lake-store.md). Het wordt het standaardopslagaccount genoemd. Het HDInsight-cluster en het standaardopslagaccount moeten in dezelfde Azure-regio worden ondergebracht. Als u clusters verwijdert, wordt het opslagaccount niet verwijderd.

    Selecteer het tabblad **Controleren + maken.**

1. Controleer op het tabblad **Controleren + maken** de waarden die u in de eerdere stappen hebt geselecteerd.

    ![HDInsight Linux aan de slag cluster samenvatting](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux aan de slag cluster samenvatting")

1. Selecteer **Maken**. Het duurt ongeveer 20 minuten om een cluster te maken.

    Zodra het cluster is gemaakt, ziet u de overzichtspagina van het cluster in Azure Portal.

    ![Aan de slag met clusterinstellingen in HDInsight op basis van Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "HDInsight-clustereigenschappen")

## <a name="run-apache-hive-queries"></a>Apache Hive-query's uitvoeren

[Apache Hive](hdinsight-use-hive.md) is het meest populaire onderdeel dat in HDInsight wordt gebruikt. Er zijn veel manieren om Hive-taken uit te voeren in HDInsight. In deze quickstart gebruikt u de Ambari Hive-weergave vanaf de portal. Voor andere methoden voor het indienen van Hive-taken raadpleegt u [Hive gebruiken in HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive View is niet beschikbaar in HDInsight 4.0.

1. Als u Ambari wilt openen, selecteert u **Clusterdashboard** in de vorige schermafbeelding.  U ook `https://ClusterName.azurehdinsight.net` `ClusterName` bladeren naar waar het cluster is dat u in de vorige sectie hebt gemaakt.

    ![HDInsight Linux aan de slag cluster dashboard](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux aan de slag cluster dashboard")

2. Voer de gebruikersnaam en het wachtwoord voor Hadoop in die u hebt opgegeven tijdens het maken van het cluster. De standaardgebruikersnaam **admin**.

3. Open **Hive-weergave** zoals weergegeven in de volgende schermafbeelding:

    ![Hive-weergave selecteren in Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "HdInsight Hive Viewer menu")

4. Plak in het tabblad **QUERY** de volgende HiveQL-instructies in het werkblad:

    ```sql
    SHOW TABLES;
    ```

    ![QUERY-editor hdinsight hive-weergave](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "QUERY-editor hdinsight hive-weergave")

5. Selecteer **Uitvoeren**. Er wordt een tabblad **RESULTATEN** weergegeven onder het tabblad **QUERY** met informatie over de taak. 

    Zodra de query is voltooid, worden op het tabblad **QUERY** de resultaten van de bewerking weergegeven. U ziet één tabel met de naam **hivesampletable**. Deze Hive-voorbeeldtabel is bij alle HDInsight-clusters inbegrepen.

    ![HDInsight Apache Hive bekijk resultaten](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight Apache Hive bekijk resultaten")

6. Herhaal stap 4 en 5 om de volgende query uit te voeren:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. U kunt de resultaten van de query ook opslaan. Selecteer de menuknop aan de rechterkant en geef aan of u de resultaten wilt downloaden als een CSV-bestand of deze wilt opslaan in het opslagaccount dat aan het cluster is gekoppeld.

    ![Resultaat van Apache Hive-query opslaan](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Resultaat van Apache Hive-query opslaan")

Nadat u een Hive-taak hebt voltooid, u [de resultaten exporteren naar Azure SQL Database of SQL Server-database,](apache-hadoop-use-sqoop-mac-linux.md)u de resultaten ook [visualiseren met Excel.](apache-hadoop-connect-excel-power-query.md) Zie [Apache Hive en HiveQL gebruiken met Apache Hadoop in HDInsight voor het analyseren van een voorbeeldbestand van de Apache-log4j](hdinsight-use-hive.md) voor meer informatie over het gebruik van Hive in HDInsight.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

> [!NOTE]  
> Als u *onmiddellijk* doorgaat naar het volgende artikel om te leren hoe u ETL-bewerkingen uitvoert met Hadoop op HDInsight, u het cluster draaiende houden. Dit komt omdat je in de tutorial opnieuw een Hadoop-cluster moet maken. Als u het volgende artikel echter niet meteen doorloopt, moet u het cluster nu verwijderen.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Het cluster en/of het standaardopslagaccount verwijderen

1. Ga terug naar het browsertabblad voor Azure Portal. U komt terecht op de overzichtspagina voor het cluster. Selecteer **Verwijderen** als u alleen het cluster wilt verwijderen maar het standaardopslagaccount wilt behouden.

    ![Azure HDInsight-cluster verwijderen](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Azure HDInsight-cluster verwijderen")

2. Als u het cluster en het standaardopslagaccount wilt verwijderen, selecteert u de naam van de resourcegroep (gemarkeerd in de vorige schermafbeelding) om de pagina van de resourcegroep te openen.

3. Selecteer **Resourcegroep verwijderen** om de resourcegroep te verwijderen. De groep bevat zowel het cluster als het standaardopslagaccount. Als u de resourcegroep verwijdert, wordt ook het opslagaccount verwijderd. Als u het opslagaccount wilt behouden, verwijdert u alleen het cluster.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een HDInsight-cluster op basis van Linux maken met behulp van een Resource Manager-sjabloon en hoe u basisquery's van Hive uitvoeren. In het volgende artikel leert u hoe u een ETL-bewerking (Extraction, Transformation, Loading) uitvoert met behulp van Hadoop in HDInsight.

> [!div class="nextstepaction"]
> [Gegevens extraheren, transformeren en laden met interactieve query op HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
