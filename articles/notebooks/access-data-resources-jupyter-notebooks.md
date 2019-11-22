---
title: Toegang tot gegevensresources via Jupyter-notebooks in Azure
description: Klik hier voor meer informatie over het openen van bestanden, REST-API's, databases en andere Azure Storage-resources van een Jupyter-notebook.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 23acdf99f6cb69f100e484e236580f3b2b43ba94
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277688"
---
# <a name="access-cloud-data-in-a-notebook"></a>Toegang tot cloud-gegevens in een notitieblok

Interessante werk in een Jupyter-notebook vereist dat gegevens. Gegevens, is namelijk de levensader van notitieblokken.

Natuurlijk kunt u [gegevensbestanden importeren in een project](work-with-project-data-files.md), zelfs met behulp van opdrachten zoals `curl` uit in een notitieblok voor het downloaden van een bestand rechtstreeks. Het is echter waarschijnlijk dat u wilt werken met veel meer uitgebreide gegevens die beschikbaar is op geen bestand-bronnen, zoals REST API's en relationele databases en opslag, zoals Azure-tabellen in de cloud.

In dit artikel bevat kort een overzicht van deze verschillende opties. Omdat de toegang tot gegevens is best in actie zien, vindt u uitvoerbaar code in de [voorbeelden van Azure-notitieblokken - toegang tot uw gegevens](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>REST API's

In het algemeen is de enorme hoeveelheid gegevens die beschikbaar zijn via Internet toegankelijk niet via bestanden, maar via REST-API's. Gelukkig, omdat een cel notebook mag de code die u wilt, kunt u code voor het verzenden van aanvragen en ontvangen van JSON-gegevens. Vervolgens kunt u JSON converteren naar welke indeling u gebruiken wilt, zoals een pandas dataframe.

Voor toegang tot gegevens met behulp van een REST-API, gebruikt u dezelfde code in van een laptop code cellen die u in een andere toepassing gebruikt. De algemene structuur met behulp van de bibliotheek aanvragen is als volgt:

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

U kunt toegang tot SQL Server-databases met de hulp van de pyodbc of pymssql-bibliotheken.

[Python gebruiken om aan te vragen uit een Azure SQL-database](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) vindt u instructies voor het maken van een database met gegevens van AdventureWorks en laat zien hoe die gegevens op te vragen. Dezelfde code wordt weergegeven in de voorbeeld-notebook voor dit artikel.

## <a name="azure-storage"></a>Azure Storage

Azure Storage voorziet in verschillende typen niet-relationele opslag, afhankelijk van het type gegevens die u hebt en hoe u nodig hebt om deze te openen:

- Table Storage: biedt lage kosten, hoge volumes opslag voor gegevens in tabelvorm, zoals verzamelde sensor logs en diagnostische logboeken.
- BLOB-opslag: biedt bestand-achtige opslag voor elk type gegevens.

De notebook voorbeeld ziet u werken met tabellen en -blobs, met inbegrip van het gebruik van een shared access signature voor alleen-lezen toegang tot blobs.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB biedt een volledig geïndexeerde NoSQL-archief voor JSON-documenten). De volgende artikelen bieden een aantal verschillende manieren om te werken met Cosmos DB met Python:

- [Bouw een SQL-API-app met Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Bouw een kolf-app met de API van het Azure Cosmos DB voor MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Een grafiekdatabase met behulp van Python en de Gremlin-API maken](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Een Cassandra-app ontwikkelen met Python en Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Een tabel met Python en Azure Cosmos DB API-app bouwen](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Als u werkt met Cosmos DB, kunt u de [azure-cosmos DB-tabel](https://pypi.org/project/azure-cosmosdb-table/) bibliotheek.

## <a name="other-azure-databases"></a>Andere Azure-databases

Azure biedt een aantal andere gegevenstypen van de database die u kunt gebruiken. De onderstaande artikelen bieden hulp voor toegang tot deze databases met Python:

- [Azure Database voor PostgreSQL: Python gebruiken om te verbinden en gegevens op te vragen](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Snelstartgids: Azure Redis-Cache gebruiken met Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database voor MySQL: Python gebruiken om te verbinden en gegevens op te vragen](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [De Wizard voor Azure Data Factory kopiëren](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Volgende stappen

- [Hoe: werken met gegevens projectbestanden](work-with-project-data-files.md)
