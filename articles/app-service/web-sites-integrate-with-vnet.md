---
title: App integreren met Azure Virtual Network
description: Integreer de app in Azure App Service met virtuele Azure-netwerken.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4866397af244ffb3c6aa9c7547b0a9413b10ccfd
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604863"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Uw app integreren met een virtueel Azure-netwerk

In dit artikel wordt de VNet-integratiefunctie van Azure App Service beschreven en hoe u deze instellen met apps in [Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714) Met [Azure Virtual Network][VNETOverview] (VNets) u veel van uw Azure-bronnen in een niet-internetrouteerbaar netwerk plaatsen.

Azure App Service heeft twee varianten:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet-integratie inschakelen

1. Ga naar de **gebruikersinterface voor netwerken** in de app-serviceportal. Selecteer onder **VNet-integratie** **Klik hier om te configureren**.

1. Selecteer **VNet toevoegen**.

   ![VNet-integratie selecteren][1]

1. De vervolgkeuzelijst bevat alle virtuele Azure Resource Manager-netwerken in uw abonnement in dezelfde regio. Daaronder is een lijst van de Resource Manager virtuele netwerken in alle andere regio's. Selecteer het VNet waarmee u wilt integreren.

   ![Selecteer de VNet][2]

   * Als de VNet zich in dezelfde regio bevindt, maakt u een nieuw subnet of selecteert u een leeg bestaand subnet.
   * Als u een VNet in een andere regio wilt selecteren, moet u een VNet-gateway hebben ingericht met point-to-site ingeschakeld.
   * Als u wilt integreren met een klassieke VNet, selecteert u in plaats van de vervolgkeuzelijst **Virtueel netwerk** **te selecteren, klik hier om verbinding te maken met een klassieke VNet.** Selecteer het gewenste klassieke virtuele netwerk. Het doel VNet moet al een Virtual Network gateway hebben ingericht met point-to-site ingeschakeld.

    ![Klassieke VNet selecteren][3]

Tijdens de integratie wordt uw app opnieuw opgestart. Wanneer de integratie is voltooid, ziet u details op het VNet waarmee u bent geïntegreerd.

## <a name="regional-vnet-integration"></a>Regionale VNet-integratie

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Hoe regionale VNet-integratie werkt

Apps in App-service worden gehost in werknemersrollen. De basis- en hogere prijsplannen zijn speciale hostingplannen waarbij er geen workloads van andere klanten zijn die op dezelfde werknemers worden uitgevoerd. Regionale VNet-integratie werkt door het monteren van virtuele interfaces met adressen in het gedelegeerde subnet. Omdat het van adres in uw VNet staat, kan het toegang krijgen tot de meeste dingen in of via uw VNet zoals een VM in uw VNet zou doen. De netwerkimplementatie is anders dan het uitvoeren van een VM in uw VNet. Daarom zijn sommige netwerkfuncties nog niet beschikbaar voor deze functie.

![Hoe regionale VNet-integratie werkt][5]

Wanneer regionale VNet-integratie is ingeschakeld, voert uw app uitgaande gesprekken naar het internet via dezelfde kanalen als normaal. De uitgaande adressen die worden vermeld in de app-eigenschappenportal zijn de adressen die nog steeds door uw app worden gebruikt. Wat verandert er voor uw app zijn de calls to service endpoint secured services, of RFC 1918 adressen gaan naar uw VNet. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer naar uw VNet worden verzonden.

De functie ondersteunt slechts één virtuele interface per werknemer. Eén virtuele interface per werknemer betekent één regionaal VNet-integratieper App Service-abonnement. Alle apps in hetzelfde App Service-abonnement kunnen dezelfde VNet-integratie gebruiken. Als u een app nodig hebt om verbinding te maken met een extra VNet, moet u een ander App Service-abonnement maken. De virtuele interface die wordt gebruikt, is geen bron waarklanten rechtstreeks toegang toe hebben.

