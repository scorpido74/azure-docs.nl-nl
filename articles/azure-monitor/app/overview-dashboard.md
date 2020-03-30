---
title: Overzichtsdashboard Azure Application Insights | Microsoft Documenten
description: Beheer toepassingen met Azure Application Insights en Overview Dashboard-functionaliteit.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e5188972d9058b85a9765c7d33f6209b37245d7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669893"
---
# <a name="application-insights-overview-dashboard"></a>Overzichtsdashboard voor toepassingsinzichten

Application Insights heeft altijd een overzichtsvenster met een overzicht som gemaakt, zodat de status en prestaties van uw toepassing in één oogopslag snel en in één oogopslag kunnen worden beoordeeld. Het nieuwe overzichtsdashboard biedt een snellere flexibelere ervaring.

## <a name="how-do-i-test-out-the-new-experience"></a>Hoe test ik de nieuwe ervaring?

Het nieuwe overzichtsdashboard wordt nu standaard gelanceerd:

![Deelvenster Voorbeeld van overzicht](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Betere prestaties

De selectie van tijdsbereik is vereenvoudigd tot een eenvoudige interface met één klik.

![Tijdsbereik](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

De algehele prestaties zijn sterk toegenomen. Je hebt met één klik toegang tot populaire functies zoals **Zoeken** en **Analytics.** Elke standaard-KPI-tegel die dynamisch wordt bijgewerkt, biedt inzicht in de bijbehorende Application Insights-functies. Selecteer **Fouten** onder de kop **Onderzoeken** voor meer informatie over mislukte aanvragen:

![Fouten](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Toepassingsdashboard

Het toepassingsdashboard maakt gebruik van de bestaande dashboardtechnologie binnen Azure om een volledig aanpasbare weergave van uw toepassingsstatus en -prestaties te bieden.

Als u toegang wilt krijgen tot het standaarddashboard, selecteert u _Toepassingsdashboard_ in de linkerbovenhoek.

![Dashboardweergave](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Als dit de eerste keer is dat u toegang krijgt tot het dashboard, wordt een standaardweergave gestart:

![Dashboardweergave](./media/overview-dashboard/0001-dashboard.png)

U de standaardweergave behouden als u deze leuk vindt. Of u ook toevoegen, en verwijderen uit het dashboard om het beste te passen bij de behoeften van uw team.

> [!NOTE]
> Alle gebruikers met toegang tot de Application Insights-bron delen dezelfde toepassingsdashboardervaring. Wijzigingen die door één gebruiker zijn aangebracht, wijzigen de weergave voor alle gebruikers.

Als u terug wilt navigeren naar de overzichtservaring selecteert u het volgende:

![Knop Overzicht](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Problemen oplossen

Als u **tegelinstellingen configureren** selecteert en een aangepast tijdsbereik van meer dan 31 dagen instelt, wordt uw dashboard niet langer dan 31 dagen weergegeven, zelfs niet met de standaardgegevensretentie van 90 dagen. Er is momenteel geen tijdelijke oplossing voor dit gedrag.

## <a name="next-steps"></a>Volgende stappen

- [Trechters](../../azure-monitor/app/usage-funnels.md)
- [Retentie](../../azure-monitor/app/usage-retention.md)
- [Gebruikersstromen](../../azure-monitor/app/usage-flows.md)
