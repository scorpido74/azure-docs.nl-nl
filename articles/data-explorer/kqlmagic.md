---
title: Een Jupyter-notitieblok gebruiken om gegevens te analyseren in Azure Data Explorer
description: In dit onderwerp ziet u hoe u gegevens in Azure Data Explorer analyseert met behulp van een Jupyter-notitieblok en de Kqlmagic-extensie.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 83902ea5a3e73603311a0c469126ed603d0ebd16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064866"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Een Jupyter-notebook- en Kqlmagic-extensie gebruiken om gegevens in Azure Data Explorer te analyseren

Jupyter Notebook is een open-source webapplicatie waarmee u documenten maken en delen met live code, vergelijkingen, visualisaties en verhalende tekst. Het gebruik omvat gegevensreiniging en -transformatie, numerieke simulatie, statistische modellering, gegevensvisualisatie en machine learning.
[Jupyter Notebook](https://jupyter.org/) ondersteunt magische functies die de mogelijkheden van de kernel uitbreiden door extra opdrachten te ondersteunen. KQL-magie is een opdracht die de mogelijkheden van de Python-kernel in Jupyter Notebook uitbreidt, zodat u Kusto-taalquery's native uitvoeren. U python- en Kusto-querytaal eenvoudig combineren om `render` gegevens op te vragen en te visualiseren met behulp van rijke Plot.ly bibliotheek die is geïntegreerd met opdrachten. Gegevensbronnen voor het uitvoeren van query's worden ondersteund. Deze gegevensbronnen omvatten Azure Data Explorer, een snelle en zeer schaalbare gegevensverkenningsservice voor logboek- en telemetriegegevens, evenals Azure Monitor-logboeken en toepassingsinzichten. KQL-magie werkt ook met Azure Notebooks, Jupyter Lab en Visual Studio Code Jupyter-extensie.

## <a name="prerequisites"></a>Vereisten

- Organisatie-e-mailaccount dat lid is van Azure Active Directory (AAD).
- Jupyter-notitieblok is geïnstalleerd op uw lokale machine of gebruikt Azure-notitieblokken en kloont het voorbeeld [azure-notitieblok](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>KQL-magische bibliotheek installeren

1. Installeer KQL magie:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Bij het gebruik van Azure Notebooks is deze stap niet vereist.

1. KQL-magie laden:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Wijzig de Kernel-versie in Python 3.6 door op Kernel > Change Kernel > Python 3.6 te klikken
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Verbinding maken met het Help-cluster azure Data Explorer

Gebruik de volgende opdracht om verbinding te maken met de *database Samples* die wordt gehost in het *Help-cluster.* Voor niet-Microsoft AAD-gebruikers vervangt `Microsoft.com` u de tenantnaam door uw AAD-tenant.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Query's en visualiseren

Querygegevens met behulp van de [renderoperator](/azure/kusto/query/renderoperator) en visualiseer gegevens met behulp van de ploy.ly bibliotheek. Deze query en visualisatie biedt een geïntegreerde ervaring die gebruik maakt van native KQL. Kqlmagic ondersteunt de `timepivot` `pivotchart`meeste `ladderchart`grafieken, behalve , , en . Render wordt ondersteund met alle `kind` `ysplit`kenmerken, `accumulate`behalve , en . 

### <a name="query-and-render-piechart"></a>Cirkeldiagram opvragen en renderen

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Tijddiagram query's en renderen

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Deze grafieken zijn interactief. Selecteer een tijdsbereik om in te zoomen op een bepaalde tijd.

### <a name="customize-the-chart-colors"></a>De grafiekkleuren aanpassen

Als het standaardkleurenpalet u niet bevalt, past u de grafieken aan met paletopties. De beschikbare paletten zijn hier te vinden: [Kies kleuren palet voor uw KQL magische query grafiek resultaat](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Voor een lijst met paletten:

    ```python
    %kql --palettes -popup_window
    ```

1. Selecteer `cool` het kleurenpalet en maak de query opnieuw renderen:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Een query parameteriseren met Python

KQL magie zorgt voor een eenvoudige uitwisseling tussen Kusto query taal en Python. Voor meer informatie: [Parameterize uw KQL magische query met Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Een Python-variabele gebruiken in uw KQL-query

U de waarde van een Python-variabele in uw query gebruiken om de gegevens te filteren:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Queryresultaten converteren naar Pandas DataFrame

U hebt toegang tot de resultaten van een KQL-query in Pandas DataFrame. Toegang tot de laatst uitgevoerde `_kql_raw_result_` queryresultaten op variabele en zet de resultaten eenvoudig als volgt om in Pandas DataFrame:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Voorbeeld

In veel analysescenario's u herbruikbare notitieblokken maken die veel query's bevatten en de resultaten van één query in volgende query's verwerken. In het onderstaande `statefilter` voorbeeld wordt de variabele Python gebruikt om de gegevens te filteren.

1. Voer een query uit om de `DamageProperty`top 10-statussen met maximaal te bekijken:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Voer een query uit om de bovenste status te extraheren en deze in te stellen op een Python-variabele:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Voer een query `let` uit met de instructie en de variabele Python:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Voer de opdracht Help uit:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Gebruik . `%config Kqlmagic` Gebruik om Kusto-fouten op te lossen en vast te leggen, zoals verbindingsproblemen en onjuiste query's,`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Volgende stappen

Voer de opdracht Help uit om de volgende voorbeeldnotitieblokken te verkennen die alle ondersteunde functies bevatten:
- [Aan de slag met KQL-magie voor Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Aan de slag met KQL-magie voor Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Aan de slag met KQL-magie voor Azure Monitor-logboeken](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize uw KQL magische query met Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Kies kleurenpalet voor het resultaat van uw KQL-magische querydiagram](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
