---
title: Afwijkings detectie service voor facturering met data limiet-Microsoft Azure Marketplace
description: Hierin wordt beschreven hoe afwijkings detectie werkt, wanneer meldingen worden verzonden en wat u ermee kunt doen, en ondersteunings opties.
author: anbene
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: b83b6fc0da6a8b2016676d7fb748fc87880868f3
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964563"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Afwijkings detectie service voor facturering met data limiet

Met de [Marketplace-meet service](marketplace-metering-service-apis-faq.md) kunt u aanbiedingen maken in het commerciële Marketplace-programma dat wordt gefactureerd op basis van niet-standaard eenheden. Met facturering met data limieten verzendt u gebruiks gebeurtenissen voor het gebruik van uw klant naar micro soft en wordt de facturering voor bereid op basis van het gebruik.

Onjuiste gebruiks gegevens kunnen afkomstig zijn uit verschillende oorzaken, zoals bugs, verkeerde configuratie in uw verbruiks tracking of fraude. Onjuiste gebruiks gegevens leiden tot onjuiste kosten voor klanten en facturerings geschillen.

Om het risico te beperken, past onze afwijkende detectie service machine learning algoritmen toe om het normale facturerings gedrag met data limiet te bepalen, het gebruik van de gefactureerde facturering te analyseren en afwijkingen te detecteren met minimale tussen komst van de gebruiker.

U ontvangt een melding als er sprake is van een afwijkend overzicht in het facturerings gebruik met data limiet. Dit biedt u de mogelijkheid om ons te onderzoeken en hiervan op de hoogte te stellen als er een afwijkend probleem is bevestigd, op welk punt acties kunnen worden uitgevoerd om het probleem van de klant facturering proactief te aanpakken.

Naast plotselinge pieken, spannings dips en trend veranderingen van het gebruik van gefactureerd facturerings model, hebben we ook accounts voor seizoen effecten gebruikt. Omdat facturering via data limiet wordt gecommuniceerd via overschrijding-gegevens, kan ons model ook een lange periode van ontbrekende gegevens verwerken.

Hieronder vindt u voor beelden van anomalie detectie resultaten. Het verwachte bereik wordt weer gegeven als een gele band. Een geaccepteerd factuur gebruik in de data limiet wordt weer gegeven als groene sterren in de band. Facturerings gebruik buiten de band wordt weer gegeven als een rode stip.  

Afwijkingen gedetecteerd buiten een voorspel bare trend:

![Illustreert afwijkingen die buiten een voorspel bare trend zijn gedetecteerd.](media/anomaly-1.png)

Afwijkingen gedetecteerd buiten een terugkerende cyclische trend:

![Illustreert afwijkingen die buiten een terugkerende cyclische trend zijn gedetecteerd.](media/anomaly-2.png)

Afwijkingen gedetecteerd in een opwaartse trend:

![Illustreert afwijkingen die in een opwaartse trend zijn gedetecteerd.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Hoe afwijkings detectie service werkt

Anomalie detectie wordt automatisch ingeschakeld voor al het facturerings gebruik met data limiet. Wanneer u de gebruiks gebeurtenissen naar micro soft verzendt, maakt de afwijkings detectie service een model van de verwachte waarden op basis van de gegevens in het verleden. Dit model wordt wekelijks uitgevoerd.

Anomalie detectie werkt op een niveau per meter en per klant. Dit betekent dat elke-meter met elke klant een model heeft getraind op basis van het vorige gebruiks patroon van deze klant van deze meter.

Het model werkt door het genereren van interval voor retroactieve vertrouwen. De time series-prognose is een gegeneraliseerd additief model dat bestaat uit een trend Voorspellings onderdeel en een seizoensgebonden onderdeel. Omdat het model is geformuleerd als een regressie taak, kan het een goede periode van ontbrekende gegevens op de juiste manier verwerken. Als een waarneming buiten de voorspelde betrouwbaarheids intervallen valt, betekent dit dat waarneming niet kan worden uitgelegd op basis van historische patronen van de facturering met data limieten. Dit kan daarom een afwijkend probleem zijn.

## <a name="anomaly-detection-notification"></a>Melding over afwijkings detectie

Er worden wekelijks meldingen over afwijkings detectie per week verzonden. Het bevat alle afwijkingen die in de week voor alle meters en klanten zijn gedetecteerd. Dit e-mail bericht wordt verzonden naar de **technische** en **ondersteunings** contacten die zijn verschaft tijdens het maken van de aanbieding.

U wordt verwacht te onderzoeken of gedetecteerde afwijkingen echte problemen zijn en als dit het geval is, neem dan contact op met micro soft om het onjuiste gebruik te melden (Zie de sectie ondersteuning hieronder).

Als u bevestigt dat gedetecteerde afwijkingen normaal gebruik zijn, is er geen verdere actie nodig. Als een afwijkend mogelijk groot financieel risico oplevert, kunnen we echter contact met u opnemen om het gebruik te bevestigen.  

## <a name="when-and-how-to-get-support"></a>Wanneer en hoe u ondersteuning krijgt

Als u het verkeerde gebruik hebt verzonden naar micro soft en dit hebt gedaan, of als gevolg van de klant, zal micro soft geen factuur voor de klant initiëren voor gebruik onder het rapport of u betalen voor dat gebruik. U moet rekening houden met het verlies van inkomsten als gevolg van rapportages.

Als een van de volgende gevallen van toepassing is, kunt u een ondersteunings ticket openen om terugbetaling of facturerings correctie voor uw klanten aan te vragen:

- U hebt bevestigd dat een van de gevonden afwijkingen een echt probleem is en het onjuiste gebruik zou leiden tot **overbelasting** van de klant.
- U ontdekt dat u onjuist gebruik naar ons hebt verzonden en dat het onjuiste gebruik zou leiden tot **overbelasting** van de klant.
- U wilt een terugbetaling aanvragen voor de kosten van het facturerings gebruik van uw klant.

Een ticket verzenden:

1. Ga naar de ondersteunings pagina. Voer in het **vak vertel ons over uw probleem**' onjuist gebruik ' in.
2. Selecteer in ondersteunings onderwerpen een van de volgende opties in de vervolg keuzelijst van zoek resultaten:
    - **Commerciële Marketplace**  >  Factuur met data **limiet**  >  **Onjuist gebruik verzonden voor de aanbieding van Azure-toepassingen**of
    - **Commerciële Marketplace**  >  Factuur met data **limiet**  >  **Onjuist gebruik verzonden voor SaaS-aanbieding**
3. Onder **volgende stap**selecteert u de knop **oplossingen controleren** om u aan te melden bij het partner centrum om een ondersteunings ticket in te dienen.

Voor meer ondersteunings opties voor Publisher raadpleegt u [ondersteuning voor het programma voor commerciële Marketplace in Partner Center](support.md).

## <a name="next-step"></a>Volgende stap

- Meer informatie over de [Marketplace meter Service-API](marketplace-metering-service-apis.md).
