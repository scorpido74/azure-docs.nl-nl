---
title: Gebruiks dashboard in micro soft Commercial Marketplace Analytics
description: Meer informatie over het gebruik van toegang tot alle virtuele machines biedt metrische gegevens over facturering en data limieten. Ga naar het gebruiks dashboard in partner centrum onder commerciële Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 8747ff98cd2b53773d9e0a95436d90fef74fda55
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120667"
---
# <a name="usage-dashboard-in-microsoft-commercial-marketplace-analytics"></a>Gebruiks dashboard in micro soft Commercial Marketplace Analytics

Dit artikel bevat informatie over het gebruiks dashboard in partner centrum. In dit dash board worden alle virtuele machines gebruikt voor het gebruik en de metrische facturerings gegevens op twee afzonderlijke tabbladen: gebruik van VM'S en gefactureerd facturerings gebruik.

Open het dash board **[analyseren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** onder **commerciële Marketplace**om toegang te krijgen tot het gebruiks dashboard.

>[!NOTE]
> Zie [Veelgestelde vragen en terminologie voor de analyse van commerciële Marketplace](./faq-terminology.md)voor gedetailleerde definities van analyse terminologie.

## <a name="usage-dashboard"></a>Dashboard voor gebruik

Het gebruiks Dashboard bevat de metrische gegevens voor alle virtuele machines (VM) die gebruikmaken van het gebruik en facturerings gebruik met data limiet. Deze metrische gegevens vindt u op twee afzonderlijke tabbladen: gebruik van virtuele machines en facturering via data limiet.

Op het tabblad gebruik van de virtuele machine worden grafische voors tellingen van de volgende items weer gegeven:

- [Gebruiks overzicht](#usage-summary)
- [Gebruik per Geografie](#usage-by-geography)
- [Gebruik per aanbiedingen](#usage-by-offers)
- [Gebruiks trend per aanbiedingen en Sku's](#usage-trend-by-offers-and-skus)
- [Gebruik per aanbiedings type](#usage-by-offer-type)
- [Gebruik op VM-grootte](#usage-by-vm-size)
- [Gebruik per verkoop kanaal](#usage-by-sales-channel)
- [Gedetailleerde gebruiks gegevens](#detailed-usage-data)

> [!NOTE]
> Analyse rapporten worden anders weer gegeven in Cloud Partner-portal (CPP) en het partner centrum. De **verkoop inzichten** in cpp hebben het tabblad Orders en gebruik, die gegevens voor zowel op gebruik gebaseerde aanbiedingen als voor aanbiedingen op basis van gebruik worden weer gegeven. In Partner Center worden de metrische gegevens over het gebruik op een afzonderlijke pagina weer gegeven.

### <a name="usage-summary"></a>Overzicht van gebruik

De tabel gebruiks overzicht bevat de gebruiks uren van de klant voor alle aanbiedingen die ze hebben gekocht.

- Genormaliseerde gebruiks uren worden gedefinieerd als het gebruik van uren die zijn genormaliseerd voor het aantal VM-kernen ([aantal VM-kernen] x [uur van onbewerkt gebruik]). Vm's die zijn aangewezen als "SHAREDCORE" gebruiken 1/6 (of 0,1666) als [aantal VM-kernen] vermenigvuldiger.
- Onbewerkte gebruiks uren worden gedefinieerd als het aantal tijd Vm's dat is uitgevoerd in het kader van uren.
- De waarde percentage vertegenwoordigt wijziging van de gebruiks groei voor het geselecteerde datum bereik ([gebruik van de vorige maand – eerste maand gebruik])/het gebruik van de eerste maand).
- Groene drie hoeken die omhoog wijzen, geven aan dat de groei is gewijzigd.
- Rode drie hoek omlaag die omlaag wijst wijst op een negatieve groei wijziging ten opzichte van de vorige maand.
- Micro staaf grafieken vertegenwoordigen maandelijkse waarden. U kunt de waarde voor elke maand weer geven door de muis aanwijzer boven de kolommen in de grafiek te plaatsen.

### <a name="usage-by-geography"></a>Gebruik per Geografie

**In het genormaliseerde gebruik van geografie** heatmap worden gebruiks tijden weer gegeven die zijn toegewezen op basis van het land of de regio van de klant. Kleur variatie van land/regio vertegenwoordigt genormaliseerde gebruiks concentratie. Keer terug naar de oorspronkelijke weer gave door op de knop **Start** op de kaart te drukken.

### <a name="usage-by-offers"></a>Gebruik per aanbiedingen

- In het cirkel diagram **genormaliseerd gebruik per** wordt een uitsplitsing weer gegeven van genormaliseerde gebruiks uren per aanbiedingen volgens het geselecteerde datum bereik. De vijf belangrijkste aanbiedingen worden weer gegeven in de grafiek, terwijl de rest wordt gegroepeerd in de categorie **rest alle** .
- In het staaf diagram ziet u een groei trend per maand voor het geselecteerde datum bereik. De maand kolommen vertegenwoordigen de gebruiks uren van de aanbiedingen met de hoogste gebruiks uren voor de betreffende maand. In het lijn diagram wordt de trend van het groei percentage weer gegeven die op de secundaire Y-as is getekend.
- Gebruik de schuif regelaar aan de bovenkant van de grafiek om naar links te schuiven langs de x-as en/of focus op specifieke gegevens punten.

### <a name="usage-trend-by-offers-and-skus"></a>Gebruiks trend per aanbiedingen en Sku's

Dit diagram toont de trend van genormaliseerd gebruik voor de geselecteerde Sku's van een aanbieding. De aanbieding Leader Board geeft de Top 50 aanbiedingen met het hoogste gebruik weer, gesorteerd op gebruiks uren. De SKU Leader Board geeft de Top 50 Sku's weer met het hoogste gebruik voor de geselecteerde aanbieding.

### <a name="usage-by-offer-type"></a>Gebruik per aanbiedings type

- Het **type voor gebruik per aanbieding in** cirkel diagram organiseert het gebruik volgens het type aanbieding.
- De beste aanbiedingen worden weer gegeven in de grafiek en de rest van de aanbiedingen worden gegroepeerd als ' rest all '.
- In het **trend** diagram worden trends in de maand per maand weer gegeven. In de kolom maand staat het gebruik van de topaanbiedings typen in die maand.

### <a name="usage-by-vm-size"></a>Gebruik op VM-grootte

Dit diagram vertegenwoordigt de gebruiks trend voor de geselecteerde VM-grootten (Maxi maal 5) van al uw aanbiedingen/Sku's. Het kolom diagram is gestapeld met de gebruiks uren van de geselecteerde VM-grootten.

De Leader Board geeft de hoogste 50 VM-grootten weer met het hoogste gebruik en worden gesorteerd op gebruiks uren.

### <a name="usage-by-sales-channel"></a>Gebruik per verkoop kanaal

- Het cirkel diagram gebruik per verkoop kanaal organiseert het gebruik volgens het verkoop kanaal
- Het bovenste verkoop kanaal met het hoogste gebruik wordt weer gegeven in de grafiek en de rest van het verkoop kanaal worden gegroepeerd als ' rest all '.
- In de kolom maand staat het gebruik van het bovenste verkoop kanaal in die maand.
- De functies van deze grafiek zijn hetzelfde als de grafiek gebruik per aanbiedingen

### <a name="detailed-usage-data"></a>Gedetailleerde gebruiks gegevens

In de **tabel gebruiks gegevens** wordt een genummerde lijst weer gegeven met de bovenste 1000 gebruiks records gesorteerd op gebruik.

- Elke kolom in het raster is sorteerbaar.
- De gegevens kunnen worden geëxtraheerd naar een CSV-bestand als het aantal records kleiner is dan 1000.
- Als aantal records groter is dan 1000, worden de export gegevens asynchroon geplaatst op een pagina down loads die de volgende 30 dagen beschikbaar zullen zijn.
- Filters kunnen worden toegepast op de **gedetailleerde gebruiks gegevens** om alleen de gegevens weer te geven waarin u geïnteresseerd bent. Gegevens kunnen worden gefilterd op land/regio, verkoop kanaal, licentie type Marketplace, gebruiks type, naam van aanbieding, type aanbieding, gratis proef versies, abonnement-ID van Marketplace, klant-ID en bedrijfs naam.

> [!NOTE]
> Selecteer het **gebruiks type** in het pagina filter om grafieken weer te geven op de pagina in de weer gave ' genormaliseerde weer gave ' of ' RAW '. De standaard weergave voor deze grafieken is ' genormaliseerde weer gave '.

De **gebruiks pagina filters** worden toegepast op pagina niveau. U kunt meerdere filters selecteren om de grafiek te genereren voor de criteria die u wilt weer geven en de gegevens die u wilt weer gegeven in het raster ' gedetailleerde gebruiks gegevens '. Filters worden toegepast op de gegevens die zijn geëxtraheerd voor het gegevens bereik dat u hebt geselecteerd in de rechter bovenhoek van de pagina Orders.

- **Aanbiedings typen** en **aanbiedings namen** worden alleen vermeld voor de aanbiedingen die u hebt verkregen tijdens het geselecteerde datum bereik. Namen van aanbiedingen in de lijst worden weer gegeven voor aanbiedings typen die in de lijst zijn geselecteerd.
- De standaard selectie is alle voor elk van de filter opties, met uitzonde ring van het **gebruiks type**. De standaard selectie voor het **gebruiks type** is genormaliseerd gebruik. Als u onbewerkt gebruik in de grafieken wilt weer geven, selecteert u onbewerkt gebruik.
- Met toegepaste filters wordt de selectie telling weer gegeven voor de filters electies die zijn gemaakt. Toegepaste filters worden niet weer gegeven voor de standaard selecties.

> [!NOTE]
> Een gedetailleerde definitie van elk veld in het raster ' gedetailleerde order gegevens ', pagina filters en alle mogelijke selecties worden gedefinieerd in de sectie data dictionary van het artikel [Veelgestelde vragen en terminologie](link needed) .

Op het tabblad **gebruik van facturering via data limiet** worden gebruiks gegevens weer gegeven voor aanbiedings typen waarbij gebruik wordt gemeten met per meter dimensie. SaaS-aanbieding type overschrijding wordt momenteel weer gegeven. Het tabblad geeft grafische voors tellingen van overschrijding-trends voor het gebruik van SaaS-data limieten:

- **Overschrijding trend per meter dimensie**: geeft de maandelijkse overschrijding trend weer voor de geselecteerde meter dimensie van een aanbieding. De X-as vertegenwoordigt de maand en de Y-as vertegenwoordigt het gebruiks aantal. De meet eenheid van de aangepaste meter wordt ook weer gegeven op de Y-as.
- **Overschrijding trend per SKU**: vertegenwoordigt de trend van het gebruiks aantal van de geselecteerde meter dimensie per sku's. De weer gegeven Sku's vertegenwoordigen de top 5 Sku's met de hoogste hoeveelheid gebruik voor de geselecteerde aanbieding.
- **Overschrijding trend per top 50-klanten**: de Top 50 aanbiedingen met de hoogste gebruiks uren worden weer gegeven op een ***Leader Board*** en worden geclassificeerd op basis van het hoogste gebruik van de aangepaste meter. Selecteer een klant in de Leader Board om de gebruiks trend van een geselecteerde meter dimensie weer te geven.
- **Overschrijding trend van belangrijkste klanten**: geeft de belangrijkste klant-percentiel (en) weer die bijdragen aan het percentage van het totale gebruik. Het bovenste klant percentiel wordt weer gegeven langs de X-as en wordt bepaald door de gebruiks hoeveelheid van de klant. Op de Y-as wordt het gebruiks aantal weer gegeven. U kunt details weer geven door boven punten in het lijn diagram te bewegen.

> [!NOTE]
> De details van het gebruik en alle grafieken op deze pagina worden weer gegeven voor welke meter dimensie is geselecteerd voor het pagina filter.

## <a name="next-steps"></a>Volgende stappen

- Voor een overzicht van analyse rapporten die beschikbaar zijn in de commerciële Marketplace van partner Center raadpleegt u [Analytics voor de commerciële Marketplace in Partner Center](./analytics.md).
- Zie [Summary dash board in Commercial Marketplace Analytics](./summary-dashboard.md)voor grafieken, trends en waarden van statistische gegevens die de Marketplace-activiteiten voor uw aanbieding samenvatten.
- Voor informatie over uw orders in een grafische en download bare indeling raadpleegt u het [dash board orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Zie [klanten dashboard in Commercial Marketplace Analytics](./customer-dashboard.md)voor gedetailleerde informatie over uw klanten, waaronder groei trends.
- Zie [dash board downloaden in Commercial Marketplace Analytics](./downloads-dashboard.md)voor een lijst met Download aanvragen voor de afgelopen 30 dagen.
- Voor een geconsolideerde weer gave van feedback van klanten voor aanbiedingen op Microsoft AppSource en Azure Marketplace raadpleegt u het [dash board beoordelingen en beoordelingen in Commercial Marketplace Analytics](./ratings-reviews.md).
- Zie [Veelgestelde vragen en terminologie voor de analyse van commerciële Marketplace](./faq-terminology.md)voor veelgestelde vragen over commerciële Marketplace-analyses en voor een uitgebreid woorden boek met gegevens termen.
