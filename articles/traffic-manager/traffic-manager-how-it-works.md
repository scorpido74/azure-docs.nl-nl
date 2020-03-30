---
title: Hoe Azure Traffic Manager werkt | Microsoft Documenten
description: In dit artikel u begrijpen hoe Traffic Manager verkeer doorstuurt voor hoge prestaties en beschikbaarheid van uw webtoepassingen
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: rohink
ms.openlocfilehash: 4863ffd383cfcd46bad462156e26293d145fd418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294857"
---
# <a name="how-traffic-manager-works"></a>Hoe verkeersmanager werkt

Azure Traffic Manager stelt u in staat om de verdeling van het verkeer over de eindpunten van uw toepassing te beheren. Een eindpunt is een internetgerichte service die binnen of buiten Azure wordt gehost.

Traffic Manager biedt twee belangrijke voordelen:

- Verdeling van het verkeer volgens een van de verschillende [verkeersrouteringsmethoden](traffic-manager-routing-methods.md)
- [Continue bewaking van de status van eindpunt](traffic-manager-monitoring.md) en automatische failover wanneer eindpunten mislukken

Wanneer een client probeert verbinding maken met een service, moet deze eerst de DNS-naam van de service omzetten naar een IP-adres. De client maakt vervolgens verbinding met dat IP-adres om toegang te krijgen tot de service.

**Het belangrijkste punt om te begrijpen is dat Traffic Manager werkt op DNS-niveau.**  Traffic Manager gebruikt DNS om clients naar specifieke serviceeindpunten te leiden op basis van de regels van de verkeersrouteringsmethode. Clients maken **rechtstreeks**verbinding met het geselecteerde eindpunt . Traffic Manager is geen proxy of gateway. Traffic Manager ziet het verkeer niet passeren tussen de klant en de service.

## <a name="traffic-manager-example"></a>Voorbeeld van Verkeersmanager

Contoso Corp heeft een nieuw partnerportaal ontwikkeld. De URL voor `https://partners.contoso.com/login.aspx`deze portal is . De toepassing wordt gehost in drie regio's van Azure. Om de beschikbaarheid te verbeteren en de wereldwijde prestaties te maximaliseren, gebruiken ze Traffic Manager om clientverkeer te distribueren naar het dichtstbijzijnde beschikbare eindpunt.

Om deze configuratie te bereiken, voeren ze de volgende stappen uit:

1. Implementeer drie exemplaren van hun service. De DNS-namen van deze implementaties zijn 'contoso-us.cloudapp.net', 'contoso-eu.cloudapp.net' en 'contoso-asia.cloudapp.net'.
1. Maak een Traffic Manager-profiel met de naam 'contoso.trafficmanager.net' en configureer het om de verkeersrouteringsmethode 'Prestaties' over de drie eindpunten te gebruiken.
1. Configureer hun ijdelheiddomeinnaam 'partners.contoso.com', om naar 'contoso.trafficmanager.net' te wijzen, met behulp van een DNS CNAME-record.

![DNS-configuratie van Traffic Manager][1]

> [!NOTE]
> Wanneer u een ijdelheidsdomein gebruikt met Azure Traffic Manager, moet u een CNAME gebruiken om uw ijdelheidsdomeinnaam naar uw Traffic Manager-domeinnaam te leiden. Dns-standaarden staan u niet toe om een CNAME te maken op de 'apex' (of root) van een domein. Zo u geen CNAME maken voor 'contoso.com' (ook wel een 'naakt' domein genoemd). U alleen een CNAME maken voor een domein onder 'contoso.com', zoals 'www.contoso.com'. Om deze beperking te omzeilen, raden we u aan uw DNS-domein te hosten op [Azure DNS](../dns/dns-overview.md) en [Alias-records](../dns/tutorial-alias-tm.md) te gebruiken om uw profiel van verkeersbeheer aan te wijzen. U ook een eenvoudige HTTP-omleiding gebruiken om aanvragen voor 'contoso.com' te sturen naar een alternatieve naam zoals 'www.contoso.com'.

### <a name="how-clients-connect-using-traffic-manager"></a>Hoe clients verbinding maken met Traffic Manager

Als een client de pagina `https://partners.contoso.com/login.aspx`aanvraagt, voert de client de volgende stappen uit om de DNS-naam op te lossen en een verbinding tot stand te brengen:

![Verbindingsinstelling met verkeersbeheer][2]

