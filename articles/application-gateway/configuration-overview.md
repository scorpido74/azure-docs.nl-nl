---
title: Overzicht van configuratie van Azure Application Gateway
description: In dit artikel wordt beschreven hoe u de onderdelen van Azure Application Gateway configureert
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: absha
ms.openlocfilehash: 89d894a5125a16f95e6ef8a15c2503d48f3a8e55
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632175"
---
# <a name="application-gateway-configuration-overview"></a>Overzicht van toepassingsgatewayconfiguratie

Azure Application Gateway bestaat uit verschillende componenten die u op verschillende manieren configureren voor verschillende scenario's. In dit artikel ziet u hoe u elk onderdeel configureert.

![Stroomdiagram voor toepassingsgatewaycomponenten](./media/configuration-overview/configuration-overview1.png)

Deze afbeelding illustreert een toepassing met drie listeners. De eerste twee zijn multi-site luisteraars voor `http://acme.com/*` en `http://fabrikam.com/*`, respectievelijk. Beide luisteren op poort 80. De derde is een basislistener met end-to-end Transport Layer Security (TLS) beëindiging, voorheen bekend als Secure Sockets Layer (SSL) beëindiging.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtueel Azure-netwerk en speciaal subnet

Een toepassingsgateway is een speciale implementatie in uw virtuele netwerk. Binnen uw virtuele netwerk is een speciaal subnet vereist voor de toepassingsgateway. U meerdere exemplaren van een bepaalde implementatie van een toepassingsgateway in een subnet hebben. U ook andere toepassingsgateways implementeren in het subnet. Maar u geen andere resource implementeren in het subnet van de toepassingsgateway.

> [!NOTE]
> U Standard_v2 en Standaard Azure Application Gateway niet op hetzelfde subnet mixen.

#### <a name="size-of-the-subnet"></a>Grootte van het subnet

Application Gateway gebruikt één privé-IP-adres per exemplaar, plus een ander privé-IP-adres als een privé-front-end IP is geconfigureerd.

Azure reserveert ook vijf IP-adressen in elk subnet voor intern gebruik: de eerste vier en de laatste IP-adressen. Denk bijvoorbeeld aan 15 toepassingsgateway-exemplaren zonder privé-front-end IP. U hebt ten minste 20 IP-adressen nodig voor dit subnet: vijf voor intern gebruik en 15 voor de toepassingsgateway-exemplaren. Dus, je moet een / 27 subnet grootte of groter.

Overweeg een subnet met 27 toepassingsgateway-exemplaren en een IP-adres voor een privé-front-end IP. In dit geval hebt u 33 IP-adressen nodig: 27 voor de toepassingsgateway-exemplaren, één voor de private front-end en vijf voor intern gebruik. Dus, je moet een / 26 subnet grootte of groter.

We raden u aan een subnetgrootte van ten minste /28 te gebruiken. Deze grootte geeft je 11 bruikbare IP-adressen. Als voor uw toepassingsbelasting meer dan 10 toepassingsgateway-exemplaren nodig zijn, moet u rekening houden met een subnetgrootte van /27 of /26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Netwerkbeveiligingsgroepen op het subnet Application Gateway

