---
title: App integreren met Azure Virtual Network-Azure App Service
description: Laat zien hoe u een app in Azure App Service verbindt met een nieuw of bestaand virtueel Azure-netwerk
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7246a0223e156abd866594c65542069944601b01
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018258"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Uw app integreren met een Azure-Virtual Network
In dit document wordt de Azure App Service functie voor de integratie van virtuele netwerken beschreven en wordt uitgelegd hoe u deze kunt instellen met apps in de [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714). [Virtuele netwerken van Azure][VNETOverview] (VNets) kunt u veel van uw Azure-resources in een niet-Internet routeerbaar netwerk plaatsen.  

De Azure App Service heeft twee variaties. 

1. De multi tenant systemen die ondersteuning bieden voor het volledige aanbod aan prijs plannen, behalve geïsoleerd
2. De App Service Environment (ASE), die in uw VNet wordt geïmplementeerd en die ondersteuning biedt voor geïsoleerd prijs plan-apps

Dit document gaat over de twee VNet-integratie functies, die u kunt gebruiken in de multi tenant-App Service. Als uw app zich in [app service Environment][ASEintro]bevindt, is deze al in een VNet en is het niet nodig om de VNet-integratie functie te gebruiken om resources in hetzelfde VNet te bereiken. Lees [app service-netwerk functies](networking-features.md) voor meer informatie over alle app service-netwerk functies

Er zijn twee formulieren voor de VNet-integratie functie

1. Met één versie kan de integratie met VNets in dezelfde regio worden geïntegreerd. Deze vorm van de functie vereist een subnet in een VNet in dezelfde regio. Deze functie is nog steeds beschikbaar als preview, maar wordt wel ondersteund voor werk belastingen voor de Windows-app-productie met enkele aanvullende voor waarden die hieronder zijn vermeld.
2. De andere versie maakt integratie mogelijk met VNets in andere regio's of met klassieke VNets. Voor deze versie van de functie is een Virtual Network-gateway in uw VNet geïmplementeerd. Dit is de punt-naar-site-VPN-functie en wordt alleen ondersteund met Windows-apps.

Een app kan slechts één vorm van de VNet-integratie functie tegelijk gebruiken. De vraag is vervolgens welke functie u moet gebruiken. U kunt voor een groot aantal dingen gebruiken. De Clear verschillen zijn als volgt:

| Probleem  | Oplossing | 
|----------|----------|
| U wilt een RFC 1918-adres bereiken (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) in dezelfde regio | regionale VNet-integratie |
| U wilt bronnen bereiken in een klassiek VNet of een VNet in een andere regio | vereiste VNet-integratie voor gateway |
| U wilt RFC 1918-eind punten bereiken via ExpressRoute | regionale VNet-integratie |
| Resources willen bereiken tussen service-eind punten | regionale VNet-integratie |

Geen van beide functies biedt u de mogelijkheid om niet-RFC 1918-adressen te bereiken in ExpressRoute. Hiervoor moet u nu een ASE gebruiken.

Het gebruik van de regionale VNet-integratie verbindt uw VNet niet met on-premises of het configureren van service-eind punten. Dit is een afzonderlijke netwerk configuratie. Met de regionale VNet-integratie kan uw app eenvoudig aanroepen over deze verbindings typen.

VNet-integratie geeft uw web-app toegang tot resources in uw virtuele netwerk, maar verleent geen inkomende persoonlijke toegang tot uw web-app vanuit het virtuele netwerk, ongeacht de gebruikte versie. Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals vanuit een virtueel Azure-netwerk. VNet-integratie is alleen voor het maken van uitgaande oproepen vanuit uw app naar uw VNet. 

De VNet-integratie functie:

* vereist een Standard-, Premium-of PremiumV2-prijs plan 
* ondersteunt TCP en UDP
* werkt met App Service-apps en functie-apps

Er zijn enkele zaken die VNet-integratie niet ondersteunt, waaronder:

* een station koppelen
* AD-integratie 
* NetBios

