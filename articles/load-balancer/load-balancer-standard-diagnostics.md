---
title: Diagnostische gegevens met statistieken, waarschuwingen en resourcestatus - Azure Standard Load Balancer
description: Gebruik de beschikbare statistieken, waarschuwingen en informatie over de resourcestatus om een diagnose te stellen van uw Azure Standard Load Balancer.
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
ms.openlocfilehash: 951f24ad06014f6d95f10c91e1bad8e99bbbc736
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991770"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnose van Standard Load Balancer met metrische gegevens, meldingen en status van resources

Azure Standard Load Balancer legt de volgende diagnostische mogelijkheden bloot:

* **Multidimensionale statistieken en waarschuwingen:** biedt multidimensionale diagnostische mogelijkheden via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) voor standaard configuraties van load balancer. U uw standaard resources voor de load balancer controleren, beheren en oplossen.

* **Resourcestatus:** de pagina Load Balancer in de Azure-portal en de pagina Resourcestatus (onder Monitor) weergeven de sectie Resourcestatus voor StandaardloadBalancer. 

Dit artikel biedt een snelle rondleiding door deze mogelijkheden en biedt manieren om ze te gebruiken voor Standard Load Balancer. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Multidimensionale statistieken

Azure Load Balancer biedt multidimensionale statistieken via de Azure Metrics in de Azure-portal en het helpt u om realtime diagnostische inzichten te krijgen in uw load balancer-resources. 

De verschillende Standard Load Balancer-configuraties bieden de volgende statistieken:

| Gegevens | Resourcetype | Beschrijving | Aanbevolen aggregatie |
| --- | --- | --- | --- |
| Beschikbaarheid van gegevenspad (VIP-beschikbaarheid)| Openbare en interne load balancer | Standard Load Balancer oefent continu het gegevenspad uit vanuit een regio naar de front-end van de load balancer, helemaal tot aan de SDN-stack die uw VM ondersteunt. Zolang er gezonde exemplaren blijven, volgt de meting hetzelfde pad als het load-balanced verkeer van uw toepassing. Het gegevenspad dat uw klanten gebruiken, wordt ook gevalideerd. De meting is onzichtbaar voor uw toepassing en verstoort geen andere bewerkingen.| Average |
| Status van status van status (BESCHIKBAARHEID VAN DIP) | Openbare en interne load balancer | Standard Load Balancer maakt gebruik van een gedistribueerde health-probing service die de status van uw toepassingseindpunt controleert op basis van uw configuratie-instellingen. Deze statistiek biedt een geaggregeerde of gefilterde weergave per eindpunt van elk instantieeindpunt in de groep load balancer. U zien hoe Load Balancer de status van uw toepassing bekijkt, zoals aangegeven door uw configuratie van de statussonde. |  Average |
| SYN-pakketten (synchroniseren) | Openbare en interne load balancer | Standard Load Balancer beëindigt geen TCP-verbindingen (Transmission Control Protocol) of werkt niet samen met TCP- of UDP-pakketstromen. Stromen en hun handdrukken bevinden zich altijd tussen de bron en de VM-instantie. Om uw TCP-protocolscenario's beter op te lossen, u gebruik maken van SYN-pakkettentellers om te begrijpen hoeveel TCP-verbindingspogingen worden uitgevoerd. De statistiek rapporteert het aantal TCP SYN-pakketten dat is ontvangen.| Average |
| SNAT-verbindingen | Public load balancer |Standard Load Balancer rapporteert het aantal uitgaande stromen dat is vermomd naar de front-end van het ip-adres van het publiek. SNAT-poorten (Source Network Address Translation) zijn een uitputtende bron. Deze statistiek kan een indicatie geven van hoe zwaar uw toepassing is die afhankelijk is van SNAT voor uitgaande afkomstige stromen. Tellers voor geslaagde en mislukte uitgaande SNAT-stromen worden gerapporteerd en kunnen worden gebruikt om de status van uw uitgaande stromen op te lossen en te begrijpen.| Average |
| Toegewezen SNAT-poorten | Public load balancer | Standard Load Balancer rapporteert het aantal SNAT-poorten dat per backend-instantie is toegewezen | Gemiddelde. |
| Gebruikte SNAT-poorten | Public load balancer | Standard Load Balancer rapporteert het aantal SNAT-poorten dat per backend-instantie wordt gebruikt. | Average | 
| Byte tellers |  Openbare en interne load balancer | Standard Load Balancer rapporteert de gegevens die per front-end worden verwerkt. U merken dat de bytes niet gelijk verdeeld zijn over de backend-exemplaren. Dit wordt verwacht omdat het Load Balancer-algoritme van Azure is gebaseerd op stromen | Average |
| Pakkettellers |  Openbare en interne load balancer | Standard Load Balancer rapporteert de pakketten die per front-end worden verwerkt.| Average |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Uw load balancer-statistieken weergeven in de Azure-portal

