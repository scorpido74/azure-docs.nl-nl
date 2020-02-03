---
title: Overzicht van Azure-toepassing gateway configuratie
description: In dit artikel wordt beschreven hoe u de onderdelen van Azure-toepassing gateway configureert
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: absha
ms.openlocfilehash: 146dbdbf2f4e107e81515ce83188fa48c52aef36
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714855"
---
# <a name="application-gateway-configuration-overview"></a>Overzicht van Application Gateway configuratie

Azure-toepassing gateway bestaat uit verschillende onderdelen die u op verschillende manieren kunt configureren voor verschillende scenario's. In dit artikel wordt beschreven hoe u elk onderdeel kunt configureren.

![Stroom diagram van Application Gateway onderdelen](./media/configuration-overview/configuration-overview1.png)

Deze afbeelding illustreert een toepassing met drie listeners. De eerste twee zijn multi-site listeners voor `http://acme.com/*` en `http://fabrikam.com/*`. Luister beide op poort 80. De derde is een basis-listener met end-to-end Secure Sockets Layer (SSL) beëindiging.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtueel Azure-netwerk en toegewezen subnet

Een toepassings gateway is een speciale implementatie in uw virtuele netwerk. In het virtuele netwerk is een toegewezen subnet vereist voor de toepassings gateway. U kunt meerdere exemplaren van een bepaalde toepassings gateway-implementatie in een subnet hebben. U kunt ook andere toepassings gateways implementeren in het subnet. Maar u kunt geen andere resources implementeren in het subnet van de toepassings gateway.

> [!NOTE]
> U kunt Standard_v2 en de standaard Azure-toepassing gateway niet op hetzelfde subnet combi neren.

#### <a name="size-of-the-subnet"></a>Grootte van het subnet

Application Gateway verbruikt 1 privé-IP-adres per exemplaar, plus een ander privé-IP-adres als er een privé-front-end-IP is geconfigureerd.

Azure reserveert ook vijf IP-adressen in elk subnet voor intern gebruik: de eerste 4 en het laatste IP-adres. Denk bijvoorbeeld aan 15 Application Gateway-instanties zonder persoonlijk front-end IP-adres. U hebt ten minste 20 IP-adressen voor dit subnet nodig: 5 voor intern gebruik en 15 voor de Application Gateway-exemplaren. U hebt dus een/27-subnet groter of groter.

Overweeg een subnet met 27 Application Gateway-instanties en een IP-adres voor een privé-front-end-IP. In dit geval hebt u 33 IP-adressen nodig: 27 voor de Application Gateway-instanties, 1 voor de privé-front-end en 5 voor intern gebruik. U hebt dus een/26-subnet groter of groter.

U wordt aangeraden een subnet-grootte van Mini maal/28 te gebruiken. Met deze grootte beschikt u over elf bruikbare IP-adressen. Als het laden van de toepassing meer dan tien Application Gateway exemplaren vereist, moet u rekening houden met de grootte van een/27-of/26-subnet.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Netwerk beveiligings groepen op het Application Gateway subnet

