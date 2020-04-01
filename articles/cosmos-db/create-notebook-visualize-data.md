---
title: 'Zelfstudie: Een notitieblok maken in Azure Cosmos DB om de gegevens te analyseren en te visualiseren'
description: 'Zelfstudie: Meer informatie over het gebruik van ingebouwde Jupyter-notitieblokken om gegevens te importeren naar Azure Cosmos DB, de gegevens te analyseren en de uitvoer te visualiseren.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 45dd4e8dcfd74cdb5d96b935e239b9f4b5094a7c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73720921"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Zelfstudie: Een notitieblok maken in Azure Cosmos DB om de gegevens te analyseren en te visualiseren

In dit artikel wordt beschreven hoe u ingebouwde Jupyter-notitieblokken gebruiken om voorbeeldgegevens van detailhandelsgegevens te importeren in Azure Cosmos DB. U ziet hoe u de sql- en Azure Cosmos DB-magische opdrachten gebruiken om query's uit te voeren, de gegevens te analyseren en de resultaten te visualiseren.

## <a name="prerequisites"></a>Vereisten

* [Ondersteuning voor notitieblokken inschakelen tijdens het maken van het Azure Cosmos-account](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>De resources en importgegevens maken
 
In deze sectie maakt u de Azure Cosmos-database, de container en de invoer van de detailhandelsgegevens naar de container.

1. Navigeer naar uw Azure Cosmos-account en open de **Data Explorer.**

1. Ga naar het tabblad `…` **Notitieblokken,** selecteer naast **Mijn notitieblokken** en maak een **nieuw notitieblok**. Selecteer **Python 3** als standaardkernel.

   ![Een nieuwe notebook maken](./media/create-notebook-visualize-data/create-new-notebook.png)

1. Nadat een nieuw notitieblok is gemaakt, u deze hernoemen naar iets als **VisualizeRetailData.ipynb.**

1. Vervolgens maakt u een database met de naam "RetailDemo" en een container met de naam "WebsiteData" om de retailgegevens op te slaan. U /CardID als partitiesleutel gebruiken. Kopieer en plak de volgende code naar een nieuwe cel in uw notitieblok en voer deze uit:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Als u een `Shift + Enter` cel wilt uitvoeren, selecteert u de cel of selecteert u de optie **Actieve cel uitvoeren** op de navigatiebalk van de gegevensverkenner.

   ![De actieve cel uitvoeren](./media/create-notebook-visualize-data/run-active-cell.png)

   De database en container worden gemaakt in uw huidige Azure Cosmos-account. De container is voorzien van 400 RU/s. U ziet de volgende uitvoer nadat de database en container is gemaakt. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   U ook het tabblad **Gegevens** vernieuwen en de nieuw gemaakte bronnen bekijken:

   ![Het tabblad Gegevens vernieuwen om de nieuwe container te bekijken](media/create-notebook-visualize-data/refresh-data-tab.png)

1. Vervolgens importeert u de voorbeeldgegevens voor detailhandelsgegevens in de Azure Cosmos-container. Hier is het formaat van een item uit de retail gegevens:

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

   Voor het zelfstudiedoel worden de voorbeeldgegevens van de detailhandel opgeslagen in de Azure blob-opslag. U deze importeren in de Azure Cosmos-container door de volgende code in een nieuwe cel te plakken. U bevestigen dat de gegevens zijn geïmporteerd door een query uit te voeren om het aantal items te selecteren.

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

## <a name="get-your-data-into-a-dataframe"></a>Uw gegevens in een DataFrame krijgen

Voordat u query's uitvoert om de gegevens te analyseren, u de gegevens van container tot een [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) lezen voor analyse. Gebruik de volgende sql-magische opdracht om de gegevens in een DataFrame te lezen:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Zie de [ingebouwde laptopopdrachten en -functies in](use-notebook-features-and-commands.md) het artikel Azure Cosmos DB voor meer informatie. U voert de `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`query uit. De resultaten worden opgeslagen in een Pandas DataFrame met de naam df_cosmos. Plak de volgende opdracht in een nieuwe notitieblokcel en voer deze uit:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Voer in een nieuwe notitieblokcel de volgende code uit om de eerste 10 items uit de uitvoer te lezen:

```python
# See a sample of the result
df_cosmos.head(10)
```

![Query uitvoeren om top 10-items te krijgen](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>Query's uitvoeren en uw gegevens analyseren

In deze sectie voert u enkele query's uit op de opgehaalde gegevens.

* **Query1:** Voer een groep op query uit op het DataFrame om de som van de totale omzet voor elk land op te halen en 5 items uit de resultaten weer te geven. Voer in een nieuwe notitieblokcel de volgende code uit:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![Totale omzetopbrengst](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **Query2:** Als u een lijst met vijf gekochte items wilt krijgen, opent u een nieuwe notitieblokcel en voert u de volgende code uit:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![Top vijf gekochte artikelen](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>Uw gegevens visualiseren  

1. Nu we onze gegevens over de inkomsten uit de Azure Cosmos-container hebben, u uw gegevens visualiseren met een visualisatiebibliotheek naar keuze. In deze tutorial gebruiken we de Bokeh-bibliotheek. Open een nieuwe notebookcel en voer de volgende code uit om de Bokeh-bibliotheek te installeren. Nadat aan alle eisen is voldaan, wordt de bibliotheek geïnstalleerd.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Vervolgens voor te bereiden op de gegevens plot op een kaart. Sluit u aan bij de gegevens in Azure Cosmos DB met landgegevens in Azure Blob-opslag en converteer het resultaat naar de GeoJSON-indeling. Kopieer de volgende code naar een nieuwe notitieblokcel en voer deze uit.

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

1. Visualiseer de verkoopopbrengsten van verschillende landen op een wereldkaart door de volgende code in een nieuwe notitieblokcel uit te voeren:

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

   De uitvoer toont de wereldkaart met verschillende kleuren. De kleuren donkerder tot lichter vertegenwoordigen de landen met de hoogste omzet tot de laagste omzet.

   ![Visualisatie van landen-inkomstenkaart](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. Laten we eens kijken naar een ander geval van gegevensvisualisatie. De WebsiteData-container bevat gegevens van gebruikers die een item hebben bekeken, aan hun winkelwagentje hebben toegevoegd en het item hebben gekocht. Laten we de conversieratio van gekochte artikelen uitzetten. Voer de volgende code uit in een nieuwe cel om de conversieratio voor elk item te visualiseren:

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

   ![Aankoopconversieratio visualiseren](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>Volgende stappen

* Zie in het artikel van [Azure Cosmos DB](use-notebook-features-and-commands.md) voor meer informatie over laptopopdrachten.
