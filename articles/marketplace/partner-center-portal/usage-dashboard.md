---
title: Gebruiksdashboard in commerciële marktplaatsanalyses in partnercentrum
description: Meer informatie over hoe u toegang krijgt tot alle vm-aanbiedingen en factureringsstatistieken met datameting.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 33762540d14ea51e8325abe9a466007cd7cca748
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262176"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Gebruiksdashboard in commerciële marktplaatsanalyses

In dit artikel vindt u informatie over het dashboard Gebruik in partnercentrum. In dit dashboard worden alle VM-factureringsstatistieken en factureringsstatistieken met datameting weergegeven in twee afzonderlijke tabbladen: VM-gebruik en factureringsgebruik met datameter.

Als u toegang wilt krijgen tot het dashboard Gebruik, opent u het dashboard **[Analyseren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** onder Commerciële marktplaats.

>[!NOTE]
> Zie [Veelgestelde vragen en terminologie voor commerciële marktplaatsanalyses voor](./faq-terminology.md)gedetailleerde definities van analytics-terminologie.

## <a name="usage-dashboard"></a>Dashboard voor gebruik

Het gebruiksdashboard vertegenwoordigt de statistieken voor alle virtuele machine (VM) biedt gebruik en factureringsgebruik met datameting. Deze zijn te vinden in twee afzonderlijke tabbladen: VM-gebruik en factureringsgebruik met datameter.

Op het tabblad VM-gebruik staan grafische weergaven van de volgende items:

- [Gebruiksoverzicht](#usage-summary)
- [Gebruik per geografie](#usage-by-geography)
- [Gebruik door aanbiedingen](#usage-by-offers)
- [Gebruikstrend door aanbiedingen en SKU's](#usage-trend-by-offers-and-skus)
- [Gebruik per aanbiedingstype](#usage-by-offer-type)
- [Gebruik op VM-grootte](#usage-by-vm-size)
- [Gebruik per verkoopkanaal](#usage-by-sales-channel)
- [Gedetailleerde gebruiksgegevens](#detailed-usage-data)

> [!NOTE]
> Analytics-rapporten worden anders weergegeven in Cloud Partner Portal (CPP) en Partner Center. **Verkopersinzichten** in CPP hebben een tabblad orders en gebruik, dat gegevens weergeeft voor zowel op gebruik gebaseerde aanbiedingen als aanbiedingen op basis van niet-gebruik. In Partnercentrum worden de gebruiksstatistieken op een aparte pagina weergegeven.

### <a name="usage-summary"></a>Overzicht van gebruik

In de overzichtstabel voor het gebruik worden de openingstijden van het klantgebruik weergegeven voor alle aanbiedingen die ze hebben gekocht.

- Genormaliseerde gebruiksuren worden gedefinieerd als de gebruiksuren die zijn genormaliseerd om rekening te houden met het aantal VM-kernen ([aantal VM-kernen] x [uren onberauw gebruik]). VM's die als "SHAREDCORE" zijn aangeduid, gebruiken 1/6 (of 0,1666) als de vermenigvuldiger van [aantal VM-kernen].
- Onbewerkte gebruiksuren worden gedefinieerd als de hoeveelheid tijd die VM's hebben uitgevoerd in termen van uren.
- De procentuele waarde vertegenwoordigt verandering in de gebruiksgroei voor het geselecteerde datumbereik ([gebruik van vorige maand – gebruik van de eerste maand])/ gebruik van de eerste maand).
- Groene driehoeken die naar boven wijzen duiden op een groeiverandering.
- Rode driehoek die naar beneden wijst duidt op een negatieve groeiverandering ten opzichte van de voorgaande maand.
- Grafieken van microbalken vertegenwoordigen maandelijkse waarden. U de waarde voor elke maand weergeven door boven de kolommen in de grafiek te zweven.

### <a name="usage-by-geography"></a>Gebruik per geografie

Het **genormaliseerde gebruik per aardrijkskunde** heatmap geeft gebruiksuren weer die zijn toegewezen volgens het land van de klant. De kleurvariatie van het land vertegenwoordigt genormaliseerde gebruiksconcentratie. Keer terug naar de oorspronkelijke weergave door op de **startknop** op de kaart te drukken.

### <a name="usage-by-offers"></a>Gebruik door aanbiedingen

- Het **genormaliseerde gebruik door biedt** cirkeldiagram geeft een uitsplitsing weer van genormaliseerde gebruiksuren op basis van aanbiedingen op basis van het geselecteerde datumbereik. De top 5 aanbiedingen worden weergegeven in de grafiek, terwijl de rest zijn gegroepeerd in de "rest all" categorie.
- Het staafdiagram toont een groeitrend per maand voor het geselecteerde datumbereik. De maandkolommen geven gebruiksuren weer van de aanbiedingen met de hoogste gebruiksuren voor de betreffende maand. Het lijndiagram toont de groeipercentagetrend die is uitgezet op de secundaire Y-as.
- Gebruik de schuifregelaar boven aan de grafiek om van rechts naar links te scrollen langs de x-as en/of te focussen op specifieke gegevenspunten.

### <a name="usage-trend-by-offers-and-skus"></a>Gebruikstrend door aanbiedingen en SKU's

In deze grafiek wordt de trend van genormaliseerd gebruik voor de geselecteerde SKU's van een aanbieding weergegeven. Het aanbod leaderboard toont de top 50 aanbiedingen met het hoogste gebruik en worden gesorteerd op gebruik uren. Het SKU-leaderboard toont de top 50 SKU's met het hoogste gebruik voor de geselecteerde aanbieding.

### <a name="usage-by-offer-type"></a>Gebruik per aanbiedingstype

- Het **gebruik per aanbiedingstype** cirkeldiagram organiseert het gebruik op basis van het aanbiedingstype.
- De topaanbiedingen worden weergegeven in de grafiek en de rest van de aanbiedingen zijn gegroepeerd als 'Rest All'.
- Het **trenddiagram** toont groeitrends per maand. De kolom maand vertegenwoordigt het gebruik op de beste aanbiedingstypen in die maand.

### <a name="usage-by-vm-size"></a>Gebruik op VM-grootte

Deze grafiek geeft de gebruikstrend weer voor geselecteerde VM-formaten (max 5) van al uw aanbiedingen/SKU's. Het kolomdiagram wordt gestapeld met de gebruiksuren van de geselecteerde VM-grootten.

Het leaderboard toont de top 50 VM-formaten met het hoogste gebruik en gesorteerd op gebruiksuren.

### <a name="usage-by-sales-channel"></a>Gebruik per verkoopkanaal

- Het gebruik per verkoopkanaalcirkeldiagram organiseert het gebruik op basis van het verkoopkanaal
- Het hoogste verkoopkanaal met het hoogste gebruik wordt weergegeven in de grafiek en de rest van het verkoopkanaal wordt gegroepeerd als 'Rest All'.
- De kolom maand vertegenwoordigt het gebruik per topverkoopkanaal in die maand.
- Kenmerken van deze grafiek zijn hetzelfde als de grafiek 'Gebruik door aanbiedingen'

### <a name="detailed-usage-data"></a>Gedetailleerde gebruiksgegevens

In **de tabel met gebruiksgegevens** wordt een genummerde lijst weergegeven met de top 1000 gebruiksrecords gesorteerd op gebruik.

- Elke kolom in het raster is sorteerbaar.
- De gegevens kunnen worden geëxtraheerd naar een CSV-bestand als het aantal records minder dan 1000 is.
- Als het aantal records meer dan 1000 bedraagt, worden exportgegevens asynchroon geplaatst op een downloadspagina die de komende 30 dagen beschikbaar zal zijn.
- Filters kunnen worden toegepast op de **gedetailleerde gebruiksgegevens** om alleen de gegevens weer te geven waarin u geïnteresseerd bent. Gegevens kunnen worden gefilterd op land, verkoopkanaal, Marketplace-licentietype, gebruikstype, aanbiedingsnaam, aanbiedingstype, gratis proefversies, Marketplace-abonnements-ID, klant-id en bedrijfsnaam.

> [!NOTE]
> Selecteer het **gebruikstype** in het paginafilter om grafieken op de pagina weer te geven in de 'Genormaliseerde weergave' of 'Ruwe weergave'. De standaardweergave voor deze grafieken is 'Genormaliseerde weergave'.

De **paginafilters voor gebruik** worden toegepast op paginaniveau. U meerdere filters selecteren om de grafiek weer te geven voor de criteria die u wilt weergeven en de gegevens die u wilt weergeven in het raster 'gedetailleerde gebruiksgegevens'/exporteren. Filters worden toegepast op de gegevens die zijn geëxtraheerd voor het gegevensbereik dat u hebt geselecteerd in de rechterbovenhoek van de opdrachtpagina.

- **Aanbiedingstypen** en **aanbiedingsnamen** worden alleen vermeld voor de aanbiedingen die u hebt verkregen tijdens het geselecteerde datumbereik. Aanbiedingsnamen in de lijst worden weergegeven voor aanbiedingstypen die in de lijst zijn geselecteerd.
- De standaardselectie is 'Alles' voor elk van de filteropties, met uitzondering van **gebruikstype**. De standaardselectie voor **gebruikstype** is genormaliseerd gebruik. Als u het ruwe gebruik in de grafieken wilt weergeven, selecteert u 'onbewerkt gebruik'.
- Toegepaste filters geven de aantalselecties(en) weer voor de filterselecties die zijn gemaakt. Toegepaste filters worden niet weergegeven voor de standaardselecties.

> [!NOTE]
> Een gedetailleerde definitie van elk van de velden in het raster 'gedetailleerde ordergegevens', paginafilters en alle mogelijke selecties worden gedefinieerd in het gedeelte gegevenswoordenboek van de [veelgestelde vragen en terminologieartikel.](link needed)

Op het tabblad **Facturering met gegevens over facturering met gegevens over de facturering gemeten** wordt het gebruik van de aanbiedingstypen per meterdimensie weergegeven. SaaS aanbod type overschrijding wordt momenteel gepresenteerd. Het tabblad presenteert grafische weergaven van overschrijdingstrends voor factureringsgebruik met saas-gemeten:

- **Overschrijdingstrend per meterdimensie**: geeft de maandelijkse overschrijdingstrend weer voor de geselecteerde meterafmeting van een aanbieding. De X-As vertegenwoordigt de maand en de Y-as vertegenwoordigt het gebruiksaantal. De meeteenheid van de aangepaste meter wordt ook weergegeven op de Y-as.
- **Overschrijdingstrend door SKU**: Geeft de trend weer van de gebruikshoeveelheid van de geselecteerde meterdimensie door SKU's. De weergegeven SKU's vertegenwoordigen de top 5 SKU's met de hoogste hoeveelheid gebruik voor de geselecteerde aanbieding.
- **Overschrijdingtrend door Top 50-klanten**: De top 50-aanbiedingen met de hoogste gebruiksuren worden weergegeven op een ***klassement*** en worden gerangschikt op basis van het hoogste gebruik van de aangepaste meter. Selecteer een klant in het leaderboard om de gebruikstrend van een geselecteerde meterdimensie te bekijken.
- **Overage trend door top klanten**: Presenteert top klant percentiel (s) die bijdragen aan het % van het totale gebruik. Het bovenste klantpercentiel wordt weergegeven langs de X-as en wordt bepaald door het gebruikshoeveelheid van de klant. De Y-as geeft het gebruiksaantal weer. U details weergeven door over punten langs het lijndiagram te zweven.

> [!NOTE]
> De gebruiksgegevens en alle grafieken op deze pagina worden weergegeven voor de meterdimensie die is geselecteerd voor het paginafilter.

## <a name="next-steps"></a>Volgende stappen

- Zie [Analytics voor de commerciële marktplaats voor](./analytics.md)de commerciële marktplaats voor een overzicht van analyserapporten die beschikbaar zijn in de commerciële marktplaats voor het Partnercentrum.
- Zie [Overzichtsdashboard in commerciële marktplaatsanalyses](./summary-dashboard.md)voor grafieken, trends en waarden van geaggregeerde gegevens die de activiteit op de markt voor uw aanbieding samenvatten.
- Zie [Orders Dashboard in commerciële marktplaatsanalyses](./orders-dashboard.md)voor informatie over uw bestellingen in een grafische en downloadbare indeling.
- Zie [Customer Dashboard in commerciële marktplaatsanalyses](./customer-dashboard.md)voor gedetailleerde informatie over uw klanten, inclusief groeitrends.
- Zie [Downloads Dashboard in commerciële marktplaatsanalyses](./downloads-dashboard.md)voor een lijst met uw downloadaanvragen van de afgelopen 30 dagen.
- Zie [Dashboard Beoordelingen en beoordelingen in commerciële marktplaatsanalyses](./ratings-reviews.md)voor ziet een geconsolideerde weergave van feedback van klanten voor aanbiedingen op Azure Marketplace en AppSource.
- Voor veelgestelde vragen over commerciële marktplaatsanalyse en voor een uitgebreid woordenboek van gegevenstermen, zie [veelgestelde vragen en terminologie voor commerciële marktplaatsanalyses.](./faq-terminology.md)
