---
title: Azure Monitor-statistieken voor Application Gateway
description: Meer informatie over het gebruik van statistieken om de prestaties van de toepassingsgateway te controleren
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: ebbdda39f019f374f8e5abe951d0180c0dd453f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457872"
---
# <a name="metrics-for-application-gateway"></a>Statistieken voor toepassingsgateway

Application Gateway publiceert gegevenspunten, met de naam metrics, aan [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) voor de prestaties van uw Application Gateway en backend-exemplaren. Deze statistieken zijn numerieke waarden in een geordende set tijdreeksgegevens die een bepaald aspect van uw toepassingsgateway op een bepaald moment beschrijven. Als er aanvragen door de Application Gateway stromen, worden de statistieken meten en verzenden in intervallen van 60 seconden. Als er geen aanvragen door de toepassingsgateway stromen of als er geen gegevens voor een statistiek worden gegevens, wordt de statistiek niet gerapporteerd. Zie [Azure Monitor-statistieken](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)voor meer informatie .

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Statistieken ondersteund door Application Gateway V2 SKU

### <a name="timing-metrics"></a>Timingstatistieken

Application Gateway biedt verschillende ingebouwde timingstatistieken met betrekking tot de aanvraag en het antwoord, die allemaal in milliseconden worden gemeten. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Als er meer dan één listener in de toepassingsgateway zijn, filter dan altijd op de dimensie *Listener* terwijl u verschillende latentiestatistieken vergelijkt om zinvolle gevolgtrekking te krijgen.

- **Backend verbindingstijd**

  Tijd besteed aan het tot stand brengen van een verbinding met de backend-toepassing. 

  Dit omvat de netwerklatentie en de tijd die de TCP-stack van de backendserver nodig heeft om nieuwe verbindingen tot stand te brengen. In het geval van TLS, het omvat ook de tijd besteed aan handdruk. 

