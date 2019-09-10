---
title: Veelgestelde vragen over Azure-toepassing gateway
description: Vind antwoorden op veelgestelde vragen over Azure-toepassing gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: fd4eef9771ae89e330c99b398ad6d473356213f5
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858542"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Veelgestelde vragen over Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hier volgen enkele veelgestelde vragen over Azure-toepassing gateway.

## <a name="general"></a>Algemeen

### <a name="what-is-application-gateway"></a>Wat is Application Gateway?

Azure-toepassing gateway biedt een ADC (Application Delivery controller) als een service. Het biedt diverse Layer 7-functies voor taak verdeling voor uw toepassingen. Deze service is Maxi maal beschikbaar en schaalbaar en volledig beheerd door Azure.

### <a name="what-features-does-application-gateway-support"></a>Welke functies ondersteunt Application Gateway?

Application Gateway ondersteunt automatisch schalen, SSL-offloading en end-to-end SSL, een Web Application Firewall (WAF), sessie affiniteit op basis van cookies, op URL-pad gebaseerde route ring, hosting van meerdere sites en andere functies. Zie [Inleiding tot Application Gateway](application-gateway-introduction.md)voor een volledige lijst met ondersteunde functies.

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Hoe verschillen Application Gateway en Azure Load Balancer?

Application Gateway is een laag 7-load balancer, wat betekent dat het alleen werkt met webverkeer (HTTP, HTTPS, WebSocket en HTTP/2). Het ondersteunt mogelijkheden als SSL-beëindiging, sessie affiniteit op basis van cookies en round robin voor verkeer voor taak verdeling. Load Balancer load-balanceert het verkeer op laag 4 (TCP of UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Welke protocollen Application Gateway ondersteunen?

Application Gateway ondersteunt HTTP, HTTPS, HTTP/2 en WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hoe ondersteunt Application Gateway HTTP/2?

Zie [http/2-ondersteuning](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Welke resources worden ondersteund als onderdeel van een back-end-groep?

Bekijk de [ondersteunde back-end-bronnen](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>In welke regio's is Application Gateway beschikbaar?

Application Gateway is beschikbaar in alle regio's van wereld wijd Azure. Het is ook beschikbaar in [Azure China 21vianet](https://www.azure.cn/) en [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Is deze implementatie specifiek voor mijn abonnement of wordt deze gedeeld door klanten?

Application Gateway is een speciale implementatie in uw virtuele netwerk.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Ondersteunt Application Gateway HTTP-naar-HTTPS-omleiding?

Omleiding wordt ondersteund. Zie [Application Gateway omleidings overzicht](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In welke volg orde worden listeners verwerkt?

Bekijk de [volg orde van de verwerking van de listener](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Waar vind ik de Application Gateway IP en DNS?

Als u een openbaar IP-adres gebruikt als een eind punt, vindt u de IP-en DNS-informatie over de open bare IP-adres resource. U kunt deze ook vinden in de portal, op de pagina overzicht voor de toepassings gateway. Als u interne IP-adressen gebruikt, vindt u de informatie op de pagina overzicht.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Wat zijn de instellingen voor Keep-Alive time-out en TCP-time-out voor inactiviteit?

 In de SKU van Application Gateway v1 is de Keep-Alive-time-out 120 seconden. De time-out voor Keep-Alive voor de v2-SKU is 75 seconden. De time-out voor TCP-inactiviteit is een standaard waarde van 4 minuten voor het virtuele frontend-IP-adres (VIP) van Application Gateway.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Verandert de IP-of DNS-naam gedurende de levens duur van de toepassings gateway?

Het VIP kan worden gewijzigd als u de toepassings gateway stopt en start. Maar de DNS-naam die aan de toepassings gateway is gekoppeld, wordt niet gewijzigd gedurende de levens duur van de gateway. Omdat de DNS-naam niet wordt gewijzigd, moet u een CNAME-alias gebruiken en deze verwijzen naar het DNS-adres van de toepassings gateway.

### <a name="does-application-gateway-support-static-ip"></a>Ondersteunt Application Gateway statisch IP-adres?

Ja, de SKU van Application Gateway v2 ondersteunt statische open bare IP-adressen. De V1-SKU ondersteunt statische interne Ip's.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Ondersteunt Application Gateway meerdere open bare IP-adressen op de gateway?

Een toepassings gateway ondersteunt slechts één openbaar IP-adres.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hoe groot moet ik mijn subnet voor Application Gateway maken?

Zie [Application Gateway de grootte](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)van het subnet.

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kan ik meer dan één Application Gateway resource op één subnet implementeren?

Ja. Naast meerdere exemplaren van een bepaalde Application Gateway-implementatie, kunt u een andere unieke Application Gateway resource inrichten voor een bestaand subnet dat een andere Application Gateway resource bevat.

Eén subnet ondersteunt niet zowel Standard_v2 als standaard Application Gateway samen.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Ondersteunt Application Gateway x-doorgestuurd-voor kopteksten?

Ja. Zie [wijzigingen aan een aanvraag](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Hoe lang duurt het om een toepassings gateway te implementeren? Werkt mijn toepassings gateway terwijl deze wordt bijgewerkt?

Het kan Maxi maal 20 minuten duren voordat nieuwe SKU-implementaties van Application Gateway v1 beschikbaar zijn. Wijzigingen in de exemplaar grootte of het aantal zijn niet verstoord en de gateway blijft actief gedurende deze periode.

De meeste implementaties die gebruikmaken van de v2-SKU nemen ongeveer 6 minuten in beslag. Het kan echter langer duren, afhankelijk van het type implementatie. Implementaties over meerdere Beschikbaarheidszones met veel instanties kunnen bijvoorbeeld meer dan 6 minuten duren. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kan ik Exchange Server gebruiken als een back-end met Application Gateway?

Nee. Application Gateway biedt geen ondersteuning voor e-mail protocollen zoals SMTP, IMAP en POP3. 

## <a name="performance"></a>Prestaties

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hoe ondersteunt Application Gateway hoge Beschik baarheid en schaal baarheid?

De Application Gateway v1-SKU ondersteunt scenario's met hoge Beschik baarheid wanneer u twee of meer exemplaren hebt geïmplementeerd. Azure distribueert deze instanties over update-en fout domeinen om ervoor te zorgen dat de exemplaren niet allemaal op hetzelfde moment worden uitgevoerd. De V1-SKU ondersteunt schaal baarheid door meerdere exemplaren van dezelfde gateway toe te voegen om de belasting te delen.

De v2-SKU zorgt er automatisch voor dat nieuwe instanties worden verspreid over fout domeinen en update domeinen. Als u zone redundantie kiest, worden de nieuwste instanties ook verspreid over de beschik bare zones om zonegebonden-fout tolerantie aan te bieden.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hoe kan ik een DR-scenario in data centers behaalt met behulp van Application Gateway?

Gebruik Traffic Manager om verkeer te verdelen over meerdere toepassings gateways in verschillende data centers.

### <a name="does-application-gateway-support-autoscaling"></a>Ondersteunt Application Gateway automatisch schalen?

Ja, de SKU van Application Gateway v2 ondersteunt automatisch schalen. Zie voor meer informatie automatisch [schalen en zone-redundante Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Leidt hand matig omhoog of omlaag schalen tot uitval tijd?

Nee. Instanties worden gedistribueerd over upgrade domeinen en fout domeinen.

### <a name="does-application-gateway-support-connection-draining"></a>Ondersteunt Application Gateway verbindings afvoer?

Ja. U kunt de verbinding verbreken instellen om leden binnen een back-end-groep zonder onderbreking te wijzigen. Met deze installatie kunt u bestaande verbindingen naar hun vorige bestemming blijven verzenden totdat de verbinding wordt gesloten of een Configureer bare time-out verloopt. Er wordt gewacht tot er alleen huidige verbindingen in de vlucht zijn voltooid. Application Gateway is niet op de hoogte van de sessie status van de toepassing.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan ik de instantie grootte wijzigen van gemiddeld in groot, zonder onderbreking?

Ja. Azure distribueert exemplaren in update-en fout domeinen om ervoor te zorgen dat de exemplaren niet allemaal op hetzelfde moment worden uitgevoerd. Application Gateway ondersteunt schalen door meerdere exemplaren van dezelfde gateway toe te voegen om de belasting te delen.

## <a name="configuration"></a>Configuratie

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Wordt Application Gateway altijd geïmplementeerd in een virtueel netwerk?

Ja. Application Gateway wordt altijd geïmplementeerd in een subnet van een virtueel netwerk. Dit subnet kan alleen toepassings gateways bevatten. Zie [vereisten voor virtuele netwerken en subnetten](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)voor meer informatie.

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kan Application Gateway communiceren met exemplaren buiten het virtuele netwerk of buiten het bijbehorende abonnement?

Als u een IP-verbinding hebt, kan Application Gateway communiceren met exemplaren buiten het virtuele netwerk waarin het zich bevindt. Application Gateway kunt ook communiceren met exemplaren buiten het abonnement waarin deze zich bevinden. Als u interne Ip's als back-endadresgroep wilt gebruiken, gebruik dan [virtuele netwerk peering](../virtual-network/virtual-network-peering-overview.md) of [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan ik iets anders in het subnet van de toepassings Gateway implementeren?

Nee. Maar u kunt ook andere toepassings gateways implementeren in het subnet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Worden netwerk beveiligings groepen ondersteund op het subnet van de toepassings gateway?

Zie [netwerk beveiligings groepen in het Application Gateway subnet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Ondersteunt het subnet van de toepassings gateway door de gebruiker gedefinieerde routes?

Zie door [de gebruiker gedefinieerde routes die worden ondersteund in het Application Gateway subnet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Wat zijn de limieten voor Application Gateway? Kan ik deze limieten verhogen?

Zie [Application Gateway limieten](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Kan ik Application Gateway gelijktijdig gebruiken voor zowel extern als intern verkeer?

Ja. Application Gateway ondersteunt één intern IP-adres en één extern IP-adres per toepassings gateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Ondersteunt Application Gateway peering voor virtuele netwerken?

Ja. Met peering voor virtuele netwerken kunt u verkeer verdelen in andere virtuele netwerken.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Kan ik praten met on-premises servers wanneer ze zijn verbonden via ExpressRoute of VPN-tunnels?

Ja, zolang verkeer is toegestaan.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Kan één back-end-pool veel toepassingen op verschillende poorten gebruiken?

Micro service-architectuur wordt ondersteund. Als u op verschillende poorten wilt testen, moet u meerdere HTTP-instellingen configureren.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Ondersteunen aangepaste tests joker tekens of regex op antwoord gegevens?

Nee. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hoe worden routerings regels verwerkt in Application Gateway?

Zie [volg orde van verwerkings regels](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Wat is het veld host voor aangepaste tests?

In het veld host geeft u de naam op voor het verzenden van de test wanneer u meerdere locaties op Application Gateway hebt geconfigureerd. Gebruik anders ' 127.0.0.1 '. Deze waarde wijkt af van de hostnaam van de virtuele machine. De indeling is \<protocol\>://\<host\>:\<poort\>pad.\>\<

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kan ik Application Gateway toegang tot slechts enkele bron-IP-adressen toestaan?

Ja. Zie [toegang tot specifieke bron-ip's beperken](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kan ik dezelfde poort gebruiken voor zowel open bare als privé gerichte listeners?

Nee.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Zijn er richt lijnen beschikbaar om te migreren van de V1-SKU naar de v2-SKU?

Ja. Voor meer informatie raadpleegt u [Azure-toepassing gateway en Web Application firewall van v1 naar v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Configuratie-SSL

### <a name="what-certificates-does-application-gateway-support"></a>Welke certificaten ondersteunt Application Gateway?

Application Gateway ondersteunt zelfondertekende certificaten, certificaten van CERTIFICERINGs instanties, certificaten voor uitgebreide validatie (EV) en Joker certificaten.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Welke coderings suites ondersteunt Application Gateway?

Application Gateway ondersteunt de volgende coderings suites. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Zie [SSL-beleids versies en coderings suites configureren op Application Gateway](application-gateway-configure-ssl-policy-powershell.md)voor meer informatie over het aanpassen van SSL-opties.

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Ondersteunt Application Gateway het opnieuw versleutelen van verkeer naar de back-end?

Ja. Application Gateway ondersteunt SSL-offload en end-to-end SSL, waarmee het verkeer wordt opnieuw versleuteld naar de back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan ik SSL-beleid configureren om SSL-protocol versies te beheren?

Ja. U kunt Application Gateway zodanig configureren dat TLS 1.0, TLS 1.1 en TLS 1.2 worden geweigerd. SSL 2,0 en 3,0 zijn standaard al uitgeschakeld en kunnen niet worden geconfigureerd.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kan ik coderings suites en beleids volgorde configureren?

Ja. In Application Gateway kunt u [coderings suites configureren](application-gateway-ssl-policy-overview.md). Schakel ten minste één van de volgende coderings suites in om een aangepast beleid te definiëren. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway maakt gebruik van SHA256 voor back-end-beheer.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Hoeveel SSL-certificaten ondersteunt Application Gateway?

Application Gateway ondersteunt Maxi maal 100 SSL-certificaten.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hoeveel authenticatie certificaten voor back-end versleuteling Application Gateway ondersteuning?

Application Gateway ondersteunt Maxi maal 10 verificatie certificaten. De standaard waarde is 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Biedt Application Gateway systeem eigen integratie met Azure Key Vault?

Ja, de SKU van Application Gateway v2 ondersteunt Key Vault. Zie [SSL-beëindiging met Key Vault certificaten](key-vault-certs.md)voor meer informatie.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>HTTPS-listeners Hoe kan ik configureren voor. com-en .net-sites? 

Voor meerdere op een domein gebaseerde (op een host gebaseerde) route ring kunt u multi site-listeners maken, listeners instellen die HTTPS gebruiken als protocol en de listeners koppelen aan de routerings regels. Zie voor meer informatie [meerdere sites hosten met behulp van Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Kan ik speciale tekens in het wacht woord voor het pfx-bestand gebruiken?

Nee, gebruik alleen alfanumerieke tekens in het wacht woord voor het pfx-bestand.

## <a name="configuration---web-application-firewall-waf"></a>Configuratie-Web Application Firewall (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Biedt de WAF-SKU alle functies die beschikbaar zijn in de standaard-SKU?

Ja. WAF biedt ondersteuning voor alle functies in de standaard-SKU.

### <a name="which-crs-versions-does-application-gateway-support"></a>Welke CRS-versies ondersteunt Application Gateway?

Application Gateway ondersteunt CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) en CRS [3,0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>WAF Hoe kan ik controleren?

Bewaak WAF via diagnostische logboek registratie. Zie [Diagnostische logboek registratie en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md)voor meer informatie.

### <a name="does-detection-mode-block-traffic"></a>Blokkeert de detectie modus verkeer?

Nee. In de detectie modus wordt alleen verkeer geregistreerd dat een WAF-regel activeert.

### <a name="can-i-customize-waf-rules"></a>Kan ik WAF-regels aanpassen?

Ja. Zie [WAF-regel groepen en-regels aanpassen](application-gateway-customize-waf-rules-portal.md)voor meer informatie.

### <a name="what-rules-are-currently-available-for-waf"></a>Welke regels zijn momenteel beschikbaar voor WAF?

WAF biedt momenteel ondersteuning voor CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) en [3,0](application-gateway-crs-rulegroups-rules.md#owasp30). Deze regels bieden basis beveiliging tegen de meeste van de Top-10 beveiligings problemen die OWASP (Web Application Security project) identificeren: 

* Beveiliging tegen SQL-injecties
* Beveiliging voor cross-site scripting
* Bescherming tegen veelvoorkomende webaanvalen, zoals het injecteren van opdrachten, HTTP-aanvragen smuggling, het splitsen van HTTP-antwoorden en insluiting van externe bestanden
* Beveiliging tegen schendingen van het HTTP-protocol
* Beveiliging tegen afwijkingen van het HTTP-protocol, zoals een gebruikersagent voor de host en Accept-headers die ontbreken
* Beveiliging tegen bots, crawlers en scanners
* Detectie van veelvoorkomende onjuiste configuraties van toepassingen (dat wil zeggen Apache, IIS, enzovoort)

Zie [OWASP Top-10-beveiligings problemen](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)voor meer informatie.

### <a name="does-waf-support-ddos-protection"></a>Biedt WAF ondersteuning voor DDoS-beveiliging?

Ja. U kunt DDoS-beveiliging inschakelen voor het virtuele netwerk waarin de toepassings gateway is geïmplementeerd. Met deze instelling zorgt u ervoor dat de Azure DDoS Protection-Service ook de virtuele IP-adressen van de toepassings gateway (VIP) beveiligt.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Zijn er richt lijnen beschikbaar om te migreren van de V1-SKU naar de v2-SKU?

Ja. Voor meer informatie raadpleegt u [Azure-toepassing gateway en Web Application firewall van v1 naar v2](migrate-v1-v2.md).

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Welke typen logboeken bevat Application Gateway?

Application Gateway biedt drie logboeken: 

* **ApplicationGatewayAccessLog**: Het toegangs logboek bevat elke aanvraag die wordt verzonden naar het front-end van de toepassings gateway. De gegevens omvatten het IP-adres van de beller, de aangevraagde URL, reactie latentie, retour code en bytes in en uit. Het toegangs logboek wordt elke 300 seconden verzameld. Het bevat één record per toepassings gateway.
* **ApplicationGatewayPerformanceLog**: In het prestatie logboek worden prestatie gegevens voor elke toepassings gateway vastgelegd. De informatie omvat de door Voer in bytes, totaal aantal geleverde aanvragen, aantal mislukte aanvragen en een gezonde en slechte back-end van een backend-exemplaar.
* **ApplicationGatewayFirewallLog**: Voor toepassings gateways die u configureert met WAF, bevat het logboek van de firewall aanvragen die zijn geregistreerd via de detectie modus of de preventie modus.

Zie voor meer informatie [back-end status, Diagnostische logboeken en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hoe kan ik weet of de leden van de back-endadresgroep in orde zijn?

Controleer de status met behulp van `Get-AzApplicationGatewayBackendHealth` de Power shell-cmdlet of de portal. Zie [Application Gateway Diagnostics](application-gateway-diagnostics.md)(diagnostische gegevens) voor meer informatie.

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Wat is het Bewaar beleid voor de diagnostische logboeken?

Diagnostische logboeken stroomt naar het opslag account van de klant. Klanten kunnen het Bewaar beleid instellen op basis van hun voor keur. Diagnostische logboeken kunnen ook worden verzonden naar een Event Hub-of Azure Monitor-Logboeken. Zie [Application Gateway Diagnostics](application-gateway-diagnostics.md)(diagnostische gegevens) voor meer informatie.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hoe kan ik audit logboeken voor Application Gateway ophalen?

Selecteer in de portal op de menu-Blade van een toepassings gateway het **activiteiten logboek** voor toegang tot het controle logboek. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan ik waarschuwingen instellen met Application Gateway?

Ja. In Application Gateway worden waarschuwingen voor metrische gegevens geconfigureerd. Zie [Application Gateway metrische gegevens](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) en [waarschuwings meldingen ontvangen](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)voor meer informatie.

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hoe kan ik verkeers statistieken voor Application Gateway analyseren?

U kunt toegangs logboeken op verschillende manieren weer geven en analyseren. Gebruik Azure Monitor-logboeken, Excel, Power BI, enzovoort.

U kunt ook een resource manager-sjabloon gebruiken waarmee de populaire [GoAccess](https://goaccess.io/) log analyzer voor Application Gateway Access-Logboeken wordt geïnstalleerd en uitgevoerd. GoAccess biedt waardevolle statistieken voor HTTP-verkeer, zoals unieke bezoekers, aangevraagde bestanden, hosts, besturings systemen, browsers en HTTP-status codes. Voor meer informatie, in GitHub, raadpleegt u het [Leesmij-bestand in de map Resource Manager-sjabloon](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Wat kan ertoe leiden dat de status van de back-end wordt weer gegeven?

Normaal gesp roken ziet u een onbekende status wanneer de toegang tot de back-end wordt geblokkeerd door een netwerk beveiligings groep (NSG), aangepaste DNS of door de gebruiker gedefinieerde route ring (UDR) op het toepassings gateway-subnet. Zie voor meer informatie [status van back-end, diagnostische logboek registratie en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Is er een geval waarin NSG-stroom logboeken geen toegestaan verkeer weer geven?

Ja. Als uw configuratie overeenkomt met het volgende scenario, ziet u geen toegestaan verkeer in uw NSG-stroom logboeken:
- U hebt Application Gateway v2 geïmplementeerd
- U hebt een NSG op het toepassings gateway-subnet
- U hebt NSG-stroom logboeken ingeschakeld op die NSG

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure-toepassing gateway?](overview.md)voor meer informatie over Application Gateway.