## <a name="regional-vnet-integration"></a>Regionale VNet-integratie 

Wanneer VNet-integratie wordt gebruikt in combi natie met VNets in dezelfde regio als uw app, is het gebruik van een overgedragen subnet vereist met ten minste 32 adressen. Het subnet kan niet worden gebruikt voor iets anders. Uitgaande oproepen van uw app worden gemaakt op basis van de adressen in het overgedragen subnet. Wanneer u deze versie van VNet-integratie gebruikt, worden de aanroepen gemaakt op basis van adressen in uw VNet. Door gebruik te maken van adressen in uw VNet kan uw app:

* Aanroepen naar beveiligde services voor service-eind punten
* Toegang tot resources via ExpressRoute-verbindingen
* Toegang tot resources in het VNet waarmee u verbinding hebt
* Toegang tot bronnen tussen peered verbindingen, waaronder ExpressRoute-verbindingen

Deze functie is beschikbaar als preview-versie, maar wordt wel ondersteund voor werk belastingen voor Windows-app-productie met de volgende beperkingen:

* U kunt alleen adressen bereiken die zich in het RFC 1918-bereik bevinden. Dit zijn adressen in de blokken 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
* U kunt geen resources bereiken in globale peering-verbindingen
* U kunt geen routes instellen voor het verkeer dat afkomstig is van uw app naar uw VNet
* De functie is alleen beschikbaar vanaf nieuwere App Service schaal eenheden die ondersteuning bieden voor PremiumV2 App Service-plannen.
* Het integratie subnet kan slechts door één App Service plan worden gebruikt
* De functie kan niet worden gebruikt door toepassingen van het geïsoleerde abonnement in een App Service Environment
* Voor de functie is een ongebruikt subnet met een/27 met 32-adressen of groter vereist in uw Resource Manager VNet
* De app en het VNet moeten zich in dezelfde regio bevinden
* U kunt een VNet met een geïntegreerde app niet verwijderen. U moet eerst de integratie verwijderen 
* U kunt slechts één regionale VNet-integratie per App Service plan hebben. Meerdere apps in hetzelfde App Service-abonnement kunnen hetzelfde VNet gebruiken. 
* U kunt het abonnement van een app of een App Service plan niet wijzigen terwijl er een app is die gebruikmaakt van regionale VNet-integratie

Er wordt één adres gebruikt voor elk exemplaar van de App Service-abonnement. Als u uw app naar 5 exemplaren hebt geschaald, worden 5 adressen gebruikt. Aangezien de grootte van het subnet niet kan worden gewijzigd na toewijzing, moet u een subnet gebruiken dat groot genoeg is om te kunnen voldoen aan de schaal van uw app. Een/26 met 64-adressen is de aanbevolen grootte. A/27 met 32-adressen bieden een Premium-App Service plan 20 instanties als u de grootte van het App Service plan niet hebt gewijzigd. Wanneer u een App Service plan omhoog of omlaag schaalt, hebt u twee keer zoveel adressen nodig voor een korte periode. 

Als u wilt dat uw apps in een andere App Service een VNet bereiken dat al is verbonden met apps in een ander App Service-abonnement, moet u een ander subnet selecteren dan dat van de bestaande VNet-integratie.  

De functie is ook beschikbaar als preview-versie voor Linux. De VNet-integratie functie gebruiken met een resource manager VNet in dezelfde regio:

1. Ga naar de gebruikers interface van het netwerk in de portal. Als uw app de nieuwe functie kan gebruiken, wordt er een optie weer gegeven voor het toevoegen van VNet (preview).  

   ![VNet-integratie selecteren][6]

1. Selecteer **VNet toevoegen (preview)** .  

1. Selecteer de Resource Manager VNet die u wilt integreren met en maak vervolgens een nieuw subnet of kies een leeg al bestaand subnet. Het volt ooien van de integratie duurt minder dan een minuut. Tijdens de integratie wordt uw app opnieuw opgestart.  Wanneer de integratie is voltooid, ziet u de details van het VNet dat u hebt geïntegreerd en een banner bovenaan die aangeeft dat de functie in preview is.

   ![Het VNet en subnet selecteren][7]

