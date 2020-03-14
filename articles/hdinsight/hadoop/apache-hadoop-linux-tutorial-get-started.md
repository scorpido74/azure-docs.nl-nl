---
title: 'Snelstartgids: Apache Hadoop & Resource Manager-Azure HDInsight'
description: In deze Quick Start maakt u Apache Hadoop cluster in azure HDInsight met behulp van Resource Manager-sjabloon
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 03/11/2020
ms.openlocfilehash: d3d8b749841e2c58b0999b92e942a79f4e3170ca
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370879"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snelstartgids: Apache Hadoop cluster in azure HDInsight maken met Resource Manager-sjabloon

In deze Quick Start leert u hoe u een Apache Hadoop cluster maakt in azure HDInsight met behulp van een resource manager-sjabloon.

Vergelijk bare sjablonen kunnen worden bekeken in [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)start-sjablonen. De sjabloonverwijzing vindt u [hier](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).  U kunt ook een cluster maken met behulp van de [Azure Portal](apache-hadoop-linux-create-cluster-get-started-portal.md).  

Op dit moment wordt HDInsight geleverd met [zeven verschillende clustertypen](../hdinsight-overview.md#cluster-types-in-hdinsight). Elk clustertype ondersteunt een andere set onderdelen. Alle clustertypen ondersteunen Hive. Zie voor een lijst van ondersteunde onderdelen in HDInsight [Wat is er nieuw in de Hadoop-clusterversies geleverd door HDInsight?](../hdinsight-component-versioning.md)  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-hadoop-cluster"></a>Een Hadoop-cluster maken

1. Selecteer de onderstaande knop **implementeren naar Azure** om u aan te melden bij Azure en de Resource Manager-sjabloon te openen in de Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Typ of selecteer de volgende waarden:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement|  Selecteer uw Azure-abonnement. |
    |Resourcegroep | Maak een resourcegroep of selecteer een bestaande resourcegroep.  Een resourcegroep is een container met Azure-onderdelen.  In dit geval bevat de resourcegroep het HDInsight-cluster en het afhankelijke Azure Storage-account. |
    |Locatie | Selecteer een Azure-locatie waar u het cluster wilt maken.  Kies een locatie zo dicht mogelijk bij u in de buurt voor betere prestaties. |
    |Clusternaam | Voer een naam in voor het Hadoop-cluster. Omdat alle clusters in HDInsight dezelfde DNS-naamruimte delen, moet deze naam uniek zijn. De naam mag alleen kleine letters, nummers en afbreekstreepjes bevatten en moet beginnen met een letter.  Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter.  De naam moet bovendien tussen 3 en 59 tekens lang zijn. |
    |Clustertype | Selecteer **hadoop**. |
    |Aanmeldings naam en wacht woord voor het cluster | De standaard aanmeldings naam is **admin**. Het wacht woord moet uit minstens tien tekens bestaan en moet ten minste één cijfer, één hoofd letter en één kleine letters bevatten, één niet-alfanumeriek teken (met uitzonde ring van de tekens ' ") \). Zorg ervoor dat u **geen makkelijk te raden** wachtwoorden gebruikt, zoals 'Pass@word1'.|
    |SSH-gebruikers naam en-wacht woord| De standaardgebruikersnaam is **sshuser**.  U kunt de SSH-gebruikersnaam wijzigen.  Voor het SSH-gebruikerswachtwoord gelden dezelfde vereisten als voor het aanmeldingswachtwoord voor het cluster.|

    Sommige eigenschappen zijn vastgelegd in de sjabloon.  U kunt deze waarden uit de sjabloon configureren. Raadpleeg voor meer uitleg over deze eigenschappen [Apache Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > De opgegeven waarden moeten uniek zijn en moeten voldoen aan deze richtlijnen voor naamgeving. De sjabloon voert geen validatiecontroles uit. Als de waarden die u opgeeft, al in gebruik zijn of niet aan de richtlijnen voldoen, krijgt u een foutmelding nadat u de sjabloon hebt verzonden.  

    ![Met HDInsight Linux wordt de Resource Manager-sjabloon op de portal gestart](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Hadoop-cluster in HDInsight implementeren met behulp van de Azure Portal en een resource Group Manager-sjabloon")

3. Selecteer **Ik ga akkoord met de bovenstaande voor waarden en**Selecteer vervolgens **kopen**. U ontvangt een melding dat uw implementatie wordt uitgevoerd.  Het duurt ongeveer 20 minuten om een cluster te maken.

4. Zodra het cluster is gemaakt, ontvangt u een melding over **implementatie geslaagd** met een koppeling **naar de resource groep** .  Op de pagina **Resourcegroep** worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster is afhankelijk van een [Azure Storage-account](../hdinsight-hadoop-use-blob-storage.md) of een [Azure Data Lake Storage-account](../hdinsight-hadoop-use-data-lake-store.md). Dit wordt aangeduid als het standaard opslag account. Het HDInsight-cluster en het standaard opslag account moeten zich in dezelfde Azure-regio bevinden. Als u clusters verwijdert, wordt het opslag account niet verwijderd.

> [!NOTE]  
> Zie [HDInsight-clusters maken](../hdinsight-hadoop-provision-linux-clusters.md)voor andere methoden voor het maken van een cluster en over de eigenschappen die worden gebruikt in deze Quick Start.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

> [!NOTE]  
> Als u *meteen* verder wilt gaan met de volgende zelfstudie om te leren hoe u ETL-bewerkingen uitvoert met behulp van Hadoop in HDInsight, kunt u het cluster beter behouden. De reden hiervoor is dat u in de zelf studie een Hadoop-cluster opnieuw moet maken. Als u echter niet direct verdergaat met de volgende zelfstudie, moet u het cluster nu verwijderen.

Het cluster en/of het standaard opslag account verwijderen:

1. Ga terug naar het browsertabblad voor Azure Portal. U komt terecht op de overzichtspagina voor het cluster. Selecteer **Verwijderen** als u alleen het cluster wilt verwijderen maar het standaardopslagaccount wilt behouden.

    ![HDInsight-cluster verwijderen uit Portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight-cluster verwijderen uit Portal")

2. Als u het cluster en het standaardopslagaccount wilt verwijderen, selecteert u de naam van de resourcegroep (gemarkeerd in de vorige schermafbeelding) om de pagina van de resourcegroep te openen.

3. Selecteer **Resourcegroep verwijderen** om de resourcegroep te verwijderen. De groep bevat zowel het cluster als het standaardopslagaccount. Als u de resourcegroep verwijdert, wordt ook het opslagaccount verwijderd. Als u het opslagaccount wilt behouden, verwijdert u alleen het cluster.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Apache Hadoop cluster maakt in HDInsight met behulp van een resource manager-sjabloon. In het volgende artikel leert u hoe u een ETL-bewerking (Extraction, Transformation, Loading) uitvoert met behulp van Hadoop in HDInsight.

> [!div class="nextstepaction"]
> [Gegevens uitpakken, transformeren en laden met interactieve Query's op HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)