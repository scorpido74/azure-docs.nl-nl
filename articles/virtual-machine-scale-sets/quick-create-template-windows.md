---
title: 'Snelstartgids: een schaalset voor virtuele Windows-machines maken met een Azure-sjabloon'
description: Leer hoe u snel een virtuele-machineschaalset in Windows maakt met behulp van een Azure Resource Manager-sjabloon waarmee een voorbeeld-app wordt geïmplementeerd en regels voor automatisch schalen worden geconfigureerd
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: f3dd2fd092e49a0e526856f1e55211ec1837cfa4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351082"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Snelstartgids: Een schaalset voor virtuele Windows-machines maken met een Azure-sjabloon

Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstartgids gebruikt u een Azure Resource Manager-sjabloon om een schaalset voor virtuele machines te maken en een voorbeeldtoepassing te implementeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="define-a-scale-set-in-a-template"></a>Een schaalset definiëren in een sjabloon
Met Azure Resource Manager-sjablonen kunt u groepen gerelateerde resources implementeren. Sjablonen zijn geschreven in JSON (JavaScript Object Notation). Deze definiëren de hele omgeving van de Azure-infrastructuur voor uw toepassing. In één sjabloon kunt u de virtuele-machineschaalset maken, toepassingen installeren en regels voor automatisch schalen configureren. Met behulp van variabelen en parameters kan deze sjabloon opnieuw worden gebruikt voor het bijwerken van bestaande schaalsets, of om extra schaalsets te maken. U kunt sjablonen implementeren via de Azure-portal, Azure CLI of Azure PowerShell of via CI/CD-pijplijnen (Continuous Integration/Continuous Delivery).

Zie [Overzicht van Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) voor meer informatie over sjablonen. Zie de sjabloonverwijzing [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets) voor de JSON-syntaxis en eigenschappen.

Met een sjabloon wordt de configuratie voor elk resourcetype gedefinieerd. Een resourcetype voor een virtuele-machineschaalset is vergelijkbaar met een afzonderlijke VM. De basisonderdelen van het resourcetype voor de virtuele-machineschaalset zijn:

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

 In het volgende voorbeeld ziet u de resourcedefinitie voor een basisschaalset. Als u een schaalsetsjabloon wilt aanpassen, kunt u de VM-grootte of initiële capaciteit wijzigen, of een ander platform of aangepaste afbeelding gebruiken.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 De virtuele NIC-configuratie (Network Interface Card) wordt niet weergegeven om het voorbeeld kort te houden. Extra onderdelen, zoals een load balancer, worden ook niet weergegeven. [Aan het eind van dit artikel](#deploy-the-template) wordt een volledige schaalsetsjabloon weergegeven.


## <a name="add-a-sample-application"></a>Voorbeeldtoepassing toevoegen
Als u de schaalset wilt testen, installeert u een eenvoudige webtoepassing. Wanneer u een schaalset implementeert, kunnen VM-extensies worden gebruikt voor configuratie- en automatiseringstaken na de implementatie, zoals het installeren van een app. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. Als u een extensie wilt toepassen op uw schaalset, voegt u de sectie *extensionProfile* toe aan het voorgaande resourcevoorbeeld. Met het extensieprofiel worden doorgaans de volgende eigenschappen gedefinieerd:

- Type extensie
- Uitgever van de extensie
- Versie van de extensie
- Locatie van de configuratie of installatiescripts
- Opdrachten om uit te voeren op de VM-instanties

Het voorbeeldsjabloon [ASP.NET-toepassing in Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) maakt gebruik van de PowerShell DSC-extensie om een ASP.NET MVC-app te installeren die wordt uitgevoerd in IIS. 

Er wordt een installatiescript gedownload uit GitHub, zoals gedefinieerd in *URL*. Met de extensie wordt vervolgens *InstallIIS* uitgevoerd vanuit het script *IISInstall.ps1*, zoals gedefinieerd in *Functie* en *Script*. De ASP.NET-app zelf wordt geleverd als een Web Deploy-pakket, dat ook kan worden gedownload uit GitHub, zoals gedefinieerd in *WebDeployPackagePath*:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>De sjabloon implementeren
U kunt de sjabloon [ASP.NET MVC -toepassing in Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) implementeren met de volgende knop **Implementeren in Azure**. Met deze knop wordt Azure Portal geopend, de volledige sjabloon geladen, en wordt u gevraagd om een aantal parameters op te geven, zoals een naam voor de schaalset, het aantal instanties en de beheerdersreferenties.

[![Sjabloon implementeren in Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

U kunt ook Azure PowerShell gebruiken om de ASP.NET-toepassing in Windows te installeren met [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Dit doet u als volgt:

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


## <a name="test-your-scale-set"></a>Uw schaalset testen
U kunt de schaalset in actie zien door in een webbrowser naar de voorbeeldwebtoepassing te gaan. Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) als volgt om het openbare IP-adres van uw load balancer op te vragen:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Geef het open bare IP-adres van de load balancer op in een webbrowser in de notatie *http:\//publicIpAddress/MyApp*. Via de load balancer wordt verkeer naar een van uw VM-instanties gedistribueerd, zoals wordt weergegeven in het volgende voorbeeld:

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
