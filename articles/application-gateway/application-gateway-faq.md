---
title: Veelgestelde vragen over Azure Application Gateway
description: Antwoorden vinden op veelgestelde vragen over Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 290467d5d20a74f8b8b2c23f6da0dcadfd74cc56
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411018"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Veelgestelde vragen over Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hieronder vindt u veelgestelde vragen over Azure Application Gateway.

## <a name="general"></a>Algemeen

### <a name="what-is-application-gateway"></a>Wat is Application Gateway?

Azure Application Gateway biedt een ADC (Application Delivery Controller) als service. Het biedt verschillende layer 7 load-balancing mogelijkheden voor uw toepassingen. Deze service is zeer beschikbaar, schaalbaar en volledig beheerd door Azure.

### <a name="what-features-does-application-gateway-support"></a>Welke functies ondersteunt Application Gateway?

Application Gateway ondersteunt automatisch schalen, SSL-offloading en end-to-end SSL, een webtoepassingsfirewall (WAF), sessieaffiniteit op basis van cookies, URL-padgebaseerde routering, multisitehosting en andere functies. Zie [Inleiding tot toepassingsgateway](application-gateway-introduction.md)voor een volledige lijst met ondersteunde functies.

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Hoe verschillen Application Gateway en Azure Load Balancer?

Application Gateway is een layer 7 load balancer, wat betekent dat deze alleen werkt met webverkeer (HTTP, HTTPS, WebSocket en HTTP/2). Het ondersteunt mogelijkheden zoals SSL-beëindiging, cookie-gebaseerde sessieaffiniteit en round robin voor load-balancing verkeer. Load Balancer load-balances verkeer op laag 4 (TCP of UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Welke protocollen ondersteunt Application Gateway?

Application Gateway ondersteunt HTTP, HTTPS, HTTP/2 en WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hoe ondersteunt Application Gateway HTTP/2?

Zie [HTTP/2-ondersteuning](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Welke resources worden ondersteund als onderdeel van een backend pool?

Zie [ondersteunde backendresources](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>In welke regio's is Application Gateway beschikbaar?

Application Gateway is beschikbaar in alle regio's van wereldwijd Azure. Het is ook beschikbaar in [Azure China 21Vianet](https://www.azure.cn/) en [Azure Government.](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Is deze implementatie speciaal voor mijn abonnement, of wordt deze gedeeld door klanten?

Application Gateway is een speciale implementatie in uw virtuele netwerk.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Ondersteunt Application Gateway http-naar-HTTPS omleiding?

Omleiding wordt ondersteund. Zie [overzicht van omleidingsgateway van toepassing](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In welke volgorde worden luisteraars verwerkt?

Zie de [volgorde van de verwerking van luisteraars](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Waar vind ik het IP- en DNS van de Application Gateway?

Als u een openbaar IP-adres als eindpunt gebruikt, vindt u de IP- en DNS-informatie op de bron van het openbare IP-adres. Of vind het in de portal, op de overzichtspagina voor de applicatiegateway. Als u interne IP-adressen gebruikt, vindt u de informatie op de overzichtspagina.

Open voor de v2-SKU de openbare IP-bron en selecteer **Configuratie**. Het veld **DNS-naamlabel (optioneel)** is beschikbaar om de DNS-naam te configureren.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Wat zijn de instellingen voor Keep-Alive time-out en TCP idle time-out?

*Een time-out met Keep-Alive* bepaalt hoe lang de Toepassingsgateway wacht tot een client een andere HTTP-aanvraag op een permanente verbinding verzendt voordat deze opnieuw wordt gebruikt of wordt gesloten. *TCP idle time-out* bepaalt hoe lang een TCP-verbinding open wordt gehouden in het geval van geen activiteit. 

De *Keep-Alive time-out* in de Application Gateway v1 SKU is 120 seconden en in de v2 SKU is het 75 seconden. De *TCP idle time-out* is een standaard van 4 minuten op de frontend virtual IP (VIP) van zowel v1 als v2 SKU of Application Gateway. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Verandert de IP- of DNS-naam gedurende de levensduur van de toepassingsgateway?

In Application Gateway V1 SKU kan de VIP wijzigen als u stopt en de toepassingsgateway start. Maar de DNS-naam die is gekoppeld aan de toepassingsgateway verandert niet gedurende de levensduur van de gateway. Omdat de DNS-naam niet verandert, moet u een CNAME-alias gebruiken en deze naar het DNS-adres van de toepassingsgateway aanwijzen. In Application Gateway V2 SKU u het IP-adres als statisch instellen, zodat de IP- en DNS-naam niet verandert gedurende de levensduur van de toepassingsgateway. 

### <a name="does-application-gateway-support-static-ip"></a>Ondersteunt Application Gateway statisch IP?

Ja, de Application Gateway v2 SKU ondersteunt statische openbare IP-adressen. De v1 SKU ondersteunt statische interne IP's.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Ondersteunt Application Gateway meerdere openbare IP's op de gateway?

Een toepassingsgateway ondersteunt slechts één openbaar IP-adres.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hoe groot moet ik mijn subnet voor Application Gateway maken?

Zie [overwegingen subnetgrootte van application gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kan ik meer dan één Application Gateway-bron implementeren in één subnet?

Ja. Naast meerdere exemplaren van een bepaalde Application Gateway-implementatie, u een andere unieke Application Gateway-bron inrichten op een bestaand subnet dat een andere Application Gateway-bron bevat.

Eén subnet kan zowel Standard_v2 als Standard Application Gateway niet samen ondersteunen.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Ondersteunt Application Gateway v2 door gebruikers gedefinieerde routes (UDR)?

Ja, maar alleen specifieke scenario's. Zie [Configuratieoverzicht Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)voor meer informatie.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Ondersteunt Application Gateway x-forwarded-for headers?

Ja. Zie [Wijzigingen in een aanvraag](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Hoe lang duurt het om een toepassingsgateway te implementeren? Werkt mijn toepassingsgateway terwijl deze wordt bijgewerkt?

Nieuwe Application Gateway v1 SKU-implementaties kunnen tot 20 minuten duren voordat de inlevering is ingesteld. Wijzigingen in de grootte of het aantal voorbeelden zijn niet storend en de gateway blijft gedurende deze periode actief.

De meeste implementaties die de v2 SKU gebruiken, duren ongeveer 6 minuten om in te richten. Het kan echter langer duren, afhankelijk van het type implementatie. Implementaties in meerdere beschikbaarheidszones met veel instanties kunnen bijvoorbeeld meer dan 6 minuten duren. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kan ik Exchange Server gebruiken als back-end met Application Gateway?

Nee. Application Gateway biedt geen ondersteuning voor e-mailprotocollen zoals SMTP, IMAP en POP3. 

## <a name="performance"></a>Prestaties

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hoe ondersteunt Application Gateway hoge beschikbaarheid en schaalbaarheid?

De Application Gateway v1 SKU ondersteunt scenario's met hoge beschikbaarheid wanneer u twee of meer exemplaren hebt geïmplementeerd. Azure distribueert deze exemplaren over update- en foutdomeinen om ervoor te zorgen dat exemplaren niet allemaal tegelijk mislukken. De v1 SKU ondersteunt schaalbaarheid door meerdere exemplaren van dezelfde gateway toe te voegen om de belasting te delen.

De v2 SKU zorgt er automatisch voor dat nieuwe exemplaren worden verspreid over foutdomeinen en updatedomeinen. Als u zoneredundantie kiest, zijn de nieuwste exemplaren ook verspreid over beschikbaarheidszones om zonale fouttolerantie te bieden.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hoe bereik ik een DR-scenario in datacenters met behulp van Application Gateway?

Gebruik Traffic Manager om verkeer te distribueren over meerdere toepassingsgateways in verschillende datacenters.

### <a name="does-application-gateway-support-autoscaling"></a>Ondersteunt Application Gateway automatisch schalen?

Ja, de Application Gateway v2 SKU ondersteunt automatisch schalen. Zie [Automatisch schalen en ZoneredundantE Application Gateway](application-gateway-autoscaling-zone-redundant.md)voor meer informatie.

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Veroorzaakt handmatige of automatische opschalen of opschalen downtime?

Nee. Exemplaren worden verdeeld over upgradedomeinen en foutdomeinen.

### <a name="does-application-gateway-support-connection-draining"></a>Ondersteunt Application Gateway het aftappen van verbindingen?

Ja. U het aftappen van verbindingen instellen om zonder onderbreking van lid te veranderen in een backendpool. Zie sectie [verbinding aftappen van Application Gateway](features.md#connection-draining)voor meer informatie.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan ik de grootte van de instantie veranderen van gemiddeld naar groot zonder onderbreking?

Ja.

## <a name="configuration"></a>Configuratie

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Wordt Application Gateway altijd geïmplementeerd in een virtueel netwerk?

Ja. Application Gateway wordt altijd geïmplementeerd in een virtueel netwerksubnet. Dit subnet kan alleen toepassingsgateways bevatten. Zie voor meer informatie [de vereisten voor virtuele netwerken en subnets](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kan Application Gateway communiceren met instanties buiten het virtuele netwerk of buiten het abonnement?

Zolang u IP-connectiviteit hebt, kan Application Gateway communiceren met instanties buiten het virtuele netwerk waarin het zich bevindt. Application Gateway kan ook communiceren met instanties buiten het abonnement waarin het zich bevindt. Als u van plan bent interne IP's te gebruiken als backendpoolleden, gebruikt u [virtuele netwerkpeering](../virtual-network/virtual-network-peering-overview.md) of [Azure VPN Gateway.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan ik iets anders implementeren in het subnet van de toepassingsgateway?

Nee. Maar u andere toepassingsgateways implementeren in het subnet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Worden netwerkbeveiligingsgroepen ondersteund op het subnet van de toepassingsgateway?

Zie [Netwerkbeveiligingsgroepen in het subnet Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Ondersteunt het subnet van de toepassingsgateway door de gebruiker gedefinieerde routes?

Zie [door de gebruiker gedefinieerde routes die worden ondersteund in het subnet Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Wat zijn de limieten voor Application Gateway? Kan ik deze limieten verhogen?

Zie [Toepassingsgatewaylimieten](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Kan ik tegelijkertijd Application Gateway gebruiken voor zowel extern als intern verkeer?

Ja. Application Gateway ondersteunt één intern IP-adres en één extern IP per toepassingsgateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Ondersteunt Application Gateway virtueel netwerkpeeren?

Ja. Virtuele netwerkpeering helpt load-balance verkeer in andere virtuele netwerken.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Kan ik met on-premises servers praten wanneer ze verbonden zijn via ExpressRoute of VPN-tunnels?

Ja, zolang het verkeer is toegestaan.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Kan één backendpool veel toepassingen op verschillende poorten bedienen?

Microservice architectuur wordt ondersteund. Als u op verschillende poorten wilt sonde, moet u meerdere HTTP-instellingen configureren.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Ondersteunen aangepaste sondes wildcards of regex op responsgegevens?

Nee. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hoe worden routeringsregels verwerkt in Application Gateway?

Zie [Regels voor orde van verwerking](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Wat betekent het veld Host voor aangepaste sondes?

In het veld Host wordt de naam opgegeven waarnaar de sonde moet worden verzonden wanneer u multisite op Application Gateway hebt geconfigureerd. Gebruik anders '127.0.0.1'. Deze waarde verschilt van de naam van de virtuele machinehost. De indeling \<\>is\<\>protocol\<\>\<://\>host : poortpad .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kan ik Application Gateway toegang geven tot slechts een paar bron-IP-adressen?

Ja. Zie [de toegang tot specifieke bron-IP's beperken](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kan ik dezelfde poort gebruiken voor zowel publiekgerichte als privé-gerichte luisteraars?

Nee.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Is er begeleiding beschikbaar om te migreren van de v1 SKU naar de v2 SKU?

Ja. Voor meer informatie [zie, Migreren Azure Application Gateway en Web Application Firewall van v1 naar v2](migrate-v1-v2.md).

### <a name="does-application-gateway-support-ipv6"></a>Ondersteunt Application Gateway IPv6?

Application Gateway v2 ondersteunt momenteel geen IPv6. Het kan werken in een dual stack VNet met alleen IPv4, maar de gateway subnet moet IPv4-only. Application Gateway v1 ondersteunt geen dual stack VNets. 

## <a name="configuration---ssl"></a>Configuratie - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Welke certificaten ondersteunt Application Gateway?

Application Gateway ondersteunt zelfondertekende certificaten, CA-certificaten (certificate authority), Extended Validation (EV) en wildcardcertificaten.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Welke cipher suites ondersteunt Application Gateway?

Application Gateway ondersteunt de volgende cipher suites. 

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

Zie [SSL-beleidsversies en cijfersuites configureren op Application Gateway](application-gateway-configure-ssl-policy-powershell.md)voor informatie over het aanpassen van SSL-opties.

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Ondersteunt Application Gateway herversleuteling van verkeer naar de backend?

Ja. Application Gateway ondersteunt SSL-offload en end-to-end SSL, die verkeer opnieuw versleutelen naar de backend.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan ik SSL-beleid configureren om SSL-protocolversies te beheren?

Ja. U Application Gateway configureren om TLS1.0, TLS1.1 en TLS1.2 te weigeren. SSL 2.0 en 3.0 zijn standaard al uitgeschakeld en kunnen niet worden geconfigureerd.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kan ik cijfersuites en beleidsvolgorde configureren?

Ja. In Application Gateway u [cijfersuites configureren.](application-gateway-ssl-policy-overview.md) Als u een aangepast beleid wilt definiëren, schakelt u ten minste één van de volgende versleutelingssuites in. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway maakt gebruik van SHA256 voor backendbeheer.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Hoeveel SSL-certificaten ondersteunt Application Gateway?

Application Gateway ondersteunt maximaal 100 SSL-certificaten.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hoeveel verificatiecertificaten voor backendrecodering ondersteunt Application Gateway?

Application Gateway ondersteunt maximaal 100 verificatiecertificaten.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Integreert Application Gateway native met Azure Key Vault?

Ja, de Application Gateway v2 SKU ondersteunt Key Vault. Zie [SSL-beëindiging met Key Vault-certificaten voor](key-vault-certs.md)meer informatie.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Hoe configureer ik HTTPS-listeners voor .com- en .net-sites? 

Voor meerdere domeingebaseerde (hostgebaseerde) routering u multisite-listeners maken, listeners instellen die HTTPS als protocol gebruiken en de listeners koppelen aan de routeringsregels. Zie [Meerdere sites hosten met behulp van Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)voor meer informatie.

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Kan ik speciale tekens gebruiken in mijn .pfx-bestandswachtwoord?

Nee, gebruik alleen alfanumerieke tekens in het wachtwoord van het PFX-bestand.

## <a name="configuration---web-application-firewall-waf"></a>Configuratie - firewall voor webtoepassingen (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Biedt de WAF SKU alle functies die beschikbaar zijn in de Standaard SKU?

Ja. WAF ondersteunt alle functies in de Standaard SKU.

### <a name="how-do-i-monitor-waf"></a>Hoe houd ik WAF in de gaten?

Monitor WAF via diagnostische logging. Zie [Diagnostische logboekregistratie en metrische gegevens voor Application Gateway voor](application-gateway-diagnostics.md)meer informatie.

### <a name="does-detection-mode-block-traffic"></a>Blokkeert de detectiemodus het verkeer?

Nee. De detectiemodus registreert alleen verkeer dat een WAF-regel activeert.

### <a name="can-i-customize-waf-rules"></a>Kan ik WAF-regels aanpassen?

Ja. Zie [WAF-regelgroepen en -regels aanpassen voor](application-gateway-customize-waf-rules-portal.md)meer informatie .

### <a name="what-rules-are-currently-available-for-waf"></a>Welke regels zijn momenteel beschikbaar voor WAF?

WAF ondersteunt momenteel CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)en [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Deze regels bieden basislijnbeveiliging tegen de meeste van de top-10 kwetsbaarheden die Open Web Application Security Project (OWASP) identificeert: 

* Beveiliging tegen SQL-injecties
* Beveiliging van scripts op verschillende plaatsen
* Bescherming tegen veelvoorkomende webaanvallen zoals commando-injectie, HTTP-verzoeksmokkel, HTTP-responssplitsing en aanval op het gebied van bestandsopname op afstand
* Beveiliging tegen schendingen van het HTTP-protocol
* Beveiliging tegen afwijkingen van het HTTP-protocol, zoals een gebruikersagent voor de host en Accept-headers die ontbreken
* Beveiliging tegen bots, crawlers en scanners
* Detectie van veelvoorkomende verkeerde configuraties van toepassingen (dat wil zeggen Apache, IIS, enzovoort)

Zie [OWASP top-10 kwetsbaarheden](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)voor meer informatie.

### <a name="does-waf-support-ddos-protection"></a>Ondersteunt WAF DDoS-bescherming?

Ja. U DDoS-beveiliging inschakelen op het virtuele netwerk waar de toepassingsgateway wordt geïmplementeerd. Deze instelling zorgt ervoor dat de Azure DDoS Protection-service ook het virtuele IP (VIRTUAL IP) (Application Gateway) beschermt.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Is er begeleiding beschikbaar om te migreren van de v1 SKU naar de v2 SKU?

Ja. Voor meer informatie [zie, Migreren Azure Application Gateway en Web Application Firewall van v1 naar v2](migrate-v1-v2.md).

## <a name="configuration---ingress-controller-for-aks"></a>Configuratie - invallende controller voor AKS

### <a name="what-is-an-ingress-controller"></a>Wat is een Ingress Controller?

Kubernetes maakt `deployment` het `service` mogelijk om een groep pods intern in het cluster te maken en te gebruiken. Om dezelfde service extern bloot [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) te stellen, wordt een resource gedefinieerd die load balancing, SSL-beëindiging en op naam gebaseerde virtuele hosting biedt.
Om aan `Ingress` deze resource te voldoen, is een Ingress-controller vereist die luistert naar eventuele wijzigingen in `Ingress` resources en het beleid voor de load balancer configureert.

Met de Application Gateway Ingress Controller kan [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) worden gebruikt als de ingress voor een Azure Kubernetes [Service](https://azure.microsoft.com/services/kubernetes-service/) die ook wel een AKS-cluster wordt genoemd.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Kan een enkele instantie voor invallende controller meerdere Application Gateways beheren?

Momenteel kan één exemplaar van Ingress Controller alleen aan één Application Gateway worden gekoppeld.

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Welke typen logboeken biedt Application Gateway?

Application Gateway biedt drie logboeken: 

* **ApplicationGatewayAccessLog**: Het toegangslogboek bevat elk verzoek dat wordt ingediend bij de frontend van de toepassingsgateway. De gegevens omvatten het IP-adres van de beller, de gevraagde URL, de reactielatentie, de retourcode en de bytes in en uit. Het bevat één record per toepassingsgateway.
* **ApplicationGatewayPerformanceLog:** Het prestatielogboek legt prestatiegegevens vast voor elke toepassingsgateway. Informatie omvat de doorvoer in bytes, totale aanvragen die worden weergegeven, het aantal mislukte aanvragen en het aantal gezonde en ongezonde backend-instantie.
* **ApplicationGatewayFirewallLog:** Voor toepassingsgateways die u configureert met WAF, bevat het firewalllogboek aanvragen die worden geregistreerd via de detectiemodus of preventiemodus.

Alle logs worden elke 60 seconden verzameld. Zie [Backend-status, diagnostische logboeken en statistieken voor Application Gateway voor](application-gateway-diagnostics.md)meer informatie.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hoe weet ik of mijn backend poolleden gezond zijn?

Controleer de status met behulp `Get-AzApplicationGatewayBackendHealth` van de PowerShell-cmdlet of de portal. Zie [Application Gateway diagnostics](application-gateway-diagnostics.md)voor meer informatie.

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Wat is het bewaarbeleid voor de diagnostische logboeken?

Diagnostische logboeken stromen naar het opslagaccount van de klant. Klanten kunnen het bewaarbeleid instellen op basis van hun voorkeur. Diagnostische logboeken kunnen ook worden verzonden naar een gebeurtenishub of Azure Monitor-logboeken. Zie [Application Gateway diagnostics](application-gateway-diagnostics.md)voor meer informatie.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hoe krijg ik controlelogboeken voor Application Gateway?

Selecteer **activiteitslogboek** in de portal in het menublad van een toepassingsgateway om toegang te krijgen tot het controlelogboek. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan ik waarschuwingen instellen met Application Gateway?

Ja. In Application Gateway worden waarschuwingen geconfigureerd op basis van metrische gegevens. Zie [Application Gateway-statistieken](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) en [Waarschuwingsmeldingen ontvangen voor](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)meer informatie.

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hoe analyseer ik verkeersstatistieken voor Application Gateway?

U toegangslogboeken op verschillende manieren bekijken en analyseren. Gebruik Azure Monitor-logboeken, Excel, Power BI, enzovoort.

U ook een Resource Manager-sjabloon gebruiken die de populaire [GoAccess-loganalyzer](https://goaccess.io/) voor toepassingsgatewaytoegangslogboeken installeert en uitvoert. GoAccess biedt waardevolle HTTP-verkeersstatistieken, zoals unieke bezoekers, opgevraagde bestanden, hosts, besturingssystemen, browsers en HTTP-statuscodes. Zie het [bestand Readme in de sjabloonmap Resourcebeheer](https://aka.ms/appgwgoaccessreadme)voor meer informatie in GitHub.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Wat kan leiden tot backend gezondheid om een onbekende status terug te keren?

Meestal ziet u een onbekende status wanneer de toegang tot de backend wordt geblokkeerd door een netwerkbeveiligingsgroep (NSG), aangepaste DNS of door de gebruiker gedefinieerde routering (UDR) op het subnet van de toepassingsgateway. Zie [Backend-status, diagnostische logboekregistratie en metrische gegevens voor Application Gateway voor](application-gateway-diagnostics.md)meer informatie.

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Is er een geval waarin NSG stroom logs zal niet laten zien toegestaan verkeer?

Ja. Als uw configuratie overeenkomt met het volgende scenario, ziet u geen toegestaan verkeer in uw NSG-stroomlogboeken:
- U hebt Application Gateway v2 geïmplementeerd
- U hebt een NSG op het subnet van de toepassingsgateway
- U hebt NSG-stroomlogboeken ingeschakeld op die NSG

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Hoe gebruik ik Application Gateway V2 met alleen een privé ip-adres aan de voorkant?

Application Gateway V2 ondersteunt momenteel niet alleen de privé-IP-modus. Het ondersteunt de volgende combinaties
* Private IP en public IP
* Alleen openbaar IP

Maar als u Application Gateway V2 wilt gebruiken met alleen privé-IP, u het onderstaande proces volgen:
1. Een application gateway maken met zowel openbaar als privé IP-adres voor frontend
2. Maak geen listeners voor het openbare IP-adres aan de voorkant. Application Gateway luistert niet naar verkeer op het openbare IP-adres als er geen listeners voor worden gemaakt.
3. Maak en voeg een [netwerkbeveiligingsgroep](https://docs.microsoft.com/azure/virtual-network/security-overview) voor het subnet Application Gateway toe met de volgende configuratie in de volgorde van prioriteit:
    
    a. Verkeer vanuit Source toestaan als **GatewayManager-servicetag** en -bestemming als **doel- en** bestemmingspoort als **65200-65535**. Dit poortbereik is vereist voor azure-infrastructuurcommunicatie. Deze poorten zijn beveiligd (vergrendeld) door certificaatverificatie. Externe entiteiten, waaronder de gatewaygebruikersbeheerders, kunnen geen wijzigingen op die eindpunten initiëren zonder de juiste certificaten
    
    b. Verkeer van Bron toestaan als **AzureLoadBalancer-servicetag** en **-bestemmingspoort** als alle
    
    c. Ontken al het binnenkomende verkeer van Bron als **internetservicetag** en **bestemmingspoort**als Elke . Geef deze regel de *minste prioriteit* in de binnenkomende regels
    
    d. Houd de standaardregels zoals het toestaan van VirtualNetwork inbound zodat de toegang op privé-IP-adres niet wordt geblokkeerd
    
    e. Uitgaande internetverbinding kan niet worden geblokkeerd. Anders zult u problemen ondervinden met logboekregistratie, statistieken, enzovoort.

Voorbeeld van NSG-configuratie voor ![alleen privé-IP-toegang: Application Gateway V2 NSG-configuratie voor alleen privé-IP-toegang](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Ondersteunt Application Gateway-affiniteitscookie het kenmerk SameSite?
Ja, de [Chromium browser](https://www.chromium.org/Home) [v80 update](https://chromiumdash.appspot.com/schedule) introduceerde een mandaat op HTTP-cookies zonder SameSite attribuut te worden behandeld als SameSite = Lax. Dit betekent dat de affiniteitscookie van Application Gateway niet door de browser wordt verzonden in een context van derden. Om dit scenario te ondersteunen, injecteert Application Gateway een andere cookie genaamd *ApplicationGatewayAffinityCORS* naast de bestaande *ApplicationGatewayAffinity-cookie.*  Deze cookies zijn vergelijkbaar, maar de *ApplicationGatewayAffinityCORS-cookie* heeft nog twee kenmerken toegevoegd: *SameSite=None; Veilig.* Deze kenmerken behouden plakkerige sessies, zelfs voor cross-origine aanvragen. Zie de [cookie gebaseerde affiniteit sectie](configuration-overview.md#cookie-based-affinity) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie Wat is Azure [Application Gateway voor](overview.md)meer informatie over Application Gateway?
