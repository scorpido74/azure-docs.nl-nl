---
title: 'Quickstart: Een Azure-firewall met meerdere openbare IP-adressen maken: Resource Manager-sjabloon'
description: Ontdek hoe u met een Resource Manager-sjabloon een Azure-firewall met meerdere openbare IP-adressen maakt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: b81362f191e6317aa7ffa974a6d432b0c7514d8f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680539"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Quickstart: Een Azure-firewall met meerdere openbare IP-adressen maken: Resource Manager-sjabloon

In deze quickstart gebruikt u een Resource Manager-sjabloon om een Azure-firewall met meerdere openbare IP-adressen te implementeren. De geïmplementeerde firewall heeft regels voor het verzamelen van NAT-regels waarmee RDP-verbindingen met twee Windows Server 2019-VM's worden toegestaan.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zie [Een Azure-firewall met meerdere openbare IP-adressen implementeren met Azure PowerShell](deploy-multi-public-ip-powershell.md) voor meer informatie over Azure Firewall met meerdere openbare IP-adressen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-azure-firewall"></a>Een Azure-firewall maken

Met deze sjabloon maakt u een Azure-firewall met twee openbare IP-adressen, samen met de benodigde resources die ondersteuning bieden voor de Azure-firewall.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/fw-docs-qs).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Er worden meerdere resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>De sjabloon implementeren

De Resource Manager-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u een Azure-firewall, de netwerkinfrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. Typ of selecteer in de portal, op de pagina **Een Azure-firewall met openbare IP-adressen maken**, de volgende waarden:
   - Abonnement: Selecteer uit bestaande abonnementen 
   - Resourcegroep:  Selecteer uit bestaande resourcegroepen of selecteer **Nieuwe maken** en vervolgens **OK**.
   - Locatie: Selecteer een locatie
   - Gebruikersnaam voor beheerder: Voer de gebruikersnaam voor het beheerdersaccount in 
   - Wachtwoord voor beheerder: Voer het wachtwoord of de sleutel voor de beheerder in

3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**. De implementatie kan 10 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

Controleer in Azure Portal de geïmplementeerde resources. Noteer de openbare IP-adressen van de firewall.  

Gebruik Verbinding met extern bureaublad om verbinding te maken met de openbare IP-adressen van de firewall. Voor verbindingen die tot stand zijn gebracht, worden NAT-regels voor de firewall weergegeven die de verbinding met de backendservers toestaan.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de firewall hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de firewall en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall implementeren en configureren in een hybride netwerk met Azure Portal](tutorial-hybrid-portal.md)