Als uw app is geïntegreerd met uw VNet, wordt dezelfde DNS-server gebruikt als uw VNet is geconfigureerd met. 

Voor regionale VNet-integratie moet het integratie-subnet worden gedelegeerd naar micro soft. Web.  Het subnet wordt automatisch door de gebruikers interface van VNet-integratie naar micro soft. Web gedelegeerd. Als uw account niet voldoende netwerk machtigingen heeft om dit in te stellen, moet u iemand hebben die kenmerken kan instellen op het subnet met integratie om het subnet te delegeren. Als u het subnet met integratie hand matig wilt overdragen, gaat u naar de gebruikers interface van het Azure Virtual Network-subnet en stelt u de overdracht in voor micro soft. Web

Selecteer de **verbinding verbreken**om uw app te verbreken vanuit het VNet. Hiermee wordt de web-app opnieuw opgestart. 


#### <a name="web-app-for-containers"></a>Web-app voor containers

Als u gebruikmaakt van App Service op Linux met de ingebouwde installatie kopieën, werkt de regionale VNet-integratie functie zonder extra wijzigingen. Als u Web App for Containers gebruikt, moet u uw docker-installatie kopie aanpassen om VNet-integratie te kunnen gebruiken. Gebruik in uw docker-installatie kopie de variabele poort omgeving als de gateway poort van de hoofd webserver, in plaats van een hardcoded poort nummer te gebruiken. De variabele poort omgeving wordt automatisch ingesteld door App Service platform op de opstart tijd van de container.

### <a name="service-endpoints"></a>Service-eindpunten

Met de nieuwe functie VNet-integratie kunt u service-eind punten gebruiken.  Als u service-eind punten wilt gebruiken in combi natie met uw app, gebruikt u de nieuwe VNet-integratie om verbinding te maken met een geselecteerd VNet en configureert u vervolgens service-eind punten in het subnet dat u voor de integratie hebt gebruikt. 


### <a name="how-vnet-integration-works"></a>Hoe VNet-integratie werkt

Apps in de App Service worden gehost op werk rollen. De basis-en hogere prijs plannen zijn toegewezen hosting plannen waarbij er geen andere werk belastingen voor klanten op dezelfde werk nemers worden uitgevoerd. VNet-integratie werkt door virtuele interfaces te koppelen aan adressen in het overgedragen subnet. Omdat het van-adres zich in uw VNet bevindt, heeft het toegang tot de meeste zaken in of via uw VNet, net zoals een virtuele machine in uw VNet zou. De netwerk implementatie verschilt van het uitvoeren van een virtuele machine in uw VNet en daarom zijn sommige netwerk functies nog niet beschikbaar tijdens het gebruik van deze functie.

![VNet-integratie](media/web-sites-integrate-with-vnet/vnet-integration.png)

Wanneer VNet-integratie is ingeschakeld, maakt uw app nog steeds uitgaande oproepen naar het Internet via dezelfde kanalen als normaal. De uitgaande adressen die worden vermeld in de app-eigenschappen Portal zijn nog steeds de adressen die worden gebruikt door uw app. Welke wijzigingen voor uw app zijn, aanroepen naar service-eind punten beveiligde services of RFC 1918-adressen gaan naar uw VNet. 

De functie ondersteunt slechts één virtuele interface per werk nemer.  Eén virtuele interface per werk nemer betekent één regionale VNet-integratie per App Service plan. Alle apps in hetzelfde App Service-abonnement kunnen dezelfde VNet-integratie gebruiken, maar als u een app nodig hebt om verbinding te maken met een extra VNet, moet u een ander App Service-abonnement aanmaken. De gebruikte virtuele interface is geen resource waarmee klanten rechtstreeks toegang hebben.

Vanwege de aard van de werking van deze technologie wordt het verkeer dat wordt gebruikt met VNet-integratie niet weer gegeven in Network Watcher-of NSG-stroom Logboeken.  

