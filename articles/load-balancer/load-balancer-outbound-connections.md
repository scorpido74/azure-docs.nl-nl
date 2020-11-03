---
title: Azure Load Balancer van uitgaande proxy
description: Beschrijft hoe Azure Load Balancer wordt gebruikt als proxy voor uitgaande internet connectiviteit
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 185bb47677e978a3098f39024995da6399f90658
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241766"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Azure Load Balancer van uitgaande proxy

Een Azure-load balancer kan worden gebruikt als een proxy voor uitgaande Internet verbinding. De load balancer biedt de uitgaande verbinding voor de back-end-exemplaren. 

Deze configuratie maakt gebruik van **bron Network Address Translation (SNAT)** . SNAT schrijft het IP-adres van de back-end naar het open bare IP-adres van uw load balancer. 

SNAT maakt **IP-maskering** van het back-end-exemplaar mogelijk. Hierdoor wordt voor komen dat externe bronnen een direct adres hebben voor de back-end-exemplaren. 

Het delen van een IP-adres tussen back-end-instanties reduceert de kosten van statische open bare Ip's en ondersteunt scenario's zoals het vereenvoudigen van IP-lijsten met verkeer van bekende open bare Ip's. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Poorten delen via resources

Als de back-end-bronnen van een load balancer geen ILPIP-adressen op exemplaar niveau hebben, wordt er een uitgaande verbinding tot stand gebracht via het frontend-IP-adres van de open bare load balancer.

Poorten worden gebruikt voor het genereren van unieke id's die worden gebruikt voor het onderhouden van afzonderlijke stromen. Het Internet maakt gebruik van een vijf tuple om dit onderscheid te bieden.

De vijf-tuple bestaat uit:

* Doel-IP
* Doelpoort
* Bron-IP
* Bron poort en-protocol om dit onderscheid te bieden.

Als een poort wordt gebruikt voor binnenkomende verbindingen, heeft deze een **listener** voor binnenkomende verbindings aanvragen op die poort en kan deze niet worden gebruikt voor uitgaande verbindingen. 

Als u een uitgaande verbinding wilt maken, moet u een **tijdelijke poort** gebruiken om de bestemming aan te bieden met een poort waarop u een afzonderlijke verkeers stroom wilt communiceren en onderhouden. 

Elk IP-adres heeft 65.535 poorten. De eerste 1024-poorten zijn gereserveerd als **systeem poorten** . Elke poort kan worden gebruikt voor binnenkomende of uitgaande verbindingen voor TCP en UDP. 

Van de resterende poorten biedt Azure 64.000 voor gebruik als **tijdelijke poorten** . Wanneer een IP-adres wordt toegevoegd als een front-end-IP-configuratie, kunnen deze tijdelijke poorten worden gebruikt voor SNAT.

