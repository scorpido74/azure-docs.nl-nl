---
title: Visualisaties van Azure Monitor werkmap
description: Meer informatie over alle Azure Monitor werkmap visualisaties-onderdelen, zoals tekst, grafieken, rasters, structuren en grafieken.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: bcc60c0f934111f779e3fdedc399881acb16f208
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872788"
---
# <a name="azure-monitor-workbook-visualizations"></a>Visualisaties van Azure Monitor werkmap

Azure Monitor werkmappen ondersteunen een aantal verschillende stijlen van visualisaties om te voldoen aan uw rapportage behoeften. In dit artikel vindt u voor beelden van elk type visualisatie.

## <a name="text"></a>Tekst

Met werkmappen kunnen auteurs tekst blokken in hun werkmappen bevatten. De tekst kan een menselijke analyse zijn van telemetrie, informatie om gebruikers te helpen bij het interpreteren van uw gegevens, sectie koppen, enzovoort.

![Scherm opname van Apdex-tabel met tekst](./media/workbooks-visualizations/apdex.png)

Tekst wordt toegevoegd aan de hand van een besturings element voor een prijs opwaardering, waarmee het besturings element voor volledige opmaak

![Scherm opname van onbewerkte prijs opgave die de gerenderde tabel bouwt](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Een besturings element tekst toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht **bewerken** .
2. Gebruik de koppeling **tekst toevoegen** om een besturings element tekst toe te voegen aan de werkmap.
3. Prijs verlaging toevoegen aan het besturings element.
4. Klik op de knop **bewerken is voltooid** om de opgemaakte tekst te bekijken.

> [!TIP]
> Gebruik dit [Cheat-blad voor prijs verlaging](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) om meer te weten te komen over verschillende opmaak opties.

## <a name="charts"></a>Grafieken

Met werkmappen kunnen bewakings gegevens worden weer gegeven als grafieken. Ondersteunde grafiek typen zijn onder andere lijn-, staaf-, staaf-categorische, vlak-, spreidings-en cirkel-en tijd diagrammen. Auteurs kunnen ervoor kiezen om de hoogte, breedte, kleuren palet, legenda, titels, no-data-bericht, enzovoort van de grafiek aan te passen.

Werkmappen ondersteunen grafieken voor logboeken en metrische gegevens bronnen. 

### <a name="adding-a-log-chart"></a>Een logboek grafiek toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht **bewerken** .
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. Gebruik de query-editor om de [KQL](https://docs.microsoft.com/azure/kusto/query/) voor uw analyse in te voeren (bijvoorbeeld trend van aanvragen).
5. Stel de visualisatie in op een van de volgende elementen: **vlak**, **staaf**, **staaf (categorische)** , **lijn**, **cirkel**, **sprei ding**of **tijd**.
6. Stel andere para meters in als dat nodig is, zoals een tijds bereik, visualisatie, grootte, kleuren palet en legenda.

![Scherm afbeelding van de logboek grafiek in de bewerkings modus](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Para meters van het logboek diagram

| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `Query Type` | Het type query dat moet worden gebruikt | Log, Azure-resource grafiek, etc. |
| `Resource Type` | Het bron type van het doel | Application Insights, Log Analytics of Azure-eerst |
| `Resources` | Een set resources waaruit de metrische waarde moet worden opgehaald | MyApp1 |
| `Time Range` | Het tijd venster voor het weer geven van de logboek grafiek | Vorig uur, afgelopen 24 uur, etc. |
| `Visualization` | De visualisatie die moet worden gebruikt | Vlak, staaf, lijn, cirkel, sprei ding, tijd, staaf categorische |
| `Size` | De verticale grootte van het besturings element | Klein, gemiddeld, groot of volledig |
| `Color palette` | Het kleuren palet dat in de grafiek moet worden gebruikt. Genegeerd in de modus met meerdere metrieken of segmenten. | Blauw, groen, rood, etc. |
| `Legend` | De aggregatie functie die moet worden gebruikt voor de legenda | Som of gemiddelde van waarden of Max, min, eerste, laatste waarde |
| `Query` | Een KQL-query waarmee gegevens worden geretourneerd in de indeling die wordt verwacht door de grafiek visualisatie | _vraagt \|-reeks aanvragen = aantal () standaard = 0 op tijds tempel van geleden (1d) tot nu () stap 1U_ |

### <a name="adding-a-metric-chart"></a>Een metrische grafiek toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht **bewerken** .
2. Gebruik de koppeling **metrische gegevens toevoegen** om een metrische besturings element toe te voegen aan de werkmap.
3. Selecteer een resource type (bijvoorbeeld opslag account), de resources die het doel moeten zijn, de metrische naam ruimte en naam en de aggregatie die moet worden gebruikt.
4. Stel andere para meters in als dat nodig is, zoals een tijds bereik, Split-op, visualisatie, grootte en kleuren palet.

![Scherm opname van grafiek met metrische gegevens in de bewerkings modus](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Metrische diagram parameters

| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `Resource Type` | Het bron type van het doel | Opslag of virtuele machine. |
| `Resources` | Een set resources waaruit de metrische waarde moet worden opgehaald | MyStorage1 |
| `Namespace` | De naam ruimte met de metriek | Opslag >-BLOB |
| `Metric` | De metrische gegevens voor visualiseren | Opslag > BLOB-> transacties |
| `Aggregation` | De aggregatie functie die moet worden toegepast op de metriek | Som, aantal, gemiddelde, etc. |
| `Time Range` | Het tijd venster voor het weer geven van de metriek in | Vorig uur, afgelopen 24 uur, etc. |
| `Visualization` | De visualisatie die moet worden gebruikt | Vlak, staaf, lijn, sprei ding, Raster |
| `Split By` | De metriek voor een dimensie eventueel splitsen | Trans acties op geo-type |
| `Size` | De verticale grootte van het besturings element | Klein, gemiddeld of groot |
| `Color palette` | Het kleuren palet dat in de grafiek moet worden gebruikt. Wordt genegeerd als de para meter `Split by` wordt gebruikt | Blauw, groen, rood, etc. |

## <a name="grids"></a>Rasters

Rasters of tabellen zijn een veelgebruikte manier om gegevens te presen teren aan gebruikers. Met werkmappen kunnen gebruikers de kolommen van het raster afzonderlijk opmaken om een uitgebreide gebruikers interface te bieden voor hun rapporten.

In het onderstaande voor beeld ziet u een raster waarin pictogrammen, Heatmaps en Spark-balken worden gecombineerd om complexe gegevens te presen teren. De werkmap bevat ook sorteren, een zoekvak en een go-to-Analytics-knop.

![Scherm opname van raster op basis van logboek](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Een op een logboek gebaseerd raster toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht **bewerken** .
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. Gebruik de query-editor om de KQL voor uw analyse in te voeren (bijvoorbeeld Vm's met geheugen onder een drempel waarde)
5. Visualisatie instellen op **raster**
6. Stel andere para meters in als dat nodig is, zoals het tijds bereik, de grootte, het kleuren palet en de legenda.

![Scherm opname van raster query op basis van logboek](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Tegels

Tegels zijn een zeer handige manier om samenvattings gegevens te presen teren in werkmappen. In de onderstaande afbeelding ziet u een algemeen gebruik van tegels-samen vatting op app-niveau boven op een gedetailleerd raster.

![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-visualizations/tiles-summary.png)

Werkmap tegels bieden ondersteuning voor een titel, subtitel, grote tekst, pictogrammen, kleur overgangen op basis van metrische gegevens, vonk lijnen/staven, voet tekst, enzovoort.

### <a name="adding-a-tile"></a>Een tegel toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap. 
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Grootte instellen op **volledig**
6. De visualisatie instellen op **tegels**
7. Klik op de knop **tegel instellingen** om het deel venster instellingen te openen
8. In de **velden naast elkaar**instellen:
    * Titel: `name`
    * Links: `Requests`, renderer: `Big Number`, kleuren palet: `Green to Red`, minimale waarde: `0`
    * Onder: `appName`
9. Klik op de knop **opslaan en sluiten** onder aan het deel venster.

![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-visualizations/tile-settings.png)

Dit is de manier waarop de tegels in de Lees modus worden weer geven:

![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Mapstructuren

Werkmappen ondersteunen hiërarchische weer gaven via structuur rasters. Met structuren kunnen sommige rijen worden uitgebreid naar het volgende niveau voor een inzoom ervaring.

In het onderstaande voor beeld ziet u de metrische gegevens over de container status (werkset-grootte) visualiseren als een structuur raster. De knoop punten op het hoogste niveau hier zijn knoop punten van Azure Kubernetes service (AKS), het volgende niveau is een Peul en het laatste niveau is containers. U kunt uw kolommen zoals in een raster (heatmap, pictogrammen en koppelingen) ook opmaken. De onderliggende gegevens bron in dit geval is een Log Analytics-werk ruimte met AKS-Logboeken.

![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Een structuur raster toevoegen
1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap. 
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Visualisatie instellen op **raster**
6. Klik op de knop **kolom instellingen** om het deel venster instellingen te openen
7. Stel in de sectie **structuur/groep per instellingen** onderaan het volgende in:
    * Structuur type: `Parent/Child`
    * Id-veld: `Id`
    * Bovenliggend id-veld: `ParentId`
    * Uitvouwen weer geven op: `Name`
    * Vouw het hoogste niveau van de structuur uit: `checked`
8. In het gedeelte _kolommen_ bovenaan, stelt u het volgende in:
    * _Id_ -kolom weergave: `Hidden`
    * _Bovenliggende id_ -kolom weergave: `Hidden`
    * _Aanvragen_ -kolom renderer: `Bar`, kleur: `Blue`, minimum waarde: `0`
9. Klik op de knop _opslaan en sluiten_ onder aan het deel venster.    

![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Structuur instellingen

| Instelling | Uitleg |
|:------------- |:-------------|
| `Id Field` | De unieke id van elke rij in het raster |
| `Parent Id Field` | De id van het bovenliggende item van de huidige rij |
| `Show the expander on` | De kolom waarin de structuur weergave wordt weer gegeven. Het is gebruikelijk dat structuur rasters hun id en bovenliggend id-veld verbergen, omdat ze niet goed leesbaar zijn. In plaats daarvan wordt de uitbrei ding weer gegeven in een veld met een meer Lees bare waarde, zoals de naam van de entiteit |
| `Expand the top level of the tree` | Als u dit selectie vakje inschakelt, wordt het structuur raster uitgebreid op het hoogste niveau. Dit is handig als u standaard meer informatie wilt weer geven |

## <a name="graphs"></a>Grafieken

Werkmappen ondersteunen het visualiseren van wille keurige grafieken op basis van gegevens uit Logboeken om de relaties tussen bewakings entiteiten weer te geven.

In de volgende grafiek ziet u gegevens die in/uit een computer worden uitgestroomd via verschillende poorten naar/van externe computers. Het is gekleurd op type (computer versus poort versus extern IP-adres) en de rand grootten komen overeen met de hoeveelheid gegevens die tussen tussen worden geplaatst. De onderliggende gegevens zijn afkomstig van KQL query gericht op VM-verbindingen.

![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Een grafiek toevoegen
1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling **query toevoegen** om een besturings element voor een logboek query toe te voegen aan de werkmap. 
3. Selecteer het query type als **logboek**, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. De visualisatie instellen op **Graph**
8. Klik op de knop **grafiek instellingen** om het deel venster instellingen te openen
9. Stel in _indelings velden_ onderaan het volgende in:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. Stel in _instellingen voor knooppunt indeling_ bovenaan het volgende in:
    * _Bovenste inhoud_: gebruik de kolom `Name`, de kolom weergave: `Text`
    * _Inhoud centreren_-kolom gebruiken: `Calls`, kolom weergave: `Big Number`, kleuren palet: `None`
    * _Onderste inhoud_-gebruik kolom: `Kind`, kolom weergave: `Text`
10. Klik op de knop _opslaan en sluiten_ onder aan het deel venster.

![Scherm opname van de weer gave samen vatting van Tegel](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Volgende stappen

* [Implementeer](workbooks-automate.md) werkmappen met Azure Resource Manager.
* De toegang tot uw werkmap resources [beheren](workbooks-access-control.md) en delen.