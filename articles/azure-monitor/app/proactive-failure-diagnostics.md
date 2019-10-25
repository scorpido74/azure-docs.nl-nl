---
title: Slimme detectie-fout afwijkingen, in Application Insights | Microsoft Docs
description: Hiermee wordt u gewaarschuwd voor ongebruikelijke wijzigingen in de frequentie van mislukte aanvragen voor uw web-app en diagnostische analyse. Er is geen configuratie nodig.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.openlocfilehash: f8b8318a16b36593d2fbaf08bcbc19156dc96006
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820584"
---
# <a name="smart-detection---failure-anomalies"></a>Slimme detectie-fout afwijkingen
[Application Insights](../../azure-monitor/app/app-insights-overview.md) waarschuwt u automatisch in bijna realtime als uw web-app een abnormale toename in de frequentie van mislukte aanvragen ondervindt. Er wordt een ongebruikelijke toename gedetecteerd met het aantal HTTP-aanvragen of afhankelijkheids aanroepen dat als mislukt wordt gerapporteerd. Voor aanvragen zijn mislukte aanvragen meestal die met antwoord codes van 400 of hoger. Om u te helpen het probleem te sorteren en te diagnosticeren, wordt een analyse van de kenmerken van de storingen en gerelateerde telemetrie in de melding gegeven. Er zijn ook koppelingen naar de Application Insights portal voor verdere diagnose. De functie heeft geen instellingen of configuratie nodig, omdat deze machine learning algoritmen gebruikt om het normale fout aantal te voors pellen.

Deze functie werkt voor elke web-app, die wordt gehost in de Cloud of op uw eigen servers, die een aanvraag-of afhankelijkheids-telemetrie genereert, bijvoorbeeld als u een werk rollen hebt die [TrackRequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) of [TrackDependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)aanroept.

Nadat u Application Insights hebt ingesteld [voor uw project](../../azure-monitor/app/app-insights-overview.md)en uw app een bepaalde minimale hoeveelheid telemetrie heeft gegenereerd, duurt een slimme detectie van fout afwijkingen 24 uur om het normale gedrag van uw app te ontdekken, voordat deze wordt ingeschakeld en kan worden verzonden berichten.

Hier volgt een voor beeld van een waarschuwing.

