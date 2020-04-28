---
title: Verbinding maken met back-end v1
description: Meer informatie over hoe u veilig verbinding maakt met de back-end-bronnen via een App Service Environment. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687296"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Veilig verbinding maken met back-end-bronnen vanuit een App Service omgeving
Omdat een App Service Environment altijd wordt **gemaakt in een** Azure Resource Manager virtueel netwerk **of** een klassiek implementatie model [virtueel netwerk][virtualnetwork], kunnen uitgaande verbindingen van een app service Environment naar andere back-upbronnen alleen stromen via het virtuele netwerk.  Met een recente wijziging in juni 2016 kan as ook worden geïmplementeerd in virtuele netwerken die gebruikmaken van open bare adresbereiken, of RFC1918-adres ruimten (dat wil zeggen particuliere adressen).  

Er kan bijvoorbeeld een SQL Server worden uitgevoerd op een cluster met virtuele machines met poort 1433 vergrendeld.  Het eind punt kan worden ACLd om alleen toegang toe te staan vanuit andere bronnen in hetzelfde virtuele netwerk.  

Een ander voor beeld: gevoelige eind punten kunnen on-premises worden uitgevoerd en met Azure worden verbonden via [site-naar-site-][SiteToSite] of [Azure ExpressRoute][ExpressRoute] -verbindingen.  Als gevolg hiervan hebben alleen bronnen in virtuele netwerken die zijn verbonden met de site-naar-site-of ExpressRoute-tunnels toegang tot on-premises eind punten.

Voor al deze scenario's kunnen apps die op een App Service Environment worden uitgevoerd, veilig verbinding maken met de verschillende servers en bronnen.  Uitgaand verkeer van apps die worden uitgevoerd in een App Service Environment naar privé-eind punten in hetzelfde virtuele netwerk (of verbonden met hetzelfde virtuele netwerk), doorloopt alleen over het virtuele netwerk.  Uitgaand verkeer naar privé-eind punten verloopt niet via het open bare Internet.

Een voor behoud geldt voor uitgaand verkeer van een App Service Environment naar eind punten in een virtueel netwerk.  App Service omgevingen kunnen geen eind punten bereiken van virtuele machines die zich in **hetzelfde** subnet bevinden als de app service environment.  Normaal gesp roken is dit geen probleem, omdat App Service omgevingen worden geïmplementeerd in een subnet dat uitsluitend is gereserveerd voor exclusief gebruik door de App Service Environment.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Vereisten voor uitgaande verbindingen en DNS
Voor een goede werking van App Service Environment is uitgaande toegang tot verschillende eind punten vereist. Een volledige lijst van de externe eind punten die worden gebruikt door een ASE vindt u in de sectie ' vereiste netwerk verbinding ' van het artikel [netwerk configuratie voor ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service omgevingen vereisen een geldige DNS-infra structuur die is geconfigureerd voor het virtuele netwerk.  Als de DNS-configuratie om welke reden dan ook wordt gewijzigd nadat een App Service Environment is gemaakt, kunnen ontwikkel aars een App Service Environment afdwingen om de nieuwe DNS-configuratie op te halen.  Het activeren van een rolling omgeving opnieuw opstarten met het pictogram ' opnieuw opstarten ' boven aan de Blade App Service Environment-beheer in de portal zorgt ervoor dat de omgeving de nieuwe DNS-configuratie ophaalt.

Het is ook raadzaam dat alle aangepaste DNS-servers op het vnet vooraf worden ingesteld voordat een App Service Environment wordt gemaakt.  Als de DNS-configuratie van een virtueel netwerk wordt gewijzigd terwijl er een App Service Environment wordt gemaakt, leidt dit ertoe dat het proces voor het maken van App Service Environment mislukt.  Als er in een vergelijk bare Vein een aangepaste DNS-server bestaat aan het andere uiteinde van een VPN-gateway en de DNS-server onbereikbaar is of niet beschikbaar is, mislukt het proces voor het maken van de App Service Environment ook.

## <a name="connecting-to-a-sql-server"></a>Verbinding maken met een SQL Server
Een algemene SQL Server configuratie heeft een eind punt dat luistert op poort 1433:

![SQL Server-eind punt][SqlServerEndpoint]

Er zijn twee benaderingen voor het beperken van verkeer naar dit eind punt:

* [Lijst met netwerk Access Control][NetworkAccessControlLists] (netwerk-acl's)
* [Netwerk beveiligings groepen][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Toegang beperken met een netwerk toegangs beheer lijst
Poort 1433 kan worden beveiligd met behulp van een netwerk toegangs beheer lijst.  Het voor beeld hieronder whitelists client adressen die afkomstig zijn van binnen een virtueel netwerk en blokkeert de toegang tot alle andere clients.

![Voor beeld van lijst met netwerk Access Control][NetworkAccessControlListExample]

Toepassingen die worden uitgevoerd in App Service Environment in hetzelfde virtuele netwerk als de SQL Server, kunnen verbinding maken met het SQL Server exemplaar met behulp van het IP-adres van het **VNet-interne** voor de virtuele machine SQL Server.  

In het onderstaande voor beeld connection string wordt verwezen naar de SQL Server met behulp van het bijbehorende privé-IP-adres.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Hoewel de virtuele machine ook een openbaar eind punt heeft, worden Verbindings pogingen met het open bare IP-adres geweigerd vanwege de netwerk toegangs beheer lijst. 

## <a name="restricting-access-with-a-network-security-group"></a>Toegang beperken met een netwerk beveiligings groep
Een alternatieve methode voor het beveiligen van de toegang is met een netwerk beveiligings groep.  Netwerk beveiligings groepen kunnen worden toegepast op afzonderlijke virtuele machines of op een subnet met virtuele machines.

Eerst moet een netwerk beveiligings groep worden gemaakt:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Het beperken van toegang tot het interne VNet-verkeer is heel eenvoudig met een netwerk beveiligings groep.  De standaard regels in een netwerk beveiligings groep staan alleen toegang toe vanaf andere netwerkclients in hetzelfde virtuele netwerk.

Als gevolg hiervan is het vergren delen van toegang tot SQL Server net zo eenvoudig als het Toep assen van een netwerk beveiligings groep met de standaard regels naar de virtuele machines met SQL Server of het subnet dat de virtuele machines bevat.

In het onderstaande voor beeld wordt een netwerk beveiligings groep toegepast op het subnet dat het bevat:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Het eind resultaat is een set beveiligings regels waarmee externe toegang wordt geblokkeerd, terwijl VNet interne toegang is toegestaan:

![Standaard regels voor netwerk beveiliging][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app service Environment][IntroToAppServiceEnvironment] om aan de slag te gaan met app service omgevingen

Zie voor meer informatie over het beheren van inkomend verkeer naar uw App Service Environment het [beheren van inkomend verkeer naar een app service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
