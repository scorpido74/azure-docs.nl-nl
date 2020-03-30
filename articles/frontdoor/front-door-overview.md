---
title: Azure Voordeur | Microsoft Documenten
description: In dit artikel vindt u een overzicht van Azure Front Door. Ontdek of dit de juiste keuze is voor het balanceren van gebruikersverkeer voor uw toepassing.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: sharadag
ms.openlocfilehash: 0ee35f4f0b4bd8c46a0445e2905ae3b50d11f721
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79471642"
---
# <a name="what-is-azure-front-door"></a>Wat is Azure Front Door?
Azure Front Door stelt u in staat om de globale routering voor uw webverkeer te definiëren, te beheren en te bewaken door te optimaliseren voor de beste prestaties en directe wereldwijde failover voor hoge beschikbaarheid. Met Front Door u uw wereldwijde (multi-regio) consumenten- en bedrijfstoepassingen transformeren in robuuste, krachtige gepersonaliseerde moderne toepassingen, API's en inhoud die een wereldwijd publiek bereikt met Azure.

Front Door werkt in Laag 7 of de HTTP/HTTPS-laag en gebruikt anycast-protocol met split-TCP en Microsofts wereldwijde netwerk ter verbetering van internationale connectiviteit. Dus via de methode die u selecteert voor uw routering in de configuratie, kunt u ervoor zorgen dat Front Door uw klantaanvragen routeert naar het snelste en meest beschikbare toepassingsback-end. Een toepassingsback-end is een internetgerichte service die binnen of buiten Azure wordt gehost. Front Door biedt een scala aan [routeringsmethoden voor verkeer](front-door-routing-methods.md) en [opties voor back-endstatuscontrole](front-door-health-probes.md) om verschillende toepassingsbehoeften en modellen voor automatische failover mogelijk te kunnen maken. Front Door is vergelijkbaar met [Traffic Manager](../traffic-manager/traffic-manager-overview.md) en bestand tegen storingen, waaronder het uitvallen van een hele Azure-regio.

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Als u op zoek bent naar een internationale routering op basis van DNS en u voldoet **niet** aan de vereisten voor beëindiging van het TLS-protocol (Transport Layer Security), ('SSL-offload') of aanvragen per HTTP/HTTPS-aanvraag, verwerking via de toepassingslaag, raadpleegt u [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Als u op zoek ben naar een taakverdeling tussen uw servers in een regio, raadpleegt u voor de toepassingslaag [Application Gateway](../application-gateway/application-gateway-introduction.md) en voor de netwerklaag [Load Balancer](../load-balancer/load-balancer-overview.md). Uw end-to-end scenario 's kunnen eventueel profiteren van een combinatie van deze oplossingen.
>
> Zie [Overzicht van opties voor taakverdeling in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)voor een vergelijking met azure-taakverdelingsopties.

De volgende functies zijn opgenomen in Front Door:

## <a name="accelerate-application-performance"></a>Prestaties van toepassingen versnellen
Met behulp van het anycast-protocol op basis van split-TCP zorgt Front Door ervoor dat uw eindgebruikers snel verbinding maken met de dichtstbijzijnde Front Door POP (Point of Presence). Door Microsofts wereldwijde netwerk te gebruiken om verbinding te maken met uw toepassingsback-ends vanaf Front Door POP’s zorgt u voor hogere beschikbaarheid en betrouwbaarheid zonder in te leveren op prestatie. Deze verbinding met uw back-end is ook gebaseerd op zo weinig mogelijk netwerklatentie. Meer informatie over Front Door-routeringstechnieken als [Split-TCP](front-door-routing-architecture.md#splittcp) en [Anycast-protocol](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>De beschikbaarheid van toepassingen verhogen met slimme statuscontroles

Front Door biedt hoge beschikbaarheid voor uw essentiële toepassingen met slimme statuscontroles, door uw back-end te controleren op latentie en beschikbaarheid en door directe automatische failover te bieden wanneer een back-end uitvalt. U kunt dus zonder downtime geplande onderhoudswerkzaamheden op uw toepassingen uitvoeren. Front Door zorgt ervoor dat tijdens het onderhoud het verkeer naar alternatieve back-ends wordt doorgestuurd.

## <a name="url-based-routing"></a>URL-gebaseerde routering
Met op URL-pad gebaseerde routering kunt u verkeer routeren naar back-endpools die zijn gebaseerd op de URL-paden van de aanvraag. Een van de scenario's is het routeren van aanvragen voor verschillende inhoudstypen naar verschillende back-endpools.

Aanvragen voor `http://www.contoso.com/users/*` worden bijvoorbeeld doorgestuurd naar UserProfilePool en aanvragen voor `http://www.contoso.com/products/*` worden doorgestuurd naar ProductInventoryPool.  Met Front Door kunnen zelfs complexere scenario’s voor overeenkomende routes gebruikmaken van de beste overeenkomstalgoritmes en als dus geen van de padpatronen overeenkomen wordt uw standaardregel voor doorsturen voor `http://www.contoso.com/*` geselecteerd en wordt het verkeer naar de standaardopvangregel voor doorsturen doorgestuurd. Meer informatie op [Route Matching](front-door-route-matching.md).

## <a name="multiple-site-hosting"></a>Hosting van meerdere sites
Door meerdere sites te hosten, kunt u meer dan één website configureren op dezelfde Front Door-configuratie. Met deze functie kunt u een efficiëntere topologie voor uw implementaties configureren door verschillende websites toe te voegen aan één Front Door-configuratie. Op basis van de architectuur van uw toepassing u Azure Front Door configureren om elke website naar een eigen backendpool te leiden of verschillende websites naar dezelfde backendpool te laten leiden. Front Door kan bijvoorbeeld verkeer voor `images.contoso.com` en `videos.contoso.com` bedienen vanaf twee pools met de namen ImagePool en VideoPool. U kunt ook beide front-endhosts configureren om verkeer naar een enkele back-endpool met de naam MediaPool door te sturen.

U kunt ook twee verschillende domeinen `www.contoso.com` en `www.fabrikam.com` configureren op dezelfde Front Door.

## <a name="session-affinity"></a>Sessieaffiniteit
De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op hetzelfde toepassingsback-end wilt behouden. Met behulp van door Front Door beheerde cookies wordt het daarop volgende verkeer van een gebruikerssessie naar hetzelfde toepassingsback-end geleid voor verwerking. Deze functie is belangrijk wanneer de sessiestatus lokaal wordt opgeslagen op de back-end voor een gebruikerssessie.

## <a name="secure-sockets-layer-ssl-termination"></a>SSL-beëindiging (Secure Sockets Layer)
Front Door ondersteunt geavanceerd SSL-beëindigen, dat wil zeggen dat individuele gebruikers een SSL-verbinding kunnen opzetten met Front Door-omgevingen in plaats van verbinding te maken met het toepassingsback-end over langeafstandsverbindingen. Bovendien ondersteunt Front Door zowel HTTP- als HTTPS-connectiviteit tussen Front Door-omgevingen en uw back-ends. U kunt dus ook end-to-end SSL-versleuteling instellen. Als Front Door bijvoorbeeld voor de workload van uw toepassing meer dan 5000 aanvragen per minuut ontvangt voor actieve services als gevolg van hergebruik van warme verbindingen, worden er zeg maar ongeveer 500 verbindingen gemaakt met uw toepassingsback-end om zo de workload van uw back-ends aanmerkelijk te reduceren.

## <a name="custom-domains-and-certificate-management"></a>Aangepast domein- en certificaatbeheer
Wanneer u Front Door gebruikt voor het leveren van inhoud, is een aangepast domein nodig als u wilt dat uw eigen domeinnaam zichtbaar is in de URL van Front Door. Een zichtbare domeinnaam kan handig zijn voor uw klanten en nuttig zijn voor branding-doelen.
Front Door ondersteunt ook HTTPS voor aangepaste domeinnamen. Gebruik deze functie door te kiezen voor door Front Door beheerde certificaten voor uw verkeer of uw eigen SSL-certificaat te uploaden.

## <a name="application-layer-security"></a>Toepassingslaagbeveiliging
Met Azure Front Door u aangepaste WAF-regels (Web Application Firewall) opstellen voor toegangscontrole om uw HTTP/HTTPS-workload te beschermen tegen uitbuiting op basis van CLIENT-IP-adressen, landcode en http-parameters. Bovendien kunt u met Front Door regels voor snelheidsbeperkingen maken om kwaadaardig botverkeer tegen te gaan. Zie Wat is Azure Web Application Firewall voor meer informatie over Web Application [Firewall?](../web-application-firewall/overview.md)

Het Front Door-platform wordt zelf beschermd door [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) Basic. Voor verdere bescherming kan Azure DDoS Protection Standard worden ingeschakeld op uw VNETs en bronnen beschermen tegen netwerklaagaanvallen (TCP/UDP) via automatische afstemming en risicobeperking. Front Door is een omgekeerde proxy in laag 7 en laat webverkeer alleen door naar back-ends en blokkeert standaard ander verkeer.

## <a name="url-redirection"></a>URL-omleiding
Met de sterke industrie push op het ondersteunen van alleen veilige communicatie, webapplicaties zullen naar verwachting automatisch omleiden van alle HTTP-verkeer naar HTTPS. Dit zorgt ervoor dat alle communicatie tussen de gebruikers en de toepassing plaatsvindt via een versleuteld pad. 

Traditioneel hebben eigenaren van toepassingen deze vereiste behandeld door een speciale service te maken, waarvan het enige doel was om aanvragen die het op HTTP ontvangt, om te leiden naar HTTPS. Azure Front Door ondersteunt de mogelijkheid om verkeer om te leiden van HTTP naar HTTPS. Dit vereenvoudigt de configuratie van toepassingen, optimaliseert het resourcegebruik en biedt ondersteuning voor nieuwe omleidingsscenario's, waaronder de globale en op pad gebaseerde omleidingen. URL-omleiding van Azure Front Door is niet beperkt tot HTTP naar HTTPS-omleiding alleen, maar ook om om te leiden naar een andere hostnaam, om te leiden naar een ander pad of zelfs om te leiden naar een nieuwe queryreeks in de URL.

Zie [verkeer omleiden](front-door-url-redirect.md) met Azure Front Door voor meer informatie.

## <a name="url-rewrite"></a>URL opnieuw genereren
Front Door ondersteunt [het herschrijven van URL’s](front-door-url-rewrite.md), omdat u een optioneel Custom Forwarding-pad kunt configureren dat u kunt gebruiken wanneer u de aanvraag om door te sturen naar het back-end opbouwt. Met Front Door kunt u bovendien de host-header configureren die wordt verzonden wanneer de aanvraag wordt doorgestuurd naar uw back-end.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Protocolondersteuning: IPv6 en HTTP/2-verkeer
Azure Front Door biedt systeemeigen ondersteuning voor end-to-end-IPv6-connectiviteit en ook het HTTP/2-protocol. 

Het HTTP-/2-protocol maakt full-duplex-communicatie tussen toepassingback-ends en een client mogelijk via een langdurige TCP-verbinding. HTTP/2 maakt een meer interactieve communicatie mogelijk tussen het back-end en de client, die bidirectioneel kan zijn zonder dat hiervoor polling nodig is, zoals vereist in implementaties op basis van HTTP. Het HTTP/2-protocol heeft weinig overhead, in tegenstelling tot HTTP, en kunnen dezelfde TCP-verbinding gebruiken voor meerdere aanvragen/reacties. Dit resulteert in een efficiënter gebruik van resources. Meer informatie over [HTTP/2-ondersteuning in Azure Front Door](front-door-http2.md).

## <a name="pricing"></a>Prijzen

Zie [Prijzen van Front Door](https://azure.microsoft.com/pricing/details/frontdoor/) voor informatie over de prijzen.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