Vanwege de aard van de manier waarop deze technologie werkt, wordt het verkeer dat wordt gebruikt met VNet-integratie niet weergegeven in Azure Network Watcher of NSG-stroomlogboeken.

## <a name="gateway-required-vnet-integration"></a>VNet-integratie met gatewayvereist

Door gateway vereist VNet-integratie ondersteunt het verbinden met een VNet in een andere regio of met een klassiek virtueel netwerk. VNet-integratie met gatewayvereist:

* Hiermee kan een app verbinding maken met slechts één VNet tegelijk.
* Hiermee kunnen maximaal vijf VNets worden geïntegreerd in een App Service-abonnement.
* Hiermee kan dezelfde VNet door meerdere apps in een App Service-abonnement worden gebruikt zonder dat dit gevolgen heeft voor het totale aantal dat door een App Service-abonnement kan worden gebruikt. Als u zes apps hebt die hetzelfde VNet gebruiken in hetzelfde App Service-abonnement, telt dat als één VNet dat wordt gebruikt.
* Ondersteunt een SLA van 99,9% vanwege de SLA op de gateway.
* Hiermee kunnen uw apps de DNS gebruiken waarmee de VNet is geconfigureerd.
* Vereist een op een virtual network-route gebaseerde gateway die is geconfigureerd met een SSTP-point-to-site VPN voordat deze kan worden verbonden met een app.

U vnet-integratie die vereist is voor poorten niet gebruiken:

* Met Linux apps.
* Met een VNet verbonden met Azure ExpressRoute.
* Toegang tot serviceeindpuntbeveiligde resources.
* Met een coëxistentiegateway die zowel ExpressRoute als point-to-site of site-to-site VPN's ondersteunt.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Een gateway instellen in uw virtuele Azure-netwerk ###

Ga als lid van het nieuws als een gateway:

1. [Maak een gatewaysubnet][creategatewaysubnet] in uw VNet.  

1. [Maak de VPN-gateway.][creategateway] Selecteer een vpn-type op route.

1. [Stel de point-to-site adressen in.][setp2saddresses] Als de gateway zich niet in de basis-SKU bevindt, moet IKEV2 worden uitgeschakeld in de point-to-site-configuratie en moet SSTP worden geselecteerd. De adresruimte van punt tot locatie moet zich in de adresblokken RFC 1918 10.0.0.0/8, 172.16.0.0/12 en 192.168.0.0/16 bevindt.

Als u de gateway maakt voor gebruik met VNet-integratie van appservice, hoeft u geen certificaat te uploaden. Het maken van de gateway kan 30 minuten duren. U uw app pas integreren met uw VNet als de gateway is ingericht.

### <a name="how-gateway-required-vnet-integration-works"></a>Hoe vnet-integratie vereist door gateway's werkt

Door gateway vereist VNet-integratie is gebouwd op de top van point-to-site VPN-technologie. Point-to-site VPN's beperken de netwerktoegang tot de virtuele machine die de app host. Apps zijn beperkt om verkeer alleen via hybride verbindingen of via VNet-integratie naar het internet te verzenden. Wanneer uw app is geconfigureerd met de portal om vnet-integratie die vereist is voor gateway te gebruiken, wordt namens u een complexe onderhandeling beheerd om certificaten aan de gateway- en toepassingszijde te maken en toe te wijzen. Het resultaat is dat de werknemers die worden gebruikt om uw apps te hosten, rechtstreeks verbinding kunnen maken met de virtuele netwerkgateway in het geselecteerde VNet.

