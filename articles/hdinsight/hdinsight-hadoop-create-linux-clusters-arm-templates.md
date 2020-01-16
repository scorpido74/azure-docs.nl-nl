---
title: Apache Hadoop clusters maken met behulp van sjablonen-Azure HDInsight
description: Meer informatie over het maken van clusters voor HDInsight met behulp van Resource Manager-sjablonen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 9498f2cf56f0bfe20d0806e5dc9872403dabb180
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979095"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Apache Hadoop clusters maken in HDInsight met behulp van Resource Manager-sjablonen

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In dit artikel leert u verschillende manieren om Azure HDInsight-clusters te maken met behulp van Azure Resource Manager-sjablonen. Zie [een toepassing implementeren met Azure Resource Manager sjabloon](../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie. Als u meer wilt weten over andere hulpprogram ma's en functies voor het maken van het cluster, klikt u op de tab-kiezer boven aan deze pagina of ziet u de methoden voor het maken van een [cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Vereisten

* Een [Azure-abonnement](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell en/of Azure CLI.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Met een resource manager-sjabloon kunt u eenvoudig de volgende resources voor uw toepassing maken in een enkele, gecoördineerde bewerking:
* HDInsight-clusters en hun afhankelijke bronnen (zoals het standaard opslag account).
* Andere resources (zoals Azure SQL Database om [Apache Sqoop](https://sqoop.apache.org/)te gebruiken).

In de sjabloon definieert u de resources die nodig zijn voor de toepassing. U geeft ook implementatie parameters op voor invoer waarden voor verschillende omgevingen. De sjabloon bestaat uit JSON en expressies die u gebruikt om waarden voor uw implementatie samen te stellen.

U kunt voor beelden van HDInsight-sjablonen vinden in [Azure Quick](https://azure.microsoft.com/resources/templates/?term=hdinsight)start-sjablonen. Gebruik platformoverschrijdende [Visual Studio-code](https://code.visualstudio.com/#alt-downloads) met de [Resource Manager-extensie](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) of een tekst editor om de sjabloon in een bestand op uw werk station op te slaan.

Voor meer informatie over Resource Manager-sjablonen raadpleegt u de volgende artikelen en voor beelden:

* [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)
* [Een toepassing implementeren met Azure Resource Manager sjablonen](../azure-resource-manager/templates/deploy-powershell.md)
* Referentie voor sjablonen voor [micro soft. HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Sjablonen voor Azure Quick Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Sjablonen genereren

Met Resource Manager kunt u een resource manager-sjabloon uit bestaande resources in uw abonnement exporteren met behulp van verschillende hulpprogram ma's. Deze gegenereerde sjabloon kunt u vervolgens gebruiken om meer te weten te komen over de sjabloonsyntaxis of om desgewenst het hergebruik van uw oplossing te automatiseren. Zie [sjablonen exporteren](../azure-resource-manager/templates/export-template-portal.md)voor meer informatie.

## <a name="deploy-using-the-portal"></a>Implementeren met behulp van de portal

U kunt een resource manager-sjabloon implementeren met behulp van de Azure Portal. Zie [resources implementeren vanuit een aangepaste sjabloon](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)voor meer informatie.

## <a name="deploy-using-powershell"></a>Implementeren met PowerShell

U kunt een resource manager-sjabloon implementeren met behulp van Azure PowerShell. Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) en een [privé Resource Manager-sjabloon met SAS-token en Azure PowerShell implementeren](../azure-resource-manager/resource-manager-powershell-sas-token.md)voor meer informatie.

## <a name="deploy-using-azure-cli"></a>Implementeren met behulp van Azure CLI

U kunt een resource manager-sjabloon implementeren met behulp van Azure CLI. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../azure-resource-manager/templates/deploy-cli.md) en een [persoonlijke Resource Manager-sjabloon implementeren met SAS-token en Azure cli](../azure-resource-manager/resource-manager-cli-sas-token.md)voor meer informatie.

## <a name="deploy-using-the-rest-api"></a>Implementeren met behulp van de REST API

U kunt een resource manager-sjabloon implementeren met behulp van REST API. Zie [resources implementeren met Resource Manager-sjablonen en resource manager rest API](../azure-resource-manager/templates/deploy-rest.md)voor meer informatie.

## <a name="deploy-with-visual-studio"></a>Implementeren met Visual Studio

 Gebruik Visual Studio om een resource groeps project te maken en te implementeren in azure via de gebruikers interface. U selecteert het type resources dat u wilt toevoegen aan uw project. Deze resources worden automatisch toegevoegd aan de Resource Manager-sjabloon. Het project biedt ook een Power shell-script voor het implementeren van de sjabloon.

Zie voor een inleiding tot het gebruik van Visual Studio met resource groepen [Azure-resource groepen maken en implementeren via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u verschillende manieren geleerd om een HDInsight-cluster te maken. Raadpleeg de volgende artikelen voor meer informatie:

* Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?term=hdinsight)start-sjablonen voor meer aan HDInsight gerelateerde sjablonen.
* Zie [resources implementeren met behulp van .net-bibliotheken en een sjabloon](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor een voor beeld van het implementeren van resources via de .net-client bibliotheek.
* Zie voor een uitgebreid voor beeld van de implementatie van een toepassing micro [services zoals verwacht inrichten en implementeren in azure](../app-service/deploy-complex-application-predictably.md).
* Zie voor instructies over het implementeren van uw oplossing in verschillende omgevingen [Ontwikkeling en testomgevingen in Microsoft Azure](../solution-dev-test-environments.md).
* Zie [ontwerp sjablonen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over de secties van de sjabloon Azure Resource Manager.
* Zie [sjabloon functies](../azure-resource-manager/templates/template-functions.md)voor een overzicht van de functies die u kunt gebruiken in een Azure Resource Manager sjabloon.
