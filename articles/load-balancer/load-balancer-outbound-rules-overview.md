---
title: Uitgaande regels in Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Uitgaande regels gebruiken voor het definiëren van uitgaande netwerk adres vertalingen
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
ms.openlocfilehash: 9fc9eb347e97fe6ab57b3e30651e4ea77a4ce9c8
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790236"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer uitgaande regels

Azure Load Balancer biedt een uitgaande verbinding van een virtueel netwerk naast binnenkomend.  Met uitgaande regels kunt u de uitgaande Network Address Translation van open bare [Standard Load Balancer](load-balancer-standard-overview.md)eenvoudig configureren.  U hebt volledige declaratieve controle over uitgaande connectiviteit om deze mogelijkheid te schalen en af te stemmen op uw specifieke behoeften.

![Load Balancer uitgaande regels](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Met uitgaande regels kunt u Load Balancer gebruiken voor het volgende: 
- Geef de uitgaande NAT van een geheel nieuwe definitie op.
- het gedrag van bestaande uitgaande NAT schalen en afstemmen. 

Met uitgaande regels kunt u het volgende beheren:
- welke virtuele machines moeten worden vertaald naar de open bare IP-adressen. 
- hoe [uitgaande SNAT-poorten](load-balancer-outbound-connections.md#snat) moeten worden toegewezen.
- de protocollen waarvoor een uitgaande vertaling moet worden geboden.
- welke duur moet worden gebruikt voor de time-out voor uitgaande verbindingen (4-120 minuten).
- Hiermee wordt aangegeven of een TCP Reset moet worden verzonden bij een time-out voor inactiviteit (in open bare preview). 

Uitgaande regels worden [scenario 2](load-balancer-outbound-connections.md#lb) uitvouwen in de beschrijving in het artikel [uitgaande verbindingen](load-balancer-outbound-connections.md) en de prioriteit van het scenario blijft ongewijzigd.

## <a name="outbound-rule"></a>Uitgaande regel

Net als alle Load Balancer regels volgen uitgaande regels dezelfde vertrouwde syntaxis als taak verdeling en binnenkomende NAT-regels:

**frontend** - + **parameters** + **back-end-pool**

Met een uitgaande regel wordt uitgaande NAT geconfigureerd voor _alle virtuele machines die door de back-endadresgroep worden geïdentificeerd_ om te worden vertaald naar de front- _End_.  En _para meters_ bieden extra nauw keurige controle over de uitgaande NAT-algoritme.

API-versie 2018-07-01 geeft de volgende structuur van een uitgaande regel:

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
>De effectief uitgaande NAT-configuratie bestaat uit een combi natie van alle regels voor uitgaande verbindingen en taak verdeling. Uitgaande regels zijn incrementeel voor taakverdelings regels. Bekijk het [uitschakelen van de uitgaande NAT voor een taakverdelings regel](#disablesnat) voor het beheren van de effectief uitgaande NAT-omzetting wanneer meerdere regels van toepassing zijn op een virtuele machine. U moet [uitgaande SNAT uitschakelen](#disablesnat) bij het definiëren van een uitgaande regel die hetzelfde open bare IP-adres gebruikt als een taakverdelings regel.

### <a name="scale"></a>Uitgaand NAT schalen met meerdere IP-adressen

Terwijl een regel voor uitgaande verbindingen met slechts één openbaar IP-adres kan worden gebruikt, wordt door uitgaande regels de configuratie belasting voor het schalen van uitgaande NAT vereenvoudigd. U kunt meerdere IP-adressen gebruiken om te plannen voor grootschalige scenario's en u kunt uitgaande regels gebruiken om te voor komen dat er gevoelige patronen voor de [uitputting van SNAT](load-balancer-outbound-connections.md#snatexhaust) zijn.  

Elk extra IP-adres dat wordt geleverd door een front-end biedt 64.000 tijdelijke poorten voor Load Balancer die als SNAT-poorten kunnen worden gebruikt. Terwijl taak verdeling of binnenkomende NAT-regels één frontend hebben, breidt de uitgaande regel het front-end-principe uit en staat meerdere frontends per regel toe.  Met meerdere frontends per regel wordt het aantal beschik bare SNAT-poorten vermenigvuldigd met elk openbaar IP-adres en kunnen grote scenario's worden ondersteund.

Daarnaast kunt u een [openbaar IP-voor voegsel](https://aka.ms/lbpublicipprefix) rechtstreeks met een uitgaande regel gebruiken.  Met behulp van het voor voegsel openbaar IP-adres kunt u eenvoudig schalen en vereenvoudigd wit-overzicht van stromen die afkomstig zijn van uw Azure-implementatie. U kunt een front-end-IP-configuratie binnen de Load Balancer resource configureren om rechtstreeks naar een open bare IP-adres voorvoegsel te verwijzen.  Hierdoor is Load Balancer exclusieve controle over het open bare IP-voor voegsel en worden alle open bare IP-adressen die zich in het open bare IP-voor voegsel voor uitgaande verbindingen bevinden, automatisch door de uitgaande regel gebruikt.  Elk van de IP-adressen binnen het bereik van het open bare IP-voor voegsel bieden 64.000 tijdelijke poorten per IP-adres om Load Balancer te gebruiken als SNAT-poorten.   

U kunt geen afzonderlijke open bare IP-adres bronnen maken op basis van het open bare IP-voor voegsel wanneer u deze optie gebruikt, omdat de uitgaande regel volledige controle over het open bare IP-voor voegsel moet hebben.  Als u meer nauw keurigere controle nodig hebt, kunt u een afzonderlijke open bare IP-adres bron maken op basis van het open bare IP-voor voegsel en meerdere open bare IP-adressen afzonderlijk aan de frontend van een uitgaande regel toewijzen.

### <a name="snatports"></a>Toewijzing van SNAT-poort afstemmen

U kunt uitgaande regels gebruiken voor het afstemmen [van de automatische toewijzing van de SNAT-poort op basis van de back-endadresgroep](load-balancer-outbound-connections.md#preallocatedports) en toewijzen van meer of minder dan de automatische toewijzing van de SNAT-poort.

Gebruik de volgende para meter om 10.000 SNAT-poorten per virtuele machine toe te wijzen (NIC IP-configuratie).
 

          "allocatedOutboundPorts": 10000

Elk openbaar IP-adres van alle frontends van een uitgaande regel draagt bij aan Maxi maal 64.000 tijdelijke poorten voor gebruik als SNAT-poorten.  Load Balancer wijst SNAT-poorten toe in veelvouden van 8. Als u een waarde opgeeft die niet deelbaar is door 8, wordt de configuratie bewerking geweigerd.  Als u meer SNAT-poorten probeert toe te wijzen dan beschikbaar zijn op basis van het aantal open bare IP-adressen, wordt de configuratie bewerking geweigerd.  Als u bijvoorbeeld 10.000 poorten per VM toewijst en 7 virtuele machines in een back-end-pool een enkel openbaar IP-adres delen, wordt de configuratie geweigerd (7 x 10.000 SNAT-poorten > 64.000 SNAT-poorten).  U kunt meer open bare IP-adressen toevoegen aan de front-end van de regel voor uitgaande verbindingen om het scenario in te scha kelen.

U kunt teruggaan naar de [automatische toewijzing van de SNAT-poort op basis van de grootte](load-balancer-outbound-connections.md#preallocatedports) van de back-endadresgroep door 0 op te geven voor het aantal poorten.

### <a name="idletimeout"></a>Uitgaande time-out voor niet-actieve stroom controleren

Uitgaande regels bieden een configuratie parameter voor het beheren van de uitgaande time-out voor niet-actieve stroom en om deze te vergelijken met de behoeften van uw toepassing.  Uitgaande time-outs voor inactiviteit zijn standaard ingesteld op 4 minuten.  De para meter accepteert een waarde van 4 tot 120 tot en met een specifiek aantal minuten voor de time-out voor inactiviteit voor stromen die overeenkomen met deze specifieke regel.

Gebruik de volgende para meter om de uitgaande time-out voor inactiviteit in te stellen op 1 uur:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a> TCP Reset inschakelen bij time-out voor inactiviteit (preview-versie)

Het standaard gedrag van Load Balancer is om de stroom op de achtergrond neer te zetten wanneer de uitgaande time-out voor inactiviteit is bereikt.  Met de para meter enableTCPReset kunt u een meer voorspel bare toepassings gedrag inschakelen en bepalen of u een bidirectionele TCP Reset (TCP RST) wilt verzenden wanneer de uitgaande time-out voor inactiviteit is verlopen. 

Gebruik de volgende para meter om TCP reset in te scha kelen voor een uitgaande regel:

           "enableTcpReset": true

[TCP Reset controleren bij time-out voor inactiviteit (preview)](https://aka.ms/lbtcpreset) voor meer informatie, inclusief de beschik baarheid van regio's.

### <a name="proto"></a>Ondersteuning voor TCP-en UDP-transport protocollen met één regel

Waarschijnlijk wilt u ' all ' gebruiken voor het transport protocol van de uitgaande regel, maar u kunt ook de uitgaande regel Toep assen op een specifiek Transport Protocol, ook als dat nodig is.

Gebruik de volgende para meter om het protocol in te stellen op TCP en UDP:

          "protocol": "All"

### <a name="disablesnat"></a>Uitgaande NAT uitschakelen voor een taakverdelings regel

Zoals eerder vermeld, bieden de taakverdelings regels automatische programmering van uitgaande NAT. Sommige scenario's profiteren echter of vereisen dat u de automatische programmering van uitgaande NAT door de taakverdelings regel uitschakelt, zodat u het gedrag kunt beheren of verfijnen.  Uitgaande regels hebben scenario's waarbij het belang rijk is om de automatische uitgaande NAT-programmering te stoppen.

U kunt deze para meter op twee manieren gebruiken:
- Optionele onderdrukking van het gebruik van het inkomende IP-adres voor uitgaande NAT.  Uitgaande regels zijn incrementeel voor taakverdelings regels en met deze parameterset, wordt de regel voor uitgaande verbindingen in beheer.
  
- Stem de uitgaande NAT-para meters af van een IP-adres dat tegelijkertijd wordt gebruikt voor inkomend en uitgaand.  De automatische uitgaand NAT-programmering moet worden uitgeschakeld zodat een regel voor uitgaand verkeer kan worden beheerd.  Als u bijvoorbeeld de toewijzing van de SNAT-poort van een adres wilt wijzigen, moet deze para meter worden ingesteld op True.  Als u probeert een regel voor uitgaande verbindingen te gebruiken om de para meters van een IP-adres dat ook wordt gebruikt voor inkomend verkeer opnieuw te definiëren en geen uitgaande NAT-programmering van de taakverdelings regel heeft vrijgegeven, mislukt de bewerking voor het configureren van een uitgaande regel.

>[!IMPORTANT]
> De virtuele machine heeft geen uitgaande verbinding als u deze para meter instelt op True en geen uitgaande regel (of [openbaar IP-scenario op exemplaar niveau](load-balancer-outbound-connections.md#ilpip) hebt om uitgaande connectiviteit te definiëren.  Sommige bewerkingen van uw virtuele machine of uw toepassing kunnen afhankelijk zijn van een uitgaande verbinding. Zorg ervoor dat u bekend bent met de afhankelijkheden van uw scenario en dat dit gevolgen heeft voor het maken van deze wijziging.

U kunt uitgaande SNAT uitschakelen op de taakverdelings regel met deze configuratie parameter:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

De para meter Disableoutboundsnat toegevoegd **wordt** standaard ingesteld op False, wat betekent dat de taakverdelings regel automatische uitgaande NAT biedt als spiegel beeld van de configuratie van de taakverdelings regel.  

Als u Disableoutboundsnat toegevoegd instelt op True voor de taakverdelings regel, geeft de taakverdelings regel de controle over de andere automatische uitgaande NAT-programmering.  Uitgaande SNAT als gevolg van de taakverdelings regel is uitgeschakeld.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Bestaande back-end-Pools opnieuw gebruiken of definiëren

Uitgaande regels introduceren geen nieuw concept voor het definiëren van de groep Vm's waarop de regel van toepassing moet zijn.  In plaats daarvan wordt het concept van een back-end-pool hergebruikt, dat ook wordt gebruikt voor taakverdelings regels. U kunt dit gebruiken om de configuratie te vereenvoudigen door een bestaande back-end-groeps definitie opnieuw te gebruiken of een specifiek voor een uitgaande regel te maken.

## <a name="scenarios"></a>Scenario's

### <a name="groom"></a>Uitgaande verbindingen met een specifieke set open bare IP-adressen opschonen

U kunt een uitgaande regel voor het opschonen van uitgaande verbindingen gebruiken om aan te geven dat ze afkomstig zijn uit een specifieke set open bare IP-adressen om white list-scenario's te vereenvoudigen.  Het open bare IP-adres van de bron kan hetzelfde zijn als het gebruik van een taakverdelings regel of een andere set open bare IP-adressen dan wordt gebruikt door een taakverdelings regel.  

1. [Openbaar IP-voor voegsel](https://aka.ms/lbpublicipprefix) (of open bare IP-adressen van open bare IP-voor voegsel) maken
2. Een openbare Standard Load Balancer maken
3. Front-end maken die verwijst naar het open bare IP-voor voegsel (of open bare IP-adressen) die u wilt gebruiken
4. Hergebruik een back-end-pool of maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer
5. Een uitgaande regel voor de open bare Load Balancer configureren voor het Program meren van uitgaande NAT voor deze Vm's met de frontends
   
Als u niet wilt dat de taakverdelings regel voor uitgaand verkeer wordt gebruikt, moet u [uitgaand SNAT uitschakelen](#disablesnat) op de taakverdelings regel.

### <a name="modifysnat"></a>Toewijzing van SNAT-poort wijzigen

U kunt uitgaande regels gebruiken voor het afstemmen van de [automatische toewijzing van de SNAT-poort op basis van de back-endadresgroep](load-balancer-outbound-connections.md#preallocatedports).

Als u bijvoorbeeld twee virtuele machines hebt die één openbaar IP-adres voor uitgaande NAT delen, kunt u het aantal toegewezen SNAT-poorten van de standaard 1024-poorten verhogen als u de SNAT-uitputting ondervindt. Elk openbaar IP-adres kan bijdragen aan Maxi maal 64.000 tijdelijke poorten.  Als u een uitgaande regel configureert met één openbaar IP-adres front-end, kunt u een totaal van 64.000 SNAT-poorten distribueren naar Vm's in de back-end-pool.  Voor twee virtuele machines kunnen Maxi maal 32.000 SNAT-poorten worden toegewezen met een uitgaande regel (2x 32.000 = 64.000).

Controleer de [uitgaande verbindingen](load-balancer-outbound-connections.md) en de details over de manier waarop de [SNAT](load-balancer-outbound-connections.md#snat) -poorten worden toegewezen en gebruikt.

### <a name="outboundonly"></a>Alleen uitgaand verkeer inschakelen

U kunt een open bare Standard Load Balancer gebruiken om uitgaande NAT te bieden voor een groep Vm's. In dit scenario kunt u zelf een regel voor uitgaande verbindingen gebruiken, zonder dat u extra regels nodig hebt.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Alleen uitgaand NAT voor Vm's (geen inkomend)

Definieer een open bare Standard Load Balancer, plaats de virtuele machines in de back-end-pool en configureer een uitgaande regel voor het Program meren van uitgaande NAT en schoon de uitgaande verbindingen op die afkomstig zijn van een specifiek openbaar IP-adres. U kunt ook een openbaar IP-voor voegsel gebruiken dat is vereenvoudigd wit: de bron van uitgaande verbindingen weer geven.

1. Maak een open bare Standard Load Balancer.
2. Maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer.
3. Een uitgaande regel configureren op de open bare Load Balancer om uitgaande NAT voor deze Vm's te Program meren.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Uitgaande NAT voor interne Standard Load Balancer scenario's

Wanneer u een interne Standard Load Balancer gebruikt, is uitgaand NAT niet beschikbaar totdat de uitgaande connectiviteit expliciet is gedeclareerd. Met de volgende stappen kunt u uitgaande connectiviteit definiëren met behulp van een uitgaande regel voor het maken van uitgaande connectiviteit voor Vm's achter een interne Standard Load Balancer:

1. Maak een open bare Standard Load Balancer.
2. Maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer naast de interne Load Balancer.
3. Een uitgaande regel configureren op de open bare Load Balancer om uitgaande NAT voor deze Vm's te Program meren.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>TCP & UDP-protocollen inschakelen voor uitgaande NAT met een openbaar Standard Load Balancer

- Wanneer u een open bare Standard Load Balancer gebruikt, komt de automatische uitgaande NAT-programmering die overeenkomt met het transport protocol van de taakverdelings regel.  

   1. Schakel uitgaande SNAT uit op de taakverdelings regel.
   2. Een uitgaande regel configureren op hetzelfde Load Balancer.
   3. Gebruik de back-end-groep die al door uw virtuele machines wordt gebruikt.
   4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel.

- Wanneer alleen binnenkomende NAT-regels worden gebruikt, wordt er geen uitgaand NAT-verkeer gegeven.

   1. Virtuele machines in een back-end-groep plaatsen.
   2. Definieer een of meer frontend-IP-configuraties met een openbaar IP-adres (sen) of een openbaar IP-voor voegsel.
   3. Een uitgaande regel configureren op hetzelfde Load Balancer.
   4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel

## <a name="limitations"></a>Beperkingen

- Het maximale aantal bruikbare tijdelijke poorten per frontend-IP-adres is 64.000.
- Het bereik van de Configureer bare uitgaande time-out voor inactiviteit is 4 tot 120 minuten (240 tot 7200 seconden).
- Load Balancer biedt geen ondersteuning voor ICMP voor uitgaande NAT.
- De portal kan niet worden gebruikt om uitgaande regels te configureren of weer te geven.  Gebruik in plaats daarvan sjablonen, REST API, AZ CLI 2,0 of Power shell.
- Uitgaande regels kunnen alleen worden toegepast op de primaire IP-configuratie van een NIC.  Er worden meerdere Nic's ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik [van Load Balancer voor uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Meer informatie over [BIDIRECTIONELE TCP Reset bij time-out voor inactiviteit](load-balancer-tcp-reset.md).
- [Configureer regels voor uitgaande verbindingen met Azure CLI 2,0](configure-load-balancer-outbound-cli.md).
