---
title: App integreren met Azure Virtual Network
description: Integreer apps in Azure App Service met Azure Virtual Networks.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673215"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Een app integreren met een virtueel Azure-netwerk
In dit document wordt de functie voor virtuele netwerkintegratie van Azure App Service beschreven en hoe u deze instellen met apps in de [Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714) [Met Azure Virtual Networks][VNETOverview] (VNets) u veel van uw Azure-bronnen in een niet-internetrouteerbaar netwerk plaatsen.  

De Azure App Service heeft twee varianten.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet-integratie inschakelen 

1. Ga naar de gebruikersinterface voor netwerken in de app-serviceportal. Selecteer onder VNet-integratie de optie *'Klik hier om te configureren'.* 

1. Selecteer **VNet toevoegen**.  

   ![VNet-integratie selecteren][1]

1. De vervolgkeuzelijst bevat alle VNets van Resource Manager in uw abonnement in dezelfde regio en daaronder is een lijst met alle VNets van Resource Manager in alle andere regio's. Selecteer het VNet waarmee u wilt integreren.

   ![Selecteer de VNet][2]

   * Als de VNet zich in dezelfde regio bevindt, maakt u een nieuw subnet of kiest u een leeg bestaand subnet. 

   * Als u een VNet in een andere regio wilt selecteren, moet u een gateway voor virtueel netwerk hebben die is ingericht met point-to-site ingeschakeld.

   * Als u wilt integreren met een Klassieke VNet, selecteert u **Klik hier om verbinding te maken met een Klassieke VNet**in plaats van op de vervolgkeuzelijst VNet te klikken. Selecteer de gewenste Classic VNet. Het doel VNet moet al een Virtual Network gateway hebben ingericht met point-to-site ingeschakeld.

    ![Klassieke VNet selecteren][3]
    
Tijdens de integratie wordt uw app opnieuw opgestart.  Wanneer de integratie is voltooid, ziet u details op de VNet waarmee u bent geïntegreerd. 

Zodra uw app is geïntegreerd met uw VNet, gebruikt deze dezelfde DNS-server waarmee uw VNet is geconfigureerd, tenzij het Azure DNS Private Zones is. U momenteel geen VNet-integratie gebruiken met Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>Regionale VNet-integratie

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Hoe regionale VNet-integratie werkt

Apps in de App-service worden gehost in werknemersrollen. De basis- en hogere prijsplannen zijn speciale hostingplannen waarbij er geen andere workloads van klanten zijn die op dezelfde werknemers worden uitgevoerd. Regionale VNet-integratie werkt door het monteren van virtuele interfaces met adressen in het gedelegeerde subnet. Omdat het van adres in uw VNet staat, kan het toegang krijgen tot de meeste dingen in of via uw VNet, net zoals een VM in uw VNet zou doen. De netwerkimplementatie is anders dan het uitvoeren van een VM in uw VNet en dat is de reden waarom sommige netwerkfuncties nog niet beschikbaar zijn tijdens het gebruik van deze functie.

![Hoe regionale VNet-integratie werkt][5]

Wanneer regionale VNet-integratie is ingeschakeld, voert uw app nog steeds uitgaande gesprekken naar het internet via dezelfde kanalen als normaal. De uitgaande adressen die worden vermeld in de app-eigenschappenportal zijn nog steeds de adressen die door uw app worden gebruikt. Wat wijzigingen voor uw app zijn, oproepen naar service endpoint beveiligde services of RFC 1918-adressen gaat naar uw VNet. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer naar uw VNet worden verzonden. 

De functie ondersteunt slechts één virtuele interface per werknemer.  Eén virtuele interface per werknemer betekent één regionaal VNet-integratieper App Service-abonnement. Alle apps in hetzelfde App Service-abonnement kunnen dezelfde VNet-integratie gebruiken, maar als u een app nodig hebt om verbinding te maken met een extra VNet, moet u een ander App Service-abonnement maken. De virtuele interface die wordt gebruikt, is geen bron waartoe klanten rechtstreeks toegang hebben.

Vanwege de aard van de werking van deze technologie wordt het verkeer dat wordt gebruikt met VNet Integration niet weergegeven in Network Watcher of NSG flow logs.  

## <a name="gateway-required-vnet-integration"></a>Vereiste VNet-gateway-integratie

Gateway vereist VNet Integration ondersteunt het verbinden met een VNet in een andere regio, of met een Classic VNet. Vereiste VNet-integratie met gateway: 

* Hiermee kan een app verbinding maken met slechts 1 VNet tegelijk
* Hiermee kunnen maximaal vijf VNets worden geïntegreerd in een App Service Plan 
* Hiermee kan dezelfde VNet door meerdere apps in een App-serviceplan worden gebruikt zonder dat dit gevolgen heeft voor het totale aantal dat door een App Service-abonnement kan worden gebruikt.  Als u zes apps hebt die hetzelfde VNet gebruiken in hetzelfde App Service-abonnement, telt dat als 1 VNet die wordt gebruikt. 
* Ondersteunt een SLA van 99,9% dankzij de SLA op de gateway
* Stelt uw apps in staat om de DNS te gebruiken waarmee de VNet is geconfigureerd
* Vereist een op een virtual network-route gebaseerde gateway die is geconfigureerd met SSTP point-to-site VPN voordat deze kan worden verbonden met de app. 