- **Back-end eerste byte responstijd**

  Tijdsinterval tussen het begin van het tot stand brengen van een verbinding met backendserver en het ontvangen van de eerste byte van de antwoordkop. 

  Dit benadert de som van *backend connect tijd,* tijd die door het verzoek om de backend van Application Gateway te bereiken, tijd die door backend applicatie om te reageren (de tijd die de server nam om inhoud te genereren, potentieel ophalen database query's), en de tijd die door de eerste byte van het antwoord om de Application Gateway te bereiken vanaf de backend.

- **Laatste back-end-responstijd**

  Tijdsinterval tussen het begin van het tot stand brengen van een verbinding met backendserver en het ontvangen van de laatste byte van de antwoordbody. 

  Dit benadert de som van *Backend eerste byte responstijd* en gegevensoverdracht tijd (dit aantal kan sterk variëren, afhankelijk van de grootte van de gevraagde objecten en de latentie van het servernetwerk).

- **Totale tijd van toepassingsgateway**

  Gemiddelde tijd die nodig is om een aanvraag te ontvangen, verwerkt en het antwoord ervan te ontvangen. 

  Dit is het interval vanaf het moment dat Application Gateway de eerste byte van het HTTP-verzoek ontvangt tot het moment waarop de laatste antwoordbyte naar de client is verzonden. Dit omvat de verwerkingstijd die wordt genomen door Application Gateway, de *back-end laatste byte responstijd,* tijd die door Application Gateway om alle reactie en de *client RTT*verzenden .

- **Client RTT**

  Gemiddelde retourtijd tussen clients en Application Gateway.



Deze statistieken kunnen worden gebruikt om te bepalen of de waargenomen vertraging te wijten is aan het clientnetwerk, de prestaties van de Application Gateway, de verzadiging van de TCP-stack van backend- en backendservers, de prestaties van backendtoepassingen of de grote bestandsgrootte.

Als er bijvoorbeeld een piek in *Backend eerst byte responstijdtrend* is, maar de *backend-koppelingstijdtrend* stabiel is, kan worden afgeleid dat de toepassingsgateway voor backendlatentie en de tijd die nodig is om de verbinding tot stand te brengen stabiel is en de piek wordt veroorzaakt door een toename van de reactietijd van de backend-toepassing. Aan de andere kant, als de piek in *Backend eerste byte responstijd* is gekoppeld aan een overeenkomstige piek in *Backend connect time,* dan kan worden afgeleid dat ofwel het netwerk tussen Application Gateway en backend server of de backend server TCP stack is verzadigd. 

Als u merkt dat een piek in *Backend laatste byte responstijd,* maar de *Backend eerste byte responstijd* is stabiel, dan kan worden afgeleid dat de piek is vanwege een groter bestand wordt aangevraagd.

Als de totale tijd van de *toepassingsgateway* een piek heeft, maar de *laatste byteresponstijd van back-end* stabiel is, kan deze een teken zijn van prestatieknelpunt bij de Application Gateway of een knelpunt in het netwerk tussen client en Application Gateway. Bovendien, als de *client RTT* heeft ook een overeenkomstige piek, dan geeft aan dat dat de afbraak is vanwege het netwerk tussen client en Application Gateway.

### <a name="application-gateway-metrics"></a>Application Gateway-statistieken

Voor Application Gateway zijn de volgende statistieken beschikbaar:

- **Ontvangen bytes**

   Aantal bytes dat door de toepassingsgateway van de clients is ontvangen

- **Verzonden bytes**

   Aantal bytes dat door de toepassingsgateway naar de clients wordt verzonden

- **Client TLS-protocol**

   Aantal TLS- en niet-TLS-aanvragen geïnitieerd door de client die verbinding heeft gemaakt met de Application Gateway. Als u tls-protocoldistributie wilt weergeven, filtert u op het dimension TLS-protocol.

- **Huidige capaciteitseenheden**

   Aantal capaciteitseenheden dat wordt verbruikt om het verkeer in balans te brengen. Er zijn drie determinanten van de capaciteitseenheid - compute unit, permanente verbindingen en doorvoer. Elke capaciteitseenheid bestaat uit hoogstens: 1 rekeneenheid, of 2500 permanente verbindingen, of 2,22-Mbps doorvoer.

- **Huidige compute units**

   Aantal verbruikte processorcapaciteit. Factoren die van invloed zijn op de compute unit zijn TLS-verbindingen per seconde, URL Rewrite berekeningen en WAF-regelverwerking. 

- **Huidige verbindingen**

   Het totale aantal gelijktijdige verbindingen dat actief is van clients naar de Application Gateway
   
- **Geschatte gefactureerde capaciteitseenheden**

  Met de v2 SKU wordt het prijsmodel gedreven door verbruik. Capaciteitseenheden meten op het verbruik gebaseerde kosten die in rekening worden gebracht naast de vaste kosten. *De eenheden geschatte gefactureerde capaciteit* geven het aantal capaciteitseenheden aan waarmee de facturering wordt geschat. Dit wordt berekend als de grotere waarde tussen *de huidige capaciteitseenheden* (capaciteitseenheden die nodig zijn om het verkeer te laden) en *vaste factureerbare capaciteitseenheden* (minimale capaciteitseenheden die worden onderhouden).

- **Mislukte aanvragen**

  Aantal aanvragen dat Application Gateway heeft betekend met foutcodes van 5xx-servers. Dit omvat de 5xx-codes die worden gegenereerd vanuit de Application Gateway, evenals de 5xx-codes die worden gegenereerd vanaf de backend. Het aantal aanvragen kan verder worden gefilterd om het aantal per specifieke backendpool-http-instelling weer te geven.
   
- **Vaste factureerbare capaciteitseenheden**

  Het minimumaantal capaciteitseenheden dat wordt ingerichte volgens de instelling *Minimumschaaleenheden* (één instantie vertaalt zich naar 10 capaciteitseenheden) in de configuratie van de Toepassingsgateway.
   
 - **Nieuwe verbindingen per seconde**

   Het gemiddelde aantal nieuwe TCP-verbindingen per seconde dat is vastgesteld van clients naar de Application Gateway en van de Application Gateway naar de backend-leden.


- **Reactiestatus**

   HTTP-antwoordstatus geretourneerd door Application Gateway. De verdeling van de antwoordstatuscode kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx- en 5xx-categorieën.

- **Doorvoer**

   Aantal bytes per seconde dat de Application Gateway heeft betekend

- **Totaal aantal aanvragen**

   Aantal geslaagde aanvragen die Application Gateway heeft betekend. Het aantal aanvragen kan verder worden gefilterd om het aantal per specifieke backendpool-http-instelling weer te geven.

### <a name="backend-metrics"></a>Backend-statistieken

Voor Application Gateway zijn de volgende statistieken beschikbaar:

- **Backend-responsstatus**

  Aantal HTTP-antwoordstatuscodes die zijn geretourneerd door de backends. Dit omvat geen antwoordcodes die door de Application Gateway worden gegenereerd. De verdeling van de antwoordstatuscode kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx- en 5xx-categorieën.

- **Hostaantal in orde**

  Het aantal backends dat gezond wordt bepaald door de gezondheidssonde. U filteren op basis van een backendpool om het aantal gezonde hosts in een specifieke backendpool weer te geven.

- **Hostaantal beschadigd**

  Het aantal backends dat ongezond wordt bepaald door de gezondheidssonde. U filteren op basis van een backendpool om het aantal ongezonde hosts in een specifieke backendpool weer te geven.
  
- **Aanvragen per minuut per gezonde verhuurder**

  Het gemiddelde aantal aanvragen dat elk gezond lid in een backendpool in een minuut ontvangt. U moet de backendpool opgeven met de dimensie *BackendPool HttpSettings.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Statistieken ondersteund door Application Gateway V1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway-statistieken

Voor Application Gateway zijn de volgende statistieken beschikbaar:

- **CPU-gebruik**

  Hiermee wordt het gebruik van de CPU's die aan de toepassingsgateway zijn toegewezen, weergegeven.  Onder normale omstandigheden mag het CPU-gebruik niet regelmatig meer dan 90% bedragen, omdat dit latentie kan veroorzaken in de websites die achter de Application Gateway worden gehost en de clientervaring kan verstoren. U het CPU-gebruik indirect beheren of verbeteren door de configuratie van de toepassingsgateway te wijzigen door het aantal instanties te verhogen of door naar een grotere SKU-grootte te gaan of beide te doen.

- **Huidige verbindingen**

  Aantal huidige verbindingen met Application Gateway

- **Mislukte aanvragen**

  Aantal aanvragen dat Application Gateway heeft betekend met foutcodes van 5xx-servers. Dit omvat de 5xx-codes die worden gegenereerd vanuit de Application Gateway, evenals de 5xx-codes die worden gegenereerd vanaf de backend. Het aantal aanvragen kan verder worden gefilterd om het aantal per specifieke backendpool-http-instelling weer te geven.

- **Reactiestatus**

  HTTP-antwoordstatus geretourneerd door Application Gateway. De verdeling van de antwoordstatuscode kan verder worden gecategoriseerd om antwoorden weer te geven in 2xx-, 3xx-, 4xx- en 5xx-categorieën.

- **Doorvoer**

  Aantal bytes per seconde dat de Application Gateway heeft betekend

- **Totaal aantal aanvragen**

  Aantal geslaagde aanvragen die Application Gateway heeft betekend. Het aantal aanvragen kan verder worden gefilterd om het aantal per specifieke backendpool-http-instelling weer te geven.

- **Aantal geblokkeerde aanvragen voor webtoepassingsfirewall**
- **Distributie van geblokkeerde aanvragen voor webtoepassingsfirewall**
- **Totale regelverdeling van firewallvoor webtoepassing**

### <a name="backend-metrics"></a>Backend-statistieken

Voor Application Gateway zijn de volgende statistieken beschikbaar:

- **Hostaantal in orde**

  Het aantal backends dat gezond wordt bepaald door de gezondheidssonde. U filteren op basis van een backendpool om het aantal gezonde hosts in een specifieke backendpool weer te geven.

- **Hostaantal beschadigd**

  Het aantal backends dat ongezond wordt bepaald door de gezondheidssonde. U filteren op basis van een backendpool om het aantal ongezonde hosts in een specifieke backendpool weer te geven.

## <a name="metrics-visualization"></a>Visualisatie van metrische gegevens

Blader naar een toepassingsgateway onder **Statistieken** **controleren** . Om de beschikbare waarden te zien, selecteert u de vervolgkeuzelijst **METRISCH**.

In de volgende afbeelding ziet u een voorbeeld met drie statistieken die gedurende de laatste 30 minuten zijn weergegeven:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Zie [Ondersteunde statistieken met Azure Monitor](../azure-monitor/platform/metrics-supported.md)voor een actuele lijst met statistieken.

### <a name="alert-rules-on-metrics"></a>Waarschuwingsregels voor statistieken

U waarschuwingsregels starten op basis van statistieken voor een resource. Een waarschuwing kan bijvoorbeeld een webhook bellen of een beheerder e-mailen als de doorvoer van de toepassingsgateway zich boven, onder of op een drempelwaarde voor een bepaalde periode bevindt.

In het volgende voorbeeld wordt een waarschuwingsregel gemaakt die een e-mail naar een beheerder stuurt nadat een drempelwaarde is overschreden:

1. selecteer **Metrische waarschuwing toevoegen** om de pagina Regel **toevoegen** te openen. U deze pagina ook bereiken via de statistiekenpagina.

   ![Knop 'Metrische waarschuwing toevoegen'][6]

2. Vul op de pagina **Regel toevoegen** de naam, voorwaarde en meldingssecties in en selecteer **OK**.

   * Selecteer in de **voorwaardekiezer** een van de vier waarden: **Groter dan**groter dan **of gelijk**, Kleiner **dan**of kleiner dan of kleiner dan of kleiner dan of **gelijk aan**.

   * Selecteer in de **periodekiezer** een periode van vijf minuten tot zes uur.

   * Als u **E-maileigenaren, bijdragers en lezers**selecteert, kan de e-mail dynamisch zijn op basis van de gebruikers die toegang hebben tot die bron. Anders u een door komma's gescheiden lijst met gebruikers opnemen in het vak **E-mail(s) van extra beheerders.**

   ![Regelpagina toevoegen][7]

Als de drempelwordt overschreden, wordt een e-mail binnengekomen die vergelijkbaar is met de e-mail in de volgende afbeelding:

![E-mail voor geschonden drempel][8]

Er wordt een lijst met waarschuwingen weergegeven nadat u een metrische waarschuwing hebt gemaakt. Het geeft een overzicht van alle waarschuwingsregels.

![Lijst met waarschuwingen en regels][9]

Zie Waarschuwingsmeldingen ontvangen voor meer informatie over [waarschuwingsmeldingen.](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Ga naar [Een webhook configureren op een Azure-metrische waarschuwing](../azure-monitor/platform/alerts-webhooks.md)voor meer informatie over webhooks en hoe u ze gebruiken met waarschuwingen.

## <a name="next-steps"></a>Volgende stappen

* Visualiseer teller- en gebeurtenislogboeken met [Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualiseer uw Azure-activiteitenlogboek met Power BI-blogbericht.](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)
* [Azure-activiteitslogboeken weergeven en analyseren in Power BI en meer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogberichten.

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
