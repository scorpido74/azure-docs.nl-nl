---
title: Snelstart - Een Windows-sjabloon voor virtuele machines maken met een Azure-sjabloon
description: Leer hoe u snel een virtuele-machineschaalset in Windows maakt met behulp van een Azure Resource Manager-sjabloon waarmee een voorbeeld-app wordt geïmplementeerd en regels voor automatisch schalen worden geconfigureerd
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 03/27/2020
ms.author: jushiman
ms.openlocfilehash: 030479a02b33a92c3917ba112d99c9bcef4f7f32
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010440"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Snelstartgids: Een schaalset voor virtuele Windows-machines maken met een Azure-sjabloon

Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstartgids gebruikt u een Azure Resource Manager-sjabloon om een schaalset voor virtuele machines te maken en een voorbeeldtoepassing te implementeren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="create-a-scale-set"></a>Een schaalset maken

Met Azure Resource Manager-sjablonen kunt u groepen gerelateerde resources implementeren. In één sjabloon kunt u de virtuele-machineschaalset maken, toepassingen installeren en regels voor automatisch schalen configureren. Met behulp van variabelen en parameters kan deze sjabloon opnieuw worden gebruikt voor het bijwerken van bestaande schaalsets, of om extra schaalsets te maken. U sjablonen implementeren via de Azure-portal, Azure CLI, Azure PowerShell of via CI/CD-pijplijnen voor continue integratie / continue levering.

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/)

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json" range="1-397" highlight="236-325":::

Deze bronnen worden gedefinieerd in deze sjablonen:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleInstellingen**](/azure/templates/microsoft.insights/autoscalesettings)

#### <a name="define-a-scale-set"></a>Een schaalset definiëren

Het gemarkeerde gedeelte is de definitie van de schaalsetresource. Als u een schaal wilt maken met behulp van een sjabloon, definieert u de juiste resources. De basisonderdelen van het resourcetype voor de virtuele-machineschaalset zijn:

| Eigenschap                     | Beschrijving van eigenschap                                  | Voorbeeldwaarde van sjabloon                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | Azure-resourcetype dat moet worden gemaakt                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | De naam van de schaalset                                       | myScaleSet                                |
| location                     | De locatie voor het maken van de schaalset                     | VS - oost                                   |
| sku.name                     | De VM-grootte voor elke instantie van de schaalset                  | Standard_A1                               |
| sku.capacity                 | Het aantal VM-instanties dat in het begin moet worden gemaakt           | 2                                         |
| upgradePolicy.mode           | Upgrademodus voor VM-instantie wanneer er wijzigingen optreden              | Automatisch                                 |
| imageReference               | Het platform of de aangepaste afbeelding die moet worden gebruikt voor de VM-instanties | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Het naamvoorvoegsel voor elke VM-instantie                     | myvmss                                    |
| osProfile.adminUsername      | De gebruikersnaam voor elke VM-instantie                        | azureuser                                 |
| osProfile.adminPassword      | Het wachtwoord voor elke VM-instantie                        | P@ssw0rd!                                 |

Als u een sjabloon voor een schaalset wilt aanpassen, u de vm-grootte of de initiële capaciteit wijzigen. Een andere optie is het gebruik van een ander platform of een aangepaste afbeelding.

#### <a name="add-a-sample-application"></a>Voorbeeldtoepassing toevoegen

Als u de schaalset wilt testen, installeert u een eenvoudige webtoepassing. Wanneer u een schaalset implementeert, kunnen VM-extensies worden gebruikt voor configuratie- en automatiseringstaken na de implementatie, zoals het installeren van een app. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. Als u een extensie wilt toepassen op uw schaalset, voegt u de sectie *extensionProfile* toe aan het voorgaande resourcevoorbeeld. Met het extensieprofiel worden doorgaans de volgende eigenschappen gedefinieerd:

- Type extensie
- Uitgever van de extensie
- Versie van de extensie
- Locatie van de configuratie of installatiescripts
- Opdrachten om uit te voeren op de VM-instanties

De sjabloon gebruikt de PowerShell DSC-extensie om een ASP.NET MVC-app te installeren die in IIS wordt uitgevoerd.

Er wordt een installatiescript gedownload uit GitHub, zoals gedefinieerd in *URL*. Met de extensie wordt vervolgens *InstallIIS* uitgevoerd vanuit het script *IISInstall.ps1*, zoals gedefinieerd in *Functie* en *Script*. De ASP.NET-app zelf wordt geleverd als een Web Deploy-pakket, dat ook kan worden gedownload vanuit GitHub, zoals gedefinieerd in *WebDeployPackagePath*:

## <a name="deploy-the-template"></a>De sjabloon implementeren

U de sjabloon implementeren door de knop **Implementeren naar Azure te** selecteren. Met deze knop wordt Azure Portal geopend, de volledige sjabloon geladen, en wordt u gevraagd om een aantal parameters op te geven, zoals een naam voor de schaalset, het aantal instanties en de beheerdersreferenties.

[![Sjabloon implementeren in Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

U ook een Resource Manager-sjabloon implementeren met Azure PowerShell:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Geef de waarden op voor de naam van de schaalset en de beheerdersreferenties voor de VM-instanties, wanneer u hierom wordt gevraagd. Het kan 10-15 minuten duren voordat de schaalset is gemaakt en de extensie is toegepast om de app te configureren.

## <a name="test-the-deployment"></a>De implementatie testen

U kunt de schaalset in actie zien door in een webbrowser naar de voorbeeldwebtoepassing te gaan. Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) als volgt om het openbare IP-adres van uw load balancer op te vragen:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Voer het openbare IP-adres van de load balancer in op een webbrowser in de indeling *http:\//publicIpAddress/MyApp*. Via de load balancer wordt verkeer naar een van uw VM-instanties gedistribueerd, zoals wordt weergegeven in het volgende voorbeeld:

![Actieve IIS-site](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, schaalset en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt. De parameter `-Force` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen. De parameter `-AsJob` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een Windows-schaalset gemaakt met behulp van een Azure-sjabloon en de PowerShell DSC-extensie gebruikt om een standaard ASP.NET-app te installeren op de VM-instanties. Als u meer wilt weten over schaalsets, gaat u verder met de zelfstudie voor het maken en beheren van schaalsets voor virtuele Azure-machines.

> [!div class="nextstepaction"]
> [Schaalsets voor virtuele Azure-machines maken en beheren](tutorial-create-and-manage-powershell.md)