U de vereiste VNet-integratie met gatewayniet gebruiken:

* Met Linux-apps
* Met een VNet verbonden met ExpressRoute 
* Beveiligde bronnen voor serviceeindpunten openen
* Met een coëxistentiegateway die zowel ExpressRoute als point-to-site/site-naar-site VPN's ondersteunt

### <a name="set-up-a-gateway-in-your-vnet"></a>Een gateway instellen in uw VNet ###

Ga als lid van het nieuws als een gateway:

1. [Maak een gatewaysubnet][creategatewaysubnet] in uw VNet.  

1. [Maak de VPN-gateway.][creategateway] Selecteer een vpn-type op route.

1. [Stel het punt in op siteadressen][setp2saddresses]. Als de gateway zich niet in de basis-SKU bevindt, moet IKEV2 worden uitgeschakeld in de punt-naar-siteconfiguratie en moet SSTP worden geselecteerd. De adresruimte van punt naar locatie moet zich in de adresblokken van RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Als u alleen de gateway maakt voor gebruik met App Service VNet-integratie, hoeft u geen certificaat te uploaden. Het maken van de gateway kan 30 minuten duren. U uw app pas integreren met uw VNet als de gateway is ingericht. 

### <a name="how-gateway-required-vnet-integration-works"></a>Hoe gateway vereist VNet-integratie werkt

Gateway vereist VNet-integratie gebouwd op de top van point-to-site VPN-technologie. Point to site VPN's beperkt de netwerktoegang tot alleen de virtuele machine die de app host. Apps zijn beperkt tot het verzenden van verkeer naar het internet, via hybride verbindingen of via VNet-integratie. Wanneer uw app is geconfigureerd met de portal om de vereiste VNet-integratie met gateway te gebruiken, wordt namens u een complexe onderhandeling beheerd om certificaten aan de gateway- en toepassingszijde te maken en toe te wijzen. Het eindresultaat is dat de werknemers die worden gebruikt om uw apps te hosten, rechtstreeks verbinding kunnen maken met de virtuele netwerkgateway in het geselecteerde VNet. 

![Hoe gateway vereist VNet-integratie werkt][6]

### <a name="accessing-on-premises-resources"></a>Toegang tot on-premises resources

Apps hebben toegang tot on-premises bronnen door te integreren met VNets die site-to-site-verbindingen hebben. Als u de vereiste VNet-gateway gebruikt, moet u uw on-premises VPN-gatewayroutes bijwerken met uw point-to-site adresblokken. Wanneer de site-to-site VPN voor het eerst is ingesteld, moeten de scripts die worden gebruikt om deze te configureren, routes correct instellen. Als u de point-to-site-adressen toevoegt nadat u uw site-to-site VPN hebt gemaakt, moet u de routes handmatig bijwerken. Details over hoe dat te doen variëren per gateway en worden hier niet beschreven. U BGP niet laten configureren met een site-to-site VPN-verbinding.

Er is geen extra configuratie vereist voor de regionale VNet-integratiefunctie om via uw VNet en on-premises te bereiken. U hoeft alleen maar uw VNet aan te sluiten op on-premises via ExpressRoute of een site-to-site VPN. 

> [!NOTE]
> De vereiste VNet-integratiefunctie voor gateway integreert een app niet met een VNet met een ExpressRoute-gateway. Zelfs als de ExpressRoute Gateway is geconfigureerd in [coëxistentiemodus,][VPNERCoex] werkt de VNet-integratie niet. Als u toegang nodig hebt tot bronnen via een ExpressRoute-verbinding, u de regionale VNet-integratiefunctie of een [App Service-omgeving][ASE]gebruiken die wordt uitgevoerd in uw VNet. 
> 
> 

### <a name="peering"></a>Peering

Als u peering gebruikt met de regionale VNet-integratie, hoeft u geen extra configuratie uit te stellen. 

Als u de vereiste VNet-integratie met gateway gebruikt met peering, moet u een paar extra items configureren. Ga als het gaat om het configureren van peering om met uw app te werken:

1. Voeg een peering-verbinding toe aan de VNet waar uw app verbinding mee maakt. Schakel bij het toevoegen van de peering-verbinding **virtuele netwerktoegang toestaan in** en schakelt Doorgestuurd verkeer **toestaan** en **Gatewaytransit toestaan**.
1. Voeg een peering-verbinding toe aan het VNet die wordt peered naar het VNet waarmee u bent verbonden. Wanneer u de peeringverbinding toevoegt aan de bestemming VNet, schakelt **u Virtuele netwerktoegang toestaan in** en schakelt u Doorgestuurd verkeer **toestaan** en **Externe gateways toestaan**.
1. Ga naar het App Service-abonnement > Netwerk> VNet-integratie-gebruikersinterface in de portal.  Selecteer de VNet waar toe uw app verbinding maakt. Voeg onder de routeringssectie het adresbereik van het VNet toe dat is gekoppeld aan de VNet waarmee uw app is verbonden.  

