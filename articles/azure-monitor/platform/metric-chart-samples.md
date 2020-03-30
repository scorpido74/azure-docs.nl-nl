---
title: Voorbeeld van azure monitor-metrische grafiek
description: Meer informatie over het visualiseren van uw Azure Monitor-gegevens.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 1aaeb853a67b36a21a09db57e015029d10cd0c36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660867"
---
# <a name="metric-chart-examples"></a>Voorbeelden van metrische grafieken 

Het Azure-platform biedt [meer dan duizend statistieken,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)waarvan er vele dimensies hebben. Door [dimensiefilters](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)te gebruiken, [splitsen](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)toe te passen, grafiektype te beheren en grafiekinstellingen aan te passen, u krachtige diagnostische weergaven en dashboards maken die inzicht geven in de status van uw infrastructuur en toepassingen. In dit artikel worden enkele voorbeelden weergegeven van de grafieken die u maken met [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) en worden de noodzakelijke stappen uitgelegd om elk van deze grafieken te configureren.

Wilt u uw grote grafieken voorbeelden delen met de wereld? Draag bij aan deze pagina op GitHub en deel hier uw eigen grafiekvoorbeelden!

## <a name="website-cpu-utilization-by-server-instances"></a>Cpu-gebruik website door serverinstanties

Deze grafiek geeft aan of CPU voor een App-service binnen het acceptabele bereik was en splitst deze op naar exemplaar om te bepalen of de belasting goed is verdeeld. U in de grafiek zien dat de app vóór 06.00 uur op één serverexemplaar werd uitgevoerd en vervolgens worden opgeschaald door een ander exemplaar toe te voegen.

![Lijndiagram van gemiddeld cpu-percentage per serverinstantie](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Hoe configureer je deze grafiek?

Selecteer uw App Service-bron en zoek de statistiek **CPU-percentage.** Klik vervolgens op **Splitsen toepassen** en selecteer de **dimensie Instantie.**

## <a name="application-availability-by-region"></a>Beschikbaarheid van toepassingen per regio

Bekijk de beschikbaarheid van uw toepassing per regio om te bepalen welke geografische locaties problemen hebben. In deze grafiek ziet u de beschikbaarheidsstatistiek Application Insights. U zien dat de bewaakte toepassing geen probleem heeft met de beschikbaarheid van het Datacenter in de Oost-VS, maar dat er een probleem is met gedeeltelijke beschikbaarheid vanuit West-VS en Oost-Azië.

![Grafiek van de gemiddelde beschikbaarheid per locatie](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Hoe configureer je deze grafiek?

U moet eerst [de beschikbaarheidsbewaking](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) van Application Insights voor uw website inschakelen. Kies daarna de resource Application Insights en selecteer de statistiek Beschikbaarheid. Splitsen toepassen op de dimensie **Locatie uitvoeren.**

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volume van opslagrekeningtransacties op API-naam

Uw opslagaccountbron ondervindt een te groot volume aan transacties. U de transactiestatistiek gebruiken om te bepalen welke API verantwoordelijk is voor de overtollige belasting. De volgende grafiek is geconfigureerd met dezelfde dimensie (API-naam) bij het filteren en splitsen om de weergave te beperken tot alleen de API-aanroepen van het belang:

![Staafdiagram van API-transacties](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Hoe configureer je deze grafiek?

Selecteer in de metrische kiezer uw opslagaccount en de statistiek **Transacties.** Grafiektype overschakelen naar **Staafdiagram**. Klik **op Splitsen toepassen** en selecteer de naam van de **dimensie-API**. Klik vervolgens op het **filter Toevoegen** en kies nogmaals de dimensie **api-naam.** Selecteer in het filterdialoogvenster de API's die u in de grafiek wilt plotten.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure [Monitor-werkmappen](../../azure-monitor/app/usage-workbooks.md)
* Meer informatie over [Metric Explorer](metrics-charts.md)
