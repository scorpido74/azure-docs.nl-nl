---
title: Prestatieoverwegingen voor Azure Traffic Manager | Microsoft Documenten
description: Inzicht in de prestaties op Traffic Manager en hoe u de prestaties van uw website testen bij het gebruik van Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 84367a00643c48e7fe2fb7f907bab64589193b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938546"
---
# <a name="performance-considerations-for-traffic-manager"></a>Prestatieoverwegingen voor Traffic Manager

Op deze pagina worden prestatieoverwegingen uitgelegd met Behulp van Traffic Manager. Denkt u zich het volgende scenario eens in:

U hebt exemplaren van uw website in de regio's WestUS en Oost-Azië. Een van de gevallen is niet de health check voor de verkeersmanager sonde. Applicatieverkeer wordt gericht op de gezonde regio. Deze failover wordt verwacht, maar de prestaties kunnen een probleem zijn op basis van de latentie van het verkeer dat nu naar een verre regio reist.

## <a name="performance-considerations-for-traffic-manager"></a>Prestatieoverwegingen voor Traffic Manager

De enige prestatie-impact die Traffic Manager op uw website kan hebben, is de eerste DNS-lookup. Een DNS-verzoek om de naam van uw Traffic Manager-profiel wordt verwerkt door de Microsoft DNS-hoofdserver die de trafficmanager.net-zone host. Traffic Manager vult en werkt regelmatig de DNS-rootservers van Microsoft bij op basis van het beleid voor verkeersbeheer en de resultaten van de sonde. Dus zelfs tijdens de eerste DNS-lookup worden er geen DNS-query's naar Traffic Manager verzonden.

Traffic Manager bestaat uit verschillende componenten: DNS-naamservers, een API-service, de opslaglaag en een endpoint-bewakingsservice. Als een verkeersmanager-servicecomponent uitvalt, heeft dit geen effect op de DNS-naam die is gekoppeld aan uw Traffic Manager-profiel. De records in de Microsoft DNS-servers blijven ongewijzigd. Eindpuntbewaking en DNS-updates vinden echter niet plaats. Daarom kan Traffic Manager DNS niet bijwerken om naar uw failoversite te wijzen wanneer uw primaire site uitvalt.

DNS-naamresolutie is snel en de resultaten worden in de cache opgeslagen. De snelheid van de eerste DNS-lookup is afhankelijk van de DNS-servers die de client gebruikt voor naamomzetting. Een client kan doorgaans een DNS-lookup voltooien binnen ~ 50 ms. De resultaten van de lookup worden opgeslagen voor de duur van de DNS Time-to-live (TTL). De standaardTTL voor Traffic Manager is 300 seconden.

Verkeer stroomt NIET door Traffic Manager. Zodra de DNS-lookup is voltooid, heeft de client een IP-adres voor een exemplaar van uw website. De klant maakt rechtstreeks verbinding met dat adres en komt niet via Traffic Manager. Het beleid voor Traffic Manager dat u kiest, heeft geen invloed op de DNS-prestaties. Een routeringsmethode voor prestaties kan echter een negatieve invloed hebben op de toepassingservaring. Als uw beleid bijvoorbeeld verkeer vanuit Noord-Amerika omleidt naar een instantie die wordt gehost in Azië, kan de netwerklatentie voor die sessies een prestatieprobleem zijn.

## <a name="measuring-traffic-manager-performance"></a>Prestaties van verkeersbeheerbeheer meten

Er zijn verschillende websites die u gebruiken om de prestaties en het gedrag van een Traffic Manager-profiel te begrijpen. Veel van deze sites zijn gratis, maar kunnen beperkingen hebben. Sommige sites bieden verbeterde monitoring en rapportage voor een vergoeding.

De hulpprogramma's op deze sites meten DNS-latencies en geven de opgeloste IP-adressen weer voor clientlocaties over de hele wereld. De meeste van deze hulpprogramma's bewaren de DNS-resultaten niet. Daarom tonen de hulpprogramma's de volledige DNS-lookup elke keer dat een test wordt uitgevoerd. Wanneer u test vanuit uw eigen client, ervaart u slechts één keer de volledige DNS-opzoekprestaties tijdens de TTL-duur.

## <a name="sample-tools-to-measure-dns-performance"></a>Voorbeeldhulpprogramma’s voor het meten van de DNS-prestaties

* [DNS oplossen](https://www.solvedns.com/dns-comparison/)

    SolveDNS biedt veel prestatietools. Met de tool DNS-vergelijking u zien hoe lang het duurt om uw DNS-naam op te lossen en hoe dit zich verhoudt tot andere DNS-serviceproviders.

* [Websitepulse](https://www.websitepulse.com/help/tools.php)

    Een van de eenvoudigste tools is WebSitePulse. Voer de URL in om de DNS-resolutietijd, First Byte, Last Byte en andere prestatiestatistieken weer te geven. U kiezen uit drie verschillende testlocaties. In dit voorbeeld ziet u dat uit de eerste uitvoering blijkt dat DNS-lookup 0,204 sec in neemt.

    ![puls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Omdat de resultaten in de cache worden opgeslagen, duurt de tweede test voor hetzelfde Traffic Manager-eindpunt de DNS-lookup 0,002 sec.

    ![puls2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [CA-app synthetische monitor](https://asm.ca.com/en/checkit.php)

    Deze site, voorheen bekend als het watch-mouse Check-website-hulpprogramma, toont u de DNS-resolutietijd van meerdere geografische regio's tegelijk. Voer de URL in om de DNS-resolutietijd, verbindingstijd en -snelheid vanaf verschillende geografische locaties weer te geven. Gebruik deze test om te zien welke gehoste service wordt geretourneerd voor verschillende locaties over de hele wereld.

    ![puls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Deze tool biedt prestatiestatistieken voor elk element van een webpagina. Op het tabblad Paginaanalyse wordt het percentage tijd weergegeven dat wordt besteed aan DNS-lookup.

* [Wat is mijn DNS?](https://www.whatsmydns.net/)

    Deze site doet een DNS lookup vanaf 20 verschillende locaties en geeft de resultaten weer op een kaart.

* [Webinterface graven](https://www.digwebinterface.com)

    Deze site toont meer gedetailleerde DNS-informatie, waaronder CNAMEs en A-records. Controleer de 'Colorize output' en 'Stats' onder opties en selecteer 'Alles' onder Nameservers.

## <a name="next-steps"></a>Volgende stappen

[Over verkeersbeheermethoden](traffic-manager-routing-methods.md)

[Uw instellingen voor Traffic Manager testen](traffic-manager-testing-settings.md)

[Bewerkingen op Traffic Manager (REST API-referentiemateriaal)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager Cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager)

