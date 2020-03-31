---
title: Een azure servicebusnaamruimte maken met sjabloon
description: Sjabloon Azure Resource Manager gebruiken om een naamruimte voor Service Bus Messaging te maken
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264472"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Een naamruimte voor servicebus maken met behulp van een azure resource manager-sjabloon

Meer informatie over het implementeren van een Azure Resource Manager-sjabloon om een naamruimte voor servicebus te maken. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen. Zie [Azure Resource Manager-documentatie](/azure/azure-resource-manager/)voor meer informatie over het maken van sjablonen.

De volgende sjablonen zijn ook beschikbaar voor het maken van naamruimten voor servicebussen:

* [Een naamruimte voor servicebus maken met wachtrij](./service-bus-resource-manager-namespace-queue.md)
* [Een naamruimte voor servicebus maken met onderwerp en abonnement](./service-bus-resource-manager-namespace-topic.md)
* [Een naamruimte voor servicebus maken met wachtrij- en autorisatieregel](./service-bus-resource-manager-namespace-auth-rule.md)
* [Een naamruimte voor servicebus maken met onderwerp, abonnement en regel](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-service-bus-namespace"></a>Een naamruimte voor servicebussen maken

In deze snelstart gebruikt u een [bestaande resourcemanagersjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) van [Azure Quickstart-sjablonen:](https://azure.microsoft.com/resources/templates/)

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)voor meer sjabloonvoorbeelden .

Ga als u een naamruimte voor servicebussen maken door een sjabloon te implementeren:

1. Selecteer **Probeer het** in het volgende codeblok en volg de instructies om u aan te melden bij de Azure Cloud-shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    De naam van de resourcegroep is de naamruimtenaam servicebus met **rg** toegevoegd.

2. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
3. Klik met de rechtermuisknop op de shell console en selecteer **Plakken**.

Het duurt even om een gebeurtenishub te maken.

## <a name="verify-the-deployment"></a>De implementatie controleren

Als u de naamruimte van de geïmplementeerde servicebus wilt bekijken, u de brongroep openen vanuit de Azure-portal of het volgende Azure PowerShell-script gebruiken. Als de cloudshell nog steeds geopend is, hoeft u de eerste en tweede regels van het volgende script niet te kopiëren/uitvoeren.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell wordt gebruikt om de sjabloon in deze zelfstudie te implementeren. Zie voor andere methoden voor het implementeren van sjablonen:

* [Door gebruik te maken van de Azure-portal](../azure-resource-manager/templates/deploy-portal.md).
* [Door Azure CLI te gebruiken](../azure-resource-manager/templates/deploy-cli.md).
* [Door rest API te gebruiken.](../azure-resource-manager/templates/deploy-rest.md)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. Als de cloudshell nog steeds geopend is, hoeft u de eerste en tweede regels van het volgende script niet te kopiëren/uitvoeren.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een naamruimte voor servicebus gemaakt. Bekijk de andere snelstarts voor meer informatie over het maken van wachtrijen, onderwerpen/abonnementen en het gebruik ervan:

* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Aan de slag met Service Bus-onderwerpen](service-bus-dotnet-how-to-use-topics-subscriptions.md)
