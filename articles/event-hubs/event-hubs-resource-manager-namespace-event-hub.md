---
title: 'Snelstart: een gebeurtenishub maken met consumentengroep - Azure Event Hubs'
description: 'Snelstart: een naamruimte voor gebeurtenishubs maken met een gebeurtenishub en een consumentengroep met Azure Resource Manager-sjablonen'
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
ms.custom: subject-armqs
ms.openlocfilehash: c2221fe5b5ab38afbdde167e5bcbf6b47ed4f861
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79528078"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Snelstart: een gebeurtenishub maken met behulp van een Azure Resource Manager-sjabloon

Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs. In deze quickstart maakt u een gebeurtenishub met behulp van een [Azure Resource Manager-sjabloon](../azure-resource-manager/management/overview.md). U implementeert een Azure Resource Manager-sjabloon om een naamruimte van het type [Gebeurtenishubs](event-hubs-what-is-event-hubs.md)te maken met één gebeurtenishub.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="create-an-event-hub"></a>Een Event Hub maken

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/)

:::code language="json" source="~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json" range="1-61" highlight="32-59":::

De resources die in de sjabloon zijn gedefinieerd, zijn:

- [**Microsoft.EventHub/naamruimten**](/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft.EventHub/naamruimten/eventhubs**](/azure/templates/microsoft.eventhub/namespaces/eventhubs)

Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular)voor meer sjabloonvoorbeelden .

### <a name="deploy-the-template"></a>De sjabloon implementeren

Ga als u de sjabloon inzet:

1. Selecteer **Probeer het** in het volgende codeblok en volg de instructies om u aan te melden bij de Azure Cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Het duurt even om een gebeurtenishub te maken.

1. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
1. Klik met de rechtermuisknop op de shell console en selecteer **Plakken**.

## <a name="verify-the-deployment"></a>De implementatie controleren

Als u de implementatie wilt verifiëren, u de brongroep openen vanuit de [Azure-portal](https://portal.azure.com)of het volgende Azure PowerShell-script gebruiken.  Als de Cloud Shell nog steeds open is, hoeft u de eerste regel (Read-Host) niet te kopiëren/uitvoeren.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. Als de Cloud Shell nog steeds open is, hoeft u de eerste regel (Read-Host) niet te kopiëren/uitvoeren.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een naamruimte voor gebeurtenishubs en een gebeurtenishub in de naamruimte gemaakt. Zie de zelfstudies voor het verzenden en ontvangen van evenementen voor stapsgewijze instructies om gebeurtenissen naar (of) ontvangen van gebeurtenissen van een gebeurtenishub **te verzenden en te ontvangen:**

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-java-send-v2.md)
- [OK](event-hubs-go-get-started-send.md)
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
