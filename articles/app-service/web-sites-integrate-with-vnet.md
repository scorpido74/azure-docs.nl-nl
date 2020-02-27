---
title: App integreren met Azure Virtual Network
description: Integreer apps in Azure App Service met Azure Virtual Networks.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648997"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Uw app integreren met een Azure-Virtual Network
In dit document wordt de Azure App Service functie voor de integratie van virtuele netwerken beschreven en wordt uitgelegd hoe u deze kunt instellen met apps in de [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714). Met [Azure Virtual Networks][VNETOverview] (VNets) kunt u veel van uw Azure-resources in een niet-Internet routeerbaar netwerk plaatsen.  

De Azure App Service heeft twee variaties. 

1. De multi tenant systemen die ondersteuning bieden voor het volledige aanbod aan prijs plannen, behalve geïsoleerd
2. De App Service Environment (ASE), die in uw VNet wordt geïmplementeerd en die ondersteuning biedt voor geïsoleerd prijs plan-apps

Dit document gaat over de VNet-integratie functie, die u kunt gebruiken in de multi tenant-App Service. Als uw app zich in [app service Environment][ASEintro]bevindt, is deze al in een VNet en is het niet nodig om de VNet-integratie functie te gebruiken om resources in hetzelfde VNet te bereiken. Lees [app service-netwerk functies](networking-features.md) voor meer informatie over alle app service-netwerk functies

VNet-integratie geeft uw web-app toegang tot resources in uw virtuele netwerk, maar verleent geen inkomende persoonlijke toegang tot uw web-app vanuit het VNet. Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals vanuit een virtueel Azure-netwerk. VNet-integratie is alleen voor het maken van uitgaande oproepen vanuit uw app naar uw VNet. De VNet-integratie functie gedraagt zich anders als deze wordt gebruikt in combi natie met VNets in dezelfde regio en met VNets in andere regio's. De VNet-integratie functie heeft twee variaties.

1. Regionale VNet-integratie: wanneer u verbinding maakt met Resource Manager-VNets in dezelfde regio, moet u een toegewijd subnet hebben in het VNet waarmee u een integratie wilt maken. 
2. Voor gateway vereiste VNet-integratie: wanneer u verbinding maakt met VNets in andere regio's of een klassiek VNet in dezelfde regio, hebt u een Virtual Network gateway nodig die in het doel-VNet is ingericht.

De VNet-integratie functies:

* een prijs plan voor Standard, Premium, PremiumV2 of elastisch Premium vereisen 
* ondersteuning voor TCP en UDP
* werken met App Service apps en functie-apps

Er zijn enkele zaken die VNet-integratie niet ondersteunt, waaronder:

* een station koppelen
* AD-integratie 
* NetBios

Gateway vereiste VNet-integratie biedt alleen toegang tot resources in het doel-VNet of in netwerken die zijn verbonden met het doel-VNet met peering of Vpn's. Gateway vereiste VNet-integratie biedt geen toegang tot bronnen die beschikbaar zijn via ExpressRoute-verbindingen of die werken met Service-eind punten. 

VNet-integratie geeft uw web-app toegang tot resources in uw virtuele netwerk, maar verleent geen inkomende persoonlijke toegang tot uw web-app vanuit het virtuele netwerk, ongeacht de gebruikte versie. Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals vanuit een virtueel Azure-netwerk. VNet-integratie is alleen voor het maken van uitgaande oproepen vanuit uw app naar uw VNet. 

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

## <a name="regional-vnet-integration"></a>regionale VNet-integratie

Door gebruik te maken van regionale VNet-integratie kan uw app toegang tot:

* resources in het VNet in dezelfde regio die u integreert met 
* resources in VNets die zijn gekoppeld aan uw VNet die zich in dezelfde regio bevinden
* Services voor beveiligde service-eind punten
* bronnen over ExpressRoute-verbindingen
* resources in het VNet waarmee u verbinding hebt
* bronnen in peered verbindingen, waaronder ExpressRoute-verbindingen
* Privé-eind punten 

