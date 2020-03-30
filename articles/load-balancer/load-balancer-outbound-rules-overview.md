---
title: Uitgaande regels - Azure Load Balancer
description: Ga met dit leerpad aan de slag met uitgaande regels om uitgaande netwerkadresvertalingen te definiëren.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304188"
---
# <a name="load-balancer-outbound-rules"></a>Uitgaande regels load Balancer

Azure Load Balancer biedt uitgaande connectiviteit van een virtueel netwerk naast binnenkomend.  Uitgaande regels maken het eenvoudig om de uitgaande netwerkadresvertaling van de [standaardloadbalancer](load-balancer-standard-overview.md)te configureren.  U hebt volledige declaratieve controle over uitgaande connectiviteit om deze mogelijkheid te schalen en af te stemmen op uw specifieke behoeften.

![Uitgaande regels load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Met uitgaande regels u Load Balancer gebruiken om: 
- definiëren uitgaande NAT vanaf nul.
- schalen en afstemmen van het gedrag van bestaande uitgaande NAT. 

Met uitgaande regels u het controle hebben over:
- welke virtuele machines moeten worden vertaald naar welke openbare IP-adressen. 
- hoe [uitgaande SNAT-poorten](load-balancer-outbound-connections.md#snat) moeten worden toegewezen.
- voor welke protocollen u uitgaande vertalingen moet leveren.
- welke duur te gebruiken voor uitgaande verbinding idle time-out (4-120 minuten).
- of u een TCP Reset wilt verzenden bij een niet-actieve time-out

Uitgaande regels breiden [scenario 2](load-balancer-outbound-connections.md#lb) uit in beschreven in het artikel voor [uitgaande verbindingen](load-balancer-outbound-connections.md) en de scenariovoorrang blijft zoals is.

## <a name="outbound-rule"></a>Uitgaande regel

Net als alle regels voor load balancer volgen uitgaande regels dezelfde bekende syntaxis als load balancing en inbound NAT-regels:

**frontend** + **parameters** + **backend pool**

Een uitgaande regel configureert uitgaande NAT voor _alle virtuele machines die zijn geïdentificeerd door de backendpool_ die moet worden vertaald naar de _frontend._  En _parameters_ bieden extra fijnkorrelige controle over het uitgaande NAT-algoritme.

API-versie "2018-07-01" maakt een uitgaande regeldefinitie als volgt gestructureerd mogelijk:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>De effectieve uitgaande NAT-configuratie is een samenstelling van alle uitgaande regels en regels voor het balanceren van de lasten. Uitgaande regels zijn incrementeel naar regels voor het balanceren van de belasting. Controleer [het uitschakelen van uitgaande NAT voor een regel voor het balanceren van de last](#disablesnat) om de effectieve uitgaande NAT-vertaling te beheren wanneer er meerdere regels van toepassing zijn op een vm. U moet [uitgaande SNAT uitschakelen](#disablesnat) bij het definiëren van een uitgaande regel die hetzelfde openbare IP-adres gebruikt als regel voor het balanceren van de last.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Outbound NAT schalen met meerdere IP-adressen

Hoewel een uitgaande regel kan worden gebruikt met slechts één openbaar IP-adres, verlichten uitgaande regels de configuratielast voor het schalen van uitgaande NAT. U meerdere IP-adressen gebruiken om grootschalige scenario's te plannen en u uitgaande regels gebruiken om [snat-uitputtingsgevoelige](load-balancer-outbound-connections.md#snatexhaust) patronen te beperken.  

Elk extra IP-adres dat door een frontend wordt verstrekt, biedt 64.000 tijdelijke poorten voor Load Balancer om te gebruiken als SNAT-poorten. Terwijl load balancing of inbound NAT-regels één frontend hebben, breidt de uitgaande regel de frontend-notie uit en maakt meerdere frontends per regel mogelijk.  Met meerdere frontends per regel wordt het aantal beschikbare SNAT-poorten vermenigvuldigd met elk openbaar IP-adres en kunnen grote scenario's worden ondersteund.

Bovendien u een [openbaar IP-voorvoegsel](https://aka.ms/lbpublicipprefix) rechtstreeks gebruiken met een uitgaande regel.  Het gebruik van openbaar IP-voorvoegsel biedt eenvoudigere schaling en vereenvoudigde white-listing van stromen die afkomstig zijn van uw Azure-implementatie. U een frontend IP-configuratie configureren binnen de Load Balancer-bron om rechtstreeks naar een openbaar IP-adresvoorvoegsel te verwijzen.  Hierdoor kan Load Balancer exclusieve controle hebben over het openbare IP-voorvoegsel en gebruikt de uitgaande regel automatisch alle openbare IP-adressen in het openbare IP-voorvoegsel voor uitgaande verbindingen.  Elk IP-adres binnen het bereik van het openbare IP-voorvoegsel biedt 64.000 tijdelijke poorten per IP-adres voor Load Balancer om te gebruiken als SNAT-poorten.   

U geen afzonderlijke openbare IP-adresbronnen hebben die zijn gemaakt met het openbare IP-voorvoegsel wanneer u deze optie gebruikt, omdat de regel voor uitgaande volledige controle moet hebben over het openbare IP-voorvoegsel.  Als u meer fijnkorrelige besturingselementen nodig hebt, u afzonderlijke openbare IP-adresbron maken vanuit het openbare IP-voorvoegsel en meerdere openbare IP-adressen afzonderlijk toewijzen aan de voorkant van een uitgaande regel.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>SNAT-poorttoewijzing afstemmen

U uitgaande regels gebruiken om de [automatische SNAT-poorttoewijzing af](load-balancer-outbound-connections.md#preallocatedports) te stemmen op basis van de grootte van de backendpool en meer of minder toe te wijzen dan de automatische SNAT-poorttoewijzing biedt.

Gebruik de volgende parameter om 10.000 SNAT-poorten per VM (NIC IP-configuratie) toe te wijzen.
 

          "allocatedOutboundPorts": 10000

Elk openbaar IP-adres van alle frontends van een uitgaande regel draagt tot 64.000 tijdelijke poorten bij voor gebruik als SNAT-poorten.  Load Balancer wijst SNAT-poorten toe in veelvouden van 8. Als u een waarde opgeeft die niet deelbaar is door 8, wordt de configuratiebewerking geweigerd.  Als u probeert meer SNAT-poorten toe te wijzen dan beschikbaar is op basis van het aantal openbare IP-adressen, wordt de configuratiebewerking geweigerd.  Als u bijvoorbeeld 10.000 poorten per VM toewijst en 7 VM's in een backendpool één openbaar IP-adres zou delen, wordt de configuratie geweigerd (7 x 10.000 SNAT-poorten > 64.000 SNAT-poorten).  U meer openbare IP-adressen toevoegen aan de voorkant van de uitgaande regel om het scenario in te schakelen.

U terugkeren naar [de automatische SNAT-poorttoewijzing op basis](load-balancer-outbound-connections.md#preallocatedports) van de grootte van de backendpool door 0 op te geven voor het aantal poorten. In dat geval krijgen de eerste 50 VM-exemplaren 1024 poorten, 51-100 VM-exemplaren krijgen 512 enzovoort volgens de tabel.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Outbound Flow idle time-out beheren

Uitgaande regels bieden een configuratieparameter om de niet-actieve time-out van uitgaande stroom te beheren en deze af te koppelen aan de behoeften van uw toepassing.  Uitgaande idle time-outs standaard tot 4 minuten.  De parameter accepteert een waarde van 4 tot 120 tot specifieke het aantal minuten voor de niet-actieve time-out voor stromen die overeenkomen met deze specifieke regel.

Gebruik de volgende parameter om de uitgaande idle time-out in te stellen op 1 uur:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a> TCP-reset inschakelen bij inactieve time-out

Het standaardgedrag van Load Balancer is om de stroom stil te laten vallen wanneer de uitgaande idle time-out is bereikt.  Met de parameter enableTCPReset u een voorspelbaarder toepassingsgedrag inschakelen en bepalen of u bidirectionele TCP Reset (TCP RESET) wilt verzenden op de tijd van uitgaande idle time-out. 

Gebruik de volgende parameter om TCP Reset in te schakelen op een uitgaande regel:

           "enableTcpReset": true

Controleer [TCP Reset op idle time-out](https://aka.ms/lbtcpreset) voor details, inclusief beschikbaarheid van de regio.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Ondersteuning voor zowel TCP- als UDP-transportprotocollen met één regel

U zult waarschijnlijk All willen gebruiken voor het transportprotocol van de uitgaande regel, maar u de uitgaande regel ook toepassen op een specifiek transportprotocol als dit nodig is.

Gebruik de volgende parameter om het protocol in te stellen op TCP en UDP:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Uitgaande NAT uitschakelen voor een regel voor het balanceren van de last

Zoals eerder vermeld, bieden regels voor taakverdeling automatische programmering van uitgaande NAT. Sommige scenario's profiteren er echter van of vereisen dat u de automatische programmering van uitgaande NAT uitschakelt door de regel voor taakverdeling, zodat u het gedrag beheren of verfijnen.  Uitgaande regels hebben scenario's waarin het belangrijk is om de automatische uitgaande NAT-programmering te stoppen.

U deze parameter op twee manieren gebruiken:
- Optionele onderdrukking van het gebruik van het binnenkomende IP-adres voor uitgaande NAT.  Uitgaande regels zijn incrementeel voor regelverdelingsregels en met deze parameterset heeft de regel voor uitgaande regels de controle.
  
- Stem de uitgaande NAT-parameters af van een IP-adres dat tegelijkertijd wordt gebruikt voor binnenkomend en uitgaand.  De automatische uitgaande NAT-programmering moet worden uitgeschakeld om een uitgaande regel de controle te laten overnemen.  Om bijvoorbeeld de SNAT-poorttoewijzing te wijzigen van een adres dat ook voor binnenkomenwordt gebruikt, moet deze parameter worden ingesteld op true.  Als u een uitgaande regel probeert te gebruiken om de parameters van een IP-adres te herdefiniëren dat ook wordt gebruikt voor binnenkomende en geen uitgaande NAT-programmering van de regel voor taakverdeling hebt vrijgegeven, mislukt de bewerking om een uitgaande regel te configureren.

>[!IMPORTANT]
> Uw virtuele machine heeft geen uitgaande connectiviteit als u deze parameter op true instelt en geen uitgaande regel hebt (of [openbaar IP-scenario op instantieniveau](load-balancer-outbound-connections.md#ilpip) om uitgaande connectiviteit te definiëren.  Sommige bewerkingen van uw vm of uw toepassing zijn mogelijk afhankelijk van het beschikbaar hebben van uitgaande connectiviteit. Zorg ervoor dat u de afhankelijkheden van uw scenario begrijpt en de impact van het aanbrengen van deze wijziging hebt overwogen.

Met deze configuratieparameter u uitgaande SNAT uitschakelen op de regel voor taakverdeling:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

De parameter disableOutboundSNAT defaults to false, wat betekent dat de **regel** voor taakverdeling automatische uitgaande NAT biedt als spiegelbeeld van de configuratie van de regelvan de taakverdeling.  

Als u OutboundSnat op true instelt op de regel voor taakverdeling, geeft de regel voor taakverdeling de controle vrij over de anders automatische uitgaande NAT-programmering.  Uitgaande SNAT als gevolg van de regel voor taakverdeling is uitgeschakeld.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Bestaande bestaande backendpools opnieuw gebruiken of nieuwe backendpools definiëren

Uitgaande regels introduceren geen nieuw concept voor het definiëren van de groep VM's waarop de regel van toepassing zou moeten zijn.  In plaats daarvan hergebruiken ze het concept van een backendpool, die ook wordt gebruikt voor regels voor het balanceren van de lasten. U dit gebruiken om de configuratie te vereenvoudigen door een bestaande backendpooldefinitie opnieuw te gebruiken of er een te maken die specifiek voor een uitgaande regel is.

## <a name="scenarios"></a>Scenario's

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Uitgaande verbindingen verzorgen met een specifieke set openbare IP-adressen

U een uitgaande regel gebruiken om uitgaande verbindingen te verzorgen die lijken te zijn afkomstig van een specifieke set openbare IP-adressen om whitelisting-scenario's te vergemakkelijken.  Dit openbare IP-adres van de bron kan hetzelfde zijn als gebruikt door een regel voor taakverdeling of een andere set openbare IP-adressen dan die wordt gebruikt door een regel voor taakverdeling.  

1. Openbaar [IP-voorvoegsel](https://aka.ms/lbpublicipprefix) (of openbare IP-adressen maken vanaf openbaar IP-voorvoegsel)
2. Een openbare Standard Load Balancer maken
3. Frontends maken die verwijzen naar het openbare IP-voorvoegsel (of openbare IP-adressen) dat u wilt gebruiken
4. Een backendpool opnieuw gebruiken of een backendpool maken en de VM's in een backendpool van de openbare Load Balancer plaatsen
5. Een uitgaande regel configureren op de openbare Load Balancer om uitgaande NAT voor deze VM's te programmeren met behulp van de frontends
   
Als u niet wilt dat de regel voor het balanceren van de last en de regel voor uitgaande regels wordt gebruikt, moet u [uitgaande SNAT uitschakelen](#disablesnat) op de regel voor het balanceren van de last.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>SNAT-poorttoewijzing wijzigen

U uitgaande regels gebruiken om de [automatische SNAT-poorttoewijzing](load-balancer-outbound-connections.md#preallocatedports)af te stemmen op basis van de grootte van de backendpool.

Als u bijvoorbeeld twee virtuele machines hebt die één openbaar IP-adres delen voor uitgaande NAT, u het aantal SNAT-poorten dat is toegewezen vanaf de standaard 1024-poorten verhogen als u SNAT-uitputting ondervindt. Elk openbaar IP-adres kan tot 64.000 tijdelijke poorten bijdragen.  Als u een uitgaande regel configureert met één openbare IP-adresfrontend, u in totaal 64.000 SNAT-poorten distribueren naar VM's in de backendpool.  Voor twee VM's kunnen maximaal 32.000 SNAT-poorten worden toegewezen met een uitgaande regel (2x 32.000 = 64.000).

Bekijk [uitgaande verbindingen](load-balancer-outbound-connections.md) en de details over hoe [SNAT-poorten](load-balancer-outbound-connections.md#snat) worden toegewezen en gebruikt.

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Alleen uitgaand inschakelen

U een openbare Standaardload Balancer gebruiken om uitgaande NAT te bieden voor een groep VM's. In dit scenario u zelf een uitgaande regel gebruiken, zonder dat er aanvullende regels nodig zijn.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Uitgaande NAT alleen voor VM's (geen binnenkomende)

Definieer een openbare StandaardLoad Balancer, plaats de VM's in de backendpool en configureer een uitgaande regel om uitgaande NAT te programmeren en de uitgaande verbindingen te verzorgen om afkomstig te zijn van een specifiek openbaar IP-adres. U ook een openbaar IP-voorvoegsel gebruiken om de bron van uitgaande verbindingen te vereenvoudigen.

1. Maak een openbare Standaard Load Balancer.
2. Maak een backend pool en plaats de VM's in een backend pool van de openbare Load Balancer.
3. Configureer een uitgaande regel op de openbare Load Balancer om uitgaande NAT voor deze VM's te programmeren.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Uitgaande NAT voor interne standaardloadbalancerscenario's

Bij het gebruik van een interne Standaard Load Balancer is uitgaande NAT pas beschikbaar als uitgaande connectiviteit expliciet is gedeclareerd. Met de volgende stappen u uitgaande connectiviteit definiëren met behulp van een uitgaande regel om uitgaande connectiviteit voor VM's te maken achter een interne standaardloadbalancer:

1. Maak een openbare Standaard Load Balancer.
2. Maak een backend pool en plaats de VM's in een backend pool van de openbare Load Balancer naast de interne Load Balancer.
3. Configureer een uitgaande regel op de openbare Load Balancer om uitgaande NAT voor deze VM's te programmeren.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Beide TCP-& UDP-protocollen inschakelen voor uitgaande NAT met een openbare standaardloadbalansr

- Bij het gebruik van een openbare Standaard Load Balancer komt de automatische uitgaande NAT-programmering overeen met het transportprotocol van de regel voor het balanceren van de lasten.  

   1. Schakel uitgaande SNAT uit op de regel voor taakverdeling.
   2. Configureer een uitgaande regel op dezelfde load balancer.
   3. Gebruik de backendpool die al door uw VM's wordt gebruikt.
   4. Geef 'protocol': 'Alles' op als onderdeel van de regel uitgaan.

- Wanneer alleen inkomende NAT-regels worden gebruikt, wordt er geen uitgaande NAT verstrekt.

   1. Plaats VM's in een backend pool.
   2. Definieer een of meer frontend IP-configuraties met openbare IP-adres(en) of openbaar IP-voorvoegsel.
   3. Configureer een uitgaande regel op dezelfde load balancer.
   4. "protocol" opgeven: "Alles" als onderdeel van de regel outbound

## <a name="limitations"></a>Beperkingen

- Het maximum aantal bruikbare efemere poorten per frontend IP-adres is 64.000.
- Het bereik van de configureerbare uitgaande idle time-out is 4 tot 120 minuten (240 tot 7200 seconden).
- Load Balancer ondersteunt geen ICMP voor uitgaande NAT.
- Uitgaande regels kunnen alleen worden toegepast op de primaire IP-configuratie van een NIC.  Meerdere NIC's worden ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik [van Load Balancer voor uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Meer informatie over [bidirectionele TCP Reset bij inactieve time-out](load-balancer-tcp-reset.md).
- [Uitgaande regels configureren met Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
