---
title: Weergaven maken om logboekgegevens in Azure Monitor te analyseren | Microsoft Documenten
description: Met View Designer in Azure Monitor u aangepaste weergaven maken die worden weergegeven in de Azure-portal en verschillende visualisaties bevatten over gegevens in de werkruimte Log Analytics. Dit artikel bevat een overzicht van View Designer en bevat procedures voor het maken en bewerken van aangepaste weergaven.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/10/2019
ms.openlocfilehash: 9a7521f61dc59bd954629a05638c159ab0e70556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658486"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Aangepaste weergaven maken met View Designer in Azure Monitor
Door View Designer in Azure Monitor te gebruiken, u verschillende aangepaste weergaven maken in de Azure-portal waarmee u gegevens in uw werkruimte Log Analytics visualiseren. In dit artikel vindt u een overzicht van View Designer en procedures voor het maken en bewerken van aangepaste weergaven.

> [!IMPORTANT]
> Weergaven in Azure Monitor worden geleidelijk afgeschaft en vervangen door [werkmappen](workbooks-overview.md) die extra functionaliteit bieden. Zie [de overgangshandleiding voor de overgang van de azure-monitor-weergave ontwerper naar werkmappen](view-designer-conversion-overview.md) voor meer informatie over het converteren van uw bestaande weergaven naar werkmappen.

Zie voor meer informatie over View Designer:

* [Tegelverwijzing:](view-designer-tiles.md)biedt een referentiehandleiding voor de instellingen voor elk van de beschikbare tegels in uw aangepaste weergaven.
* [Verwijzing naar visualisatieonderdelen:](view-designer-parts.md)biedt een referentiehandleiding voor de instellingen voor de visualisatieonderdelen die beschikbaar zijn in uw aangepaste weergaven.


## <a name="concepts"></a>Concepten
Weergaven worden weergegeven op de pagina Azure **Monitor-overzicht** in de Azure-portal. Open deze pagina in het menu **Azure Monitor** door te klikken op **Meer** onder de sectie **Inzichten.** De tegels in elke aangepaste weergave worden alfabetisch weergegeven en de tegels voor de bewakingsoplossingen worden dezelfde werkruimte geïnstalleerd.

![Overzichtspagina](media/view-designer/overview-page.png)

De weergaven die u met View Designer maakt, bevatten de elementen die in de volgende tabel worden beschreven:

