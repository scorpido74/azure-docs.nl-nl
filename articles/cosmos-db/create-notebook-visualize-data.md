---
title: Een notitie blok maken in Azure Cosmos DB voor het analyseren en visualiseren van de gegevens
description: Meer informatie over het gebruik van ingebouwde Jupyter-notebooks voor het importeren van gegevens in Azure Cosmos DB, het analyseren van de gegevens en het visualiseren van de uitvoer.
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 09/25/2019
ms.author: de
ms.reviewer: sngun
ms.openlocfilehash: 6f007b60d38ea3025bbcdc36ef45673c0f370d05
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338429"
---
# <a name="create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Een notitie blok maken in Azure Cosmos DB voor het analyseren en visualiseren van de gegevens

In dit artikel wordt beschreven hoe u ingebouwde Jupyter-notebooks gebruikt voor het importeren van voor beelden van Retail-gegevens naar Azure Cosmos DB. U ziet hoe u de opdrachten SQL en Azure Cosmos DB Magic gebruikt om query's uit te voeren, de gegevens te analyseren en de resultaten te visualiseren.

## <a name="prerequisites"></a>Vereisten

* [Ondersteuning van notebooks inschakelen tijdens het maken van het Azure Cosmos-account](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>De resources maken en gegevens importeren
 
In deze sectie maakt u de Azure Cosmos-data base, container en importeert u de detail gegevens in de container.

1. Navigeer naar uw Azure Cosmos-account en open de **Data Explorer.**

1. Ga naar het tabblad **notebooks** , selecteer `…` naast **mijn notitie blokken** en maak een **Nieuw notitie blok**. Selecteer **python 3** als standaard-kernel.

   ![Een nieuwe notebook maken](./media/create-notebook-visualize-data/create-new-notebook.png)

1. Nadat u een nieuw notitie blok hebt gemaakt, kunt u de naam wijzigen in iets zoals **VisualizeRetailData. ipynb.**

1. Vervolgens maakt u een Data Base met de naam ' RetailDemo ' en een container met de naam ' WebsiteData ' om de Retail gegevens op te slaan. U kunt/CardID gebruiken als de partitie sleutel. Kopieer en plak de volgende code in een nieuwe cel in uw notitie blok en voer deze uit:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Als u een cel wilt uitvoeren, selecteert u `Shift + Enter` of selecteert u de cel en kiest u de optie **actieve cel uitvoeren** op de navigatie balk van Data Explorer.

   ![De actieve cel uitvoeren](./media/create-notebook-visualize-data/run-active-cell.png)

   De data base en de container worden gemaakt in uw huidige Azure Cosmos-account. De container is ingericht met 400 RU/s. Nadat de data base en de container is gemaakt, wordt de volgende uitvoer weer geven. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   U kunt ook het tabblad **gegevens** vernieuwen en de zojuist gemaakte resources bekijken:

   ![Vernieuw het tabblad gegevens om de nieuwe container weer te geven](media/create-notebook-visualize-data/refresh-data-tab.png)

1. Vervolgens importeert u de voor beeld-Retail gegevens in de Azure Cosmos-container. Dit is de indeling van een item uit de Retail gegevens:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Voor het doel van de zelf studie worden de Retail gegevens van het voor beeld opgeslagen in de Azure Blob-opslag. U kunt het importeren in de Azure Cosmos-container door de volgende code in een nieuwe cel te plakken. U kunt controleren of de gegevens zijn geïmporteerd door een query uit te voeren om het aantal items te selecteren.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Wanneer u de vorige query uitvoert, wordt de volgende uitvoer geretourneerd:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Uw gegevens in een Data Frame ophalen

Voordat u query's uitvoert om de gegevens te analyseren, kunt u de gegevens uit de container lezen in een [Panda data frame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) voor analyse. Gebruik de volgende SQL Magic-opdracht om de gegevens in een data frame te lezen:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Zie voor meer informatie de [ingebouwde opdrachten en functies voor notebooks in azure Cosmos DB](use-notebook-features-and-commands.md) artikel. U voert de query-`SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` uit. De resultaten worden opgeslagen in een Panda data frame met de naam df_cosmos. Plak de volgende opdracht in een nieuwe notebook-cel en voer deze uit:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Voer in een nieuwe notebook-cel de volgende code uit om de eerste tien items van de uitvoer te lezen:

```python
# See a sample of the result
df_cosmos.head(10)
```

![Query uitvoeren om top 10-items op te halen](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>Query's uitvoeren en uw gegevens analyseren

In deze sectie voert u enkele query's uit op de gegevens die worden opgehaald.

* **Query1** Voer een Group by-query uit op de data frame om de som van de totale verkoop opbrengst voor elk land op te halen en vijf items van de resultaten weer te geven. Voer de volgende code uit in een nieuwe notebook-cel:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![Totale uitvoer opbrengst omzet](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **Query2:** Als u een lijst met vijf aangeschafte items wilt ophalen, opent u een nieuwe notebook-cel en voert u de volgende code uit:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![Top vijf aangeschafte items](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>Uw gegevens visualiseren  

1. Nu we onze gegevens over de omzet van de Azure Cosmos-container hebben, kunt u uw gegevens visualiseren met een visualisatie bibliotheek van uw keuze. In deze zelf studie wordt gebruikgemaakt van de bokeh-bibliotheek. Open een nieuwe cel notebook en voer de volgende code uit om de bokeh-bibliotheek te installeren. Nadat aan alle vereisten is voldaan, wordt de bibliotheek geïnstalleerd.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Volgende voor bereiding voor het uitzetten van de gegevens op een kaart. Neem lid van de gegevens in Azure Cosmos DB met de land informatie in Azure Blob-opslag en converteer het resultaat naar geojson-indeling. Kopieer de volgende code naar een nieuwe notebook-cel en voer deze uit.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries dataframe with our data in Azure Cosmos DB, joining on country code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualiseer de verkoop opbrengst van verschillende landen op een wereld kaart door de volgende code uit te voeren in een nieuwe notebook-cel:

   ```python
    from bokeh.io import output_notebook, show
    from bokeh.plotting import figure
    from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
    from bokeh.palettes import brewer
    
    #Input GeoJSON source that contains features for plotting.
    geosource = GeoJSONDataSource(geojson = json_data)
    
    #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
    palette = brewer['YlGn'][8]
    
    #Reverse color order so that dark green is highest revenue
    palette = palette[::-1]
    
    #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
    color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
    #Define custom tick labels for color bar.
    tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
    #Create color bar. 
    color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
    border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
    #Create figure object.
    p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
    p.xgrid.grid_line_color = None
    p.ygrid.grid_line_color = None
    
    #Add patch renderer to figure. 
    p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
    #Specify figure layout.
    p.add_layout(color_bar, 'below')
    
    #Display figure inline in Jupyter Notebook.
    output_notebook()
    
    #Display figure.
    show(p)
   ```

   In de uitvoer wordt de wereld kaart met verschillende kleuren weer gegeven. De kleuren donkerder tot lichter weer spie gelen de landen met de hoogste omzet tot de laagste omzet.

   ![Visualisatie van de kaart omzet](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. Laten we eens kijken naar een ander geval van gegevens visualisatie. De WebsiteData-container bevat een record met gebruikers die een item hebben bekeken, toegevoegd aan hun mandje en het item hebben gekocht. Laten we de conversie frequentie van de gekochte items in het diagram zetten. Voer de volgende code in een nieuwe cel uit om de conversie frequentie voor elk item te visualiseren:

   ```python
    from bokeh.io import show, output_notebook
    from bokeh.plotting import figure
    from bokeh.palettes import Spectral3
    from bokeh.transform import factor_cmap
    from bokeh.models import ColumnDataSource, FactorRange
       
    # Get the top 10 items as an array
    top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
    # Filter our data to only these 10 items
    df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
    # Group by Item and Action, sorting by event count
    df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
    # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
    x_axis_values = df_top10_sorted.index.values.tolist()
    
    group = df_top10_sorted.groupby(['Item', 'Action'])
    
    # Specifiy colors for X axis
    index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
    # Create the plot
    
    p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
    p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
    #Configure how the plot looks
    p.y_range.start = 0
    p.x_range.range_padding = 0.05
    p.xgrid.grid_line_color = None
    p.xaxis.major_label_orientation = 1.2
    p.outline_line_color = "black"
    p.xaxis.axis_label = "Item"
    p.yaxis.axis_label = "Count"
    
    #Display figure inline in Jupyter Notebook.
    output_notebook()
    
    #Display figure.
    show(p)
   ```

   ![Aankoop conversie frequentie visualiseren](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over notebook-opdrachten [ingebouwde notebook opdrachten en-functies gebruiken in azure Cosmos DB](use-notebook-features-and-commands.md) artikel.