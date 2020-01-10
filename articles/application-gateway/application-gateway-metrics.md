---
title: Azure Monitor metrische gegevens voor Application Gateway
description: Meer informatie over het gebruik van metrische gegevens voor het bewaken van de prestaties van de toepassings gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 8d75dbe5d4ab819e5bbe64e20ad84eb1c26a87a3
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75777815"
---
# <a name="metrics-for-application-gateway"></a>Metrische gegevens voor Application Gateway

Application Gateway publiceert gegevens punten, met de naam metrieken, naar [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) voor de prestaties van uw Application Gateway-en back-end-exemplaren. Deze metrische gegevens zijn numerieke waarden in een geordende set gegevensverzamelinggegevens die op een bepaald moment een aspect van uw toepassings gateway beschrijven. Als er aanvragen worden doorgelopen door de Application Gateway, worden de metrische gegevens gemeten en verzonden in intervallen van 60 seconden. Als er geen aanvragen worden verzonden door de Application Gateway of geen gegevens voor een metriek, wordt de metriek niet gerapporteerd. Zie [Azure monitor metrische](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)gegevens voor meer informatie.

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metrische gegevens die worden ondersteund door Application Gateway v2 SKU

### <a name="timing-metrics"></a>Metrische gegevens over timing

De volgende metrische gegevens met betrekking tot de timing van de aanvraag en het antwoord zijn beschikbaar. Door deze metrische gegevens voor een specifieke listener te analyseren, kunt u bepalen of de vertraging in toepassing is in verband met het WAN, het Application Gateway, het netwerk tussen de Application Gateway en de back-end-toepassing, of de back-end-toepassings prestaties.

> [!NOTE]
>
> Als er meer dan één listener is in de Application Gateway, filtert u altijd op de dimensie *listener* terwijl u verschillende latentie gegevens vergelijkt om zinvolle interferentie te verkrijgen.

- **Client RTT**

  Gemiddelde Round-retour tijd tussen clients en Application Gateway. Met deze metriek wordt aangegeven hoe lang het duurt om verbindingen en retour bevestigingen tot stand te brengen. 

- **Totale tijd toepassings gateway**

  De gemiddelde tijd die nodig is voor het verwerken van een aanvraag en het antwoord op verzen ding. Dit wordt berekend als gemiddelde van het interval van de tijd dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt naar het tijdstip waarop de bewerking voor het verzenden van het antwoord is voltooid. Het is belang rijk te weten dat dit doorgaans de verwerkings tijd van Application Gateway, de tijd dat de aanvraag-en antwoord pakketten op het netwerk onderweg zijn en het tijdstip waarop de back-end-server heeft gereageerd.
  
Als de RTT van de *client* veel meer is gefilterd op basis van de *totale tijds duur van de toepassings gateway*, kan deze worden afgeleid van de latentie van de client door de netwerk verbinding tussen de client en de Application Gateway. Als beide latenties vergelijkbaar zijn, kan de hoge latentie worden veroorzaakt door een van de volgende: Application Gateway, het netwerk tussen de Application Gateway en de back-end-toepassing, of de back-end-toepassings prestaties.

- **Reactie tijd eerste byte van back-end**

  Tijds interval tussen begin van het tot stand brengen van een verbinding met de back-end-server en het ontvangen van de eerste byte van de reactie header, geschatte verwerkings tijd van de back-endserver

- **Reactie tijd laatste byte van back-end**

  Tijds interval tussen begin van het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de laatste byte van de antwoord tekst
  
Als de *totale tijd van de toepassings gateway* veel meer is dan de *reactie tijd van de laatste byte* van de back-end voor een specifieke listener, dan kan deze worden afgeleid dat de maximale latentie kan worden bereikt door de Application Gateway. Daarentegen, als de twee meet waarden vergelijkbaar zijn, kan het probleem worden veroorzaakt door het netwerk tussen de Application Gateway en de back-end-toepassing, of de prestaties van de back-end-toepassing.

- **Moment back-end verbinding**

  Tijd die is besteed aan het tot stand brengen van een verbinding met een back-end-toepassing. In het geval van SSL omvat het de tijd die is besteed aan handshake. Houd er rekening mee dat deze waarde afwijkt van de andere latentie-metrische gegevens omdat dit alleen de verbindings tijd meet en daarom mag niet rechtstreeks worden vergeleken met de andere latenties. Het vergelijken van het patroon van *back-uptijden* met het patroon van de andere latenties kan echter aangeven of een toename in andere latenties kan worden afgeleid als gevolg van een variatie in het netwerk tussen de toepassing GATWAY en de back-end-toepassing. 
  

