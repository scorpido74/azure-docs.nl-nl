---
title: 'Snelstartgids: een Azure Firewall maken met Beschikbaarheidszones-Resource Manager-sjabloon'
description: Azure Firewall implementeren met behulp van een sjabloon. Het virtuele netwerk heeft één VNet met drie subnetten. Er zijn twee virtuele Windows Server-machines geïmplementeerd. een Jump box en een server.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 17ab693033b61c25ba2f5b5bd588ef52caf8c046
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597702"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>Snelstartgids: Azure Firewall implementeren met Beschikbaarheidszones-Resource Manager-sjabloon

In deze Quick Start gebruikt u een resource manager-sjabloon voor het implementeren van een Azure Firewall in drie Beschikbaarheidszones. 

Met de sjabloon wordt een test netwerk omgeving gemaakt met een firewall. Het netwerk heeft één virtueel netwerk (VNet) met drie subnetten: *AzureFirewallSubnet*, *ServersSubnet*en *JumpboxSubnet*. Het subnet *ServersSubnet* en *JumpboxSubnet* heeft één virtuele Windows Server-machine met twee kernen.

De firewall bevindt zich in het *AzureFirewallSubnet* -subnet en heeft een verzameling van toepassings regels met één regel waarmee `www.microsoft.com`toegang kan worden toegestaan.

Een door de gebruiker gedefinieerde route wijst netwerk verkeer van het *ServersSubnet* -subnet via de firewall, waarbij de firewall regels worden toegepast.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zie [Azure firewall implementeren en configureren met behulp van de Azure Portal](tutorial-firewall-deploy-portal.md)voor meer informatie over Azure firewall.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-with-availability-zones"></a>Een Azure Firewall met Beschikbaarheidszones maken

Met deze sjabloon maakt u een Azure Firewall met Beschikbaarheidszones, samen met de benodigde resources ter ondersteuning van de Azure Firewall.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-with-zones-sandbox/azuredeploy.json)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

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

De Resource Manager-sjabloon implementeren in Azure:

1. Selecteer **implementeren naar Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u een Azure Firewall, de netwerk infrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. In de portal, op de pagina **een sandbox instellen van Azure Firewall met zones** , typt of selecteert u de volgende waarden:
   - **Resource groep**: Selecteer **nieuwe maken**, typ een naam voor de resource groep en selecteer **OK**. 
   - **Virtual Network naam**: Typ een naam voor het nieuwe VNet. 
   - **Beheerder gebruikers naam**: Typ een gebruikers naam voor het gebruikers account van de beheerder.
   - **Beheerders wachtwoord**: Typ een beheerders wachtwoord. 

3. Lees de voor waarden en selecteer **Ik ga akkoord met de bovenstaande voor waarden** en selecteer vervolgens **kopen**. Het volt ooien van de implementatie kan 10 minuten of langer duren.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Verken de resources die zijn gemaakt met de firewall.

Zie [micro soft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)voor meer informatie over de JSON-syntaxis en eigenschappen voor een firewall in een sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de resource groep, de firewall en alle gerelateerde bronnen verwijderen door de `Remove-AzResourceGroup` Power shell-opdracht uit te voeren. Als u een resource groep met de naam *MyResourceGroup*wilt verwijderen, voert u de volgende handelingen uit: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Verwijder de resource groep en de firewall niet als u van plan bent om door te gaan naar de zelf studie over Firewall bewaking. 

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u de Azure Firewall-logboeken bewaken:

[Zelfstudie: Azure Firewall-logboeken bewaken](./tutorial-diagnostics.md)