## <a name="gateway-required-vnet-integration"></a>Vereiste VNet-integratie voor gateway 

De gateway vereist VNet-integratie functie:

* Kan worden gebruikt om verbinding te maken met VNets in een wille keurige regio, de resources Resource Manager of klassieke VNets
* Hiermee kan een app verbinding maken met slechts één VNet per keer
* Hiermee kunnen Maxi maal vijf VNets worden geïntegreerd met in een App Service-abonnement 
* Hiermee kan hetzelfde VNet worden gebruikt door meerdere apps in een App Service-abonnement zonder dat dit van invloed is op het totale aantal dat kan worden gebruikt door een App Service plan.  Als u 6 apps hebt die gebruikmaken van hetzelfde VNet in hetzelfde App Service-abonnement, wordt het aantal gebruikte VNet gebruikt. 
* Vereist een Virtual Network gateway die is geconfigureerd met Point-to-site VPN
* Ondersteunt een SLA van 99,9% door de SLA op de gateway

Deze functie biedt geen ondersteuning voor:
* Gebruiken met Linux-apps
* Toegang tot resources in ExpressRoute 
* Toegang tot resources in service-eindpunten 

### <a name="getting-started"></a>Aan de slag

Hier volgen enkele dingen die u moet onthouden voordat u uw Web-App verbindt met een virtueel netwerk:

* Voor een virtueel doelnet doel moet punt-naar-site-VPN zijn ingeschakeld met een op route gebaseerde gateway voordat deze kan worden verbonden met de app. 
* Het VNet moet zich in hetzelfde abonnement benemen als uw App Service plan (ASP).
* De apps die met een VNet worden geïntegreerd, gebruiken de DNS die voor dat VNet is opgegeven.

### <a name="set-up-a-gateway-in-your-vnet"></a>Een gateway instellen in uw VNet ###

Als u al een gateway hebt geconfigureerd met Point-to-site-adressen, kunt u door gaan met het configureren van de VNet-integratie met uw app.  
Een gateway maken:

1. [Maak een gateway-subnet][creategatewaysubnet] in uw VNet.  

1. [Maak de VPN-gateway][creategateway]. Selecteer een op route gebaseerd VPN-type.

1. [Stel het punt in op site adressen][setp2saddresses]. Als de gateway zich niet in de basis-SKU bevindt, moet IKEV2 worden uitgeschakeld in de punt-naar-site-configuratie en moet SSTP worden geselecteerd. De adres ruimte moet de RFC 1918-adres blokken, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 zijn.

Als u de gateway alleen maakt voor gebruik met App Service VNet-integratie, hoeft u geen certificaat te uploaden. Het maken van de gateway kan 30 minuten duren. U kunt uw app pas met uw VNet integreren nadat de gateway is ingericht. 

### <a name="configure-vnet-integration-with-your-app"></a>VNet-integratie met uw app configureren 

VNet-integratie voor uw app inschakelen: 

1. Ga naar uw app in de Azure Portal en open de app-instellingen en selecteer Netwerk > VNet-integratie. Uw ASP moet zich in een standaard-SKU of beter kunnen gebruiken voor het gebruik van de VNet-integratie functie. 
 ![VNet-integratie GEBRUIKERSINTERFACE][1]

1. Selecteer **VNet toevoegen**. 
 ![VNet-integratie toevoegen][2]

1. Selecteer uw VNet. 
  ![Uw VNet selecteren][8]
  
De app wordt na deze laatste stap opnieuw opgestart.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Hoe de gateway de vereiste VNet-integratie functie werkt

De gateway vereiste VNet-integratie functie is gebouwd op basis van punt-naar-site-VPN-technologie. De punt-naar-site-technologie beperkt netwerk toegang tot alleen de virtuele machine die als host fungeert voor de app. Apps worden beperkt tot het verzenden van verkeer naar Internet, via Hybride verbindingen of via VNet-integratie. 