### <a name="application-gateway-metrics"></a>Application Gateway metrische gegevens

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **Ontvangen bytes**

   Aantal bytes dat is ontvangen door de Application Gateway van de clients

- **Verzonden bytes**

   Aantal bytes dat door de Application Gateway is verzonden naar de clients

- **TLS-protocol van client**

   Aantal TLS-en niet-TLS-aanvragen dat door de client is gestart en die verbinding heeft gemaakt met de Application Gateway. Als u de TLS-protocol distributie wilt weer geven, filtert u op het TLS-protocol van de dimensie.

- **Huidige capaciteits eenheden**

   Aantal verbruikte capaciteits eenheden. Capaciteits eenheden meten kosten op basis van verbruik en worden naast de vaste kosten in rekening gebracht. Er zijn drie determinanten voor de capaciteits eenheid: reken eenheid, permanente verbindingen en door voer. Elke capaciteits eenheid bestaat uit Maxi maal 1 Compute-eenheid of 2500 permanente verbindingen, of door Voer van 2,22-Mbps.

- **Huidige reken eenheden**

   Aantal verbruikte processor capaciteit. Factoren die invloed hebben op Compute-eenheid zijn TLS-verbindingen per seconde, berekeningen voor het herschrijven van URL'S en de verwerking van WAF-regels. 

- **Huidige verbindingen**

   Aantal actieve verbindingen dat tot stand is gebracht met Application Gateway

- **Mislukte aanvragen**

   Aantal mislukte aanvragen dat Application Gateway heeft geleverd. Het aantal aanvragen kan verder worden gefilterd om het aantal weer te geven per/specifieke back-end-groep-combi natie van http-instellingen.


- **Reactie status**

   De HTTP-antwoord status die is geretourneerd door Application Gateway. De antwoord status code distributie kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx-en 5xx-categorieën.

- **Doorvoer**

   Aantal bytes per seconde dat de Application Gateway heeft bediend

- **Totaal aantal aanvragen**

   Aantal geslaagde aanvragen dat Application Gateway heeft geleverd. Het aantal aanvragen kan verder worden gefilterd om het aantal weer te geven per/specifieke back-end-groep-combi natie van http-instellingen.

- **Overeenkomende regels voor Web Application firewall**

- **Geactiveerde regels voor Web Application firewall**

### <a name="backend-metrics"></a>Metrische gegevens van back-end

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **Reactie status van back-end**

  Aantal HTTP-antwoord status codes dat door de back-end wordt geretourneerd. Dit omvat geen antwoord codes die zijn gegenereerd door de Application Gateway. De antwoord status code distributie kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx-en 5xx-categorieën.

- **Aantal goede hosts**

  Het aantal back-ends dat in orde wordt gesteld door de status test. U kunt filteren op basis van een per back-end-groep om gezonde/beschadigde hosts in een specifieke back-end-groep weer te geven.

- **Aantal hosts met slechte status**

  Het aantal back-ends dat niet in orde is voor de status test. U kunt filteren op basis van een per back-end-groep om de beschadigde hosts in een specifieke back-end-groep weer te geven.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Metrische gegevens die worden ondersteund door Application Gateway v1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway metrische gegevens

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **CPU-gebruik**

  Hier wordt het gebruik van de Cpu's weer gegeven die zijn toegewezen aan de Application Gateway.  Onder normale omstandigheden mag het CPU-gebruik niet regel matig 90% overschrijden, omdat dit een latentie kan veroorzaken in de websites die achter de Application Gateway worden gehost en de client ervaring verstoort. U kunt het CPU-gebruik indirect beheren of verbeteren door de configuratie van de Application Gateway te wijzigen door het aantal exemplaren te verhogen of door naar een grotere SKU-grootte te verplaatsen of beide te doen.

- **Huidige verbindingen**

  Aantal actieve verbindingen dat tot stand is gebracht met Application Gateway

- **Mislukte aanvragen**

  Aantal mislukte aanvragen dat Application Gateway heeft geleverd. Het aantal aanvragen kan verder worden gefilterd om het aantal weer te geven per/specifieke back-end-groep-combi natie van http-instellingen.

