---
title: Azure Application Gateway-functies
description: Meer informatie over Azure Application Gateway-functies
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 550d9f4f5396b2165260e39cd28222b083dd6756
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279986"
---
# <a name="azure-application-gateway-features"></a>Azure Application Gateway-functies

[Azure Application Gateway](overview.md) is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren.

![Application Gateway conceptual](media/overview/figure1-720.png)

Application Gateway bevat de volgende functies:

- [Ssl/TLS-beëindiging (Secure Sockets Layer)](#secure-sockets-layer-ssltls-termination)
- [Automatisch schalen](#autoscaling)
- [Zoneredundantie](#zone-redundancy)
- [Statische VIP](#static-vip)
- [Web Application Firewall](#web-application-firewall)
- [Controller van inkomend verkeer voor AKS](#ingress-controller-for-aks)
- [Op URL gebaseerde routering](#url-based-routing)
- [Hosting van meerdere sites](#multiple-site-hosting)
- [Omleiding](#redirection)
- [Sessieaffiniteit](#session-affinity)
- [Websocket- en HTTP-/2-verkeer](#websocket-and-http2-traffic)
- [Verwerkingsstop voor verbindingen](#connection-draining)
- [Aangepaste foutenpagina's](#custom-error-pages)
- [HTTP-headers opnieuw genereren](#rewrite-http-headers)
- [Grootte aanpassen](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Ssl/TLS-beëindiging (Secure Sockets Layer)

Application gateway ondersteunt SSL/TLS-beëindiging bij de gateway, waarna verkeer meestal onversleuteld naar de backendservers stroomt. Met deze functie voorkomt u prijzige overhead voor het versleutelen en ontsleutelen voor uw webservers. Maar soms is onversleutelde communicatie naar de servers geen acceptabele optie. Dit kan zijn vanwege beveiligingsvereisten, nalevingsvereisten of de toepassing kan alleen een beveiligde verbinding accepteren. Voor deze toepassingen ondersteunt application gateway end-to-end SSL/TLS-versleuteling.

Zie [Overzicht van SSL-beëindiging en end-to-end SSL met Application Gateway](ssl-overview.md) voor meer informatie

## <a name="autoscaling"></a>Automatisch schalen

Application Gateway Standard_v2 ondersteunt automatisch schalen en kan worden opgeschaald of omlaag op basis van veranderende verkeersbelastingspatronen. Automatisch schalen heft ook de vereiste op om tijdens het inrichten een implementatiegrootte of het aantal instanties te kiezen. 

Zie [Autoscaling v2 SKU](application-gateway-autoscaling-zone-redundant.md)voor meer informatie over de functies van de Application Gateway Standard_v2.

## <a name="zone-redundancy"></a>Zoneredundantie

Een Standard_v2 Application Gateway kan meerdere beschikbaarheidszones omvatten, waardoor een betere fouttolerantie wordt verholpen en de noodzaak om afzonderlijke toepassingsgateways in elke zone te plaatsen, wordt verwijderd.

## <a name="static-vip"></a>Statische VIP

De toepassingsgateway Standard_v2 SKU ondersteunt uitsluitend statisch VIP-type. Dit zorgt ervoor dat de VIP die is gekoppeld aan de toepassingsgateway niet verandert, zelfs niet gedurende de levensduur van de Application Gateway.

## <a name="web-application-firewall"></a>Web Application Firewall

Web Application Firewall (WAF) is een service die gecentraliseerde bescherming van uw webapplicaties biedt tegen veelvoorkomende exploits en kwetsbaarheden. WAF is gebaseerd op regels uit de [kernregelsets 3.1](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) (WAF_v2), 3.0 en 2.2.9. 

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Veelvoorkomende aanvallen zijn hierbij onder andere aanvallen met SQL-injecties en aanvallen via scripting op meerdere sites. Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen en dit kan tevens veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webtoepassingen maakt het beveiligingsbeheer veel eenvoudiger en biedt toepassingsbeheerders meer veiligheid tegen bedreigingen of aanvallen. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassingsgateways kunnen eenvoudig worden geconverteerd naar een toepassinggateway met webtoepassingfirewall.

Zie Wat is Azure Web Application Firewall voor meer [informatie?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Controller van inkomend verkeer voor AKS
Met Application Gateway Ingress Controller (AGIC) u Application Gateway gebruiken als ingress voor een [AKS-cluster (Azure Kubernetes Service).](https://azure.microsoft.com/services/kubernetes-service/) 

De invallende controller draait als een pod binnen het AKS-cluster en verbruikt [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) en converteert deze naar een Application Gateway-configuratie, waarmee de gateway verkeer naar de Kubernetes-pods kan laden. De ingress-controller ondersteunt alleen Application Gateway-Standard_v2 en WAF_v2 SKU's. 

Zie [Application Gateway Ingress Controller (AGIC) voor](ingress-controller-overview.md)meer informatie.

## <a name="url-based-routing"></a>URL-gebaseerde routering

Met op URL-pad gebaseerde routering kunt u verkeer routeren naar back-endserverpools die zijn gebaseerd op de URL-paden van de aanvraag. Een van de scenario's is het routeren van aanvragen voor verschillende inhoudstypen naar een andere pool.

Aanvragen voor `http://contoso.com/video/*` worden bijvoorbeeld doorgestuurd naar VideoServerPool en aanvragen voor `http://contoso.com/images/*` worden doorgestuurd naar ImageServerPool. Als geen van de padpatronen overeenkomen, wordt DefaultServerPool geselecteerd.

Zie [overzicht van URL-paden gebaseerde routering](url-route-overview.md)voor meer informatie .

## <a name="multiple-site-hosting"></a>Hosting van meerdere sites

Door meerdere sites te hosten, kunt u meer dan een website configureren op dezelfde instantie van de toepassingsgateway. Met deze functie u een efficiëntere topologie voor uw implementaties configureren door maximaal 100 websites toe te voegen aan één Application Gateway (voor optimale prestaties). Elke website kan worden omgeleid naar een eigen pool. Application Gateway kan bijvoorbeeld verkeer regelen voor `contoso.com` en `fabrikam.com` vanaf twee servergroepen genaamd ContosoServerPool en FabrikamServerPool.

Aanvragen voor `http://contoso.com` worden gerouteerd naar ContosoServerPool en aanvragen voor `http://fabrikam.com` worden gerouteerd naar FabrikamServerPool.

Op dezelfde manier kunnen twee subdomeinen van hetzelfde bovenliggende domein worden gehost op dezelfde implementatie van een toepassingsgateway. Voorbeelden van subdomeinen die worden gehost op één toepassingsgateway-implementatie, zijn `http://blog.contoso.com` en `http://app.contoso.com`.

Zie [Application Gateway meerdere sitehosting](multiple-site-overview.md)voor meer informatie.

## <a name="redirection"></a>Omleiding

Een veelvoorkomend scenario voor veel webtoepassingen is de ondersteuning van automatische HTTP-naar-HTTPS-omleiding zodat alle communicatie tussen een toepassing en gebruikers plaatsvindt via een versleuteld pad.

In het verleden hebt u mogelijk technieken gebruikt, zoals het maken van speciale zwembaden, waarvan het enige doel is om aanvragen die het op HTTP ontvangt, om te leiden naar HTTPS. Application Gateway ondersteunt het omleiden van verkeer op de Application Gateway. Dit vereenvoudigt de configuratie van toepassingen, optimaliseert het resourcegebruik en biedt ondersteuning voor nieuwe omleidingsscenario's, waaronder de globale en op pad gebaseerde omleidingen. Ondersteuning voor omleiding van de toepassingsgateway is niet beperkt tot HTTP tot HTTPS-omleiding alleen. Dit is een algemeen omleidingsmechanisme, zodat u op basis van regels kunt omleiden van en naar elke poort die u gebruikt. Ook omleiding naar een externe site wordt ondersteund.

Ondersteuning voor Application Gateway-omleiding biedt de volgende mogelijkheden:

- Globale omleiding van de ene poort naar de andere poort op de Gateway. Hierdoor is HTTP-naar-HTTPS-omleiding op een site mogelijk.
- Padgebaseerde omleiding. Dit type omleiding maakt HTTP-naar-HTTPS-omleiding alleen mogelijk op een specifiek sitegebied, bijvoorbeeld een winkelwagengebied aangegeven door `/cart/*`.
- Omleiden naar een externe site.

Zie [Overzicht van omleidingvan toepassinggateway](redirect-overview.md)voor meer informatie.

## <a name="session-affinity"></a>Sessieaffiniteit

De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op dezelfde server wilt behouden. Met behulp van de gatewaybeheerde cookies kan de Application Gateway het daarop volgende verkeer van een gebruikerssessie naar dezelfde server leiden voor verwerking. Dit is belangrijk wanneer de sessiestatus lokaal wordt opgeslagen op de server voor een gebruikerssessie.

Zie [Hoe een toepassingsgateway werkt](how-application-gateway-works.md#modifications-to-the-request)voor meer informatie .

## <a name="websocket-and-http2-traffic"></a>Websocket- en HTTP-/2-verkeer

Application Gateway biedt systeemeigen ondersteuning voor de WebSocket- en HTTP-/2-protocollen. Er is geen door de gebruiker configureerbare instelling om selectief WebSocket-ondersteuning in of uit te schakelen.

De WebSocket- en HTTP-/2-protocollen maken full-duplex-communicatie tussen een server en een client mogelijk via een langdurige TCP-verbinding. Dit maakt een meer interactieve communicatie mogelijk tussen de webserver en de client, die bidirectioneel kan zijn zonder dat hiervoor polling nodig is, zoals vereist in implementaties op basis van HTTP. Deze protocollen hebben een lage overhead, in tegenstelling tot HTTP, en kunnen dezelfde TCP-verbinding opnieuw gebruiken voor meerdere aanvragen/antwoorden, wat resulteert in een efficiënter gebruik van resources. Deze protocollen zijn ontworpen om te werken via de traditionele HTTP-poorten: 80 en 443.

Zie [WebSocket-ondersteuning](application-gateway-websocket.md) en [HTTP/2-ondersteuning](configuration-overview.md#http2-support)voor meer informatie.

## <a name="connection-draining"></a>Verwerkingsstop voor verbindingen

Verwerkingsstop voor verbindingen helpt u om back-endgroepsleden zonder problemen te verwijderen tijdens geplande service-updates. Deze instelling wordt ingeschakeld via de HTTP-instelling van de back-end en kan tijdens het maken van de regel worden toegepast op alle leden van een back-endgroep. Eenmaal ingeschakeld, zorgt Application Gateway ervoor dat alle uitregistratieexemplaren van een backendpool geen nieuwe aanvraag ontvangen, terwijl bestaande aanvragen binnen een geconfigureerde tijdslimiet kunnen worden voltooid. Dit geldt voor zowel backend-exemplaren die expliciet uit de backendgroep worden verwijderd door een wijziging van de gebruikersconfiguratie als backend-exemplaren die als ongezond worden gerapporteerd, zoals bepaald door de statussondes. De enige uitzondering hierop zijn aanvragen die zijn gebonden aan het uitschrijven van exemplaren, die expliciet zijn uitgeschreven vanwege de affiniteit met gatewaybeheer de sessie en nog steeds worden geproxieerd naar de instantie die wordt verwijderd.

Zie [Overzicht van configuratie van toepassingsgateway](configuration-overview.md#connection-draining)voor meer informatie .

## <a name="custom-error-pages"></a>Aangepaste foutenpagina's

Met Application Gateway kunt u aangepaste foutpagina's maken in plaats van standaardfoutpagina's weer te geven. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken.

Zie [Aangepaste fouten voor](custom-error.md)meer informatie .

## <a name="rewrite-http-headers"></a>HTTP-headers opnieuw genereren

Met HTTP-headers kunnen de client en server aanvullende informatie doorgeven aan het verzoek of het antwoord. Door deze HTTP-headers te herschrijven, u verschillende belangrijke scenario's uitvoeren, zoals:

- Het toevoegen van beveiligingsgerelateerde koptekstvelden zoals HSTS/ X-XSS-Protection.
- Het verwijderen van antwoordkopvelden die gevoelige informatie kunnen onthullen.
- Strippoortinformatie van X-Forwarded-For-headers.

Application Gateway ondersteunt de mogelijkheid om HTTP-aanvraag- en antwoordkoppen toe te voegen, te verwijderen of bij te werken, terwijl de aanvraag- en antwoordpakketten tussen de client en back-endgroepen worden verplaatst. Het biedt u ook de mogelijkheid om voorwaarden toe te voegen om ervoor te zorgen dat de opgegeven headers alleen worden herschreven wanneer aan bepaalde voorwaarden is voldaan.

Zie [HTTP-headers opnieuw schrijven voor](rewrite-http-headers.md)meer informatie .

## <a name="sizing"></a>Grootte aanpassen

Application Gateway Standard_v2 kunnen worden geconfigureerd voor automatische schaling of implementaties met een vaste grootte. Deze SKU biedt geen verschillende instantiegroottes. Zie [Autoscaling v2 SKU](application-gateway-autoscaling-zone-redundant.md#pricing)voor meer informatie over de prestaties en prijzen van V2.

De Application Gateway Standard wordt aangeboden in drie maten: **Klein,** **Gemiddeld**en **Groot**. Kleine exemplaargrootten zijn bedoeld voor het ontwikkelen en testen van scenario's.

Zie [Servicelimieten voor Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits) voor een volledige lijst van toepassingsgateway-limieten.

In de volgende tabel ziet u een gemiddelde prestatiedoorvoer voor elke v1-exemplaar van de toepassingsgateway met SSL-offload ingeschakeld:

| Gemiddelde grootte van een antwoord van de back-endpagina | Klein | Middelgroot | Groot |
| --- | --- | --- | --- |
| 6 kB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 kB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Deze waarden zijn geschatte waarden voor de doorvoer van een toepassingsgateway. De werkelijke doorvoer hangt af van verschillende details van de omgeving, zoals de gemiddelde paginagrootte, locatie van back-endexemplaren en de verwerkingstijd voor een pagina. Voor nauwkeurige prestatiecijfers moet u uw eigen tests uitvoeren. Deze waarden worden alleen geboden als richtlijn voor de capaciteitsplanning.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe Application Gateway werkt - [Hoe een toepassingsgateway werkt](how-application-gateway-works.md)