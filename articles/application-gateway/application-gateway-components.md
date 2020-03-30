---
title: Toepassingsgatewaycomponenten
description: In dit artikel vindt u informatie over de verschillende onderdelen in een toepassingsgateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 90b3c3fd18bc9211c731ccf16dd646a64a4a1116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133095"
---
# <a name="application-gateway-components"></a>Toepassingsgatewaycomponenten

 Een applicatiegateway fungeert als het enige aanspreekpunt voor clients. Het distribueert binnenkomend toepassingsverkeer over meerdere backendpools, waaronder Azure VM's, virtuele machineschaalsets, Azure App Service en on-premises/externe servers. Om verkeer te distribueren, gebruikt een toepassingsgateway verschillende componenten die in dit artikel worden beschreven.

![De componenten die worden gebruikt in een toepassingsgateway](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Frontend IP-adressen

Een frontend IP-adres is het IP-adres dat is gekoppeld aan een toepassingsgateway. U een toepassingsgateway configureren om een openbaar IP-adres, een privé-IP-adres of beide te hebben. Een toepassingsgateway ondersteunt één openbaar of één privé IP-adres. Uw virtuele netwerk en openbare IP-adres moeten zich op dezelfde locatie bevinden als uw toepassingsgateway. Nadat het is gemaakt, wordt een ip-adres aan de frontend gekoppeld aan een listener.

### <a name="static-versus-dynamic-public-ip-address"></a>Statisch versus dynamisch openbaar IP-adres

De Azure Application Gateway V2 SKU kan worden geconfigureerd om zowel statisch intern IP-adres als statisch openbaar IP-adres of alleen statisch openbaar IP-adres te ondersteunen. Het kan niet worden geconfigureerd om alleen statisch intern IP-adres te ondersteunen.

De V1 SKU kan worden geconfigureerd om statisch of dynamisch intern IP-adres en dynamisch openbaar IP-adres te ondersteunen. Het dynamische IP-adres van Application Gateway verandert niet op een lopende gateway. Het kan alleen veranderen wanneer u de Gateway stopt of start. Het verandert niet op systeemfouten, updates, Azure host updates etc. 

De DNS-naam die is gekoppeld aan een toepassingsgateway verandert niet gedurende de levenscyclus van de gateway. Als gevolg hiervan moet u een CNAME-alias gebruiken en deze naar het DNS-adres van de toepassingsgateway aanwijzen.

## <a name="listeners"></a>Listeners

Een listener is een logische entiteit die controleert op binnenkomende verbindingsaanvragen. Een listener accepteert een aanvraag als het protocol, de poort, de hostnaam en het IP-adres dat aan het verzoek is gekoppeld, overeenkomen met dezelfde elementen die zijn gekoppeld aan de listenerconfiguratie.

Voordat u een toepassingsgateway gebruikt, moet u ten minste één listener toevoegen. Er kunnen meerdere listeners zijn gekoppeld aan een toepassingsgateway en ze kunnen worden gebruikt voor hetzelfde protocol.

Nadat een listener binnenkomende aanvragen van clients detecteert, leidt de toepassingsgateway deze aanvragen door naar leden in de backendpool die in de regel zijn geconfigureerd.

Luisteraars ondersteunen de volgende poorten en protocollen.

### <a name="ports"></a>Poorten

Een poort is waar een luisteraar luistert naar het clientverzoek. U poorten configureren variërend van 1 tot 65502 voor de v1 SKU en 1 tot 65199 voor de v2 SKU.

### <a name="protocols"></a>Protocollen

Application Gateway ondersteunt vier protocollen: HTTP, HTTPS, HTTP/2 en WebSocket:
>[!NOTE]
>HTTP/2-protocolondersteuning is alleen beschikbaar voor clients die verbinding maken met toepassingsgatewaylisteners. De communicatie naar backendserverpools is altijd via HTTP/1.1. Standaard is HTTP/2-ondersteuning uitgeschakeld. U ervoor kiezen om het in te schakelen.

- Opgeven tussen de HTTP- en HTTPS-protocollen in de listenerconfiguratie.
- Ondersteuning voor [WebSockets en HTTP/2-protocollen](features.md#websocket-and-http2-traffic) wordt native geleverd en [WebSocket-ondersteuning](application-gateway-websocket.md) is standaard ingeschakeld. Er is geen door de gebruiker configureerbare instelling om selectief WebSocket-ondersteuning in of uit te schakelen. Gebruik WebSockets met zowel HTTP- als HTTPS-listeners.

Gebruik een HTTPS-listener voor TLS-beëindiging. Een HTTPS-listener ontlaadt de versleuteling en decryptie werken naar uw toepassingsgateway, zodat uw webservers niet worden belast door de overhead.

### <a name="custom-error-pages"></a>Aangepaste foutenpagina's

Met Application Gateway u aangepaste foutpagina's maken in plaats van standaardfoutpagina's weer te geven. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken. Application Gateway geeft een aangepaste foutpagina weer wanneer een aanvraag de backend niet kan bereiken.

Zie [Aangepaste foutpagina's voor uw toepassingsgateway voor](custom-error.md)meer informatie.

### <a name="types-of-listeners"></a>Typen luisteraars

Er zijn twee soorten luisteraars:

- **Basic**. Dit type listener luistert naar één domeinsite, waar het één DNS-toewijzing heeft naar het IP-adres van de toepassingsgateway. Deze listenerconfiguratie is vereist wanneer u één site achter een toepassingsgateway host.

- **Multi-site**. Deze listenerconfiguratie is vereist wanneer u meer dan één webtoepassing configureert op dezelfde toepassingsgateway-instantie. Hiermee u een efficiëntere topologie voor uw implementaties configureren door maximaal 100 websites toe te voegen aan één toepassingsgateway. Elke website kan worden omgeleid naar een eigen back-endpool. Drie subdomeinen, abc.contoso.com, xyz.contoso.com en pqr.contoso.com, wijzen bijvoorbeeld naar het IP-adres van de toepassingsgateway. U maakt drie multi-site listeners en configureert elke listener voor de respectievelijke poort- en protocolinstelling.

    Zie [Hosting met meerdere site's](application-gateway-web-app-overview.md)voor meer informatie.

Nadat u een listener hebt gemaakt, koppelt u deze aan een routeringsregel voor aanvragen. Deze regel bepaalt hoe de aanvraag die op de listener wordt ontvangen, moet worden doorgestuurd naar de backend.

Application Gateway verwerkt luisteraars in de [weergegeven volgorde](configuration-overview.md#order-of-processing-listeners).

## <a name="request-routing-rules"></a>Routeringsregels aanvragen

Een regel voor het routeren van aanvragen is een belangrijk onderdeel van een toepassingsgateway omdat deze bepaalt hoe verkeer op de listener moet worden routeren. De regel bindt de listener, de back-endservergroep en de BACKend HTTP-instellingen.

Wanneer een listener een aanvraag accepteert, stuurt de routeringsregel van de aanvraag het verzoek door naar de backend of stuurt het naar elders. Als de aanvraag wordt doorgestuurd naar de backend, bepaalt de regel voor het routeren van aanvragen naar welke backendservergroep u deze wilt doorsturen. De regel voor het routeren van aanvragen bepaalt ook of de kopteksten in de aanvraag moeten worden herschreven. Eén luisteraar kan aan één regel worden gekoppeld.

Er zijn twee soorten regels voor het routeren van aanvragen:

- **Basic**. Alle aanvragen op de gekoppelde listener (bijvoorbeeld blog.contoso.com/*) worden doorgestuurd naar de bijbehorende backendpool met behulp van de bijbehorende HTTP-instelling.

- **Op pad gebaseerd**. Met deze routeringsregel u de aanvragen voor de gekoppelde listener doorsturen naar een specifieke backendpool, op basis van de URL in de aanvraag. Als het pad van de URL in een aanvraag overeenkomt met het padpatroon in een op paden gebaseerde regel, wordt de regel die verzoek aangevraagd. Het padpatroon wordt alleen gebruikt op het URL-pad, niet op de queryparameters. Als het URL-pad op een listenerverzoek niet overeenkomt met een van de op paden gebaseerde regels, wordt het verzoek doorgestoend naar de standaard backendpool en HTTP-instellingen.

Zie [URL-gebaseerde routering](url-route-overview.md)voor meer informatie.

### <a name="redirection-support"></a>Ondersteuning voor omleiding

Met de regel voor het routeren van aanvragen u ook verkeer op de toepassingsgateway omleiden. Dit is een generiek omleidingsmechanisme, zodat u omleiden naar en vanuit elke poort die u definieert met behulp van regels.

U het omleidingsdoel kiezen als een andere listener (waarmee u automatische HTTP-omleiding naar HTTPS kan inschakelen) of een externe site. U er ook voor kiezen om de omleiding tijdelijk of permanent te laten zijn, of om het URI-pad en de querytekenreeks toe te wijzen aan de doorgestuurde URL.

Zie [Verkeer op uw toepassingsgateway omleiden](redirect-overview.md)voor meer informatie.

### <a name="rewrite-http-headers"></a>HTTP-headers opnieuw genereren

Door gebruik te maken van de regels voor het routeren van aanvragen, u HTTP-aanvragen en antwoordkoppen toevoegen, verwijderen of bijwerken terwijl de aanvraag- en antwoordpakketten tussen de client en backendpools via de toepassingsgateway worden verplaatst.

De kopteksten kunnen worden ingesteld op statische waarden of op andere kopteksten en servervariabelen. Dit helpt bij belangrijke use cases, zoals het extraheren van CLIENT IP-adressen, het verwijderen van gevoelige informatie over de backend, het toevoegen van meer beveiliging, enzovoort.

Zie [HTTP-headers opnieuw schrijven op de toepassingsgateway](rewrite-http-headers.md)voor meer informatie.

## <a name="http-settings"></a>HTTP-instellingen

Een toepassingsgateway leidt het verkeer naar de backendservers (opgegeven in de regel voor het routeren van aanvragen die HTTP-instellingen bevatten) met behulp van het poortnummer, het protocol en andere instellingen die in dit onderdeel zijn beschreven.

De poort en het protocol die in de HTTP-instellingen worden gebruikt, bepalen of het verkeer tussen de toepassingsgateway en backendservers is versleuteld (het verstrekken van end-to-end TLS) of onversleuteld.

Dit onderdeel wordt ook gebruikt om:

- Bepaal of een gebruikerssessie op dezelfde server moet worden bewaard met behulp van de [sessieaffiniteit op basis van cookies.](features.md#session-affinity)

- Verwijder op een elegante manier backend poolleden door het gebruik van [verbinding aftappen](features.md#connection-draining).

- Een aangepaste sonde koppelen om de backendstatus te controleren, het time-outinterval van de aanvraag in te stellen, hostnaam en pad in de aanvraag te overschrijven en met één klik te bieden om instellingen voor de backend van de appservice op te geven.

## <a name="backend-pools"></a>Backend pools

Een backend pool routes verzoek naar backend servers, die het verzoek te dienen. Backend pools kunnen bevatten:

- NIC’s
- Virtuele-machineschaalsets
- Openbare IP-adressen
- Interne IP-adressen
- FQDN
- Backends voor meerdere tenant 's (zoals App Service)

Leden van de backendpool van Application Gateway zijn niet gebonden aan een beschikbaarheidsset. Een toepassingsgateway kan communiceren met instanties buiten het virtuele netwerk waarin deze zich bevindt. Als gevolg hiervan kunnen de leden van de backendpools zich over clusters, in datacenters of buiten Azure bevinden, zolang er IP-connectiviteit is.

Als u interne IP's gebruikt als backendpoolleden, moet u [virtuele netwerkpeering](../virtual-network/virtual-network-peering-overview.md) of een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)gebruiken. Virtuele netwerkpeering wordt ondersteund en gunstig voor load-balancing verkeer in andere virtuele netwerken.

Een toepassingsgateway kan ook communiceren met on-premises servers wanneer deze zijn verbonden via Azure ExpressRoute of VPN-tunnels als verkeer is toegestaan.

U verschillende backendpools maken voor verschillende typen aanvragen. Maak bijvoorbeeld één backendpool voor algemene aanvragen en vervolgens een andere backendpool voor aanvragen voor de microservices voor uw toepassing.

## <a name="health-probes"></a>Statuscontroles

Standaard controleert een toepassingsgateway de status van alle resources in de backendgroep en verwijdert deze automatisch ongezonde bronnen. Vervolgens worden ongezonde exemplaren bewaakt en weer toegevoegd aan de gezonde backendpool wanneer ze beschikbaar zijn en reageren op gezondheidssondes.

Naast het gebruik van standaard statussondebewaking, u de statussonde ook aanpassen aan de vereisten van uw toepassing. Aangepaste sondes zorgen voor meer gedetailleerde controle over de statusbewaking. Wanneer u aangepaste sondes gebruikt, u het sondeinterval, de URL en het te testen pad configureren en hoeveel mislukte antwoorden moeten worden geaccepteerd voordat de instantie backendpool als ongezond is gemarkeerd. We raden u aan aangepaste sondes te configureren om de status van elke backendpool te controleren.

Zie [De status van uw toepassingsgateway controleren](../application-gateway/application-gateway-probe-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Een toepassingsgateway maken:

* [In de Azure-portal](quick-create-portal.md)
* [Met Azure PowerShell](quick-create-powershell.md)
* [Met behulp van de Azure CLI](quick-create-cli.md)
