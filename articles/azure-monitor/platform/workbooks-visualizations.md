---
title: Azure Monitor-werkmapvisualisaties
description: Meer informatie over alle onderdelen van azure monitor-werkmapvisualisaties, waaronder - tekst, grafieken, rasters, bomen en grafieken.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658027"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor-werkmapvisualisaties

Azure Monitor-werkmappen ondersteunen een aantal verschillende stijlen visualisaties om aan uw rapportagebehoeften te voldoen. In dit artikel vindt u voorbeelden van elk type visualisatie.

## <a name="text"></a>Tekst

Met werkmappen kunnen auteurs tekstblokken opnemen in hun werkmappen. De tekst kan menselijke analyse van telemetrie zijn, informatie om gebruikers te helpen uw gegevens te interpreteren, sectiekoppen, enz.

![Schermafbeelding van de tabel Apdex met tekst](./media/workbooks-visualizations/apdex.png)

Tekst wordt toegevoegd via een Markdown-besturingselement dat voorziet in volledige opmaakbesturingselement.

![Schermafbeelding van ruwe markdown die de gerenderde tabel bouwt](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Een tekstbesturingselement toevoegen

1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem **Bewerken** te klikken.
2. Gebruik de koppeling **Tekst toevoegen** om een tekstbesturingselement toe te voegen aan de werkmap.
3. Markdown toevoegen aan het besturingselement.
4. Klik op de knop **Gereed bewerken** om de opgemaakte tekst weer te geven.

> [!TIP]
> Gebruik dit [spiekbriefje van Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) voor meer informatie over verschillende opmaakopties.

## <a name="charts"></a>Grafieken

Met werkmappen kunnen bewakingsgegevens worden gepresenteerd als grafieken. Ondersteunde grafiektypen zijn lijn, balk, balk categorisch, gebied, spreidingspercelen, cirkel en tijd. Auteurs kunnen ervoor kiezen om de hoogte, breedte, kleurenpalet, legenda, titels, no-data bericht, etc. van de grafiek aan te passen.

Werkmappen ondersteunen grafieken voor zowel logboeken als metrische gegevensbronnen. 

### <a name="adding-a-log-chart"></a>Een logboekdiagram toevoegen

1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem **Bewerken** te klikken.
2. Gebruik de koppeling **Query toevoegen** om een logboekquerybesturingselement toe te voegen aan de werkmap.
3. Selecteer het querytype als **Logboek,** resourcetype (bijvoorbeeld Toepassingsinzichten) en de resources die u wilt targeten.
4. Gebruik de queryeditor om de [KQL](https://docs.microsoft.com/azure/kusto/query/) in te voeren voor uw analyse (bijvoorbeeld trend van aanvragen).
5. Stel de visualisatie in op een van: **Gebied**, **Balk**, **Staaf (categorisch),** **Lijn**, **Cirkel,** **Spreiding**of **Tijd**.
6. Stel indien nodig andere parameters in- zoals tijdbereik, visualisatie, grootte, kleurenpalet en legenda.

![Schermafbeelding van logboekdiagram in de bewerkingsmodus](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parameters van logboekdiagram

| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `Query Type` | Het type query dat moet worden gebruikt | Logboek, Azure Resource Graph, enz. |
| `Resource Type` | Het resourcetype dat u wilt targeten | Toepassingsinzichten, Logboekanalyse of Azure-first |
| `Resources` | Een set resources om de waarde van de statistieken te halen uit | MyApp1 (MyApp1) |
| `Time Range` | Het tijdvenster om het logboekdiagram weer te geven | Laatste uur, Laatste 24 uur, enz. |
| `Visualization` | De visualisatie die u wilt gebruiken | Gebied, Bar, Lijn, Pie, Scatter, Tijd, balk categorisch |
| `Size` | De verticale grootte van het besturingselement | Klein, gemiddeld, groot of vol |
| `Color palette` | Het kleurenpalet dat u in de grafiek wilt gebruiken. Genegeerd in de multimetrische of gesegmenteerde modus. | Blauw, groen, rood, enz. |
| `Legend` | De aggregatiefunctie die u voor de legenda moet gebruiken | Som of Gemiddelde van waarden of Max, Min, Eerste, Laatste waarde |
| `Query` | Elke KQL-query die gegevens retourneert in de indeling die wordt verwacht door de grafiekvisualisatie | _aanvragen \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() stap 1h_ |

### <a name="adding-a-metric-chart"></a>Een metrische grafiek toevoegen

1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem **Bewerken** te klikken.
2. Gebruik de **koppeling Metrische koppeling toevoegen** om een metrische besturingselement toe te voegen aan de werkmap.
3. Selecteer een resourcetype (bijvoorbeeld Opslagaccount), de resources die u wilt targeten, de metrische naamruimte en naam en de te gebruiken aggregatie.
4. Stel indien nodig andere parameters in, zoals tijdbereik, split-by, visualisatie, grootte en kleurenpalet.

![Schermafbeelding van metrische grafiek in de bewerkingsmodus](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parameters voor metrische grafieken

| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `Resource Type` | Het resourcetype dat u wilt targeten | Opslag of virtuele machine. |
| `Resources` | Een set resources om de waarde van de statistieken te halen uit | MyStorage1 |
| `Namespace` | De naamruimte met de statistiek | Opslag > Blob |
| `Metric` | De statistiek die u wilt visualiseren | Opslag > >-transacties |
| `Aggregation` | De aggregatiefunctie die moet worden toegepast op de statistiek | Som, telling, gemiddelde, enz. |
| `Time Range` | Het tijdvenster waarin de statistiek moet worden weergegeven | Laatste uur, Laatste 24 uur, enz. |
| `Visualization` | De visualisatie die u wilt gebruiken | Gebied, Bar, Lijn, Verstrooiing, Raster |
| `Split By` | De statistiek eventueel splitsen op een dimensie | Transacties op geotype |
| `Size` | De verticale grootte van het besturingselement | Klein, gemiddeld of groot |
| `Color palette` | Het kleurenpalet dat u in de grafiek wilt gebruiken. Genegeerd als `Split by` de parameter wordt gebruikt | Blauw, groen, rood, enz. |

## <a name="grids"></a>Rasters

Rasters of tabellen zijn een veelvoorkomende manier om gegevens aan gebruikers te presenteren. Met werkmappen kunnen gebruikers de kolommen van het raster afzonderlijk stylen om een uitgebreide gebruikersinterface voor hun rapporten te bieden.

In het onderstaande voorbeeld ziet u een raster dat pictogrammen, heatmaps en spark-bars combineert om complexe informatie weer te geven. De werkmap biedt ook sorteren, een zoekvak en een go-to-analytics-knop.

![Schermafbeelding van logboekraster](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Een op logboeken gebaseerd raster toevoegen

1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem **Bewerken** te klikken.
2. Gebruik de koppeling **Query toevoegen** om een logboekquerybesturingselement toe te voegen aan de werkmap.
3. Selecteer het querytype als **Logboek,** resourcetype (bijvoorbeeld Toepassingsinzichten) en de resources die u wilt targeten.
4. De queryeditor gebruiken om de KQL in te voeren voor uw analyse (bijvoorbeeld VM's met geheugen onder een drempelwaarde)
5. De visualisatie instellen op **Raster**
6. Stel indien nodig andere parameters in- zoals tijdbereik, grootte, kleurenpalet en legenda.

![Schermafbeelding van op logboeken gebaseerde rasterquery](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Tegels

Tegels zijn een zeer nuttige manier om overzichtsgegevens in werkmappen te presenteren. De afbeelding hieronder toont een veelgebruikte use case van tegels - samenvatting op app-niveau bovenop een gedetailleerd raster.

![Schermafbeelding van de overzichtsweergave van tegels](./media/workbooks-visualizations/tiles-summary.png)

Werkmaptegels ondersteunen het weergeven van een titel, ondertitel, grote tekst, pictogrammen, op metrische basis gebaseerde verlopen, sparkline/bars, voettekst, enz.

### <a name="adding-a-tile"></a>Een tegel toevoegen

1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem _Bewerken_ te klikken.
2. Gebruik de koppeling **Query toevoegen** om een logboekquerybesturingselement toe te voegen aan de werkmap. 
3. Selecteer het querytype als **Logboek,** resourcetype (bijvoorbeeld Toepassingsinzichten) en de resources die u wilt targeten.
4. De queryeditor gebruiken om de KQL in te voeren voor uw analyse
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Grootte instellen op **Volledig**
6. De visualisatie instellen op **tegels**
7. Klik op de knop **Tegelinstellingen** om het instellingenvenster te openen
8. Stel **in tegelvelden**het gewenste aantal in:
    * Titel:`name`
    * Links: `Requests`, Renderer: `Big Number`, `Green to Red`Kleurenpalet: , Min-waarde:`0`
    * Onder:`appName`
9. Klik op de knop **Opslaan en sluiten** onder aan het deelvenster.

![Schermafbeelding van de overzichtsweergave van tegels](./media/workbooks-visualizations/tile-settings.png)

Dit is hoe de tegels eruit zullen zien in de leesmodus:

![Schermafbeelding van de overzichtsweergave van tegels](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Bomen

Werkmappen ondersteunen hiërarchische weergaven via structuurrasters. Met bomen kunnen sommige rijen worden uitbreidbaar naar het volgende niveau voor een drill-down-ervaring.

In het onderstaande voorbeeld worden containerstatusstatistieken (werksetgrootte) weergegeven die zijn gevisualiseerd als een raster voor bomen. De knooppunten op het hoogste niveau zijn hier AKS-knooppunten (Azure Kubernetes Service), het volgende niveau zijn pods en het uiteindelijke niveau zijn containers. Merk op dat u uw kolommen nog steeds opmaken zoals in een raster (heatmap, pictogrammen, koppeling). De onderliggende gegevensbron in dit geval is een Log Analytics-werkruimte met AKS-logboeken.

![Schermafbeelding van de overzichtsweergave van tegels](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Een boomraster toevoegen
1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem _Bewerken_ te klikken.
2. Gebruik de koppeling **Query toevoegen** om een logboekquerybesturingselement toe te voegen aan de werkmap. 
3. Selecteer het querytype als **Logboek,** resourcetype (bijvoorbeeld Toepassingsinzichten) en de resources die u wilt targeten.
4. De queryeditor gebruiken om de KQL in te voeren voor uw analyse
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
5. De visualisatie instellen op **Raster**
6. Klik op de knop **Kolominstellingen** om het deelvenster Instellingen te openen
7. Stel in de sectie **Structuur/Groep op instellingen** onderaan het volgende in:
    * Boomtype:`Parent/Child`
    * Id-veld:`Id`
    * Bovenliggend-idveld:`ParentId`
    * De uitvouwer weergeven op:`Name`
    * Vouw het bovenste niveau van de boom uit:`checked`
8. Stel in de sectie _Kolommen_ bovenaan het volgende in:
    * _Id_ - Kolomrenderer:`Hidden`
    * _Bovenliggende id_ - Kolomrenderer:`Hidden`
    * _Aanvragen_ - Kolomrenderer: `Bar` `Blue`, Kleur: , Minimumwaarde:`0`
9. Klik op de knop _Opslaan en sluiten_ onder aan het deelvenster.    

![Schermafbeelding van de overzichtsweergave van tegels](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Structuurinstellingen

| Instelling | Uitleg |
|:------------- |:-------------|
| `Id Field` | De unieke id van elke rij in het raster |
| `Parent Id Field` | De id van de bovenliggende van de huidige rij |
| `Show the expander on` | De kolom waarop de tree expander te tonen. Het is gebruikelijk dat boomrasters hun id- en bovenliggende id-veld verbergen omdat ze niet erg leesbaar zijn. In plaats daarvan wordt de uitvouwer weergegeven op een veld met een meer leesbare waarde, zoals de naam van de entiteit |
| `Expand the top level of the tree` | Indien aangevinkt, wordt het boomraster op het hoogste niveau uitgebreid. Handig als u standaard meer informatie wilt weergeven |

## <a name="graphs"></a>Grafieken

Werkmappen ondersteunen het visualiseren van willekeurige grafieken op basis van gegevens uit logboeken om de relaties tussen bewakingsentiteiten weer te geven.

De onderstaande grafiek toont gegevens die in/uit een computer stromen via verschillende poort-naar-/van externe computers. Het wordt gekleurd op type (computer vs. poort versus extern IP) en de randgroottekomt overeen met de hoeveelheid gegevens die ertussen binnenstromen. De onderliggende gegevens zijn afkomstig van VM-verbindingen voor KQL-querytargeting.

![Schermafbeelding van de overzichtsweergave van tegels](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Een grafiek toevoegen
1. Schakel de werkmap over naar de bewerkingsmodus door op het werkbalkitem _Bewerken_ te klikken.
2. Gebruik de koppeling **Query toevoegen** om een logboekquerybesturingselement toe te voegen aan de werkmap. 
3. Selecteer het querytype als **Logboek,** resourcetype (bijvoorbeeld Toepassingsinzichten) en de resources die u wilt targeten.
4. De queryeditor gebruiken om de KQL in te voeren voor uw analyse
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
7. De visualisatie instellen op **Grafiek**
8. Klik op de knop **Grafiekinstellingen** om het instellingenvenster te openen
9. Stel in _Indelingsvelden_ onderaan het gewenste aantal in:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. Stel boven in Instellingen voor _knooppuntindeling:_
    * _Topinhoud_- Kolom `Name`gebruiken: , Kolomrenderer:`Text`
    * _Inhoud centreren_- `Calls`Kolom gebruiken: `Big Number`, Kolomrenderer: , Kleurenpalet:`None`
    * _Bodeminhoud_- Kolom `Kind`gebruiken: , Kolomrenderer:`Text`
10. Klik op de knop _Opslaan en sluiten_ onder aan het deelvenster.

![Schermafbeelding van de overzichtsweergave van tegels](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Volgende stappen

* [Werkmappen implementeren](workbooks-automate.md) met Azure Resource Manager.
* [Beheer](workbooks-access-control.md) en deel de toegang tot uw werkmapbronnen.
