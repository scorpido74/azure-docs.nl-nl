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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673215"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Uw app integreren met een Azure-Virtual Network
In dit document wordt de Azure App Service functie voor de integratie van virtuele netwerken beschreven en wordt uitgelegd hoe u deze kunt instellen met apps in de [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714). Met [Azure Virtual Networks][VNETOverview] (VNets) kunt u veel van uw Azure-resources in een niet-Internet routeerbaar netwerk plaatsen.  

De Azure App Service heeft twee variaties.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet-integratie inschakelen 

1. Ga naar de gebruikers interface van het netwerk in de App Service Portal. Onder VNet-integratie selecteert u *' Klik hier om te configureren '* . 

1. Selecteer **VNet toevoegen**.  

   ![VNet-integratie selecteren][1]

1. De vervolg keuzelijst bevat alle Resource Manager-VNets in uw abonnement in dezelfde regio en hieronder ziet u een lijst met alle Resource Manager-VNets in alle andere regio's. Selecteer het VNet dat u wilt integreren met.

   ![Het VNet selecteren][2]

   * Als het VNet zich in dezelfde regio bevindt, maakt u een nieuw subnet of kiest u een leeg al bestaand subnet. 

   * Als u een VNet in een andere regio wilt selecteren, moet u een Virtual Network gateway hebben ingericht met Point-to-site ingeschakeld.

   * Als u wilt integreren met een klassiek VNet, selecteert u in plaats van te klikken op de vervolg keuzelijst VNet. **Klik hier om verbinding te maken met een klassiek vnet**. Selecteer het gewenste klassieke VNet. Het doel-VNet moet al een Virtual Network gateway hebben ingericht met Point-to-site ingeschakeld.

    ![Klassiek VNet selecteren][3]
    
Tijdens de integratie wordt uw app opnieuw opgestart.  Wanneer de integratie is voltooid, worden er details weer geven over het VNet dat u hebt geïntegreerd met. 

Als uw app is geïntegreerd met uw VNet, gebruikt deze dezelfde DNS-server als waarop uw VNet is geconfigureerd, tenzij dit Azure DNS Private Zones is. VNet-integratie met Azure DNS Private Zones kan momenteel niet worden gebruikt.

## <a name="regional-vnet-integration"></a>Regionale VNet-integratie

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Hoe regionale VNet-integratie werkt

Apps in de App Service worden gehost op werk rollen. De basis-en hogere prijs plannen zijn toegewezen hosting plannen waarbij er geen andere werk belastingen voor klanten op dezelfde werk nemers worden uitgevoerd. Regionale VNet-integratie werkt door virtuele interfaces te koppelen aan adressen in het overgedragen subnet. Omdat het van-adres zich in uw VNet bevindt, kan het toegang krijgen tot de meeste zaken in of via uw VNet, net zoals een virtuele machine in uw VNet zou. De netwerk implementatie verschilt van het uitvoeren van een virtuele machine in uw VNet en daarom zijn sommige netwerk functies nog niet beschikbaar tijdens het gebruik van deze functie.

![Hoe regionale VNet-integratie werkt][5]

Wanneer de regionale VNet-integratie is ingeschakeld, maakt uw app nog steeds uitgaande oproepen naar het Internet via dezelfde kanalen als normaal. De uitgaande adressen die worden vermeld in de app-eigenschappen Portal zijn nog steeds de adressen die worden gebruikt door uw app. Welke wijzigingen voor uw app zijn, aanroepen naar service-eind punten beveiligde services of RFC 1918-adressen gaan naar uw VNet. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer naar uw VNet worden verzonden. 

De functie ondersteunt slechts één virtuele interface per werk nemer.  Eén virtuele interface per werk nemer betekent één regionale VNet-integratie per App Service plan. Alle apps in hetzelfde App Service-abonnement kunnen dezelfde VNet-integratie gebruiken, maar als u een app nodig hebt om verbinding te maken met een extra VNet, moet u een ander App Service-abonnement aanmaken. De gebruikte virtuele interface is geen resource waarmee klanten rechtstreeks toegang hebben.

