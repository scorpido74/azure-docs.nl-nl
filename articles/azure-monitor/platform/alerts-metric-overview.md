---
title: Begrijpen hoe metrische waarschuwingen werken in Azure Monitor.
description: Krijg een overzicht van wat u doen met metrische waarschuwingen en hoe ze werken in Azure Monitor.
ms.date: 03/17/2020
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: b6d8bc69a407838025c5e78e0a1c773ab457c409
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480229"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Begrijpen hoe waarschuwingen voor metrische gegevens werken in Azure Monitor

Metrische waarschuwingen in Azure Monitor werken bovenop multidimensionale statistieken. Deze statistieken kunnen [platformstatistieken,](alerts-metric-near-real-time.md#metrics-and-dimensions-supported) [aangepaste statistieken,](../../azure-monitor/platform/metrics-custom-overview.md) [populaire logboeken van Azure Monitor zijn die zijn geconverteerd naar statistieken](../../azure-monitor/platform/alerts-metric-logs.md) en Statistieken voor Toepassingsstatistieken. Metrische waarschuwingen evalueren regelmatig om te controleren of de omstandigheden op een of meer metrische tijdreeksen waar zijn en stellen u op de hoogte wanneer aan de evaluaties is voldaan. Metrische waarschuwingen zijn stateful, dat wil zeggen, ze sturen alleen meldingen wanneer de status verandert.

## <a name="how-do-metric-alerts-work"></a>Hoe werken metrische waarschuwingen?

U een metrische waarschuwingsregel definiëren door een te controleren doelbron, metrische naam, voorwaardetype (statisch of dynamisch) en de voorwaarde (een operator en een drempel/gevoeligheid) en een actiegroep op te geven die moet worden geactiveerd wanneer de waarschuwingsregel wordt geactiveerd. Voorwaardentypen beïnvloeden de manier waarop drempelwaarden worden bepaald. [Meer informatie over het voorwaardetype dynamische drempels en gevoeligheidsopties](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Waarschuwingsregel met statisch voorwaardetype

Stel dat u als volgt een eenvoudige statische drempelmetrische waarschuwingsregel hebt gemaakt:

- Doelbron (de Azure-bron die u wilt controleren): myVM
- Statistiek: Percentage CPU
- Voorwaardetype: statisch
- Tijdaggregatie (Statistiek die wordt uitgevoerd over ruwe metrische waarden. Ondersteunde tijdsaggregaties zijn Min, Max, Avg, Totaal, Aantal): Gemiddeld
- Periode (Het terugblikvenster waarover metrische waarden worden gecontroleerd): In de afgelopen 5 minuten
- Frequentie (de frequentie waarmee de metrische waarschuwing controleert of aan de voorwaarden is voldaan): 1 min
- Operator: groter dan
- Drempel: 70

Vanaf het moment dat de waarschuwingsregel wordt gemaakt, wordt de monitor elke 1 minuten uitgevoerd en wordt de metrische waarden van de laatste 5 minuten bekeken en wordt gecontroleerd of het gemiddelde van die waarden hoger is dan 70. Als aan de voorwaarde is voldaan, dat wil zeggen dat het gemiddelde percentage CPU voor de laatste 5 minuten hoger is dan 70, wordt een geactiveerde melding geactiveerd. Als u een e-mail of een webhaakactie hebt geconfigureerd in de actiegroep die is gekoppeld aan de waarschuwingsregel, ontvangt u op beide een geactiveerde melding.

Wanneer u meerdere voorwaarden in één regel gebruikt, "ands" de regel samen.  Dat wil zeggen, de waarschuwing branden wanneer alle voorwaarden in de waarschuwing te evalueren als waar en op te lossen wanneer een van de voorwaarden niet langer waar is. En voorbeeld van dit type waarschuwing zou alert zijn wanneer "CPU hoger dan 90%" en "wachtrij lengte is meer dan 300 items". 

### <a name="alert-rule-with-dynamic-condition-type"></a>Waarschuwingsregel met dynamisch voorwaardetype

Stel dat u als volgt een eenvoudige regel dynamische drempelwaarden hebt gemaakt:

- Doelbron (de Azure-bron die u wilt controleren): myVM
- Statistiek: Percentage CPU
- Voorwaardetype: Dynamisch
- Tijdaggregatie (Statistiek die wordt uitgevoerd over ruwe metrische waarden. Ondersteunde tijdsaggregaties zijn Min, Max, Avg, Totaal, Aantal): Gemiddeld
- Periode (Het terugblikvenster waarover metrische waarden worden gecontroleerd): In de afgelopen 5 minuten
- Frequentie (de frequentie waarmee de metrische waarschuwing controleert of aan de voorwaarden is voldaan): 1 min
- Operator: groter dan
- Gevoeligheid: Gemiddeld
- Terugblik periodes: 4
- Aantal overtredingen: 4

Zodra de waarschuwingsregel is gemaakt, verwerft het machine learning-algoritme Dynamische drempels historische gegevens die beschikbaar zijn, berekent u de drempelwaarde die het beste past bij het gedragspatroon van de metrische reeks en leert het voortdurend op basis van nieuwe gegevens om de drempel nauwkeuriger.

Vanaf het moment dat de waarschuwingsregel wordt gemaakt, wordt de monitor elke 1 min uitgevoerd en wordt de metrische waarden in de laatste 20 minuten gegroepeerd in perioden van 5 minuten en wordt gecontroleerd of het gemiddelde van de periodewaarden in elk van de 4 perioden de verwachte drempel overschrijdt. Als aan de voorwaarde is voldaan, dat wil zeggen dat de gemiddelde CPU-percentage in de laatste 20 minuten (vier perioden van 5 minuten) vier keer van verwacht gedrag is afgeweken, wordt een geactiveerde melding geactiveerd. Als u een e-mail of een webhaakactie hebt geconfigureerd in de actiegroep die is gekoppeld aan de waarschuwingsregel, ontvangt u op beide een geactiveerde melding.

### <a name="view-and-resolution-of-fired-alerts"></a>Het weergeven en oplossen van ontslagen waarschuwingen

De bovenstaande voorbeelden van het afvuren van waarschuwingsregels kunnen ook worden weergegeven in de Azure-portal in het blade **Alle waarschuwingen.**

Stel dat het gebruik op "myVM" blijft boven de drempel in de volgende controles, zal de waarschuwingsregel niet opnieuw branden totdat de voorwaarden zijn opgelost.

Na enige tijd, het gebruik op "myVM" komt terug naar normaal (gaat onder de drempel). De waarschuwingsregel controleert de voorwaarde nog twee keer om een opgeloste melding uit te sturen. De waarschuwingsregel stuurt een opgelost/gedeactiveerd bericht wanneer drie opeenvolgende perioden niet aan de waarschuwingsvoorwaarde wordt voldaan om ruis te verminderen in geval van klapperende omstandigheden.

Aangezien de opgeloste melding wordt verzonden via webhaken of e-mail, is de status van de waarschuwingsinstantie (de zogenaamde monitorstatus) in Azure-portal ook ingesteld op opgelost.

### <a name="using-dimensions"></a>Afmetingen gebruiken

Metrische waarschuwingen in Azure Monitor ondersteunen ook het bewaken van meerdere dimensionswaardecombinaties met één regel. Laten we begrijpen waarom u meerdere dimensiecombinaties gebruiken met behulp van een voorbeeld.

Stel dat u een App Service-abonnement voor uw website hebt. U wilt het CPU-gebruik controleren op meerdere exemplaren waarop uw website/app wordt uitgevoerd. U dat als volgt doen met een metrische waarschuwingsregel:

- Doelbron: myAppServicePlan
- Statistiek: Percentage CPU
- Voorwaardetype: statisch
- Dimensies
  - Instantie = InstanceName1, InstanceName2
- Tijdaggregatie: Gemiddeld
- Periode: In de laatste 5 minuten
- Frequentie: 1 min
- Operator: Groter dan
- Drempel: 70

Net als voorheen, deze regel controleert als de gemiddelde CPU-gebruik voor de laatste 5 minuten meer dan 70%. Met dezelfde regel u echter twee exemplaren controleren waarop uw website wordt uitgevoerd. Elk exemplaar wordt individueel gecontroleerd en u ontvangt meldingen afzonderlijk.

Stel dat u een web-app die ziet enorme vraag en je moet meer exemplaren toe te voegen. De bovenstaande regel controleert nog steeds slechts twee exemplaren. U echter een regel als volgt maken:

- Doelbron: myAppServicePlan
- Statistiek: Percentage CPU
- Voorwaardetype: statisch
- Dimensies
  - Instantie = *
- Tijdaggregatie: Gemiddeld
- Periode: In de laatste 5 minuten
- Frequentie: 1 min
- Operator: Groter dan
- Drempel: 70

Deze regel controleert automatisch alle waarden voor de instantie d.w.z. u uw instanties controleren wanneer ze naar boven komen zonder dat u uw metrische waarschuwingsregel opnieuw hoeft te wijzigen.

Bij het bewaken van meerdere dimensies kan de regel dynamische drempelwaarden voor honderden metrische reeksen tegelijk maken. Dynamische drempels resulteren in minder waarschuwingsregels om te beheren en aanzienlijke tijdsbesparing op het beheer en het maken van waarschuwingsregels.

Stel dat u een web-app met veel exemplaren en je weet niet wat de meest geschikte drempel is. De bovenstaande regels zullen altijd gebruik maken van een drempel van 70%. U echter een regel als volgt maken:

- Doelbron: myAppServicePlan
- Statistiek: Percentage CPU
- Voorwaardetype: Dynamisch
- Dimensies
  - Instantie = *
- Tijdaggregatie: Gemiddeld
- Periode: In de laatste 5 minuten
- Frequentie: 1 min
- Operator: Groter dan
- Gevoeligheid: Gemiddeld
- Terugblik periodes: 1
- Aantal overtredingen: 1

Deze regel houdt in of het gemiddelde CPU-gebruik van de afgelopen 5 minuten het verwachte gedrag voor elke instantie overschrijdt. Dezelfde regel die u controleren als deze worden opgesteld zonder dat u uw metrische waarschuwingsregel opnieuw hoeft te wijzigen. Elke instantie krijgt een drempelwaarde die past bij het gedragspatroon van de metrische reeks en verandert voortdurend op basis van nieuwe gegevens om de drempel nauwkeuriger te maken. Net als voorheen wordt elk exemplaar afzonderlijk gecontroleerd en ontvangt u meldingen afzonderlijk.

Het verhogen van de terugkijkperioden en het aantal overtredingen kunnen ook het filteren van waarschuwingen mogelijk maken om alleen te waarschuwen voor uw definitie van een significante afwijking. [Meer informatie over geavanceerde dynamische drempels .](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean)

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Bewaken op schaal met metrische waarschuwingen in Azure Monitor

Tot nu toe hebt u gezien hoe een enkele metrische waarschuwing kan worden gebruikt om een of meer metrische tijdreeksen met betrekking tot één Azure-bron te controleren. Vaak wilt u mogelijk dezelfde waarschuwingsregel die op veel resources wordt toegepast. Azure Monitor ondersteunt ook het bewaken van meerdere resources (van hetzelfde type) met één metrische waarschuwingsregel, voor resources die in dezelfde Azure-regio bestaan. 

Deze functie wordt momenteel ondersteund voor platformstatistieken (geen aangepaste statistieken) voor de volgende services in de volgende Azure-clouds:

| Service | Openbaar Azure | Overheid | China |
|:--------|:--------|:--------|:--------|
| Virtuele machines  | **Ja** | Nee | Nee |
| SQL-serverdatabases | **Ja** | **Ja** | Nee |
| Elastische groepen van SQL-server | **Ja** | **Ja** | Nee |
| Randapparaten voor gegevensvakrand | **Ja** | **Ja** | Nee |

U het bereik van de bewaking op drie manieren opgeven met één metrische waarschuwingsregel. Bij virtuele machines u bijvoorbeeld het bereik opgeven als:  

- een lijst met virtuele machines in één Azure-regio binnen een abonnement
- alle virtuele machines (in één Azure-regio) in een of meer resourcegroepen in een abonnement
- alle virtuele machines (in één Azure-regio) in één abonnement

Het maken van metrische waarschuwingsregels die meerdere resources bewaken is als [het maken van een andere metrische waarschuwing](alerts-metric.md) die één resource bewaakt. Het enige verschil is dat u alle resources selecteert die u wilt controleren. U deze regels ook maken via [Azure Resource Manager-sjablonen.](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources) U ontvangt individuele meldingen voor elke bewaakte resource.

## <a name="typical-latency"></a>Typische latentie

Voor metrische waarschuwingen ontvangt u meestal binnen 5 minuten een melding als u de waarschuwingsregelfrequentie op 1 min instelt. In geval van zware belasting voor meldingssystemen ziet u mogelijk een langere latentie.

## <a name="supported-resource-types-for-metric-alerts"></a>Ondersteunde resourcetypen voor metrische waarschuwingen

In dit [artikel](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)vindt u de volledige lijst met ondersteunde brontypen.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het maken, weergeven en beheren van metrische waarschuwingen in Azure](alerts-metric.md)
- [Informatie over het implementeren van metrische waarschuwingen met Azure Resource Manager-sjablonen](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Meer informatie over actiegroepen](action-groups.md)
- [Meer informatie over het voorwaardetype Dynamische drempels](alerts-dynamic-thresholds.md)
