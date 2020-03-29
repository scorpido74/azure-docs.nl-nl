---
title: Ingebouwde notitieblokopdrachten en -functies gebruiken in Azure Cosmos DB (voorbeeld)
description: Meer informatie over het gebruik van ingebouwde opdrachten en functies voor algemene bewerkingen met de ingebouwde notitieblokken van Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513396"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Ingebouwde notitieblokopdrachten en -functies gebruiken in Azure Cosmos DB (voorbeeld)

Met ingebouwde Jupyter-notitieblokken in Azure Cosmos DB u uw gegevens van de Azure-portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u ingebouwde notebookopdrachten en -functies gebruikt om algemene bewerkingen uit te voeren.

## <a name="install-a-new-package"></a>Een nieuw pakket installeren
Nadat u laptopondersteuning voor uw Azure Cosmos-accounts hebt ingeschakeld, u een nieuw notitieblok openen en een pakket installeren.

In een nieuwe codecel voegt u de ``PackageToBeInstalled`` volgende code in en voert u deze uit, ter vervanging van het gewenste Python-pakket.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Dit pakket is beschikbaar voor gebruik vanuit elk notitieblok in de azure cosmos-accountwerkruimte. 

> [!TIP]
> Als uw notitieblok een aangepast pakket vereist, raden we u aan een cel in uw notitieblok toe te voegen om het pakket te installeren, omdat pakketten worden verwijderd als u [de werkruimte opnieuw instelt.](#reset-notebooks-workspace)  

## <a name="run-a-sql-query"></a>Een SQL-query uitvoeren

U ``%%sql`` de magische opdracht gebruiken om een [SQL-query](sql-query-getting-started.md) uit te voeren tegen elke container in uw account. Gebruik de syntaxis:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Vervang ``{database_id}`` ``{container_id}`` en met de naam van de database en container in uw Cosmos-account. Als ``--database`` de ``--container`` argumenten en argumenten niet worden opgegeven, wordt de query uitgevoerd in de [standaarddatabase en container.](#set-default-database-for-queries)
- U elke SQL-query uitvoeren die geldig is in Azure Cosmos DB. De querytekst moet zich op een nieuwe regel begeven.

Bijvoorbeeld: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Voer ```%%sql?``` in een cel uit om de help-documentatie voor de sql-magische opdracht in het notitieblok te bekijken.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Een SQL-query en -uitvoer uitvoeren naar een Pandas DataFrame

U de resultaten ``%%sql`` van een query uitvoeren naar een [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). Gebruik de syntaxis: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Vervang ``{database_id}`` ``{container_id}`` en met de naam van de database en container in uw Cosmos-account. Als ``--database`` de ``--container`` argumenten en argumenten niet worden opgegeven, wordt de query uitgevoerd in de [standaarddatabase en container.](#set-default-database-for-queries)
- Vervang ``{outputDataFrameVar}`` de naam van de variabele DataFrame die de resultaten bevat.
- U elke SQL-query uitvoeren die geldig is in Azure Cosmos DB. De querytekst moet zich op een nieuwe regel begeven. 

Bijvoorbeeld:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>JSON-items uploaden naar een container
U ``%%upload`` de magische opdracht gebruiken om gegevens uit een JSON-bestand te uploaden naar een opgegeven Azure Cosmos-container. Gebruik de volgende opdracht om de items te uploaden:

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Vervang ``{database_id}`` ``{container_id}`` en met de naam van de database en container in uw Azure Cosmos-account. Als ``--database`` de ``--container`` argumenten en argumenten niet worden opgegeven, wordt de query uitgevoerd in de [standaarddatabase en container.](#set-default-database-for-queries)
- Vervang ``{url_location_of_file}`` de locatie van uw JSON-bestand. Het bestand moet een array van geldige JSON-objecten zijn en moet toegankelijk zijn via het openbare internet.

Bijvoorbeeld:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Met de uitvoerstatistieken u de effectieve RU/s berekenen die worden gebruikt om de items te uploaden. Als er bijvoorbeeld 25.000 RU's in 38 seconden zijn verbruikt, is de effectieve RU/s 25.000 RU/s / 38 seconden = 658 RU/s.

## <a name="set-default-database-for-queries"></a>Standaarddatabase instellen voor query's
U de ```%%sql``` standaarddatabaseopdrachten instellen die voor het notitieblok worden gebruikt. Vervang ```{database_id}``` door de naam van uw database.

```bash
%database {database_id}
```
Voer ```%database?``` in een cel om documentatie in het notitieblok te bekijken.

## <a name="set-default-container-for-queries"></a>Standaardcontainer instellen voor query's
U de ```%%sql``` standaardcontaineropdrachten instellen die voor het notitieblok worden gebruikt. Vervang ```{container_id}``` door de naam van uw container.

```bash
%container {container_id}
```
Voer ```%container?``` in een cel om documentatie in het notitieblok te bekijken.

## <a name="use-built-in-nteract-data-explorer"></a>Ingebouwde nteract-gegevensverkenner gebruiken
U de ingebouwde [nteract-gegevensverkenner](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) gebruiken om een DataFrame te filteren en te visualiseren. Als u deze functie ``pd.options.display.html.table_schema`` wilt ``True`` ``pd.options.display.max_rows`` inschakelen, stelt u de ``pd.options.display.max_rows`` ``None`` optie in op en op de gewenste waarde (u instellen om alle resultaten weer te geven).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract data explorer](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>Gebruik de ingebouwde Python SDK
Versie 4 van de [Azure Cosmos DB Python SDK voor SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) is geïnstalleerd en opgenomen in de notebookomgeving voor het Azure Cosmos-account.

Gebruik de ingebouwde ``cosmos_client`` instantie om een SDK-bewerking uit te voeren. 

Bijvoorbeeld:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Zie [Python SDK-voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> De ingebouwde Python SDK wordt alleen ondersteund voor SQL (Core) API-accounts. Voor andere API's moet u [het relevante Python-stuurprogramma installeren](#install-a-new-package) dat overeenkomt met de API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Een aangepaste instantie maken van``cosmos_client``
Voor meer flexibiliteit u een ``cosmos_client`` aangepaste instantie maken om:

- Het [verbindingsbeleid](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview) aanpassen
- Bewerkingen uitvoeren op een ander Azure Cosmos-account dan het account waarin u zich bevindt

U hebt toegang tot de verbindingstekenreeks en de primaire sleutel van de lopende rekening via de [omgevingsvariabelen.](#access-the-account-endpoint-and-primary-key-env-variables) 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Toegang tot het accounteindpunt en de primaire sleutelenv-variabelen
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> De ``COSMOS_ENDPOINT`` ``COSMOS_KEY`` omgevingsvariabelen en omgevingsvariabelen zijn alleen van toepassing op SQL API. Voor andere API's vindt u het eindpunt en de sleutel in het **blade Van Verbindingstekenreeksen** of **Sleutels** in uw Azure Cosmos-account.  

## <a name="reset-notebooks-workspace"></a>Werkruimte notitieblokken opnieuw instellen
Als u de werkruimte voor notitieblokken wilt herstellen naar de standaardinstellingen, selecteert u **Werkruimte opnieuw instellen** op de opdrachtbalk. Hierdoor worden aangepaste geïnstalleerde pakketten verwijderd en wordt de Jupyter-server opnieuw opgestart. Uw notitieblokken, bestanden en Azure Cosmos-bronnen worden niet beïnvloed.  

![Werkruimte notitieblokken opnieuw instellen](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de voordelen van [Azure Cosmos DB Jupyter-notitieblokken](cosmosdb-jupyter-notebooks.md)
- Meer informatie over de [Azure Cosmos DB Python SDK voor SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
