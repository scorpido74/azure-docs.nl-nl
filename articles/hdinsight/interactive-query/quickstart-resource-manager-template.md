---
title: 'Snelstart: cluster interactieve query maken met sjabloon - Azure HDInsight'
description: Met deze snelle start ziet u hoe u resourcebeheersjabloon gebruiken om een cluster voor interactieve query's in Azure HDInsight te maken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 74b1c25c8bab11c0b2a72510fd419df239e2c23a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603566"
---
# <a name="quickstart-create-interactive-query-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snelstart: cluster interactieve query maken in Azure HDInsight met behulp van de sjabloon Resourcebeheer

In deze snelstart gebruikt u een Azure Resource Manager-sjabloon om een [cluster voor interactieve query's](./apache-interactive-query-get-started.md) in Azure HDInsight te maken. Interactieve query (ook wel Apache Hive LLAP of [Low Latency Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP)is een Azure HDInsight-clustertype. [cluster type](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-interactive-query-cluster"></a>Een cluster interactieve query maken

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-interactive-hive)

:::code language="json" source="~/quickstart-templates/101-hdinsight-interactive-hive/azuredeploy.json" range="1-158":::


In de sjabloon worden twee Azure-resources gedefinieerd:

* [Microsoft.Storage/storageAccounts:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)maak een Azure Storage Account.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): maak een HDInsight-cluster.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de knop **Implementeren naar Azure** hieronder om u aan te melden bij Azure en open de sjabloon Resourcebeheer.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer in de vervolgkeuzelijst het Azure-abonnement dat voor het cluster wordt gebruikt.|
    |Resourcegroep|Selecteer in de vervolgkeuzelijst uw bestaande resourcegroep of selecteer **Nieuw maken**.|
    |Locatie|De waarde wordt automatisch gevuld met de locatie die wordt gebruikt voor de resourcegroep.|
    |Clusternaam|Voer een wereldwijd unieke naam in. Gebruik voor deze sjabloon alleen kleine letters en cijfers.|
    |Gebruikersnaam voor clusteraanmelding|Geef de gebruikersnaam op, standaard is **beheerder**.|
    |Wachtwoord voor clusteraanmelding|Geef een wachtwoord op. Het wachtwoord moet ten minste tien tekens lang zijn en moet ten minste één cijfer, één hoofdletter en één kleine letter bevatten, één niet-alfanumeriek teken (met uitzondering van tekens " ). |
    |Ssh-gebruikersnaam|Geef de gebruikersnaam op, standaard is sshuser|
    |Ssh-wachtwoord|Geef het wachtwoord op.|

    ![Resource Manager-sjabloon HBase](./media/quickstart-resource-manager-template/resource-manager-template-hive.png)

1. Bekijk de **algemene voorwaarden**. Selecteer dan **ik ga akkoord met de hierboven vermelde algemene voorwaarden,** dan **Koop**. U ontvangt een melding dat uw implementatie aan de gang is. Het duurt ongeveer 20 minuten om een cluster te maken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Zodra het cluster is gemaakt, ontvangt u een **door implementatie geslaagde** melding met een **koppeling naar de bron ga.** Op de pagina Resourcegroep worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster heeft een [Azure Storage-account](../hdinsight-hadoop-use-blob-storage.md) of een Azure [Data Lake Storage-accountafhankelijkheid.](../hdinsight-hadoop-use-data-lake-store.md) Het wordt het standaardopslagaccount genoemd. Het HDInsight-cluster en het standaardopslagaccount moeten in dezelfde Azure-regio worden ondergebracht. Als u clusters verwijdert, wordt het opslagaccount niet verwijderd.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Navigeer in de Azure-portal naar uw cluster en selecteer **Verwijderen**.

![Resource Manager-sjabloon HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hive.png)

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de brongroep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een cluster voor interactieve query's in HDInsight maken met behulp van een resourcemanagersjabloon. In het volgende artikel leert u hoe u Apache Zeppelin gebruiken om Apache Hive-query's uit te voeren.

> [!div class="nextstepaction"]
> [Apache Hive-query's uitvoeren in Azure HDInsight met Apache Zeppelin](./hdinsight-connect-hive-zeppelin.md)
