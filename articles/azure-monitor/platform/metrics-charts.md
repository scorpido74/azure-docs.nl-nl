---
title: Geavanceerde functies van Azure Metrics Explorer
description: Meer informatie over geavanceerde functies van Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 2df1e0bb7d586edb13dc86e163f0e5728608d2a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371605"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Geavanceerde functies van Azure Metrics Explorer

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u bekend bent met basisfuncties van Metrics Explorer. Zie [Aan de slag met Azure Metrics Explorer](metrics-getting-started.md)als u een nieuwe gebruiker bent en meer wilt weten over het maken van uw eerste metrische grafiek.

## <a name="metrics-in-azure"></a>Statistieken in Azure

[Statistieken in Azure Monitor](data-platform-metrics.md) zijn de reeks gemeten waarden en tellingen die in de loop van de tijd worden verzameld en opgeslagen. Er zijn standaardstatistieken (of 'platform') en aangepaste statistieken. De standaardstatistieken worden u aangeboden door het Azure-platform zelf. Standaardstatistieken geven de status- en gebruiksstatistieken van uw Azure-bronnen weer. Terwijl aangepaste statistieken naar Azure worden verzonden door uw toepassingen met behulp van de [Application Insights API voor aangepaste gebeurtenissen en statistieken](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), Windows Azure Diagnostics [(WAD) extensie,](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)of door [Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Weergaven maken met meerdere statistieken en grafieken

U grafieken maken die meerdere metrische lijnen uitstippelen of meerdere metrische grafieken tegelijk weergeven. Met deze functionaliteit u:

- gerelateerde statistieken op dezelfde grafiek correleren om te zien hoe de ene waarde gerelateerd is aan een andere
- statistieken weergeven met verschillende maateenheden in de nabijheid
- statistieken uit meerdere bronnen visueel samenvoegen en vergelijken

Als u bijvoorbeeld 5 opslagaccounts hebt en u wilt weten hoeveel totale ruimte er tussen hen wordt verbruikt, u een (gestapeld) gebiedsdiagram maken waarin de persoon en de som van alle waarden op bepaalde punten in de tijd worden weergegeven.

### <a name="multiple-metrics-on-the-same-chart"></a>Meerdere statistieken op dezelfde grafiek

Maak eerst [een nieuwe grafiek](metrics-getting-started.md#create-your-first-metric-chart). Klik **op Metric toevoegen** en herhaal de stappen om een andere statistiek aan dezelfde grafiek toe te voegen.

   > [!NOTE]
   > U wilt meestal geen metrische gegevens hebben met verschillende maateenheden (d.w.z. "milliseconden" en "kilobytes") of met een aanzienlijk andere schaal op één grafiek. In plaats daarvan u overwegen meerdere grafieken te gebruiken. Klik op de knop Grafiek toevoegen om meerdere grafieken te maken in de verkenner.

### <a name="multiple-charts"></a>Meerdere grafieken

Klik **op** de grafiek Toevoegen en maak een andere grafiek met een andere statistiek.

### <a name="order-or-delete-multiple-charts"></a>Meerdere grafieken ordenen of verwijderen

Als u meerdere grafieken wilt ordenen of verwijderen, klikt u op het symbool ellipsen ( **...** ) om het grafiekmenu te openen en kiest u het juiste menu-item van **Omhoog,** **Omlaag**of **Verwijderen**.

## <a name="apply-filters-to-charts"></a>Filters toepassen op grafieken

U filters toepassen op de grafieken met statistieken met afmetingen. Als de statistiek 'Transactietelling' bijvoorbeeld een dimensie heeft, 'Antwoordtype', die aangeeft of het antwoord van transacties is geslaagd of mislukt, wordt een grafiekregel voor alleen succesvolle (of alleen mislukte) transacties geplot. 

### <a name="to-add-a-filter"></a>Een filter toevoegen

1. Filter **toevoegen** boven de grafiek selecteren

2. Selecteer welke dimensie (eigenschap) u wilt filteren

   ![afbeelding van metrische gegevens](./media/metrics-charts/00006.png)

3. Selecteer welke dimensiewaarden u wilt opnemen bij het plotten van de grafiek (in dit voorbeeld wordt het filteren van de geslaagde opslagtransacties weergegeven):

   ![afbeelding van metrische gegevens](./media/metrics-charts/00007.png)

4. Nadat u de filterwaarden hebt geselecteerd, klikt u weg van de filterkiezer om deze te sluiten. Nu geeft de grafiek aan hoeveel opslagtransacties zijn mislukt:

   ![afbeelding van metrische gegevens](./media/metrics-charts/00008.png)

5. U stap 1-4 herhalen om meerdere filters op dezelfde grafieken toe te passen.



## <a name="apply-splitting-to-a-chart"></a>Splitsen toepassen op een grafiek

U een statistiek splitsen op dimensie om te visualiseren hoe verschillende segmenten van de statistiek met elkaar verhouden en de afgelegen segmenten van een dimensie te identificeren.

### <a name="apply-splitting"></a>Splitsen toepassen

1. Klik op Splitsen boven de grafiek **toepassen.**
 
   > [!NOTE]
   > Splitsen kan niet worden gebruikt met grafieken met meerdere statistieken. U ook meerdere filters hebben, maar slechts één splitsingsdimensie die op één grafiek wordt toegepast.

2. Kies een dimensie waarop u uw grafiek wilt segmenteren:

   ![afbeelding van metrische gegevens](./media/metrics-charts/00010.png)

   Nu toont de grafiek nu meerdere lijnen, één voor elk segment van dimensie:

   ![afbeelding van metrische gegevens](./media/metrics-charts/00012.png)

3. Klik weg van de **groepselectiekiezer** om deze te sluiten.

   > [!NOTE]
   > Gebruik zowel Filteren als Splitsen op dezelfde dimensie om de segmenten te verbergen die niet relevant zijn voor uw scenario en grafieken gemakkelijker leesbaar te maken.

## <a name="lock-boundaries-of-chart-y-axis"></a>Grenzen van de y-as van grafiek vergrendelen

Het vergrendelen van het bereik van de y-as wordt belangrijk wanneer de grafiek kleinere schommelingen van grotere waarden weergeeft. 

Wanneer het aantal succesvolle aanvragen bijvoorbeeld daalt van 99,99% naar 99,5%, kan dit een aanzienlijke vermindering van de kwaliteit van de dienstverlening betekenen. Het opmerken van een kleine numerieke waardefluctuatie zou echter moeilijk of zelfs onmogelijk zijn vanuit de standaardgrafiekinstellingen. In dit geval u de laagste grens van de grafiek vergrendelen tot 99%, wat deze kleine daling duidelijker zou maken. 

Een ander voorbeeld is een fluctuatie in het beschikbare geheugen, waarbij de waarde technisch nooit 0 zal bereiken. Het vaststellen van het bereik op een hogere waarde kan de druppels in het beschikbare geheugen gemakkelijker te herkennen. 

Als u het y-asbereik wilt beheren, gebruikt u de "..." grafiekmenu en selecteer **Grafiek bewerken** om toegang te krijgen tot geavanceerde grafiekinstellingen. Wijzig de waarden in de sectie Y-asbereik of gebruik de knop **Automatisch** om terug te keren naar standaardwaarden.

![afbeelding van metrische gegevens](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Het vergrendelen van de grenzen van de y-as voor de grafieken die verschillende tellingen of sommen bijhouden over een periode van tijd (en dus het aantal, de som, het minimum of de maximale aggregaties gebruiken), vereist meestal het opgeven van een vaste tijdgranulariteit in plaats van te vertrouwen op de automatische standaardwaarden. Dit is nodig omdat de waarden in grafieken veranderen wanneer de tijdgranulariteit automatisch wordt gewijzigd door de gebruiker het formaat van het browservenster wijzigt of van de ene schermresolutie naar de andere gaat. De resulterende verandering in de tijdgranulariteit beïnvloedt het uiterlijk van de grafiek, waardoor de huidige selectie van het y-asbereik ongeldig wordt.

## <a name="change-colors-of-chart-lines"></a>Kleuren van grafieklijnen wijzigen

Nadat u de grafieken hebt geconfigureerd, krijgen de grafiekregels automatisch een kleur toegewezen uit een standaardpalet. Je die kleuren veranderen.

Als u de kleur van een grafieklijn wilt wijzigen, klikt u op de gekleurde balk in de legenda die overeenkomt met de grafiek. Het dialoogvenster kleurkiezer wordt geopend. Gebruik de kleurkiezer om de kleur voor de lijn te configureren.

Nadat de grafiekkleuren zijn geconfigureerd, blijven ze zo wanneer u de grafiek vastmaakt aan een dashboard. In de volgende sectie ziet u hoe u een grafiek vastmaakt.

> [!NOTE]
> Vanwege beperkingen van ons release- en publicatieschema vereist het wijzigen van kleuren van de grafieklijnen tijdelijk het [https://portal.azure.com/?feature.colorpicker=true](https://portal.azure.com/?feature.colorpicker=true)doorgeven van een speciale parameter **?feature.colorpicker=true** bij het starten van Azure-portal. Deze beperking zal binnenkort worden verwijderd. 

![afbeelding van metrische gegevens](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Grafieken vastmaken aan dashboards

Nadat u de grafieken hebt geconfigureerd, u deze toevoegen aan de dashboards, zodat u deze opnieuw bekijken, mogelijk in context van andere telemetrie van bewaking, of delen met uw team.

Een geconfigureerde grafiek vastmaken aan een dashboard:

Nadat u de grafiek hebt geconfigureerd, klikt u op het menu **Grafiekacties** in de rechterbovenhoek van de grafiek en klikt u op **Vastmaken aan het dashboard**.

![afbeelding van metrische gegevens](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Waarschuwingsregels maken

U de criteria die u hebt ingesteld gebruiken om uw statistieken te visualiseren als basis voor een op metrische gegevens gebaseerde waarschuwingsregel. De nieuwe waarschuwingsregel bevat uw doelbron, metrische, splitsings- en filterdimensies uit uw grafiek. U deze instellingen later wijzigen in het deelvenster Voor het maken van waarschuwingsregels.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Als u een nieuwe waarschuwingsregel wilt maken, klikt u op **Nieuwe waarschuwingsregel**

![Knop Nieuwe waarschuwingsregel rood gemarkeerd](./media/metrics-charts/015.png)

U wordt naar het deelvenster voor het maken van waarschuwingsregels gebracht met de onderliggende metrische dimensies van uw vooraf ingevulde grafiek, waardoor het eenvoudiger wordt om aangepaste waarschuwingsregels te genereren.

![Waarschuwingsregel maken](./media/metrics-charts/016.png)

Lees dit [artikel](alerts-metric.md) voor meer informatie over het instellen van metrische waarschuwingen.

## <a name="troubleshooting"></a>Problemen oplossen

*Ik zie geen gegevens op mijn kaart.*

* Filters zijn van toepassing op alle grafieken in het deelvenster. Zorg ervoor dat u tijdens de focus op één grafiek geen filter hebt ingesteld dat alle gegevens op een andere kaart uitsluit.

* Als u verschillende filters op verschillende grafieken wilt instellen, maakt u ze in verschillende bladen en slaat u ze op als afzonderlijke favorieten. Als u wilt, u ze vastmaken aan het dashboard, zodat u ze naast elkaar zien.

* Als u een grafiek segmenteert door een eigenschap die niet is gedefinieerd op de statistiek, staat er niets in de grafiek. Probeer de segmentatie te wissen (splitsen) of kies een andere eigenschap.

## <a name="next-steps"></a>Volgende stappen

  Lees [Aangepaste KPI-dashboards maken](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) om meer te weten te komen over de aanbevolen procedures voor het maken van bruikbare dashboards met statistieken.

