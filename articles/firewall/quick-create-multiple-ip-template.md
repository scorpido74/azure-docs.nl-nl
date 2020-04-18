---
title: 'Snelstart: een Azure Firewall maken met meerdere openbare IP-adressen - Sjabloon Resourcebeheer'
description: Meer informatie over het gebruik van een Resource Manager-sjabloon om een Azure Firewall te maken met meerdere openbare IP-adressen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 3d58173d239e7a9249b588ff038ea46cfedb27a3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605211"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Snelstart: een Azure Firewall maken met meerdere openbare IP-adressen - Sjabloon Resourcebeheer

In deze quickstart gebruikt u een resourcebeheersjabloon om een Azure Firewall met meerdere openbare IP-adressen te implementeren.

De geïmplementeerde firewall heeft NAT-regels voor het verzamelen van regels waarmee RDP-verbindingen met twee virtuele Windows Server 2019-machines mogelijk zijn.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zie Een Azure Firewall implementeren met meerdere [openbare IP-adressen met Azure PowerShell](deploy-multi-public-ip-powershell.md)voor meer informatie over Azure Firewall met meerdere openbare IP-adressen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-azure-firewall"></a>Een Azure Firewall maken

Met deze sjabloon wordt een Azure Firewall gemaakt met twee openbare IP-adressen, samen met de benodigde resources om de Azure Firewall te ondersteunen.

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Meerdere Azure-bronnen worden gedefinieerd in de sjabloon:

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

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. Typ of selecteer in de portal op de pagina **Een Azure Firewall maken met meerdere IP-adressen** de volgende waarden:
   - Abonnement: Kiezen uit bestaande abonnementen 
   - Resourcegroep: Selecteer uit bestaande resourcegroepen of selecteer **Nieuw maken**en selecteer **OK**.
   - Locatie: een locatie selecteren
   - Gebruikersnaam beheerder: gebruikersnaam typen voor het beheerdersgebruikersaccount 
   - Beheerderswachtwoord: typ een beheerderswachtwoord of -sleutel

3. Selecteer **Ik ga akkoord met de hierboven vermelde algemene voorwaarden** en selecteer Vervolgens **Kopen**. Het kan 10 minuten of langer duren voordat de implementatie is voltooid.

## <a name="validate-the-deployment"></a>De implementatie valideren

Bekijk in de Azure-portal de geïmplementeerde resources. Let op de openbare IP-adressen van de firewall.  

Gebruik Verbinding met extern bureaublad om verbinding te maken met de openbare IP-adressen van de firewall. Succesvolle verbindingen tonen firewall NAT-regels aan die de verbinding met de backendservers mogelijk maken.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de firewall hebt gemaakt, niet meer nodig hebt, verwijdert u de brongroep. Hiermee worden de firewall en alle bijbehorende bronnen verwijderd.

Als u de brongroep `Remove-AzResourceGroup` wilt verwijderen, roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met behulp van de Azure-portal](tutorial-hybrid-portal.md)