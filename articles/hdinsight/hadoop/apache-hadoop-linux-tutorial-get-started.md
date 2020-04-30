---
title: 'Snelstartgids: Apache Hadoop cluster in azure HDInsight maken met Resource Manager-sjabloon'
description: In deze Quick Start maakt u Apache Hadoop cluster in azure HDInsight met behulp van Resource Manager-sjabloon
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: ae0f29b8085bd9637f527f2a58229dd89ce6933b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80064681"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snelstartgids: Apache Hadoop cluster in azure HDInsight maken met Resource Manager-sjabloon

In deze Quick Start gebruikt u een Azure Resource Manager sjabloon voor het maken van een [Apache Hadoop](./apache-hadoop-introduction.md) cluster in azure HDInsight. Hadoop was het originele open-source framework voor gedistribueerde verwerking en analyse van big data sets op clusters. Het Hadoop-ecosysteem bevat gerelateerde software en hulpprogram ma's, waaronder Apache Hive, Apache HBase, Spark, Kafka en vele andere.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
Op dit moment wordt HDInsight geleverd met [zeven verschillende clustertypen](../hdinsight-overview.md#cluster-types-in-hdinsight). Elk clustertype ondersteunt een andere set onderdelen. Alle clustertypen ondersteunen Hive. Zie voor een lijst van ondersteunde onderdelen in HDInsight [Wat is er nieuw in de Hadoop-clusterversies geleverd door HDInsight?](../hdinsight-component-versioning.md)  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json" range="1-148":::


Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [Micro soft. Storage/Storage accounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): een Azure Storage-account maken.
* [Micro soft. HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): een HDInsight-cluster maken.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de onderstaande knop **implementeren naar Azure** om u aan te melden bij Azure en de Resource Manager-sjabloon te openen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Typ of selecteer de volgende waarden:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement|Selecteer in de vervolg keuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster.|
    |Resourcegroep|Selecteer in de vervolg keuzelijst de bestaande resource groep of selecteer **nieuwe maken**.|
    |Locatie|De waarde wordt automatisch ingevuld met de locatie die wordt gebruikt voor de resource groep.|
    |Clusternaam|Voer een wereldwijd unieke naam in. Voor deze sjabloon gebruikt u alleen kleine letters en cijfers.|
    |Clustertype | Selecteer **Hadoop**. |
    |Gebruikersnaam voor clusteraanmelding|Geef de gebruikers naam op. de standaard instelling is **admin**.|
    |Wachtwoord voor clusteraanmelding|Geef een wacht woord op. Het wacht woord moet uit minstens tien tekens bestaan en moet ten minste één cijfer, één hoofd letter en één kleine letters, één niet-alfanumeriek teken (behalve de tekens ' "') bevatten. |
    |SSH-gebruikers naam|Geef de gebruikers naam op. de standaard waarde is **sshuser**|
    |SSH-wacht woord|Geef het wacht woord op.|

    Sommige eigenschappen zijn vastgelegd in de sjabloon.  U kunt deze waarden uit de sjabloon configureren. Raadpleeg voor meer uitleg over deze eigenschappen [Apache Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > De opgegeven waarden moeten uniek zijn en moeten voldoen aan deze richtlijnen voor naamgeving. De sjabloon voert geen validatiecontroles uit. Als de waarden die u opgeeft, al in gebruik zijn of niet aan de richtlijnen voldoen, krijgt u een foutmelding nadat u de sjabloon hebt verzonden.  

    ![Met HDInsight Linux wordt de Resource Manager-sjabloon op de portal gestart](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Hadoop-cluster in HDInsight implementeren met behulp van de Azure Portal en een resource Group Manager-sjabloon")

1. Bekijk de **voor waarden**. Selecteer **Ik ga akkoord met de bovenstaande voor waarden en**klik vervolgens op **kopen**. U ontvangt een melding dat uw implementatie wordt uitgevoerd. Het duurt ongeveer 20 minuten om een cluster te maken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Zodra het cluster is gemaakt, ontvangt u een melding over **implementatie geslaagd** met een koppeling **naar de resource** . Op de pagina Resourcegroep worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster heeft een [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) account of een afhankelijkheid van [Azure data Lake Storage-account](../hdinsight-hadoop-use-data-lake-store.md) . Dit wordt aangeduid als het standaard opslag account. Het HDInsight-cluster en het standaard opslag account moeten zich in dezelfde Azure-regio bevinden. Als u clusters verwijdert, wordt het opslag account niet verwijderd.

> [!NOTE]  
> Zie [HDInsight-clusters maken](../hdinsight-hadoop-provision-linux-clusters.md)voor andere methoden voor het maken van een cluster en over de eigenschappen die worden gebruikt in deze Quick Start.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

> [!NOTE]  
> Als u *meteen* verder wilt gaan met de volgende zelfstudie om te leren hoe u ETL-bewerkingen uitvoert met behulp van Hadoop in HDInsight, kunt u het cluster beter behouden. De reden hiervoor is dat u in de zelf studie een Hadoop-cluster opnieuw moet maken. Als u echter niet direct verdergaat met de volgende zelfstudie, moet u het cluster nu verwijderen.

Navigeer vanuit het Azure Portal naar uw cluster en selecteer **verwijderen**.

![HDInsight-cluster verwijderen uit Portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight-cluster verwijderen uit Portal")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de resource groep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaard opslag account.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Apache Hadoop cluster maakt in HDInsight met behulp van een resource manager-sjabloon. In het volgende artikel leert u hoe u een ETL-bewerking (Extraction, Transformation, Loading) uitvoert met behulp van Hadoop in HDInsight.

> [!div class="nextstepaction"]
> [Gegevens uitpakken, transformeren en laden met interactieve Query's op HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
