---
title: Diagnostische gegevens met metrische gegevens, waarschuwingen en resource status-Azure Standard Load Balancer
description: Gebruik de beschik bare metrische gegevens, waarschuwingen en informatie over de resource status om uw Azure-Standard Load Balancer te diagnosticeren.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: ff42c6e9bd3c25721d2b77e49c2dd98a3eebdb43
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048731"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnose van Standard Load Balancer met metrische gegevens, meldingen en status van resources

Azure Standard Load Balancer maakt de volgende diagnostische mogelijkheden beschikbaar:

* **Multi-dimensionale metrische gegevens en waarschuwingen**: voorziet in nieuwe multi-dimensionale diagnostische mogelijkheden via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) voor standaard Load Balancer configuraties. U kunt uw standaard load balancer-resources bewaken, beheren en problemen oplossen.

* **Resource status**: de Load Balancer pagina in de Azure Portal en de resource Health pagina (onder monitor) geven de sectie Resource Health voor Standard Load Balancer zichtbaar. 

Dit artikel bevat een korte rond leiding door deze mogelijkheden en biedt manieren om ze te gebruiken voor Standard Load Balancer.

## <a name = "MultiDimensionalMetrics"></a>Multi-dimensionale metrische gegevens

Azure Load Balancer voorziet in nieuwe multidimensionale metrische gegevens via de nieuwe metrische gegevens van Azure in de Azure Portal en helpt u bij het gebruik van real-time diagnose inzichten in uw load balancer-resources. 

De verschillende Standard Load Balancer configuraties bieden de volgende metrische gegevens:

| Gegevens | Resourcetype | Beschrijving | Aanbevolen aggregatie |
| --- | --- | --- | --- |
| Beschik baarheid van gegevenspad (VIP-Beschik baarheid)| Open bare en interne load balancer | Standard Load Balancer doorlopend het gegevenspad vanuit een gebied naar de load balancer front-end, die helemaal naar de SDN-stack gaat die uw virtuele machine ondersteunt. Zolang de gezonde instanties blijven bestaan, volgt de meting hetzelfde pad als het verkeer met gelijke taak verdeling van uw toepassing. Het gegevenspad dat door uw klanten wordt gebruikt, wordt ook gevalideerd. De meting is onzichtbaar voor uw toepassing en heeft geen invloed op andere bewerkingen.| Average |
| Health probe status (DIP-Beschik baarheid) | Open bare en interne load balancer | Standard Load Balancer maakt gebruik van een gedistribueerde status-probing-service die de status van uw toepassings eindpunt bewaakt volgens de configuratie-instellingen. Deze metriek levert een gefilterde weer gave van een aggregatie of per eind punt van elk eind punt van de instantie in de load balancer groep. U kunt zien hoe Load Balancer de status van uw toepassing weergeeft, zoals wordt aangegeven door de configuratie van uw Health probe. |  Average |
| SYN-pakketten (synchroniseren) | Open bare en interne load balancer | Standard Load Balancer beëindigt Transmission Control Protocol (TCP)-verbindingen of communiceert niet met TCP-of UDP-pakket stromen. Stromen en hun hand shakes zijn altijd tussen de bron-en de VM-instantie. Voor een betere probleem oplossing van uw TCP-protocol scenario's kunt u de tellers van SYN-pakketten gebruiken om te begrijpen hoeveel TCP-verbindings pogingen er worden gedaan. De metriek rapporteert het aantal TCP SYN-pakketten dat is ontvangen.| Average |
| SNAT-verbindingen | Open bare load balancer |Standard Load Balancer rapporteert het aantal uitgaande stromen dat is gemaskerd voor de front-end van het open bare IP-adres. De bron Network Address Translation SNAT-poorten zijn een exhaustible-bron. Met deze metriek kan worden aangegeven hoe sterk uw toepassing vertrouwt op SNAT voor uitgaande stroom stromen. Tellers voor geslaagde en mislukte uitgaande SNAT-stromen worden gerapporteerd en kunnen worden gebruikt om problemen op te lossen en de status van uw uitgaande stromen te begrijpen.| Average |
| Byte tellers |  Open bare en interne load balancer | Standard Load Balancer rapporteert de verwerkte gegevens per front-end.| Average |
| Pakket items |  Open bare en interne load balancer | Standard Load Balancer rapporteert de verwerkte pakketten per front-end.| Average |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Uw load balancer metrische gegevens weer geven in de Azure Portal

