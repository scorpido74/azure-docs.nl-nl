---
title: Azure Table Storage en Azure Cosmos DB Table API gebruiken met C++
description: Sla gestructureerde gegevens op in de cloud met Azure Table Storage of de Azure Cosmos DB Table-API.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: e6d61e329ba91f53b11ace4d258b35950e188dcb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76771220"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Azure-tabelopslag en Azure Cosmos DB Table-API gebruiken met C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

In deze handleiding ziet u veelvoorkomende scenario's met behulp van de Azure Table-opslagservice of Azure Cosmos DB Table API. De voorbeelden zijn geschreven in C++ en maken gebruik van de [Azure Storage-clientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). In dit artikel worden de volgende scenario's bezien:

* Een tabel maken en verwijderen
* Werken met tabelentiteiten

> [!NOTE]
> Deze handleiding is gericht op de Azure-opslagclientbibliotheek voor C++ versie 1.0.0 en hoger. De aanbevolen versie is Storage Client Library 2.2.0, die beschikbaar is met [NuGet](https://www.nuget.org/packages/wastorage) of [GitHub.](https://github.com/Azure/azure-storage-cpp/)
>

## <a name="create-accounts"></a>Accounts maken

### <a name="create-an-azure-service-account"></a>Een Azure-serviceaccount maken

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Een Azure Cosmos DB Table-API-account maken

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Een C++-toepassing maken

In deze handleiding gebruikt u opslagfuncties van een C++ toepassing. Installeer hiervoor de Azure Storage Client Library voor C++.

Als u de Azure Storage Client Library voor C++ wilt installeren, gebruikt u de volgende methoden:

* **Linux:** Volg de instructies in de [Azure Storage Client Library voor C++ README: Aan de slag op linux-pagina.](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)
* **Windows:** Gebruik [vcpkg](https://github.com/microsoft/vcpkg) in Windows als afhankelijkheidsmanager. Volg de [snelle start](https://github.com/microsoft/vcpkg#quick-start) om vcpkg te initialiseren. Gebruik vervolgens de volgende opdracht om de bibliotheek te installeren:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

In het [README-bestand](https://github.com/Azure/azure-storage-cpp#download--install) vindt u een handleiding voor het bouwen van de broncode en exporteren naar Nuget.

### <a name="configure-access-to-the-table-client-library"></a>Toegang tot de tabelclientbibliotheek configureren

Als u de Azure-opslag-API's `include` wilt gebruiken om toegang te krijgen tot tabellen, voegt u de volgende instructies toe aan de bovenkant van het C++-bestand:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Een Azure-opslagclient of Cosmos DB-client gebruikt een opslagverbindingstekenreeks voor het opslaan van eindpunten en referenties voor toegang tot gegevensbeheerservices. Wanneer u een clienttoepassing uitvoert, moet u de tekenreeks voor de opslagverbinding of de Azure Cosmos DB-verbindingstekenreeks in de juiste indeling opgeven.

### <a name="set-up-an-azure-storage-connection-string"></a>Een Azure-opslagverbindingstekenreeks instellen

In dit voorbeeld ziet u hoe u een statisch veld declareert om de tekenreeks azure storage-verbinding vast te houden:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Gebruik de naam van `<your_storage_account>`uw opslagaccount voor . Gebruik voor <your_storage_account_key> de toegangssleutel voor het opslagaccount dat wordt vermeld in de [Azure-portal.](https://portal.azure.com) Zie [Een opslagaccount maken](../storage/common/storage-create-storage-account.md)voor informatie over opslagaccounts en toegangssleutels.

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Een Azure Cosmos DB-verbindingstekenreeks instellen

In dit voorbeeld ziet u hoe u een statisch veld declareert om de Azure Cosmos DB-verbindingstekenreeks vast te houden:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Gebruik de naam van uw `<your_cosmos_db_account>`Azure Cosmos DB-account voor . Voer uw primaire `<your_cosmos_db_account_key>`sleutel in voor . Voer het eindpunt in dat wordt `<your_cosmos_db_endpoint>`vermeld in de [Azure-portal](https://portal.azure.com) voor .

Als u uw toepassing in uw lokale Windows-computer wilt testen, kunt u de Azure-opslagemulator gebruiken die is geïnstalleerd met de [Azure SDK](https://azure.microsoft.com/downloads/). De opslagemulator is een hulpprogramma dat de Azure-blob-, wachtrij-en tabelservices simuleert die beschikbaar zijn op uw lokale ontwikkelingsmachine. In het volgende voorbeeld ziet u hoe u een statisch veld declareert om de verbindingstekenreeks vast te houden aan uw lokale opslagemulator:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Als u de Azure-opslagemulator wilt starten, selecteert u vanaf uw Windows-bureaublad de knop **Start** of de Windows-toets. Microsoft *Azure Storage Emulator*invoeren en uitvoeren . Zie [De Azure-opslagemulator gebruiken voor ontwikkeling en testen voor](../storage/common/storage-use-emulator.md)meer informatie.

### <a name="retrieve-your-connection-string"></a>De verbindingsreeks ophalen

U `cloud_storage_account` de klasse gebruiken om uw opslagaccountgegevens weer te geven. Als u uw opslagaccountgegevens wilt ophalen `parse` uit de tekenreeks voor opslagverbindingen, gebruikt u de methode.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Vervolgens krijgt u een `cloud_table_client` verwijzing naar een klas. Met deze klasse u referentieobjecten opvragen voor tabellen en entiteiten die zijn opgeslagen in de opslagservice Tabel. Met de volgende `cloud_table_client` code wordt een object gemaakt met het opslagaccountobject dat u eerder hebt opgehaald:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Entiteiten maken en toevoegen aan een tabel

### <a name="create-a-table"></a>Een tabel maken

Met `cloud_table_client` een object u referentieobjecten voor tabellen en entiteiten krijgen. Met de volgende `cloud_table_client` code wordt een object gemaakt en gebruikt u deze om een nieuwe tabel te maken.

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

Als u een entiteit aan een `table_entity` tabel wilt `table_operation::insert_entity`toevoegen, maakt u een nieuw object en geeft u het door aan . Met de volgende code gebruikt u de voornaam van de klant als de rijsleutel en de achternaam als de partitiesleutel. De partitie- en rijsleutel van een entiteit vormen samen de unieke id van de entiteit in de tabel. Entiteiten met dezelfde partitiesleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitiesleutels. Het gebruik van diverse partitiesleutels zorgt voor een grotere schaalbaarheid van parallelle bewerkingen. Raadpleeg de [Controlelijst voor prestaties en schaalbaarheid van Microsoft Azure Storage](../storage/common/storage-performance-checklist.md) voor meer informatie.

Met de volgende code `table_entity` wordt een nieuw exemplaar gemaakt van bepaalde klantgegevens die u wilt opslaan. De code `table_operation::insert_entity` volgende aanroepen om een `table_operation` object te maken om een entiteit in een tabel in te voegen en koppelt de nieuwe tabelentiteit eraan. Ten slotte roept `execute` de code `cloud_table` de methode op het object aan. De `table_operation` nieuwe stuurt een verzoek naar de tabelservice `people` om de nieuwe klantentiteit in de tabel in te voegen.  

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

U kunt in één schrijfbewerking een batch entiteiten invoegen in de tabelservice. Met de volgende `table_batch_operation` code wordt een object gemaakt en worden er vervolgens drie invoegbewerkingen aan toegevoegd. Elke invoegbewerking wordt toegevoegd door een nieuw entiteitsobject `insert` te maken, de waarden in te stellen en vervolgens de methode op het `table_batch_operation` object aan te roepen om de entiteit te koppelen aan een nieuwe invoegbewerking. Vervolgens roept de `cloud_table.execute` code om de bewerking uit te voeren.  

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

* U maximaal `insert`100 `delete`, `replace` `insert-or-merge`, `insert-or-replace` , `merge`, en bewerkingen in een combinatie in een enkele batch.  
* Een batchbewerking kan een bewerking voor ophalen hebben, als dit de enige bewerking in de batch is.  
* Alle entiteiten in een batchbewerking moeten dezelfde partitiesleutel hebben.  
* Een batchbewerking is beperkt tot een gegevensnettolading van 4 MB.  

## <a name="query-and-modify-entities"></a>Entiteiten aanvragen en wijzigen

### <a name="retrieve-all-entities-in-a-partition"></a>Alle entiteiten in een partitie ophalen

Als u een tabel wilt opvragen voor `table_query` alle entiteiten in een partitie, gebruikt u een object. In het volgende codevoorbeeld wordt `Smith` een filter opgegeven voor entiteiten waar de partitiesleutel is. In dit voorbeeld worden de velden van elke entiteit in de queryresultaten naar de console afgedrukt.  

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

De query in dit voorbeeld retourneert alle entiteiten die overeenkomen met de filtercriteria. Als u grote tabellen hebt en de tabelentiteiten vaak moet downloaden, raden we u aan uw gegevens in plaats daarvan op te slaan in Azure-opslagblobs.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Een bereik van entiteiten in een partitie ophalen

Als u niet alle entiteiten in een partitie wilt opvragen, u een bereik opgeven. Combineer het filter van de partitiesleutel met een rijsleutelfilter. In het volgende codevoorbeeld worden twee filters `Smith` gebruikt om alle entiteiten in partitie te `E` krijgen waar de rijsleutel (voornaam) begint met een letter eerder dan in het alfabet en worden de queryresultaten afgedrukt.  

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

U kunt een query schrijven om één specifieke entiteit op te halen. De volgende `table_operation::retrieve_entity` code wordt `Jeff Smith`gebruikt om de klant op te geven . Deze methode retourneert slechts één entiteit in plaats `table_result`van een verzameling en de geretourneerde waarde bevindt zich in . Het opgeven van zowel partitie- als rijsleutels in een query is de snelste manier om één entiteit op te halen uit de Tabelservice.  

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

Als u een entiteit wilt vervangen, haalt u deze op uit de Tabelservice, wijzigt u het entiteitsobject en slaat u de wijzigingen weer op in de Tabelservice. Met de volgende code wijzigt u het telefoonnummer en het e-mailadres van een bestaande klant. In plaats `table_operation::insert_entity`van te `table_operation::replace_entity`bellen, gebruikt deze code . Deze benadering zorgt ervoor dat de entiteit volledig wordt vervangen op de server, tenzij de entiteit op de server is gewijzigd sinds deze is opgehaald. Als deze is gewijzigd, mislukt de bewerking. Deze fout voorkomt dat uw toepassing een wijziging overschrijft die is aangebracht tussen het ophalen en bijwerken door een ander onderdeel. De juiste afhandeling van deze fout is om de entiteit opnieuw op te `table_operation::replace_entity` halen, uw wijzigingen aan te brengen, indien nog geldig, en vervolgens een andere bewerking uit te voeren.  

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

`table_operation::replace_entity`bewerkingen mislukken als de entiteit is gewijzigd sinds deze van de server is opgehaald. Bovendien moet u de entiteit eerst van de `table_operation::replace_entity` server ophalen om succesvol te zijn. Soms weet u niet of de entiteit op de server bestaat. De huidige waarden die erin zijn opgeslagen, zijn niet relevant, omdat uw update ze allemaal moet overschrijven. Gebruik een `table_operation::insert_or_replace_entity` bewerking om dit resultaat te bereiken. Met deze bewerking wordt de entiteit ingevoegd als deze niet bestaat. De bewerking vervangt de entiteit als deze bestaat. In het volgende codevoorbeeld wordt `Jeff Smith` de klantentiteit nog steeds opgehaald, maar deze `table_operation::insert_or_replace_entity`wordt vervolgens opgeslagen op de server met behulp van . Eventuele wijzigingen die in de entiteit zijn aangebracht tussen het moment van ophalen en het moment van bijwerken, worden overschreven.  

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

Met een query naar een tabel kunnen slechts enkele eigenschappen van een entiteit worden opgehaald. De query in de `table_query::set_select_columns` volgende code gebruikt de methode om alleen de e-mailadressen van entiteiten in de tabel terug te sturen.  

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

U een entiteit verwijderen nadat u deze hebt opgehaald. Nadat u een entiteit `table_operation::delete_entity` hebt opgehaald, belt u met de entiteit om te verwijderen. Roep dan `cloud_table.execute` de methode. Met de volgende code wordt een entiteit met `Smith` een partitiesleutel `Jeff`van en een rijsleutel van .

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

Ten slotte wordt met het volgende codevoorbeeld een tabel uit een opslagaccount verwijderd. Een tabel die is verwijderd, kan na de verwijdering enige tijd niet opnieuw worden gemaakt.  

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

Als uw project voor Visual Studio Community Edition buildfouten krijgt vanwege de bestanden *storage_account.h* en *table.h,* verwijdert u de **/tolerante compilerswitch:**

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Properties**.
1. Vouw in het dialoogvenster **Eigenschappenpagina’s** de optie **Configuratie-eigenschappen** en vervolgens **C/C++** uit en selecteer **Taal**.
1. Stel **Overeenstemmingsmodus** in op **Nee**.

## <a name="next-steps"></a>Volgende stappen

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.

Volg deze links koppelingen meer informatie over Azure Storage en de tabel-API in Azure Cosmos DB:

* [Introductie tot de tabel-API](table-introduction.md)
* [Azure Storage-resources in C++ weergeven](../storage/common/storage-c-plus-plus-enumeration.md)
* [Naslaginformatie over de Storage-clientbibliotheek voor C++](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-documentatie](https://azure.microsoft.com/documentation/services/storage/)
