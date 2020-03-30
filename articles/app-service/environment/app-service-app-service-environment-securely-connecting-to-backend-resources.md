---
title: Verbinding maken met back-end v1
description: Meer informatie over hoe u veilig verbinding maken met backendbronnen vanuit een app-serviceomgeving. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687296"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Veilig verbinding maken met back-endbronnen vanuit een App-serviceomgeving
Aangezien een App Service-omgeving altijd wordt gemaakt **in** een virtueel Azure Resource Manager-netwerk **of** een [virtueel netwerk][virtualnetwork]met een klassiek implementatiemodel, kunnen uitgaande verbindingen van een App-serviceomgeving naar andere backendbronnen uitsluitend over het virtuele netwerk stromen.  Met een recente wijziging in juni 2016 kunnen ASE's ook worden geïmplementeerd in virtuele netwerken die gebruikmaken van openbare adresbereiken of RFC1918-adresruimten (d.w.z. privéadressen).  

Er kan bijvoorbeeld een SQL Server worden uitgevoerd op een cluster van virtuele machines met poort 1433 vergrendeld.  Het eindpunt kan AcLd zijn om alleen toegang toe te staan vanuit andere bronnen op hetzelfde virtuele netwerk.  

Als ander voorbeeld kunnen gevoelige eindpunten on-premises worden uitgevoerd en worden ze verbonden met Azure via [site-to-site-][SiteToSite] of [Azure ExpressRoute-verbindingen.][ExpressRoute]  Als gevolg hiervan kunnen alleen bronnen in virtuele netwerken die zijn verbonden met de site-to-site- of ExpressRoute-tunnels toegang krijgen tot on-premises eindpunten.

Voor al deze scenario's kunnen apps die op een App-serviceomgeving worden uitgevoerd, veilig verbinding maken met de verschillende servers en bronnen.  Uitgaand verkeer van apps die worden uitgevoerd in een App-serviceomgeving naar privéeindpunten in hetzelfde virtuele netwerk (of verbonden met hetzelfde virtuele netwerk), stroomt alleen over het virtuele netwerk.  Uitgaand verkeer naar privéeindpunten stroomt niet via het openbare internet.

Eén voorbehoud is van toepassing op uitgaand verkeer van een App Service-omgeving naar eindpunten binnen een virtueel netwerk.  App-serviceomgevingen kunnen geen eindpunten van virtuele machines bereiken die zich in **hetzelfde** subnet bevinden als de App-serviceomgeving.  Dit zou normaal gesproken geen probleem moeten zijn zolang App Service Omgevingen worden geïmplementeerd in een subnet gereserveerd voor exclusief gebruik door alleen de App Service Omgeving.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Vereisten voor uitgaande verbindingen en DNS
Als een App Service-omgeving goed functioneert, is er uitgaande toegang tot verschillende eindpunten nodig. Een volledige lijst van de externe eindpunten die door een ASE worden gebruikt, bevindt zich in het gedeelte 'Vereiste netwerkconnectiviteit' van het artikel [Netwerkconfiguratie voor ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

App-serviceomgevingen vereisen een geldige DNS-infrastructuur die is geconfigureerd voor het virtuele netwerk.  Als de DNS-configuratie om welke reden dan ook wordt gewijzigd nadat een App Service-omgeving is gemaakt, kunnen ontwikkelaars een App Service-omgeving dwingen om de nieuwe DNS-configuratie op te halen.  Als u een herstart van de rolling omgeving activeert met behulp van het pictogram Opnieuw starten boven aan het beheerblad voor het beheervan de appserviceomgeving in de portal, wordt de omgeving de nieuwe DNS-configuratie opgehaald.

Het wordt ook aanbevolen dat aangepaste DNS-servers op het vnet van tevoren worden ingesteld voordat u een App Service-omgeving maakt.  Als de DNS-configuratie van een virtueel netwerk wordt gewijzigd terwijl een App-serviceomgeving wordt gemaakt, wordt het proces voor het maken van de app-serviceomgeving mislukt.  Als er aan de andere kant van een VPN-gateway een aangepaste DNS-server bestaat en de DNS-server onbereikbaar of niet beschikbaar is, mislukt ook het proces voor het maken van de app-serviceomgeving.

## <a name="connecting-to-a-sql-server"></a>Verbinding maken met een SQL Server
Een algemene SQL Server-configuratie heeft een endpoint-luisteren op poort 1433:

![SQL Server-eindpunt][SqlServerEndpoint]

Er zijn twee benaderingen voor het beperken van het verkeer tot dit eindpunt:

* [Lijsten met netwerktoegangsbeheer][NetworkAccessControlLists] (NetwerkACL's)
* [Netwerkbeveiligingsgroepen][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Toegang beperken met een ACL netwerk
Poort 1433 kan worden beveiligd met behulp van een lijst met netwerktoegangscontrole.  In het voorbeeld hieronder worden clientadressen van gebruikers afkomstig van binnen uit een virtueel netwerk op de witte lijst geplaatst en wordt de toegang tot alle andere clients blokkeert.

![Voorbeeld van lijst met netwerktoegangsbeheer][NetworkAccessControlListExample]

Alle toepassingen die in de App-serviceomgeving in hetzelfde virtuele netwerk worden uitgevoerd als de SQL Server, kunnen verbinding maken met de SQL Server-instantie met behulp van het interne IP-adres **vnet** voor de virtuele SQL Server-machine.  

De voorbeeldverbindingstekenreeks hieronder verwijst naar de SQL Server met behulp van het privé-IP-adres.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Hoewel de virtuele machine ook een openbaar eindpunt heeft, worden verbindingspogingen met het openbare IP-adres geweigerd vanwege het netwerk ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Toegang beperken met een netwerkbeveiligingsgroep
Een alternatieve benadering voor het beveiligen van toegang is met een netwerkbeveiligingsgroep.  Netwerkbeveiligingsgroepen kunnen worden toegepast op afzonderlijke virtuele machines of op een subnet met virtuele machines.

Eerst moet er een netwerkbeveiligingsgroep worden gemaakt:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Het beperken van de toegang tot alleen VNet intern verkeer is zeer eenvoudig met een netwerkbeveiligingsgroep.  De standaardregels in een netwerkbeveiligingsgroep bieden alleen toegang toe van andere netwerkclients in hetzelfde virtuele netwerk.

Als gevolg hiervan is het vergrendelen van toegang tot SQL Server net zo eenvoudig als het toepassen van een netwerkbeveiligingsgroep met de standaardregels op de virtuele machines met SQL Server of het subnet met de virtuele machines.

In het onderstaande voorbeeld wordt een netwerkbeveiligingsgroep op het subnet met inhoud gebruikt:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

Het eindresultaat is een set beveiligingsregels die externe toegang blokkeren, terwijl VNet interne toegang toestaat:

![Standaardregels voor netwerkbeveiliging][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app-serviceomgeving][IntroToAppServiceEnvironment] om aan de slag te gaan met app-serviceomgeving

Zie [Binnenkomend verkeer naar een App-serviceomgeving beheren voor][ControlInboundASE] meer informatie over het beheren van binnenkomend verkeer naar uw app-serviceomgeving

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
