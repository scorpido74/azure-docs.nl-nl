---
title: 'Quickstart: Een load balancer maken - Azure-sjabloon'
titleSuffix: Azure Load Balancer
description: In deze quickstart leest u hoe u een load balancer kunt maken met de Azure Resource Manager-sjabloon.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: ebf2f926f5be86ffee5f3a3e30277962a6060762
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85479757"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Quickstart: Een load balancer maken om taken van VM's te verdelen met behulp van een ARM-sjabloon

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines (VM's). In deze quickstart leest u hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) kunt implementeren waarmee een standaard load balancer wordt gemaakt om de taken van VM's te verdelen. Als u een ARM-sjabloon gebruikt, hoeft u minder stappen te voltooien in vergelijking met andere implementatiemethoden.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-load-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-load-balancer-standard-create/).

SKU's voor load balancer en openbare IP moeten overeenkomen. Wanneer u een Standard Load Balancer maakt, moet u ook een nieuw, standaard, openbaar IP-adres maken dat als de front-end wordt geconfigureerd voor de Standard Load Balancer. Als u een Basic Load Balancer wilt maken, gebruikt u [deze sjabloon](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Microsoft raadt aan om Standard SKU te gebruiken voor productiewerkbelastingen.

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="57-122":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): voor de load balancer en voor elk van de drie virtuele machines.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 hiervan).
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 hiervan).
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 hiervan): gebruik dit om de IIS en de webpagina's te configureren.

Zie [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular) als u meer sjablonen wilt vinden die gerelateerd zijn aan Azure Load Balancer.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **Proberen** in het volgende codeblok om Azure Cloud Shell te openen en volg de instructies om u aan te melden bij Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Wacht totdat u de prompt van de console ziet.

1. Selecteer **Kopiëren** in het vorige codeblok om het PowerShell-script te kopiëren.

1. Klik met de rechtermuisknop op het shell-consoledeelvenster en selecteer **Plakken**.

1. Voer de waarden in.

   Met de sjabloonimplementatie worden drie beschikbaarheidszones gemaakt. Beschikbaarheidszones worden alleen ondersteund in [bepaalde regio's](../availability-zones/az-overview.md). Gebruik een van de ondersteunde regio's. Als u twijfelt, voert u **centralus** in.

   De naam van de resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd. U hebt de naam van de resourcegroep nodig in de volgende sectie.

Het duurt ongeveer 10 minuten om de sjabloon te implementeren. Wanneer voltooid is de uitvoer vergelijkbaar met:

![Uitvoer van Azure Standard Load Balancer Resource Manager-sjabloon PowerShell-implementatie](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. Naast Azure PowerShell kunt u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De naam van de standaard resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

1. Selecteer de load balancer. De standaardnaam is de naam van het project met **-lb** eraan toegevoegd.

1. Kopieer alleen het IP-adresgedeelte van het openbare IP-adres en plak het in de adresbalk van de browser.

   ![Azure Standard Load Balancer Resource Manager-sjabloon openbaar IP](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    In de browser wordt de standaardpagina van de Internet Information Services-webserver (IIS) weergegeven.

   ![IIS-webserver](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Als u wilt zien hoe de load balancer verkeer distribueert naar alle drie de VM's, kunt u vernieuwing van uw webbrowser afdwingen op de clientcomputer.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de load balancer en alle gerelateerde resources verwijderen wanneer u deze niet meer nodig hebt. Hiervoor gaat u naar de Azure-portal, selecteert u de resourcegroep die de load balancer bevat en selecteert u vervolgens **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Standard load balancer gemaakt, VM's daaraan gekoppeld, een regel voor het load balancer-verkeer geconfigureerd, een statustest gemaakt en vervolgens de load balancer getest.

Voor meer informatie gaat u door naar de zelfstudies voor load balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
