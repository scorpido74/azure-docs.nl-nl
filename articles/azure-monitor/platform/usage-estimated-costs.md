---
title: Het gebruik en de geschatte kosten in Azure Monitor bewaken
description: Overzicht van het gebruiksproces op de pagina Azure Monitor-gebruik en geschatte kosten
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658812"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Het gebruik en de geschatte kosten in Azure Monitor bewaken

> [!NOTE]
> In dit artikel wordt beschreven hoe u het gebruik en de geschatte kosten voor meerdere Azure-bewakingsfuncties weergeven. Gerelateerde artikelen voor specifieke onderdelen van Azure Monitor zijn:
> - [Beheer het gebruik en de kosten met Azure Monitor Logs](manage-cost-storage.md) beschrijft hoe u uw kosten beheren door uw bewaarperiode voor gegevens te wijzigen en hoe u uw gegevensgebruik analyseren en waarschuwen.
> - [Beheer het gebruik en de kosten voor Application Insights](../../azure-monitor/app/pricing.md) beschrijft hoe u gegevensgebruik analyseren in Application Insights.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor-prijsmodel

Het basisfactureringsmodel voor Azure Monitor is een cloudvriendelijke, op verbruik gebaseerde prijs ('Pay-As-You-Go'). U betaalt alleen voor wat u gebruikt. Prijsdetails zijn beschikbaar voor [waarschuwingen, statistieken, meldingen,](https://azure.microsoft.com/pricing/details/monitor/) [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) en [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Naast het Pay-As-You-Go-model voor loggegevens, heeft Log Analytics capaciteitsreserveringen, waarmee u maar liefst 25% besparen in vergelijking met de Pay-As-You-Go-prijs. Met de capaciteitsreserveringsprijzen u een reservering kopen vanaf 100 GB/dag. Elk gebruik boven het reserveringsniveau wordt in rekening gebracht tegen het pay-as-you-go-tarief. [Meer informatie](https://azure.microsoft.com/pricing/details/monitor/) over capaciteitsreserveringsprijzen.

Sommige klanten hebben toegang tot [oudere log analytics-prijsniveaus](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) en de [verouderde prijscategorie Enterprise Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier) 

## <a name="understanding-your-azure-monitor-costs"></a>Inzicht in de kosten van uw Azure Monitor

Er zijn twee fasen voor het begrijpen van de kosten. De eerste is wanneer u Azure Monitor als bewakingsoplossing overweegt. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Het schatten van de kosten voor het beheer van uw omgeving

Als u Azure Monitor Logs nog niet gebruikt, u de [azure monitor-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=monitor) gebruiken om de kosten van het gebruik van Azure Monitor te schatten. Begin met het invoeren van 'Azure Monitor' in het vak Zoeken en klik op de resulterende Azure Monitor-tegel. Schuif omlaag naar Azure Monitor en selecteer een van de opties in de vervolgkeuzelijst Type:

- Metrische query's en waarschuwingen  
- Log Analytics
- Application Insights

In elk van deze, de prijsstelling calculator zal u helpen bij het schatten van uw waarschijnlijke kosten op basis van uw verwachte gebruik.

Met Log Analytics u bijvoorbeeld het aantal VM's en de GB aan gegevens invoeren die u van elke vm verwacht te verzamelen. Meestal wordt 1 GB tot 3 GB gegevensmaand ingenomen van een typische Azure VM. Als u Azure Monitor Logs al evalueert, u uw gegevensstatistieken uit uw eigen omgeving gebruiken. Zie hieronder hoe u het [aantal bewaakte VM's](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) en het volume van de [gegevens die uw werkruimte inneemt,](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)bepalen.

Als u voor Application Insights ook de functionaliteit 'Schatting van het gegevensvolume op basis van toepassingsactiviteit' inschakelt, u invoer over uw toepassing (aanvragen per maand en paginaweergaven per maand, voor het geval u telemetrie aan de clientzijde verzamelt), en dan zal de rekenmachine u de mediaan en 90e percentiel hoeveelheid gegevens verzameld door soortgelijke toepassingen vertellen. Deze toepassingen omvatten het bereik van Application Insights-configuratie (bijvoorbeeld sommige hebben standaardsampling, sommige hebben geen steekproeven enz.), dus u hebt nog steeds de controle om het volume van gegevens die u ver onder het mediaanniveau inneemt met behulp van steekproeven te verminderen. Maar dit is een uitgangspunt om te begrijpen wat andere, soortgelijke klanten zien. [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) over het schatten van de kosten voor Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Inzicht in uw gebruik en geschatte kosten

Het is belangrijk om uw gebruik te begrijpen en bij te houden zodra u Azure Monitor gebruikt, en er zijn uitgebreide reeks tools om dit te vergemakkelijken. 