![Hoe VNet-integratie werkt][3]

## <a name="managing-vnet-integration"></a>VNet-integratie beheren
De mogelijkheid om verbinding te maken en de verbinding met een VNet te verbreken is op een app-niveau. Bewerkingen die van invloed kunnen zijn op de VNet-integratie tussen meerdere apps, bevinden zich op het niveau van App Service plan. Vanuit de app > Network > VNet-integratie Portal kunt u meer informatie krijgen over uw VNet. U kunt vergelijk bare gegevens zien op het ASP-niveau in het ASP-> netwerk > VNet-integratie Portal, met inbegrip van de apps in dat App Service plan een bepaalde integratie te gebruiken.

 ![VNet-Details][4]

De gegevens die u in de gebruikers interface van de VNet-integratie hebt, zijn hetzelfde als die van de app-en ASP-portals.

* VNet-naam-koppelingen naar de gebruikers interface van het virtuele netwerk
* Locatie: komt overeen met de locatie van uw VNet. Integreren met een VNet op een andere locatie kan latentie problemen veroorzaken voor uw app. 
* Certificaat status: geeft aan of uw certificaten synchroon zijn tussen uw App Service-abonnement en uw VNet.
* Status van de gateway: als u de gateway-vereiste VNet-integratie gebruikt, ziet u de status van de gateway.
* VNet-adres ruimte: toont de IP-adres ruimte voor uw VNet. 
* Punt-naar-site-adres ruimte: toont het punt van de site-IP-adres ruimte voor uw VNet. Wanneer u uw VNet aanroept terwijl u de functie gateway vereist gebruikt, is uw app van adres een van deze adressen. 
* Site-naar-site-adres ruimte: u kunt site-naar-site-Vpn's gebruiken om uw VNet te verbinden met uw on-premises resources of een ander VNet. De IP-adresbereiken die zijn gedefinieerd voor deze VPN-verbinding worden hier weer gegeven.
* DNS-servers: hier worden de DNS-servers weer gegeven die zijn geconfigureerd met uw VNet.
* IP-adressen die naar het VNet worden gerouteerd, worden in de adres blokken weer gegeven die worden gebruikt om het verkeer naar uw VNet te sturen 

De enige bewerking die u kunt uitvoeren in de app-weer gave van uw VNet-integratie is het verbreken van de verbinding van uw app met het VNet waarmee momenteel verbinding is gemaakt. Als u uw app wilt loskoppelen van een VNet, selecteert u **verbinding verbreken**. Uw app wordt opnieuw gestart wanneer u de verbinding met een VNet verbreekt. Als u de verbinding verbreekt, wordt uw VNet niet gewijzigd. Het subnet of de gateway wordt niet verwijderd. Als u uw VNet vervolgens wilt verwijderen, moet u de app eerst loskoppelen van het VNet en de resources hierin verwijderen, zoals gateways. 

Als u de gebruikers interface voor de integratie van ASP wilt bereiken, opent u uw ASP-gebruikers interface en selecteert u **netwerken**.  Selecteer onder VNet-integratie de optie **Klik hier om te configureren** om de gebruikers interface van de netwerk functie te openen.

![Informatie over ASP VNet-integratie][5]

De gebruikers interface van de ASP VNet-integratie geeft alle VNets weer die worden gebruikt door de apps in uw ASP. Als u de details van elk VNet wilt bekijken, klikt u op het VNet waarin u bent geïnteresseerd. U kunt hier twee acties uitvoeren.

* **Netwerk synchroniseren**. De synchronisatie netwerk bewerking is alleen voor de gateway-afhankelijke VNet-integratie functie. Wanneer u een synchronisatie netwerk uitvoert, zorgt u ervoor dat uw certificaten en netwerk gegevens synchroon zijn. Als u de DNS van uw VNet toevoegt of wijzigt, moet u een **synchronisatie netwerk** bewerking uitvoeren. Met deze bewerking worden alle apps die gebruikmaken van dit VNet, opnieuw gestart.
* **Routes toevoegen** Door routes toe te voegen, wordt uitgaand verkeer naar uw VNet geleid.

