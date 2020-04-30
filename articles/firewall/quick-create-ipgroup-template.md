---
title: 'Snelstartgids: een Azure Firewall en IP-groepen maken-Resource Manager-sjabloon'
description: Meer informatie over het gebruik van een resource manager-sjabloon om een Azure Firewall en IP-groepen te maken.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 53e5bf4f770ce986af2f3572bd6c1ef4cd9e3c2b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605236"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Snelstartgids: een Azure Firewall en IP-groepen maken-Resource Manager-sjabloon

In deze Quick Start gebruikt u een resource manager-sjabloon om een Azure Firewall te implementeren met voor beeld-IP-groepen die worden gebruikt in een netwerk regel en toepassings regel.

Een IP-groep is een resource op het hoogste niveau waarmee u IP-adressen, bereiken en subnetten kunt definiëren en groeperen in één object. Dit is handig voor het beheren van IP-adressen in Azure Firewall-regels. U kunt de IP-adressen hand matig invoeren of importeren uit een bestand.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-and-ip-groups"></a>Een Azure Firewall en IP-groepen maken

Met deze sjabloon maakt u een Azure Firewall en IP-groepen, samen met de benodigde resources ter ondersteuning van de Azure Firewall.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json) start-sjablonen

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Micro soft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
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

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. Typ of Selecteer in de portal op de pagina **een Azure firewall maken met IpGroups** de volgende waarden:
   - Abonnement: selecteren uit bestaande abonnementen 
   - Resource groep: Selecteer uit bestaande resource groepen of selecteer **nieuwe maken**en selecteer **OK**.
   - Locatie: Selecteer een locatie
   - Virtual Network naam: Typ een naam voor het nieuwe virtuele netwerk (VNet) 
   - IP-groeps naam 1: type naam voor IP-groep 1 
   - IP-groeps naam 2: type naam voor IP-groep 2 
   - Beheerder gebruikers naam: Typ gebruikers naam voor het gebruikers account van de beheerder 
   - Verificatie: Selecteer sshPublicKey of wacht woord 
   - Beheerders wachtwoord: Typ een beheerders wachtwoord of-sleutel

3. Selecteer **Ik ga akkoord met de bovenstaande voor waarden** en selecteer vervolgens **kopen**. Het volt ooien van de implementatie kan 10 minuten of langer duren.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Bekijk in de Azure Portal de geïmplementeerde resources, met name de firewall regels die gebruikmaken van IP-groepen.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP-groepen.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Netwerk regels.":::

Zie voor meer informatie over de JSON-syntaxis en eigenschappen voor een firewall in een sjabloon, [micro soft. Network azureFirewalls Temp late Reference](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de firewall hebt gemaakt niet meer nodig hebt, verwijdert u de resource groep. Hiermee verwijdert u de firewall en alle gerelateerde resources.

Als u de resource groep wilt verwijderen, `Remove-AzResourceGroup` roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure Portal](tutorial-hybrid-portal.md)