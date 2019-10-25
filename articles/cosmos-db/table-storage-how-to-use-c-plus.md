---
title: Azure Table Storage en Azure Cosmos DB Table-API gebruiken met C++
description: Sla gestructureerde gegevens op in de cloud met Azure-tabelopslag of de Azure Cosmos DB Table-API.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 48222bf3f964f8c728f980f839c460862a8212ca
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818647"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Azure-tabelopslag en Azure Cosmos DB Table-API gebruiken met C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

In deze hand leiding worden veelgebruikte scenario's weer gegeven met behulp van de Azure Table Storage-service of de Azure Cosmos DB Table-API. De voorbeelden zijn geschreven in C++ en maken gebruik van de [Azure Storage-clientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). In dit artikel komen de volgende scenario's aan bod:

* Een tabel maken en verwijderen
* Werken met tabel entiteiten

> [!NOTE]
> Deze handleiding is gericht op de Azure-opslagclientbibliotheek voor C++ versie 1.0.0 en hoger. De aanbevolen versie is de opslag-client bibliotheek 2.2.0, die beschikbaar is via [NuGet](https://www.nuget.org/packages/wastorage) of [github](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Accounts maken

### <a name="create-an-azure-service-account"></a>Een Azure-serviceaccount maken

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Een Azure Cosmos DB Table-API-account maken

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Een C++-toepassing maken

In deze hand leiding gebruikt u opslag functies van een C++ toepassing. Als u dit wilt doen, moet u de Azure Storage C++-client bibliotheek voor installeren.

Als u de Azure Storage-client bibliotheek C++voor wilt installeren, gebruikt u de volgende methoden:

* Linux. Volg de instructies in [Azure Storage-client bibliotheek voor C++ ](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
* Windows. Selecteer in Visual Studio **Tools > NuGet package manager > Package Manager console**. Voer de volgende opdracht uit in de **console pakket beheer**:

  ```powershell
  Install-Package wastorage
  ```

Zie [pakketten installeren en beheren met de Package Manager-console in Visual Studio](/nuget/tools/package-manager-console)voor meer informatie over de **Package Management-Console**.

### <a name="configure-access-to-the-table-client-library"></a>Toegang tot de tabelclientbibliotheek configureren

Als u de Azure Storage-Api's wilt gebruiken om toegang te krijgen tot tabellen, voegt u de volgende C++ `include`-instructies toe aan het begin van het bestand:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Een Azure-opslagclient of Cosmos DB-client gebruikt een opslagverbindingstekenreeks voor het opslaan van eindpunten en referenties voor toegang tot gegevensbeheerservices. Wanneer u een client toepassing uitvoert, moet u de opslag connection string of de Azure Cosmos DB connection string in de juiste indeling opgeven.

### <a name="set-up-an-azure-storage-connection-string"></a>Een Azure-opslagverbindingstekenreeks instellen

In dit voor beeld ziet u hoe u een statisch veld declareert om de Azure Storage connection string op te slaan:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Gebruik de naam van uw opslag account voor `<your_storage_account>`. Gebruik voor < your_storage_account_key > de toegangs sleutel voor het opslag account dat wordt vermeld in de [Azure Portal](https://portal.azure.com). Zie [een opslag account maken](../storage/common/storage-create-storage-account.md)voor meer informatie over opslag accounts en toegangs sleutels.

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Een Azure Cosmos DB-verbindingstekenreeks instellen

In dit voor beeld ziet u hoe u een statisch veld declareert om de Azure Cosmos DB connection string op te slaan:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Gebruik de naam van uw Azure Cosmos DB-account voor `<your_cosmos_db_account>`. Voer uw primaire sleutel in voor de `<your_cosmos_db_account_key>`. Voer het eind punt in dat wordt weer gegeven in de [Azure Portal](https://portal.azure.com) voor `<your_cosmos_db_endpoint>`.

Als u uw toepassing wilt testen op uw lokale Windows-computer, kunt u de Azure-opslag emulator gebruiken die is geïnstalleerd met de [Azure SDK](https://azure.microsoft.com/downloads/). De opslagemulator is een hulpprogramma dat de Azure-blob-, wachtrij-en tabelservices simuleert die beschikbaar zijn op uw lokale ontwikkelingsmachine. In het volgende voor beeld ziet u hoe u een statisch veld declareert om de connection string op te slaan op uw lokale opslag emulator:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Als u de Azure-opslag emulator wilt starten, selecteert u de knop **Start** of de Windows-toets op het Windows-bureau blad. Voer *Microsoft Azure-opslagemulator*in en voer deze uit. Zie [De Azure-opslagemulator gebruiken voor ontwikkeling en testen](../storage/common/storage-use-emulator.md) voor meer informatie.

### <a name="retrieve-your-connection-string"></a>De verbindingsreeks ophalen

U kunt de klasse `cloud_storage_account` gebruiken om de gegevens van uw opslag account te vertegenwoordigen. Als u de gegevens van uw opslag account wilt ophalen uit de opslag connection string, gebruikt u de methode `parse`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Vervolgens haalt u een verwijzing op naar een `cloud_table_client`-klasse. Met deze klasse kunt u referentie objecten voor tabellen en entiteiten ophalen die zijn opgeslagen in de Table Storage-service. Met de volgende code wordt een `cloud_table_client`-object gemaakt met behulp van het object van het opslag account dat u eerder hebt opgehaald:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Entiteiten maken en toevoegen aan een tabel

### <a name="create-a-table"></a>Een tabel maken

Met een `cloud_table_client`-object kunt u referentie objecten voor tabellen en entiteiten ophalen. Met de volgende code wordt een `cloud_table_client`-object gemaakt en gebruikt om een nieuwe tabel te maken.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

Als u een entiteit wilt toevoegen aan een tabel, maakt u een nieuw `table_entity`-object en geeft u het door aan `table_operation::insert_entity`. Met de volgende code gebruikt u de voornaam van de klant als de rijsleutel en de achternaam als de partitiesleutel. De partitie- en rijsleutel van een entiteit vormen samen de unieke id van de entiteit in de tabel. Entiteiten met dezelfde partitie sleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitie sleutels. Het gebruik van verschillende partitie sleutels maakt een grotere schaal baarheid van parallelle bewerkingen mogelijk. Raadpleeg de [Controlelijst voor prestaties en schaalbaarheid van Microsoft Azure Storage](../storage/common/storage-performance-checklist.md) voor meer informatie.

Met de volgende code wordt een nieuw exemplaar van `table_entity` gemaakt met klant gegevens die moeten worden opgeslagen. De code Next roept `table_operation::insert_entity` om een `table_operation`-object te maken om een entiteit in een tabel in te voegen en koppelt de nieuwe tabel entiteit hieraan. Ten slotte roept de code de `execute` methode aan voor het `cloud_table`-object. De nieuwe `table_operation` verzendt een aanvraag naar de Table service om de nieuwe klant entiteit in de tabel `people` in te voegen.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Een batch entiteiten invoegen

U kunt in één schrijfbewerking een batch entiteiten invoegen in de tabelservice. Met de volgende code wordt een `table_batch_operation`-object gemaakt en worden vervolgens drie insert-bewerkingen aan toegevoegd. Elke invoeg bewerking wordt toegevoegd door een nieuw entiteits object te maken, de waarden in te stellen en vervolgens de `insert`-methode aan te roepen op het `table_batch_operation`-object om de entiteit te koppelen aan een nieuwe invoeg bewerking. Vervolgens roept de code `cloud_table.execute` om de bewerking uit te voeren.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Een aantal zaken die u moet weten over batchbewerkingen:

* U kunt Maxi maal 100 `insert`, `delete`, `merge`, `replace`, `insert-or-merge`en `insert-or-replace` bewerkingen uitvoeren in een combi natie van een enkele batch.  
* Een batch-bewerking kan een ophaal bewerking hebben, als dit de enige bewerking in de batch is.  
* Alle entiteiten in een batchbewerking moeten dezelfde partitiesleutel hebben.  
* Een batchbewerking is beperkt tot een gegevensnettolading van 4 MB.  

## <a name="query-and-modify-entities"></a>Entiteiten opvragen en wijzigen

### <a name="retrieve-all-entities-in-a-partition"></a>Alle entiteiten in een partitie ophalen

Gebruik een `table_query`-object om een tabel voor alle entiteiten in een partitie op te vragen. In het volgende code voorbeeld geeft u een filter op voor entiteiten waarvan `Smith` de partitie sleutel is. In dit voorbeeld worden de velden van elke entiteit in de queryresultaten naar de console afgedrukt.  

> [!NOTE]
> Deze methoden worden momenteel niet ondersteund voor C++ in Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

De query in dit voor beeld retourneert alle entiteiten die voldoen aan de filter criteria. Als u grote tabellen hebt en de tabelentiteiten vaak moet downloaden, raden we u aan uw gegevens in plaats daarvan op te slaan in Azure-opslagblobs.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Een bereik van entiteiten in een partitie ophalen

Als u niet alle entiteiten in een partitie wilt opvragen, kunt u een bereik opgeven. Combi neer het partitie sleutel filter met een filter voor rij-sleutels. Het volgende code voorbeeld maakt gebruik van twee filters om alle entiteiten in de partitie te verkrijgen `Smith` waarbij de rij (voor naam) begint met een letter die eerder is dan `E` in het alfabet en de query resultaten vervolgens afdrukt.  

> [!NOTE]
> Deze methoden worden momenteel niet ondersteund voor C++ in Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Eén entiteit ophalen

U kunt een query schrijven om één specifieke entiteit op te halen. De volgende code gebruikt `table_operation::retrieve_entity` om de klant `Jeff Smith`op te geven. Deze methode retourneert slechts één entiteit in plaats van een verzameling, en de geretourneerde waarde bevindt zich in `table_result`. Het opgeven van zowel partitie- als rijsleutels in een query is de snelste manier om één entiteit op te halen uit de Tabelservice.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Een entiteit vervangen

Als u een entiteit wilt vervangen, haalt u deze op uit de Tabelservice, wijzigt u het entiteitsobject en slaat u de wijzigingen weer op in de Tabelservice. Met de volgende code wijzigt u het telefoonnummer en het e-mailadres van een bestaande klant. In plaats van `table_operation::insert_entity`aan te roepen, gebruikt deze code `table_operation::replace_entity`. Deze aanpak zorgt ervoor dat de entiteit volledig wordt vervangen op de server, tenzij de entiteit op de server is gewijzigd nadat deze is opgehaald. Als deze is gewijzigd, mislukt de bewerking. Met deze fout wordt voor komen dat uw toepassing een wijziging overschrijft tussen het ophalen en bijwerken van een ander onderdeel. De juiste afhandeling van deze fout is om de entiteit opnieuw op te halen, de wijzigingen aan te brengen, als deze nog geldig zijn en vervolgens een andere `table_operation::replace_entity` bewerking uit te voeren.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Een entiteit invoegen of vervangen

`table_operation::replace_entity`-bewerkingen mislukken als de entiteit is gewijzigd nadat deze is opgehaald van de server. U moet de entiteit bovendien eerst ophalen van de server om de `table_operation::replace_entity` te kunnen volt ooien. Soms weet u niet of de entiteit bestaat op de server. De huidige opgeslagen waarden zijn niet relevant, omdat uw update deze allemaal moet overschrijven. Gebruik een `table_operation::insert_or_replace_entity` bewerking om dit resultaat te bereiken. Met deze bewerking wordt de entiteit ingevoegd als deze niet bestaat. Met deze bewerking wordt de entiteit vervangen als deze bestaat. In het volgende code voorbeeld wordt de klant entiteit voor `Jeff Smith` nog steeds opgehaald, maar vervolgens teruggezonden naar de server met behulp van `table_operation::insert_or_replace_entity`. Eventuele wijzigingen die in de entiteit zijn aangebracht tussen het moment van ophalen en het moment van bijwerken, worden overschreven.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Een query uitvoeren op een subset van entiteitseigenschappen

Met een query naar een tabel kunnen slechts enkele eigenschappen van een entiteit worden opgehaald. De query in de volgende code gebruikt de methode `table_query::set_select_columns` om alleen de e-mail adressen van entiteiten in de tabel te retour neren.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Het uitvoeren van een query voor een aantal eigenschappen van een entiteit is een efficiëntere bewerking dan het ophalen van alle eigenschappen.
>

## <a name="delete-content"></a>Inhoud verwijderen

### <a name="delete-an-entity"></a>Een entiteit verwijderen

U kunt een entiteit verwijderen nadat u deze hebt opgehaald. Nadat u een entiteit hebt opgehaald, roept u `table_operation::delete_entity` aan met de entiteit die u wilt verwijderen. Roep vervolgens de `cloud_table.execute` methode aan. Met de volgende code wordt een entiteit opgehaald en verwijderd met de partitie sleutel van `Smith` en een rij met `Jeff`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Een tabel verwijderen

Ten slotte wordt met het volgende codevoorbeeld een tabel uit een opslagaccount verwijderd. Een tabel die is verwijderd, is niet beschikbaar om opnieuw te worden gemaakt voor een bepaalde tijd na het verwijderen.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Problemen oplossen

Als uw project in Visual Studio Community Edition opgebouwde fouten oplevert vanwege de include files *storage_account. h* en *Table. h*, verwijdert u de **/Permissive-** -compiler switch:

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Properties**.
1. Vouw in het dialoogvenster **Eigenschappenpagina’s** de optie **Configuratie-eigenschappen** en vervolgens **C/C++** uit en selecteer **Taal**.
1. Stel **Overeenstemmingsmodus** in op **Nee**.

## <a name="next-steps"></a>Volgende stappen

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.

Volg deze links koppelingen meer informatie over Azure Storage en de tabel-API in Azure Cosmos DB:

* [Introductie tot de tabel-API](table-introduction.md)
* [Azure Storage-resources in C++ weergeven](../storage/common/storage-c-plus-plus-enumeration.md)
* [Naslaginformatie over de Storage-clientbibliotheek voor C++](https://azure.github.io/azure-storage-cpp)
* [Documentatie bij Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