Netwerk beveiligings groepen (Nsg's) worden ondersteund op Application Gateway. Er zijn echter enkele beperkingen:

- U moet binnenkomend Internet verkeer toestaan op TCP-poorten 65503-65534 voor de Application Gateway v1-SKU en TCP-poorten 65200-65535 voor de v2-SKU met het doel-subnet als **enige** en bron als **GatewayManager** -service label. Dit poort bereik is vereist voor de communicatie van Azure-infra structuur. Deze poorten worden beveiligd (vergrendeld) door Azure-certificaten. Externe entiteiten, met inbegrip van de klanten van deze gateways, kunnen niet communiceren op deze eind punten.

- De uitgaande Internet verbinding kan niet worden geblokkeerd. Standaard regels voor uitgaande verbindingen in de NSG staan Internet connectiviteit toe. U wordt aangeraden dat u:

  - Verwijder de standaard regels voor uitgaande verbindingen niet.
  - Maak geen andere uitgaande regels waarmee uitgaande verbindingen worden geweigerd.

- Verkeer van het label **AzureLoadBalancer** moet zijn toegestaan.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Application Gateway toegang tot een aantal bron-Ip's toestaan

Voor dit scenario gebruikt u Nsg's in het subnet Application Gateway. Plaats de volgende beperkingen op het subnet in deze volg orde van prioriteit:

1. Sta binnenkomend verkeer van een bron-IP of IP-bereik met de bestemming als het gehele adres bereik van Application Gateway subnet en de doel poort toe als uw binnenkomende toegangs poort, bijvoorbeeld poort 80 voor HTTP-toegang.
2. Sta binnenkomende aanvragen van de bron als **GatewayManager** -service label en-bestemming toe als **wille keurige** en doel poorten als 65503-65534 voor de Application Gateway v1-SKU en poorten 65200-65535 voor v2-SKU voor de [status communicatie van back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Dit poort bereik is vereist voor de communicatie van Azure-infra structuur. Deze poorten worden beveiligd (vergrendeld) door Azure-certificaten. Zonder de juiste certificaten kunnen externe entiteiten geen wijzigingen op deze eind punten initiëren.
3. Sta binnenkomende Azure Load Balancer tests (*AzureLoadBalancer* tag) en binnenkomend virtueel netwerk verkeer (*VirtualNetwork* -tag) toe aan de [netwerk beveiligings groep](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Alle andere binnenkomende verkeer blok keren met behulp van de regel deny-all.
5. Uitgaand verkeer naar Internet toestaan voor alle bestemmingen.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Door de gebruiker gedefinieerde routes die worden ondersteund op het Application Gateway subnet

Voor de V1-SKU worden door de gebruiker gedefinieerde routes (Udr's) ondersteund op het subnet van de Application Gateway, zolang ze de end-to-end-communicatie van aanvragen en antwoorden niet wijzigen. U kunt bijvoorbeeld een UDR in het subnet van Application Gateway instellen om te verwijzen naar een firewall apparaat voor pakket inspectie. Maar u moet ervoor zorgen dat het pakket na de inspectie de beoogde bestemming kan bereiken. Als u dit niet doet, kan dit leiden tot een onjuiste werking van de status test of het routeren van verkeer. Hiertoe behoren geleerde routes of standaard 0.0.0.0/0-routes die worden door gegeven door Azure ExpressRoute of VPN-gateways in het virtuele netwerk.

Voor de v2-SKU worden Udr's niet ondersteund op het Application Gateway subnet. Zie [Azure-toepassing gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku)voor meer informatie.

> [!NOTE]
> Udr's worden op dit moment niet ondersteund voor de v2-SKU.

> [!NOTE]
> Het gebruik van Udr's op het Application Gateway subnet kan ertoe leiden dat de status in de [weer gave status van de back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) wordt weer gegeven als ' onbekend '. Het kan ook leiden tot het genereren van Application Gateway logboeken en de metrische gegevens. U wordt aangeraden Udr's niet te gebruiken op het Application Gateway subnet, zodat u de status, logboeken en metrische gegevens van de back-end kunt bekijken.

## <a name="front-end-ip"></a>Front-end-IP

U kunt de toepassings gateway configureren voor een openbaar IP-adres, een privé IP-adres of beide. Een openbaar IP-adres is vereist wanneer u een back-end host dat clients via internet toegang moeten hebben via een virtueel IP-adres (VIP) op internet. 

Een openbaar IP-adres is niet vereist voor een intern eind punt dat niet beschikbaar is op internet. Dit wordt ook wel een intern ILB-eind punt ( *Load-Balancer* ) of een persoonlijk frontend-IP-adres genoemd. Een Application Gateway ILB is handig voor interne line-of-business-toepassingen die niet worden blootgesteld aan Internet. Het is ook nuttig voor services en lagen in een toepassing met meerdere lagen binnen een beveiligings grens die niet beschikbaar is op internet, maar waarvoor Round Robin-taak verdeling, sessie persistentie of SSL-beëindiging vereist is.

Er wordt slechts één openbaar IP-adres of één privé-IP-adres ondersteund. U kiest het front-end-IP-adres wanneer u de toepassings gateway maakt.

- Voor een openbaar IP kunt u een nieuw openbaar IP-adres maken of een bestaande open bare IP gebruiken op dezelfde locatie als de toepassings gateway. Zie [static versus Dynamic Public IP Address](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)(Engelstalig) voor meer informatie.

- Voor een privé-IP kunt u een privé IP-adres opgeven in het subnet waarin de toepassings gateway is gemaakt. Als u er geen opgeeft, wordt er automatisch een wille keurig IP-adres geselecteerd in het subnet. Het IP-adres type dat u selecteert (statisch of dynamisch) kan niet later worden gewijzigd. Zie [een toepassings gateway maken met een interne Load Balancer](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)voor meer informatie.

Een front-end-IP-adres is gekoppeld aan een *listener*, waarmee wordt gecontroleerd op binnenkomende aanvragen op de front-end-IP.

## <a name="listeners"></a>Listeners

Een listener is een logische entiteit die controleert op binnenkomende verbindings aanvragen met behulp van de poort, het Protocol, de host en het IP-adres. Wanneer u de listener configureert, moet u waarden opgeven voor deze die overeenkomen met de overeenkomende waarden in de inkomende aanvraag op de gateway.

Wanneer u een toepassings gateway maakt met behulp van de Azure Portal, maakt u ook een standaard-listener door het protocol en de poort voor de listener te kiezen. U kunt kiezen of u ondersteuning voor HTTP2 wilt inschakelen voor de listener. Nadat u de toepassings gateway hebt gemaakt, kunt u de instellingen van de standaard-listener (*appGatewayHttpListener*) bewerken of nieuwe listeners maken.

### <a name="listener-type"></a>Type listener

Wanneer u een nieuwe listener maakt, kiest u tussen een [ *basis* en een *multi-site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Als u wilt dat al uw aanvragen (voor elk domein) worden geaccepteerd en doorgestuurd naar back-endservers, kiest u basis. Meer informatie [over het maken van een toepassings gateway met een Basic-listener](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Als u aanvragen wilt door sturen naar verschillende back-Pools op basis van de *host* -header of hostnaam, kiest u multi-site listener, waarbij u ook een hostnaam moet opgeven die overeenkomt met de inkomende aanvraag. Dit komt omdat Application Gateway afhankelijk zijn van HTTP 1,1-hostheaders om meer dan één website op hetzelfde open bare IP-adres en dezelfde poort te hosten.

#### <a name="order-of-processing-listeners"></a>Volg orde van de verwerkings listeners

Voor de V1-SKU worden aanvragen vergeleken volgens de volg orde van de regels en het type listener. Als een regel met Basic listener het eerst in de aangegeven volg orde komt, wordt deze eerst verwerkt en wordt elke aanvraag voor die poort en IP-combi natie geaccepteerd. Om dit te voor komen, configureert u de regels met multi-site listeners eerst en pusht u de regel met de Basic-listener naar de laatste in de lijst.

Voor de v2-SKU worden multi-site-listeners verwerkt vóór basis-listeners.

### <a name="front-end-ip"></a>Front-end-IP

Kies het front-end-IP-adres dat u wilt koppelen aan deze listener. De listener luistert naar binnenkomende aanvragen op dit IP-adres.

### <a name="front-end-port"></a>Front-end-poort

Kies de front-end poort. Selecteer een bestaande poort of maak een nieuwe. Kies een wille keurige waarde in het [toegestane bereik van poorten](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). U kunt niet alleen bekende poorten gebruiken, zoals 80 en 443, maar elke toegestane aangepaste poort die geschikt is. Een poort kan worden gebruikt voor open bare listeners of op privé gerichte listeners.

### <a name="protocol"></a>Protocol

Kies HTTP of HTTPS:

- Als u HTTP kiest, wordt het verkeer tussen de client en de toepassings gateway niet versleuteld.

- Kies HTTPS als u [SSL-beëindiging](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) of [end-to-end SSL-versleuteling](https://docs.microsoft.com/azure/application-gateway/ssl-overview)wilt. Het verkeer tussen de client en de toepassings gateway is versleuteld. En de SSL-verbinding wordt beëindigd op de Application Gateway. Als u end-to-end SSL-versleuteling wilt, moet u HTTPS kiezen en de **http-instelling back-end** configureren. Dit zorgt ervoor dat verkeer wordt opnieuw versleuteld wanneer het van de toepassings gateway naar de back-end wordt verplaatst.

Als u SSL-beëindiging en end-to-end SSL-versleuteling wilt configureren, moet u een certificaat toevoegen aan de listener om de toepassings gateway in te scha kelen voor het afleiden van een symmetrische sleutel. Dit wordt bepaald door de SSL-protocol specificatie. De symmetrische sleutel wordt gebruikt voor het versleutelen en ontsleutelen van het verkeer dat naar de gateway wordt verzonden. Het gateway certificaat moet de PFX-indeling (Personal Information Exchange) hebben. Met deze indeling kunt u de persoonlijke sleutel exporteren die door de gateway wordt gebruikt voor het versleutelen en ontsleutelen van verkeer.

#### <a name="supported-certificates"></a>Ondersteunde certificaten

Zie de certificaten die worden [ondersteund voor SSL-beëindiging](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Aanvullende protocol ondersteuning

#### <a name="http2-support"></a>HTTP2-ondersteuning

Ondersteuning voor HTTP/2-protocollen is beschikbaar voor clients die alleen verbinding maken met de Application Gateway-listeners. De communicatie met back-end-server groepen is via HTTP/1.1. HTTP/2-ondersteuning is standaard uitgeschakeld. In het volgende code fragment van Azure PowerShell ziet u hoe u dit kunt inschakelen:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Ondersteuning voor WebSocket

Ondersteuning voor websockets is standaard ingeschakeld. Er is geen door de gebruiker Configureer bare instelling om deze in of uit te scha kelen. U kunt websockets gebruiken met HTTP-en HTTPS-listeners.

### <a name="custom-error-pages"></a>Aangepaste foutpagina's

U kunt een aangepaste fout op globaal niveau of op het niveau van de listener definiëren. Het maken van aangepaste fout pagina's op globaal niveau vanuit het Azure Portal wordt momenteel niet ondersteund. U kunt een aangepaste fout pagina configureren voor een 403-Web Application Firewall fout of een 502-onderhouds pagina op het niveau van de listener. U moet ook een openbaar toegankelijke BLOB-URL opgeven voor de gegeven fout status code. Zie voor meer informatie [Aangepaste foutpagina's maken voor Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Application Gateway fout codes](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Als u een algemene aangepaste fout pagina wilt configureren, raadpleegt u [Azure PowerShell-configuratie](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>SSL-beleid

U kunt het SSL-certificaat beheer centraliseren en de overhead voor het ontsleutelen van versleuteling voor een back-endserver verlagen. Met gecentraliseerde SSL-verwerking kunt u ook een centraal SSL-beleid opgeven dat geschikt is voor uw beveiligings vereisten. U kunt *standaard*, *vooraf gedefinieerd*of *aangepast* SSL-beleid kiezen.

U configureert SSL-beleid om de SSL-protocol versies te beheren. U kunt een toepassings gateway configureren voor het gebruik van een minimale Protocol versie voor TLS-Handshakes van TLS 1.0, TLS 1.1 en TLS 1.2. SSL 2,0 en 3,0 zijn standaard uitgeschakeld en kunnen niet worden geconfigureerd. Zie [Application Gateway overzicht van SSL-beleid](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)voor meer informatie.

Nadat u een listener hebt gemaakt, koppelt u deze aan een regel voor het door sturen van aanvragen. Deze regel bepaalt hoe aanvragen die worden ontvangen op de listener worden doorgestuurd naar de back-end.

## <a name="request-routing-rules"></a>Routerings regels voor aanvragen

Wanneer u een toepassings gateway maakt met behulp van de Azure Portal, maakt u een standaard regel (*firewallregel1*). Deze regel koppelt de standaard-listener (*appGatewayHttpListener*) aan de standaard back-end-pool (*appGatewayBackendPool*) en de standaard back-end-http-instellingen (*appGatewayBackendHttpSettings*). Nadat u de gateway hebt gemaakt, kunt u de instellingen van de standaard regel bewerken of nieuwe regels maken.

### <a name="rule-type"></a>Regel type

Wanneer u een regel maakt, kiest u tussen [ *basis* en *op basis van het pad*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Kies basis als u alle aanvragen voor de gekoppelde listener (bijvoorbeeld *blog<i></i>. contoso.com/\*)* wilt door sturen naar één back-end-groep.
- Kies op basis van pad als u aanvragen van specifieke URL-paden naar specifieke back-endservers wilt routeren. Het pad patroon wordt alleen toegepast op het pad van de URL, niet op de query parameters.

#### <a name="order-of-processing-rules"></a>Volg orde van de verwerkings regels

Voor de V1-SKU wordt het vergelijken van het patroon van binnenkomende aanvragen verwerkt in de volg orde waarin de paden worden weer gegeven in de URL-pad toewijzing van de op pad gebaseerde regel. Als een aanvraag overeenkomt met het patroon in twee of meer paden in de pad-map, wordt het pad dat als eerste wordt weer gegeven. En de aanvraag wordt doorgestuurd naar de back-end die is gekoppeld aan het pad.

Voor de v2-SKU is een exacte overeenkomst een hogere prioriteit dan de volg orde van paden in de URL-pad toewijzing. Als een aanvraag overeenkomt met het patroon in twee of meer paden, wordt de aanvraag doorgestuurd naar de back-end die is gekoppeld aan het pad dat precies overeenkomt met de aanvraag. Als het pad in de binnenkomende aanvraag niet exact overeenkomt met een pad in de kaart, wordt het vergelijken van het patroon van de aanvraag verwerkt in de volg orde van de pad-map voor de regel op basis van het pad.

### <a name="associated-listener"></a>Gekoppelde listener

Koppel een listener aan de regel zodat de regel voor het door sturen van de *aanvraag* die is gekoppeld aan de listener, wordt geëvalueerd om de back-end-pool te bepalen voor het routeren van de aanvraag.

### <a name="associated-back-end-pool"></a>Gekoppelde back-end-pool

Koppel aan de regel de back-end-groep die de back-end-doelen bevat die aanvragen bedienen die de listener ontvangt.

 - Voor een eenvoudige regel is slechts één back-end-pool toegestaan. Alle aanvragen voor de bijbehorende listener worden doorgestuurd naar die back-end-pool.

 - Voor een regel op basis van een pad voegt u meerdere back-end-Pools toe die overeenkomen met elk URL-pad. De aanvragen die overeenkomen met het opgegeven URL-pad worden doorgestuurd naar de bijbehorende back-end-groep. Voeg ook een standaard back-end-pool toe. Aanvragen die niet overeenkomen met een URL-pad in de regel, worden doorgestuurd naar die groep.

### <a name="associated-back-end-http-setting"></a>Gekoppelde back-end-HTTP-instelling

Voeg een back-end-HTTP-instelling voor elke regel toe. Aanvragen worden gerouteerd van de toepassings gateway naar de back-end-doelen met behulp van het poort nummer, het protocol en andere informatie die is opgegeven in deze instelling.

Voor een eenvoudige regel is slechts één back-end HTTP-instelling toegestaan. Alle aanvragen voor de bijbehorende listener worden doorgestuurd naar de bijbehorende back-end-doelen met behulp van deze HTTP-instelling.

Voor een regel op basis van een pad voegt u meerdere back-end-HTTP-instellingen toe die overeenkomen met elk URL-pad. Aanvragen die overeenkomen met het URL-pad in deze instelling worden doorgestuurd naar de bijbehorende back-end-doelen met behulp van de HTTP-instellingen die overeenkomen met elk URL-pad. Voeg ook een standaard HTTP-instelling toe. Aanvragen die niet overeenkomen met een URL-pad in deze regel, worden doorgestuurd naar de standaard back-end-groep met behulp van de standaard-HTTP-instelling.

### <a name="redirection-setting"></a>Omleidings instelling

Als omleiding is geconfigureerd voor een basis regel, worden alle aanvragen voor de gekoppelde listener omgeleid naar het doel. Dit is *wereld wijde* omleiding. Als omleiding is geconfigureerd voor een op een pad gebaseerde regel, worden alleen aanvragen in een specifiek site gebied omgeleid. Een voor beeld is een boodschappen mand gebied dat wordt aangeduid met */cart/\** . Dit is een omleiding *op basis van pad* .

Zie [Application Gateway omleidings overzicht](https://docs.microsoft.com/azure/application-gateway/redirect-overview)voor meer informatie over omleidingen.

#### <a name="redirection-type"></a>Type omleiding

Kies het type omleiding vereist: *permanent (301)* , *tijdelijk (307*), *gevonden (302)* of *Zie other (303)* .

#### <a name="redirection-target"></a>Doel van omleiding

Kies een andere listener of een externe site als het doel voor omleiding.

##### <a name="listener"></a>Listener

Kies listener als omleidings doel om verkeer van de ene listener naar de andere op de gateway om te leiden. Deze instelling is vereist wanneer u HTTP-naar-HTTPS-omleiding wilt inschakelen. Het omgeleide verkeer van de bron-listener die controleert op binnenkomende HTTP-aanvragen bij de doel-listener die controleert op binnenkomende HTTPS-aanvragen. U kunt er ook voor kiezen om de query teken reeks en het pad op te halen uit de oorspronkelijke aanvraag in de aanvraag die wordt doorgestuurd naar het omleidings doel.

![Het dialoog venster Application Gateway onderdelen](./media/configuration-overview/configure-redirection.png)

Zie voor meer informatie over HTTP-naar-HTTPS-omleiding:
- [HTTP-naar-HTTPS-omleiding met behulp van de Azure Portal](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [HTTP-naar-HTTPS-omleiding met behulp van Power shell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [HTTP-naar-HTTPS-omleiding via de Azure CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Externe site

Kies externe site wanneer u het verkeer wilt omleiden van de listener die is gekoppeld aan deze regel naar een externe site. U kunt ervoor kiezen om de query reeks uit de oorspronkelijke aanvraag op te halen in de aanvraag die wordt doorgestuurd naar het omleidings doel. U kunt het pad naar de externe site die zich in de oorspronkelijke aanvraag bevonden, niet door sturen.

Zie voor meer informatie over omleiding:
- [Verkeer omleiden naar een externe site met behulp van Power shell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Verkeer omleiden naar een externe site door gebruik te maken van de CLI](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>De HTTP-header-instelling opnieuw schrijven

Met deze instelling worden HTTP-aanvragen en-antwoord headers toegevoegd, verwijderd of bijgewerkt, terwijl de aanvraag-en antwoord pakketten tussen de client en de back-end-pool worden verplaatst. Ga voor meer informatie naar:

 - [Overzicht van HTTP-headers opnieuw schrijven](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Herschrijven van HTTP-header configureren](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)

## <a name="http-settings"></a>HTTP-instellingen

De Application Gateway routeert het verkeer naar de back-endservers door gebruik te maken van de configuratie die u hier opgeeft. Nadat u een HTTP-instelling hebt gemaakt, moet u deze koppelen aan een of meer aanvraag-routerings regels.

### <a name="cookie-based-affinity"></a>Affiniteit op basis van cookies

Deze functie is handig als u een gebruikers sessie op dezelfde server wilt blijven gebruiken. Met door gateways beheerde cookies kan de Application Gateway volgend verkeer van een gebruikers sessie naar dezelfde server sturen voor verwerking. Dit is belang rijk wanneer de sessie status lokaal op de server wordt opgeslagen voor een gebruikers sessie. Als de toepassing geen affiniteit op basis van cookies kan verwerken, kunt u deze functie niet gebruiken. Als u deze wilt gebruiken, moet u ervoor zorgen dat de clients cookies ondersteunen.

### <a name="connection-draining"></a>Verwerkingsstop voor verbindingen

Met de verbinding verbreken kunt u de leden van de back-end groep tijdens geplande service-updates zonder problemen verwijderen. U kunt deze instelling Toep assen op alle leden van een back-end-groep tijdens het maken van de regel. Zo zorgt u ervoor dat alle ongedaan maken van de registratie van exemplaren van een back-end-groep de bestaande verbindingen blijven behouden en dat er doorlopende aanvragen voor een Configureer bare time-out worden verzonden en geen nieuwe aanvragen of verbindingen worden ontvangen. De enige uitzonde ring hierop is het aantal aanvragen dat is gebonden voor het deregistreren van instanties vanwege gateway beheer sessie-affiniteit en zal worden doorgestuurd naar de instanties van de registratie. Het afbreken van de verbinding is van toepassing op back-end-exemplaren die expliciet uit de back-end-pool worden verwijderd.

### <a name="protocol"></a>Protocol

Application Gateway ondersteunt HTTP en HTTPS voor het routeren van aanvragen naar de back-endservers. Als u HTTP kiest, wordt het verkeer naar de back-endservers niet versleuteld. Als niet-versleutelde communicatie niet acceptabel is, kiest u HTTPS.

Deze instelling in combi natie met HTTPS in de listener ondersteunt [end-to-end SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Zo kunt u veilig gevoelige gegevens die zijn versleuteld verzenden naar de back-end. Elke back-endserver in de back-end-groep waarvoor end-to-end SSL is ingeschakeld, moet worden geconfigureerd met een certificaat om beveiligde communicatie toe te staan.

### <a name="port"></a>Poort

Met deze instelling geeft u de poort op waarop de back-endservers worden geluisterd naar verkeer van de toepassings gateway. U kunt poorten configureren variërend van 1 tot en met 65535.

### <a name="request-timeout"></a>Time-out van aanvraag

Deze instelling is het aantal seconden dat de toepassings gateway wacht op het ontvangen van een reactie van de back-endserver.

### <a name="override-back-end-path"></a>Pad van back-end overschrijven

Met deze instelling kunt u een optioneel aangepast doorstuur traject configureren dat moet worden gebruikt wanneer de aanvraag wordt doorgestuurd naar de back-end. Elk deel van het binnenkomende pad dat overeenkomt met het aangepaste pad in het veld **onderdrukking van back-end pad** , wordt gekopieerd naar het doorgestuurde pad. De volgende tabel laat zien hoe deze functie werkt:

- Wanneer de HTTP-instelling is gekoppeld aan een basis regel voor het routeren van aanvragen:

  | Oorspronkelijke aanvraag  | Pad van back-end overschrijven | Aanvraag doorgestuurd naar back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | mijn            | overschrijven            | /override/home/              |
  | /home/secondhome/ | overschrijven            | /override/home/secondhome/   |

- Wanneer de HTTP-instelling is gekoppeld aan een op een pad gebaseerde aanvraag routerings regel:

  | Oorspronkelijke aanvraag           | Padregel       | Pad van back-end overschrijven | Aanvraag doorgestuurd naar back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | overschrijven            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | overschrijven            | /override/home/secondhome/   |
  | mijn                     | pathrule      | overschrijven            | /override/home/              |
  | /home/secondhome/          | pathrule      | overschrijven            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | overschrijven            | overschrijven                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | overschrijven            | /override/secondhome/        |
  | pathrule                 | pathrule      | overschrijven            | overschrijven                   |

### <a name="use-for-app-service"></a>Gebruiken voor app service

Dit is een snelkoppeling naar een gebruikers interface die de twee vereiste instellingen voor de Azure App Service back-end selecteert. Hiermee kunt u de **hostnaam van het back-end-adres kiezen**en wordt er een nieuwe aangepaste test gemaakt als u er nog geen hebt. (Zie de sectie [hostnaam selecteren uit het back-end-adres in](#pick) dit artikel voor meer informatie.) Er wordt een nieuwe test gemaakt en de test header wordt opgehaald uit het adres van het back-end-lid.

### <a name="use-custom-probe"></a>Aangepaste test gebruiken

Met deze instelling wordt een [aangepaste test](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) gekoppeld aan een http-instelling. U kunt slechts één aangepaste test koppelen met een HTTP-instelling. Als u een aangepaste test niet expliciet koppelt, wordt de [standaard test](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) gebruikt voor het controleren van de status van de back-end. We raden u aan om een aangepaste test te maken voor meer controle over de status controle van uw back-ends.

> [!NOTE]
> De aangepaste test controleert de status van de back-end-groep niet, tenzij de bijbehorende HTTP-instelling expliciet is gekoppeld aan een listener.

### <a id="pick"/></a>Kies een hostnaam uit het back-end-adres

Met deze mogelijkheid wordt de *host* -header in de aanvraag dynamisch ingesteld op de hostnaam van de back-end-pool. Er wordt gebruikgemaakt van een IP-adres of een FQDN-naam.

Deze functie helpt wanneer de domein naam van de back-end verschilt van de DNS-naam van de toepassings gateway en de back-end is afhankelijk van een specifieke host-header om naar het juiste eind punt te leiden.

Een voor beeld is het gebruik van multi tenant Services als de back-end. Een app service is een multi tenant service die gebruikmaakt van een gedeelde ruimte met één IP-adres. Dit betekent dat een app service alleen toegankelijk is via de hostnamen die zijn geconfigureerd in de instellingen van het aangepaste domein.

De aangepaste domein naam is standaard *example.azurewebsites.net*. Als u toegang wilt krijgen tot uw app service met behulp van een toepassings gateway via een hostnaam die niet expliciet is geregistreerd in de app service of via de FQDN van de toepassings gateway, overschrijft u de hostnaam in de oorspronkelijke aanvraag naar de hostnaam van de app-service. Als u dit wilt doen, schakelt u de optie **hostnaam van back-end-adres kiezen in** .

U hoeft deze instelling niet in te scha kelen voor een aangepast domein waarvan de bestaande aangepaste DNS-naam wordt toegewezen aan de app service.

> [!NOTE]
> Deze instelling is niet vereist voor App Service Environment, een specifieke implementatie.

### <a name="host-name-override"></a>Hostnaam negeren

Deze functie vervangt de *host* -header in de inkomende aanvraag op de toepassings gateway met de hostnaam die u opgeeft.

Als bijvoorbeeld *www.contoso.com* is opgegeven in de instelling voor de **hostnaam** , wordt de oorspronkelijke aanvraag * https://appgw.eastus.cloudapp.azure.com/path1 gewijzigd in * https://www.contoso.com/path1 wanneer de aanvraag wordt doorgestuurd naar de back-endserver.

## <a name="back-end-pool"></a>Back-endgroep

U kunt een back-end-pool naar vier typen back-endservers verwijzen: een specifieke virtuele machine, een virtuele-machine schaalset, een IP-adres/FQDN of een app service. Elke back-end-pool kan verwijzen naar meerdere leden van hetzelfde type. Het is niet toegestaan om leden van verschillende typen in dezelfde back-end-groep aan te wijzen.

Nadat u een back-end-pool hebt gemaakt, moet u deze koppelen aan een of meer aanvraag-routerings regels. U moet ook status controles configureren voor elke back-end-pool op uw toepassings gateway. Wanneer aan een regel voorwaarde voor het door sturen van de aanvraag wordt voldaan, stuurt de toepassings gateway het verkeer naar de gezonde servers (zoals bepaald door de status tests) in de bijbehorende back-end-pool.

## <a name="health-probes"></a>Statuscontroles

Een toepassings gateway controleert standaard de status van alle resources in de back-end. We raden u echter ten zeerste aan een aangepaste test te maken voor elke back-end-HTTP-instelling om meer controle te krijgen over de status controle. Zie [aangepaste Health probe-instellingen](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)voor meer informatie over het configureren van een aangepaste test.

> [!NOTE]
> Nadat u een aangepaste status test hebt gemaakt, moet u deze koppelen aan een back-end-HTTP-instelling. Een aangepaste test controleert de status van de back-end-pool alleen als de bijbehorende HTTP-instelling expliciet is gekoppeld aan een listener met behulp van een regel.

## <a name="next-steps"></a>Volgende stappen

Nu u weet over Application Gateway-onderdelen, kunt u het volgende doen:

- [Een toepassings gateway maken in de Azure Portal](quick-create-portal.md)
- [Een toepassings gateway maken met behulp van Power shell](quick-create-powershell.md)
- [Een toepassings gateway maken met behulp van Azure CLI](quick-create-cli.md)