Via uitgaande regels kunnen deze SNAT-poorten worden gedistribueerd naar back-end-exemplaren zodat ze de open bare IP ('s) van de load balancer voor uitgaande verbindingen delen.

Netwerken op de host voor elk back-end-exemplaar doen SNAT naar pakketten die deel uitmaken van een uitgaande verbinding. De host herschrijft het bron-IP-adres naar een van de open bare Ip's. De host herschrijft de bron poort van elk uitgaand pakket naar een van de SNAT-poorten.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Poorten uitgeputen

Elke verbinding met hetzelfde doel-IP-adres en deze doel poort maakt gebruik van een SNAT-poort. Deze verbinding houdt een afzonderlijke **verkeers stroom** bij van het back-end-exemplaar of de **client** naar een **Server** . Dit proces geeft de server een afzonderlijke poort waarop verkeer kan worden geadresseerd. Zonder dit proces is de client computer niet op de hoogte van welke stroom een pakket deel uitmaakt.

Stel dat u meerdere browsers gaat https://www.microsoft.com :

* Doel-IP = 23.53.254.142
* Doel poort = 443
* Protocol = TCP

Zonder verschillende doel poorten voor het retour verkeer (de SNAT-poort die wordt gebruikt om de verbinding tot stand te brengen), heeft de client geen enkele manier om één query resultaat van een andere te scheiden.

Uitgaande verbindingen kunnen burst zijn. Aan een back-end-exemplaar kunnen onvoldoende poorten worden toegewezen. Als het **opnieuw gebruiken** van de verbinding is ingeschakeld, wordt het risico van de SNAT- **poort uitputting** verhoogd.

Nieuwe uitgaande verbindingen met een doel-IP mislukken als er een poort uitvalt. Verbindingen worden uitgevoerd wanneer een poort beschikbaar wordt. Deze uitputting treedt op wanneer de 64.000-poorten van een IP-adres dun zijn verdeeld over veel backend-exemplaren. Raadpleeg de [hand leiding](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)voor het oplossen van problemen voor meer informatie over het beperken van de uitputting van de SNAT-poort.  

Voor TCP-verbindingen gebruikt de load balancer één SNAT-poort voor elke doel-IP en poort. Met deze Multiuse kunnen meerdere verbindingen met dezelfde doel-IP worden met dezelfde SNAT-poort. Deze Multiuse is beperkt als de verbinding niet is met verschillende doel poorten.

Voor UDP-verbindingen gebruikt de load balancer een door de **poort beperkte kegel-NAT** -algoritme, die een SNAT-poort per doel-IP gebruikt, ongeacht de doel poort. 

Een poort wordt opnieuw gebruikt voor een onbeperkt aantal verbindingen. De poort wordt alleen opnieuw gebruikt als het doel-IP of de poort verschilt.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Poort toewijzing

Elk openbaar IP-adres dat is toegewezen als frontend-IP van uw load balancer krijgt 64.000 SNAT-poorten voor de back-endadresgroep. Poorten kunnen niet worden gedeeld met leden van de back-endadresgroep-groep. Een bereik aan SNAT-poorten kan alleen worden gebruikt door één back-end-instantie om ervoor te zorgen dat retour pakketten correct worden gerouteerd. 

U kunt het beste een expliciete uitgaande regel gebruiken om de toewijzing van de SNAT-poort te configureren. Met deze regel wordt het aantal SNAT-poorten voor elk back-end-exemplaar dat beschikbaar is voor uitgaande verbindingen, gemaximaliseerd. 

Als u de automatische toewijzing van uitgaande SNAT via een taakverdelings regel gebruikt, wordt in de toewijzings tabel uw poort toewijzing gedefinieerd.

In de volgende <a name="snatporttable"></a> tabel ziet u de SNAT-poort voortoewijzingen voor lagen van back-endadresgroep-Pools:

| Pool grootte (VM-exemplaren) | Vooraf toegewezen SNAT-poorten per IP-configuratie |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 | 

>[!NOTE]
> Als u een back-end-groep hebt met een maximale grootte van 10, kan elke instantie 64000/10 = 6.400 poorten bevatten als u een expliciete uitgaande regel definieert. Volgens de bovenstaande tabel is er slechts 1.024 als u automatische toewijzing kiest.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Uitgaande regels en Virtual Network NAT

Azure Load Balancer regels voor uitgaande verbindingen en Virtual Network NAT zijn opties beschikbaar voor het afbreken van een virtueel netwerk.

Zie voor meer informatie over uitgaande regels [Uitgaande regels](outbound-rules.md).

Zie [Wat is azure Virtual Network NAT](../virtual-network/nat-overview.md)? voor meer informatie over Azure Virtual Network nat.

## <a name="constraints"></a>Beperkingen

*   Poorten worden na 15 seconden vrijgegeven als een **eerste TCP** -bericht wordt ontvangen of verzonden
*   Poorten worden na 240 seconden vrijgegeven als een **FINACK** wordt ontvangen of verzonden
*   Wanneer een verbinding niet-actief is zonder dat er nieuwe pakketten worden verzonden, worden de poorten na 4 tot 120 minuten vrijgegeven.
  * Deze drempel waarde kan worden geconfigureerd via uitgaande regels.
*   Elk IP-adres biedt 64.000 poorten die kunnen worden gebruikt voor SNAT.
*   Elke poort kan worden gebruikt voor TCP-en UDP-verbindingen met een doel-IP-adres
  * Er is een UDP SNAT-poort nodig, ongeacht of de doel poort uniek is of niet. Voor elke UDP-verbinding met een bestemmings-IP wordt één UDP SNAT-poort gebruikt.
  * Een TCP SNAT-poort kan worden gebruikt voor meerdere verbindingen met dezelfde doel-IP als de doel poorten verschillen.
*   SNAT-uitputting treedt op wanneer een back-end-exemplaar wordt uitgevoerd met de opgegeven SNAT-poorten. Een load balancer kan nog steeds ongebruikte SNAT-poorten hebben. Als de gebruikte SNAT-poorten van een back-end-exemplaar de opgegeven SNAT-poorten overschrijden, kunnen er geen nieuwe uitgaande verbindingen tot stand worden gebracht.

## <a name="next-steps"></a>Volgende stappen

*   [Problemen met uitgaande verbindingen oplossen vanwege een SNAT-uitputting](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Bekijk de metrische gegevens](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) over de SNAT en lees hoe u deze kunt filteren, splitsen en bekijken.

