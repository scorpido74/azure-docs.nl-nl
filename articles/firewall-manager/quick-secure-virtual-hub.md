---
title: 'Quickstart: virtuele hub beveiligen met Azure Firewall Manager Preview - Resource Manager-sjabloon'
description: Meer informatie over het beveiligen van uw virtuele hub met Azure Firewall Manager Preview.
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/19/2020
ms.author: victorh
ms.openlocfilehash: 9c51fdb5142159e390ac4fcf59a04aa3dd747469
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167193"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---resource-manager-template"></a>Quickstart: uw virtuele hub beveiligen met Azure Firewall Manager - Resource Manager-sjabloon

In deze quickstart gebruikt u een Resource Manager-sjabloon om uw virtuele hub te beveiligen met Azure Firewall Manager Preview. De geïmplementeerde firewall heeft een toepassingsregel waarmee verbindingen met `www.microsoft.com` worden toegestaan. Twee virtuele Windows Server 2019-machines worden geïmplementeerd om de firewall te testen. Eén jumpserver wordt gebruikt om verbinding te maken met de workloadserver. Vanaf de workloadserver kunt u alleen verbinding maken met `www.microsoft.com`.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zie [What is Azure Firewall Manager Preview?](overview.md) (Wat is Azure Firewall Manager Preview?) voor meer informatie over Azure Firewall Manager Preview.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-secured-virtual-hub"></a>Een beveiligde virtuele hub maken

Met deze sjabloon maakt u een beveiligde virtuele hub met Azure Firewall Manager Preview, plus de benodigde resources om het scenario te ondersteunen.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json" range="001-477" highlight="47-76":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>De sjabloon implementeren

De Resource Manager-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u een Azure-firewall, een virtuele WAN en virtuele hub, de netwerkinfrastructuur en twee virtuele machines.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. Typ of selecteer in de portal op de pagina **Beveiligde virtuele hubs** de volgende waarden:
   - Abonnement: selecteer uit bestaande abonnementen 
   - Resourcegroep:  selecteer uit bestaande resourcegroepen of selecteer **Nieuwe maken** en selecteer vervolgens **OK**.
   - Locatie: selecteer een locatie
   - Gebruikersnaam voor beheerder: voer de gebruikersnaam voor het beheerdersaccount in 
   - Wachtwoord voor beheerder: voer het wachtwoord of de sleutel voor de beheerder in

3. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**. De implementatie kan 10 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

Test nu de firewallregels om te controleren of deze werkt zoals verwacht.

1. Controleer in Azure Portal de netwerkinstellingen voor de virtuele machine **Workload-Srv** en noteer het privé-IP-adres.
2. Verbind een extern bureaublad met de virtuele machine **Jump-Srv** en meld u aan. Open vervolgens een verbinding met een extern bureaublad met het privé-IP-adres **Workload-Srv**.

3. Open Internet Explorer en blader naar `www.microsoft.com`.
4. Selecteer **OK** > **Sluiten** in de beveiligingswaarschuwingen van Internet Explorer.

   Als het goed is, ziet u nu de startpagina van Microsoft.

5. Blader naar `www.google.com`.

   U zou nu door de firewall moeten worden geblokkeerd.

Nu u hebt geverifieerd dat de firewallregels werken:

* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de firewall hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de firewall en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over vertrouwde beveiligingspartners](trusted-security-partners.md)
