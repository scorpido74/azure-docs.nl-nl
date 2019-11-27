---
title: Configureer taak verdeling en uitgaande regels met behulp van Azure PowerShell
titleSuffix: Azure Load Balancer
description: In dit artikel wordt beschreven hoe u taak verdeling en uitgaande regels in Standard Load Balancer kunt configureren met behulp van Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225444"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Configureer taak verdeling en uitgaande regels in Standard Load Balancer door gebruik te maken van Azure PowerShell

In dit artikel leest u hoe u uitgaande regels in Standard Load Balancer kunt configureren met behulp van Azure PowerShell.  

Wanneer u het scenario van dit artikel voltooit, bevat de load balancer resource twee front-ends en de bijbehorende regels. U hebt één front-end voor inkomend verkeer en een andere front-end voor uitgaand verkeer.  

Elke front-end verwijst naar een openbaar IP-adres. In dit scenario verschilt het open bare IP-adres voor inkomend verkeer van het adres voor uitgaand verkeer.   De taakverdelings regel biedt alleen binnenkomende taak verdeling. De regel voor uitgaand verkeer bepaalt de uitgaande Network Address Translation (NAT) voor de virtuele machine.  

In het scenario worden twee back-endservers gebruikt: één voor binnenkomend verkeer en één voor uitgaand verkeer. Deze groepen illustreren de mogelijkheden en bieden flexibiliteit voor het scenario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account
Meld u aan bij uw Azure-abonnement met behulp van de opdracht [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Volg vervolgens de instructies op het scherm.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resource groep met behulp van [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Een Azure-resource groep is een logische container waarin Azure-resources worden geïmplementeerd. De resources worden vervolgens beheerd vanuit de groep.

In het volgende voor beeld wordt een resource groep met de naam *myresourcegroupoutbound* gemaakt op de locatie *eastus2* :

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk met de naam *myvnetoutbound*. Noem het subnet *mysubnetoutbound*. Plaats deze in *myresourcegroupoutbound* met behulp van [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) en [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Een binnenkomend openbaar IP-adres maken 

Als u toegang wilt krijgen tot uw web-app op internet, hebt u een openbaar IP-adres nodig voor de load balancer. Standard Load Balancer ondersteunt alleen standaard open bare IP-adressen. 

Gebruik [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) om een standaard openbaar IP-adres te maken met de naam *mypublicipinbound* in *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Een uitgaand openbaar IP-adres maken 

Maak een standaard-IP-adres voor de uitgaande configuratie van de front-end van de load balancer met behulp van [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>Een Azure-load balancer maken

In deze sectie wordt uitgelegd hoe u de volgende onderdelen van de load balancer maakt en configureert:
  - Een front-end-IP-adres dat het binnenkomende netwerk verkeer op de load balancer ontvangt
  - Een back-end-pool waar de front-end-IP het netwerk verkeer met gelijke taak verdeling verzendt
  - Een back-end-pool voor uitgaande verbindingen
  - Een status test die de status van de VM-exemplaren van de back-end bepaalt
  - Een regel voor binnenkomende Load Balancer die definieert hoe verkeer wordt gedistribueerd naar de Vm's
  - Een uitgaande regel voor een Load Balancer die definieert hoe verkeer van de virtuele machines wordt gedistribueerd

### <a name="create-an-inbound-front-end-ip"></a>Een binnenkomend front-end-IP-adres maken
Maak de inkomende front-end-IP-configuratie voor de load balancer met behulp van [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Het load balancer moet een inkomende front-end-IP-configuratie met de naam *myfrontendinbound*bevatten. Koppel deze configuratie aan het open bare IP-adres *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Een uitgaand front-end-IP-adres maken
Maak de uitgaande front-end-IP-configuratie voor de load balancer met behulp van [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Deze load balancer moet een uitgaande front-end-IP-configuratie met de naam *myfrontendoutbound*bevatten. Koppel deze configuratie aan het open bare IP-adres *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Een binnenkomende back-end-pool maken
Maak de back-end-binnenkomende groep voor de load balancer met behulp van [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Noem de groep *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Een uitgaande back-end-pool maken
Gebruik de volgende opdracht om een andere back-end-adres groep te maken om de uitgaande connectiviteit voor een groep Vm's te definiëren met behulp van [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Geef een naam op voor deze groep *bepooloutbound*. 

Door een afzonderlijke uitgaande pool te maken, kunt u Maxi maal flexibiliteit bieden. U kunt deze stap echter weglaten en alleen de inkomende *bepoolinbound* gebruiken als u dat wilt.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Een statustest maken

Een status test controleert alle VM-exemplaren om te controleren of ze netwerk verkeer kunnen verzenden. Het VM-exemplaar dat de tests van de test mislukt, wordt verwijderd uit de load balancer totdat het weer online is en een test controle vaststelt dat deze in orde is. 

Als u de status van de Vm's wilt bewaken, maakt u een status test met behulp van [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Een Load Balancer-regel maken

Een Load Balancer-regel definieert de front-end-IP-configuratie voor het binnenkomende verkeer en de back-end-pool om het verkeer te ontvangen. Het definieert ook de vereiste bron-en doel poort. 

Maak een Load Balancer-regel met de naam *myinboundlbrule* met behulp van [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Met deze regel wordt geluisterd naar poort 80 in de front-end-groep *myfrontendinbound*. Er wordt ook poort 80 gebruikt voor het verzenden van netwerk verkeer met taak verdeling naar de back- *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Met deze taakverdelings regel wordt automatische uitgaande NAT (SNAT) uitgeschakeld vanwege de para meter **-disableoutboundsnat toegevoegd** . De uitgaande NAT wordt alleen door de uitgaande regel verschaft.

### <a name="create-an-outbound-rule"></a>Een uitgaande regel maken

Een uitgaande regel definieert de front-end open bare IP, die wordt vertegenwoordigd door de front-end- *myfrontendoutbound*. Deze front-end wordt gebruikt voor al het uitgaande NAT-verkeer en de back-end-pool waarop de regel van toepassing is.  

Gebruik de volgende opdracht om een uitgaande regel *myoutboundrule* te maken voor uitgaande netwerk omzetting van alle virtuele machines (in IP-configuratie van NIC) in de *bepool* back-end-pool.  De opdracht wijzigt de uitgaande time-out voor inactiviteit van 4 in 15 minuten. Er worden 10.000 SNAT-poorten toegewezen in plaats van 1.024. Zie [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0)voor meer informatie.

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Als u geen afzonderlijke uitgaande pool wilt gebruiken, kunt u het argument adres groep wijzigen in de voor gaande opdracht om in plaats daarvan *$bepoolin* op te geven.  We raden u aan om afzonderlijke groepen te gebruiken om de resulterende configuratie flexibeler en leesbaar te maken.

### <a name="create-a-load-balancer"></a>Een load balancer maken

Gebruik de volgende opdracht om een load balancer voor het inkomende IP-adres te maken met behulp van [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Noem de load balancer *lb*. Het moet een inkomende front-end-IP-configuratie bevatten. De back- *bepoolinbound* moeten worden gekoppeld aan de *mypublicipinbound* voor het open bare IP-adres dat u in de vorige stap hebt gemaakt.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

U kunt nu door gaan met het toevoegen van uw virtuele machines aan zowel *bepoolinbound* -als *bepooloutbound* -back-end-Pools door de IP-configuratie van de desbetreffende NIC-bronnen bij te werken. Werk de resource configuratie bij met behulp van [add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resource groep, load balancer en gerelateerde resources niet meer nodig hebt, kunt u deze verwijderen met behulp van [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een standaard load balancer gemaakt, zowel binnenkomende als uitgaande Load Balancer-regels voor verkeer geconfigureerd en een status test geconfigureerd voor de virtuele machines in de back-end-pool. 

Ga verder met de [zelf studies voor Azure Load Balancer voor](tutorial-load-balancer-standard-public-zone-redundant-portal.md)meer informatie.
