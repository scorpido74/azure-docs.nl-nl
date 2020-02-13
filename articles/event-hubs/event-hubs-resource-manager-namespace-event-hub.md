---
title: 'Snelstartgids: een Event Hub maken met een consumenten groep-Azure Event Hubs'
description: 'Snelstartgids: een Event Hubs naam ruimte met een Event Hub en een consument groep maken met behulp van Azure Resource Manager sjablonen'
services: event-hubs
documentationcenter: .net
author: spelluru
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 88cd29af75239f0ad79eb78b5ff8e106c3b2ee56
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163072"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Snelstartgids: een Event Hub maken met behulp van een Azure Resource Manager sjabloon

Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze Quick Start maakt u een Event Hub met behulp van een [Azure Resource Manager sjabloon](../azure-resource-manager/management/overview.md). U implementeert een Azure Resource Manager sjabloon om een naam ruimte van het type [Event hubs](event-hubs-what-is-event-hubs.md)te maken met één event hub. Het artikel wordt beschreven hoe om te definiëren welke resources worden geïmplementeerd en over het definiëren van parameters die zijn opgegeven wanneer de implementatie wordt uitgevoerd. U kunt deze sjabloon gebruiken voor uw eigen implementaties of de sjabloon aanpassen aan uw eisen. Zie [Azure Resource Manager sjablonen ontwerpen][Authoring Azure Resource Manager templates]voor meer informatie over het maken van sjablonen. Zie [Microsoft.EventHub resource types](/azure/templates/microsoft.eventhub/allversions) (Microsoft.EventHub-resourcetypen) voor de JSON-syntaxis en eigenschappen die u kunt gebruiken in een sjabloon.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-event-hub"></a>Een Event Hub maken

In deze Snelstartgids gebruikt u een [bestaande Quick](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)start-sjabloon:

[!code-json[create-azure-event-hub-namespace](~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)]

Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular)start-sjablonen voor meer voor beelden van sjablonen.

De sjabloon implementeren:

1. Selecteer **Probeer het** uit het volgende code blok en volg de instructies om u aan te melden bij de Azure Cloud shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Het duurt enkele minuten om een Event Hub te maken.

1. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
1. Klik met de rechter muisknop op de shell-console en selecteer vervolgens **Plakken**.

## <a name="verify-the-deployment"></a>De implementatie controleren

Als u de implementatie wilt controleren, kunt u de resource groep openen vanuit het [Azure Portal](https://portal.azure.com)of het volgende Azure PowerShell script gebruiken.  Als de Cloud shell nog steeds geopend is, hoeft u de eerste regel niet te kopiëren/uit te voeren (Read-host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. Als de Cloud shell nog steeds geopend is, hoeft u de eerste regel niet te kopiëren/uit te voeren (Read-host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Event Hubs naam ruimte gemaakt en een Event Hub in de naam ruimte. Voor stapsgewijze instructies voor het verzenden van gebeurtenissen naar (of) het ontvangen van gebeurtenissen van een Event Hub, raadpleegt u de zelf studies voor het **verzenden en ontvangen van gebeurtenissen** :

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (alleen verzenden)](event-hubs-c-getstarted-send.md)
- [Apache Storm (alleen ontvangen)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