## <a name="managing-vnet-integration"></a>VNet-integratie beheren 

Verbinding maken en loskoppelen met een VNet is op app-niveau. Bewerkingen die van invloed kunnen zijn op de VNet-integratie in meerdere apps, bevinden zich op het planniveau van App Service. Vanuit de app > Networking > VNet Integration portal, u meer informatie op uw VNet. Vergelijkbare informatie u op ASP-niveau zien in de ASP->-netwerkportal > VNet-integratie.

De enige bewerking die u uitvoeren in de app-weergave van uw VNet-integratie is om uw app los te koppelen van het VNet waaraan deze momenteel is verbonden. Als u de verbinding met uw app met een VNet wilt **verbreken,** selecteert u Verbinding verbreken . Uw app wordt opnieuw gestart wanneer u de verbinding met een VNet verbreekt. De loskoppeling verandert uw VNet niet. Het subnet of de gateway wordt niet verwijderd. Als u vervolgens uw VNet wilt verwijderen, moet u eerst de verbinding met uw app verbreken van het VNet en de bronnen verwijderen die erin zitten, zoals gateways. 

De ASP VNet Integration UI toont u alle VNet-integraties die worden gebruikt door de apps in uw ASP. Om details op elke VNet te zien, klik je op het VNet waarin je geïnteresseerd bent. Er zijn twee acties die u hier uitvoeren voor de vereiste VNet-integratie met de gateway.

* **Netwerk synchroniseren**. De synchronisatienetwerkbewerking is alleen voor de functie VNet-integratie die afhankelijk is van de gateway. Het uitvoeren van een synchronisatienetwerkbewerking zorgt ervoor dat uw certificaten en netwerkgegevens gesynchroniseerd zijn. Als u de DNS van uw VNet toevoegt of wijzigt, moet u een **synchronisatienetwerkbewerking** uitvoeren. Deze bewerking start alle apps opnieuw met behulp van deze VNet.
* **Routes toevoegen** Als u routes toevoegt, wordt uitgaand verkeer naar uw VNet gebracht. 

**Vereiste VNet-integratieroutering voor gateway** De routes die in uw VNet worden gedefinieerd, worden gebruikt om verkeer vanuit uw app naar uw VNet te leiden. Als u extra uitgaand verkeer naar het VNet moet verzenden, u deze adresblokken hier toevoegen. Deze mogelijkheid werkt alleen met gateway vereist VNet-integratie. Routetabellen hebben geen invloed op uw app-verkeer bij het gebruik van de vereiste VNet-integratie met de gateway zoals ze dat doen met regionale VNet-integratie.

**Vereiste VNet-integratiecertificaten voor gateway** Wanneer de gateway vereist VNet Integration ingeschakeld, is er een vereiste uitwisseling van certificaten om de veiligheid van de verbinding te waarborgen. Samen met de certificaten zijn de DNS-configuratie, routes en andere soortgelijke dingen die het netwerk beschrijven.
Als certificaten of netwerkgegevens worden gewijzigd, moet u op 'Netwerk synchroniseren' klikken. Wanneer u op 'Netwerk synchroniseren' klikt, veroorzaakt u een korte onderbreking in de connectiviteit tussen uw app en uw VNet. Hoewel uw app niet opnieuw is gestart, kan het verlies van connectiviteit ertoe leiden dat uw site niet goed functioneert. 

## <a name="pricing-details"></a>Prijsdetails
De regionale VNet-integratiefunctie heeft geen extra kosten voor gebruik buiten de ASP-prijstarieven.

Er zijn drie gerelateerde kosten voor het gebruik van de vereiste VNet-integratiefunctie:

* ASP-prijstarieven - Uw apps moeten in een standaard-, Premium- of Premium-appserviceabonnement zijn. Meer informatie over deze kosten vindt u hier: [App Service Pricing][ASPricing]. 
* Kosten voor gegevensoverdracht - Er zijn kosten verbonden aan gegevensuitgangen, zelfs als het VNet zich in hetzelfde datacenter bevindt. Deze kosten worden beschreven in [de details van de verrekenprijzen][DataPricing]. 
* VPN-gatewaykosten - Er zijn kosten verbonden aan de VNet-gateway die nodig zijn voor de point-to-site VPN. De details staan op de [VPN Gateway Pricing-pagina.][VNETPricing]

## <a name="troubleshooting"></a>Problemen oplossen

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Er is CLI-ondersteuning voor regionale VNet-integratie. Installeer [Azure CLI][installCLI]voor toegang tot de volgende opdrachten. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Voor vnet-integratie waarvoor gateway vereist is, u App Service integreren met een Azure Virtual Network met PowerShell. Zie Een app in Azure [App Service verbinden met een Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)voor een kant-en-klaar script.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
