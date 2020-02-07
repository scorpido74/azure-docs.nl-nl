---
title: Een Jupyter Notebook gebruiken voor het analyseren van gegevens in azure Data Explorer
description: In dit onderwerp wordt beschreven hoe u gegevens in azure Data Explorer analyseert met behulp van een Jupyter Notebook en de Kqlmagic-extensie.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 83902ea5a3e73603311a0c469126ed603d0ebd16
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064866"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Een Jupyter Notebook-en Kqlmagic-extensie gebruiken voor het analyseren van gegevens in azure Data Explorer

Jupyter Notebook is een open-source webtoepassing waarmee u documenten kunt maken en delen met Live code, vergelijkingen, visualisaties en tekst. Gebruik omvat het opschonen van gegevens en trans formatie, het numeriek simuleren, statistisch model leren, gegevens visualisatie en machine learning.
[Jupyter notebook](https://jupyter.org/) ondersteunt Magic-functies die de mogelijkheden van de kernel uitbreiden door extra opdrachten te ondersteunen. KQL Magic is een opdracht waarmee de mogelijkheden van de python-kernel in Jupyter Notebook worden uitgebreid, zodat u Kusto taal query's systeem eigen kunt uitvoeren. U kunt de python-en Kusto-query taal eenvoudig combi neren om gegevens op te vragen en te visualiseren met de uitgebreide Plot.ly-bibliotheek, geïntegreerd met `render`-opdrachten. Gegevens bronnen voor het uitvoeren van query's worden ondersteund. Deze gegevens bronnen zijn onder andere Azure Data Explorer, een snelle en zeer schaal bare service voor gegevens verkenning voor logboek-en telemetriegegevens, evenals Azure Monitor logboeken en Application Insights. KQL Magic werkt ook met de extensie Azure Notebooks, Jupyter Lab en Visual Studio code Jupyter.

## <a name="prerequisites"></a>Vereisten

- Organisatie-e-mail account dat lid is van Azure Active Directory (AAD).
- Jupyter Notebook op uw lokale machine geïnstalleerd of gebruik Azure Notebooks en kloon de voor beeld- [Azure-notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>KQL Magic-bibliotheek installeren

1. KQL Magic installeren:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Wanneer u Azure Notebooks gebruikt, is deze stap niet vereist.

1. KQL Magic laden:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Wijzig de kernel-versie in Python 3,6 door te klikken op kernel > kernel te wijzigen > python 3,6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Verbinding maken met het Azure Data Explorer Help-cluster

Gebruik de volgende opdracht om verbinding te maken met de *voorbeeld* database die op het *Help* -cluster wordt gehost. Voor gebruikers die niet van micro soft zijn AAD, vervangt u de Tenant naam `Microsoft.com` met uw AAD-Tenant.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Query's uitvoeren en visualiseren

Gegevens opvragen met behulp van de [render-operator](/azure/kusto/query/renderoperator) en gegevens visualiseren met behulp van de Ploy.ly-bibliotheek. Deze query en visualisatie biedt een geïntegreerde ervaring waarbij gebruik wordt gemaakt van systeem eigen KQL. Kqlmagic biedt ondersteuning voor de meeste grafieken, behalve `timepivot`, `pivotchart`en `ladderchart`. Rendering wordt ondersteund met alle kenmerken behalve `kind`, `ysplit`en `accumulate`. 

### <a name="query-and-render-piechart"></a>Piechart opvragen en weer geven

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Timechart opvragen en weer geven

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Deze grafieken zijn interactief. Selecteer een tijds bereik om in te zoomen op een specifieke tijd.

### <a name="customize-the-chart-colors"></a>De grafiek kleuren aanpassen

Als u het standaardkleuren palet niet mooi vindt, past u de grafieken aan met behulp van de palet opties. De beschik bare paletten vindt u hier: [kleuren palet kiezen voor het KQL Magic-query diagram resultaat](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Voor een lijst met paletten:

    ```python
    %kql --palettes -popup_window
    ```

1. Selecteer het `cool` kleuren palet en Genereer de query opnieuw:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Een query para meters met python

KQL Magic maakt eenvoudige uitwisseling mogelijk tussen Kusto-query taal en python. Voor meer informatie: [para meters your KQL Magic query with python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Een python-variabele gebruiken in uw KQL-query

U kunt de waarde van een python-variabele in uw query gebruiken om de gegevens te filteren:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Query resultaten converteren naar Panda data frame

U kunt de resultaten van een KQL-query openen in Pandas data frame. Open de laatst uitgevoerde query resultaten op variabele `_kql_raw_result_` en converteer de resultaten eenvoudig naar een Panda data frame als volgt:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Voorbeeld

In veel analyse scenario's wilt u mogelijk herbruikbare notitie blokken maken die veel query's bevatten en de resultaten van een query in de volgende query's invoeren. In het volgende voor beeld wordt de python-variabele `statefilter` gebruikt om de gegevens te filteren.

1. Voer een query uit om de tien statussen met het maximum aantal `DamageProperty`weer te geven:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Voer een query uit om de bovenste status op te halen en in te stellen als een python-variabele:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Voer een query uit met behulp van de `let`-instructie en de python-variabele:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Voer de Help-opdracht uit:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Gebruik `%config Kqlmagic`om informatie over alle beschik bare configuraties te ontvangen. Gebruik `%config Kqlmagic.short_errors=False` om problemen met Kusto op te lossen en vast te leggen, zoals verbindings problemen en onjuiste query's.

## <a name="next-steps"></a>Volgende stappen

Voer de Help opdracht uit om de volgende voorbeeld notitieblokken te verkennen die alle ondersteunde functies bevatten:
- [Aan de slag met KQL Magic voor Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Aan de slag met KQL Magic voor Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Aan de slag met KQL Magic voor Azure Monitor-logboeken](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize uw KQL Magic-query met python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Kleuren palet kiezen voor het KQL Magic-query diagram resultaat](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
