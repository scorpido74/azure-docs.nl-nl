---
title: Azure Monitor metrische gegevens voor Application Gateway
description: Meer informatie over het gebruik van metrische gegevens voor het bewaken van de prestaties van de toepassings gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: f0937ee53e66cb1bf0c5d6b55a8dde045570e924
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309811"
---
# <a name="metrics-for-application-gateway"></a>Metrische gegevens voor Application Gateway

Application Gateway publiceert gegevens punten, met de naam metrieken, naar [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) voor de prestaties van uw Application Gateway-en back-end-exemplaren. Deze metrische gegevens zijn numerieke waarden in een geordende set gegevensverzamelinggegevens die op een bepaald moment een aspect van uw toepassings gateway beschrijven. Als er aanvragen worden doorgelopen door de Application Gateway, worden de metrische gegevens gemeten en verzonden in intervallen van 60 seconden. Als er geen aanvragen worden verzonden door de Application Gateway of geen gegevens voor een metriek, wordt de metriek niet gerapporteerd. Zie [Azure monitor metrische](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)gegevens voor meer informatie.

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Metrische gegevens die worden ondersteund door Application Gateway v2 SKU

### <a name="timing-metrics"></a>Metrische gegevens over timing

De volgende metrische gegevens met betrekking tot de timing van de aanvraag en het antwoord zijn beschikbaar. Door deze metrische gegevens te analyseren, kunt u bepalen of de vertraging in toepassing is in verband met het WAN, het Application Gateway, het netwerk tussen de Application Gateway en de back-end of de prestaties van de toepassing.

- **Client RTT**

  Gemiddelde Round-retour tijd tussen clients en Application Gateway. Met deze metriek wordt aangegeven hoe lang het duurt om verbindingen en retour bevestigingen tot stand te brengen.

- **Totale tijd toepassings gateway**

  De gemiddelde tijd die nodig is voor het verwerken van een aanvraag en het antwoord op verzen ding. Dit wordt berekend als gemiddelde van het interval van de tijd dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt naar het tijdstip waarop de bewerking voor het verzenden van het antwoord is voltooid. Het is belang rijk te weten dat dit doorgaans de verwerkings tijd van Application Gateway, de tijd dat de aanvraag-en antwoord pakketten op het netwerk onderweg zijn en het tijdstip waarop de back-end-server heeft gereageerd.

- **Moment back-end verbinding**

  De tijd die is besteed aan het tot stand brengen van een verbinding met een back-endserver. 

- **Reactie tijd eerste byte van back-end**

  Tijds interval tussen begin van het tot stand brengen van een verbinding met de back-end-server en het ontvangen van de eerste byte van de reactie header, geschatte verwerkings tijd van de back-endserver

- **Reactie tijd laatste byte van back-end**

  Tijds interval tussen begin van het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de laatste byte van de antwoord tekst

### <a name="application-gateway-metrics"></a>Application Gateway metrische gegevens

De volgende metrische gegevens zijn beschikbaar voor Application Gateway:

- **Ontvangen bytes**

   Aantal bytes dat is ontvangen door de Application Gateway van de clients

- **Verzonden bytes**

   Aantal bytes dat door de Application Gateway is verzonden naar de clients

- **TLS-protocol van client**

   Aantal TLS-en niet-TLS-aanvragen dat door de client is gestart en die verbinding heeft gemaakt met de Application Gateway. Als u de TLS-protocol distributie wilt weer geven, filtert u op het TLS-protocol van de dimensie.

- **Huidige capaciteits eenheden**

   Aantal verbruikte capaciteits eenheden. Capaciteits eenheden meten kosten op basis van verbruik en worden naast de vaste kosten in rekening gebracht. Er zijn drie determinanten voor de capaciteits eenheid: reken eenheid, permanente verbindingen en door voer. Elke capaciteitseenheid bestaat ten minste uit: 1 reken eenheid of 2500 permanente verbindingen, of door Voer van 2,22-Mbps.

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

[![](media/application-gateway-diagnostics/figure5.png "Metrische weer gave")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Zie [ondersteunde metrische gegevens met Azure monitor](../azure-monitor/platform/metrics-supported.md)voor een actuele lijst met metrische gegevens.

### <a name="alert-rules-on-metrics"></a>Waarschuwings regels voor metrische gegevens

U kunt waarschuwings regels starten op basis van metrische gegevens voor een resource. Een waarschuwing kan bijvoorbeeld een webhook aanroepen of een beheerder e-mailen als de door Voer van de toepassings gateway boven, onder of met een drempel waarde voor een opgegeven periode.

In het volgende voor beeld wordt stapsgewijs uitgelegd hoe u een waarschuwings regel maakt waarmee een e-mail wordt verzonden naar een beheerder na een drempel waarde voor de door Voer, bijvoorbeeld:

1. Selecteer **waarschuwing voor metrische gegevens toevoegen** om de pagina **regel toevoegen** te openen. U kunt deze pagina ook bereiken via de pagina metrische gegevens.

   ![Knop voor waarschuwing voor metrische gegevens toevoegen][6]

2. Vul op de pagina **regel toevoegen** de naam, de voor waarde en de meldings secties in en selecteer **OK**.

   * Selecteer een van de vier waarden in de **voor waarden** kiezer: **Groter dan**, **groter dan of gelijk**aan, **kleiner**dan of **kleiner dan of gelijk aan**.

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
