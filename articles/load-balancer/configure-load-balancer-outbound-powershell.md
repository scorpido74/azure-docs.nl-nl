---
title: Taak verdeling en uitgaande regels configureren met behulp van Azure PowerShell
titlesuffix: Azure Load Balancer
description: In dit artikel wordt beschreven hoe u taak verdeling en uitgaande regels configureert in een Standard Load Balancer met behulp van de Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262620"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Configureer taak verdeling en uitgaande regels in de standaard load balancer met behulp van Azure PowerShell

In dit artikel leest u hoe u uitgaande regels configureert in de standaard load balancer met behulp van Azure PowerShell.  

Wanneer u klaar bent, bevat de load balancer resource twee front-end-en-regels die eraan zijn gekoppeld: één voor inkomend en een andere voor uitgaand verkeer.  Elke front-end bevat een verwijzing naar een openbaar IP-adres en deze scenario maakt gebruik van een ander openbaar IP-adres voor inkomend en uitgaand.   De load balancer-regel biedt alleen binnenkomende load balancing en de uitgaande regel bepaalt de uitgaande NAT opgegeven voor de virtuele machine.  In dit artikel worden twee afzonderlijke back-endservers gebruikt, één voor inkomend en één voor uitgaand, om de mogelijkheid te illustreren en flexibiliteit te bieden voor dit scenario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Verbinding maken met Azure-account
Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) en volg de instructies op het scherm:
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Het volgende voorbeeld wordt een resourcegroep met de naam *myresourcegroupoutbound* in de *eastus2* locatie:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Virtueel netwerk maken
Maak een virtueel netwerk met de naam *myvnetoutbound* met een subnet met de naam *mysubnetoutbound* in de *myresourcegroupoutbound* met [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) en [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0):

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Inkomend openbaar IP-adres maken 

Om toegang te krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. Een standaard load balancer ondersteunt alleen standaard open bare IP-adressen. Gebruik [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) om een standaard openbaar IP-adres te maken met de naam *mypublicipinbound* in *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Uitgaande openbare IP-adres maken 

Maak een standaard-IP-adres voor de uitgaande configuratie van de frontend van de load balancer met [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Azure-load balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:
  - Een front-end IP-adres dat het binnenkomende netwerkverkeer op de load balancer ontvangt.
  - Een back-end-groep waarbij het frontend-IP-netwerk verkeer met gelijke taak verdeling verzendt.
  - Een back-end-groep voor uitgaande verbindingen. 
  - een statustest die bepaalt de status van de back-end-VM-exemplaren.
  - Een load balancer inkomende regel waarmee wordt gedefinieerd hoe verkeer wordt gedistribueerd naar de virtuele machines.
  - Een load balancer uitgaande regel waarmee wordt gedefinieerd hoe verkeer wordt gedistribueerd naar de VM's.

### <a name="create-inbound-frontend-ip"></a>Een binnenkomend frontend-IP maken
Maak de uitgaande frontend-IP-configuratie voor de load balancer met [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) die een inkomende frontend-IP-configuratie bevat met de naam *myfrontendinbound* die is gekoppeld aan het open bare IP-adres  *mypublicipinbound*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Uitgaande frontend-IP-adres maken
Maak de uitgaande frontend-IP-configuratie voor de load balancer met [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) die een uitgaande frontend-IP-configuratie bevat met de naam *myfrontendoutbound* die is gekoppeld aan het open bare IP-adres  *mypublicipoutbound*:

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Een inkomende back-end-pool maken
Maak de back-endserver voor binnenkomende pool voor de load balancer met [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) met de naam *bepoolinbound*:

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Een uitgaande back-end-pool maken
Maak een extra back-end-adres groep om de uitgaande connectiviteit voor een groep Vm's te definiëren met de [nieuwe AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) met de naam *bepooloutbound*. Het maken van een afzonderlijke uitgaande groep biedt maximale flexibiliteit, maar u kunt deze stap overs Laan en ook alleen de inkomende *bepoolinbound* gebruiken.  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Statustest maken

Een statuscontrole controleert alle exemplaren van de virtuele machines om ervoor te zorgen dat deze netwerkverkeer kunnen verzenden. Het exemplaar van een virtuele machine met mislukte testcontroles wordt uit de load balancer verwijderd totdat deze weer online komt en een testcontrole bepaalt of deze in orde is. Maak een status test met [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) om de status van de virtuele machines te bewaken. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Regel voor taakverdeling maken

Een load balancer-regel definieert de front-end-IP-configuratie voor het binnenkomende verkeer en de back endadresgroep voor het ontvangen van het verkeer, samen met de gewenste bron- en doelserver. Maak een load balancer regel *myinboundlbrule* met [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) voor het Luis teren naar poort 80 in de front- *myfrontendinbound* en verzend netwerk verkeer met gelijke taak verdeling naar de back-end-adres groep  *bepoolinbound* maakt ook gebruik van poort 80. 

>[!NOTE]
>Met deze taakverdelings regel worden de Automatic outbound (S) NAT uitgeschakeld als gevolg van deze regel met de para meter **-disableoutboundsnat toegevoegd** . Uitgaande NAT wordt alleen geleverd door de regel voor uitgaande.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Uitgaande regel maken

Een uitgaande regel definieert het front-end openbare IP-adres, vertegenwoordigd door de front-end *myfrontendoutbound*, die wordt gebruikt voor alle uitgaande NAT-verkeer, evenals de back-endpool waarop deze regel van toepassing is.  Maakt een uitgaande regel *myoutboundrule* voor uitgaand netwerkverkeer vertaling van alle virtuele machines (NIC IP-configuraties) in *bepool* back-endpool.  De onderstaande opdracht ook de uitgaande time-out voor inactiviteit wordt gewijzigd van 4 tot 15 minuten en 10000 SNAT poorten in plaats van 1024 toegewezen.  Raadpleeg [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) voor meer informatie.

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Als u geen afzonderlijke uitgaande pool wilt gebruiken, kunt u het argument adres groep wijzigen in de voor gaande opdracht om in plaats daarvan *$bepoolin* op te geven.  We raden u aan om afzonderlijke groepen te gebruiken voor flexibiliteit en lees baarheid van de resulterende configuratie.

### <a name="create-load-balancer"></a>Load balancer maken

Maak een load balancer met het inkomende IP-adres met behulp van [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) met de naam *lb* . Dit omvat een inkomende frontend-IP-configuratie en een back- *bepoolinbound* die is gekoppeld aan het open bare IP-adres  *mypublicipinbound* die u in de vorige stap hebt gemaakt.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Op dit moment kunt u door gaan met het toevoegen van uw virtuele machines aan de back- *bepoolinbound* __en__ *bepooloutbound* door de IP-configuratie van de betreffende NIC-bronnen bij te werken met [add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de opdracht [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) gebruiken om de resource groep, Load Balancer en alle gerelateerde resources te verwijderen.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u standaard load balancer gemaakt, zowel de regels voor binnenkomend load balancer-verkeer, de configuratie en de status test voor de virtuele machines in de back-endadresgroep geconfigureerd. Als u meer wilt weten over Azure Load Balancer, gaat u verder met de zelf studies voor Azure load balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
