---
title: 'Snelstart: een Azure WAF v2 maken op sjabloon Application Gateway - Resource Manager'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van een resourcemanagersjabloon om een Web Application Firewall v2 te maken op Azure Application Gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 6759071e73adfd3af4ac780da6db3a0e6e967ea1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617987"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>Snelstart: een Azure WAF v2 maken op sjabloon Application Gateway - Resource Manager

In deze snelstart gebruikt u een resourcebeheersjabloon om een Azure Web Application Firewall v2 op Application Gateway te maken.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-web-application-firewall"></a>Een firewall voor webtoepassingen maken

Met deze sjabloon wordt een eenvoudige Web Application Firewall v2 gemaakt op Azure Application Gateway. Dit omvat een openbaar IP-frontend IP-adres, HTTP-instellingen, een regel met een basislistener op poort 80 en een backendpool. Er wordt een WAF-beleid met een aangepaste regel gemaakt om verkeer naar de backendpool te blokkeren op basis van een IP-adresovereenkomsttype.

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

Meerdere Azure-bronnen worden gedefinieerd in de sjabloon:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewall-beleid**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses) één voor de toepassingsgateway en twee voor de virtuele machines.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : twee virtuele machines
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : twee voor de virtuele machines
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : IIS en de webpagina's configureren

### <a name="deploy-the-template"></a>De sjabloon implementeren

Resourcebeheersjabloon implementeren in Azure:

1. Selecteer **Implementeren naar Azure** om u aan te melden bij Azure en open de sjabloon. De sjabloon maakt een toepassingsgateway, de netwerkinfrastructuur en twee virtuele machines in de backendpool met IIS.

   [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Selecteer of maak uw resourcegroep.
3. Selecteer **Ik ga akkoord met de hierboven vermelde algemene voorwaarden** en selecteer Vervolgens **Kopen**. Het kan 10 minuten of langer duren voordat de implementatie is voltooid.

## <a name="validate-the-deployment"></a>De implementatie valideren

Hoewel IIS niet vereist is om de toepassingsgateway te maken, is deze geïnstalleerd op de backendservers om te controleren of Azure een WAF v2 heeft gemaakt op de toepassingsgateway. 

Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het openbare IP-adres voor de toepassingsgateway op de **pagina Overzicht.** ![Openbare IP-adres](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) van de toepassingsgateway opnemen Of u **Alle bronnen**selecteren, *myAGPublicIPAddress* invoeren in het zoekvak en het vervolgens selecteren in de zoekresultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
2. Kopieer het openbare IP-adres en plak het vervolgens in de adresbalk van uw browser om door dat IP-adres te bladeren.
3. Controleer het antwoord. Een **403 Verboden** reactie controleert of de WAF met succes is gemaakt en blokkeert verbindingen met de backend pool.
4. Wijzig de aangepaste regel om **verkeer toe**te staan .
  Voer het volgende Azure PowerShell-script uit en vervang de naam van uw resourcegroep:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Vernieuw uw browser meerdere keren en u ziet verbindingen met zowel myVM1 als myVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de toepassingsgateway hebt gemaakt, niet meer nodig hebt, verwijdert u de brongroep. Hiermee worden de toepassingsgateway en alle bijbehorende bronnen verwijderd.

Als u de brongroep `Remove-AzResourceGroup` wilt verwijderen, roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een toepassingsgateway maken met een firewall voor webtoepassingen met behulp van de Azure-portal](application-gateway-web-application-firewall-portal.md)