Vanwege de aard van de werking van deze technologie wordt het verkeer dat wordt gebruikt met VNet-integratie niet weer gegeven in Network Watcher-of NSG-stroom Logboeken.  

## <a name="gateway-required-vnet-integration"></a>Vereiste VNet-integratie voor gateway

De gateway vereiste VNet-integratie ondersteunt het verbinden met een VNet in een andere regio of op een klassiek VNet. Vereiste VNet-integratie voor gateway: 

* Hiermee kan een app verbinding maken met slechts één VNet per keer
* Hiermee kunnen Maxi maal vijf VNets worden geïntegreerd in een App Service plan 
* Hiermee kan hetzelfde VNet worden gebruikt door meerdere apps in een App Service-abonnement zonder dat dit van invloed is op het totale aantal dat kan worden gebruikt door een App Service plan.  Als u zes apps hebt die hetzelfde VNet gebruiken in hetzelfde App Service-abonnement, telt dat op 1 VNet dat wordt gebruikt. 
* Ondersteunt een SLA van 99,9% door de SLA op de gateway
* Hiermee kunnen uw apps de DNS gebruiken waarvoor het VNet is geconfigureerd
* Vereist een Virtual Network op route gebaseerde gateway die is geconfigureerd met een SSTP-punt-naar-site-VPN voordat deze kan worden verbonden met de app. 

U kunt Gateway vereiste VNet-integratie niet gebruiken:

* Met Linux-apps
* Met een VNet dat is verbonden met ExpressRoute 
* Om toegang te krijgen tot service-eind punten beveiligde bronnen
* Met een gateway voor samen werking die zowel ExpressRoute ondersteunt als site/site-naar-site-Vpn's

### <a name="set-up-a-gateway-in-your-vnet"></a>Een gateway instellen in uw VNet ###

Een gateway maken:

1. [Maak een gateway-subnet][creategatewaysubnet] in uw VNet.  

1. [Maak de VPN-gateway][creategateway]. Selecteer een op route gebaseerd VPN-type.

1. [Stel het punt in op site adressen][setp2saddresses]. Als de gateway zich niet in de basis-SKU bevindt, moet IKEV2 worden uitgeschakeld in het punt voor site configuratie en SSTP moet worden geselecteerd. De adres ruimte van het punt naar de site moet zich in de RFC 1918-adres blokken benemen, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Als u de gateway alleen maakt voor gebruik met App Service VNet-integratie, hoeft u geen certificaat te uploaden. Het maken van de gateway kan 30 minuten duren. U kunt uw app pas met uw VNet integreren nadat de gateway is ingericht. 

### <a name="how-gateway-required-vnet-integration-works"></a>Hoe gateway vereiste VNet-integratie werkt

Gateway vereist VNet-integratie die is gebouwd op het punt van de VPN-technologie van de site. Site VPN-verbindingen beperken netwerk toegang tot alleen de virtuele machine die als host fungeert voor de app. Apps worden beperkt tot het verzenden van verkeer naar Internet, via Hybride verbindingen of via VNet-integratie. Als uw app is geconfigureerd met de portal om de vereiste VNet-integratie voor de gateway te gebruiken, wordt een complexe onderhandeling namens u beheerd om certificaten te maken en toe te wijzen aan de-gateway en aan de toepassing. Het eind resultaat is dat de werk nemers die worden gebruikt voor het hosten van uw apps, rechtstreeks verbinding kunnen maken met de virtuele netwerk gateway in het geselecteerde VNet. 

![Hoe gateway vereiste VNet-integratie werkt][6]

### <a name="accessing-on-premises-resources"></a>Toegang tot on-premises resources