De Azure-portal legt de load balancer-statistieken bloot via de pagina Metrische gegevens, die beschikbaar is op zowel de resourcepagina load balancer voor een bepaalde resource als de Azure Monitor-pagina. 

Ga als het gaat om de statistieken voor uw resources voor standaardlastbalansen weer te geven:
1. Ga naar de pagina Statistieken en ga op een van de volgende handelingen:
   * Selecteer op de resourcepagina van de load balancer het metrische type in de vervolgkeuzelijst.
   * Selecteer op de pagina Azure Monitor de load balancer-bron.
2. Stel het juiste type metrische aggregatie in.
3. Configureer de seinen en groeperen optioneel.
4. Configureer de seinen het tijdsbereik en de aggregatie. Standaard wordt de tijd weergegeven in UTC.

  >[!NOTE] 
  >Tijdsaggregatie is belangrijk bij het interpreteren van bepaalde statistieken, omdat gegevens eenmaal per minuut worden bemonsterd. Als de tijdsaggregatie is ingesteld op vijf minuten en het metrische aggregatietype Som wordt gebruikt voor statistieken zoals SNAT-toewijzing, wordt in uw grafiek vijf keer de totale toegewezen SNAT-poorten weergegeven. 

![Statistieken voor Standaard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Afbeelding: Gegevenspadbeschikbaarheidsstatistiek voor standaardloadbalansr*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Multidimensionale statistieken programmatisch ophalen via API's

Zie Azure [Monitoring REST API-walkthrough](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)voor API-richtlijnen voor het ophalen van multidimensionale metrische definities en waarden. Deze statistieken kunnen alleen via de optie 'Alle statistieken' naar een opslagaccount worden geschreven. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Waarschuwingen configureren voor multidimensionale statistieken ###

Azure Standard Load Balancer ondersteunt eenvoudig configureerbare waarschuwingen voor multidimensionale statistieken. Configureer aangepaste drempelwaarden voor specifieke statistieken om waarschuwingen met verschillende ernstniveaus te activeren om een touchless resourcemonitoring-ervaring mogelijk te maken.

Waarschuwingen configureren:
1. Ga naar het waarschuwingssubblad voor de load balancer
1. Nieuwe waarschuwingsregel maken
    1.  Waarschuwingsvoorwaarde configureren
    1.  (Optioneel) Actiegroep toevoegen voor geautomatiseerde reparatie
    1.  Toewijzen van waarschuwingsernst, naam en beschrijving die intuïtieve reactie mogelijk maakt

  >[!NOTE]
  >Het configuratievenster van de waarschuwingsvoorwaarde toont tijdreeksen voor de signaalgeschiedenis. Er is een optie om deze tijdreeks te filteren op dimensies zoals Backend IP. Dit filtert de tijdreeksgrafiek, maar **niet** de waarschuwing zelf. U geen waarschuwingen configureren voor specifieke Backend IP-adressen.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Veelvoorkomende diagnostische scenario's en aanbevolen weergaven

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Is het gegevenspad omhoog en beschikbaar voor mijn load balancer VIP?
<details><summary>Uitvouwen</summary>

De statistiek VIP-beschikbaarheid beschrijft de status van het gegevenspad in de regio naar de compute host waar uw VM's zich bevinden. De statistiek is een weerspiegeling van de status van de Azure-infrastructuur. U de statistiek gebruiken om:
- Bewaken van de externe beschikbaarheid van uw service
- Graaf dieper en begrijp of het platform waarop uw service wordt geïmplementeerd, gezond is of dat uw gast-besturingssysteem of toepassingsinstantie in orde is.
- Isoleer of een gebeurtenis gerelateerd is aan uw service of het onderliggende gegevensvlak. Verwar deze statistiek niet met de status van de status van de status van de status van de status van de status van de status van de sonde ('DIP-beschikbaarheid').

Ga als volgt te werk om de beschikbaarheid van gegevenspad voor uw resources voor standaardlastbalansen op te halen:
1. Controleer of de juiste resource voor de load balancer is geselecteerd. 
2. Selecteer in de vervolgkeuzelijst **Metrische** optie **Beschikbaarheid van gegevenspad**. 
3. Selecteer **Avg**in de vervolgkeuzelijst **Aggregatie** . 
4. Voeg bovendien een filter toe op het FRONTend IP-adres of frontend-poort als dimensie met het vereiste front-end IP-adres of front-endpoort en groepeer ze vervolgens op de geselecteerde dimensie.

![VIP indringende](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figuur: Load Balancer Frontend indringende details*

De statistiek wordt gegenereerd door een actieve in-band meting. Een indringende dienst binnen het gebied komt verkeer voor de meting voort. De service wordt geactiveerd zodra u een implementatie maakt met een openbare front-end en gaat door totdat u de front-end verwijdert. 

Een pakket dat overeenkomt met de front-end en regel van uw implementatie wordt periodiek gegenereerd. Het doorkruist de regio van de bron naar de host waar een VM in de back-end pool zich bevindt. De infrastructuur van de load balancer voert dezelfde taakverdelings- en vertaalbewerkingen uit als voor al het andere verkeer. Deze sonde is in-band op uw load-balanced endpoint. Nadat de sonde op de compute host is aangekomen, waar een gezonde VM in de back-endpool zich bevindt, genereert de compute host een antwoord op de indringende service. Uw VM ziet dit verkeer niet.

Vip beschikbaarheid mislukt om de volgende redenen:
- Uw implementatie heeft geen gezonde VM's meer in de back-endpool. 
- Er is een storing in de infrastructuur opgetreden.

Voor diagnostische doeleinden u de [statistiek Beschikbaarheid van gegevenspad gebruiken samen met de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status .](#vipavailabilityandhealthprobes)

Gebruik **Gemiddeld** als aggregatie voor de meeste scenario's.
</details>

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Reageren de back-end exemplaren voor mijn VIP op sondes?
<details>
  <summary>Uitvouwen</summary>
De statusstatistiek status status van de status van de status van de status van de status van de status beschrijft de statusstatus van uw toepassing zoals die door u is geconfigureerd wanneer u de statussonde van uw load balancer configureert. De load balancer gebruikt de status van de statussonde om te bepalen waar nieuwe stromen moeten worden verzonden. Statussen zijn afkomstig van een Azure-infrastructuuradres en zijn zichtbaar in het gastbesturingssysteem van de VM.

Ga als u op zoek naar de status van de status van de status van de status van de status van de sonde voor uw standaardloadbalancer:To get the health probe status for your Standard Load Balancer
1. Selecteer de statistiek **Statusstatus status van status van de status** van de status van de status van de status van de status van de status van de status van de status van de **status** 
2. Pas een filter toe op het vereiste Frontend IP-adres of -poort (of beide).

Health probes mislukken om de volgende redenen:
- U configureert een statussonde naar een poort die niet luistert of niet reageert of het verkeerde protocol gebruikt. Als uw service gebruikmaakt van regels voor directe serverretour (DSR of zwevende IP)-regels, moet u ervoor zorgen dat de service luistert op het IP-adres van de IP-configuratie van de NIC en niet alleen op de loopback die is geconfigureerd met het IP-adres aan de voorkant.
- Uw sonde is niet toegestaan door de Network Security Group, de vm's guest OS firewall of de application layer filters.

Gebruik **Gemiddeld** als aggregatie voor de meeste scenario's.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hoe controleer ik mijn uitgaande verbindingsstatistieken? 
<details>
  <summary>Uitvouwen</summary>
De statistiek SNAT-verbindingen beschrijft het volume van geslaagde en mislukte verbindingen voor [uitgaande stromen](https://aka.ms/lboutbound).

Een mislukt verbindingsvolume van meer dan nul duidt op snat-poortuitputting. U moet verder onderzoeken om te bepalen wat deze fouten kan veroorzaken. SNAT-poortuitputting manifesteert zich als een verzuim om een [uitgaande stroom](https://aka.ms/lboutbound)vast te stellen . Bekijk het artikel over uitgaande verbindingen om inzicht te krijgen in de scenario's en mechanismen op het werk en om te leren hoe u de SNAT-poortuitputting beperken en ontwerpen. 

Ga als u snat-verbindingsstatistieken op de eerste plaats zet:
1. Selecteer **snat-verbindingen** metrische tekst en **Som** als aggregatie. 
2. Groeperen op **verbindingsstatus** voor geslaagde en mislukte SNAT-verbindingstellingen die worden weergegeven door verschillende regels. 

![SNAT-verbinding](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Afbeelding: aantal SNAT-verbindingspunten van Load Balancer*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Hoe controleer ik het gebruik en de toewijzing van mijn SNAT-poort?
<details>
  <summary>Uitvouwen</summary>
De snat-gebruiksstatistiek geeft aan hoeveel unieke stromen er zijn ingesteld tussen een internetbron en een backend VM- of virtuele machineschaalset die zich achter een load balancer bevindt en geen openbaar IP-adres heeft. Door dit te vergelijken met de SNAT-toewijzingsstatistiek, u bepalen of uw service last heeft van of het risico loopt op SNAT-uitputting en resulterende uitgaande stroomuitval. 

Als uw statistieken het risico van [een outstroomfout](https://aka.ms/lboutbound) aangeven, raadpleegt u het artikel en neemt u stappen om dit te beperken om de servicestatus te waarborgen.

Ga als u snat-poortgebruik en -toewijzing bekijken:
1. Stel de tijdsaggregatie van de grafiek in op 1 minuut om ervoor te zorgen dat de gewenste gegevens worden weergegeven.
1. **SNAT-gebruik** en/of **SNAT-toewijzing** selecteren als het metrische type en **Gemiddeld** als aggregatie
    * Standaard is dit het gemiddelde aantal SNAT-poorten dat is toegewezen aan of wordt gebruikt door elke backend VM's of VMSS'en, overeenkomend met alle frontend-openbare IP's die zijn toegewezen aan de Load Balancer, geaggregeerd over TCP en UDP.
    * De totale SNAT-poorten weergeven die worden gebruikt door of toegewezen voor de load balancer use metric aggregation **Sum**
1. Filter op een specifiek **protocoltype**, een set **Backend-IP's**en/of **Frontend-IP's**.
1. Als u de status per backend- of frontend-instantie wilt controleren, past u splitsen toe. 
    * Met het splitsen van notities kan slechts één statistiek tegelijk worden weergegeven. 
1. Bijvoorbeeld om het SNAT-gebruik voor TCP-stromen per machine te controleren, te worden samengevoegd op **gemiddelde**, gesplitst door **Backend-IP's** en filter op **protocoltype**. 

![SNAT-toewijzing en -gebruik](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Afbeelding: Gemiddelde TCP SNAT-poorttoewijzing en -gebruik voor een set backend VM's*

![SNAT-gebruik per backend-instantie](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Figuur: TCP SNAT-poortgebruik per backend-instantie*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hoe controleer ik inkomende/uitgaande verbindingspogingen voor mijn service?
<details>
  <summary>Uitvouwen</summary>
Een statistiek met een SYN-pakketten beschrijft het volume van TCP SYN-pakketten, die zijn aangekomen of verzonden (voor [uitgaande stromen)](https://aka.ms/lboutbound)die zijn gekoppeld aan een specifieke front-end. U deze statistiek gebruiken om inzicht te krijgen in TCP-verbindingspogingen voor uw service.

Gebruik **Total** als aggregatie voor de meeste scenario's.

![SYN-verbinding](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figuur: AANTAL LOAD Balancer SYN*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hoe controleer ik het verbruik van mijn netwerkbandbreedte? 
<details>
  <summary>Uitvouwen</summary>
De statistiek bytes en pakkettellers beschrijft het volume van bytes en pakketten die per front-end worden verzonden of ontvangen door uw service.

Gebruik **Total** als aggregatie voor de meeste scenario's.

Ga als u de statistieken over het aantal pere of het aantal pakketten wilt krijgen:
1. Selecteer het **statistiektype Aantal bytes** en/of **pakketaantal** met **Avg** als aggregatie. 
2. Voer een van de volgende bewerkingen uit:
   * Pas een filter toe op een specifieke front-end IP,front-end poort, back-end IP of back-end poort.
   * Ontvang algemene statistieken voor uw load balancer-bron zonder dat deze worden gefilterd.

![Aantal Byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figuur: Aantal lastenbalansen per saldo*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Hoe diagnosticeer ik de implementatie van mijn load balancer?
<details>
  <summary>Uitvouwen</summary>
Door een combinatie van de VIP-beschikbaarheid en health probe metrics op een enkele grafiek u identificeren waar te zoeken naar het probleem en het probleem op te lossen. U de zekerheid krijgen dat Azure correct werkt en deze kennis gebruiken om definitief te bepalen dat de configuratie of toepassing de hoofdoorzaak is.

U statussen van sondestatistieken gebruiken om te begrijpen hoe Azure de status van uw implementatie bekijkt volgens de configuratie die u hebt opgegeven. Kijken naar de gezondheid sondes is altijd een grote eerste stap in het toezicht of het bepalen van een oorzaak.

U een stap verder gaan en VIP-beschikbaarheidsstatistieken gebruiken om inzicht te krijgen in hoe Azure de status van het onderliggende gegevensvlak bekijkt dat verantwoordelijk is voor uw specifieke implementatie. Wanneer u beide statistieken combineert, u isoleren waar de fout zich kan begeven, zoals in dit voorbeeld wordt geïllustreerd:

![Gegevenspadbeschikbaarheid en statusstatistieken van statussen van gegevenstypen combineren](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Afbeelding: Gegevenspadbeschikbaarheid combineren en statusstatistieken van statussen van status van status van status*

In de grafiek worden de volgende gegevens weergegeven:
- De infrastructuur voor het hosten van uw VM's was niet beschikbaar en op 0 procent aan het begin van de grafiek. Later was de infrastructuur gezond en waren de VM's bereikbaar en werd meer dan één VM in de back-end geplaatst. Deze informatie wordt aangegeven door de blauwe tracering voor beschikbaarheid van gegevenspad (VIP beschikbaarheid), die later op 100 procent. 
- De status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de gezondheid (DIP), aangegeven door het paarse spoor, bevindt zich op 0 procent aan het begin van de grafiek. Het omcirkelde gebied in groene hoogtepunten waar de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de ziekte werd en op welk punt de implementatie van de klant nieuwe stromen kon accepteren.

De grafiek stelt klanten in staat om de implementatie zelf op te lossen zonder te hoeven raden of ondersteuning te vragen of er andere problemen optreden. De service was niet beschikbaar omdat de statussondes niet beschikbaar waren vanwege een verkeerde configuratie of een mislukte toepassing.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Status van resourcestatus

De status van de status voor de resources Standard Load Balancer wordt weergegeven via de bestaande **resourcestatus** onder **Monitor > Service Health**.

Ga als uw gevolg om de status van uw openbare resources voor standaardlastbalansen weer te geven:
1. Selecteer > **Monitorservicestatus**. **Monitor**

   ![Monitorpagina](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Afbeelding: de koppeling Servicestatus op Azure-monitor*

2. Selecteer **Resourcestatus**en controleer of **abonnements-id** en **resourcetype = Load Balancer** zijn geselecteerd.

   ![Status van resourcestatus](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Afbeelding: Resource selecteren voor de statusweergave*

3. Selecteer in de lijst de resource Load Balancer om de historische status van de status van de status te bekijken.

    ![Status load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Afbeelding: weergave resourcestatus load Balancer*
 
De verschillende statussen van resourcestatussen en hun beschrijvingen worden vermeld in de volgende tabel: 

| Status van resourcestatus | Beschrijving |
| --- | --- |
| Beschikbaar | Uw standaard load balancer resource is gezond en beschikbaar. |
| Niet beschikbaar | Uw standaard load balancer resource is niet gezond. Diagnose van de status door **Azure Monitor** > **Metrics**te selecteren .<br>(*Niet-beschikbare* status kan ook betekenen dat de resource niet is gekoppeld aan uw standaardloadbalancer.) |
| Onbekend | De status van resource status voor uw standaard load balancer resource is nog niet bijgewerkt.<br>(*Onbekende* status kan ook betekenen dat de resource niet is gekoppeld aan uw standaard load balancer.)  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Meer informatie over uw [uitgaande connectiviteit van de Load balancer](https://aka.ms/lboutbound).
- Meer informatie over [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Meer informatie over de [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) en hoe u statistieken ophalen via REST [API.](/rest/api/monitor/metrics/list)
