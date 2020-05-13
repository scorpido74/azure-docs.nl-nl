---
title: Azure Monitor voor Azure-cache voor redis (preview-versie) | Microsoft Docs
description: In dit artikel wordt de Azure Monitor beschreven voor Redis Cache-functie die Azure-cache biedt voor redis-eigen aren, met een duidelijk beeld van de prestaties en het gebruik van problemen.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210972"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Azure Monitor voor Azure-cache verkennen voor redis (preview-versie)

Azure Monitor voor Azure cache voor redis (preview) biedt een overzicht van de algemene prestaties, fouten, capaciteit en operationele status van al uw Azure-cache voor redis-resources in een uniforme interactieve ervaring. In dit artikel vindt u meer informatie over de voor delen van deze nieuwe bewakings ervaring en hoe u de ervaring kunt aanpassen en aanpassen aan de unieke behoeften van uw organisatie.

## <a name="introduction"></a>Inleiding

Voordat u aan de slag gaat, moet u weten hoe de informatie wordt gepresenteerd en gevisualiseerd.

Het biedt:

* **Op schaal perspectief** van uw Azure-cache voor redis-resources in al uw abonnementen op één locatie, met de mogelijkheid om selectief te maken voor de abonnementen en resources die u wilt evalueren.

* **Inzoomen** op een bepaalde Azure-cache voor de redis-resource om problemen op te lossen of gedetailleerde analyses uit te voeren op categorie gebruik, storingen, capaciteit en bewerkingen. Als u een van deze opties selecteert, krijgt u een gedetailleerde weer gave van de relevante.  

* **Aanpasbaar** : deze ervaring is gebaseerd op Azure monitor werkmap sjablonen, zodat u kunt wijzigen welke metrische gegevens worden weer gegeven, wijzigen of instellen van drempel waarden die worden uitgelijnd met uw limieten en vervolgens opslaan in een aangepaste werkmap. Grafieken in de werkmappen kunnen vervolgens worden vastgemaakt aan Azure-Dash boards.  

Voor deze functie hoeft u niets in te scha kelen of te configureren. deze Azure-cache voor redis wordt standaard verzameld.