| Onderdeel | Beschrijving |
|:--- |:--- |
| Tegels | Worden weergegeven op de pagina Overzicht van **Azure-monitor.** Elke tegel geeft een visuele samenvatting weer van de aangepaste weergave die wordt weergegeven. Elk tegeltype biedt een andere visualisatie van uw records. U selecteert een tegel om een aangepaste weergave weer te geven. |
| Aangepaste weergave | Weergegeven wanneer u een tegel selecteert. Elke weergave bevat een of meer visualisatieonderdelen. |
| Visualisatieonderdelen | Presenteer een visualisatie van gegevens in de werkruimte Log Analytics op basis van een of meer [logboekquery's.](../log-query/log-query-overview.md) De meeste onderdelen bevatten een koptekst, die een visualisatie op hoog niveau biedt, en een lijst, die de hoogste resultaten weergeeft. Elk onderdeeltype biedt een andere visualisatie van de records in de werkruimte Log Analytics. U selecteert elementen in het onderdeel om een logboekquery uit te voeren met gedetailleerde records. |

## <a name="required-permissions"></a>Vereiste machtigingen
U hebt ten minste [machtigingen op bijdrageniveau](manage-access.md#manage-access-using-azure-permissions) in de werkruimte Log Analytics nodig om weergaven te maken of te wijzigen. Als u deze toestemming niet hebt, wordt de optie Ontwerp weergeven niet weergegeven in het menu.


## <a name="work-with-an-existing-view"></a>Werken met een bestaande weergave
Weergaven die zijn gemaakt met View Designer geven de volgende opties weer:

![Menu Overzicht](media/view-designer/overview-menu.png)

De opties worden beschreven in de volgende tabel:

| Optie | Beschrijving |
|:--|:--|
| Vernieuwen   | Hiermee vernieuwt u de weergave met de nieuwste gegevens. | 
| Logboeken      | Hiermee opent u de [Logboekanalyse](../log-query/portals.md) om gegevens te analyseren met logboekquery's. |
| Bewerken       | Hiermee opent u de weergave in View Designer om de inhoud en configuratie ervan te bewerken.  |
| Klonen      | Hiermee maakt u een nieuwe weergave en opent deze in View Designer. De naam van de nieuwe weergave is hetzelfde als de oorspronkelijke naam, maar met *Kopie* toegevoegd aan het. |
| Datumbereik | Stel het datum- en tijdbereikfilter in voor de gegevens die in de weergave zijn opgenomen. Dit datumbereik wordt toegepast vóór datumbereiken die zijn ingesteld in query's in de weergave.  |
| +          | Definieer een aangepast filter dat is gedefinieerd voor de weergave. |


## <a name="create-a-new-view"></a>Een nieuwe weergave maken
U een nieuwe weergave maken in View Designer door **Weergaveontwerper** te selecteren in het menu van uw werkruimte Log Analytics.

![Designertegel weergeven](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Werken met View Designer
U gebruikt View Designer om nieuwe weergaven te maken of bestaande weergaven te bewerken. 

View Designer heeft drie deelvensters: 
* **Ontwerp:** bevat de aangepaste weergave die u maakt of bewerkt. 
* **Besturingselementen:** bevat de tegels en onderdelen die u aan het **deelvenster Ontwerp** toevoegt. 
* **Eigenschappen:** geeft de eigenschappen van de tegels of geselecteerde onderdelen weer.

![Designer weergeven](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>De weergavetegel configureren
Een aangepaste weergave kan slechts één tegel hebben. Als u de huidige tegel wilt weergeven of een alternatieve wilt selecteren, selecteert u het tabblad **Tegel** in het deelvenster **Besturingselement.** In het deelvenster **Eigenschappen** worden de eigenschappen van de huidige tegel weergegeven. 

U de tegeleigenschappen configureren op basis van de informatie in de [verwijzing naar Tile](view-designer-tiles.md) en vervolgens op **Toepassen** klikken om de wijzigingen op te slaan.

### <a name="configure-the-visualization-parts"></a>De visualisatieonderdelen configureren
Een weergave kan een willekeurig aantal visualisatieonderdelen bevatten. Als u onderdelen aan een weergave wilt toevoegen, selecteert u het tabblad **Weergave** en selecteert u een visualisatieonderdeel. In het deelvenster **Eigenschappen** worden de eigenschappen van het geselecteerde onderdeel weergegeven. 

U de weergave-eigenschappen configureren op basis van de informatie in de [verwijzing naar het onderdeel Visualisatie](view-designer-parts.md) en vervolgens op **Toepassen** klikken om de wijzigingen op te slaan.

Weergaven hebben slechts één rij visualisatieonderdelen. U de bestaande onderdelen opnieuw rangschikken door ze naar een nieuwe locatie te slepen.

U een visualisatieonderdeel uit de weergave verwijderen door de **X** rechtsboven in het onderdeel te selecteren.


### <a name="menu-options"></a>Menuopties
De opties voor het werken met weergaven in de bewerkingsmodus worden beschreven in de volgende tabel.

![Menu Bewerken](media/view-designer/edit-menu.png)

| Optie | Beschrijving |
|:--|:--|
| Opslaan        | Slaat uw wijzigingen op en sluit de weergave. |
| Annuleren      | Hiermee verwijdert u uw wijzigingen en sluit u de weergave. |
| Weergave verwijderen | Hiermee verwijdert u de weergave. |
| Exporteren      | Hiermee exporteert u de weergave naar een [Azure Resource Manager-sjabloon](../../azure-resource-manager/templates/template-syntax.md) die u importeren in een andere werkruimte. De naam van het bestand is de naam van de weergave en het heeft een *omsview-extensie.* |
| Importeren      | Hiermee importeert u het *omsview-bestand* dat u vanuit een andere werkruimte hebt geëxporteerd. Met deze actie wordt de configuratie van de bestaande weergave overschrijft. |
| Klonen       | Hiermee maakt u een nieuwe weergave en opent deze in View Designer. De naam van de nieuwe weergave is hetzelfde als de oorspronkelijke naam, maar met *Kopie* toegevoegd aan het. |

## <a name="next-steps"></a>Volgende stappen
* Tegels [Tiles](view-designer-tiles.md) toevoegen aan uw aangepaste weergave.
* [Visualisatieonderdelen](view-designer-parts.md) toevoegen aan uw aangepaste weergave.