Wanneer u VNet-integratie met VNets in dezelfde regio gebruikt, kunt u de volgende Azure-netwerk functies gebruiken:

* Netwerk beveiligings groepen (Nsg's): u kunt uitgaand verkeer blok keren met een netwerk beveiligings groep die is opgenomen in het subnet met integratie. De regels voor binnenkomende verbindingen zijn niet van toepassing omdat u VNet-integratie niet kunt gebruiken om inkomende toegang tot uw web-app te bieden.
* Route tabellen (Udr's): u kunt een route tabel plaatsen op het subnet met integratie om uitgaand verkeer te verzenden naar de gewenste locatie. 

Standaard stuurt uw app alleen RFC1918-verkeer naar uw VNet. Als u al het uitgaande verkeer wilt door sturen naar uw VNet, past u de app-instelling WEBSITE_VNET_ROUTE_ALL toe op uw app. De app-instelling configureren:

1. Ga naar de configuratie GEBRUIKERSINTERFACE in de app-Portal. **Nieuwe toepassings instelling** selecteren
1. Typ **WEBSITE_VNET_ROUTE_ALL** in het veld naam en **1** in het veld waarde

   ![Toepassings instelling opgeven][4]

1. Selecteer **OK**
1. Selecteer **Opslaan**

Als u al het uitgaande verkeer naar uw VNet routert, is het onderhevig aan de Nsg's en Udr's die worden toegepast op uw integratie subnet. Wanneer u al het uitgaande verkeer naar uw VNet routert, zijn uw uitgaande adressen nog steeds de uitgaande adressen die worden vermeld in de app-eigenschappen, tenzij u routes opgeeft om het verkeer elders te verzenden. 

Er zijn enkele beperkingen bij het gebruik van VNet-integratie met VNets in dezelfde regio:

* U kunt geen resources bereiken in globale peering-verbindingen
* De functie is alleen beschikbaar vanaf nieuwere App Service schaal eenheden die ondersteuning bieden voor PremiumV2 App Service-plannen.
* Het integratie subnet kan slechts door één App Service plan worden gebruikt
* De functie kan niet worden gebruikt door toepassingen van het geïsoleerde abonnement in een App Service Environment
* Voor de functie is een ongebruikt subnet van a/27 met 32 adressen of groter vereist in een resource manager-VNet
* De app en het VNet moeten zich in dezelfde regio bevinden
* U kunt een VNet met een geïntegreerde app niet verwijderen. Verwijder de integratie voordat u het VNet verwijdert. 
* U kunt alleen integreren met VNets in hetzelfde abonnement als de web-app
* U kunt slechts één regionale VNet-integratie per App Service plan hebben. Meerdere apps in hetzelfde App Service-abonnement kunnen hetzelfde VNet gebruiken. 
* U kunt het abonnement van een app of een App Service plan niet wijzigen terwijl er een app is die gebruikmaakt van regionale VNet-integratie

Er wordt één adres gebruikt voor elk exemplaar van de App Service-abonnement. Als u uw app schaalt naar vijf instanties, worden er vijf adressen gebruikt. Aangezien de grootte van het subnet niet kan worden gewijzigd na toewijzing, moet u een subnet gebruiken dat groot genoeg is om te kunnen voldoen aan de schaal van uw app. Een/26 met 64-adressen is de aanbevolen grootte. Een/26 met 64-adressen is geschikt voor een Premium-App Service abonnement met 30 exemplaren. Wanneer u een App Service plan omhoog of omlaag schaalt, hebt u twee keer zoveel adressen nodig voor een korte periode. 

Als u wilt dat uw apps in een andere App Service een VNet bereiken dat al is verbonden met apps in een ander App Service-abonnement, moet u een ander subnet selecteren dan dat van de bestaande VNet-integratie.  

De functie is beschikbaar als preview-versie voor Linux. Het Linux-formulier van de functie ondersteunt alleen het maken van aanroepen naar RFC 1918-adressen (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-app-for-containers"></a>Web App for Containers

Als u gebruikmaakt van App Service op Linux met de ingebouwde installatie kopieën, werkt de regionale VNet-integratie zonder extra wijzigingen. Als u Web App for Containers gebruikt, moet u uw docker-installatie kopie aanpassen om VNet-integratie te kunnen gebruiken. Gebruik in uw docker-installatie kopie de variabele poort omgeving als de gateway poort van de hoofd webserver, in plaats van een hardcoded poort nummer te gebruiken. De variabele poort omgeving wordt automatisch ingesteld door App Service platform op de opstart tijd van de container. Als u SSH gebruikt, moet de SSH-daemon worden geconfigureerd om te Luis teren naar het poort nummer dat is opgegeven door de SSH_PORT omgevings variabele bij gebruik van regionale VNet-integratie.  Er is geen ondersteuning voor de gateway vereiste VNet-integratie op Linux. 

### <a name="service-endpoints"></a>Service-eind punten

Met regionale VNet-integratie kunt u service-eind punten gebruiken.  Als u service-eind punten wilt gebruiken in combi natie met uw app, gebruikt u de regionale VNet-integratie om verbinding te maken met een geselecteerd VNet en configureert u vervolgens service-eind punten in het subnet dat u voor de integratie hebt gebruikt. 

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Met netwerk beveiligings groepen kunt u binnenkomend en uitgaand verkeer naar resources in een VNet blok keren. Een web-app die regionale VNet-integratie gebruikt, kan de [netwerk beveiligings groep][VNETnsg] gebruiken om uitgaand verkeer naar resources in uw VNet of Internet te blok keren. Als u verkeer naar open bare adressen wilt blok keren, moet u de toepassings instelling WEBSITE_VNET_ROUTE_ALL instellen op 1. De binnenkomende regels in een NSG zijn niet van toepassing op uw app omdat VNet-integratie alleen invloed heeft op uitgaand verkeer van uw app. Als u inkomend verkeer naar uw web-app wilt beheren, gebruikt u de functie toegangs beperkingen. Een NSG die wordt toegepast op het integratie subnet, wordt van kracht ongeacht eventuele routes die worden toegepast op het integratie subnet. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1 en u geen routes hebt die invloed hebben op het verkeer van open bare adressen op het subnet met integratie, is al uw uitgaand verkeer nog steeds onderhevig aan Nsg's toegewezen aan uw integratie subnet. Als WEBSITE_VNET_ROUTE_ALL niet is ingesteld, wordt Nsg's alleen toegepast op RFC1918-verkeer.

### <a name="routes"></a>Routes

Met route tabellen kunt u uitgaand verkeer vanuit uw app naar de gewenste locatie routeren. Routerings tabellen zijn standaard alleen van invloed op uw RFC1918-doel verkeer.  Als u WEBSITE_VNET_ROUTE_ALL instelt op 1, worden al uw uitgaande oproepen beïnvloed. Routes die zijn ingesteld op het integratie subnet, zijn niet van invloed op antwoorden op aanvragen voor inkomende apps. Algemene doelen kunnen Firewall apparaten of gateways zijn. Als u al het uitgaande verkeer on-premises wilt routeren, kunt u een route tabel gebruiken om al het uitgaande verkeer naar uw ExpressRoute-gateway te verzenden. Als u verkeer naar een gateway stuurt, moet u ervoor zorgen dat routes worden ingesteld in het externe netwerk om antwoorden terug te sturen.

Border Gateway Protocol (BGP)-routes hebben ook invloed op uw app-verkeer. Als u BGP-routes van iets zoals een ExpressRoute-gateway hebt, heeft dit gevolgen voor het uitgaande verkeer van uw app. Standaard heeft BGP-routes alleen invloed op uw RFC1918-doel verkeer. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer worden beïnvloed door de BGP-routes. 

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
Hoewel de functie eenvoudig kan worden ingesteld, betekent dat niet dat uw ervaring geen probleem is. Als u problemen ondervindt met het verkrijgen van toegang tot uw gewenste eind punt, kunt u een aantal hulpprogram ma's gebruiken om de connectiviteit vanuit de app-console te testen. Er zijn twee consoles die u kunt gebruiken. Een van de kudu-console en de andere is de console in de Azure Portal. Als u de kudu-console wilt bereiken vanuit uw app, gaat u naar extra-> kudu. U kunt de Kudo-console ook bereiken op [site naam]. scm. azurewebsites. net. Zodra de website is geladen, gaat u naar het tabblad debug console. Als u naar de Azure Portal gehoste console wilt gaan vanuit uw app, gaat u naar Hulpprogram Ma's->-console. 

#### <a name="tools"></a>Hulpprogramma's
De hulpprogram ma's **ping**, **nslookup**en **tracert** werken niet via de console vanwege beveiligings beperkingen. Als u de void wilt vullen, worden er twee afzonderlijke extra hulp middelen toegevoegd. We hebben een hulp programma met de naam nameresolver. exe toegevoegd om de DNS-functionaliteit te testen. De syntaxis is:

    nameresolver.exe hostname [optional: DNS Server]

U kunt **nameresolver** gebruiken om de hostnamen te controleren waarvan uw app afhankelijk is. Op deze manier kunt u testen of er iets mis is met uw DNS of dat u mogelijk geen toegang hebt tot uw DNS-server. U kunt de DNS-server zien die door uw app wordt gebruikt in de-console door te kijken naar de omgevings variabelen WEBSITE_DNS_SERVER en WEBSITE_DNS_ALT_SERVER.

Met het volgende hulp programma kunt u testen op TCP-connectiviteit met een combi natie van host en poort. Dit hulp programma heet **tcpping** en de syntaxis is:

    tcpping.exe hostname [optional: port]

Het **tcpping** -hulp programma vertelt u of u een specifieke host en poort kunt bereiken. Het kan alleen succes weer geven als: er luistert een toepassing naar de combi natie van host en poort en er is netwerk toegang vanuit uw app naar de opgegeven host en poort.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Fout opsporing voor gehoste resources in VNet
Er zijn een aantal dingen die kunnen voor komen dat uw app een specifieke host en poort bereikt. De meeste tijd is het een van de volgende drie dingen:

* **Een firewall is in de weg.** Als u een firewall op de gewenste manier hebt, bereikt u de TCP-time-out. In dit geval is de TCP-time-out 21 seconden. Gebruik het hulp programma **tcpping** om de connectiviteit te testen. TCP-time-outs kunnen te wijten zijn aan veel dingen buiten firewalls, maar u kunt hier beginnen. 
* **DNS is niet toegankelijk.** De DNS-time-out is drie seconden per DNS-server. Als u twee DNS-servers hebt, is de time-out 6 seconden. Gebruik nameresolver om te controleren of DNS werkt. Houd er rekening mee dat u Nslookup niet kunt gebruiken als dat geen gebruik maakt van de DNS waarvoor uw VNet is geconfigureerd. Als dat niet mogelijk is, kunt u een firewall hebben of NSG de toegang tot DNS blok keren of kan deze worden uitgeschakeld.

Als dat niet het geval is, zoekt u eerst naar zaken als: 

**regionale VNet-integratie**
* is uw doel een niet-RFC1918-adres en hebt u geen WEBSITE_VNET_ROUTE_ALL ingesteld op 1
* is er een NSG die uitkomend verkeer van uw integratie subnet blokkeert
* Als u een ExpressRoute of een VPN-verbinding wilt maken, is uw on-premises gateway geconfigureerd voor het routeren van verkeer naar Azure? Als u eind punten in uw VNet maar niet on-premises kunt bereiken, controleert u uw routes.
* hebt u voldoende machtigingen voor het instellen van delegering op het subnet met integratie? Tijdens de configuratie van de regionale VNet-integratie wordt uw integratie-subnet gedelegeerd naar micro soft. Web. Het subnet wordt automatisch door de gebruikers interface van VNet-integratie naar micro soft. Web gedelegeerd. Als uw account niet voldoende netwerk machtigingen heeft voor het instellen van delegering, hebt u iemand nodig die kenmerken kan instellen op het subnet met integratie om het subnet te delegeren. Als u het subnet met integratie hand matig wilt overdragen, gaat u naar de gebruikers interface van het Azure Virtual Network-subnet en stelt u de overdracht in voor micro soft. Web 

**vereiste VNet-integratie voor gateway**
* is het punt-naar-site-adres bereik in de RFC 1918-bereiken (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Wordt de gateway in de portal weer gegeven? Als uw gateway niet beschikbaar is, zet u deze terug.
* Worden certificaten weer gegeven als gesynchroniseerd of vermoedt u dat de netwerk configuratie is gewijzigd?  Als uw certificaten niet zijn gesynchroniseerd of als u vermoedt dat er een wijziging is aangebracht in uw VNet-configuratie die niet is gesynchroniseerd met uw ASPs, klikt u op ' netwerk synchroniseren '.
* Als u een VPN-verbinding wilt maken, is de on-premises gateway die is geconfigureerd voor het routeren van verkeer naar Azure? Als u eind punten in uw VNet maar niet on-premises kunt bereiken, controleert u uw routes.
* probeert u een gateway voor samen werking te gebruiken die beide Point-to-site-en ExpressRoute ondersteunt? Naast elkaar aanwezige gateways worden niet ondersteund met VNet-integratie 

Fout opsporing in netwerk problemen is een uitdaging omdat u niet kunt zien wat de toegang tot een specifieke host blokkeert: poort combinatie. Enkele oorzaken zijn onder andere:

* u hebt een firewall op uw host waarmee u de toegang tot de toepassings poort van uw Point-site-IP-bereik kunt voor komen. Voor kruisende subnetten is vaak open bare toegang vereist.
* de doelhost is niet beschikbaar
* uw toepassing is niet beschikbaar
* u hebt een onjuist IP-adres of hostnaam
* uw toepassing luistert op een andere poort dan verwacht. U kunt uw proces-ID met de luister poort overeenkomen met ' netstat-Aon ' op de host van het eind punt. 
* uw netwerk beveiligings groepen worden zo geconfigureerd dat ze de toegang tot uw toepassingshost en poort van uw punt tot het IP-bereik van de site verhinderen

Houd er rekening mee dat u niet weet welk adres uw app daad werkelijk zal gebruiken. Dit kan elk adres zijn in het integratie subnet of het adres bereik van punt-naar-site. u moet dus toegang tot het hele adres bereik toestaan. 

Aanvullende stappen voor fout opsporing zijn onder andere:

* Maak verbinding met een virtuele machine in uw VNet en probeer uw bronhost: poort te bereiken. Als u wilt testen op TCP-toegang, gebruikt u de Power shell **-opdracht test-NetConnection**. De syntaxis is:

      test-netconnection hostname [optional: -Port]

* een toepassing op een virtuele machine weer geven en de toegang tot die host en poort testen vanuit de console vanuit uw app met behulp van **tcpping**

#### <a name="on-premises-resources"></a>On-premises resources ####

Als uw app een on-premises resource niet kan bereiken, controleert u of u de bron kunt bereiken vanuit uw VNet. Gebruik de Power shell **-opdracht test-NetConnection** om te controleren op TCP-toegang. Als uw virtuele machine uw on-premises resource niet kan bereiken, is uw VPN-of ExpressRoute-verbinding mogelijk niet juist geconfigureerd.

Als uw door VNet gehoste VM uw on-premises systeem kan bereiken, maar uw app niet kan, kan dit een van de volgende oorzaken hebben:

* uw routes zijn niet geconfigureerd met uw subnet of verwijzen naar site adresbereiken in uw on-premises gateway
* uw netwerk beveiligings groepen blok keren de toegang voor uw punt-naar-site-IP-bereik
* uw on-premises firewalls blok keren verkeer van uw punt-naar-site-IP-bereik
* u probeert een niet-RFC 1918-adres te bereiken met behulp van de regionale VNet-integratie functie


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
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