**Route ring** De routes die in uw VNet zijn gedefinieerd, worden gebruikt voor het omleiden van verkeer naar uw VNet vanuit uw app. Als u extra uitgaand verkeer naar het VNet wilt verzenden, kunt u deze adres blokken hier toevoegen. Deze functie werkt alleen met Gateway vereiste VNet-integratie.

**Certificaten** Wanneer de gateway vereist VNet-integratie is ingeschakeld, is er een vereiste uitwisseling van certificaten vereist om te zorgen voor de beveiliging van de verbinding. Naast de certificaten zijn de DNS-configuratie, routes en andere vergelijk bare dingen die het netwerk beschrijven.
Als certificaten of netwerk gegevens worden gewijzigd, moet u klikken op netwerk synchroniseren. Wanneer u op netwerk synchroniseren klikt, wordt er een korte onderbreking in de connectiviteit tussen uw app en uw VNet veroorzaakt. Als uw app niet opnieuw wordt gestart, kan het verlies van de connectiviteit ertoe leiden dat uw site niet goed werkt. 

## <a name="accessing-on-premises-resources"></a>Toegang tot on-premises resources
Apps kunnen toegang krijgen tot on-premises resources door te integreren met VNets met site-naar-site-verbindingen. Als u de gateway vereiste VNet-integratie gebruikt, moet u uw on-premises VPN-gateway routes bijwerken met uw punt-naar-site-adres blokken. Wanneer de site-naar-site-VPN voor het eerst wordt ingesteld, moeten de scripts die worden gebruikt voor het configureren van de server routes op de juiste wijze instellen. Als u de punt-naar-site-adressen toevoegt nadat u uw site-naar-site-VPN hebt gemaakt, moet u de routes hand matig bijwerken. Meer informatie over hoe u dit doet, verschilt per gateway en wordt hier niet beschreven. U kunt BGP niet geconfigureerd met een site-naar-site-VPN-verbinding.

Er is geen aanvullende configuratie vereist voor de functie van de regionale VNet-integratie die u kunt bereiken via uw VNet en on-premises. U hoeft alleen maar uw VNet te verbinden met on-premises met behulp van ExpressRoute of een site-naar-site-VPN. 

> [!NOTE]
> De gateway vereist VNet-integratie functie integreert geen app met een VNet met een ExpressRoute-gateway. Zelfs als de ExpressRoute-gateway is geconfigureerd in de modus voor samen [werking][VPNERCoex] , werkt de VNet-integratie niet. Als u toegang nodig hebt tot bronnen via een ExpressRoute-verbinding, kunt u de functie voor regionale VNet-integratie gebruiken of een [app service Environment][ASE], die wordt uitgevoerd in uw VNet. 
> 
> 

## <a name="peering"></a>Peering
Als u peering met de regionale VNet-integratie gebruikt, hoeft u geen aanvullende configuratie uit te voeren. 

Als u de gateway vereist VNet-integratie met peering gebruikt, moet u enkele extra items configureren. Peering configureren voor gebruik met uw app:

1. Een peering-verbinding toevoegen op het VNet waarmee uw app verbinding maakt. Wanneer u de peering-verbinding toevoegt, schakelt u **toegang tot virtueel netwerk toestaan** in en schakelt u **doorgestuurd verkeer toestaan** uit en laat u toe dat **gateway door Voer**.
1. Voeg een peering-verbinding toe op het VNet dat wordt gekoppeld aan het VNet waarmee u verbinding hebt. Bij het toevoegen van de peering-verbinding op het doel-VNet schakelt u **toegang tot virtueel netwerk toestaan** in en schakelt u **doorgestuurd verkeer toestaan** uit en **externe gateways toestaan**.
1. Ga naar het App Service plan > netwerken > VNet-integratie GEBRUIKERSINTERFACE in de portal.  Selecteer het VNet waarmee uw app verbinding maakt. Voeg onder de sectie route ring het adres bereik toe van het VNet dat is gekoppeld aan het VNet waarmee uw app is verbonden.  


