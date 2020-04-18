---
title: 'Snelstart: een Azure Firewall- en IP-groep maken - sjabloon Resourcebeheer'
description: Meer informatie over het gebruik van een Resource Manager-sjabloon om een Azure Firewall en IP-groepen te maken.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 53e5bf4f770ce986af2f3572bd6c1ef4cd9e3c2b
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605236"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Snelstart: een Azure Firewall- en IP-groep maken - sjabloon Resourcebeheer

In deze snelstart gebruikt u een resourcebeheersjabloon om een Azure Firewall te implementeren met voorbeeld-IP-groepen die worden gebruikt in een netwerkregel en toepassingsregel.

Een IP-groep is een bron op het hoogste niveau waarmee u IP-adressen, bereiken en subnetten definiëren en groeperen in één object. Dit is handig voor het beheren van IP-adressen in Azure Firewall-regels. U handmatig IP-adressen invoeren of importeren uit een bestand.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-azure-firewall-and-ip-groups"></a>Een Azure Firewall- en IP-groepen maken

Met deze sjabloon wordt een Azure Firewall en IP-groepen gemaakt, samen met de benodigde resources om de Azure Firewall te ondersteunen.

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

Meerdere Azure-bronnen worden gedefinieerd in de sjabloon:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Tabellen voor Microsoft.Network/route**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>De sjabloon implementeren

Resourcebeheersjabloon implementeren in Azure:

1. Selecteer **Implementeren naar Azure** om u aan te melden bij Azure en open de sjabloon. De sjabloon maakt een Azure Firewall, de netwerkinfrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. Typ of selecteer in de portal op de pagina **Een Azure Firewall maken met IpGroups** de volgende waarden:
   - Abonnement: Kiezen uit bestaande abonnementen 
   - Resourcegroep: Selecteer uit bestaande resourcegroepen of selecteer **Nieuw maken**en selecteer **OK**.
   - Locatie: een locatie selecteren
   - Virtuele netwerknaam: typ een naam voor het nieuwe virtuele netwerk (VNet) 
   - IP-groepsnaam 1: naam type voor IP-groep 1 
   - IP-groepsnaam 2: typenaam voor IP-groep 2 
   - Gebruikersnaam beheerder: gebruikersnaam typen voor het beheerdersgebruikersaccount 
   - Verificatie: SshPublicKey of wachtwoord selecteren 
   - Beheerderswachtwoord: typ een beheerderswachtwoord of -sleutel

3. Selecteer **Ik ga akkoord met de hierboven vermelde algemene voorwaarden** en selecteer Vervolgens **Kopen**. Het kan 10 minuten of langer duren voordat de implementatie is voltooid.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Controleer in de Azure-portal de geïmplementeerde resources, met name de firewallregels die IP-groepen gebruiken.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP-groepen.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Netwerkregels.":::

Zie [Microsoft.Network azureFirewalls-sjabloonverwijzing voor](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls)meer informatie over de syntaxis en eigenschappen van JSON voor een firewall in een sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de firewall hebt gemaakt, niet meer nodig hebt, verwijdert u de brongroep. Hiermee worden de firewall en alle bijbehorende bronnen verwijderd.

Als u de brongroep `Remove-AzResourceGroup` wilt verwijderen, roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal](tutorial-hybrid-portal.md)