---
title: Overzichtsdashboard voor Partner Center-analyses in commerciële marketplace
description: Meer informatie over het openen van grafieken, trends en waarden van geaggregeerde gegevens die de activiteit van de marktplaats samenvattend samenvatten vanuit het dashboard Samenvatting in partnercentrum.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 3da3109bd813fc3b99a4f59e5a357fa351c75394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281371"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Overzichtsdashboard in Commercial Marketplace-analyses

In dit artikel vindt u informatie over het dashboard Samenvatting in het Partnercentrum. In dit dashboard worden grafieken, trends en waarden van geaggregeerde gegevens weergegeven die de activiteit van de marktplaats voor uw aanbiedingen samenvatten.

Als u toegang wilt krijgen tot het dashboard Overzicht, opent u het **[dashboard Analyseren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** onder Commerciële marktplaats.

>[!NOTE]
> Zie [Veelgestelde vragen en terminologie voor commerciële marketplace-analyses voor](./faq-terminology.md)gedetailleerde definities van analytics-terminologie.

## <a name="summary-dashboard"></a>Dashboard voor samenvattingen

Het **overzichtsdashboard** geeft een overzicht op basis van elk aanbiedingstype. **Inzichten** tonen in één oogopslag kritieke informatie en bieden een breed beeld van de verkoopactiviteiten van uw aanbiedingen. U deze rapporten visualiseren via het **dashboard Overzicht.** Dit artikel gaat in meer details over elk van de volgende elementen:

- [Datumbereik](#date-range)
- [Samenvatting sectie](#summary-section)
- [Klanten per geografie](#customers-by-geography)
- [Groeitrendgrafieken](#growth-trend-charts)
- [Klantleaderboard](#customer-leaderboard)
- [Trend van het aantal zitplaatsen](#seat-count-trend)
- [Gratis proeven SaaS orders trend](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementen van het dashboard Samenvatting

In de volgende secties wordt beschreven hoe u het overzichtsdashboard gebruikt en hoe u de gegevens lezen.

### <a name="date-range"></a>Datumbereik

U een datumbereikselectie vinden in de rechterbovenhoek van elke pagina. Pas de uitvoer van de grafieken van de **overzichtspagina** aan door een datumbereik te selecteren op basis van de afgelopen 3, 6 of 12 maanden of door een aangepast datumbereik te selecteren met een maximale duur van 12 maanden. Het standaarddatumbereik is zes maanden.

![Dashboard Voor het analyseren van partnercentrum](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Samenvatting sectie

In de sectie Overzicht wordt een aantal van alle gedane bestellingen weergegeven die zijn gemaakt, klanten zijn aangeschaft en het gebruik dat wordt gerapporteerd tijdens het geselecteerde datumbereik. Gedeeltelijke huidige maand wordt uitgesloten van de berekening van deze statistieken. Bijvoorbeeld: Als u het tijdsbestek van 6 M hebt geselecteerd, worden de gebruiksuren berekend voor de zes maanden voorafgaand aan de huidige maand. Als een aangepast datumbereik is geselecteerd, wordt een gedeeltelijk bedrag van de huidige maand uitgesloten van de berekening.

![Groeitrends in het dashboard Samenvatting](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>Het lezen van de samenvatting sectie

- **Bestellingen**: Aantal gekochte bestellingen (exclusief geannuleerde bestellingen) voor aanbiedingen die u tot nu toe hebt gepubliceerd.
- **Klanten**: Tel van alle klanten die uw aanbiedingen hebben gekocht en ten minste één niet-geannuleerde bestelling hebben.
- **Genormaliseerde gebruiksuren**: gedefinieerd als de gebruiksuren die zijn genormaliseerd om rekening te houden met het aantal VM-kernen ([aantal VM-kernen] x [uren onberauw gebruik]). VM's die als "SHAREDCORE" zijn aangeduid, gebruiken 1/6 (of 0,1666) als de vermenigvuldiger van [aantal VM-kernen].
- **Onbewerkte gebruiksuren**: De hoeveelheid tijd die VM's hebben uitgevoerd in termen van uren. Het percentage waarde naast **het totale aantal orders**, het totale aantal **klanten**, **genormaliseerde gebruiksuren,** **onbewerkte gebruiksuren,** **paginabezoeken**en **call-to-actions** vertegenwoordigen de hoeveelheid gebruiksgroei voor het geselecteerde datumbereik ([gebruik van vorige maand – eerste maandgebruik])/ gebruik van de eerste maand). Zoals hierboven beschreven, wordt een gedeeltelijk bedrag van de huidige maand van deze statistiek uitgesloten.
- **Groeitrends:** Als u de bovendekolommen van de grafiek zweeft, geven staafgrafieken de waarde voor elke maand weer.
- **Groene driehoek naar boven wijst**: Duidt op een positieve groeitrend.
- **Rode driehoek naar beneden gericht**: Duidt op een negatieve groeitrend ten opzichte van de voorgaande maand.

### <a name="customers-by-geography"></a>Klanten per geografie

De **customers by geography** heatmap toont een klanttelling op een wereldkaart.

![Klanten op geografie in overzichtsdashboard](./media/summary-customers-by-geography.png)

- U de kaart verplaatsen om de exacte locatie te bekijken.
- U inzoomen op een specifieke locatie.
- De heatmap heeft een aanvullend raster om de details van het aantal klanten, het aantal bestellingen, de genormaliseerde gebruiksuren op de specifieke locatie te bekijken.
- U zoeken en een land in het raster selecteren om in te zoomen op de locatie op de kaart. Ga terug naar de oorspronkelijke weergave door op de **startknop** in de kaart te drukken.
- Een **nieuwe** klant heeft een van uw aanbiedingen voor het eerst in de loop van de maand binnen het geselecteerde datumbereik gekocht.

### <a name="growth-trend-charts"></a>Groeitrendgrafieken

U trends bekijken op basis van de groei van uw **gekochte bestellingen** (inclusief geannuleerde bestellingen), **klanten die zijn overgenomen** (inclusief verloren klanten) en **gerapporteerd gebruik,** die maand na maand worden weergegeven op basis van het geselecteerde datumbereik. U deze trends verder analyseren door koppelingen onder de grafiek te selecteren, die naar de respectievelijke **pagina's met bestellingen,** **gebruik,** **klant**of **Marketplace Insights** navigeren.

De **Marketplace-aanbieding paginabezoeken en trenddiagrammen voor call-to-action** worden weergegeven voor Azure marketplace en AppSource op twee tabbladen.

![Paginabezoeken en trenddiagrammen voor aanzet tot acties in het overzichtsdashboard](./media/summary-page-visits-and-cta.png)

De **grafiek orders per aanbieding** organiseert uw bestellingen op basis van de naam Aanbieding.

De **orders per verkoopkanaalcirkeldiagram** organiseert uw bestellingen (inclusief bestellingen die klanten hebben geannuleerd) tijdens het geselecteerde datumbereik, per verkoopkanaal. Verkoopkanaal is het type licentieovereenkomst dat door klanten wordt gebruikt om Azure aan te schaffen, namelijk Cloud Solution Provider (CSP), Enterprise, Enterprise via Reseller, GTM en Pay As You Go.

**Gebruik door aanbiedingen** en **gebruik door verkoopkanaal** cirkeldiagrammen presenteren de verdeling van het gebruik door topaanbiedingen en verkoopkanalen, respectievelijk. De grafiek met de binnenste cirkel vertegenwoordigt ruw gebruik en de buitenste cirkeldiagram vertegenwoordigt genormaliseerd gebruik.

De **orders per marktplaatslicentietype** en **het gebruik per marketplace-licentietype** cirkeldiagrammen geven een uitsplitsing van orders en gebruik naar hun respectievelijke licentietype weer. Licentietypen zijn:

- **Gefactureerd via Azure**: Microsoft factureert klanten namens u wanneer u ervoor kiest om uw aanbieding via Microsoft te verkopen met dit licentietype. Betalingstypen omvatten pay-as-you-go via creditcard of Enterprise-facturering.
- **Breng uw eigen licentie:** Microsoft factureert geen klanten voor hun gebruik met dit type marktplaatsaanbieding. Dit gebruik wordt vermeld als **Get it now (Gratis)** in de markt.
- **Gratis:** Microsoft factureert geen klanten voor hun gebruik met dit type marktplaatsaanbieding. Dit gebruik wordt vermeld als **Gratis proefversie** in de markt.
- **Microsoft als reseller**: Vertegenwoordigt aanbiedingen die door Microsoft-resellers worden verkocht als onderdeel van het **CSP-programma (Cloud Solution Provider).**

### <a name="customer-leaderboard"></a>Klantleaderboard

De top 50 klanten met het hoogste aantal orders worden weergegeven op een *klassement*, gesorteerd op hoogste orderaantal en bestelpercentage.

- Selecteer een klant om zijn of haar profielgegevens, orders die zijn georganiseerd op aanbieding of orders te bekijken die zijn georganiseerd op basis van azure-licentietype en prijskanaal.
- De **grafiek Aanbiedingen op bestellingen** presenteert de top vier aanbiedingen (op ordertelling) en de overige aanbiedingen gegroepeerd als 'Rest All'.
- Het **genormaliseerde gebruik door aanbieding** donut grafiek presenteert de top vijf aanbiedingen door gebruik.

> [!NOTE]
> Persoonlijke gegevens van klanten worden alleen gepresenteerd als de klant toestemming heeft gegeven. U deze informatie bekijken als u bent ingelogd met een machtigingsniveau **voor eigenaren.** Gebruikers met de rol **Inzender** kunnen deze informatie niet bekijken. [Meer informatie over gebruikersrollen en machtigingen](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Seat Count Trend

De **orders per stoel/ per site** grafiek presenteert de uitsplitsing van alle bestellingen gekocht volgens het prijsmodel. Het **trenddiagram voor het aantal zitplaatsen** presenteert stoelen ten opzichte van bestellingen die zijn aangeschaft voor al uw software as a Service (SaaS) voor al uw aanbiedingen per stoel.

### <a name="free-trials-saas-orders-trend"></a>Gratis proeven SaaS orders trend

De **Gratis proefperiode SaaS orders trend** grafiek presenteert de trend van bestellingen voor gratis proeven SaaS biedt met een proefperiode van 30 dagen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Analytics voor de commerciële marktplaats voor](./analytics.md)een overzicht van analyserapporten die beschikbaar zijn in het Partnercenter Commercial Marketplace.
- Zie [Orders Dashboard in Commercial Marketplace analytics](./orders-dashboard.md)voor informatie over uw bestellingen in een grafische en downloadbare indeling.
- Zie [Gebruiksdashboard in Commercial Marketplace-analyses](./usage-dashboard.md)voor virtuele machine (VM) voor gebruiks- en factureringsstatistieken met een datameter.
- Zie Customer Dashboard in Commercial Marketplace [analytics](./customer-dashboard.md)voor gedetailleerde informatie over uw klanten, inclusief groeitrends.
- Zie [Downloads Dashboard in Commercial Marketplace analytics](./downloads-dashboard.md)voor een lijst met uw downloadaanvragen van de afgelopen 30 dagen.
- Zie [Dashboard Beoordelingen en beoordelingen in Commercial Marketplace-analyses](./ratings-reviews.md)voor ziet u in een geconsolideerde weergave van feedback van klanten voor aanbiedingen op Azure Marketplace en AppSource.
- Voor veelgestelde vragen over Commercial Marketplace analytics en voor een uitgebreid woordenboek van gegevenstermen, zie [veelgestelde vragen en terminologie voor Commercial Marketplace analytics.](./faq-terminology.md)
