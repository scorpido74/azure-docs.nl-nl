---
title: Een go-toepassing verbinden met de API van Azure Cosmos DB voor MongoDB
description: In deze Quick start ziet u hoe u een bestaande Go-toepassing kunt verbinden met de API van Azure Cosmos DB voor MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 2cad73f85ce81c650e1f1ff702a099b2d6df8e16
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984878"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Quick Start: een go-toepassing verbinden met de API van Azure Cosmos DB voor MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert. In deze Quick Start kunt u een Azure Cosmos DB-account maken en beheren met behulp van de Azure Cloud Shell, een bestaande voorbeeld toepassing klonen van GitHub en configureren voor gebruik met Azure Cosmos DB. 

De voorbeeld toepassing is een beheer programma op basis `todo` van opdracht regels dat is geschreven in go. De API van Azure Cosmos DB voor MongoDB is [compatibel met het MongoDb wire-protocol](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction#wire-protocol-compatibility), waardoor elk MongoDb-client stuur programma er verbinding mee kan maken. Deze toepassing maakt gebruik van het [Go-stuur programma voor MongoDb](https://github.com/mongodb/mongo-go-driver) op een manier die transparant is voor de toepassing die de gegevens in een Azure Cosmos DB-Data Base zijn opgeslagen.

## <a name="prerequisites"></a>Vereisten
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free). Of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement. U kunt ook de [Azure Cosmos DB emulator](https://aka.ms/cosmosdb-emulator) gebruiken met de Connection String `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- [Ga naar](https://golang.org/) de computer en een werk ervaring van go.
- [Git](https://git-scm.com/downloads).
- Als u Azure Cloud Shell niet wilt gebruiken, gaat u naar [Azure CLI 2.0 +](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen.

1. Open een opdracht prompt, maak een nieuwe map met `git-samples`de naam en sluit de opdracht prompt.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de toepassing werkt, kunt u de volgende code fragmenten bekijken. Als dat niet het geval is, kunt u door gaan om [de toepassing uit te voeren](#run-the-application). De indeling van de toepassing is als volgt:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

De volgende codefragmenten zijn allemaal afkomstig uit het bestand `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>De Go-app verbinden met Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions)Hiermee wordt de connection string voor Azure Cosmos DB, die wordt door gegeven in een omgevings variabele (Details in de komende sectie) ingekapseld. De verbinding wordt geïnitialiseerd op basis [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) waarvan het `clientOptions` exemplaar is door gegeven. de functie wordt aangeroepen om de geslaagde connectiviteit te bevestigen (het is een Fail-Fast-strategie) [ `Ping` ](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> Het gebruik [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) van de configuratie is belang rijk, zonder dat u de volgende verbindings fout krijgt:`unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Een `todo` item maken

Om een `todo`te maken, krijgen we een ingang naar [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) een en wordt [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) de functie aangeroepen. 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

We geven een `Todo` struct die de beschrijving en de status bevat (die in eerste instantie is ingesteld `pending`op)

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>Items `todo` weer geven

We kunnen taken weer geven op basis van criteria. Er [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) wordt een gemaakt om de filter criteria te inkapselen

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find)wordt gebruikt om te zoeken naar documenten op basis van het filter en het resultaat wordt geconverteerd naar een segment van`Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Ten slotte worden de gegevens weer gegeven in tabel vorm

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Een `todo` item bijwerken

Een `todo` kan worden bijgewerkt op basis van `_id`de. Er [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) wordt een filter gemaakt op basis `_id` van de en er wordt voor de bijgewerkte informatie een nieuwe status (`completed` of `pending`) in dit geval gemaakt. Ten slotte wordt [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) de functie aangeroepen met het filter en het bijgewerkte document

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Een verwijderen`todo`

A `todo` wordt verwijderd op basis van `_id` de en wordt ingekapseld in de vorm van een [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) exemplaar. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne)wordt aangeroepen om het document te verwijderen.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>De toepassing bouwen

Ga naar de map waarin u de toepassing hebt gekloond en bouw deze (met `go build`).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Om te controleren of de toepassing correct is gemaakt.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Azure Cosmos DB instellen

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren] als u een installatie of upgrade wilt uitvoeren. 

Als u een geïnstalleerde Azure CLI gebruikt, meldt u zich aan bij uw Azure-abonnement met de opdracht [AZ login](/cli/azure/reference-index#az-login) en volgt u de instructies op het scherm. U kunt deze stap overslaan als u de Azure Cloud Shell gebruikt.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>De Azure Cosmos DB-module toevoegen

Als u van een geïnstalleerde Azure CLI gebruikmaakt, controleert u of het onderdeel `cosmosdb` al is geïnstalleerd door de opdracht `az` uit te voeren. Als `cosmosdb` in de lijst met basisopdrachten staat, gaat u verder met de volgende opdracht. U kunt deze stap overslaan als u de Azure Cloud Shell gebruikt.

Als `cosmosdb` niet in de lijst met basisopdrachten staat, installeert u [Azure CLI](/cli/azure/install-azure-cli) opnieuw.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [resource groep](../azure-resource-manager/management/overview.md) met de [AZ-groep Create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources, zoals web-apps, databases en opslagaccounts, worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio Europa - west. Kies een unieke naam voor de resourcegroep.

Als u Azure Cloud Shell gebruikt, selecteert u **try**-out, volgt u de aanwijzingen op het scherm om u aan te melden en kopieert u de opdracht naar de opdracht prompt.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Maak een Cosmos-account met de opdracht [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

Vervang in de volgende opdracht waar u de plaatsaanduiding `<cosmosdb-name>` ziet staan, de accountnaam met uw unieke Cosmos-accountnaam. Deze unieke naam wordt gebruikt als onderdeel van uw Cosmos DB-eindpunt (`https://<cosmosdb-name>.documents.azure.com/`). De naam moet daarom uniek zijn binnen alle Cosmos-accounts in Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

De parameter `--kind MongoDB` maakt MongoDB-clientverbindingen mogelijk.

Wanneer de Azure Cosmos DB-account wordt gemaakt toont de Azure CLI informatie die lijkt op het volgende voorbeeld. 

> [!NOTE]
> In dit voorbeeld wordt JSON gebruikt als de Azure CLI-uitvoerindeling. Dit is standaardindeling. Zie [Output formats for Azure CLI commands](https://docs.microsoft.com/cli/azure/format-output-azure-cli) (Uitvoerindelingen voor Azure CLI-opdrachten) als u een andere uitvoerindeling wilt gebruiken.

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>De databasesleutel ophalen

U hebt de databasesleutel nodig om verbinding te kunnen maken met een Cosmos-database. Gebruik de opdracht [AZ cosmosdb Keys List](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) om de primaire sleutel op te halen.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

De Azure CLI voert informatie uit die lijkt op het volgende voorbeeld. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>De toepassing configureren 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exporteer de connection string, MongoDB-data base en verzamelings namen als omgevings variabelen. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> De `ssl=true` optie is belang rijk vanwege Cosmos DB vereisten. Zie vereisten voor de [verbindings reeks](connect-mongodb-account.md#connection-string-requirements)voor meer informatie.
>

Vervang voor `MONGODB_CONNECTION_STRING` de omgevings variabele de tijdelijke aanduidingen `<COSMOSDB_ACCOUNT_NAME>` voor en`<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: De naam van het Azure Cosmos DB-account dat u hebt gemaakt
2. `<COSMOSDB_PASSWORD>`: De database sleutel die in de vorige stap is geëxtraheerd

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

U kunt uw voorkeurs waarden kiezen voor `MONGODB_DATABASE` en `MONGODB_COLLECTION` of ze laten staan.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voor het maken van een`todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Als dit lukt, ziet u een uitvoer met de MongoDB `_id` van het zojuist gemaakte document:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Nieuwe maken`todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Alle `todo`s weer geven

```bash
./todo --list all
```

Als dat het goed is, ziet u de items die u zojuist hebt toegevoegd in een tabel indeling

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Als u de status van een `todo` (bijvoorbeeld wijzigen in `completed` status) wilt bijwerken, gebruikt u `todo` de id

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Alleen de voltooide `todo`s weer geven

```bash
./todo --list completed
```

U ziet nu de versie die u zojuist hebt bijgewerkt

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Gegevens bekijken in Data Explorer

Gegevens die zijn opgeslagen in Azure Cosmos DB, zijn beschikbaar voor weer gave en query's in de Azure Portal.

Meld u aan bij de [Azure Portal](https://portal.azure.com) in uw webbrowser om de gebruikersgegevens die u in de vorige stap hebt gemaakt, te bekijken, query’s erop uit te voeren of andere taken ermee uit te voeren.

Voer **Azure Cosmos DB**in het bovenste zoekvak in. Wanneer uw Cosmos-accountblade wordt geopend, selecteert u uw Cosmos-account. Selecteer in de linkernavigatiebalk **Data Explorer**. Vouw uw verzameling uit in het venster Verzamelingen. Dan kunt u de documenten in de verzameling zien, query’s op de gegevens uitvoeren en zelfs opgeslagen procedures, triggers en UDF’s maken en uitvoeren. 

![Data Explorer waarin het zojuist gemaakte document wordt weergegeven](./media/create-mongodb-go/go-cosmos-db-data-explorer.jpg)


Een `todo` gebruiken-id verwijderen

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Te bevestigen `todo`s weer geven

```bash
./todo --list all
```

De `todo` zojuist verwijderde mag niet aanwezig zijn

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB MongoDB API-account maakt met behulp van de Azure Cloud Shell, en hoe u een go-opdracht `todo`regel toepassing kunt maken en uitvoeren voor het beheren van s. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](mongodb-migrate.md)