1. De client stuurt een DNS-query naar de geconfigureerde recursieve DNS-service om de naam 'partners.contoso.com' op te lossen. Een recursieve DNS-service, ook wel een 'local DNS'-service genoemd, host DNS-domeinen niet rechtstreeks. Integendeel, de client off-laadt het werk van het contact opnemen met de verschillende gezaghebbende DNS-diensten over het internet die nodig zijn om een DNS-naam op te lossen.
2. Om de DNS-naam op te lossen, vindt de recursieve DNS-service de naamservers voor het domein 'contoso.com'. Vervolgens neemt het contact op met die naamservers om de 'partners.contoso.com' DNS-record aan te vragen. De contoso.com DNS-servers de CNAME-record retourneren die naar contoso.trafficmanager.net wijst.
3. Vervolgens vindt de recursieve DNS-service de naamservers voor het domein 'trafficmanager.net', die worden geleverd door de Azure Traffic Manager-service. Vervolgens stuurt het een verzoek om de 'contoso.trafficmanager.net' DNS-record naar die DNS-servers.
4. De naamservers van Traffic Manager ontvangen het verzoek. Ze kiezen een eindpunt op basis van:

    - De geconfigureerde status van elk eindpunt (uitgeschakelde eindpunten worden niet geretourneerd)
    - De huidige status van elk eindpunt, zoals bepaald door de statuscontroles van verkeersbeheerbeheer. Zie [Eindpuntbewaking van verkeersbeheer voor](traffic-manager-monitoring.md)meer informatie .
    - De gekozen verkeersrouteringsmethode. Zie [Routeringsmethoden voor verkeersbeheer](traffic-manager-routing-methods.md).

5. Het gekozen eindpunt wordt geretourneerd als een andere DNS CNAME-record. Laten we in dit geval aannemen contoso-us.cloudapp.net is teruggekeerd.
6. Vervolgens vindt de recursieve DNS-service de naamservers voor het domein 'cloudapp.net'. Het neemt contact op met die naamservers om de 'contoso-us.cloudapp.net' DNS-record aan te vragen. Een DNS'A-record met het IP-adres van het in de VS gevestigde serviceeindpunt wordt geretourneerd.
7. De recursieve DNS-service consolideert de resultaten en retourneert één DNS-antwoord op de client.
8. De client ontvangt de DNS-resultaten en maakt verbinding met het opgegeven IP-adres. De client maakt rechtstreeks verbinding met het eindpunt van de applicatieservice, niet via Traffic Manager. Aangezien het een HTTPS-eindpunt is, voert de client de benodigde SSL/TLS-handshake uit en maakt vervolgens een HTTP GET-verzoek voor de pagina '/login.aspx'.

De recursieve DNS-service slaat de DNS-antwoorden die deze ontvangt in de cache. De DNS-resolver op het clientapparaat slaat ook het resultaat in de cache. Met Caching kunnen volgende DNS-query's sneller worden beantwoord met behulp van gegevens uit de cache in plaats van andere naamservers op te vragen. De duur van de cache wordt bepaald door de eigenschap 'time-to-live' (TTL) van elke DNS-record. Kortere waarden resulteren in een snellere vervaldatum van de cache en dus meer retourvluchten naar de naamservers van Traffic Manager. Langere waarden betekenen dat het langer kan duren om verkeer weg te leiden van een mislukt eindpunt. Met Traffic Manager u de TTL die wordt gebruikt in DNS-antwoorden van Traffic Manager zo laag zijn als 0 seconden en tot 2.147.483.647 seconden (het maximale bereik dat voldoet aan [RFC-1035),](https://www.ietf.org/rfc/rfc1035.txt)zodat u de waarde kiezen die de behoeften van uw toepassing het beste in evenwicht brengt.

## <a name="faqs"></a>Veelgestelde vragen

* [Welk IP-adres gebruikt Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Welke typen verkeer kunnen worden omgeleid met Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Ondersteunt Traffic Manager "sticky" sessies?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Waarom zie ik een HTTP-fout bij het gebruik van Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Wat is de impact van de prestaties van het gebruik van Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Welke toepassingsprotocollen kan ik gebruiken met Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Kan ik Traffic Manager gebruiken met een 'naakte' domeinnaam?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Houdt Traffic Manager rekening met het subnetadres van de client bij het verwerken van DNS-query's?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Wat is DNS TTL en welke invloed heeft het op mijn gebruikers?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Hoe hoog of laag kan ik de antwoorden van TTL for Traffic Manager instellen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Hoe kan ik het aantal query's dat naar mijn profiel komt begrijpen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over eindpuntbewaking van Traffic Manager [en automatische failover](traffic-manager-monitoring.md).

Meer informatie over [verkeersrouteringsmethoden voor](traffic-manager-routing-methods.md)Traffic Manager .

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

