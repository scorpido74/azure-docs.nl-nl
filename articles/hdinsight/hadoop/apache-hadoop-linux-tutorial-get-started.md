---
title: 'Snelstart: Apache Hadoop-cluster maken in Azure HDInsight met behulp van de sjabloon ResourceManager'
description: In deze quickstart maakt u apache hadoop-cluster in Azure HDInsight met behulp van resourcebeheersjabloon
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: ae0f29b8085bd9637f527f2a58229dd89ce6933b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064681"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snelstart: Apache Hadoop-cluster maken in Azure HDInsight met behulp van de sjabloon ResourceManager

In deze snelstart gebruikt u een Azure Resource Manager-sjabloon om een [Apache Hadoop-cluster](./apache-hadoop-introduction.md) in Azure HDInsight te maken. Hadoop was het oorspronkelijke open-source framework voor gedistribueerde verwerking en analyse van big data sets op clusters. Het Hadoop-ecosysteem omvat gerelateerde software en hulpprogramma's, waaronder Apache Hive, Apache HBase, Spark, Kafka en vele anderen.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
Op dit moment wordt HDInsight geleverd met [zeven verschillende clustertypen](../hdinsight-overview.md#cluster-types-in-hdinsight). Elk clustertype ondersteunt een andere set onderdelen. Alle clustertypen ondersteunen Hive. Zie voor een lijst van ondersteunde onderdelen in HDInsight [Wat is er nieuw in de Hadoop-clusterversies geleverd door HDInsight?](../hdinsight-component-versioning.md)  

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-apache-hadoop-cluster"></a>Een Apache Hadoop-cluster maken

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json" range="1-148":::


In de sjabloon worden twee Azure-resources gedefinieerd:

* [Microsoft.Storage/storageAccounts:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)maak een Azure Storage Account.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): maak een HDInsight-cluster.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de knop **Implementeren naar Azure** hieronder om u aan te melden bij Azure en open de sjabloon Resourcebeheer.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Typ of selecteer de volgende waarden:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |Abonnement|Selecteer in de vervolgkeuzelijst het Azure-abonnement dat voor het cluster wordt gebruikt.|
    |Resourcegroep|Selecteer in de vervolgkeuzelijst uw bestaande resourcegroep of selecteer **Nieuw maken**.|
    |Locatie|De waarde wordt automatisch gevuld met de locatie die wordt gebruikt voor de resourcegroep.|
    |Clusternaam|Voer een wereldwijd unieke naam in. Gebruik voor deze sjabloon alleen kleine letters en cijfers.|
    |Clustertype | Selecteer **hadoop**. |
    |Gebruikersnaam voor clusteraanmelding|Geef de gebruikersnaam op, standaard is **beheerder**.|
    |Wachtwoord voor clusteraanmelding|Geef een wachtwoord op. Het wachtwoord moet ten minste tien tekens lang zijn en moet ten minste één cijfer, één hoofdletter en één kleine letter bevatten, één niet-alfanumeriek teken (met uitzondering van tekens " ). |
    |Ssh-gebruikersnaam|Geef de gebruikersnaam op, standaard is **sshuser**|
    |Ssh-wachtwoord|Geef het wachtwoord op.|

    Sommige eigenschappen zijn vastgelegd in de sjabloon.  U kunt deze waarden uit de sjabloon configureren. Raadpleeg voor meer uitleg over deze eigenschappen [Apache Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > De opgegeven waarden moeten uniek zijn en moeten voldoen aan deze richtlijnen voor naamgeving. De sjabloon voert geen validatiecontroles uit. Als de waarden die u opgeeft, al in gebruik zijn of niet aan de richtlijnen voldoen, krijgt u een foutmelding nadat u de sjabloon hebt verzonden.  

    ![HDInsight Linux gaat aan de slag met Resource Manager-sjabloon op portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Hadoop-cluster implementeren in HDInsight met behulp van de Azure-portal en een sjabloon voor resourcegroepbeheer")

1. Bekijk de **algemene voorwaarden**. Selecteer dan **ik ga akkoord met de hierboven vermelde algemene voorwaarden,** dan **Koop**. U ontvangt een melding dat uw implementatie aan de gang is. Het duurt ongeveer 20 minuten om een cluster te maken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Zodra het cluster is gemaakt, ontvangt u een **door implementatie geslaagde** melding met een **koppeling naar de bron ga.** Op de pagina Resourcegroep worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster heeft een [Azure Storage-account](../hdinsight-hadoop-use-blob-storage.md) of een Azure [Data Lake Storage-accountafhankelijkheid.](../hdinsight-hadoop-use-data-lake-store.md) Het wordt het standaardopslagaccount genoemd. Het HDInsight-cluster en het standaardopslagaccount moeten in dezelfde Azure-regio worden ondergebracht. Als u clusters verwijdert, wordt het opslagaccount niet verwijderd.

> [!NOTE]  
> Zie [CLUSTERS HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)maken voor andere methoden voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor het maken van clusters voor clustercreatie en inzicht in de eigenschappen die in deze quickstart worden gebruikt.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

> [!NOTE]  
> Als u *meteen* verder wilt gaan met de volgende zelfstudie om te leren hoe u ETL-bewerkingen uitvoert met behulp van Hadoop in HDInsight, kunt u het cluster beter behouden. Dit komt omdat je in de tutorial opnieuw een Hadoop-cluster moet maken. Als u echter niet direct verdergaat met de volgende zelfstudie, moet u het cluster nu verwijderen.

Navigeer in de Azure-portal naar uw cluster en selecteer **Verwijderen**.

![HDInsight verwijdert cluster uit portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight verwijdert cluster uit portal")

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de brongroep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Apache Hadoop-cluster in HDInsight maken met behulp van een Resource Manager-sjabloon. In het volgende artikel leert u hoe u een ETL-bewerking (Extraction, Transformation, Loading) uitvoert met behulp van Hadoop in HDInsight.

> [!div class="nextstepaction"]
> [Gegevens extraheren, transformeren en laden met interactieve query op HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