## <a name="pricing-details"></a>Prijsinformatie
De regionale VNet-integratie functie heeft geen extra kosten voor gebruik buiten de ASP-prijs categorie kosten.

Er zijn drie kosten verbonden aan het gebruik van de gateway vereiste VNet-integratie functie:

* Kosten voor ASP-prijs Categorieën: uw apps moeten zich in een Standard-, Premium-of PremiumV2-abonnement beApp Service. U kunt hier meer informatie over deze kosten bekijken: [App service prijzen][ASPricing]. 
* Kosten voor gegevens overdracht: er worden kosten in rekening gebracht voor het afrekenen van gegevens, zelfs als het VNet zich in hetzelfde Data Center bevindt. Deze kosten worden beschreven in [gegevensoverdracht prijs informatie][DataPricing]. 
* VPN Gateway kosten: er zijn kosten verbonden aan de VNet-gateway die is vereist voor het punt-naar-site-VPN. De details zijn te vinden op de pagina met [VPN gateway prijzen][VNETPricing] .


## <a name="troubleshooting"></a>Problemen oplossen
Hoewel de functie eenvoudig kan worden ingesteld, betekent dat niet dat uw ervaring geen probleem is. Als u problemen ondervindt met het verkrijgen van toegang tot uw gewenste eind punt, kunt u een aantal hulpprogram ma's gebruiken om de connectiviteit vanuit de app-console te testen. Er zijn twee consoles die u kunt gebruiken. Een van de kudu-console en de andere is de console in de Azure Portal. Als u de kudu-console wilt bereiken vanuit uw app, gaat u naar extra-> kudu. U kunt de Kudo-console ook bereiken op [site naam]. scm. azurewebsites. net. Zodra de website is geladen, gaat u naar het tabblad debug console. Als u naar de Azure Portal gehoste console wilt gaan vanuit uw app, gaat u naar Hulpprogram Ma's->-console. 

#### <a name="tools"></a>Hulpprogramma's
De **ping**-hulpprogram ma's van **nslookup** en **tracert** werken niet via de console vanwege beveiligings beperkingen. Als u de void wilt vullen, worden er twee afzonderlijke extra hulp middelen toegevoegd. We hebben een hulp programma met de naam nameresolver. exe toegevoegd om de DNS-functionaliteit te testen. De syntaxis is:

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
* is uw doel een RFC 1918-adres
* is er een NSG die uitkomend verkeer van uw integratie subnet blokkeert
* Als u een ExpressRoute of een VPN-verbinding wilt maken, is uw on-premises gateway geconfigureerd voor het routeren van verkeer naar Azure? Als u eind punten in uw VNet maar niet on-premises kunt bereiken, is dit goed te controleren.

**vereiste VNet-integratie voor gateway**
* is het punt-naar-site-adres bereik in de RFC 1918-bereiken (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Wordt de gateway in de portal weer gegeven? Als uw gateway niet beschikbaar is, zet u deze terug.
* Worden certificaten weer gegeven als gesynchroniseerd of vermoedt u dat de netwerk configuratie is gewijzigd?  Als uw certificaten niet zijn gesynchroniseerd of als u vermoedt dat er een wijziging is aangebracht in uw VNet-configuratie die niet is gesynchroniseerd met uw ASPs, klikt u op ' netwerk synchroniseren '.
* Als u een ExpressRoute of een VPN-verbinding wilt maken, is uw on-premises gateway geconfigureerd voor het routeren van verkeer naar Azure? Als u eind punten in uw VNet maar niet on-premises kunt bereiken, is dit goed te controleren.

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


## <a name="powershell-automation"></a>Power shell-automatisering

U kunt App Service integreren met een Azure Virtual Network met behulp van Power shell. Zie [verbinding maken met een app in azure app service met een Azure-Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)voor een kant-en-klaar script.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

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
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
