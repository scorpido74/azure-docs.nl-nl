---
title: Een verbinding met een Cosmos DB account instellen met behulp van een beheerde identiteit (preview)
titleSuffix: Azure Cognitive Search
description: Meer informatie over het instellen van een verbinding met een Indexeer functie met een Cosmos DB account met behulp van een beheerde identiteit (preview)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 107cd113645a2cbd4b452f9350fa67d734ee6df8
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143656"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>Een Indexeer functie verbinding met een Cosmos DB-Data Base instellen met behulp van een beheerde identiteit (preview)

> [!IMPORTANT] 
> Ondersteuning voor het instellen van een verbinding met een gegevens bron met behulp van een beheerde identiteit bevindt zich momenteel in een open bare preview-versie. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads.
> U kunt toegang tot de preview aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/mi-preview-request)in te vullen.

Op deze pagina wordt beschreven hoe u een Indexeer functie verbinding kunt instellen met een Azure Cosmos DB-Data Base met behulp van een beheerde identiteit in plaats van referenties op te geven in het gegevens bron object connection string.

Voor meer informatie over deze functie kunt u het beste inzicht krijgen in wat een indexer is en hoe u een Indexeer functie instelt voor uw gegevens bron. Meer informatie vindt u op de volgende koppelingen:
* [Overzicht van de indexeerfunctie](search-indexer-overview.md)
* [Indexeerfunctie voor Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Een verbinding instellen met behulp van een beheerde identiteit

### <a name="1---turn-on-system-assigned-managed-identity"></a>1-door het systeem toegewezen beheerde identiteit inschakelen

Wanneer een door het systeem toegewezen beheerde identiteit is ingeschakeld, maakt Azure een identiteit voor uw zoek service die kan worden gebruikt om te verifiëren bij andere Azure-Services binnen dezelfde Tenant en hetzelfde abonnement. U kunt deze identiteit vervolgens gebruiken in toewijzingen op basis van op rollen gebaseerde toegangs beheer (RBAC) die toegang tot gegevens tijdens het indexeren toestaan.

![Door het systeem toegewezen beheerde identiteit inschakelen](./media/search-managed-identities/turn-on-system-assigned-identity.png "Door het systeem toegewezen beheerde identiteit inschakelen")

Nadat u **Opslaan** hebt geselecteerd, ziet u een object-id die is toegewezen aan uw zoek service.

![Object-id](./media/search-managed-identities/system-assigned-identity-object-id.png "Object-id")
 
### <a name="2---add-a-role-assignment"></a>2-een roltoewijzing toevoegen

In deze stap geeft u uw Azure Cognitive Search-service toestemming om gegevens uit uw Cosmos DB-Data Base te lezen.

1. Navigeer in het Azure Portal naar het Cosmos DB-account dat de gegevens bevat die u wilt indexeren.
2. **Toegangs beheer (IAM)** selecteren
3. Selecteer **toevoegen** en vervolgens **functie toewijzing toevoegen**

    ![Roltoewijzing toevoegen](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Roltoewijzing toevoegen")

4. De rol van de **Cosmos DB-account lezer** selecteren
5. **Wijs toegang toe** als **Azure AD-gebruiker,-groep of-Service-Principal**
6. Zoek naar uw zoek service, Selecteer deze en selecteer vervolgens **Opslaan** .

    ![Toewijzing van de rol lezer en gegevens toegang toevoegen](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Toewijzing van de rol lezer en gegevens toegang toevoegen")

### <a name="3---create-the-data-source"></a>3: de gegevens bron maken

Een **gegevens bron** geeft de gegevens aan die moeten worden geïndexeerd, referenties en beleid voor het identificeren van wijzigingen in de gegevens (zoals gewijzigde of verwijderde documenten in uw verzameling). De gegevens bron wordt gedefinieerd als een onafhankelijke resource, zodat deze kan worden gebruikt door meerdere Indexeer functies.

Wanneer beheerde identiteiten worden gebruikt voor verificatie bij de gegevens bron, bevatten de **referenties** geen account sleutel.

Voor beeld van het maken van een Cosmos DB gegevens bron object met behulp van de [rest API](https://docs.microsoft.com/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

De hoofd tekst van de aanvraag bevat de definitie van de gegevens bron, die de volgende velden moet bevatten:

| Veld   | Beschrijving |
|---------|-------------|
| **naam** | Vereist. Kies een wille keurige naam voor uw gegevens bron object. |
|**type**| Vereist. Moet zijn `cosmosdb` . |
|**aanmeldings** | Vereist. <br/><br/>Wanneer u verbinding maakt met behulp van een beheerde identiteit, moet de indeling van de **referenties** zijn: *Data base = [database naam]; ResourceId = [resource-id-String];(soort = [API-kind];)*<br/> <br/>De ResourceId-indeling: *ResourceID =/Subscriptions/**uw abonnements-id**/resourceGroups/**de naam van de resource groep**/providers/Microsoft.DocumentDB/databaseAccounts/**uw Cosmos DB-account naam**/;*<br/><br/>Voor SQL-verzamelingen is voor de connection string geen soort vereist.<br/><br/>Voeg voor MongoDB-verzamelingen **soort = MongoDb** toe aan de Connection String. <br/><br/>Voor Gremlin-grafieken en Cassandra-tabellen meldt u zich aan voor de preview-versie van de [Indexeer functie](https://aka.ms/azure-cognitive-search/indexer-preview) om toegang te krijgen tot de preview-versie en informatie over het format teren van de referenties.<br/>|
| **verpakking** | Bevat de volgende elementen: <br/>**naam**: vereist. Geef de ID op van de database verzameling die moet worden geïndexeerd.<br/>**query**: optioneel. U kunt een query opgeven voor het afvlakken van een wille keurig JSON-document in een plat schema dat door Azure Cognitive Search kan worden geïndexeerd.<br/>Query's worden niet ondersteund voor de MongoDB-API, de Gremlin-API en de Cassandra-API. |
| **dataChangeDetectionPolicy** | Aanbevolen |
|**dataDeletionDetectionPolicy** | Optioneel |

De Azure Portal en de [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) ondersteunen ook de beheerde identiteiten Connection String. De Azure Portal vereist een functie vlag die aan u wordt gegeven wanneer u zich aanmeldt voor de preview met behulp van de koppeling boven aan deze pagina. 

### <a name="4---create-the-index"></a>4-de index maken

De index specificeert de velden in een document, kenmerken en andere constructies die de zoek ervaring vormen.

Ga als volgt te werk om een index te maken met een doorzoekbaar `booktitle` veld:

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Zie [index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over het maken van indexen

### <a name="5---create-the-indexer"></a>5: de Indexeer functie maken

Een Indexeer functie verbindt een gegevens bron met een doel zoek index en biedt een planning voor het automatiseren van de gegevens vernieuwing.

Zodra de index en gegevens bron zijn gemaakt, kunt u de Indexeer functie maken.

Voor beeld van de definitie van de Indexeer functie:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Deze Indexeer functie wordt elke twee uur uitgevoerd (schema-interval is ingesteld op "PT2H"). Als u een Indexeer functie elke 30 minuten wilt uitvoeren, stelt u het interval in op ' PT30M '. Het kortste ondersteunde interval is 5 minuten. Het schema is optioneel: als u dit weglaat, wordt een Indexeer functie slechts eenmaal uitgevoerd wanneer deze wordt gemaakt. U kunt echter op elk gewenst moment een Indexeer functie op aanvraag uitvoeren.   

Bekijk [Indexeer functie maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer)voor meer informatie over het maken van Indexeer functie-API.

Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van de planningen voor de Indexeer functie.

## <a name="see-also"></a>Zie ook

Meer informatie over Cosmos DB Indexeer functies:
* [Indexeerfunctie voor Azure Cosmos DB](search-howto-index-cosmosdb.md)
