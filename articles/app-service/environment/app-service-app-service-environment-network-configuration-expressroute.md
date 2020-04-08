---
title: Azure ExpressRoute v1 configureren
description: Netwerkconfiguratie voor App Service-omgeving voor PowerApps met Azure ExpressRoute. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fc11c6932d625b119ad933f5d4d128b4355530c5
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804432"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Netwerkconfiguratiegegevens voor App-serviceomgeving voor PowerApps met Azure ExpressRoute

Klanten kunnen een [Azure ExpressRoute-circuit][ExpressRoute] verbinden met hun virtuele netwerkinfrastructuur om hun on-premises netwerk uit te breiden naar Azure. App Service Environment wordt gemaakt in een subnet van de [virtuele netwerkinfrastructuur.][virtualnetwork] Apps die worden uitgevoerd op de App-serviceomgeving, zorgen voor beveiligde verbindingen met back-endbronnen die alleen toegankelijk zijn via de ExpressRoute-verbinding.  

App Service Omgeving kan worden gemaakt in deze scenario's:
- Virtuele netwerken van Azure Resource Manager.
- Klassieke implementatiemodel virtuele netwerken.
- Virtuele netwerken die gebruikmaken van adressen bereiken van het publiek of RFC1918 adresruimten (dat wil zeggen, prive-adressen). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Vereiste netwerkconnectiviteit

App Service Environment heeft netwerkconnectiviteitsvereisten waaraan in eerste instantie mogelijk niet wordt voldaan in een virtueel netwerk dat is verbonden met ExpressRoute.

Voor de app-serviceomgeving moeten de volgende netwerkverbindingsinstellingen goed functioneren:

* Uitgaande netwerkconnectiviteit met Azure Storage-eindpunten wereldwijd op zowel poort 80 als poort 443. Deze eindpunten bevinden zich in dezelfde regio als de App Service-omgeving en ook andere Azure-regio's. Azure Storage-eindpunten worden opgelost onder de volgende DNS-domeinen: table.core.windows.net, blob.core.windows.net, queue.core.windows.net en file.core.windows.net.  

* Uitgaande netwerkconnectiviteit met de Azure Files-service op poort 445.

* Uitgaande netwerkconnectiviteit met Azure SQL Database-eindpunten die zich in dezelfde regio bevinden als de App Service-omgeving. SQL Database-eindpunten worden opgelost onder het database.windows.net-domein, waarvoor open toegang tot poorten 1433, 11000-11999 en 14000-14999 vereist is. Zie [Poorten na 1433 voor ADO.NET 4.5 voor](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)meer informatie over het gebruik van de SQL Database V12-poort.

* Uitgaande netwerkconnectiviteit met de eindpunten Azure-beheervlak (zowel het klassieke implementatiemodel van Azure als azure resourcebeheereindpunten). Connectiviteit met deze eindpunten omvat de management.core.windows.net en management.azure.com domeinen. 

* Uitgaande netwerkconnectiviteit met de domeinen ocsp.msocsp.com, mscrl.microsoft.com en crl.microsoft.com. Connectiviteit met deze domeinen is nodig om TLS-functionaliteit te ondersteunen.

* De DNS-configuratie voor het virtuele netwerk moet alle eindpunten en domeinen die in dit artikel worden genoemd, kunnen oplossen. Als de eindpunten niet kunnen worden opgelost, mislukt het maken van de app-serviceomgeving. Elke bestaande App-serviceomgeving is gemarkeerd als ongezond.

* Uitgaande toegang op poort 53 is vereist voor communicatie met DNS-servers.

* Als er een aangepaste DNS-server bestaat aan de andere kant van een VPN-gateway, moet de DNS-server bereikbaar zijn vanaf het subnet dat de App Service-omgeving bevat. 

* Het uitgaande netwerkpad kan niet door interne bedrijfsproxy's reizen en kan niet worden gedwongen op locatie. Met deze acties wordt het effectieve NAT-adres van uitgaand netwerkverkeer van app-serviceomgeving gewijzigd. Wijzigingen in het NAT-adres van uitgaand netwerkverkeer van app-serviceomgeving veroorzaken verbindingsfouten in veel eindpunten. App Service Environment creation mislukt. Elke bestaande App-serviceomgeving is gemarkeerd als ongezond.