De Azure Portal geeft de load balancer metrieken via de pagina metrische gegevens, die beschikbaar is op de pagina load balancer resource voor een bepaalde resource en de Azure Monitor pagina. 

De metrische gegevens voor uw Standard Load Balancer resources weer geven:
1. Ga naar de pagina metrische gegevens en voer een van de volgende handelingen uit:
   * Selecteer op de pagina load balancer resource het type metrische gegevens in de vervolg keuzelijst.
   * Selecteer op de pagina Azure Monitor de load balancer resource.
2. Stel het juiste aggregatie type in.
3. U kunt desgewenst de vereiste filtering en groepering configureren.

    ![Metrische gegevens voor Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Afbeelding: metrische gegevens voor de beschik baarheid van gegevenspaden voor Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Via Api's multi-dimensionale metrieken ophalen

Zie [Azure Monitoring rest API-overzicht](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)voor API-richt lijnen voor het ophalen van multi-dimensionale metrische definities en waarden.

### <a name = "DiagnosticScenarios"></a>Veelvoorkomende diagnostische scenario's en aanbevolen weer gaven

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Is het gegevenspad actief en beschikbaar voor mijn load balancer VIP?

De metrische gegevens voor VIP-Beschik baarheid beschrijft de status van het gegevenspad in de regio naar de compute-host waar uw Vm's zich bevinden. De metriek is een reflectie van de status van de Azure-infra structuur. U kunt de metrische gegevens gebruiken voor het volgende:
- De externe Beschik baarheid van uw service bewaken
- Dieper en begrijp of het platform waarop uw service is geïmplementeerd in orde is en of uw gast besturingssysteem of toepassings exemplaar in orde is.
- Isoleer of een gebeurtenis is gerelateerd aan uw service of het onderliggende gegevens vlak. Verwar deze metrische waarde niet met de status Probe ("DIP-Beschik baarheid").

Om de beschik baarheid van het gegevenspad voor uw Standard Load Balancer-resources te verkrijgen:
1. Zorg ervoor dat de juiste load balancer resource is geselecteerd. 
2. Selecteer in de vervolg keuzelijst **metrische** **gegevens beschik baarheid gegevenspaden**. 
3. Selecteer in de vervolg keuzelijst **aggregatie** de optie **Gem**. 
4. Daarnaast voegt u een filter op het frontend-IP-adres of de frontend-poort toe als de dimensie met het vereiste front-end-IP-adres of de front-end-poort en groepeert u deze op basis van de geselecteerde dimensie.

![VIP-zoeken](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Afbeelding: Details van het Load Balancer van de front-end zoeken*

De metriek wordt gegenereerd door een actieve, in-band meting. Een probing-service binnen de regio is van het verkeer voor de meting. De service wordt geactiveerd zodra u een implementatie met een open bare front-end maakt en deze blijft totdat u de front-end verwijdert. 

Een pakket dat overeenkomt met de front-end van de implementatie en de regel wordt regel matig gegenereerd. Hiermee wordt de regio gepasseerd van de bron naar de host waar een virtuele machine in de back-end-pool zich bevindt. De load balancer-infra structuur voert dezelfde taak verdeling en vertaal bewerkingen uit, zoals voor al het andere verkeer. Deze test bevindt zich in de band van uw eind punt met gelijke taak verdeling. Wanneer de test op de compute-host arriveert, waarbij een gezonde VM in de back-end-pool is gevonden, genereert de compute-host een reactie op de service voor het zoeken naar de juiste locatie. Dit verkeer wordt niet in uw virtuele machine weer geven.

De VIP-Beschik baarheid mislukt om de volgende redenen:
- Uw implementatie heeft geen gezonde Vm's in de back-end-pool. 
- Er is een storing in de infra structuur opgetreden.

Voor diagnostische doel einden kunt u de [metrische gegevens over de beschik baarheid van het gegevenspad samen met de status prob](#vipavailabilityandhealthprobes)gebruiken.

Gebruik **gemiddelde** als de aggregatie voor de meeste scenario's.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Zijn de back-end-exemplaren voor mijn VIP reageren op tests?

De metrische status van de Health probe beschrijft de status van uw toepassings implementatie zoals geconfigureerd door u wanneer u de status test van uw load balancer configureert. De load balancer gebruikt de status van de status test om te bepalen waar nieuwe stromen worden verzonden. Status tests zijn afkomstig van een Azure-infrastructuur adres en zijn zichtbaar binnen het gast besturingssysteem van de virtuele machine.

Als u de status test voor uw Standard Load Balancer resources wilt ophalen:
1. Selecteer de metrische status van de **Health probe** -waarde met het **Gem** samenvoegings type. 
2. Een filter Toep assen op het vereiste frontend-IP-adres of-poort (of beide).

Status controles mislukken om de volgende redenen:
- U configureert een status test naar een poort die niet luistert of niet reageert of het verkeerde protocol gebruikt. Als uw service gebruikmaakt van Direct Server Return (DSR of zwevende IP-regels), moet u ervoor zorgen dat de service luistert naar het IP-adres van de IP-configuratie van de NIC en niet alleen op de loop back die is geconfigureerd met het front-end-IP-adres.
- Uw test is niet toegestaan door de netwerk beveiligings groep, de firewall van het gast besturingssysteem van de virtuele machine of de filters voor de toepassingslaag.

Gebruik **gemiddelde** als de aggregatie voor de meeste scenario's.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hoe kan ik de statistieken van mijn uitgaande verbinding controleren? 

Met de metrische gegevens van de SNAT-verbindingen wordt het volume van de geslaagde en mislukte verbindingen voor [uitgaande stromen](https://aka.ms/lboutbound)beschreven.

Een volume met mislukte verbindingen van meer dan nul geeft de SNAT-poort uitputting aan. U moet verder onderzoeken om te bepalen wat deze fouten kunnen veroorzaken. Er is een fout opgetreden in de SNAT-poort als er een [uitgaande stroom](https://aka.ms/lboutbound)niet tot stand kan worden gebracht. Raadpleeg het artikel over uitgaande verbindingen voor meer informatie over de scenario's en mechanismen op het werk, en om te leren hoe u het probleem kunt verhelpen en ontwerpen om te voor komen dat de SNAT-poort uitgeput raakt. 

Statistieken voor de SNAT-verbinding ophalen:
1. Selecteer het metrische type voor de **SNAT-verbindingen** en **som** als aggregatie. 
2. Groeperen op **verbindings status** voor geslaagde en mislukte SNAT-verbindings aantallen die door verschillende regels worden vertegenwoordigd. 

![SNAT-verbinding](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Afbeelding: aantal Load Balancer SNAT-verbindingen*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hoe kan ik controleren op inkomende/uitgaande Verbindings pogingen voor mijn service?

De metrische SYN-pakketten beschrijft het volume TCP SYN-pakketten, die zijn aangekomen of verzonden (voor [uitgaande stromen](https://aka.ms/lboutbound)) die zijn gekoppeld aan een specifieke front-end. U kunt deze metrische gegevens gebruiken om de TCP-verbindings pogingen met uw service te begrijpen.

Gebruik **totaal** als de aggregatie voor de meeste scenario's.

![SYN-verbinding](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Afbeelding: aantal Load Balancer SYN*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Het gebruik van mijn netwerk bandbreedte Hoe kan ik controleren? 

De metrische gegevens voor bytes en pakket items beschrijven het volume van de bytes en pakketten die door uw service per front-end worden verzonden of ontvangen.

Gebruik **totaal** als de aggregatie voor de meeste scenario's.

Statistieken voor byte of pakket aantal ophalen:
1. Selecteer het waardetype **aantal bytes** en/of **aantal pakketten** , waarbij **Gem** als de aggregatie. 
2. Voer een van de volgende bewerkingen uit:
   * Een filter Toep assen op een specifieke front-end-IP, front-end-poort, back-end-IP of back-end-poort.
   * Bekijk de algemene statistieken voor uw load balancer resource zonder filters.

![Aantal bytes](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Afbeelding: aantal Load Balancer bytes*

#### <a name = "vipavailabilityandhealthprobes"></a>Hoe kan ik een diagnose van mijn load balancer-implementatie?

Door gebruik te maken van een combi natie van metrische VIP-Beschik baarheid en status tests in één grafiek kunt u bepalen waar het probleem moet worden gezocht en het probleem oplossen. U kunt er zeker van zijn dat Azure goed werkt en deze kennis gebruiken om te bepalen of de configuratie of toepassing de hoofd oorzaak is.

U kunt metrische gegevens over Health probe gebruiken om te begrijpen hoe Azure de status van uw implementatie weergeeft volgens de configuratie die u hebt ingesteld. Het controleren of bepalen van de status tests is altijd een fantastische eerste stap bij het bewaken of vaststellen van een oorzaak.

U kunt een stap verder nemen en metrische gegevens over VIP-Beschik baarheid gebruiken om inzicht te krijgen in hoe Azure de status van het onderliggende gegevens vlak weergeeft dat verantwoordelijk is voor uw specifieke implementatie. Wanneer u beide metrische gegevens combineert, kunt u isoleren waar de fout voor komt, zoals wordt geïllustreerd in dit voor beeld:

![De metrische gegevens voor de beschik baarheid van data-en status tests combi neren](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Afbeelding: de metrische gegevens voor de beschik baarheid van data-en status waarden worden gecombineerd*

In het diagram wordt de volgende informatie weer gegeven:
- De infra structuur waarop uw Vm's worden gehost, is niet beschikbaar en 0 procent aan het begin van de grafiek. Later is de infra structuur in orde en zijn de Vm's bereikbaar en zijn er meer dan één virtuele machine in de back-end geplaatst. Deze informatie wordt aangegeven door de blauwe tracering van de beschik baarheid van gegevenspaden (VIP-Beschik baarheid), die later is 100 procent. 
- De status Probe (DIP-Beschik baarheid), aangegeven door de paarse tracering, is 0 procent aan het begin van de grafiek. Het omcirkelde gebied in groene hooglichten waarbij de status van de status test (DIP-Beschik baarheid) in orde is geworden en waarop de implementatie van de klant nieuwe stromen heeft geaccepteerd.

Met de grafiek kunnen klanten de implementatie zelf oplossen zonder dat ze hoeven te raden of om ondersteuning te vragen of er andere problemen optreden. De service is niet beschikbaar omdat de status controles zijn mislukt vanwege een onjuiste configuratie of een mislukte toepassing.

## <a name = "ResourceHealth"></a>Status van resource status

De integriteits status voor de Standard Load Balancer bronnen wordt weer gegeven via de bestaande **resource status** onder **monitor > Service Health**.

De status van uw open bare Standard Load Balancer-resources weer geven:
1. Selecteer > **service Health** **bewaken** .

   ![Pagina bewaken](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Afbeelding: de Service Health koppeling op Azure Monitor*

2. Selecteer **resource Health**en controleer of de **abonnements-id** en het **resource type = Load Balancer** zijn geselecteerd.

   ![Status van resource status](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Afbeelding: resource selecteren voor status weergave*

3. Selecteer in de lijst de Load Balancer resource om de historische status weer te geven.

    ![Status van Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Afbeelding: Load Balancer resource status weer geven*
 
De verschillende statussen van de bron en de bijbehorende beschrijvingen worden weer gegeven in de volgende tabel: 

| Status van resource status | Beschrijving |
| --- | --- |
| Beschikbaar | Uw standaard load balancer resource is in orde en beschikbaar. |
| Niet beschikbaar | Uw standaard load balancer resource is niet in orde. De status vaststellen door **Azure Monitor** > **metrieken**te selecteren.<br>(Niet-*beschik bare* status kan ook betekenen dat de resource niet is verbonden met uw standaard Load Balancer.) |
| Onbekend | De resource status voor de standaard load balancer resource is nog niet bijgewerkt.<br>(*Onbekende* status kan ook betekenen dat de bron niet is verbonden met uw standaard Load Balancer.)  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Meer informatie over de [uitgaande connectiviteit van de Load Balancer](https://aka.ms/lboutbound).
- Meer informatie over [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Meer informatie over de [Azure Monitor rest API](https://docs.microsoft.com/rest/api/monitor/) en [hoe u metrische gegevens kunt ophalen via rest API](/rest/api/monitor/metrics/list).