Apps kunnen toegang krijgen tot on-premises resources door te integreren met VNets met site-naar-site-verbindingen. Als u de gateway vereiste VNet-integratie gebruikt, moet u uw on-premises VPN-gateway routes bijwerken met uw punt-naar-site-adres blokken. Wanneer de site-naar-site-VPN voor het eerst wordt ingesteld, moeten de scripts die worden gebruikt voor het configureren van de server routes op de juiste wijze instellen. Als u de punt-naar-site-adressen toevoegt nadat u uw site-naar-site-VPN hebt gemaakt, moet u de routes hand matig bijwerken. Meer informatie over hoe u dit doet, verschilt per gateway en wordt hier niet beschreven. U kunt BGP niet geconfigureerd met een site-naar-site-VPN-verbinding.

Er is geen aanvullende configuratie vereist voor de functie van de regionale VNet-integratie die u kunt bereiken via uw VNet en on-premises. U hoeft alleen maar uw VNet te verbinden met on-premises met behulp van ExpressRoute of een site-naar-site-VPN. 

> [!NOTE]
> De gateway vereist VNet-integratie functie integreert geen app met een VNet met een ExpressRoute-gateway. Zelfs als de ExpressRoute-gateway is geconfigureerd in de modus voor samen [werking][VPNERCoex] , werkt de VNet-integratie niet. Als u toegang nodig hebt tot bronnen via een ExpressRoute-verbinding, kunt u de functie voor regionale VNet-integratie gebruiken of een [app service Environment][ASE], die wordt uitgevoerd in uw VNet. 
> 
> 

### <a name="peering"></a>Peering

Als u peering met de regionale VNet-integratie gebruikt, hoeft u geen aanvullende configuratie uit te voeren. 

Als u de gateway vereist VNet-integratie met peering gebruikt, moet u enkele extra items configureren. Peering configureren voor gebruik met uw app:

1. Een peering-verbinding toevoegen op het VNet waarmee uw app verbinding maakt. Wanneer u de peering-verbinding toevoegt, schakelt u **toegang tot virtueel netwerk toestaan** in en schakelt u **doorgestuurd verkeer toestaan** uit en laat u toe dat **gateway door Voer**.
1. Voeg een peering-verbinding toe op het VNet dat wordt gekoppeld aan het VNet waarmee u verbinding hebt. Bij het toevoegen van de peering-verbinding op het doel-VNet schakelt u **toegang tot virtueel netwerk toestaan** in en schakelt u **doorgestuurd verkeer toestaan** uit en **externe gateways toestaan**.
1. Ga naar het App Service plan > netwerken > VNet-integratie GEBRUIKERSINTERFACE in de portal.  Selecteer het VNet waarmee uw app verbinding maakt. Voeg onder de sectie route ring het adres bereik toe van het VNet dat is gekoppeld aan het VNet waarmee uw app is verbonden.  

## <a name="managing-vnet-integration"></a>VNet-integratie beheren 

Het verbinden en verbreken van de verbinding met een VNet bevindt zich op een app-niveau. Bewerkingen die van invloed kunnen zijn op de VNet-integratie tussen meerdere apps, bevinden zich op het niveau van App Service plan. Vanuit de app > Network > VNet-integratie Portal kunt u meer informatie krijgen over uw VNet. U kunt vergelijk bare gegevens zien op het ASP-niveau in het ASP-> netwerk > VNet-integratie Portal.

De enige bewerking die u kunt uitvoeren in de app-weer gave van uw VNet-integratie is het verbreken van de verbinding van uw app met het VNet waarmee momenteel verbinding is gemaakt. Als u uw app wilt loskoppelen van een VNet, selecteert u **verbinding verbreken**. Uw app wordt opnieuw gestart wanneer u de verbinding met een VNet verbreekt. Als u de verbinding verbreekt, wordt uw VNet niet gewijzigd. Het subnet of de gateway wordt niet verwijderd. Als u uw VNet vervolgens wilt verwijderen, moet u de app eerst loskoppelen van het VNet en de resources hierin verwijderen, zoals gateways. 