>[!NOTE]
>Er zijn geen kosten verbonden aan het verkrijgen van toegang tot deze functie en er worden alleen kosten in rekening gebracht voor de Azure Monitor essentiële functies die u configureert of inschakelt, zoals wordt beschreven op de pagina met [Azure monitor prijs informatie](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Metrische gegevens over gebruik en prestaties voor Azure cache voor redis weer geven

Voer de volgende stappen uit om het gebruik en de prestaties van uw opslag accounts in al uw abonnementen weer te geven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **monitor** en selecteer **monitor**.

    ![Zoekvak met het woord ' Monitor ' en een vervolg keuzelijst met de tekst ' Monitor ' met een snelheid van de schijf stijl](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecteer **Azure-cache voor redis (preview-versie)**.

Als deze optie niet aanwezig is, klikt u op **meer** en selecteert u **Azure cache voor redis**.

### <a name="overview"></a>Overzicht

In het **overzicht**wordt in de tabel interactieve Azure-cache weer gegeven voor redis-metrische gegevens. U kunt de resultaten filteren op basis van de opties die u selecteert in de volgende vervolg keuzelijsten:

* **Abonnementen** : alleen abonnementen met een Azure-cache voor de redis-resource worden weer gegeven.  

* **Azure-cache voor redis** : u kunt alle, een subset of één Azure-cache voor redis-resource selecteren.

* **Tijds bereik** : de laatste 4 uur aan gegevens worden standaard weer gegeven op basis van de bijbehorende selecties.

De tegel item in de vervolg keuzelijst bevat een samen telling van het totale aantal Azure-cache geheugen voor redis-resources in de geselecteerde abonnementen. Er zijn voorwaardelijke kleurcoderings-of Heatmaps voor kolommen in de werkmap die de metrische gegevens van trans acties rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur op basis van de laagste waarden.

Als u een vervolg keuze pijl naast een van de Azure-cache voor redis bronnen selecteert, wordt er een uitsplitsing van de prestatie gegevens op het niveau van de afzonderlijke resource weer gegeven:

![Scherm afbeelding van de overzichts ervaring](./media/redis-cache-insights-overview/overview.png)

Als u de naam van het Azure-cache geheugen selecteert voor de redis-resource in blauw gemarkeerd, gaat u naar het standaard **overzicht** voor het gekoppelde account. Hiermee wordt `Used Memory` ,,, `Used Memory Percentage` `Server Load` `Server Load Timeline` , `CPU` , `Connected Clients` , `Cache Misses` ,, `Errors (Max)` weer gegeven.

### <a name="operations"></a>Bewerkingen

Selecteer **bewerkingen** boven aan de pagina en het gedeelte **bewerkingen** van de werkmap sjabloon wordt geopend. Hierin wordt `Total Operations` , `Total Operations Timeline` , `Operations Per Second` , `Gets` , weer gegeven `Sets` .

![Scherm afbeelding van de overzichts ervaring](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Gebruik

Selecteer **gebruik** boven aan de pagina en het **gebruiks** gedeelte van de werkmap sjabloon wordt geopend. Hierin wordt `Cache Read` , `Cache Read Timeline` , `CacheWrite` , `CacheHits` , weer gegeven `Cache Misses` .

![Scherm afbeelding van de overzichts ervaring](./media/redis-cache-insights-overview/usage.png)

Selecteer **fouten** aan de bovenkant van de pagina en het gedeelte **storingen** van de werkmap sjabloon wordt geopend. Hiermee wordt `Total Errors` ,,, `Failover/Errors` `UnresponsiveClient/Errors` `RDB/Errors` , `AOF/Errors` , `Export/Errors` , `Dataloss/Errors` ,, `Import/Errors` weer gegeven.

![Scherm opname van fouten met uitsplitsing op basis van het type HTTP-aanvraag](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Metrische definities

Bekijk het [artikel beschik bare metrische gegevens en rapportage-intervallen](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals)voor een volledige lijst met metrische definities die deze werkmappen vormen.

## <a name="pin-export-and-expand"></a>Vastmaken, exporteren en uitvouwen

U kunt een van de metrische gedeelten aan een Azure- [dash board](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) vastmaken door het pictogram punaise rechtsboven in de sectie te selecteren.

![Voor beeld van de sectie metrische gegevens van de metriek naar dash board](./media/cosmosdb-insights-overview/pin.png)

Als u uw gegevens wilt exporteren naar de Excel-indeling, selecteert u de pijl-omlaag links van het punaise pictogram.

![Pictogram werkmap exporteren](./media/cosmosdb-insights-overview/export.png)

Als u alle vervolg keuzelijsten in de werkmap wilt uitvouwen of samen vouwen, selecteert u het pictogram uitvouwen links van het pictogram exporteren:

![Pictogram werkmap uitvouwen](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Azure Monitor voor Azure-cache aanpassen voor redis (preview-versie)

Omdat deze ervaring is gebaseerd op Azure monitor werkmap sjablonen, kunt u **Customize**  >  **bewerken** en een kopie van uw gewijzigde versie in een aangepaste werkmap **Opslaan** . 

![Balk aanpassen](./media/cosmosdb-insights-overview/customize.png)

Werkmappen worden opgeslagen in een resource groep, hetzij in het gedeelte **mijn rapporten** dat persoonlijk is of in de sectie **gedeelde rapporten** dat toegankelijk is voor iedereen die toegang heeft tot de resource groep. Nadat u de aangepaste werkmap hebt opgeslagen, moet u naar de galerie met werkmappen gaan om deze te starten.

![Werkmap galerie starten vanaf de opdracht balk](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Volgende stappen

* [Waarschuwingen voor metrische gegevens](../platform/alerts-metric.md) en [service status meldingen](../../service-health/alerts-activity-log-service-notifications.md) configureren om automatische waarschuwingen in te stellen voor hulp bij het detecteren van problemen.

* Meer informatie over de scenario's werkmappen zijn ontworpen voor ondersteuning, het ontwerpen van nieuwe en het aanpassen van bestaande rapporten en meer door [interactieve rapporten maken met Azure monitor werkmappen](../app/usage-workbooks.md)te controleren.
