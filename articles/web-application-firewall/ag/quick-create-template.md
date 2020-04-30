---
title: 'Quick Start: een Azure WAF v2 maken op Application Gateway-Resource Manager-sjabloon'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van een resource manager-sjabloon voor het maken van een Web Application firewall v2 op Azure-toepassing gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 6759071e73adfd3af4ac780da6db3a0e6e967ea1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81617987"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>Quick Start: een Azure WAF v2 maken op Application Gateway-Resource Manager-sjabloon

In deze Quick Start gebruikt u een resource manager-sjabloon om een Azure Web Application-firewall v2 te maken op Application Gateway.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-web-application-firewall"></a>Een Web Application firewall maken

Met deze sjabloon maakt u een eenvoudige Web Application firewall v2 op Azure-toepassing gateway. Dit omvat een openbaar IP-frontend-IP-adres, HTTP-instellingen, een regel met een Basic-listener op poort 80 en een back-end-pool. Er wordt een WAF-beleid met een aangepaste regel gemaakt om het verkeer naar de back-end-groep te blok keren op basis van een overeenkomend type voor een IP-adres.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json) start-sjablonen

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Micro soft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Micro soft. Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Micro soft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : een voor de toepassings gateway en twee voor de virtuele machines.
- [**Micro soft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Micro soft. Compute/informatie**](/azure/templates/microsoft.compute/virtualmachines) : twee virtuele machines
- [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : twee voor de virtuele machines
- [**Micro soft. Compute/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : IIS en de webpagina's configureren

### <a name="deploy-the-template"></a>De sjabloon implementeren

Resource Manager-sjabloon implementeren in Azure:

1. Selecteer **implementeren naar Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u een toepassings gateway, de netwerk infrastructuur en twee virtuele machines in de back-end-groep met IIS.

   [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Selecteer of maak een resource groep.
3. Selecteer **Ik ga akkoord met de bovenstaande voor waarden** en selecteer vervolgens **kopen**. Het volt ooien van de implementatie kan 10 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

Hoewel IIS niet is vereist voor het maken van de toepassings gateway, wordt het geïnstalleerd op de back-endservers om te controleren of Azure een WAF v2 heeft gemaakt op de Application Gateway. 

Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het open bare IP-adres voor de toepassings gateway op de pagina **overzicht** . ![Neem het open bare IP-](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) adres van de toepassings gateway op of u kunt **alle resources**selecteren, *myAGPublicIPAddress* invoeren in het zoekvak en deze vervolgens selecteren in de zoek resultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
2. Kopieer het open bare IP-adres en plak het in de adres balk van uw browser om door dat IP-adres te bladeren.
3. Controleer het antwoord. Een **403 verboden** antwoord controleert of de WAF is gemaakt en blokkeert verbindingen met de back-end-groep.
4. Wijzig de aangepaste regel om **verkeer toe te staan**.
  Voer het volgende Azure PowerShell script uit, waarbij u de naam van de resource groep vervangt:
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

   Vernieuw de browser meerdere keren en controleer de verbindingen met zowel myVM1 als myVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt met de Application Gateway niet meer nodig hebt, verwijdert u de resource groep. Hiermee verwijdert u de toepassings gateway en alle gerelateerde resources.

Als u de resource groep wilt verwijderen, `Remove-AzResourceGroup` roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: een toepassings gateway maken met een firewall voor webtoepassingen met behulp van de Azure Portal](application-gateway-web-application-firewall-portal.md)