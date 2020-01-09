---
title: Azure front-deur service-Veelgestelde vragen
description: Op deze pagina vindt u antwoorden op veelgestelde vragen over de Azure front-deur service
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
ms.openlocfilehash: 39051014e5e474264a44983fb366bc08f02c31e0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639851"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Veelgestelde vragen over de Azure front-deur service

In dit artikel vindt u antwoorden op veelgestelde vragen over de functies en functionaliteit van de Azure front-deur service. Als u het antwoord op uw vraag niet ziet, kunt u contact met ons opnemen via de volgende kanalen (in de volg orde waarin ze worden doorzocht):

1. De sectie opmerkingen van dit artikel.
2. [Azure front-deur service UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft ondersteuning:** Als u een nieuwe ondersteunings aanvraag wilt maken, selecteert u in het Azure Portal op het tabblad **Help** de knop **Help en ondersteuning** en selecteert u vervolgens **nieuwe ondersteunings aanvraag**.

## <a name="general"></a>Algemeen

### <a name="what-is-azure-front-door-service"></a>Wat is Azure Front Door Service?

De Azure front-deur service is een Application Delivery Network (ADN) als een service, met diverse mogelijkheden voor taak verdeling voor laag 7 voor uw toepassingen. Het biedt dynamische site versnelling (DSA) samen met globale taak verdeling met een nabije realtime failover. Het is een Maxi maal beschik bare en schaal bare service die volledig wordt beheerd door Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Welke functies worden ondersteund door de Azure front-deur service?

De Azure front-deur service biedt ondersteuning voor dynamische site versnelling (DSA), SSL-offloading en end-to-end SSL, Web Application firewall, op cookies gebaseerde sessie affiniteit, op URL-pad gebaseerde route ring, gratis certificaten en meerdere domein beheer, en andere. Zie [overzicht van de Azure front-deur service](front-door-overview.md)voor een volledige lijst met ondersteunde functies.

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Wat is het verschil tussen de Azure front-deur service en Azure-toepassing gateway?

Hoewel zowel de voor deur als de Application Gateway laag 7 (HTTP/HTTPS) load balancers zijn, is het belangrijkste verschil dat de voor deur een wereld wijde service is, terwijl Application Gateway een regionale service is. Met Application Gateway kunt u een taak verdeling tussen uw virtuele machines/containers/clusters/stempels in verschillende regio's, met behulp van de voor deur, waarmee u het evenwicht tussen uw Vm's en recipiënten, enzovoort.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Wanneer moet een Application Gateway achter aan de voor deur worden geïmplementeerd?

De belangrijkste scenario's waarvan een moet worden gebruikt Application Gateway achter de voor deur zijn:

- Met de voor deur kan alleen op het globale niveau taak verdeling op basis van een pad worden uitgevoerd, maar als er nog meer verkeer in het virtuele netwerk (VNET) moet worden geladen, moeten ze Application Gateway gebruiken.
- Omdat de voor deur niet werkt op een VM-of container niveau, kan er geen verbinding worden verbroken. Met Application Gateway kunt u echter de verbinding verbreken. 
- Met een Application Gateway achter AFD kunt u een SSL-offload van 100% krijgen en alleen HTTP-aanvragen in hun virtuele netwerk (VNET) routeren.
- De voor deur en Application Gateway de affiniteit van de ondersteunings sessie. Hoewel de front-deur volgend verkeer van een gebruikers sessie naar hetzelfde cluster of dezelfde back-end in een bepaalde regio kan omleiden, kan Application Gateway het verkeer naar dezelfde server in het cluster affinitize.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kunnen we Azure Load Balancer implementeren achter de voor deur?

Voor de Azure front-deur service is een openbaar VIP of een openbaar beschik bare DNS-naam vereist om het verkeer naar te sturen. Het implementeren van een Azure Load Balancer achter deur is een veelvoorkomende use-case.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Welke protocollen ondersteunt de Azure front-deur service?

De Azure front-deur service ondersteunt HTTP, HTTPS en HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Hoe ondersteunt de Azure front-deur service HTTP/2?

Ondersteuning voor HTTP/2-protocollen is alleen beschikbaar voor clients die verbinding maken met de Azure front-deur service. De communicatie met back-ends in de back-end-groep is via HTTP/1.1. HTTP/2-ondersteuning is standaard ingeschakeld.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Welke bronnen worden vandaag ondersteund als onderdeel van de back-end-groep?

Back-endservers kunnen bestaan uit opslag, Web-app, Kubernetes-instanties of een andere aangepaste hostnaam die open bare connectiviteit heeft. De Azure front-deur service vereist dat de back-ends worden gedefinieerd via een openbaar IP-adres of een openbaar omzet bare DNS-hostnaam. Leden van back-endservers kunnen zich in zones, regio's of zelfs buiten Azure bevinden, zolang ze open bare connectiviteit hebben.

### <a name="what-regions-is-the-service-available-in"></a>In welke regio's is de service beschikbaar?

De Azure front-deur service is een wereld wijde service en is niet gebonden aan een specifieke Azure-regio. De enige locatie die u moet opgeven tijdens het maken van een voor deur is de locatie van de resource groep, die in principe aangeeft waar de meta gegevens voor de resource groep worden opgeslagen. De voor deur van de resource zelf wordt gemaakt als globale resource en de configuratie wordt globaal geïmplementeerd op alle Pop's (Presence Point). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Wat zijn de POP-locaties voor de Azure front-deur-service?

De Azure front-deur service heeft dezelfde lijst met POP-locaties (Point of Presence) als Azure CDN van micro soft. Voor de volledige lijst met onze Pop's verwijzen we naar [Azure CDN pop-locaties van micro soft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Is de Azure front-deur service een speciale implementatie voor mijn toepassing of wordt deze gedeeld door klanten?

De Azure front-deur service is een wereld wijd gedistribueerde multi tenant-service. De infra structuur voor de voor deur wordt dus gedeeld door alle klanten. Als u echter een front deur profiel maakt, definieert u de specifieke configuratie die is vereist voor uw toepassing. er zijn geen wijzigingen aangebracht aan de voor deur van andere front-deur configuraties.

### <a name="is-http-https-redirection-supported"></a>Wordt HTTP-> HTTPS-omleiding ondersteund?

Ja. De Azure front-deur service ondersteunt ook host-, pad-en query teken reeks omleidingen, evenals een deel van URL-omleiding. Meer informatie over [URL-omleiding](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>In welke volg orde worden routerings regels verwerkt?

Routes voor de voor deur worden niet besteld en een specifieke route wordt geselecteerd op basis van de beste overeenkomst. Meer informatie over [de voor deur die overeenkomt met aanvragen voor een routerings regel](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hoe kan ik Vergrendel de toegang tot mijn back-end naar een Azure front-deur?

Als u uw toepassing wilt vergren delen om alleen verkeer van uw specifieke voor deur te accepteren, moet u IP-Acl's instellen voor uw back-end en vervolgens de set geaccepteerde waarden voor de header X-forward-host die door de Azure front-deur wordt verzonden, beperken. Deze stappen worden hieronder beschreven:

- Configureer IP-Acl's voor voor uw back-ends om verkeer te accepteren van de back-end-IP-adres ruimte van de Azure front deur en alleen de infrastructuur services van Azure. We werken samen met de integratie met [Azure IP-bereiken en-tags,](https://www.microsoft.com/download/details.aspx?id=56519) maar voor nu kunt u de IP-adresbereiken als hieronder raadplegen:
 
    - **IPv4** -back-end van de voor deur: `147.243.0.0/16`
    - **IPv6** -back-end van front deur: `2a01:111:2050::/44`
    - De [basis infrastructuur services](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) van Azure via gevirtualiseerde host-IP-adressen: `168.63.129.16` en `169.254.169.254`

    > [!WARNING]
    > De backend-back-end van de voor deur kan later worden gewijzigd, maar we zorgen ervoor dat voordat dat gebeurt, dat we hebben geïntegreerd met [Azure IP-bereiken en-Tags](https://www.microsoft.com/download/details.aspx?id=56519). U wordt aangeraden om u te abonneren op [Azure IP-bereiken en service Tags](https://www.microsoft.com/download/details.aspx?id=56519) voor eventuele wijzigingen of updates.

-   Filter op de waarden voor de inkomende header**X-forward-host**verzonden door de voor deur. De enige toegestane waarden voor de header moeten alle frontend-hosts zijn, zoals gedefinieerd in de voor deur configuratie. Meer specifiek, alleen de hostnamen waarvoor u verkeer wilt accepteren, op deze specifieke back-end van uw bedrijf.
    - Voor beeld: Stel dat uw front-deur configuratie de volgende frontend-hosts bevat _`contoso.azurefd.net`_ (a), _`www.contoso.com`_ (B), _ (C) en _`notifications.contoso.com`_ (D). Stel dat u twee back-ends hebt X en Y. 
    - Back-end X mag alleen verkeer van hostnamen A en B overnemen. back-end-Y kan verkeer van A, C en D overnemen.
    - Op backend X moet u dus alleen verkeer accepteren waarbij de header**X-forward-host**is ingesteld op _`contoso.azurefd.net`_ of _`www.contoso.com`_ . Voor alle andere gegevens, backend X moet het verkeer afwijzen.
    - Op de back-end-Y mag alleen verkeer worden geaccepteerd dat de header**X-forward-host**heeft ingesteld op _`contoso.azurefd.net`_ , _`api.contoso.com`_ of _`notifications.contoso.com`_ . Voor alle andere zaken moet de back-end-Y het verkeer afwijzen.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Kan het anycast-IP-adres worden gewijzigd gedurende de levens duur van mijn front-deur?

Het front-end-frontend-IP-adres voor de voor deur moet normaal gesp roken niet worden gewijzigd en kan statisch blijven gedurende de levens duur van de voor deur. Er zijn echter **geen garanties** voor dezelfde. Neem geen directe afhankelijkheden op het IP-adres.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Ondersteunt de Azure front-deur service statische of toegewezen Ip's?

Nee, de Azure front-deur service biedt momenteel geen ondersteuning voor statische of speciale frontend-IP-adressen. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Ondersteunt de Azure front-deur service x-doorgestuurd-voor kopteksten?

Ja, de Azure front-deur service ondersteunt de headers X-forward-for, X-forward-host en X-forward-proto. Voor X-doorgestuurd: als de header al aanwezig is, wordt het IP-adres van de client aan de voor deur toegevoegd. Anders wordt de header met het IP-adres van de client-socket als waarde toegevoegd. Voor X-doorgestuurd-host en X-doorgestuurd-proto wordt de waarde overschreven.

Meer informatie over de [ondersteunde HTTP-headers voor de voor deur](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Hoe lang duurt het om een Azure front-deur service te implementeren? Werkt mijn front-deur nog steeds wanneer ze worden bijgewerkt?

Een nieuwe voor deur maken of updates voor een bestaande front-deur neemt ongeveer drie tot vijf minuten in beslag voor wereld wijde implementatie. Dat betekent ongeveer 3 tot 5 minuten dat de configuratie van de voor deur wordt geïmplementeerd in al onze Pop's wereld wijd.

Opmerking: aangepaste SSL-certificaat updates nemen ongeveer 30 minuten in beslag.

## <a name="configuration"></a>Configuratie

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan de taak verdeling van de voor deur van Azure voor de voor grond zijn of verkeer binnen een virtueel netwerk routeren?

Voor de Azure-deur (AFD) is een openbaar IP-adres of een openbaar omgezette DNS-naam vereist om verkeer te routeren. Het antwoord is dus geen AFD direct in staat te stellen om binnen een virtueel netwerk te routeren, maar met behulp van een Application Gateway of Azure Load Balancer in tussen wordt dit scenario opgelost.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Wat zijn de verschillende time-outs en limieten voor de Azure front-deur service?

Meer informatie over alle gedocumenteerde [time-outs en limieten voor de Azure front-deur service](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Prestaties

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Hoe ondersteunt de Azure front-deur service hoge Beschik baarheid en schaal baarheid?

De Azure front-deur service is een wereld wijd gedistribueerd multi tenant platform met grote hoeveel heden capaciteit voor de schaal baarheids behoeften van uw toepassing. De deur van het wereld wijde netwerk van micro soft biedt wereld wijde mogelijkheden voor taak verdeling waarmee u een failover kunt uitvoeren voor uw hele toepassing of zelfs voor afzonderlijke micro Services in regio's of verschillende Clouds.

## <a name="ssl-configuration"></a>SSL-configuratie

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Welke TLS-versies worden ondersteund door de Azure front-deur service?

Voor alle profielen voor de voorste deur die na september 2019 worden gemaakt, wordt TLS 1,2 als standaard minimum gebruikt.

De front-deur ondersteunt TLS-versies 1,0, 1,1 en 1,2. TLS 1,3 wordt nog niet ondersteund.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Welke certificaten worden ondersteund op de Azure front-deur-service?

Als u het HTTPS-protocol wilt inschakelen voor het veilig leveren van inhoud op een aangepast domein voor de voor deur, kunt u kiezen of u een certificaat wilt gebruiken dat wordt beheerd door de Azure front-deur service of uw eigen certificaat wilt gebruiken.
De voor deur beheerde optie voorziet in een standaard SSL-certificaat via Digicert en wordt opgeslagen in de Key Vault van de front-deur. Als u ervoor kiest uw eigen certificaat te gebruiken, kunt u een certificaat van een ondersteunde CA onboarden en een standaard SSL-, uitgebreid validatie certificaat of zelfs een certificaat met Joker tekens. Zelfondertekende certificaten worden niet ondersteund. Meer informatie [over het inschakelen van HTTPS voor een aangepast domein](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Ondersteunt front-deur het omdraaien van certificaten?

Voor de optie beheerd certificaat voor de voor deur worden de certificaten automatisch gedraaid door de voor deur. Als u een door de voor deur beheerd certificaat gebruikt en u ziet dat de verval datum van het certificaat korter is dan 60 dagen, kunt u een ondersteunings ticket indienen.
</br>Voor uw eigen aangepaste SSL-certificaat wordt autorotation niet ondersteund. Net als bij het instellen van de eerste keer voor een aangepast domein, moet u de voor deur naar de juiste certificaat versie in uw Key Vault pointen en ervoor zorgen dat de service-principal voor de voor deur nog steeds toegang heeft tot de Key Vault. Deze bijgewerkte certificaat lancerings bewerking door de voor deur is Atomic en heeft geen invloed op de productie, mits de object naam of het SAN voor het certificaat niet verandert.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Wat zijn de huidige coderings suites die worden ondersteund door de Azure front-deur service?

Hieronder ziet u de huidige coderings suites die worden ondersteund door de Azure front-deur service:

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

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Ondersteunt de Azure front-deur service ook het opnieuw versleutelen van verkeer naar de back-end?

Ja, de Azure front-deur service ondersteunt SSL-offload en end-to-end SSL, waardoor het verkeer opnieuw versleutelt wordt naar de back-end. Omdat de verbindingen met de back-end een openbaar IP-adres hebben, wordt aanbevolen dat u de voor deur configureert om HTTPS te gebruiken als het doorstuur protocol.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Kan ik SSL-beleid configureren om SSL-protocol versies te beheren?

U kunt een minimale TLS-versie in azure front-deur configureren via de [Azure-rest API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Op dit moment kunt u kiezen tussen 1,0 en 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan ik front-deur zo configureren dat alleen specifieke coderings suites worden ondersteund?

Nee, het configureren van de voor deur voor specifieke coderings suites wordt niet ondersteund. 

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Welke soorten metrische gegevens en logboeken zijn beschikbaar met de Azure front-deur service?

Zie [metrische gegevens en logboeken voor de voor deur bewaken](front-door-diagnostics.md)voor meer informatie over Logboeken en andere diagnostische mogelijkheden.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Wat is het Bewaar beleid voor de diagnostische logboeken?

Diagnostische logboeken stroomt naar het opslag account van klanten en klanten kunnen het Bewaar beleid instellen op basis van hun voor keur. Diagnostische logboeken kunnen ook worden verzonden naar een event hub of Azure Monitor-Logboeken. Zie [Azure front-deur service diagnostiek](front-door-diagnostics.md)voor meer informatie.

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Hoe kan ik audit logboeken voor de Azure front-deur-service ophalen?

Audit logboeken zijn beschikbaar voor de Azure front-deur-service. Klik in de portal op **activiteiten logboek** in de menu-Blade van de voor deur om toegang te krijgen tot het audit logboek. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Kan ik waarschuwingen instellen met de Azure front-deur-service?

Ja, de Azure front-deur service biedt ondersteuning voor waarschuwingen. Waarschuwingen worden geconfigureerd voor metrische gegevens. 

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