* Inkomende netwerktoegang tot vereiste poorten voor App Service-omgeving moet worden toegestaan. Zie [Binnenkomend verkeer naar app-serviceomgeving beheren voor][requiredports]meer informatie.

Als u aan de DNS-vereisten wilt voldoen, controleert u of een geldige DNS-infrastructuur is geconfigureerd en onderhouden voor het virtuele netwerk. Als de DNS-configuratie wordt gewijzigd nadat de App Service-omgeving is gemaakt, kunnen ontwikkelaars de app-serviceomgeving dwingen om de nieuwe DNS-configuratie op te halen. U een reboot van een rolling omgeving activeren met het pictogram **Opnieuw starten** onder App Service-omgevingbeheer in de [Azure-portal.][NewPortal] De reboot zorgt ervoor dat de omgeving de nieuwe DNS-configuratie oppikt.

Als u wilt voldoen aan de vereisten voor binnenkomende netwerktoegang, configureert u een [netwerkbeveiligingsgroep (NSG)][NetworkSecurityGroups] op het subnet App Service Environment. De NSG biedt de vereiste toegang [tot het inkomende verkeer tot de App Service-omgeving.][requiredports]

## <a name="outbound-network-connectivity"></a>Uitgaande netwerkconnectiviteit

Standaard adverteert een nieuw gemaakt ExpressRoute-circuit een standaardroute die uitgaande internetverbinding mogelijk maakt. App Service-omgeving kan deze configuratie gebruiken om verbinding te maken met andere Azure-eindpunten.

Een gemeenschappelijke klantconfiguratie is het definiëren van hun eigen standaardroute (0.0.0.0/0), waardoor uitgaand internetverkeer on-premises kan stromen. Deze verkeersstroom breekt steevast App Service Environment. Het uitgaande verkeer wordt geblokkeerd op locatie of NAT'd naar een onherkenbare set adressen die niet meer werken met verschillende Azure-eindpunten.

