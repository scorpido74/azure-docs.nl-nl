---
title: Wat is Azure Application Gateway?
description: Ontdek hoe u een Azure-toepassingsgateway kunt gebruiken voor het beheren van webverkeer naar uw toepassing.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: a61b1a44419ac35efa5888de2b5a6e4988dfb512
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422313"
---
# <a name="what-is-azure-application-gateway"></a>Wat is Azure Application Gateway?

Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Traditionele load balancers werken op de transportlaag (OSI-laag 4 - TCP en UDP) en routeren verkeer op basis van IP-bronadres en een bronpoort naar een IP-doeladres en doelpoort.

![Application Gateway conceptual](media/overview/figure1-720.png)

Met Application Gateway kunt u routerings beslissingen nemen op basis van aanvullende kenmerken van een HTTP-aanvraag, zoals URI-pad of hostheaders. U kunt bijvoorbeeld verkeer op basis van de binnenkomende URL routeren. Dus als `/images` de binnenkomende URL is, kunt u verkeer routeren naar een specifieke set servers (ook wel een pool genoemd) die is geconfigureerd voor installatiekopieën. Als `/video` in de URL staat, wordt dat verkeer doorgestuurd naar een andere groep die is geoptimaliseerd voor Video's.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Dit type routering staat bekend al taakverdeling op de toepassingslaag (OSI-laag 7). Azure Application Gateway kan URL-gebaseerde routering en meer uitvoeren.

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Zie [Wat is Azure Load Balancer?](../load-balancer/load-balancer-overview.md) als u behoefte hebt aan hoge prestaties en lage latentie, laag-4 taak verdeling. Als u op zoek bent naar globale DNS-taak verdeling, raadpleegt u [Wat is Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Uw end-to-end-scenario's kunnen van pas komen bij het combi neren van deze oplossingen.
>
> Zie [overzicht van opties voor taak verdeling in azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)voor een vergelijking van Azure-opties voor taak verdeling.

Azure Application Gateway bevat de volgende functies:

## <a name="secure-sockets-layer-ssltls-termination"></a>Beëindiging van Secure Sockets Layer (SSL/TLS)

Application Gateway ondersteunt het beëindigen van SSL/TLS op de gateway, waarna verkeer doorgaans niet-versleuteld naar de back-endservers stromen. Met deze functie voorkomt u prijzige overhead voor het versleutelen en ontsleutelen voor uw webservers. Maar soms is onversleutelde communicatie met de server echter geen aanvaardbare optie. Dit kan worden veroorzaakt door beveiligings vereisten, nalevings vereisten of door de toepassing alleen een beveiligde verbinding te accepteren. Voor deze toepassingen ondersteunt Application Gateway end-to-end SSL/TLS-versleuteling.

## <a name="autoscaling"></a>Automatisch schalen

Application Gateway-of WAF-implementaties onder Standard_v2 of WAF_v2 SKU ondersteunen automatisch schalen en kunnen omhoog of omlaag worden geschaald op basis van het wijzigen van de verkeers laad patronen. Automatisch schalen heft ook de vereiste op om tijdens het inrichten een implementatiegrootte of het aantal instanties te kiezen. Zie [v2 SKU](application-gateway-autoscaling-zone-redundant.md)voor automatisch schalen voor meer informatie over de functies Application Gateway Standard_v2 en WAF_v2.

## <a name="zone-redundancy"></a>Zone redundantie

Een Application Gateway-of WAF-implementaties onder Standard_v2 of WAF_v2 SKU kan meerdere Beschikbaarheidszones omvatten, waardoor er betere fout tolerantie wordt geboden en de nood zaak voor het inrichten van afzonderlijke toepassings gateways in elke zone wordt verwijderd.

## <a name="static-vip"></a>Statisch VIP

Het VIP van de toepassings gateway op Standard_v2 of WAF_v2 SKU ondersteunt alleen een statisch VIP-type. Dit zorgt ervoor dat het VIP dat is gekoppeld aan de toepassings gateway niet wordt gewijzigd, zelfs gedurende de levens duur van de Application Gateway.

## <a name="web-application-firewall"></a>Web Application Firewall

Web Application firewall (WAF) is een service waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen. WAF is gebaseerd op regels van de [OWASP (open Web Application Security project) kern regel sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1 (alleen WAF_v2), 3,0 en 2.2.9. 

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Veelvoorkomende aanvallen zijn hierbij onder andere aanvallen met SQL-injecties en aanvallen via scripting op meerdere sites. Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen en dit kan tevens veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webtoepassingen maakt het beveiligingsbeheer veel eenvoudiger en biedt toepassingsbeheerders meer veiligheid tegen bedreigingen of aanvallen. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassingsgateways kunnen eenvoudig worden geconverteerd naar een toepassingsgateway met Web Application Firewall.

Zie [Wat is Azure Web Application firewall?](../web-application-firewall/overview.md)voor meer informatie.

## <a name="ingress-controller-for-aks"></a>Controller van inkomend verkeer voor AKS
Met Application Gateway ingangs controller (AGIC) kunt u Application Gateway gebruiken als binnenkomend verkeer voor een [Azure Kubernetes service-cluster (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . 

De ingangs controller wordt uitgevoerd als een pod in het AKS-cluster en maakt gebruik van Kubernetes inkomende [bronnen](https://kubernetes.io/docs/concepts/services-networking/ingress/) en converteert deze naar een Application Gateway configuratie, waarmee de gateway het verkeer kan verdelen over het gehele Kubernetes. De ingangs controller ondersteunt alleen Application Gateway v2 SKU. 

Zie [Application Gateway ingress controller (AGIC) (Engelstalig)](ingress-controller-overview.md)voor meer informatie.

## <a name="url-based-routing"></a>URL-gebaseerde routering

Met op URL-pad gebaseerde routering kunt u verkeer routeren naar back-endserverpools die zijn gebaseerd op de URL-paden van de aanvraag. Een van de scenario's is het routeren van aanvragen voor verschillende inhoudstypen naar een andere pool.

Aanvragen voor `http://contoso.com/video/*` worden bijvoorbeeld doorgestuurd naar VideoServerPool en aanvragen voor `http://contoso.com/images/*` worden doorgestuurd naar ImageServerPool. Als geen van de padpatronen overeenkomen, wordt DefaultServerPool geselecteerd.

Zie [URL-based route ring met Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview)voor meer informatie.

## <a name="multiple-site-hosting"></a>Hosting van meerdere sites

Door meerdere sites te hosten, kunt u meer dan een website configureren op dezelfde instantie van de toepassingsgateway. Met deze functie kunt u een efficiëntere topologie voor uw implementaties configureren door Maxi maal 100 websites toe te voegen aan één Application Gateway, of 40 voor WAF (voor optimale prestaties). Elke website kan worden omgeleid naar een eigen pool. Application Gateway kan bijvoorbeeld verkeer regelen voor `contoso.com` en `fabrikam.com` vanaf twee servergroepen genaamd ContosoServerPool en FabrikamServerPool.

Aanvragen voor `http://contoso.com` worden gerouteerd naar ContosoServerPool en aanvragen voor `http://fabrikam.com` worden gerouteerd naar FabrikamServerPool.

Op dezelfde manier kunnen twee subdomeinen van hetzelfde bovenliggende domein worden gehost op dezelfde implementatie van een toepassingsgateway. Voorbeelden van subdomeinen die worden gehost op één toepassingsgateway-implementatie, zijn `http://blog.contoso.com` en `http://app.contoso.com`.

Zie [meerdere sites hosten met Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)voor meer informatie.

## <a name="redirection"></a>Omleiding

Een veelvoorkomend scenario voor veel webtoepassingen is de ondersteuning van automatische HTTP-naar-HTTPS-omleiding zodat alle communicatie tussen een toepassing en gebruikers plaatsvindt via een versleuteld pad.

In het verleden hebt u mogelijk technieken gebruikt, zoals het maken van een exclusieve pool waarvan het enige doel is om aanvragen om te leiden die worden ontvangen op HTTP-HTTPS. Application Gateway ondersteunt het omleiden van verkeer op de Application Gateway. Dit vereenvoudigt de configuratie van toepassingen, optimaliseert het resourcegebruik en biedt ondersteuning voor nieuwe omleidingsscenario's, waaronder de globale en op pad gebaseerde omleidingen. Application Gateway omleidings ondersteuning is niet beperkt tot HTTP-naar-HTTPS-omleiding. Dit is een algemeen omleidingsmechanisme, zodat u op basis van regels kunt omleiden van en naar elke poort die u gebruikt. Ook omleiding naar een externe site wordt ondersteund.

Ondersteuning voor Application Gateway-omleiding biedt de volgende mogelijkheden:

- Globale omleiding van de ene poort naar de andere poort op de Gateway. Hierdoor is HTTP-naar-HTTPS-omleiding op een site mogelijk.
- Padgebaseerde omleiding. Dit type omleiding maakt HTTP-naar-HTTPS-omleiding alleen mogelijk op een specifiek sitegebied, bijvoorbeeld een winkelwagengebied aangegeven door `/cart/*`.
- Omleiden naar een externe site.

Zie [verkeer omleiden](https://docs.microsoft.com/azure/application-gateway/redirect-overview) met Application Gateway voor meer informatie.

## <a name="session-affinity"></a>Sessieaffiniteit

De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op dezelfde server wilt behouden. Met behulp van de gatewaybeheerde cookies kan de Application Gateway het daarop volgende verkeer van een gebruikerssessie naar dezelfde server leiden voor verwerking. Dit is belangrijk wanneer de sessiestatus lokaal wordt opgeslagen op de server voor een gebruikerssessie.

## <a name="websocket-and-http2-traffic"></a>Websocket- en HTTP-/2-verkeer

Application Gateway biedt systeemeigen ondersteuning voor de WebSocket- en HTTP-/2-protocollen. Er is geen door de gebruiker configureerbare instelling om selectief WebSocket-ondersteuning in of uit te schakelen.

De WebSocket- en HTTP-/2-protocollen maken full-duplex-communicatie tussen een server en een client mogelijk via een langdurige TCP-verbinding. Dit maakt een meer interactieve communicatie mogelijk tussen de webserver en de client, die bidirectioneel kan zijn zonder dat hiervoor polling nodig is, zoals vereist in implementaties op basis van HTTP. Deze protocollen hebben een lage overhead, in tegens telling tot HTTP, en kunnen dezelfde TCP-verbinding opnieuw gebruiken voor meerdere aanvragen en antwoorden, wat resulteert in een efficiëntere bron gebruik. Deze protocollen zijn ontworpen om te werken via de traditionele HTTP-poorten: 80 en 443.

Zie ondersteuning voor [Websockets](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) en [http/2-ondersteuning](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)voor meer informatie.

## <a name="connection-draining"></a>Verwerkingsstop voor verbindingen

Verwerkingsstop voor verbindingen helpt u om back-endgroepsleden zonder problemen te verwijderen tijdens geplande service-updates. Deze instelling wordt ingeschakeld via de HTTP-instelling van de back-end en kan tijdens het maken van de regel worden toegepast op alle leden van een back-endgroep. Wanneer deze functie Application Gateway is ingeschakeld, worden alle exemplaren van een back-end-groep die niet meer worden geregistreerd, niet meer in een nieuwe aanvraag ontvangen en kunnen bestaande aanvragen binnen een geconfigureerde tijds limiet worden voltooid. Dit geldt voor beide back-endservers die expliciet worden verwijderd uit de back-end-pool door een API-aanroep en back-end-exemplaren die als slecht zijn gerapporteerd, zoals bepaald door de status controles.

Zie voor meer informatie de sectie verbindings afvoer van [Application Gateway configuratie-overzicht](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining).

## <a name="custom-error-pages"></a>Aangepaste foutenpagina's

Met Application Gateway kunt u aangepaste foutpagina's maken in plaats van standaardfoutpagina's weer te geven. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken.

Zie [aangepaste fouten](custom-error.md)voor meer informatie.

## <a name="rewrite-http-headers"></a>HTTP-headers opnieuw genereren

Met HTTP-headers kunnen de client en server aanvullende informatie door geven met de aanvraag of het antwoord. Het herschrijven van deze HTTP-headers helpt u bij het uitvoeren van verschillende belang rijke scenario's, zoals:

- Toevoegen van aan beveiliging gerelateerde header velden zoals HSTS/X-XSS-beveiliging.
- De velden van de antwoord header worden verwijderd die gevoelige informatie kunnen onthullen.
- Poort gegevens van X-doorgestuurd-voor kopteksten

Application Gateway ondersteunt de mogelijkheid om HTTP-aanvragen en-antwoord headers toe te voegen, te verwijderen of bij te werken, terwijl de aanvraag-en antwoord pakketten tussen de client en de back-end-pool worden verplaatst. Het biedt ook de mogelijkheid om voor waarden toe te voegen om ervoor te zorgen dat de opgegeven headers alleen worden herschreven wanneer aan bepaalde voor waarden wordt voldaan.

Zie [HTTP-headers herschrijven](rewrite-http-headers.md)voor meer informatie.

## <a name="sizing"></a>Grootte aanpassen

Application Gateway Standard_v2 en WAF_v2 SKU kunnen worden geconfigureerd voor implementaties met automatisch schalen of vaste grootte. Deze Sku's bieden geen verschillende exemplaar grootten. Zie [v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing)voor automatisch schalen voor meer informatie over de prestaties en prijzen van v2.

De Application Gateway Standard-en WAF-SKU worden momenteel aangeboden in drie grootten: **klein**, **gemiddeld**en **groot**. Kleine exemplaargrootten zijn bedoeld voor het ontwikkelen en testen van scenario's.

Zie [Servicelimieten voor Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits) voor een volledige lijst van toepassingsgateway-limieten.

De volgende tabel toont een gemiddelde prestaties doorvoer voor elke Application Gateway v1-instantie waarvoor SSL-offload is ingeschakeld:

| Gemiddelde grootte van een antwoord van de back-endpagina | Klein | Middelgroot | Groot |
| --- | --- | --- | --- |
| 6 kB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 kB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Deze waarden zijn geschatte waarden voor de doorvoer van een toepassingsgateway. De werkelijke doorvoer hangt af van verschillende details van de omgeving, zoals de gemiddelde paginagrootte, locatie van back-endexemplaren en de verwerkingstijd voor een pagina. Voor nauwkeurige prestatiecijfers moet u uw eigen tests uitvoeren. Deze waarden worden alleen geboden als richtlijn voor de capaciteitsplanning.

## <a name="next-steps"></a>Volgende stappen

Afhankelijk van uw vereisten en omgeving, kunt u een Application Gateway voor testdoeleinden maken met behulp van de Azure Portal, Azure PowerShell of Azur CLI:

- [Snelstartgids: webverkeer omleiden met Azure-toepassing gateway-Azure Portal](quick-create-portal.md)
- [Snelstart: Webverkeer omleiden met Azure Application Gateway - Azure PowerShell](quick-create-powershell.md)
- [Snelstart: Webverkeer omleiden met Azure Application Gateway - Azure CLI](quick-create-cli.md)
