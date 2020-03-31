---
title: Feed wijzigen in de Azure Cosmos DB API voor Cassandra
description: Meer informatie over het gebruik van de wijzigingsfeed in de Azure Cosmos DB API voor Cassandra om de wijzigingen in uw gegevens op te halen.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694621"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Feed wijzigen in de Azure Cosmos DB API voor Cassandra

[Feed-ondersteuning wijzigen](change-feed.md) in de Azure Cosmos DB API voor Cassandra is beschikbaar via de querypredicaten in de Cassandra Query Language (CQL). Met behulp van deze predicaatvoorwaarden u de API voor wijzigingsfeed opvragen. Toepassingen kunnen de wijzigingen in een tabel krijgen met behulp van de primaire sleutel (ook wel de partitiesleutel genoemd) zoals vereist in CQL. U dan verdere acties ondernemen op basis van de resultaten. Wijzigingen in de rijen in de tabel worden vastgelegd in de volgorde van hun wijzigingstijd en de sorteervolgorde is gegarandeerd per partitiesleutel.

In het volgende voorbeeld ziet u hoe u een wijzigingsfeed krijgen voor alle rijen in een Cassandra API Keyspace-tabel met behulp van .NET. Het predicaat COSMOS_CHANGEFEED_START_TIME() wordt direct binnen CQL gebruikt om items in de wijzigingsfeed op te vragen vanaf een bepaalde begintijd (in dit geval huidige datum). U het volledige voorbeeld [hier](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)downloaden.

In elke iteratie worden de queryhervat op het laatste punt wijzigingen gelezen, met behulp van paging status. We zien een continue stroom van nieuwe wijzigingen in de tabel in de Keyspace. We zien wijzigingen in rijen die worden ingevoegd of bijgewerkt. Het bekijken van verwijderingsbewerkingen met behulp van wijzigingsfeed in de Cassandra-API wordt momenteel niet ondersteund. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Als u de wijzigingen in één rij per primaire sleutel wilt krijgen, u de primaire sleutel in de query toevoegen. In het volgende voorbeeld ziet u hoe u wijzigingen bijhoudt voor de rij waarin "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing bij het gebruik van change feed met Cassandra API:

* Inserts en updates worden momenteel ondersteund. De bewerking verwijderen wordt nog niet ondersteund. Als tijdelijke oplossing u een zachte markering toevoegen aan rijen die worden verwijderd. Voeg bijvoorbeeld een veld toe in de rij met de naam 'verwijderd' en stel het in op 'waar'.
* De laatste update wordt gehandhaafd omdat in de CORE SQL API en tussentijdse updates voor de entiteit niet beschikbaar zijn.


## <a name="error-handling"></a>Foutafhandeling

De volgende foutcodes en berichten worden ondersteund bij het gebruik van change feed in Cassandra API:

* **HTTP-foutcode 429** - Wanneer de wijzigingsfeed beperkt is, wordt een lege pagina geretourneerd.

## <a name="next-steps"></a>Volgende stappen

* [Azure Cosmos DB Cassandra API-resources beheren met Azure Resource Manager-sjablonen](manage-cassandra-with-resource-manager.md)
