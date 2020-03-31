---
title: Testen van prestaties en belasting met Azure Application Insights | Microsoft Documenten
description: Prestatie- en belastingtests instellen met Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669859"
---
# <a name="performance-testing"></a>Prestaties testen

> [!NOTE]
> De cloudgebaseerde load testing-service is afgeschaft. Meer informatie over de afschaffing, de beschikbaarheid van de service en alternatieve diensten vindt u [hier.](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops)

Met Application Insights u belastingstests genereren voor uw websites. Net als [beschikbaarheidstests](monitor-web-app-availability.md)u basisaanvragen of aanvragen met meerdere stappen van [Azure-testagents](availability-multistep.md) over de hele wereld verzenden. Met prestatietests u tot 20.000 gelijktijdige gebruikers simuleren gedurende maximaal 60 minuten.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Als u een prestatietest wilt maken, moet u eerst een Application Insights-bron maken. Als u al een resource hebt gemaakt, gaat u door naar de volgende sectie.

Selecteer in de Azure-portal de optie**AppInsights voor** **hulpprogramma's voor hulpprogramma's** > **Developer Tools** > voor resources maken en maak een Application Insights-bron.

## <a name="configure-performance-testing"></a>Prestatietesten configureren

Als dit de eerste keer is dat u prestatietest maakt, selecteert u **Organisatie instellen** en kiest u een Azure DevOps-organisatie als bron voor uw prestatietests.

Ga **onder Configureren**naar **Prestatietesten** en klik op **Nieuw** om een test te maken.

![Vul in elk geval de URL van uw website in](./media/performance-testing/new-performance-test.png)

Als u een basisprestatietest wilt maken, selecteert u een testtype **handmatige test** en vult u de gewenste instellingen voor uw test in.

|Instelling| Maximumwaarde
|----------|------------|
| Gebruikersbelasting | 20.000 |
| Duur (minuten)  | 60 |  

Nadat de test is gemaakt, klikt u op **Test uitvoeren**.

Zodra de test is voltooid, ziet u resultaten die lijken op de resultaten hieronder:

![Testresultaten](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Visual Studio-webtest configureren

De geavanceerde mogelijkheden voor het testen van prestaties van Application Insights zijn gebaseerd op de prestaties en belastingtestprojecten van Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Volgende stappen

* [Webtests met meerdere stappen](availability-multistep.md)
* [Url-pingtests](monitor-web-app-availability.md)
