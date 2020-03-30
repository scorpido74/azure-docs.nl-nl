---
title: Een Firewall-beleid voor webtoepassingen koppelen aan een bestaande Azure Application Gateway
description: Meer informatie over het koppelen van een Firewall-beleid voor webtoepassingen aan een bestaande Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083912"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Een WAF-beleid koppelen aan een bestaande toepassingsgateway

U Azure PowerShell gebruiken om [een WAF-beleid](create-waf-policy-ag.md)te maken, maar mogelijk hebt u al een toepassingsgateway en wilt u er gewoon een WAF-beleid aan koppelen. In dit artikel, je doet precies dat; u maakt een WAF-beleid en koppelt deze aan een reeds bestaande Application Gateway. 

1. Download uw application gateway- en firewallbeleid. Zie stap 2 als u geen bestaand firewallbeleid hebt. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Optioneel) Maak een firewallbeleid.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Als u dit WAF-beleid maakt om over te stappen van een WAF Config naar een WAF-beleid, moet het beleid een exacte kopie van uw oude Config zijn. Dit betekent dat elke uitsluiting, aangepaste regel, uitgeschakelde regelgroep, enz.
3. (Optioneel) U het WAF-beleid zo configureren dat aan uw behoeften voldoet. Dit omvat aangepaste regels, het uitschakelen van regels/regelgroepen, uitsluitingen, het instellen van limieten voor het uploaden van bestanden, enz. Als u deze stap overslaat, worden alle standaardinstellingen geselecteerd. 
   
4. Sla het beleid op en voeg het toe aan de toepassingsgateway. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over aangepaste regels.](configure-waf-custom-rules.md)
