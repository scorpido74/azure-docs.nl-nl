---
title: Azure-productienetwerk
description: In dit artikel vindt u een algemene beschrijving van het Azure-productienetwerk.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7c0748e4ff1531649274834cb1e602c228f102e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726695"
---
# <a name="the-azure-production-network"></a>Het Azure-productienetwerk
De gebruikers van het Azure-productienetwerk omvatten zowel externe klanten die toegang hebben tot hun eigen Azure-toepassingen als intern Azure-ondersteuningspersoneel dat het productienetwerk beheert. In dit artikel worden de beveiligingstoegangsmethoden en beveiligingsmechanismen voor het tot stand brengen van verbindingen met het Azure-productienetwerk besproken.

## <a name="internet-routing-and-fault-tolerance"></a>Internetroutering en fouttolerantie
Een wereldwijd redundante interne en externe AZURE Domain Name Service (DNS)-infrastructuur, gecombineerd met meerdere primaire en secundaire DNS-serverclusters, biedt fouttolerantie. Tegelijkertijd worden extra Azure-netwerkbeveiligingsbesturingselementen, zoals NetScaler, gebruikt om DDoS-aanvallen (distributed denial of service) te voorkomen en de integriteit van Azure DNS-services te beschermen.

De Azure DNS-servers bevinden zich in meerdere datacenterfaciliteiten. De Azure DNS-implementatie bevat een hiërarchie van secundaire en primaire DNS-servers om azure-klantdomeinnamen openbaar op te lossen. De domeinnamen meestal op te lossen om een CloudApp.net adres, die het virtuele IP (VIP) adres voor de service van de klant wraps. Uniek voor Azure, de VIP die overeenkomt met intern dedicated IP (DIP) adres van de tenant vertaling wordt gedaan door de Microsoft load balancers die verantwoordelijk zijn voor die VIP.

Azure wordt gehost in geografisch gedistribueerde Azure-datacenters binnen de VS en is gebouwd op state-of-the-art routeringsplatforms die robuuste, schaalbare architecturale standaarden implementeren. Onder de opmerkelijke kenmerken zijn:

- Multiprotocol Label Switching (MPLS)-gebaseerde verkeersengineering, die een efficiënt gebruik van de verbinding en sierlijke degradatie van de service biedt als er een storing is.
- Netwerken worden geïmplementeerd met "need plus one" (N+1) redundantiearchitecturen of beter.
- Extern worden datacenters bediend door speciale netwerkcircuits met hoge bandbreedte die redundant eigenschappen verbinden met meer dan 1.200 internetserviceproviders wereldwijd op meerdere peeringpunten. Deze verbinding biedt meer dan 2.000 gigabyte per seconde (GBps) edge capaciteit.

Omdat Microsoft eigenaar is van eigen netwerkcircuits tussen datacenters, helpen deze kenmerken het Azure-aanbod om 99,9+ procent netwerkbeschikbaarheid te bereiken zonder dat traditionele externe internetproviders nodig zijn.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Verbinding met productienetwerk en bijbehorende firewalls
Het azure-netwerkverkeerverkeerleidt verkeer naar het Azure-productienetwerk dat zich in het dichtstbijzijnde regionale datacenter in de VS bevindt. Omdat de Azure-productiedatacenters consistente netwerkarchitectuur en -hardware behouden, is de beschrijving van de verkeersstroom die volgt consistent van toepassing op alle datacenters.

Nadat internetverkeer voor Azure is doorgestuurd naar het dichtstbijzijnde datacenter, wordt een verbinding tot stand gebracht met de toegangsrouters. Deze toegangsrouters dienen om verkeer tussen Azure-knooppunten en geinstantiated VM's met klantproblemen te isoleren. Netwerkinfrastructuurapparaten op de toegangs- en randlocaties zijn de grenspunten waar invallen- en uitgangsfilters worden toegepast. Deze routers zijn geconfigureerd via een tiered access-control list (ACL) om ongewenst netwerkverkeer te filteren en indien nodig verkeerstarieflimieten toe te passen. Verkeer dat is toegestaan door ACL wordt doorgestuurd naar de load balancers. Distributierouters zijn ontworpen om alleen door Microsoft goedgekeurde IP-adressen toe te staan, anti-spoofing te bieden en TCP-verbindingen tot stand te brengen die ACL's gebruiken.

Externe load-balancing-apparaten bevinden zich achter de toegangsrouters om de vertaling van netwerkadres (NAT) uit te voeren, van internet-routable IP's naar interne IP-gebruikers van Azure. De apparaten leiden ook pakketten naar geldige interne IP's en poorten voor productie, en ze fungeren als een beschermingsmechanisme om het blootstellen van de interne productienetwerkadresruimte te beperken.

Standaard dwingt Microsoft Hypertext Transfer Protocol Secure (HTTPS) af voor al het verkeer dat wordt verzonden naar webbrowsers van klanten, inclusief aanmelding en alle verkeer daarna. Het gebruik van TLS v1.2 maakt een veilige tunnel mogelijk waar het verkeer doorheen kan stromen. ACL's op toegang en core routers zorgen ervoor dat de bron van het verkeer consistent is met wat wordt verwacht.

Een belangrijk onderscheid in deze architectuur, wanneer het wordt vergeleken met de traditionele beveiligingsarchitectuur, is dat er geen speciale hardwarefirewalls, gespecialiseerde inbraakdetectie- of preventieapparaten zijn die normaal gesproken verwacht voordat verbindingen worden gemaakt met de Azure-productieomgeving. Klanten verwachten meestal deze hardwarefirewallapparaten in het Azure-netwerk; er wordt echter geen gebruikt in Azure. Bijna uitsluitend zijn deze beveiligingsfuncties ingebouwd in de software waarop de Azure-omgeving wordt uitgevoerd om robuuste, gelaagde beveiligingsmechanismen te bieden, waaronder firewallmogelijkheden. Bovendien is het bereik van de grens en de bijbehorende uitbreiding van kritieke beveiligingsapparaten eenvoudiger te beheren en te inventariseren, zoals in de vorige afbeelding wordt weergegeven, omdat deze wordt beheerd door de software waarop Azure wordt uitgevoerd.

