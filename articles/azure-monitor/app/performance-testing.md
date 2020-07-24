---
title: Prestaties en belasting testen met Azure-toepassing Insights | Microsoft Docs
description: Prestatie-en belasting tests instellen met Azure-toepassing Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 8885a2e511c11317f593706255437e52819adcae
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024347"
---
# <a name="performance-testing"></a>Prestaties testen

> [!NOTE]
> De Cloud service voor belasting tests is afgeschaft. Meer informatie over de afschaffing, de beschik baarheid van de service en alternatieve Services vindt u [hier](/azure/devops/test/load-test/overview?view=azure-devops).

Met Application Insights kunt u belasting tests voor uw websites genereren. Net als bij [beschikbaarheids tests](monitor-web-app-availability.md)kunt u basis aanvragen of [aanvragen voor meerdere stappen](availability-multistep.md) verzenden van Azure test agents over de hele wereld. Met prestatie tests kunt u Maxi maal 20.000 gelijktijdige gebruikers gedurende Maxi maal 60 minuten simuleren.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Als u een prestatie test wilt maken, moet u eerst een Application Insights resource maken. Als u al een resource hebt gemaakt, gaat u door naar de volgende sectie.

Selecteer in de Azure Portal **een resource maken**  >  **Ontwikkelhulpprogramma's**  >  **Application Insights** en maak een Application Insights-resource.

## <a name="configure-performance-testing"></a>Prestatie testen configureren

Als dit de eerste keer is dat u de prestatie test maakt, selecteert u **organisatie instellen** en kiest u een Azure DevOps-organisatie als bron voor uw prestatie testen.

Ga onder **configureren**naar **prestatie testen** en klik op **Nieuw** om een test te maken.

![Vul in elk geval de URL van uw website in](./media/performance-testing/new-performance-test.png)

Als u een basis prestatie test wilt maken, selecteert u een test type **hand matig testen** en vult u de gewenste instellingen voor de test in.

|Instelling| Maximumwaarde
|----------|------------|
| Gebruikers belasting | 20.000 |
| Duur (minuten)  | 60 |  

Nadat de test is gemaakt, klikt u op **test uitvoeren**.

Zodra de test is voltooid, worden de resultaten weer gegeven die er ongeveer als volgt uitzien:

![Testresultaten](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Visual Studio Web Test configureren

Application Insights geavanceerde functies voor prestatie testen zijn gebouwd op het hoogste niveau van Visual Studio-prestaties en het laden van test projecten.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Volgende stappen

* [Webtests met meerdere stappen](availability-multistep.md)
* [URL-ping-tests](monitor-web-app-availability.md)
