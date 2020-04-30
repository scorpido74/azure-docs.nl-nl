---
title: 'Snelstartgids: een interactief query cluster maken met behulp van sjabloon-Azure HDInsight'
description: In deze Quick start ziet u hoe u Resource Manager-sjabloon gebruikt voor het maken van een interactief query cluster in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 74b1c25c8bab11c0b2a72510fd419df239e2c23a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81603566"
---
# <a name="quickstart-create-interactive-query-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Quick Start: een interactief query cluster maken in azure HDInsight met behulp van Resource Manager-sjabloon

In deze Quick Start gebruikt u een Azure Resource Manager sjabloon voor het maken van een [interactief query](./apache-interactive-query-get-started.md) cluster in azure HDInsight. Interactieve Query's (ook wel Apache Hive LLAP of [analyse verwerking met lage latentie](https://cwiki.apache.org/confluence/display/Hive/LLAP)) is een Azure HDInsight- [cluster type](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-interactive-query-cluster"></a>Een interactief query cluster maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-interactive-hive)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-hdinsight-interactive-hive/azuredeploy.json" range="1-158":::


Er worden twee Azure-resources gedefinieerd in de sjabloon:

* [Micro soft. Storage/Storage accounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): een Azure Storage-account maken.
* [Micro soft. HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): een HDInsight-cluster maken.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de onderstaande knop **implementeren naar Azure** om u aan te melden bij Azure en de Resource Manager-sjabloon te openen.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

1. Typ of selecteer de volgende waarden:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer in de vervolg keuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster.|
    |Resourcegroep|Selecteer in de vervolg keuzelijst de bestaande resource groep of selecteer **nieuwe maken**.|
    |Locatie|De waarde wordt automatisch ingevuld met de locatie die wordt gebruikt voor de resource groep.|
    |Clusternaam|Voer een wereldwijd unieke naam in. Voor deze sjabloon gebruikt u alleen kleine letters en cijfers.|
    |Gebruikersnaam voor clusteraanmelding|Geef de gebruikers naam op. de standaard instelling is **admin**.|
    |Wachtwoord voor clusteraanmelding|Geef een wacht woord op. Het wacht woord moet uit minstens tien tekens bestaan en moet ten minste één cijfer, één hoofd letter en één kleine letters, één niet-alfanumeriek teken (behalve de tekens ' "') bevatten. |
    |SSH-gebruikers naam|Geef de gebruikers naam op. de standaard waarde is sshuser|
    |SSH-wacht woord|Geef het wacht woord op.|

    ![Resource Manager-sjabloon HBase](./media/quickstart-resource-manager-template/resource-manager-template-hive.png)

1. Bekijk de **voor waarden**. Selecteer **Ik ga akkoord met de bovenstaande voor waarden en**klik vervolgens op **kopen**. U ontvangt een melding dat uw implementatie wordt uitgevoerd. Het duurt ongeveer 20 minuten om een cluster te maken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Zodra het cluster is gemaakt, ontvangt u een melding over **implementatie geslaagd** met een koppeling **naar de resource** . Op de pagina Resourcegroep worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster heeft een [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) account of een afhankelijkheid van [Azure data Lake Storage-account](../hdinsight-hadoop-use-data-lake-store.md) . Dit wordt aangeduid als het standaard opslag account. Het HDInsight-cluster en het standaard opslag account moeten zich in dezelfde Azure-regio bevinden. Als u clusters verwijdert, wordt het opslag account niet verwijderd.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

Navigeer vanuit het Azure Portal naar uw cluster en selecteer **verwijderen**.

![Resource Manager-sjabloon HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hive.png)

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de resource groep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaard opslag account.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een interactief query cluster in HDInsight kunt maken met behulp van een resource manager-sjabloon. In het volgende artikel leert u hoe u Apache Zeppelin gebruikt om Apache Hive query's uit te voeren.

> [!div class="nextstepaction"]
> [Apache Hive query's uitvoeren in azure HDInsight met Apache Zeppelin](./hdinsight-connect-hive-zeppelin.md)
