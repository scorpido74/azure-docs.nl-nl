---
title: 'Snelstart: ML Services-cluster maken met sjabloon - Azure HDInsight'
description: In deze snelstart wordt weergegeven hoe u resourcebeheersjabloon gebruiken om een ML Services-cluster in Azure HDInsight te maken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: cde8d6932400966ae22720b1e86f3c5164f25b30
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603433"
---
# <a name="quickstart-create-ml-services-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snelstart: ML Services-cluster maken in Azure HDInsight met behulp van resourcebeheersjabloon

In deze snelstart gebruikt u een Azure Resource Manager-sjabloon om een [ML Services-cluster](./r-server-overview.md) in Azure HDInsight te maken. Microsoft Machine Learning Server is beschikbaar als implementatieoptie wanneer u HDInsight-clusters maakt in Azure. Het clustertype dat deze optie biedt, heet ML Services. Deze mogelijkheid biedt data scientists, statistici en R-programmeurs on-demand toegang tot schaalbare, gedistribueerde analysemethoden op HDInsight.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-ml-services-cluster"></a>Een ML Services-cluster maken

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-rserver)

:::code language="json" source="~/quickstart-templates/101-hdinsight-rserver/azuredeploy.json" range="1-171":::


In de sjabloon worden twee Azure-resources gedefinieerd:

* [Microsoft.Storage/storageAccounts:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)maak een Azure Storage Account.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): maak een HDInsight-cluster.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de knop **Implementeren naar Azure** hieronder om u aan te melden bij Azure en open de sjabloon Resourcebeheer.

    [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

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

    ![Resource Manager-sjabloon HBase](./media/quickstart-resource-manager-template/resource-manager-template-rserver.png)

1. Bekijk de **algemene voorwaarden**. Selecteer dan **ik ga akkoord met de hierboven vermelde algemene voorwaarden,** dan **Koop**. U ontvangt een melding dat uw implementatie aan de gang is. Het duurt ongeveer 20 minuten om een cluster te maken.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Zodra het cluster is gemaakt, ontvangt u een **door implementatie geslaagde** melding met een **koppeling naar de bron ga.** Op de pagina Resourcegroep worden uw nieuwe HDInsight-cluster en de standaardopslag bij het cluster weergegeven. Elk cluster heeft een [Azure Storage-account](../hdinsight-hadoop-use-blob-storage.md) of een Azure [Data Lake Storage-accountafhankelijkheid.](../hdinsight-hadoop-use-data-lake-store.md) Het wordt het standaardopslagaccount genoemd. Het HDInsight-cluster en het standaardopslagaccount moeten in dezelfde Azure-regio worden ondergebracht. Als u clusters verwijdert, wordt het opslagaccount niet verwijderd.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Navigeer in de Azure-portal naar uw cluster en selecteer **Verwijderen**.

![Resource Manager-sjabloon HBase](./media/quickstart-resource-manager-template/azure-portal-delete-rserver.png)

U kunt ook de naam van de resourcegroep selecteren om de pagina van de resourcegroep te openen en vervolgens **Resourcegroep verwijderen** selecteren. Door de brongroep te verwijderen, verwijdert u zowel het HDInsight-cluster als het standaardopslagaccount.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een ML Services-cluster in HDInsight maakt met behulp van een Resource Manager-sjabloon. In het volgende artikel leert u hoe u een R-script met RStudio Server uitvoert dat aantoont dat Spark wordt gebruikt voor gedistribueerde R-berekeningen..

> [!div class="nextstepaction"]
> [Een R-script uitvoeren op een ML Services-cluster in Azure HDInsight met RStudio Server](./machine-learning-services-quickstart-job-rstudio.md)