Azure biedt veel nuttige functionaliteit in de hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Nadat u de hub **Azure Cost Management + Billing hebt** geopend, klikt u op **Kostenbeheer** en selecteert u het [bereik](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (de set resources die u wilt onderzoeken). 

Als u vervolgens de kosten van de Azure Monitor van de afgelopen 30 dagen wilt bekijken, klikt u op de tegel **Dagelijkse kosten,** kiest u 'Laatste 30 dagen' onder Relatieve datums en voegt u een filter toe dat de servicenamen selecteert:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Inzicht en analyses

Dit resulteert in een weergave zoals:

![Schermafbeelding van Azure Cost Management](./media/usage-estimated-costs/010.png)

Vanaf hier u inzoomen op deze geaccumuleerde kostensamenvatting om de fijnere details in de weergave 'Kosten per resource' te krijgen. In de huidige prijsniveaus worden Azure Log-gegevens op dezelfde set meters in rekening gebracht, ongeacht of deze afkomstig zijn van Log Analytics of Application Insights. Als u de kosten wilt scheiden van uw gebruik van Logboekanalyse of Toepassingsstatistieken, u een filter toevoegen op **Resourcetype.** Als u alle kosten voor Application Insights wilt bekijken, filtert u het resourcetype op 'microsoft.insights/components' en filtert u voor De kosten van Logboekanalyse resourceop 'microsoft.operationalinsights/workspaces'. 

Meer details over uw gebruik zijn beschikbaar door [uw gebruik te downloaden van de Azure-portal.](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal) In de gedownloade spreadsheet u het gebruik per Azure-bron per dag zien. In deze Excel-spreadsheet u het gebruik van uw resources voor Application Insights vinden door eerst te filteren op de kolom 'Metercategorie' om 'Toepassingsinzichten' en 'Logboekanalyse' weer te geven en vervolgens een filter toe te voegen aan de kolom 'Instantie-id' die 'microsoft.insights/components' bevat.  Het meeste gebruik van Application Insights wordt gerapporteerd op meters met de metercategorie van Log Analytics, omdat er één backend voor logboeken is voor alle Azure Monitor-componenten.  Alleen Application Insights-bronnen voor oudere prijsniveaus en webtests in meerdere stappen worden gerapporteerd met een metercategorie met toepassingsinzichten.  Het gebruik wordt weergegeven in de kolom 'Verbruikt aantal' en de eenheid voor elk item wordt weergegeven in de kolom 'Maateenheid'.  Meer details zijn beschikbaar om u inzicht te [geven in uw Microsoft Azure-factuur.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) 

> [!NOTE]
> Het gebruik van **Kostenbeheer** in de hub **Azure Cost Management + Billing** is de voorkeursbenadering voor een breed begrip van de bewakingskosten.  De ervaringen **met gebruiken en geschatte kosten** voor Log [Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) en [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) bieden diepere inzichten voor elk van deze onderdelen van Azure Monitor.

Een andere optie voor het weergeven van uw Azure Monitor-gebruik is de pagina **Gebruik en geschatte kosten** in de hub Monitor. Hier ziet u het gebruik van kernbewakingsfuncties zoals [waarschuwingen, metrische gegevens, meldingen,](https://azure.microsoft.com/pricing/details/monitor/) [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)en Azure Application [Insights](https://azure.microsoft.com/pricing/details/application-insights/). Voor klanten met de prijsplannen die vóór april 2018 beschikbaar zijn, omvat dit ook het gebruik van Log Analytics dat is aangeschaft via de aanbiedingen Insights and Analytics.

Op deze pagina kunnen gebruikers hun resourcegebruik van de afgelopen 31 dagen bekijken, geaggregeerd per abonnement. `Drill-ins`gebruikstrends over de periode van 31 dagen weer te geven. Veel gegevens moeten samenkomen voor deze schatting, dus wees geduldig als de pagina laadt.

In dit voorbeeld wordt het controlegebruik en een schatting van de resulterende kosten weergegeven:

![Schermafbeelding van gebruiks- en geschatte kostenportal](./media/usage-estimated-costs/001.png)

Selecteer de koppeling in de kolom maandelijks gebruik om een grafiek te openen die gebruikstrends over de laatste periode van 31 dagen weergeeft: 

![Schermafbeelding van schermafbeelding van een staafdiagram per knooppunt](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite-abonnementsrechten

Klanten die Microsoft Operations Management Suite E1 en E2 hebben gekocht, komen in aanmerking voor rechten voor gegevensopname per knooppunt voor [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) en [Application Insights.](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) Ga als volgende voor de volgende rechten voor Log Analytics-werkruimten of Toepassingsstatistieken bronnen in een bepaald abonnement: 

- Log Analytics-werkruimten moeten de prijslaag 'Per knooppunt (OMS)' gebruiken.
- Application Insights-bronnen moeten de prijslaag 'Enterprise' gebruiken.

Afhankelijk van het aantal knooppunten van de suite dat uw organisatie heeft gekocht, kan het voordelig zijn om bepaalde abonnementen naar een prijscategorie voor betalen per gebruik (Per GB) te verplaatsen, maar dit vereist een zorgvuldige afweging.

> [!WARNING]
> Als uw organisatie de huidige Microsoft Operations Management Suite E1 en E2 heeft, is het meestal het beste om uw Log Analytics-werkruimten in de prijscategorie 'Per-node (OMS)' en uw Toepassingsinzichten in de prijscategorie 'Enterprise' te houden. 
>
