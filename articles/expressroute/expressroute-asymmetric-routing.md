---
title: 'Azure ExpressRoute: Asymmetrische routering'
description: In dit artikel worden problemen met asymmetrische routering besproken die u kunt tegenkomen in een netwerk met meerdere koppelingen naar een bestemming.
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: osamam
ms.openlocfilehash: 8adfcc6559e3e2d48aabd3cfeec4fe20541917c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072145"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Asymmetrische routering met meerdere netwerkpaden
In dit artikel wordt uitgelegd hoe uitgaand en binnenkomend netwerkverkeer verschillende routes kan nemen wanneer er meerdere paden beschikbaar zijn tussen netwerkbron en -bestemming.

Om asymmetrische routering te begrijpen, moet u twee concepten begrijpen. Het ene is het effect van meerdere netwerkpaden. Het andere is hoe apparaten, zoals een firewall, hun staat behouden (keep state). Dit soort apparaten worden stateful apparaten genoemd. Door een combinatie van deze twee factoren ontstaan scenario's waarin verkeer wordt verwijderd door een stateful apparaat, omdat het stateful apparaat niet heeft gedetecteerd dat verkeer afkomstig was van het apparaat zelf.

## <a name="multiple-network-paths"></a>Meerdere netwerkpaden
Wanneer een bedrijfsnetwerk maar één koppeling met internet heeft via de internetprovider, loopt al het verkeer naar en van internet via hetzelfde pad. Bedrijven schaffen vaak meerdere aansluitingen aan, als redundante paden, ter verbetering van de uptime van het netwerk. In die gevallen is het mogelijk dat verkeer dat buiten het netwerk naar internet gaat via de ene koppeling gaat, en het retourverkeer via een andere koppeling komt. Dit wordt doorgaans asymmetrische routering genoemd. Bij asymmetrische routering neemt het terugkerende netwerkverkeer een andere weg dan de uitgaande stroom.