De oplossing is om een (of meer) door de gebruiker gedefinieerde routes (UDR's) te definiëren op het subnet dat de App Service-omgeving bevat. Een UDR definieert subnetspecifieke routes die worden gehonoreerd in plaats van de standaardroute.

Gebruik indien mogelijk de volgende configuratie:

* De ExpressRoute-configuratie adverteert 0.0.0.0/0. Standaard worden de configuratietroepen alle uitgaande verkeer on-premises tunnels.
* De UDR toegepast op het subnet dat App Service Environment bevat definieert 0.0.0.0/0 met een volgende hop type internet. Een voorbeeld van deze configuratie wordt later in dit artikel beschreven.

Het gecombineerde effect van deze configuratie is dat de subnet-level UDR voorrang heeft op de ExpressRoute kracht tunneling. Uitgaande internettoegang vanuit de App Service-omgeving is gegarandeerd.

> [!IMPORTANT]
> De routes die in een UDR zijn gedefinieerd, moeten specifiek genoeg zijn om voorrang te krijgen op routes die worden geadverteerd door de ExpressRoute-configuratie. In het voorbeeld dat in de volgende sectie wordt beschreven, wordt het brede adresbereik 0,0 0 0/0 gebruikt. Dit bereik kan per ongeluk worden overschreven door routeadvertenties die specifiekere adresbereiken gebruiken.
> 
> App Service Environment wordt niet ondersteund met ExpressRoute-configuraties die routes cross-advertiseeren van het openbare peeringpad naar het privé-peeringpad. ExpressRoute-configuraties met geconfigureerd voor openbare peering ontvangen routeadvertenties van Microsoft voor een grote set Microsoft Azure-IP-adresbereiken. Als deze adresbereiken worden geadverteerd op het privé-peeringpad, worden alle uitgaande netwerkpakketten van het subnet App Service Environment gedwongen getunneld naar de on-premises netwerkinfrastructuur van de klant. Deze netwerkstroom wordt momenteel niet ondersteund met App Service Environment. Een oplossing is om cross-advertising routes te stoppen van de openbare peering pad naar de prive-peering pad.
> 
> 

Zie [Routering van virtueel netwerkverkeer][UDROverview]voor achtergrondinformatie over door gebruikers gedefinieerde routes.  

Zie [Netwerkverkeer routeren met een routetabel met PowerShell][UDRHowTo]voor meer informatie over het maken en configureren van door gebruikers gedefinieerde routes.

## <a name="udr-configuration"></a>UDR-configuratie

In deze sectie ziet u een voorbeeld-UDR-configuratie voor app-serviceomgeving.

### <a name="prerequisites"></a>Vereisten

* Installeer Azure PowerShell vanaf de [pagina Azure Downloads][AzureDownloads]. Kies een download met een datum van juni 2015 of hoger. Selecteer onder **Command-line-tools** > **Windows PowerShell**de optie **Installeren** om de nieuwste PowerShell-cmdlets te installeren.

* Maak een uniek subnet voor exclusief gebruik door App Service Environment. Het unieke subnet zorgt ervoor dat de UDR's alleen worden toegepast op het subnet open uitgaand verkeer voor App Service Environment.

> [!IMPORTANT]
> Implementeer de app-serviceomgeving alleen nadat u de configuratiestappen hebt voltooid. De stappen zorgen ervoor dat uitgaande netwerkconnectiviteit beschikbaar is voordat u de app-serviceomgeving probeert te implementeren.

### <a name="step-1-create-a-route-table"></a>Stap 1: Een routetabel maken

Maak een routetabel met de naam **DirectInternetRouteTable** in de Azure-regio West US, zoals in dit fragment wordt weergegeven:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Stap 2: Routes maken in de tabel

Voeg routes toe aan de routetabel om uitgaande internettoegang in te schakelen.  

U uitgaande toegang tot internet configureren. Definieer een route voor 0.0.0.0/0 zoals weergegeven in dit fragment:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 is een breed adresbereik. Het bereik wordt overschreven door adresbereiken die worden geadverteerd door ExpressRoute die specifieker zijn. Een UDR met een route 0.0.0.0/0 moet worden gebruikt in combinatie met een ExpressRoute-configuratie die alleen 0.0.0.0/0 adverteert. 

Als alternatief u een actuele, uitgebreide lijst met CIDR-bereiken downloaden die door Azure worden gebruikt. Het XML-bestand voor alle Azure IP-adresbereiken is beschikbaar in het [Microsoft Download Center.][DownloadCenterAddressRanges]  

> [!NOTE]
>
> De Azure IP-adresbereiken veranderen in de loop van de tijd. Door de gebruiker gedefinieerde routes hebben periodieke handmatige updates nodig om synchroon te blijven.
>
> Een enkele UDR heeft een standaard bovengrens van 100 routes. U moet de Azure IP-adresbereiken samenvatten om binnen de limiet van 100-route te passen. Door UDR gedefinieerde routes moeten specifieker zijn dan routes die worden geadverteerd door uw ExpressRoute-verbinding.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Stap 3: De tabel koppelen aan het subnet

Koppel de routetabel aan het subnet waar u de app-serviceomgeving wilt implementeren. Met deze opdracht wordt de tabel **DirectInternetRouteTable** aan het **ASESubnet-subnet** met de app-serviceomgeving verbonden.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Stap 4: Test en bevestig de route

Nadat de routetabel is gebonden aan het subnet, test en bevestig t/m de route.

Implementeer een virtuele machine in het subnet en bevestig deze voorwaarden:

* Uitgaand verkeer naar de Azure- en niet-Azure-eindpunten die in dit artikel worden beschreven, stroomt **niet** over het ExpressRoute-circuit. Als uitgaand verkeer van het subnet on-premises wordt getunneld, mislukt app-serviceomgeving altijd.
* DNS-lookups voor de eindpunten die in dit artikel worden beschreven, worden allemaal goed opgelost. 

Nadat u de configuratiestappen hebt voltooid en de route hebt bevestigd, verwijdert u de virtuele machine. Het subnet moet 'leeg' zijn wanneer de App Service-omgeving wordt gemaakt.

Nu bent u klaar om app-serviceomgeving te implementeren!

## <a name="next-steps"></a>Volgende stappen

Zie [Inleiding tot de app-serviceomgeving][IntroToAppServiceEnvironment]om aan de slag te gaan met app-serviceomgeving.

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