## <a name="core-security-and-firewall-features"></a>Kernbeveiligings- en firewallfuncties
Azure implementeert robuuste softwarebeveiliging en firewallfuncties op verschillende niveaus om beveiligingsfuncties af te dwingen die meestal worden verwacht in een traditionele omgeving om de belangrijkste beveiligingsautorisatiegrens te beschermen.

### <a name="azure-security-features"></a>Azure-beveiligingsfuncties
Azure implementeert hostgebaseerde softwarefirewalls binnen het productienetwerk. Verschillende kernbeveiligings- en firewallfuncties bevinden zich binnen de kernazure-omgeving. Deze beveiligingsfuncties weerspiegelen een diepgaande strategie binnen de Azure-omgeving. Klantgegevens in Azure worden beschermd door de volgende firewalls:

**Hypervisor firewall (packet filter)**: Deze firewall wordt geïmplementeerd in de hypervisor en geconfigureerd door de fabric controller (FC) agent. Deze firewall beschermt de tenant die binnen de VM wordt uitgevoerd tegen ongeautoriseerde toegang. Wanneer een VM wordt gemaakt, wordt al het verkeer standaard geblokkeerd en voegt de FC-agent regels en uitzonderingen toe in het filter om geautoriseerd verkeer toe te staan.

Hier worden twee categorieën regels geprogrammeerd:

- **Machineconfig of infrastructuurregels**: Standaard wordt alle communicatie geblokkeerd. Er bestaan uitzonderingen waarmee een VM DHCP-communicatie- en DNS-informatie (Dynamic Host Configuration Protocol) kan verzenden en ontvangen en verkeer naar de 'openbare' uitgaande internet naar andere VM's binnen de FC-cluster- en OS-activeringsserver kan verzenden. Omdat de lijst met uitgaande bestemmingen van de VM's geen Azure-router-subnetten en andere Microsoft-eigenschappen bevat, fungeren de regels als een verdedigingslaag voor hen.
- **Regels voor rolconfiguratiebestand:** definieert de binnenkomende ACL's op basis van het servicemodel van de tenants. Als een tenant bijvoorbeeld een webfrontend heeft op poort 80 op een bepaalde VM, wordt poort 80 geopend voor alle IP-adressen. Als de VM een werkrol heeft, wordt de werkrol alleen geopend voor de vm binnen dezelfde tenant.

**Native host firewall**: Azure Service Fabric en Azure Storage draaien op een native OS, dat geen hypervisor heeft en daarom is Windows Firewall geconfigureerd met de voorgaande twee sets regels.

**Hostfirewall**: De hostfirewall beschermt de hostpartitie, waarop de hypervisor wordt uitgevoerd. De regels zijn zo geprogrammeerd dat alleen de FC en de springboxen met de hostpartitie op een specifieke poort kunnen praten. De andere uitzonderingen zijn het toestaan van DHCP-antwoord- en DNS-antwoorden. Azure maakt gebruik van een machineconfiguratiebestand, dat een sjabloon met firewallregels voor de hostpartitie bevat. Er bestaat ook een uitzondering voor hostfirewall waarmee VM's kunnen communiceren met hostcomponenten, draadserver en metadataserver, via specifieke protocol/poorten.

**Gastfirewall**: Het Windows Firewall-stuk van het gastbesturingssysteem, dat door klanten kan worden geconfigureerd op vm's en opslag van klanten.

Aanvullende beveiligingsfuncties die zijn ingebouwd in de Azure-mogelijkheden zijn:

- Infrastructuurcomponenten die IP-adressen toegewezen krijgen die afkomstig zijn van DIP's. Een aanvaller op het internet kan geen verkeer naar die adressen richten omdat het Microsoft niet zou bereiken. Internetgatewayrouters filteren pakketten die uitsluitend zijn geadresseerd aan interne adressen, zodat ze niet in het productienetwerk terechtkomen. De enige onderdelen die verkeer accepteren dat naar VIP's is gericht, zijn load balancers.
- Firewalls die op alle interne knooppunten worden geïmplementeerd, hebben drie overwegingen voor primaire beveiligingsarchitectuur voor een bepaald scenario:

   - Firewalls worden achter de load balancer geplaatst en accepteren pakketten vanaf elke locatie. Deze pakketten zijn bedoeld om extern worden blootgesteld en zou overeenkomen met de open poorten in een traditionele perimeter firewall.
   - Firewalls accepteren pakketten alleen vanaf een beperkt aantal adressen. Deze overweging maakt deel uit van de defensieve diepgaande strategie tegen DDoS-aanvallen. Dergelijke verbindingen zijn cryptografisch geverifieerd.
   - Firewalls zijn alleen toegankelijk via geselecteerde interne knooppunten. Ze accepteren pakketten alleen van een opgesomde lijst met bron-IP-adressen, die allemaal DIP's binnen het Azure-netwerk zijn. Een aanval op het bedrijfsnetwerk kan bijvoorbeeld aanvragen naar deze adressen sturen, maar de aanvallen worden geblokkeerd, tenzij het bronadres van het pakket één is in de opgesomde lijst binnen het Azure-netwerk.
     - De toegangsrouter in de perimeter blokkeert uitgaande pakketten die zijn geadresseerd aan een adres dat zich in het Azure-netwerk bevindt vanwege de geconfigureerde statische routes.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)
