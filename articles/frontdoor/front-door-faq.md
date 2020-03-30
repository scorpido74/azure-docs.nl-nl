---
title: Azure Front Door - Veelgestelde vragen
description: Deze pagina biedt antwoorden op veelgestelde vragen over Azure Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 1cfee9749bf2eb30799efb05ac875843bcde6651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372620"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Veelgestelde vragen voor Azure Front Door

In dit artikel worden veelgestelde vragen beantwoord over azure front door-functies en -functionaliteit. Als u het antwoord op uw vraag niet ziet, u contact met ons opnemen via de volgende kanalen (in escalerende volgorde):

1. De reacties sectie van dit artikel.
2. [Azure Front Door UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft-ondersteuning:** Als u een nieuw ondersteuningsverzoek wilt maken, selecteert u in de Azure-portal op het tabblad **Help** de knop **Help + ondersteuning** en selecteert u Vervolgens Nieuw **ondersteuningsverzoek**.

## <a name="general"></a>Algemeen

### <a name="what-is-azure-front-door"></a>Wat is Azure Front Door?

Azure Front Door is een Application Delivery Network (ADN) als service, die verschillende layer 7 load-balancing mogelijkheden biedt voor uw toepassingen. Het biedt dynamische site versnelling (DSA) samen met de wereldwijde load balancing met bijna real-time failover. Het is een zeer beschikbare en schaalbare service, die volledig wordt beheerd door Azure.

### <a name="what-features-does-azure-front-door-support"></a>Welke functies ondersteunt Azure Front Door?

Azure Front Door ondersteunt dynamic site acceleration (DSA), SSL offloading and end to end SSL, Web Application Firewall, cookie-based session affinity, url path-based routing, free certificates and multiple domain management, and others. Zie Overzicht van Azure Front [Door](front-door-overview.md)voor een volledige lijst met ondersteunde functies.

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Wat is het verschil tussen Azure Front Door en Azure Application Gateway?

Hoewel zowel Front Door als Application Gateway laag 7 (HTTP/HTTPS) load balancers zijn, is het belangrijkste verschil dat Front Door een wereldwijde service is, terwijl Application Gateway een regionale service is. Terwijl Front Door de balans kan laden tussen uw verschillende schaaleenheden/clusters/stempeleenheden in verschillende regio's, u met Application Gateway de balans tussen uw VM's/containers etc. laden die zich binnen de schaaleenheid bevinden.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Wanneer moeten we een Application Gateway achter De Voordeur implementeren?

De belangrijkste scenario's waarom men Application Gateway achter Front Door zou moeten gebruiken zijn:

- Front Door kan alleen op pad gebaseerde load balancing uitvoeren op mondiaal niveau, maar als men het balansverkeer nog verder wil laden binnen hun virtuele netwerk (VNET), dan moeten ze Application Gateway gebruiken.
- Aangezien Voordeur niet werkt op VM/containerniveau, kan het niet doen Connection Draining. Met Application Gateway u echter Connection Draining doen. 
- Met een Application Gateway achter AFD kan men 100% SSL offload bereiken en alleen HTTP-aanvragen routeren binnen hun virtuele netwerk (VNET).
- Front Door en Application Gateway ondersteunen beide sessieaffiniteit. Terwijl Front Door het volgende verkeer van een gebruikerssessie naar hetzelfde cluster of backend in een bepaald gebied kan leiden, kan Application Gateway het verkeer naar dezelfde server binnen het cluster leiden.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kunnen we Azure Load Balancer achter voordeur implementeren?

Azure Front Door heeft een openbare VIP of een openbaar beschikbare DNS-naam nodig om het verkeer naar te leiden. Het implementeren van een Azure Load Balancer achter Front Door is een veelvoorkomende use case.

### <a name="what-protocols-does-azure-front-door-support"></a>Welke protocollen ondersteunt Azure Front Door?

Azure Front Door ondersteunt HTTP, HTTPS en HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Hoe ondersteunt Azure Front Door HTTP/2?

Http/2-protocolondersteuning is alleen beschikbaar voor clients die verbinding maken met Azure Front Door. De communicatie naar backends in de backend pool is meer dan HTTP/1.1. HTTP/2-ondersteuning is standaard ingeschakeld.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Welke middelen worden vandaag ondersteund als onderdeel van backend pool?

Backend-groepen kunnen bestaan uit opslag, web-app, Kubernetes-exemplaren of andere aangepaste hostname met openbare connectiviteit. Azure Front Door vereist dat de backends worden gedefinieerd via een openbaar IP of een openbaar oplosbare DNS-hostnaam. Leden van backendpools kunnen zich in verschillende zones, regio's of zelfs buiten Azure bevinden, zolang ze openbare connectiviteit hebben.

### <a name="what-regions-is-the-service-available-in"></a>In welke regio's is de service beschikbaar?

Azure Front Door is een wereldwijde service en is niet gebonden aan een specifieke Azure-regio. De enige locatie die u moet opgeven tijdens het maken van een voordeur is de locatie van de brongroep, die in principe aangeeft waar de metagegevens voor de resourcegroep worden opgeslagen. Front Door-bron zelf wordt gemaakt als een globale bron en de configuratie wordt wereldwijd geïmplementeerd in alle POP's (Point of Presence). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Wat zijn de POP-locaties voor Azure Front Door?

Azure Front Door heeft dezelfde lijst met POP-locaties (Point of Presence) als Azure CDN van Microsoft. Raadpleeg [Azure CDN POP-locaties van Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)voor de volledige lijst met onze POP's.

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Is Azure Front Door een speciale implementatie voor mijn toepassing of wordt deze gedeeld door klanten?

Azure Front Door is een wereldwijd gedistribueerde multi-tenant service. De infrastructuur voor Front Door wordt dus gedeeld door al haar klanten. Door echter een frontdoorprofiel aan te maken, definieert u de specifieke configuratie die nodig is voor uw toepassing en hebben geen wijzigingen in uw frontdeur invloed op andere frontdoorconfiguraties.

### <a name="is-http-https-redirection-supported"></a>Wordt HTTP->HTTPS-omleiding ondersteund?

Ja. Azure Front Door ondersteunt zelfs de omleiding van host-, pad- en querytekenreeksen en een deel van de omleiding van url's. Meer informatie over [url-omleiding](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>In welke volgorde worden routeringsregels verwerkt?

Routes voor uw voordeur worden niet besteld en een specifieke route wordt geselecteerd op basis van de beste match. Meer informatie over [hoe Front Door aanvragen koppelt aan een routeringsregel.](front-door-route-matching.md)

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hoe vergrendel ik de toegang tot mijn backend tot alleen Azure Front Door?

Als u uw toepassing wilt vergrendelen om alleen verkeer van uw specifieke voordeur te accepteren, moet u IP-ACL's instellen voor uw backend en vervolgens het verkeer op uw backend beperken tot de specifieke waarde van de koptekst 'X-Azure-FDID' die door Voordeur wordt verzonden. Deze stappen worden hieronder beschreven:

- Configureer IP ACLing voor uw backends om verkeer te accepteren van de backend IP-adresruimte van Azure Front Door en alleen de infrastructuurservices van Azure. Raadpleeg de IP-details hieronder voor ACLing uw backend:
 
    - Raadpleeg de sectie *AzureFrontDoor.Backend* in [Azure IP Ranges and Service Tags](https://www.microsoft.com/download/details.aspx?id=56519) for Front Door's IPv4 backend IP address range or you can also use the service tag *AzureFrontDoor.Backend* in your [network security groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) or with Azure [Firewall](https://docs.microsoft.com/azure/firewall/service-tags).
    - Ip-ruimte **voor IPv6-backend** van Front Door, terwijl deze wordt weergegeven in de servicetag, wordt niet vermeld in het JSON-bestand met Azure IP-bereiken. Als u op zoek bent naar een expliciet IPv6-adresbereik, is het momenteel beperkt tot`2a01:111:2050::/44`
    - De [basisinfrastructuurservices](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) van Azure via gevirtualiseerde host-IP-adressen: `168.63.129.16` en`169.254.169.254`

    > [!WARNING]
    > De ip-ruimte van Front Door kan later veranderen, maar we zullen ervoor zorgen dat voordat dat gebeurt, we zouden hebben geïntegreerd met [Azure IP Ranges en Service Tags](https://www.microsoft.com/download/details.aspx?id=56519). We raden u aan zich te abonneren op [Azure IP Ranges en Service Tags](https://www.microsoft.com/download/details.aspx?id=56519) voor eventuele wijzigingen of updates.

-    Voer een GET-bewerking uit op `2020-01-01` uw voordeur met de API-versie of hoger. Zoek `frontdoorID` in de API-aanroep naar veld. Filter op de inkomende header '**X-Azure-FDID'** die door Front Door naar `frontdoorID`uw backend wordt verzonden met de waarde als die van het veld. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Kan de anycast IP veranderen over de levensduur van mijn voordeur?

De frontend anycast IP voor uw voordeur mag doorgaans niet veranderen en kan statisch blijven gedurende de levensduur van de voordeur. Er zijn echter **geen garanties** voor hetzelfde. Neem geen directe afhankelijkheden van het IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Ondersteunt Azure Front Door statische of toegewezen IP's?

Nee, Azure Front Door ondersteunt momenteel geen statische of speciale frontend-IP's. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Ondersteunt Azure Front Door x-forwarded-for headers?

Ja, Azure Front Door ondersteunt de X-Forwarded-For-, X-Forwarded-Host en X-Forwarded-Proto-headers. Voor X-Forwarded-For als de header al aanwezig was, voegt Front Door de clientsocket IP eraan toe. Anders voegt het de header met de client socket IP als de waarde. Voor X-Forwarded-Host en X-Forwarded-Proto wordt de waarde overschreven.

Meer informatie over de door de [voordeur ondersteunde HTTP-headers](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Hoe lang duurt het om een Azure Front Door te implementeren? Werkt mijn voordeur nog steeds wanneer deze wordt bijgewerkt?

Een nieuwe front door creatie of updates van een bestaande Front Door duurt ongeveer 3 tot 5 minuten voor wereldwijde implementatie. Dat betekent dat in ongeveer 3 tot 5 minuten, uw Front Door configuratie zal worden ingezet in al onze POP's wereldwijd.

Opmerking - Het duurt ongeveer 30 minuten voordat aangepaste SSL-certificaatupdates wereldwijd worden geïmplementeerd.

Alle updates voor routes of backend pools etc. zijn naadloos en veroorzaken nul downtime (als de nieuwe configuratie juist is). Certificaatupdates zijn ook atoom en veroorzaken geen uitval, tenzij u overstapt van 'AFD Managed' naar 'Gebruik uw eigen cert' of vice versa.


## <a name="configuration"></a>Configuratie

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan Azure Front Door-laadbalans of routeverkeer binnen een virtueel netwerk?

Azure Front Door (AFD) vereist een openbare IP of openbaar oplosbare DNS-naam om verkeer te routeren. Het antwoord is dus dat geen AFD rechtstreeks kan routeren binnen een virtueel netwerk, maar het gebruik van een Application Gateway of Azure Load Balancer daartussen zal dit scenario oplossen.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Wat zijn de verschillende time-outs en limieten voor Azure Front Door?

Meer informatie over alle gedocumenteerde [time-outs en limieten voor Azure Front Door](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Prestaties

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hoe ondersteunt Azure Front Door hoge beschikbaarheid en schaalbaarheid?

Azure Front Door is een wereldwijd gedistribueerd multi-tenant platform met enorme hoeveelheden capaciteit om tegemoet te komen aan de schaalbaarheidsbehoeften van uw toepassing. Front Door wordt geleverd vanaf de rand van het wereldwijde netwerk van Microsoft en biedt wereldwijde mogelijkheden voor het balanceren van de lasten, waarmee u falen over uw hele toepassing of zelfs individuele microservices in verschillende regio's of verschillende clouds.

## <a name="ssl-configuration"></a>SSL-configuratie

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Welke TLS-versies worden ondersteund door Azure Front Door?

Alle Front Door profielen die na september 2019 zijn gemaakt, gebruiken TLS 1.2 als standaardminimum.

Front Door ondersteunt TLS-versies 1.0, 1.1 en 1.2. TLS 1.3 wordt nog niet ondersteund.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Welke certificaten worden ondersteund op Azure Front Door?

Als u het HTTPS-protocol wilt inschakelen voor het veilig leveren van inhoud op een aangepast frontdoordomein, u ervoor kiezen om een certificaat te gebruiken dat wordt beheerd door Azure Front Door of uw eigen certificaat te gebruiken.
De voordeur beheerde optie een standaard SSL-certificaat via Digicert en opgeslagen in De Sleutelkluis van De Voordeur. Als u ervoor kiest om uw eigen certificaat te gebruiken, u een certificaat van een ondersteunde CA aan boord nemen en kan het een standaard SSL, uitgebreid validatiecertificaat of zelfs een wildcardcertificaat zijn. Zelfondertekende certificaten worden niet ondersteund. Meer informatie over [het inschakelen van HTTPS voor een aangepast domein](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Ondersteunt Front Door autorotatie van certificaten?

Voor de optie Voordeurbeheercertificaat worden de certificaten automatisch gedraaid door Voordeur. Als u een door de voordeur beheerd certificaat gebruikt en ziet dat de vervaldatum van het certificaat minder dan 60 dagen verwijderd is, dient u een ondersteuningsticket in.
</br>Voor uw eigen aangepaste SSL-certificaat wordt autorotatie niet ondersteund. Net als bij de manier waarop het de eerste keer is ingesteld voor een bepaald aangepast domein, moet u Front Door wijzen op de juiste certificaatversie in uw Key Vault en ervoor zorgen dat de serviceprincipal voor Front Door nog steeds toegang heeft tot de Key Vault. Deze bijgewerkte certificaatimplementatiebewerking door Front Door is atoom en veroorzaakt geen productie-impact op voorwaarde dat de onderwerpnaam of SAN voor het certificaat niet verandert.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Wat zijn de huidige cipher suites ondersteund door Azure Front Door?

Hieronder volgen de huidige ciphersuites die worden ondersteund door Azure Front Door:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan ik SSL-beleid configureren om SSL-protocolversies te beheren?

U een minimale TLS-versie configureren in Azure Front Door in de aangepaste domeinHTTPS-instellingen via Azure portal of de [Azure REST API.](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) Momenteel u kiezen tussen 1.0 en 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan ik Front Door configureren om alleen specifieke cijfersuites te ondersteunen?

Nee, het configureren van Front Door voor specifieke cipher suites wordt niet ondersteund. U echter uw eigen aangepaste SSL-certificaat krijgen van uw Certificaatautoriteit (bijvoorbeeld Verisign, Entrust of Digicert) en specifieke cijfersuites op het certificaat hebben gemarkeerd wanneer u het hebt gegenereerd. 

### <a name="does-front-door-support-ocsp-stapling"></a>Ondersteunt Front Door OCSP nieten?

Ja, OCSP-nieting wordt standaard ondersteund door Front Door en er is geen configuratie vereist.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Ondersteunt Azure Front Door ook herversleuteling van verkeer naar de backend?

Ja, Azure Front Door ondersteunt SSL-offload en end-to-end SSL, waarmee het verkeer opnieuw wordt versleuteld naar de backend. In feite, aangezien de verbindingen met de backend plaatsvinden via het openbare IP, is het raadzaam dat u uw voordeur configureren om HTTPS te gebruiken als het doorsturen protocol.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Ondersteunt Front Door zelfondertekende certificaten op de backend voor HTTPS-verbinding?

Nee, zelfondertekende certificaten worden niet ondersteund op Front Door en de beperking geldt voor beide:

1. **Backends**: U zelfondertekende certificaten niet gebruiken wanneer u het verkeer doorstuurt als HTTPS- of HTTPS-statussondes of de cache vult voor vanaf de oorsprong voor routeringsregels met caching ingeschakeld.
2. **Frontend**: U zelfondertekende certificaten niet gebruiken wanneer u uw eigen aangepaste SSL-certificaat gebruikt voor het inschakelen van HTTPS op uw aangepaste domein.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Waarom mislukt HTTPS-verkeer naar mijn backend?

Voor het hebben van succesvolle HTTPS-verbindingen met uw backend, of het nu gaat om statussondes of voor doorsturen van aanvragen, kunnen er twee redenen zijn waarom HTTPS-verkeer mogelijk mislukt:

1. **Foutie naam van het certificaatonderwerp:** Voor HTTPS-verbindingen verwacht Front Door dat uw backend certificaat presenteert van een geldige CA met onderwerpnaam(en) die overeenkomt met de backendhostnaam. Als uw backendhostnaam bijvoorbeeld is `myapp-centralus.contosonews.net` ingesteld op en het certificaat dat uw back-end presenteert tijdens de SSL-handshake die noch `myapp-centralus.contosonews.net` `*myapp-centralus*.contosonews.net` in de onderwerpnaam heeft, weigert de voordeur de verbinding en resulteert dit in een fout. 
    1. **Oplossing:** Hoewel het niet wordt aanbevolen vanuit het oogpunt van naleving, u deze fout oplossen door de naamcontrole van het certificaatvak voor uw voordeur uit te schakelen. Dit is aanwezig onder Instellingen in Azure-portal en onder BackendPoolsInstellingen in de API.
2. **Backend hostingcertificaat van ongeldige CA:** Alleen certificaten van [geldige CA's](/azure/frontdoor/front-door-troubleshoot-allowed-ca) kunnen worden gebruikt aan de backend met Voordeur. Certificaten van interne CV's of zelfondertekende certificaten zijn niet toegestaan.

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Welke typen statistieken en logboeken zijn beschikbaar met Azure Front Door?

Zie [Statistieken en logboeken voor voor de voordeur controleren voor](front-door-diagnostics.md)informatie over logboeken en andere diagnostische mogelijkheden.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Wat is het bewaarbeleid voor de diagnostische logboeken?

Diagnostische logboeken stromen naar het opslagaccount van de klant en klanten kunnen het bewaarbeleid instellen op basis van hun voorkeur. Diagnostische logboeken kunnen ook worden verzonden naar een gebeurtenishub of Azure Monitor-logboeken. Zie [Azure Front Door Diagnostics](front-door-diagnostics.md)voor meer informatie .

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hoe krijg ik controlelogboeken voor Azure Front Door?

Controlelogboeken zijn beschikbaar voor Azure Front Door. Klik in de portal op **Activiteitslogboek** in het menublad van uw voordeur om toegang te krijgen tot het controlelogboek. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Kan ik waarschuwingen instellen met Azure Front Door?

Ja, Azure Front Door biedt ondersteuning voor waarschuwingen. Waarschuwingen worden geconfigureerd op basis van metrische gegevens. 

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