De gebruikers interface voor de integratie van de ASP-VNet toont u alle VNet-integraties die worden gebruikt door de apps in uw ASP. Als u de details van elk VNet wilt bekijken, klikt u op het VNet waarin u bent geïnteresseerd. Er zijn twee acties die u hier kunt uitvoeren voor gateway vereiste VNet-integratie.

* **Netwerk synchroniseren**. De synchronisatie netwerk bewerking is alleen voor de gateway-afhankelijke VNet-integratie functie. Wanneer u een synchronisatie netwerk uitvoert, zorgt u ervoor dat uw certificaten en netwerk gegevens synchroon zijn. Als u de DNS van uw VNet toevoegt of wijzigt, moet u een **synchronisatie netwerk** bewerking uitvoeren. Met deze bewerking worden alle apps die gebruikmaken van dit VNet, opnieuw gestart.
* **Routes toevoegen** Door routes toe te voegen, wordt uitgaand verkeer naar uw VNet geleid. 

**Gateway vereist VNet-integratie routering** De routes die in uw VNet zijn gedefinieerd, worden gebruikt voor het omleiden van verkeer naar uw VNet vanuit uw app. Als u extra uitgaand verkeer naar het VNet wilt verzenden, kunt u deze adres blokken hier toevoegen. Deze functie werkt alleen met Gateway vereiste VNet-integratie. Route tabellen hebben geen invloed op uw app-verkeer wanneer u Gateway vereiste VNet-integratie gebruikt op de manier die met regionale VNet-integratie.

**Gateway vereiste VNet-integratie certificaten** Wanneer de gateway vereist VNet-integratie is ingeschakeld, is er een vereiste uitwisseling van certificaten vereist om te zorgen voor de beveiliging van de verbinding. Naast de certificaten zijn de DNS-configuratie, routes en andere vergelijk bare dingen die het netwerk beschrijven.
Als certificaten of netwerk gegevens worden gewijzigd, moet u klikken op netwerk synchroniseren. Wanneer u op netwerk synchroniseren klikt, wordt er een korte onderbreking in de connectiviteit tussen uw app en uw VNet veroorzaakt. Als uw app niet opnieuw wordt gestart, kan het verlies van de connectiviteit ertoe leiden dat uw site niet goed werkt. 

## <a name="pricing-details"></a>Prijsdetails
De regionale VNet-integratie functie heeft geen extra kosten voor gebruik buiten de ASP-prijs categorie kosten.

Er zijn drie kosten verbonden aan het gebruik van de gateway vereiste VNet-integratie functie:

* Kosten voor ASP-prijs Categorieën: uw apps moeten zich in een Standard-, Premium-of PremiumV2-abonnement beApp Service. U vindt hier meer informatie over deze kosten: [app service prijzen][ASPricing]. 
* Kosten voor gegevens overdracht: er worden kosten in rekening gebracht voor het afrekenen van gegevens, zelfs als het VNet zich in hetzelfde Data Center bevindt. Deze kosten worden beschreven in [gegevensoverdracht prijs informatie][DataPricing]. 
* VPN Gateway kosten: er zijn kosten verbonden aan de VNet-gateway die is vereist voor het punt-naar-site-VPN. De details zijn te vinden op de pagina met [VPN gateway prijzen][VNETPricing] .

## <a name="troubleshooting"></a>Problemen oplossen

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatisering

Er is CLI-ondersteuning voor de regionale VNet-integratie. Als u toegang wilt krijgen tot de volgende opdrachten, [installeert u Azure cli][installCLI]. 

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

Voor gateway vereiste VNet-integratie kunt u App Service integreren met een Azure Virtual Network met behulp van Power shell. Zie [verbinding maken met een app in azure app service met een Azure-Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)voor een kant-en-klaar script.


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