- **Reactie status**

  De HTTP-antwoord status die is geretourneerd door Application Gateway. De antwoord status code distributie kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx-en 5xx-categorieën.

- **Doorvoer**

  Aantal bytes per seconde dat de Application Gateway heeft bediend

- **Totaal aantal aanvragen**

  Aantal geslaagde aanvragen dat Application Gateway heeft geleverd. Het aantal aanvragen kan verder worden gefilterd om het aantal weer te geven per/specifieke back-end-groep-combi natie van http-instellingen.

- **Overeenkomende regels voor Web Application firewall**

- **Geactiveerde regels voor Web Application firewall**

### <a name="backend-metrics"></a>Metrische gegevens van back-end

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **Aantal goede hosts**

  Het aantal back-ends dat in orde wordt gesteld door de status test. U kunt filteren op basis van een per back-end-groep om gezonde/beschadigde hosts in een specifieke back-end-groep weer te geven.

- **Aantal hosts met slechte status**

  Het aantal back-ends dat niet in orde is voor de status test. U kunt filteren op basis van een per back-end-groep om de beschadigde hosts in een specifieke back-end-groep weer te geven.

## <a name="metrics-visualization"></a>Visualisatie van metrische gegevens

Blader naar een toepassings gateway onder **bewaking** **metrische gegevens**selecteren. Om de beschikbare waarden te zien, selecteert u de vervolgkeuzelijst **METRISCH**.

In de volgende afbeelding ziet u een voor beeld met drie metrische gegevens die gedurende de laatste 30 minuten worden weer gegeven:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Zie [ondersteunde metrische gegevens met Azure monitor](../azure-monitor/platform/metrics-supported.md)voor een actuele lijst met metrische gegevens.

### <a name="alert-rules-on-metrics"></a>Waarschuwings regels voor metrische gegevens

U kunt waarschuwings regels starten op basis van metrische gegevens voor een resource. Een waarschuwing kan bijvoorbeeld een webhook aanroepen of een beheerder e-mailen als de door Voer van de toepassings gateway boven, onder of met een drempel waarde voor een opgegeven periode.

In het volgende voor beeld wordt stapsgewijs uitgelegd hoe u een waarschuwings regel maakt waarmee een e-mail wordt verzonden naar een beheerder na een drempel waarde voor de door Voer, bijvoorbeeld:

1. Selecteer **waarschuwing voor metrische gegevens toevoegen** om de pagina **regel toevoegen** te openen. U kunt deze pagina ook bereiken via de pagina metrische gegevens.

   ![Knop voor waarschuwing voor metrische gegevens toevoegen][6]

2. Vul op de pagina **regel toevoegen** de naam, de voor waarde en de meldings secties in en selecteer **OK**.

   * Selecteer in de **voor waarden** kiezer een van de vier waarden: **groter dan**, **groter dan of gelijk**aan, **kleiner dan**of **kleiner dan of gelijk aan**.

   * Selecteer in de **periode** kiezer een periode van vijf minuten tot zes uur.

   * Als u **e-mail eigenaren, inzenders en lezers**selecteert, kan het e-mail bericht dynamisch zijn op basis van de gebruikers die toegang hebben tot de bron. Als dat niet het geval is, kunt u een door komma's gescheiden lijst met gebruikers opgeven in het vak **extra e-mail adres** van de beheerder.

   ![Regel pagina toevoegen][7]

Als de drempel waarde wordt geschonden, ontvangt u een e-mail bericht dat lijkt op dat van de volgende afbeelding:

![E-mail voor drempel waarde voor schending][8]

Er wordt een lijst met waarschuwingen weer gegeven nadat u een metrische waarschuwing hebt gemaakt. Het bevat een overzicht van alle waarschuwings regels.

![Lijst met waarschuwingen en regels][9]

Zie [waarschuwings meldingen ontvangen](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)voor meer informatie over waarschuwings meldingen.

Ga voor meer informatie over webhooks en hoe u deze kunt gebruiken met waarschuwingen, [een webhook configureren voor een Azure metric-waarschuwing](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Volgende stappen

* Visualiseer teller-en gebeurtenis logboeken met behulp van [Azure monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualiseer uw Azure-activiteiten logboek met Power bi](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blog bericht.
* [Bekijk en analyseer activiteiten logboeken van Azure in Power bi en meer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blog berichten.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