Netwerkbeveiligingsgroepen (NSG's) worden ondersteund op Application Gateway. Maar er zijn enkele beperkingen:

- U moet binnenkomend internetverkeer toestaan op TCP-poorten 65503-65534 voor de Application Gateway v1 SKU en TCP-poorten 65200-65535 voor de v2 SKU met het doelsubnet als **GatewayManager-servicetag.** **Any** Dit poortbereik is vereist voor azure-infrastructuurcommunicatie. Deze poorten worden beveiligd (vergrendeld) door Azure-certificaten. Externe entiteiten, waaronder de klanten van deze gateways, kunnen niet communiceren op deze eindpunten.

- Uitgaande internetverbinding kan niet worden geblokkeerd. Standaard uitgaande regels in de NSG staan internetconnectiviteit toe. U wordt aangeraden dat u:

  - Verwijder de standaard regels voor uitgaande regels niet.
  - Maak geen andere uitgaande regels die uitgaande connectiviteit weigeren.

- Verkeer vanaf de **AzureLoadBalancer-tag** moet zijn toegestaan.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Toepassingsgatewaytoegang toestaan tot een paar bron-IP's

Gebruik voor dit scenario NSG's op het subnet Application Gateway. Zet de volgende beperkingen op het subnet in deze volgorde van prioriteit:

1. Inkomende verkeer toestaan vanuit een bron-IP- of IP-bereik met de bestemming als het volledige subnetadresbereik van de Toepassingsgateway en de doelpoort als uw binnenkomende toegangspoort, bijvoorbeeld poort 80 voor HTTP-toegang.
2. Inkomende aanvragen van bron als **GatewayManager-servicetag** en -bestemming toestaan als **Alle** en bestemmingspoorten als 65503-65534 voor de Application Gateway v1 SKU en poorten 65200-65535 voor v2 SKU voor [back-end statuscommunicatie.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) Dit poortbereik is vereist voor azure-infrastructuurcommunicatie. Deze poorten worden beveiligd (vergrendeld) door Azure-certificaten. Zonder de juiste certificaten kunnen externe entiteiten geen wijzigingen op die eindpunten initiëren.
3. Inkomende Azure Load Balancer-sondes *(AzureLoadBalancer-tag)* en binnenkomend virtueel netwerkverkeer *(VirtualNetwork-tag)* toestaan op de [netwerkbeveiligingsgroep](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blokkeer alle andere binnenkomende verkeer met behulp van een deny-all regel.
5. Laat uitgaand verkeer naar het internet toe voor alle bestemmingen.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Door de gebruiker gedefinieerde routes die worden ondersteund in Application Gateway-subnet

> [!IMPORTANT]
> Als udr's op het subnet Application Gateway worden gebruikt, wordt de status in de [back-endstatus als](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) **onbekend weergegeven.** Het kan er ook voor zorgen dat het genereren van Application Gateway-logboeken en statistieken mislukt. We raden u aan geen UDR's te gebruiken op het subnet Application Gateway, zodat u de back-endstatus, logboeken en statistieken bekijken.

- **v1**

   Voor de v1 SKU worden door gebruikers gedefinieerde routes (UDR's) ondersteund op het subnet Application Gateway, zolang ze de end-to-end request/response-communicatie niet wijzigen. U bijvoorbeeld een UDR instellen in het subnet Application Gateway om naar een firewalltoestel te wijzen voor pakketinspectie. Maar u moet ervoor zorgen dat het pakket de beoogde bestemming kan bereiken na inspectie. Als u dit niet doet, kan dit leiden tot onjuist statussonde- of verkeersroutegedrag. Dit omvat aangeleerde routes of standaard 0.0.0.0/0-routes die worden gepropageerd door Azure ExpressRoute- of VPN-gateways in het virtuele netwerk.

- **v2**

   Voor de v2 SKU zijn er ondersteunde en niet-ondersteunde scenario's:

   **v2-ondersteunde scenario's**
   > [!WARNING]
   > Een onjuiste configuratie van de routetabel kan leiden tot asymmetrische routering in Application Gateway v2. Zorg ervoor dat al het vliegtuigverkeer van beheer/controle rechtstreeks naar het internet wordt verzonden en niet via een virtueel toestel. Logging en metrics kunnen ook worden beïnvloed.


  **Scenario 1**: UDR om De verspreiding van de Border Gateway Protocol (BGP) route naar het subnet Application Gateway uit te schakelen

   Soms wordt de standaardgatewayroute (0.0.0.0/0) geadverteerd via de ExpressRoute- of VPN-gateways die zijn gekoppeld aan het virtuele netwerk van de Application Gateway. Dit breekt beheer vliegtuigverkeer, die een directe weg naar het internet vereist. In dergelijke scenario's kan een UDR worden gebruikt om de verspreiding van BGP-route uit te schakelen. 

   Ga als volgt te werk om de verspreiding van BGP-routes uit te schakelen:

   1. Maak een routetabelbron in Azure.
   2. Schakel de parameter Voor het uitzetten van de **routepropagatie van de virtuele netwerkgateway** uit. 
   3. Koppel de routetabel aan het juiste subnet. 

   Het inschakelen van de UDR voor dit scenario mag geen bestaande instellingen verbreken.

  **Scenario 2**: UDR om 0.0.0.0/0 naar het internet te leiden

   U een UDR maken om 0.0.0.0/0/0-verkeer rechtstreeks naar internet te verzenden. 

  **Scenario 3**: UDR voor Azure Kubernetes Service kubenet

  Als u kubenet gebruikt met Azure Kubernetes Service (AKS) en Application Gateway Ingress Controller (AGIC), moet u een routetabel instellen zodat verkeer dat naar de pods wordt verzonden, naar het juiste knooppunt kan worden doorgestuurd. Dit is niet nodig als u Azure CNI gebruikt. 

   Als u de routetabel wilt instellen zodat kubenet kan werken, voert u de volgende stappen uit:

  1. Maak een routetabelbron in Azure. 
  2. Ga na het maken naar de pagina **Routes.** 
  3. Een nieuwe route toevoegen:
     - Adresvoorvoegsel moet het IP-bereik zijn van de pods die u in AKS wilt bereiken. 
     - Volgende hop type moet **Virtueel toestel**. 
     - Het volgende hopadres moet het IP-adres zijn van het knooppunt dat de pods host binnen het IP-bereik dat is gedefinieerd in het veld adresvoorvoegsel. 
    
  **v2 niet-ondersteunde scenario's**

  **Scenario 1**: UDR voor virtuele apparaten

  Elk scenario waarin 0.0.0.0/0 moet worden omgeleid via een virtueel toestel, een virtueel netwerk met hub/spoke of on-premise (gedwongen tunneling) wordt niet ondersteund voor V2.

## <a name="front-end-ip"></a>Front-end IP

U de toepassingsgateway configureren om een openbaar IP-adres, een privé-IP-adres of beide te hebben. Een openbare IP is vereist wanneer u een back-end host die clients via internet via een internet-facing virtual IP (VIP) moeten openen. 

Een openbaar IP is niet vereist voor een intern eindpunt dat niet is blootgesteld aan het internet. Dat staat bekend als een *ip-eindpunt (internal load-balancer(* ILB) of een private frontend IP. Een application gateway ILB is handig voor interne line-of-business applicaties die niet worden blootgesteld aan het internet. Het is ook handig voor services en lagen in een multi-tier applicatie binnen een beveiligingsgrens die niet zijn blootgesteld aan het internet, maar die round-robin load distributie, sessie stickiness, of TLS beëindiging vereisen.

Er wordt slechts één openbaar IP-adres of één privé-IP-adres ondersteund. U kiest het front-end IP wanneer u de toepassingsgateway maakt.

- Voor een openbaar IP-adres u een nieuw openbaar IP-adres maken of een bestaand openbaar IP-adres gebruiken op dezelfde locatie als de toepassingsgateway. Zie [statisch versus dynamisch openbaar IP-adres](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)voor meer informatie .

- Voor een privé-IP u een privé-IP-adres opgeven van het subnet waar de toepassingsgateway wordt gemaakt. Als u er geen opgeeft, wordt er automatisch een willekeurig IP-adres geselecteerd uit het subnet. Het IP-adrestype dat u selecteert (statisch of dynamisch) kan later niet worden gewijzigd. Zie [Een toepassingsgateway maken met een interne load balancer](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)voor meer informatie.

Een front-end IP-adres is gekoppeld aan een *listener,* die controleert op binnenkomende aanvragen op het front-end IP.

## <a name="listeners"></a>Listeners

Een listener is een logische entiteit die controleert op binnenkomende verbindingsaanvragen met behulp van het poort-, protocol-, host- en IP-adres. Wanneer u de listener configureert, moet u waarden invoeren die overeenkomen met de overeenkomstige waarden in de binnenkomende aanvraag op de gateway.

Wanneer u een toepassingsgateway maakt met behulp van de Azure-portal, maakt u ook een standaardlistener door het protocol en de poort voor de listener te kiezen. U kiezen of u HTTP2-ondersteuning voor de listener wilt inschakelen. Nadat u de toepassingsgateway hebt gemaakt, u de instellingen van die standaardlistener bewerken *(appGatewayHttpListener)* of nieuwe listeners maken.

### <a name="listener-type"></a>Listenertype

Wanneer u een nieuwe listener maakt, kiest u tussen [ *basis-* en *multisite.*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)

- Als u wilt dat al uw aanvragen (voor elk domein) worden geaccepteerd en doorgestuurd naar backendpools, kiest u basic. Meer informatie over [het maken van een toepassingsgateway met een basislistener.](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- Als u aanvragen wilt doorsturen naar verschillende backendpools op basis van de *hostheader* of hostnaam, kiest u multi-site listener, waarbij u ook een hostnaam moet opgeven die overeenkomt met het binnenkomende verzoek. Application Gateway is namelijk afhankelijk van HTTP 1.1-hostheaders om meer dan één website op hetzelfde openbare IP-adres en dezelfde poort te hosten.

#### <a name="order-of-processing-listeners"></a>Volgorde van de verwerking van luisteraars

Voor de v1 SKU worden aanvragen afgestemd op de volgorde van de regels en het type luisteraar. Als een regel met basislistener eerst in de volgorde komt, wordt deze eerst verwerkt en accepteert deze elk verzoek voor die poort- en IP-combinatie. Om dit te voorkomen, configureert u de regels eerst met multi-site listeners en duwt u de regel met de basislistener naar de laatste in de lijst.

Voor de v2 SKU worden multi-site listeners verwerkt vóór basisluisteraars.

### <a name="front-end-ip"></a>Front-end IP

Kies het front-end IP-adres dat u wilt koppelen aan deze listener. De luisteraar luistert naar binnenkomende verzoeken op dit IP-adres.

### <a name="front-end-port"></a>Front-end poort

Kies de front-end poort. Selecteer een bestaande poort of maak een nieuwe poort. Kies een waarde uit het [toegestane bereik van poorten.](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports) U niet alleen bekende poorten gebruiken, zoals 80 en 443, maar elke toegestane aangepaste poort die geschikt is. Een poort kan worden gebruikt voor publiekgerichte luisteraars of luisteraars die privé-gericht zijn.

### <a name="protocol"></a>Protocol

Kies HTTP of HTTPS:

- Als u HTTP kiest, is het verkeer tussen de client en de toepassingsgateway onversleuteld.

- Kies HTTPS als u [TLS-beëindiging](features.md#secure-sockets-layer-ssltls-termination) of [end-to-end TLS-versleuteling](https://docs.microsoft.com/azure/application-gateway/ssl-overview)wilt. Het verkeer tussen de client en de toepassingsgateway wordt versleuteld. En de TLS-verbinding eindigt bij de toepassingsgateway. Als u end-to-end TLS-versleuteling wilt, moet u HTTPS kiezen en de **back-end HTTP-instelling** configureren. Dit zorgt ervoor dat het verkeer opnieuw wordt versleuteld wanneer het van de toepassingsgateway naar de back-end reist.


Als u TLS-beëindiging en end-to-end TLS-versleuteling wilt configureren, moet u een certificaat aan de listener toevoegen om de toepassingsgateway in staat te stellen een symmetrische sleutel af te leiden. Dit wordt gedicteerd door de TLS-protocol specificatie. De symmetrische sleutel wordt gebruikt om het verkeer dat naar de gateway wordt verzonden te versleutelen en te decoderen. Het gatewaycertificaat moet in pfx-formaat (Personal Information Exchange) zijn. Met deze indeling u de privésleutel exporteren die de gateway gebruikt om verkeer te versleutelen en te decoderen.

#### <a name="supported-certificates"></a>Ondersteunde certificaten

Bekijk [certificaten die worden ondersteund voor TLS-beëindiging](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Aanvullende protocolondersteuning

#### <a name="http2-support"></a>HTTP2-ondersteuning

HTTP/2-protocolondersteuning is beschikbaar voor clients die alleen verbinding maken met toepassingsgatewaylisteners. De communicatie naar back-endserverpools is via HTTP/1.1. Standaard is HTTP/2-ondersteuning uitgeschakeld. In het volgende Azure PowerShell-codefragment ziet u hoe u dit inschakelt:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Ondersteuning voor WebSocket

WebSocket-ondersteuning is standaard ingeschakeld. Er is geen door de gebruiker configureerbare instelling om deze in te schakelen of uit te schakelen. U WebSockets gebruiken met zowel HTTP- als HTTPS-listeners.

### <a name="custom-error-pages"></a>Aangepaste foutpagina's

U aangepaste fouten definiëren op globaal niveau of op listenerniveau. Maar het maken van aangepaste foutpagina's op mondiaal niveau vanuit de Azure-portal wordt momenteel niet ondersteund. U een aangepaste foutpagina configureren voor een firewallfout van 403 webtoepassingen of een 502-onderhoudspagina op listenerniveau. U moet ook een openbaar toegankelijke blob-URL opgeven voor de opgegeven foutstatuscode. Zie voor meer informatie [Aangepaste foutpagina's maken voor Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Foutcodes van application Gateway](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Zie [Azure PowerShell-configuratie](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)als u een algemene aangepaste foutpagina wilt configureren.

### <a name="tls-policy"></a>TLS-beleid

U tls/SSL-certificaatbeheer centraliseren en de overhead voor versleuteling-decryptie voor een back-endserverfarm verminderen. Met gecentraliseerde TLS-verwerking u ook een centraal TLS-beleid opgeven dat is afgestemd op uw beveiligingsvereisten. U *standaard,* *vooraf gedefinieerd*of *aangepast* TLS-beleid kiezen.

U configureert tls-beleid om TLS-protocolversies te beheren. U een toepassingsgateway configureren om een minimale protocolversie te gebruiken voor TLS-handshakes van TLS1.0, TLS1.1 en TLS1.2. SSL 2.0 en 3.0 zijn standaard uitgeschakeld en kunnen niet worden geconfigureerd. Zie overzicht [van het TLS-beleidsoverzicht van Application Gateway.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)

Nadat u een listener hebt gemaakt, koppelt u deze aan een regel voor het routeren van aanvragen. Die regel bepaalt hoe aanvragen die op de listener worden ontvangen, naar de back-end worden doorgestuurd.

## <a name="request-routing-rules"></a>Routeringsregels aanvragen

Wanneer u een toepassingsgateway maakt met behulp van de Azure-portal, maakt u een standaardregel *(regel1).* Deze regel bindt de standaardlistener *(appGatewayHttpListener)* met de standaardback-endpool *(appGatewayBackendPool)* en de standaard back-end HTTP-instellingen *(appGatewayBackendHttpSettings).* Nadat u de gateway hebt gemaakt, u de instellingen van de standaardregel bewerken of nieuwe regels maken.

### <a name="rule-type"></a>Regeltype

Wanneer u een regel maakt, kiest u tussen [ *basis-* en *padgebaseerde*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Kies basic als u alle aanvragen van de gekoppelde listener (bijvoorbeeld *blog<i></i>.contoso.com/)\** wilt doorsturen naar één back-endpool.
- Kies padgebaseerd als u aanvragen wilt routeren van specifieke URL-paden naar specifieke back-endgroepen. Het padpatroon wordt alleen toegepast op het pad van de URL, niet op de queryparameters.

#### <a name="order-of-processing-rules"></a>Volgorde van de verwerkingsregels

Voor de v1 SKU wordt patroonafstemming van binnenkomende aanvragen verwerkt in de volgorde waarin de paden worden weergegeven in de URL-padkaart van de op paden gebaseerde regel. Als een aanvraag overeenkomt met het patroon in twee of meer paden in de padkaart, wordt het pad dat als eerste wordt vermeld, geëvenaard. En het verzoek wordt doorgestuurd naar de back-end die is gekoppeld aan dat pad.

Voor de v2 SKU heeft een exacte overeenkomst een hogere prioriteit dan de padvolgorde in de URL-padkaart. Als een aanvraag overeenkomt met het patroon in twee of meer paden, wordt de aanvraag doorgestuurd naar de back-end die is gekoppeld aan het pad dat precies overeenkomt met de aanvraag. Als het pad in de binnenkomende aanvraag niet precies overeenkomt met een pad in de kaart, wordt patroonafstemming van de aanvraag verwerkt in de lijst met padtoewijzingslijst voor de op paden gebaseerde regel.

### <a name="associated-listener"></a>Gekoppelde listener

Koppel een listener aan de regel, zodat de *regel voor het routeren* van aanvragen die aan de listener is gekoppeld, wordt geëvalueerd om de back-endpool te bepalen om het verzoek naar te leiden.

### <a name="associated-back-end-pool"></a>Gekoppelde back-endpool

Koppel aan de regel de back-endpool die de back-enddoelen bevat die aanvragen serveren die de listener ontvangt.

 - Voor een basisregel is slechts één back-endpool toegestaan. Alle aanvragen op de gekoppelde listener worden doorgestuurd naar die back-endpool.

 - Voeg voor een op paden gebaseerde regel meerdere back-endgroepen toe die overeenkomen met elk URL-pad. De aanvragen die overeenkomen met het INGEVOERDE URL-pad worden doorgestuurd naar de bijbehorende back-endpool. Voeg ook een standaard back-endpool toe. Aanvragen die niet overeenkomen met een URL-pad in de regel, worden doorgestuurd naar die groep.

### <a name="associated-back-end-http-setting"></a>Gekoppelde back-end HTTP-instelling

Voeg voor elke regel een back-end HTTP-instelling toe. Aanvragen worden van de toepassingsgateway naar de back-enddoelen gerouteerd met behulp van het poortnummer, het protocol en andere informatie die in deze instelling is opgegeven.

Voor een basisregel is slechts één back-end HTTP-instelling toegestaan. Alle aanvragen op de gekoppelde listener worden met deze HTTP-instelling doorgestuurd naar de bijbehorende back-enddoelen.

Voeg voor een op paden gebaseerde regel meerdere BACK-end HTTP-instellingen toe die overeenkomen met elk URL-pad. Aanvragen die overeenkomen met het URL-pad in deze instelling, worden doorgestuurd naar de bijbehorende back-enddoelen met behulp van de HTTP-instellingen die overeenkomen met elk URL-pad. Voeg ook een standaard HTTP-instelling toe. Aanvragen die niet overeenkomen met een URL-pad in deze regel, worden doorgestuurd naar de standaardback-endpool met behulp van de standaard-HTTP-instelling.

### <a name="redirection-setting"></a>Omleidingsinstelling

Als omleiding is geconfigureerd voor een basisregel, worden alle aanvragen op de gekoppelde listener doorgestuurd naar het doel. Dit is *wereldwijde* omleiding. Als omleiding is geconfigureerd voor een op paden gebaseerde regel, worden alleen aanvragen in een specifiek sitegebied omgeleid. Een voorbeeld is een winkelwagentje gebied dat wordt aangeduid door */ winkelwagen /\**. Dit is *padgebaseerde* omleiding.

Zie Overzicht van omleidingen van [toepassingsgateway](redirect-overview.md)voor meer informatie over omleidingen.

#### <a name="redirection-type"></a>Omleidingstype

Kies het gewenste type omleiding: *Permanent(301),* *Tijdelijk(307),* *Gevonden(302)* of *Zie andere(303)*.

#### <a name="redirection-target"></a>Omleidingsdoel

Kies een andere listener of een externe site als omleidingsdoel.

##### <a name="listener"></a>Listener

Kies listener als het omleidingsdoel om verkeer van de ene listener naar de andere op de gateway om te leiden. Deze instelling is vereist wanneer u http-naar-HTTPS-omleiding wilt inschakelen. Hiermee wordt verkeer omgeleid van de bronlistener die controleert op binnenkomende HTTP-aanvragen naar de doellistener die controleert op binnenkomende HTTPS-aanvragen. U er ook voor kiezen om de querytekenreeks en het pad uit de oorspronkelijke aanvraag op te nemen in het verzoek dat wordt doorgestuurd naar het omleidingsdoel.

![Dialoogvenster Toepassingsgatewaycomponenten](./media/configuration-overview/configure-redirection.png)

Zie voor meer informatie over http-naar-HTTPS omleiding:
- [HTTP-naar-HTTPS-omleiding met behulp van de Azure-portal](redirect-http-to-https-portal.md)
- [HTTP-naar-HTTPS-omleiding met PowerShell](redirect-http-to-https-powershell.md)
- [HTTP-naar-HTTPS-omleiding met azure cli](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Externe site

Kies externe site wanneer u het verkeer wilt omleiden naar de listener die aan deze regel is gekoppeld, naar een externe site. U ervoor kiezen om de querytekenreeks uit de oorspronkelijke aanvraag op te nemen in de aanvraag die wordt doorgestuurd naar het omleidingsdoel. U het pad niet doorsturen naar de externe site die zich in de oorspronkelijke aanvraag bevond.

Zie voor meer informatie over omleiding:
- [Verkeer omleiden naar een externe site met PowerShell](redirect-external-site-powershell.md)
- [Verkeer omleiden naar een externe site met behulp van de CLI](redirect-external-site-cli.md)

#### <a name="rewrite-the-http-header-setting"></a>De HTTP-header-instelling opnieuw schrijven

Met deze instelling worden HTTP-aanvraag- en antwoordkoppen toegevoegd, verwijderd of bijgewerkt terwijl de aanvraag- en antwoordpakketten tussen de client- en back-endgroepen worden verplaatst. Zie voor meer informatie:

 - [Overzicht van HTTP-kopteksten herschrijven](rewrite-http-headers.md)
 - [Http-koptekst opnieuw schrijven configureren](rewrite-http-headers-portal.md)

## <a name="http-settings"></a>HTTP-instellingen

De toepassingsgateway leidt het verkeer naar de back-endservers met behulp van de configuratie die u hier opgeeft. Nadat u een HTTP-instelling hebt gemaakt, moet u deze koppelen aan een of meer regels voor het routeren van aanvragen.

### <a name="cookie-based-affinity"></a>Affiniteit op basis van cookies

Azure Application Gateway maakt gebruik van gatewaybeheerde cookies voor het onderhouden van gebruikerssessies. Wanneer een gebruiker het eerste verzoek naar Application Gateway verzendt, stelt deze een affiniteitscookie in het antwoord in met een hashwaarde die de sessiedetails bevat, zodat de volgende aanvragen met de affiniteitscookie naar dezelfde backendserver worden doorgestuurd om plakkerigheid te behouden. 

Deze functie is handig wanneer u een gebruikerssessie op dezelfde server wilt houden en wanneer de sessiestatus lokaal op de server wordt opgeslagen voor een gebruikerssessie. Als de toepassing geen cookiegebaseerde affiniteit aankan, u deze functie niet gebruiken. Om het te gebruiken, moet u ervoor zorgen dat de clients cookies ondersteunen.

De [Chromium browser](https://www.chromium.org/Home) [v80 update](https://chromiumdash.appspot.com/schedule) bracht een mandaat waar HTTP cookies zonder [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) attribuut moet worden behandeld als SameSite = Lax. In het geval van CORS-aanvragen voor (Cross-Origin Resource Sharing) moet de cookie samesite=None gebruiken als de cookie in een context van derden moet worden *verzonden; Beveiligde* kenmerken en het moet alleen via HTTPS worden verzonden. Anders verzendt de browser in een HTTP-scenario de cookies niet in de context van derden. Het doel van deze update van Chrome is om de beveiliging te verbeteren en om Aanvallen op Cross-Site Request Forgery (CSRF) te voorkomen. 

Om deze wijziging te ondersteunen, injecteert Application Gateway (alle SKU-typen) vanaf 17 februari 2020 een andere cookie genaamd *ApplicationGatewayAffinityCORS* naast de bestaande *ApplicationGatewayAffinity-cookie.* De *ApplicationGatewayAffinityCORS-cookie* heeft nog twee kenmerken toegevoegd (*"SameSite=None; Veilig"*) zodat de plaksessie zelfs voor verzoeken van cross-origine wordt gehandhaafd.

Houd er rekening mee dat de standaardnaam van de affiniteitscookie *ApplicationGatewayAffinity* is en dat u deze wijzigen. Als u een aangepaste cookienaam voor affiniteit gebruikt, wordt er een extra cookie toegevoegd met CORS als achtervoegsel. *CustomCookieNameCORS*bijvoorbeeld .

> [!NOTE]
> Als het kenmerk *SameSite=None* is ingesteld, is het verplicht dat de cookie ook de *secure-vlag* bevat en via HTTPS moet worden verzonden.  Als sessieaffiniteit vereist is via CORS, moet u uw werkbelasting migreren naar HTTPS. Raadpleeg TLS offload en End-to-End TLS-documentatie voor Application Gateway hier - [Overzicht](ssl-overview.md), [Configureer een toepassingsgateway met TLS-beëindiging met behulp van de Azure-portal](create-ssl-portal.md), [Configureer end-to-end TLS met behulp van Application Gateway met de portal](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Verwerkingsstop voor verbindingen

Door het aftappen van verbindingen u op een elegante manier back-endpoolleden verwijderen tijdens geplande service-updates. U deze instelling toepassen op alle leden van een back-endpool tijdens het maken van regels. Het zorgt ervoor dat alle uitregistratieexemplaren van een back-endpool bestaande verbindingen blijven onderhouden en lopende aanvragen voor een configureerbare time-out blijven uitvoeren en geen nieuwe aanvragen of verbindingen ontvangen. De enige uitzondering hierop zijn aanvragen die zijn gebonden aan het uitschrijven van instanties vanwege de affiniteit met gatewaybeheer en die worden doorgestuurd naar de deregistratie-exemplaren. Het aftappen van verbindingen is van toepassing op back-endexemplaren die expliciet uit de back-endpool worden verwijderd.

### <a name="protocol"></a>Protocol

Application Gateway ondersteunt zowel HTTP als HTTPS voor routeringaanvragen naar de back-endservers. Als u HTTP kiest, is het verkeer naar de back-endservers niet versleuteld. Als niet-versleutelde communicatie niet acceptabel is, kiest u HTTPS.

Deze instelling in combinatie met HTTPS in de listener ondersteunt [end-to-end TLS](ssl-overview.md). Hierdoor u gevoelige gegevens die versleuteld zijn veilig naar de back-end verzenden. Elke back-endserver in de back-endpool die end-to-end TLS heeft ingeschakeld, moet worden geconfigureerd met een certificaat om veilige communicatie mogelijk te maken.

### <a name="port"></a>Poort

Met deze instelling geeft u de poort op waar de back-endservers naar verkeer van de toepassingsgateway luisteren. U poorten configureren variërend van 1 tot 65535.

### <a name="request-timeout"></a>Time-out aanvragen

Deze instelling is het aantal seconden dat de toepassingsgateway wacht op een antwoord van de back-endserver.

### <a name="override-back-end-path"></a>Back-endpad overschrijven

Met deze instelling u een optioneel aangepast doorstuurpad configureren dat u wilt gebruiken wanneer de aanvraag wordt doorgestuurd naar de back-end. Elk deel van het binnenkomende pad dat overeenkomt met het aangepaste pad in het **backendpadveld overschrijven,** wordt gekopieerd naar het doorgestuurde pad. In de volgende tabel ziet u hoe deze functie werkt:

- Wanneer de HTTP-instelling is gekoppeld aan een basisregel voor het routeren van aanvragen:

  | Oorspronkelijke aanvraag  | Back-endpad overschrijven | Verzoek doorgestuurd naar back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- Wanneer de HTTP-instelling is gekoppeld aan een op pad gebaseerde aanvraagrouteringsregel:

  | Oorspronkelijke aanvraag           | Padregel       | Back-endpad overschrijven | Verzoek doorgestuurd naar back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

### <a name="use-for-app-service"></a>Gebruiken voor app-service

Dit is een snelkoppeling met alleen de gebruikersinterface waarmee de twee vereiste instellingen voor de back-end van Azure App Service zijn geselecteerd. Hiermee **u hostnaam kiezen van back-endadres**en wordt een nieuwe aangepaste sonde gemaakt als u er nog geen hebt. (Zie de sectie [Host-naam kiezen uit het gedeelte Back-end adres](#pick) van dit artikel voor meer informatie.) Er wordt een nieuwe sonde gemaakt en de sondekop wordt gekozen op het adres van het back-end-lid.

### <a name="use-custom-probe"></a>Aangepaste sonde gebruiken

Met deze instelling wordt een [aangepaste sonde](application-gateway-probe-overview.md#custom-health-probe) gekoppeld aan een HTTP-instelling. U slechts één aangepaste sonde koppelen aan een HTTP-instelling. Als u een aangepaste sonde niet expliciet koppelt, wordt de [standaardsonde](application-gateway-probe-overview.md#default-health-probe-settings) gebruikt om de status van de back-end te controleren. We raden u aan een aangepaste sonde te maken voor meer controle over de statusbewaking van uw back-ends.

> [!NOTE]
> De aangepaste sonde controleert de status van de back-endpool niet, tenzij de bijbehorende HTTP-instelling expliciet is gekoppeld aan een listener.

### <a name="pick-host-name-from-back-end-address"></a><a id="pick"/></a>Hostnaam kiezen op back-endadres

Met deze mogelijkheid wordt de *hostheader* in het verzoek dynamisch ingesteld op de hostnaam van de back-endpool. Het maakt gebruik van een IP-adres of FQDN.

Deze functie helpt wanneer de domeinnaam van de back-end verschilt van de DNS-naam van de toepassingsgateway en de back-end is afhankelijk van een specifieke hostheader om op te lossen tot het juiste eindpunt.

Een voorbeeldgeval is multi-tenant services als back-end. Een app-service is een multi-tenantservice die een gedeelde ruimte met één IP-adres gebruikt. Een app-service is dus alleen toegankelijk via de hostnamen die zijn geconfigureerd in de aangepaste domeininstellingen.

Standaard wordt de aangepaste domeinnaam *example.azurewebsites.net*. Als u toegang wilt krijgen tot uw app-service door een toepassingsgateway te gebruiken via een hostnaam die niet expliciet is geregistreerd in de app-service of via de FQDN van de toepassingsgateway, overschrijft u de hostnaam in het oorspronkelijke verzoek naar de hostnaam van de app-service. Schakel hiervoor de naam van de **pickhost in vanaf de instelling voor backend-adres.**

Voor een aangepast domein waarvan de bestaande aangepaste DNS-naam is toegewezen aan de app-service, hoeft u deze instelling niet in te schakelen.

> [!NOTE]
> Deze instelling is niet vereist voor de App Service-omgeving, een speciale implementatie.

### <a name="host-name-override"></a>Overschrijven hostnaam

Deze mogelijkheid vervangt de *hostheader* in de binnenkomende aanvraag op de toepassingsgateway door de hostnaam die u opgeeft.

Als *www.contoso.com* bijvoorbeeld is opgegeven in de instelling **Hostnaam,** wordt het oorspronkelijke verzoek *`https://appgw.eastus.cloudapp.azure.com/path1` gewijzigd in *`https://www.contoso.com/path1` wanneer de aanvraag wordt doorgestuurd naar de back-endserver.

## <a name="back-end-pool"></a>Back-endgroep

U een back-endpool richten op vier typen backendleden: een specifieke virtuele machine, een virtuele machineschaalset, een IP-adres/FQDN of een app-service. 

Nadat u een back-endpool hebt gemaakt, moet u deze koppelen aan een of meer regels voor het routeren van aanvragen. U moet ook statussondes configureren voor elke back-endpool op uw toepassingsgateway. Wanneer aan een voorwaarde voor de aanvraagrouterregel is voldaan, stuurt de toepassingsgateway het verkeer door naar de gezonde servers (zoals bepaald door de statussondes) in de bijbehorende back-endpool.

## <a name="health-probes"></a>Statuscontroles

Een toepassingsgateway controleert standaard de status van alle resources in de back-end. Maar we raden u ten zeerste aan een aangepaste sonde te maken voor elke back-end HTTP-instelling om meer controle te krijgen over statusbewaking. Zie Instellingen voor aangepaste [statussondes](application-gateway-probe-overview.md#custom-health-probe-settings)voor meer informatie over het configureren van een aangepaste sonde.

> [!NOTE]
> Nadat u een aangepaste statussonde hebt gemaakt, moet u deze koppelen aan een back-end HTTP-instelling. Een aangepaste sonde controleert de status van de back-endpool niet, tenzij de bijbehorende HTTP-instelling expliciet is gekoppeld aan een listener die een regel gebruikt.

## <a name="next-steps"></a>Volgende stappen

Nu u op de hoogte bent van application gateway-componenten, u het:

- [Een toepassingsgateway maken in de Azure-portal](quick-create-portal.md)
- [Een toepassingsgateway maken met PowerShell](quick-create-powershell.md)
- [Een toepassingsgateway maken met de Azure CLI](quick-create-cli.md)
