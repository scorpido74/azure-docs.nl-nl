---
title: Samenvattings dashboard voor partner centrum-analyses in commerciële Marketplace
description: Meer informatie over het openen van grafieken, trends en waarden van statistische gegevens die Marketplace-activiteiten samenvatten in het dash board samen vatting van het partner centrum.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: c7f1700cb386a197b33427e2ea615de019284d96
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83738316"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Dashboard Samenvatting in Commerciële marketplace-analyses

Dit artikel bevat informatie over het dash board samen vatting in partner centrum. In dit dash board worden grafieken, trends en waarden van statistische gegevens weer gegeven waarin Marketplace-activiteiten voor uw aanbiedingen worden samenvatten.

Als u het dash board samen vatting wilt openen, opent u het **[dash board analyseren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** onder commerciële Marketplace.

>[!NOTE]
> Zie [Veelgestelde vragen en terminologie voor de analyse van commerciële Marketplace](./faq-terminology.md)voor gedetailleerde definities van analyse terminologie.

## <a name="summary-dashboard"></a>Dashboard voor samenvattingen

Het dash board **samen vatting** geeft een overzicht op basis van elk aanbiedings type. **Inzichten** tonen essentiële informatie in één oogopslag en bieden een uitgebreid overzicht van de verkoop activiteiten van uw aanbiedingen. U kunt deze rapporten visualiseren met behulp van het dash board **samen vatting** . In dit artikel vindt u meer informatie over elk van de volgende elementen:

- [Datum bereik](#date-range)
- [Sectie samen vatting](#summary-section)
- [Klanten op geografie](#customers-by-geography)
- [Diagrammen met groei trends](#growth-trend-charts)
- [Klant Leader Board](#customer-leaderboard)
- [Trend van aantal stoelen](#seat-count-trend)
- [Gratis testen SaaS-bestellingen trend](#free-trials-saas-orders-trend)

## <a name="elements-of-the-summary-dashboard"></a>Elementen van het dash board samen vatting

In de volgende secties wordt beschreven hoe u het dash board samen vatting gebruikt en hoe u de gegevens kunt lezen.

### <a name="date-range"></a>Datumbereik

U kunt in de rechter bovenhoek van elke pagina een selectie voor het datum bereik vinden. De uitvoer van de **overzichts** pagina grafieken aanpassen door een datum bereik te selecteren op basis van de afgelopen 3, 6 of 12 maanden of door een aangepast datum bereik te selecteren met een maximale duur van 12 maanden. Het standaard datum bereik is zes maanden.

![Dash board analyseren in Partner Center](./media/analyze-dashboard.png)

### <a name="summary-section"></a>Sectie samen vatting

In het gedeelte samen vatting ziet u een telling van alle orders die zijn gemaakt, klanten die zijn verkregen en het gebruik dat is gerapporteerd tijdens het geselecteerde datum bereik. Gedeeltelijke huidige maand worden uitgesloten van de berekening van deze metrische gegevens. Bijvoorbeeld: als u het tijds bestek 6 min. hebt geselecteerd, worden de gebruiks uren berekend voor de zes maanden vóór de huidige maand. Als een aangepast datum bereik is geselecteerd, wordt een gedeeltelijke hoeveelheid van de huidige maand uitgesloten van de berekening.

![Groei trends in het dash board samen vatting](./media/summary-summary-section.png)

#### <a name="reading-the-summary-section"></a>De sectie samen vatting lezen

- **Orders**: aantal van alle gekochte orders (exclusief geannuleerde orders) voor aanbiedingen die u tot nu toe hebt gepubliceerd.
- **Klanten**: aantal klanten die uw aanbiedingen hebben gekocht en ten minste één niet-geannuleerde order hebben.
- **Genormaliseerde gebruiks uren**: gedefinieerd als het gebruik van uren die zijn genormaliseerd voor het aantal VM-kernen ([aantal VM-kernen] x [uur van onbewerkt gebruik]). Vm's die zijn aangewezen als "SHAREDCORE" gebruiken 1/6 (of 0,1666) als [aantal VM-kernen] vermenigvuldiger.
- **Onbewerkte gebruiks uren**: de hoeveelheid tijd waarop vm's zijn uitgevoerd in het kader van uren. De percentage waarde naast het **totale aantal orders**, het **totale aantal klanten**, **genormaliseerde gebruiks uren**, **onbewerkte gebruiks uren**, **pagina bezoekers**en **aanroepen naar acties** vertegenwoordigen de hoeveelheid gebruiks groei voor het geselecteerde datum bereik ([vorige maand gebruik – eerste maand gebruik])/eerste maand gebruik). Zoals hierboven beschreven, wordt een gedeeltelijke hoeveelheid van de huidige maand uitgesloten van deze metrische gegevens.
- **Groei trends**: als u de muis aanwijzer boven de kolommen van de grafiek houdt, worden de waarden voor elke maand weer gegeven.
- **Groene, drie hoek omhoog wijzend**: geeft een positieve groei trend aan.
- **Rode drie hoek omlaag wijzend**: geeft een negatieve groei trend aan ten opzichte van de vorige maand.

### <a name="customers-by-geography"></a>Klanten op geografie

De heatmap **klanten** op een wereld kaart geven een aantal klanten weer.

![Klanten per Geografie in het dash board samen vatting](./media/summary-customers-by-geography.png)

- U kunt de kaart verplaatsen om de exacte locatie weer te geven.
- U kunt inzoomen op een specifieke locatie.
- De heatmap heeft een aanvullend raster voor het weer geven van de details van het aantal klanten, het aantal orders, genormaliseerde gebruiks uren op de specifieke locatie.
- U kunt zoeken en een land/regio in het raster selecteren om in te zoomen op de locatie in de kaart. Keer terug naar de oorspronkelijke weer gave door in de kaart op de knop **Start** te drukken.
- Een **nieuwe** klant heeft een van uw aanbiedingen voor de eerste keer in de maand in het geselecteerde datum bereik gekocht.

### <a name="growth-trend-charts"></a>Diagrammen met groei trends

U kunt trends weer geven op basis van de groei van uw **gekochte orders** (inclusief geannuleerde orders), klanten die zijn **verkregen** (inclusief verloren klanten) en gerapporteerde **gebruik** , die per maand worden weer gegeven op basis van het geselecteerde datum bereik. U kunt deze trends verder analyseren door koppelingen onder het diagram te selecteren, die naar de pagina's van de betreffende **order**, het **gebruik**, de **klant**of de **Marketplace Insights** -pagina navigeren.

De pagina met Marketplace-aanbiedingen **en aanroepen naar actie** trend grafieken worden weer gegeven voor Azure Marketplace en AppSource op twee tabbladen.

![Pagina bezoeken en aanroepen naar trend grafieken voor acties in het dash board samen vatting](./media/summary-page-visits-and-cta.png)

In het diagram **Orders per aanbiedingen** worden uw orders geordend op basis van de naam van de aanbieding.

Het cirkel diagram **Orders per verkoop kanaal** organiseert uw orders (inclusief orders die klanten hebben geannuleerd) tijdens het geselecteerde datum bereik op verkoop kanaal. Verkoop kanaal is het type licentie overeenkomst dat door klanten wordt gebruikt voor het aanschaffen van Azure, wat de Cloud Solution Provider (CSP), Enter prise, Enter prise is via wederverkoper, GTM en betalen naar gebruik.

**Gebruik door aanbiedingen** en **gebruik per** cirkel diagram van verkoop kanalen bevat respectievelijk de uitsplitsing van het gebruik door Top-aanbiedingen en verkoop kanalen. Het interne cirkel diagram vertegenwoordigt het gebruik van onbewerkte gegevens en het buitenste cirkel diagram met genormaliseerd gebruik.

De **Orders per Marketplace-licentie type** en **gebruik per Marketplace-licentie type** geven een overzicht van de orders en het gebruik door hun respectieve licentie type. Licentie typen zijn onder andere:

- **Gefactureerd via Azure**: micro soft bewaart klanten namens u wanneer u ervoor kiest om uw aanbieding via micro soft te verkopen met dit licentie type. Betalings typen zijn onder andere betalen per gebruik via credit card of het factureren van een onderneming.
- **Bring your own License**: micro soft houdt geen klanten in rekening voor het gebruik van dit type Marketplace-aanbieding. Dit gebruik wordt weer gegeven als **nu downloaden (gratis)** in Marketplace.
- **Gratis**: micro soft factureert geen klanten voor hun gebruik met dit type Marketplace-aanbieding. Dit gebruik wordt in de Marketplace vermeld als **gratis proef versie** .
- **Micro soft als wederverkoper**: bevat aanbiedingen die door micro soft-wederverkopers worden verkocht als onderdeel van het programma van de **Cloud Solution Provider (CSP)**.

### <a name="customer-leaderboard"></a>Klant Leader Board

De Top 50 klanten met het hoogste aantal orders worden weer gegeven op een *Leader Board*, gesorteerd op het hoogste aantal bestellingen en het percentage van de bestelling.

- Selecteer een klant om de profiel details te bekijken, bestellingen die zijn georganiseerd per aanbieding of de orders die zijn ingedeeld op basis van het Azure-licentie type en het prijs kanaal.
- In de grafiek **aanbiedingen per** order ring worden de vier beste Voorst Ellen weer gegeven (per Bestel aantal) en de resterende aanbiedingen gegroepeerd op als ' rest all '.
- In het **genormaliseerde gebruik per aanbieding** ring diagram worden de vijf beste aanbiedingen weer gegeven.

> [!NOTE]
> Persoonlijke gegevens van de klant worden alleen weer gegeven als de klant toestemming heeft gegeven. U kunt deze informatie weer geven als u bent aangemeld met een **eigenaars** machtiging-niveau. Gebruikers met de rol **Inzender** kunnen deze gegevens niet weer geven. Meer [informatie over gebruikers rollen en machtigingen](./manage-account.md#define-user-roles-and-permissions).

### <a name="seat-count-trend"></a>Trend van aantal stoelen

In het diagram **Orders per seat/per site** ziet u de uitsplitsing van alle orders die zijn gekocht volgens het prijs model. In het **trend diagram aantal** stoelen worden seats en orders weer gegeven die zijn aangeschaft voor al uw SaaS-aanbiedingen (Software as a Service).

### <a name="free-trials-saas-orders-trend"></a>Gratis testen SaaS-bestellingen trend

Het trendanalyse overzicht van de **gratis proef versie van SaaS-orders** geeft de trend van orders voor gratis proef versies SaaS-aanbiedingen met een proef periode van 30 dagen.

## <a name="next-steps"></a>Volgende stappen

- Voor een overzicht van analyse rapporten die beschikbaar zijn in de commerciële Marketplace van partner Center raadpleegt u [Analytics voor de commerciële Marketplace in Partner Center](./analytics.md).
- Voor informatie over uw orders in een grafische en download bare indeling raadpleegt u het [dash board orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Voor virtuele machine (VM) zijn metrische gegevens over gebruik en gefactureerde facturering, Zie [gebruiks dashboard in commerciële Marketplace-analyses](./usage-dashboard.md).
- Zie [klanten dashboard in Commercial Marketplace Analytics](./customer-dashboard.md)voor gedetailleerde informatie over uw klanten, waaronder groei trends.
- Zie [dash board downloaden in Commercial Marketplace Analytics](./downloads-dashboard.md)voor een lijst met Download aanvragen voor de afgelopen 30 dagen.
- Voor een geconsolideerde weer gave van feedback van klanten voor aanbiedingen op Azure Marketplace en AppSource raadpleegt u het [dash board beoordelingen en beoordelingen in Commercial Marketplace Analytics](./ratings-reviews.md).
- Zie [Veelgestelde vragen en terminologie voor de analyse van commerciële Marketplace](./faq-terminology.md)voor veelgestelde vragen over commerciële Marketplace-analyses en voor een uitgebreid woorden boek met gegevens termen.