![Netwerk met meerdere paden](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Hoewel dit zich voornamelijk voordoet op internet, is asymmetrische routering ook van toepassing op andere combinaties van meerdere paden. Het is bijvoorbeeld ook van toepassing wanneer een internetpad en een persoonlijk pad dezelfde bestemming hebben, en wanneer meerdere persoonlijke paden eenzelfde bestemming hebben.

Elke router berekent onderweg van bron naar doel het beste pad om een bestemming te bereiken. De manier waarop de router het optimale pad bepaalt, is gebaseerd op twee hoofdfactoren:

* Routering tussen externe netwerken is gebaseerd op een routeringsprotocol, het Border Gateway Protocol (BGP). BGP kijkt naar aankondigingen van neighbors en voert hier een aantal stappen op uit om het beste pad naar de bestemming te bepalen. Het beste pad wordt opgeslagen in de routeringstabel.
* De routeringspaden worden beïnvloed door de lengte van een subnetmasker dat aan een route is gekoppeld. Als een router meerdere aankondigingen voor hetzelfde IP-adres maar met verschillende subnetmaskers ontvangt, krijgt de aankondiging met het langere subnetmasker de voorkeur, omdat deze als een meer specifieke route wordt beschouwd.

## <a name="stateful-devices"></a>Stateful apparaten
Routers kijken naar de IP-header van een pakket voor routeringsdoeleinden. Er zijn echter apparaten die nog dieper in het pakket kijken. Normaal gesproken kijken deze apparaten naar Layer4- (Transmission Control Protocol of TCP; of User Datagram Protocol of UDP), of zelfs Layer7-headers (toepassingslaag). Dergelijke apparaten zijn beveiligingsapparaten of apparaten die de bandbreedte optimaliseren. 

Een firewall is een algemeen voorbeeld van een stateful apparaat. De firewall bepaalt op basis van verschillende velden, zoals protocol, TCP/UDP-poort en URL-headers, of een pakket de interfaces mag passeren. Dit niveau van pakketinspectie betekent een grote verwerkingsbelasting voor het apparaat. Om de prestaties te verbeteren, inspecteert de firewall het eerste pakket van een stroom. Als het pakket mag doorgaan, worden de stroomgegevens in de statustabel van de firewall bewaard. Alle volgende pakketten met betrekking tot deze stroom zijn toegestaan op basis van de eerste beslissing. Een pakket dat deel uitmaakt van een bestaande stroom, kan aankomen bij de firewall. Als de firewall geen eerdere statusinformatie over het pakket heeft, wordt het pakket door de firewall geweigerd.

## <a name="asymmetric-routing-with-expressroute"></a>Asymmetrische routering met ExpressRoute
Wanneer u via ExpressRoute verbinding maakt met Microsoft, verandert het netwerk als volgt:

* U hebt meerdere koppelingen naar Microsoft. De ene koppeling is uw bestaande internetverbinding en de andere gaat via ExpressRoute. Bepaald verkeer naar Microsoft kan via internet gaan maar kan terugkeren via ExpressRoute of andersom.
* U ontvangt specifiekere IP-adressen via ExpressRoute. Voor verkeer van uw netwerk naar Microsoft voor services die worden aangeboden via ExpressRoute, geven routers dus altijd de voorkeur aan ExpressRoute.

Om te begrijpen welk effect deze twee wijzigingen op een netwerk hebben, gaan we enkele scenario's bekijken. Stel, u hebt maar één aansluiting met internet en alle Microsoft-services worden via internet gebruikt. Het verkeer van uw netwerk naar Microsoft en terug passeert via dezelfde internetkoppeling en passeert de firewall. De firewall registreert de stroom wanneer het eerste pakket binnenkomt en retourpakketten worden toegestaan, aangezien de stroom in de statustabel bestaat.

![Asymmetrische routering met ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Vervolgens schakelt u ExpressRoute in, en gebruikt u services die door Microsoft worden aangeboden nu via ExpressRoute. Alle andere services van Microsoft worden via internet gebruikt. U implementeert een afzonderlijke firewall aan de rand die is verbonden met ExpressRoute. Microsoft kondigt voor specifieke services specifiekere voorvoegsels naar het netwerk via ExpressRoute aan. De routeringsinfrastructuur kiest ExpressRoute als het voorkeurspad voor die voorvoegsels. Als u uw openbare IP-adressen niet bij Microsoft aankondigt via ExpressRoute, zal Microsoft via internet met uw openbare IP-adressen communiceren. Voorwaarts verkeer van uw netwerk naar Microsoft gebruikt dus ExpressRoute, en terugkerend verkeer van Microsoft maakt gebruik van internet. Wanneer de firewall aan de rand een antwoordpakket ziet voor een stroom die niet wordt gevonden in de statustabel, wordt het retourverkeer verwijderd.

Als u ervoor kiest om dezelfde NAT-pool (Network Address Translation) te adverteren voor ExpressRoute en voor het internet, ziet u vergelijkbare problemen met de clients in uw netwerk op privé-IP-adressen. Aanvragen voor services zoals Windows Update verlopen via internet omdat IP-adressen voor deze services niet worden verzonden via ExpressRoute. Het retourverkeer keert echter terug via ExpressRoute. Als Microsoft een IP-adres met hetzelfde subnetmasker van internet en ExpressRoute ontvangt, verkiest het ExpressRoute boven internet. Als een firewall of een ander stateful apparaat aan de rand van uw netwerk en gericht op ExpressRoute geen eerdere informatie over de stroom heeft, worden de pakketten die behoren tot deze gegevensstroom verwijderd.

## <a name="asymmetric-routing-solutions"></a>Oplossingen voor asymmetrische routering
Er zijn in feite twee oplossingen voor het probleem van asymmetrische routering. De ene is routering en de andere is het gebruik van brongebaseerde NAT (SNAT).

### <a name="routing"></a>Routering
Zorg ervoor dat uw openbare IP-adressen zijn aangekondigd aan de juiste WAN-koppelingen (Wide Area Network). Als u bijvoorbeeld internet wilt gebruiken voor verificatieverkeer en ExpressRoute voor uw e-mailverkeer, dan moet u uw openbare IP-adressen voor AD FS (Active Directory Federation Services) niet aankondigen via ExpressRoute. Evenzo moet u ervoor zorgen dat u een on-premises AD FS-server niet blootstelt aan IP-adressen die de router via ExpressRoute ontvangt. Routes die worden ontvangen via ExpressRoute zijn specifieker, zodat ExpressRoute het voorkeurspad wordt voor verificatieverkeer naar Microsoft. Dit veroorzaakt asymmetrische routering.

Als u ExpressRoute wilt gebruiken voor verificatie, zorg dan dat u openbare IP-adressen van ADFS via ExpressRoute aankondigt zonder NAT. Op deze manier loopt het verkeer dat afkomstig is van Microsoft en naar een on-premises AD FS-server gaat via ExpressRoute. Retourverkeer van de klant naar Microsoft maakt gebruik van ExpressRoute omdat het de voorkeursroute is via internet.

### <a name="source-based-nat"></a>Brongebaseerde NAT
Een andere oplossing voor problemen met asymmetrische routering is het gebruik van SNAT. U hebt bijvoorbeeld het openbare IP-adres van een on-premises SMTP-server (Simple Mail Transfer Protocol) niet via ExpressRoute aangekondigd omdat u voor dit type communicatie internet wilt gebruiken. Een aanvraag die afkomstig is van Microsoft en vervolgens naar uw on-premises SMTP-server gaat, passeert via internet. U verzendt de inkomende aanvraag via SNAT naar een intern IP-adres. Terugkerend verkeer van de SMTP-server gaat naar de edge-firewall (die u voor NAT gebruikt) in plaats van via ExpressRoute. Het retourverkeer gaat terug via internet.

![Brongebaseerde NAT-netwerkconfiguratie](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Detectie van asymmetrische routering
Traceroute is de beste manier om ervoor te zorgen dat uw netwerkverkeer via het verwachte pad loopt. Als u verwacht dat verkeer van de on-premises SMTP-server naar Microsoft via het internetpad gaat, dan loopt de verwachte traceroute van de SMTP-server naar Office 365. Zo wordt gevalideerd of het verkeer inderdaad uw netwerk verlaat richting internet en niet richting ExpressRoute.

