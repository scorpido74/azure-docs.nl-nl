---
title: 'Snelstartgids: een Azure Firewall met meerdere open bare IP-adressen maken-Resource Manager-sjabloon'
description: Meer informatie over het gebruik van een resource manager-sjabloon voor het maken van een Azure Firewall met meerdere open bare IP-adressen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 3d58173d239e7a9249b588ff038ea46cfedb27a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605211"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Snelstartgids: een Azure Firewall met meerdere open bare IP-adressen maken-Resource Manager-sjabloon

In deze Quick Start gebruikt u een resource manager-sjabloon om een Azure Firewall te implementeren met meerdere open bare IP-adressen.

De geïmplementeerde firewall heeft regels voor het verzamelen van NAT-regels waarmee RDP-verbindingen met twee virtuele Windows Server 2019-machines worden toegestaan.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zie [een Azure Firewall met meerdere open bare IP-adressen implementeren met behulp van Azure PowerShell](deploy-multi-public-ip-powershell.md)voor meer informatie over Azure Firewall met meerdere open bare IP-adressen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Een Azure Firewall maken

Met deze sjabloon maakt u een Azure Firewall met twee open bare IP-adressen, samen met de benodigde resources ter ondersteuning van de Azure Firewall.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json) start-sjablonen

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Micro soft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Micro soft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Micro soft. Storage/Storage accounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Micro soft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Micro soft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>De sjabloon implementeren

Resource Manager-sjabloon implementeren in Azure:

1. Selecteer **implementeren naar Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u een Azure Firewall, de netwerk infrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. In de portal, op de pagina **een Azure firewall maken met meerdere IP-adressen** , typt of selecteert u de volgende waarden:
   - Abonnement: selecteren uit bestaande abonnementen 
   - Resource groep: Selecteer uit bestaande resource groepen of selecteer **nieuwe maken**en selecteer **OK**.
   - Locatie: Selecteer een locatie
   - Beheerder gebruikers naam: Typ gebruikers naam voor het gebruikers account van de beheerder 
   - Beheerders wachtwoord: Typ een beheerders wachtwoord of-sleutel

3. Selecteer **Ik ga akkoord met de bovenstaande voor waarden** en selecteer vervolgens **kopen**. Het volt ooien van de implementatie kan 10 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

Controleer de geïmplementeerde resources in het Azure Portal. Noteer de open bare IP-adressen van de firewall.  

Gebruik Verbinding met extern bureaublad om verbinding te maken met de open bare IP-adressen van de firewall. Geslaagde verbindingen demonstreert firewall-NAT-regels die de verbinding met de back-endservers toestaan.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de firewall hebt gemaakt niet meer nodig hebt, verwijdert u de resource groep. Hiermee verwijdert u de firewall en alle gerelateerde resources.

Als u de resource groep wilt verwijderen, `Remove-AzResourceGroup` roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure Portal](tutorial-hybrid-portal.md)