![Hoe vnet-integratie vereist door gateway's werkt][6]

### <a name="access-on-premises-resources"></a>Toegang tot on-premises bronnen

Apps hebben toegang tot on-premises bronnen door te integreren met VNets die site-to-site-verbindingen hebben. Als u gebruikmaakt van door de gateway vereiste VNet-integratie, werkt u uw on-premises VPN-gatewayroutes bij met uw point-to-site adresblokken. Wanneer de site-to-site VPN voor het eerst is ingesteld, moeten de scripts die worden gebruikt om deze te configureren, routes correct instellen. Als u de point-to-site-adressen toevoegt nadat u uw site-to-site VPN hebt gemaakt, moet u de routes handmatig bijwerken. Details over hoe dat te doen variëren per gateway en worden hier niet beschreven. U BGP niet laten configureren met een site-to-site VPN-verbinding.

Er is geen extra configuratie vereist voor de regionale VNet-integratiefunctie om via uw VNet te bereiken naar on-premises resources. U hoeft alleen maar uw VNet te verbinden met on-premises bronnen met behulp van ExpressRoute of een site-to-site VPN.

> [!NOTE]
> De door de gateway vereiste VNet-integratiefunctie integreert een app niet met een VNet met een ExpressRoute-gateway. Zelfs als de ExpressRoute-gateway is geconfigureerd in [coëxistentiemodus,][VPNERCoex]werkt de VNet-integratie niet. Als u toegang wilt tot bronnen via een ExpressRoute-verbinding, gebruikt u de regionale VNet-integratiefunctie of een [App-serviceomgeving][ASE]die in uw VNet wordt uitgevoerd.
> 
> 

### <a name="peering"></a>Peering

Als u peering gebruikt met de regionale VNet-integratie, hoeft u geen extra configuratie uit te stellen.

Als u door de gateway vereiste VNet-integratie met peering gebruikt, moet u een paar extra items configureren. Ga als het gaat om het configureren van peering om met uw app te werken:

1. Voeg een peering-verbinding toe aan de VNet waar uw app verbinding mee maakt. Wanneer u de peeringverbinding toevoegt, schakelt **u Virtuele netwerktoegang toestaan in** en selecteert u Doorgestuurd verkeer **toestaan** en **Gatewaytransit toestaan**.
1. Voeg een peering-verbinding toe aan het VNet die wordt peered naar het VNet waarmee u bent verbonden. Wanneer u de peeringverbinding toevoegt aan de bestemming VNet, schakelt **u Virtuele netwerktoegang toestaan in** en selecteert u Doorgestuurd verkeer **toestaan** en **Externe gateways toestaan**.
1. Ga naar het **App Service-abonnement** > **Networking** > **VNet Integration** UI in de portal. Selecteer de VNet waar toe uw app verbinding maakt. Voeg onder de routeringssectie het adresbereik van het VNet toe dat is gekoppeld aan de VNet waarmee uw app is verbonden.

## <a name="manage-vnet-integration"></a>VNet-integratie beheren

Verbinding maken en loskoppelen met een VNet is op app-niveau. Bewerkingen die van invloed kunnen zijn op VNet-integratie in meerdere apps bevinden zich op het planniveau van App Service. Vanuit de app > **Networking** > **VNet Integration** portal, u meer informatie op uw VNet. U vergelijkbare informatie zien op het planniveau van app-servicein-het-app-abonnement **App Service plan** > **Networking** > **VNet** Integration-portal.

De enige bewerking die u uitvoeren in de app-weergave van uw VNet-integratie-exemplaar, is door uw app los te koppelen van het VNet waaraan deze momenteel is verbonden. Als u de verbinding met uw app met een VNet wilt **verbreken,** selecteert u Verbinding verbreken . Uw app wordt opnieuw gestart wanneer u de verbinding met een VNet verbreekt. De loskoppeling verandert uw VNet niet. Het subnet of de gateway wordt niet verwijderd. Als u vervolgens uw VNet wilt verwijderen, koppelt u de verbinding met uw app van het VNet en verwijdert u de bronnen die erin zijn, zoals gateways.

De VNet-integratie-gebruikersinterface van App Service toont u alle VNet-integraties die door de apps in uw App Service-abonnement worden gebruikt. Als u details op elke VNet wilt zien, selecteert u het VNet waarin u geïnteresseerd bent. Er zijn twee acties die u hier uitvoeren voor vnet-integratie die vereist is voor gateway:

* **Synchronisatienetwerk**: De bewerking van het synchronisatienetwerk wordt alleen gebruikt voor de functie VNet-integratie die afhankelijk is van de gateway. Het uitvoeren van een synchronisatienetwerkbewerking zorgt ervoor dat uw certificaten en netwerkgegevens gesynchroniseerd zijn. Als u de DNS van uw VNet toevoegt of wijzigt, voert u een synchronisatienetwerkbewerking uit. Met deze bewerking worden alle apps opnieuw gestart die deze VNet gebruiken.
* **Routes toevoegen:** Het toevoegen van routes zorgt voor uitgaand verkeer in uw VNet.

### <a name="gateway-required-vnet-integration-routing"></a>Door gateway vereiste VNet-integratieroutering
De routes die in uw VNet worden gedefinieerd, worden gebruikt om verkeer vanuit uw app naar uw VNet te leiden. Als u extra uitgaand verkeer naar het VNet wilt verzenden, voegt u deze adresblokken hier toe. Deze mogelijkheid werkt alleen met door de gateway vereiste VNet-integratie. Routetabellen hebben geen invloed op uw app-verkeer wanneer u vnet-integratie die vereist is voor gateway's gebruikt, zoals ze dat doen met regionale VNet-integratie.

### <a name="gateway-required-vnet-integration-certificates"></a>VNet-integratiecertificaten die vereist zijn voor gateways
Wanneer vnet-integratie met gateway vereist is ingeschakeld, is er een vereiste uitwisseling van certificaten om de beveiliging van de verbinding te garanderen. Samen met de certificaten zijn de DNS-configuratie, routes en andere soortgelijke dingen die het netwerk beschrijven.

Als certificaten of netwerkgegevens worden gewijzigd, selecteert u **Netwerk synchroniseren**. Wanneer u **Synchronisatienetwerk selecteert,** veroorzaakt u een korte onderbreking in de connectiviteit tussen uw app en uw VNet. Hoewel uw app niet opnieuw is gestart, kan het verlies van connectiviteit ertoe leiden dat uw site niet goed functioneert.

## <a name="pricing-details"></a>Prijsdetails
De regionale VNet-integratiefunctie heeft geen extra kosten voor gebruik buiten de prijstarieven voor het App Service-abonnement.

Drie kosten zijn gerelateerd aan het gebruik van de door de gateway vereiste VNet-integratiefunctie:

* **Prijzentarief tarieftarief voor App Service-abonnement**: uw apps moeten een standaard-, Premium- of Premium-appserviceabonnement hebben. Zie [App Service-prijzen][ASPricing]voor meer informatie over deze kosten.
* **Kosten voor gegevensoverdracht**: Er worden kosten in rekening gebracht voor gegevensdie uitdeinen, zelfs als het VNet zich in hetzelfde datacenter bevindt. Deze kosten worden beschreven in [de prijsdetails van Data Transfer][DataPricing].
* **VPN-gatewaykosten**: Er zijn kosten verbonden aan de virtuele netwerkgateway die nodig is voor de point-to-site VPN. Zie [VPN-gatewayprijzen][VNETPricing]voor meer informatie.

## <a name="troubleshooting"></a>Problemen oplossen

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI-ondersteuning is beschikbaar voor regionale VNet-integratie. Als u toegang wilt krijgen tot de volgende opdrachten, [installeert u de Azure CLI][installCLI].

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

Voor vnet-integratie die vereist is voor poorten, u App Service integreren met een Virtueel Azure-netwerk met PowerShell. Zie Een app in Azure [App Service verbinden met een virtueel Azure-netwerk voor](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)een kant-en-klaar script.


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
[privateendpoints]: networking/private-endpoint.md
