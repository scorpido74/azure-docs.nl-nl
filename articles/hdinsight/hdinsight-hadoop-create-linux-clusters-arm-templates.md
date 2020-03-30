---
title: Apache Hadoop-clusters maken met behulp van sjablonen - Azure HDInsight
description: Meer informatie over het maken van clusters voor HDInsight met behulp van Resource Manager-sjablonen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 9498f2cf56f0bfe20d0806e5dc9872403dabb180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979095"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Apache Hadoop-clusters maken in HDInsight met behulp van Resource Manager-sjablonen

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In dit artikel leert u verschillende manieren om Azure HDInsight-clusters te maken met Azure Resource Manager-sjablonen. Zie [Een toepassing implementeren met azure resource manager-sjabloon voor](../azure-resource-manager/templates/deploy-powershell.md)meer informatie. Als u meer wilt weten over andere hulpmiddelen en functies voor het maken van een cluster, klikt u op de tabkiezer boven aan deze pagina of ziet u [methoden voor het maken van clusteren](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Vereisten

* Een [Azure-abonnement](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell en/of Azure CLI.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Met een sjabloon Resourcemanager u eenvoudig de volgende bronnen voor uw toepassing maken in één gecoördineerde bewerking:
* HDInsight-clusters en hun afhankelijke resources (zoals het standaardopslagaccount).
* Andere bronnen (zoals Azure SQL Database om [Apache Sqoop](https://sqoop.apache.org/)te gebruiken).

In de sjabloon definieert u de resources die nodig zijn voor de toepassing. U geeft ook implementatieparameters op voor invoerwaarden voor verschillende omgevingen. De sjabloon bestaat uit JSON en expressies die u gebruikt om waarden voor uw implementatie te construeren.

U HDInsight-sjabloonvoorbeelden vinden op [Azure quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/?term=hdinsight) Gebruik visual [studiocode](https://code.visualstudio.com/#alt-downloads) met cross-platform met de [extensie ResourceManager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) of een teksteditor om de sjabloon op te slaan in een bestand op uw werkstation.

Zie de volgende artikelen en voorbeelden voor meer informatie over resourcemanagersjablonen:

* [Azure Resource Manager-sjablonen maken](../azure-resource-manager/templates/template-syntax.md)
* [Een toepassing implementeren met Azure Resource Manager-sjablonen](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.HDInsight/clusterssjabloonverwijzing](/azure/templates/microsoft.hdinsight/allversions)
* [Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Sjablonen genereren

Met Resource Manager u een resourcemanagersjabloon exporteren uit bestaande bronnen in uw abonnement met behulp van verschillende hulpprogramma's. Deze gegenereerde sjabloon kunt u vervolgens gebruiken om meer te weten te komen over de sjabloonsyntaxis of om desgewenst het hergebruik van uw oplossing te automatiseren. Zie [Sjablonen exporteren](../azure-resource-manager/templates/export-template-portal.md)voor meer informatie .

## <a name="deploy-using-the-portal"></a>Implementeren via de portal

U een Resource Manager-sjabloon implementeren met behulp van de Azure-portal. Zie Resources [implementeren van aangepaste sjabloon voor](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)meer informatie.

## <a name="deploy-using-powershell"></a>Implementeren met PowerShell

U een Resource Manager-sjabloon implementeren met Azure PowerShell. Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell en](../azure-resource-manager/templates/deploy-powershell.md) Sjabloon [Privéresourcebeheer implementeren met SAS-token en Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md)voor meer informatie.

## <a name="deploy-using-azure-cli"></a>Implementeren met behulp van Azure CLI

U een Resource Manager-sjabloon implementeren met Azure CLI. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../azure-resource-manager/templates/deploy-cli.md) en Sjabloon [Privéresourcebeheer implementeren met SAS-token en Azure CLI](../azure-resource-manager/resource-manager-cli-sas-token.md)voor meer informatie.

## <a name="deploy-using-the-rest-api"></a>Implementeren met behulp van de REST API

U een Resource Manager-sjabloon implementeren met rest-API. Zie [Resources met Resource Manager-sjablonen en Resource Manager REST API implementeren](../azure-resource-manager/templates/deploy-rest.md)voor meer informatie.

## <a name="deploy-with-visual-studio"></a>Implementeren met Visual Studio

 Gebruik Visual Studio om een resourcegroepproject te maken en te implementeren in Azure via de gebruikersinterface. U selecteert het type resources dat u in uw project wilt opnemen. Deze resources worden automatisch toegevoegd aan de sjabloon Resourcemanager. Het project biedt ook een PowerShell-script om de sjabloon te implementeren.

Zie [Azure-brongroepen maken en implementeren via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)voor een inleiding tot het gebruik van Visual Studio met resourcegroepen.

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u verschillende manieren geleerd om een HDInsight-cluster te maken. Zie voor meer informatie de volgende artikelen:

* Zie [Azure quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?term=hdinsight)voor meer HDInsight-gerelateerde sjablonen .
* Zie [Resources implementeren met .NET-bibliotheken en een sjabloon](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor een voorbeeld van het implementeren van resources via de .NET-clientbibliotheek.
* Zie [Microservices inrichten en implementeren voorspelbaar in Azure](../app-service/deploy-complex-application-predictably.md)voor een diepgaand voorbeeld van het implementeren van een toepassing.
* Zie voor instructies over het implementeren van uw oplossing in verschillende omgevingen [Ontwikkeling en testomgevingen in Microsoft Azure](../solution-dev-test-environments.md).
* Zie [Sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over de secties van de sjabloon Azure Resource Manager.
* Zie [Sjabloonfuncties](../azure-resource-manager/templates/template-functions.md)voor een lijst met de functies die u gebruiken in een sjabloon voor Azure Resource Manager.