![Voor beeld van een Smart-detectie waarschuwing met een cluster analyse rondom fouten](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Standaard krijgt u een korter e-mail adres dan dit voor beeld. U kunt echter [overschakelen naar deze gedetailleerde indeling](#configure-alerts).
>
>

U ziet dat u:

* De fout frequentie vergeleken met het gedrag van de normale app.
* Het aantal gebruikers waarop dit van toepassing is, zodat u weet hoeveel u zich kunt voordoen.
* Een karakteristiek patroon dat is gekoppeld aan de fouten. In dit voor beeld is er een bepaalde antwoord code, aanvraag naam (Operation) en App-versie. Zo weet u meteen waar u moet beginnen met het zoeken naar uw code. Andere mogelijkheden zijn mogelijk een specifieke browser of client besturingssysteem.
* De uitzonde ring, logboek traceringen en afhankelijkheids fout (data bases of andere externe onderdelen) die moeten worden gekoppeld aan de getekende fouten.
* Hiermee worden rechtstreeks koppelingen naar relevante Zoek opdrachten op de telemetrie in Application Insights.

## <a name="failure-anomalies-v2"></a>Fout afwijkingen v2
Er is nu een nieuwe versie van de waarschuwings regel voor afwijkingen van de fout beschikbaar. Deze nieuwe versie wordt uitgevoerd op het nieuwe Azure Alerting platform en introduceert een aantal verbeteringen ten opzichte van de bestaande versie.

### <a name="whats-new-in-this-version"></a>Wat is er nieuw in deze versie?
- Snellere detectie van problemen
- Een rijkere set acties: de waarschuwings regel wordt gemaakt met een gekoppelde [actie groep](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) met de naam ' Application Insights Slimme detectie ' die e-mail en webhook-acties bevat, en kan worden uitgebreid om aanvullende acties te activeren wanneer de waarschuwing wordt geactiveerd.
- Meer gerichte meldingen: e-mail meldingen die vanuit deze waarschuwings regel worden verzonden, worden nu standaard verzonden naar gebruikers die zijn gekoppeld aan de bewakings-en bewakings functies van het abonnement. Hier vindt u meer informatie over dit [onderwerp](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
- Eenvoudige configuratie via ARM-sjablonen: Zie [hier](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)voor beeld.
- Algemene ondersteuning voor waarschuwings schema's: meldingen die vanuit deze waarschuwings regel worden verzonden, volgen het [algemene waarschuwings schema](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
- Unified Email-sjabloon: e-mail meldingen van deze waarschuwings regel hebben een consistent uiterlijk & met andere waarschuwings typen. Met deze wijziging is de optie om fout afwijkingen op te halen met gedetailleerde diagnostische gegevens niet meer beschikbaar.

### <a name="how-do-i-get-the-new-version"></a>Hoe kan ik de nieuwe versie ophalen?
- Nieuw gemaakte Application Insights resources worden nu ingericht met de nieuwe versie van de waarschuwings regel voor afwijkingen van de fout.
- Bestaande Application Insights resources met de klassieke versie van de waarschuwings regel voor fout afwijkingen krijgen de nieuwe versie zodra hun host-abonnement is gemigreerd naar het nieuwe waarschuwings platform als onderdeel van het [buitenste proces voor klassieke waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> De nieuwe versie van de waarschuwings regel voor fout afwijkingen blijft vrij. Daarnaast zijn er gratis e-mail-en webhook-acties geactiveerd door de gekoppelde actie groep voor detectie van Application Insights.
> 
> 

## <a name="benefits-of-smart-detection"></a>Voor delen van Slimme detectie
Normale [metrische waarschuwingen](../../azure-monitor/app/alerts.md) geven aan dat er mogelijk een probleem is. Maar slimme detectie start het diagnostische werk voor u, waarbij veel van de analyse wordt uitgevoerd die u anders zou moeten uitvoeren. U krijgt de resultaten die u netjes kunt bundelen, zodat u snel aan de slag kunt met het probleem.

## <a name="how-it-works"></a>Het werkt als volgt
Slimme detectie bewaakt de telemetriegegevens die zijn ontvangen van uw app, met name de fout tarieven. Deze regel telt het aantal aanvragen waarvoor de eigenschap `Successful request` False is en het aantal afhankelijkheids aanroepen waarvoor de eigenschap `Successful call` False is. Voor aanvragen wordt standaard `Successful request == (resultCode < 400)` (tenzij u een aangepaste code hebt geschreven om uw eigen [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) -aanroepen te [filteren](../../azure-monitor/app/api-filtering-sampling.md#filtering) of te genereren). 

De prestaties van uw app hebben een typisch patroon van het gedrag. Sommige aanvragen of afhankelijkheids aanroepen zijn gevoelig voor fouten dan andere. en het totale aantal fouten kan optreden naarmate de belasting toeneemt. Slimme detectie maakt gebruik van machine learning om deze afwijkingen te vinden.

Wanneer telemetrie in Application Insights van uw web-app komt, vergelijkt Slimme detectie het huidige gedrag met de patronen die in de afgelopen paar dagen worden weer gegeven. Als een abnormale toename van het aantal fouten wordt waargenomen door vergelijking met de vorige prestaties, wordt een analyse geactiveerd.

Wanneer een analyse wordt geactiveerd, voert de service een cluster analyse uit op de mislukte aanvraag, om te proberen een patroon van waarden te identificeren dat de fouten kenmerkt. In het bovenstaande voor beeld heeft de analyse ontdekt dat de meeste fouten betrekking hebben op een specifieke resultaat code, aanvraag naam, server-URL-host en rolinstantie. De analyse heeft daarentegen gedetecteerd dat de eigenschap van het besturings systeem van de client wordt gedistribueerd over meerdere waarden, zodat deze niet wordt weer gegeven.

Als uw service wordt gebruikt met deze telemetrie-aanroepen, zoekt de Analyzer naar een uitzonde ring en een afhankelijkheids fout die is gekoppeld aan aanvragen in het cluster dat is geïdentificeerd, samen met een voor beeld van traceer logboeken die zijn gekoppeld aan deze aanvragen.

De resulterende analyse wordt naar u verzonden als waarschuwing, tenzij u deze hebt geconfigureerd.

Net als de [waarschuwingen die u hand matig hebt ingesteld](../../azure-monitor/app/alerts.md), kunt u de status van de waarschuwing inspecteren en configureren op de Blade waarschuwingen van uw Application Insights-resource. Maar in tegens telling tot andere waarschuwingen hoeft u geen slimme detectie in te stellen of te configureren. Als u wilt, kunt u deze uitschakelen of de e-mail adressen van het doel wijzigen.

### <a name="alert-logic-details"></a>Details van waarschuwings logica

De waarschuwingen worden geactiveerd door onze eigen machine learning-algoritme, zodat de details van de exacte implementatie niet kunnen worden gedeeld. We begrijpen dat u soms meer moet weten over de werking van de onderliggende logica. De primaire factoren die worden geëvalueerd om te bepalen of een waarschuwing moet worden geactiveerd zijn: 

* Analyse van het percentage mislukte aanvragen/afhankelijkheden in een rollend venster van 20 minuten.
* Een vergelijking van het uitval percentage van de laatste 20 minuten tot het percentage in de afgelopen 40 minuten en de afgelopen zeven dagen en op zoek naar aanzienlijke afwijkingen die de X-tijden van de standaard deviatie overschrijden.
* Het gebruik van een adaptieve limiet voor het minimale uitval percentage, dat varieert op basis van het volume van de aanvragen/afhankelijkheden van de app.

## <a name="configure-alerts"></a>Waarschuwingen configureren
U kunt slimme detectie uitschakelen, de ontvangers van het e-mail bericht wijzigen, een webhook maken of kiezen voor meer gedetailleerde waarschuwings berichten.

Open de pagina waarschuwingen. Fout afwijkingen zijn opgenomen in combi natie met waarschuwingen die u hand matig hebt ingesteld en u kunt zien of deze zich momenteel in de status van de waarschuwing bevindt.

![Klik op de pagina overzicht op waarschuwingen tegel. U kunt ook op de knop waarschuwingen klikken op de pagina met metrische gegevens.](./media/proactive-failure-diagnostics/021.png)

Klik op de waarschuwing om deze te configureren.

![Configuratie](./media/proactive-failure-diagnostics/032.png)

U kunt slimme detectie uitschakelen, maar niet verwijderen (of een andere maken).

#### <a name="detailed-alerts"></a>Gedetailleerde waarschuwingen
Als u ' meer gedetailleerde diagnostische gegevens ophalen ' selecteert, bevat het e-mail bericht meer diagnostische informatie. Soms kunt u het probleem alleen vaststellen op basis van de gegevens in het e-mail bericht.

Er is een klein risico dat de gedetailleerde waarschuwing gevoelige informatie kan bevatten, omdat deze uitzonderings-en tracerings berichten bevat. Dit gebeurt echter alleen als uw code gevoelige informatie in deze berichten zou kunnen toestaan.

## <a name="triaging-and-diagnosing-an-alert"></a>Een waarschuwing uitsorteren en diagnosticeren
Een waarschuwing geeft aan dat een abnormale toename van het aantal mislukte aanvragen is gedetecteerd. Waarschijnlijk is er sprake van een probleem met uw app of van de omgeving.

Op basis van het percentage aanvragen en het aantal betrokken gebruikers kunt u bepalen hoe urgent het probleem is. In het bovenstaande voor beeld wordt het uitval percentage van 22,5% vergeleken met een normaal percentage van 1%, wat erop wijst dat er iets mis gaat. Aan de andere kant werden slechts 11 gebruikers beïnvloed. Als het uw app is, kunt u beoordelen hoe ernstig dat is.

In veel gevallen kunt u het probleem snel vaststellen op basis van de gegevens van de aanvraag naam, uitzonde ring, fout bij het openen van een afhankelijkheid.

Er zijn enkele andere aanwijzingen. Zo is de fout frequentie afhankelijkheid in dit voor beeld hetzelfde als het uitzonderings percentage (89,3%). Dit geeft aan dat de uitzonde ring zich rechtstreeks bij de afhankelijkheids fout voordoet, waardoor u een duidelijk beeld krijgt van waar u uw code kunt zoeken.

Om verder te onderzoeken, worden de koppelingen in elke sectie direct naar een [Zoek pagina](../../azure-monitor/app/diagnostic-search.md) gefilterd op de relevante aanvragen, uitzonde ringen, afhankelijkheid of traceringen. U kunt ook de [Azure Portal](https://portal.azure.com)openen, naar de Application Insights resource voor uw app gaan en de Blade fouten openen.

In dit voor beeld wordt op de koppeling ' Details van afhankelijkheids fouten weer geven ' de Blade Application Insights zoeken geopend. Hierin wordt de SQL-instructie weer gegeven die een voor beeld van de hoofd oorzaak heeft: NULL-waarden zijn opgegeven bij verplichte velden en de validatie is niet geslaagd tijdens het opslaan.

![Diagnostische gegevens doorzoeken](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Recente waarschuwingen controleren

Klik op **Slimme detectie** om naar de meest recente waarschuwing te gaan:

![Overzicht van waarschuwingen](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Wat is het verschil...
Slimme detectie van fout afwijkingen is een aanvulling op andere vergelijk bare functies van Application Insights.

* [Metrische waarschuwingen](../../azure-monitor/app/alerts.md) worden door u ingesteld en kunnen een breed scala aan metrische gegevens bewaken, zoals CPU-bezetting, aanvraag tarieven, pagina laad tijden, enzovoort. U kunt deze gebruiken om u te waarschuwen als u bijvoorbeeld meer resources wilt toevoegen. Slimme detectie van fout afwijkingen is daarentegen van toepassing op een beperkt aantal kritieke metrische gegevens (momenteel alleen mislukte aanvragen), ontworpen om u op de bijna realtime te informeren wanneer de frequentie van de mislukte aanvragen van uw web-app sterk toeneemt in vergelijking met de normaal gedrag.

    Slimme detectie past de drempel waarde automatisch aan als reactie op de geldende voor waarden.

    Slimme detectie start het diagnostische werk voor u.
* [Slimme detectie van prestatie afwijkingen](proactive-performance-diagnostics.md) maakt ook gebruik van machine Intelligence om ongebruikelijke patronen in uw metrische gegevens te ontdekken. u hoeft hiervoor geen configuratie op te nemen. Maar in tegens telling tot Slimme detectie van fout afwijkingen, is het doel van de detectie van de prestatie afwijkingen te bepalen van de segmenten van uw gebruiks fragmenten die mogelijk niet goed worden aangeboden, bijvoorbeeld door specifieke pagina's van een specifiek type browser. De analyse wordt dagelijks uitgevoerd en als er een resultaat wordt gevonden, is het waarschijnlijk veel minder urgent dan een waarschuwing. Daarentegen wordt de analyse voor fout afwijkingen doorlopend uitgevoerd op inkomende telemetrie en wordt u binnen enkele minuten gewaarschuwd als de snelheid van de server fouten groter is dan verwacht.

## <a name="if-you-receive-a-smart-detection-alert"></a>Als u een Smart detection-waarschuwing ontvangt
*Waarom heb ik deze waarschuwing ontvangen?*

* Er is een abnormale toename in de frequentie van mislukte aanvragen gedetecteerd vergeleken met de normale basis lijn van de voor gaande periode. Na de analyse van de fouten en de bijbehorende telemetrie, denken we dat er een probleem is waarin u moet letten.

*Is er een probleem met de melding?*

* We proberen een waarschuwing te ontvangen over het onderbreken of degraderen van apps, maar alleen u kunt de semantiek en de impact van de app of gebruikers volledig begrijpen.

*Dus guys u mijn gegevens bekijken?*

* Nee. De service is volledig automatisch. Alleen de meldingen worden weer geven. Uw gegevens zijn [privé](../../azure-monitor/app/data-retention-privacy.md).

*Moet ik zich abonneren op deze waarschuwing?*

* Nee. Elke toepassing die telemetrie van aanvragen verzendt, heeft de waarschuwings regel voor slimme detectie.

*Kan ik uw abonnement opzeggen of meldingen ontvangen die naar mijn collega's worden verzonden?*

* Ja, klik in waarschuwings regels op de regel voor slimme detectie om deze te configureren. U kunt de waarschuwing uitschakelen of ontvangers wijzigen voor de waarschuwing.

*Ik ben de e-mail kwijt geraakt. Waar vind ik de meldingen in de portal?*

* In de activiteiten Logboeken. Open in azure de Application Insights resource voor uw app en selecteer vervolgens activiteiten Logboeken.

*Sommige waarschuwingen zijn te vinden op bekende problemen en ik wil ze niet ontvangen.*

* We hebben een waarschuwing over de achterstand.

## <a name="next-steps"></a>Volgende stappen
Met deze diagnostische hulpprogram ma's kunt u de telemetrie van uw app inspecteren:

* [Metrische Explorer](../../azure-monitor/app/metrics-explorer.md)
* [Zoek Verkenner](../../azure-monitor/app/diagnostic-search.md)
* [Analyse-krachtige query taal](../../azure-monitor/log-query/get-started-portal.md)

Slimme detecties zijn volledig automatisch. Maar misschien wilt u nog meer waarschuwingen instellen?

* [Hand matig geconfigureerde metrische waarschuwingen](../../azure-monitor/app/alerts.md)
* [Webtests voor Beschik baarheid](../../azure-monitor/app/monitor-web-app-availability.md)
