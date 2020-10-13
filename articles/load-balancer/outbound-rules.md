---
title: Azure Load Balancer voor uitgaande regels
description: In dit artikel wordt uitgelegd hoe u regels voor uitgaande verbindingen configureert voor het beheren van de uitgaand verkeer van Internet met Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002641"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Azure Load Balancer voor uitgaande regels

Met uitgaande regels kunt u de open bare standaard load balancer uitgaande SNAT (bron Network Address Translation) configureren. Met deze configuratie kunt u de open bare IP ('s) van uw load balancer als proxy gebruiken.

Deze configuratie maakt het volgende mogelijk:

* IP-masker
* Vereenvoudig uw acceptatie lijsten.
* Hiermee wordt het aantal open bare IP-resources voor implementatie verminderd.

Met uitgaande regels hebt u volledige declaratieve controle over uitgaande internet connectiviteit. Met uitgaande regels kunt u deze mogelijkheid schalen en afstemmen op uw specifieke behoeften. 

Uitgaande regels worden alleen gevolgd als de back-end-VM geen openbaar IP-adres op exemplaar niveau (ILPIP) heeft.

![Load Balancer uitgaande regels](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Met uitgaande regels kunt u het gedrag voor uitgaande **SNAT** expliciet definiëren.

Met uitgaande regels kunt u het volgende beheren:

* **Welke virtuele machines worden omgezet naar de open bare IP-adressen.**
     * Twee regels zijn back-end-pool A maakt gebruik van IP-adres A en B. back-end-pool B gebruikt IP-adres C en D.
* **Hoe uitgaand SNAT-poorten worden gegeven.**
     * Back-end-groep B is de enige groep die uitgaande verbindingen maakt, alle SNAT-poorten naar back-end-groep B en geen naar back-end-groep A heeft.
* **De protocollen waarvoor een uitgaande vertaling moet worden geboden.**
     * Back-end-pool B heeft UDP-poorten voor uitgaand verkeer nodig. Back-end-groep A vereist TCP. Geef TCP-poorten naar een UDP-poort aan B.
* **Welke duur moet worden gebruikt voor de time-out voor uitgaande verbindingen (4-120 minuten).**
     * Als er langlopende verbindingen met keepalives zijn, reserveert u niet-actieve poorten voor langlopende verbindingen gedurende 120 minuten. Aannemen dat verouderde verbindingen in 4 minuten worden afgebroken en de poorten voor nieuwe verbindingen worden vrijgegeven 
* **Hiermee wordt aangegeven of een TCP Reset moet worden verzonden bij een time-out voor inactiviteit.**
     * Wanneer er een time-out optreedt voor niet-actieve verbindingen, sturen we een eerste TCP-client naar de klant en server zodat ze weten dat de stroom wordt afgebroken?

## <a name="outbound-rule-definition"></a>Definitie van uitgaande regel

Uitgaande regels volgen dezelfde vertrouwde syntaxis als taak verdeling en binnenkomende NAT-regels: **frontend**-  +  **para meters**  +  **back-end-pool**. 

Met een uitgaande regel wordt uitgaande NAT geconfigureerd voor _alle virtuele machines die door de back-endadresgroep worden geïdentificeerd_ om te worden vertaald naar de front- _End_.  

De _para meters_ bieden extra nauw keurige controle over de uitgaande NAT-algoritme.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Uitgaand NAT schalen met meerdere IP-adressen

Elk extra IP-adres dat wordt geleverd door een front-end biedt extra 64.000 tijdelijke poorten voor load balancer die als SNAT-poorten kunnen worden gebruikt. 

Gebruik meerdere IP-adressen voor het plannen van grootschalige scenario's. Gebruik regels voor uitgaande verbindingen om de [SNAT-uitputting](troubleshoot-outbound-connection.md#snatexhaust)te verhelpen. 

U kunt ook een [openbaar IP-voor voegsel](https://aka.ms/lbpublicipprefix) rechtstreeks met een uitgaande regel gebruiken. 

Een openbaar IP-voor voegsel verg root de schaal van uw implementatie. Het voor voegsel kan worden toegevoegd aan de lijst met toegestane stromen die afkomstig zijn van uw Azure-resources. U kunt een front-end-IP-configuratie in de load balancer configureren om te verwijzen naar een openbaar IP-adres voorvoegsel.  

De load balancer heeft controle over het open bare IP-voor voegsel. Voor de uitgaande regel worden automatisch alle open bare IP-adressen gebruikt die zich in het open bare IP-voor voegsel voor uitgaande verbindingen bevinden. 

Elk van de IP-adressen binnen het open bare IP-voor voegsel biedt een extra 64.000 tijdelijke poorten per IP-adres voor load balancer om te gebruiken als SNAT-poorten.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Uitgaande time-out voor niet-actieve stroom en TCP Reset

Uitgaande regels bieden een configuratie parameter voor het beheren van de uitgaande time-out voor niet-actieve stroom en om deze te vergelijken met de behoeften van uw toepassing. Uitgaande time-outs voor inactiviteit zijn standaard ingesteld op 4 minuten. Zie een [time-out voor inactiviteit configureren](load-balancer-tcp-idle-timeout.md)voor meer informatie. 

Het standaard gedrag van load balancer is om de stroom op de achtergrond neer te zetten wanneer de uitgaande time-out voor inactiviteit is bereikt. De `enableTCPReset` para meter maakt een voorspelbaar toepassings gedrag en-beheer mogelijk. De para meter bepaalt of bidirectionele TCP Reset (TCP RST) moet worden verzonden bij de time-out van de uitgaande time-out voor inactiviteit. 

[TCP Reset controleren bij time-out voor inactiviteit](https://aka.ms/lbtcpreset) voor meer informatie over de beschik baarheid van regio's.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>De uitgaande connectiviteit expliciet beveiligen en beheren

Taakverdelings regels bieden automatische programmering van uitgaande NAT. Sommige scenario's profiteren of vereisen dat u de automatische programmering van uitgaande NAT door de taakverdelings regel uitschakelt. Door via de regel uit te scha kelen, kunt u het gedrag beheren of verfijnen.  

U kunt deze para meter op twee manieren gebruiken:

1. Het inkomende IP-adres voor uitgaande SNAT wordt voor komen. Schakel uitgaande SNAT uit in de taakverdelings regel.
  
2. Stem de uitgaande **SNAT** -para meters af van een IP-adres dat tegelijkertijd wordt gebruikt voor inkomend en uitgaand. De automatische uitgaande NAT moet worden uitgeschakeld zodat een regel voor uitgaand verkeer kan worden beheerd. Als u de toewijzing van de SNAT-poort van een adres dat ook wordt gebruikt voor inkomend, wilt wijzigen, `disableOutboundSnat` moet u de para meter instellen op True. 

De bewerking voor het configureren van een uitgaande regel mislukt als u probeert een IP-adres te definiëren dat wordt gebruikt voor inkomend verkeer.  Schakel eerst de uitgaande NAT van de taakverdelings regel uit.

>[!IMPORTANT]
> De virtuele machine heeft geen uitgaande verbinding als u deze para meter instelt op True en geen uitgaande regel hebt om uitgaande connectiviteit te definiëren.  Sommige bewerkingen van uw virtuele machine of uw toepassing kunnen afhankelijk zijn van een uitgaande verbinding. Zorg ervoor dat u bekend bent met de afhankelijkheden van uw scenario en dat dit gevolgen heeft voor het maken van deze wijziging.

Soms is het niet wenselijk dat een virtuele machine een uitgaande stroom maakt. Er is mogelijk een vereiste voor het beheren van welke doelen uitgaande stromen worden ontvangen of die doelen beginnen met binnenkomende stromen. Gebruik [netwerk beveiligings groepen](../virtual-network/security-overview.md) om de doelen te beheren die de virtuele machine bereikt. Gebruik Nsg's om te beheren welke open bare bestemmingen binnenkomende stromen starten.

Wanneer u een NSG toepast op een virtuele machine met taak verdeling, moet u rekening houden met de [service Tags](../virtual-network/security-overview.md#service-tags) en de [standaard beveiligings regels](../virtual-network/security-overview.md#default-security-rules). 

Zorg ervoor dat de VM Health probe-aanvragen van Azure Load Balancer kan ontvangen.

Als een NSG de status test aanvragen blokkeert vanuit het standaard label AZURE_LOADBALANCER, mislukt de VM-status test en wordt de VM gemarkeerd als niet beschikbaar. De load balancer stopt met het verzenden van nieuwe stromen naar die virtuele machine.

## <a name="limitations"></a>Beperkingen

- Het maximale aantal bruikbare tijdelijke poorten per frontend-IP-adres is 64.000.
- Het bereik van de Configureer bare uitgaande time-out voor inactiviteit is 4 tot 120 minuten (240 tot 7200 seconden).
- De Load Balancer biedt geen ondersteuning voor ICMP voor uitgaande NAT.
- Uitgaande regels kunnen alleen worden toegepast op de primaire IP-configuratie van een NIC.  U kunt geen uitgaande regel maken voor het secundaire IP-adres van een virtuele machine of NVA. Er worden meerdere Nic's ondersteund.
- Alle virtuele machines in een **beschikbaarheidsset** moeten worden toegevoegd aan de back-end-groep voor uitgaande verbindingen. 
- Alle virtuele machines in een **schaalset voor virtuele** machines moeten worden toegevoegd aan de back-end-groep voor uitgaande verbindingen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Standard Load Balancer](load-balancer-overview.md)
- Bekijk onze [Veelgestelde vragen over Azure Load Balancer](load-balancer-faqs.md)

