---
title: Toegang tot gegevens in Jupyter-notitieblokken - Proefversie van Azure Notebooks
description: Meer informatie over het openen van bestanden, REST-API's, databases en verschillende Azure Storage-bronnen uit een Jupyter-notitieblok.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646344"
---
# <a name="access-cloud-data-in-a-notebook"></a>Toegang tot cloudgegevens in een notebook

Het doen van interessant werk in een Jupyter notebook vereist gegevens. Data, inderdaad, is de levensader van notebooks.

U zeker [gegevensbestanden importeren in een project,](work-with-project-data-files.md)zelfs met opdrachten zoals `curl` vanuit een notitieblok om een bestand rechtstreeks te downloaden. Het is echter waarschijnlijk dat u moet werken met veel uitgebreidere gegevens die beschikbaar zijn uit niet-bestandsbronnen zoals REST API's, relationele databases en cloudopslag zoals Azure-tabellen.

In dit artikel worden deze verschillende opties kort beschreven. Omdat gegevenstoegang het best in actie kan worden gezien, u runnable code vinden in de [Voorbeelden van Azure Notebooks - Toegang tot uw gegevens](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>REST-API’s

Over het algemeen is de enorme hoeveelheid gegevens die beschikbaar zijn via het internet niet toegankelijk via bestanden, maar via REST API's. Gelukkig, omdat een notebook cel kan bevatten welke code je wilt, u code gebruiken om verzoeken te verzenden en JSON-gegevens te ontvangen. U dat JSON vervolgens converteren naar het formaat dat u wilt gebruiken, zoals een panda's-gegevensframe.

Als u toegang wilt krijgen tot gegevens met een REST-API, gebruikt u dezelfde code in de codecellen van een notitieblok die u in een andere toepassing gebruikt. De algemene structuur met behulp van de aanvragen bibliotheek is als volgt:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL-databases

U hebt toegang tot SQL Server-databases met behulp van de pyodbc- of pymssql-bibliotheken.

[Gebruik Python om een Azure SQL-database op te](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) vragen geeft u instructies voor het maken van een database met AdventureWorks-gegevens en laat zien hoe u die gegevens opvragen. Dezelfde code wordt weergegeven in het voorbeeldnotitieblok voor dit artikel.

## <a name="azure-storage"></a>Azure Storage

Azure Storage biedt verschillende soorten niet-relationele opslag, afhankelijk van het type gegevens dat u hebt en hoe u er toegang toe moet krijgen:

- Tabelopslag: biedt goedkope opslag met een hoog volume voor tabelgegevens, zoals verzamelde sensorlogboeken, diagnostische logboeken, enzovoort.
- Blob-opslag: biedt bestandsachtige opslag voor elk type gegevens.

Het voorbeeldnotitieblok toont het werken met zowel tabellen als blobs, inclusief het gebruik van een handtekening voor gedeelde toegang om alleen-lezen toegang tot blobs toe te staan.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB biedt een volledig geïndexeerd NoSQL-archief voor JSON-documenten). De volgende artikelen bieden een aantal verschillende manieren om te werken met Cosmos DB van Python:

- [Een SQL API-app bouwen met Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Een Flask-app bouwen met de API van Azure Cosmos DB voor MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Een grafiekdatabase maken met Python en de Gremlin-API](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Een Cassandra-app bouwen met Python en Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Een Tabel-API-app bouwen met Python en Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Wanneer u met Cosmos DB werkt, u de [azure-cosmosdb-tafelbibliotheek](https://pypi.org/project/azure-cosmosdb-table/) gebruiken.

## <a name="other-azure-databases"></a>Andere Azure-databases

Azure biedt een aantal andere databasetypen die u gebruiken. De onderstaande artikelen bieden richtlijnen voor de toegang tot deze databases van Python:

- [Azure Database voor PostgreSQL: Python gebruiken om verbinding te maken en gegevens op te vragen](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Snelstart: Azure Redis Cache gebruiken met Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database voor MySQL: Python gebruiken om verbinding te maken en gegevens op te vragen](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure-gegevensfabriek](https://azure.microsoft.com/services/data-factory/)
  - [Wizard Kopiëren voor Azure-gegevensfabriek](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Volgende stappen

- [Hoe: Werken met projectgegevensbestanden](work-with-project-data-files.md)
