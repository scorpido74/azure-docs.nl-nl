---
title: Meer informatie over het beveiligen van toegang tot gegevens in Azure Cosmos DB
description: Meer informatie over concepten voor toegangscontrole in Azure Cosmos DB, inclusief hoofdsleutels, alleen-lezensleutels, gebruikers en machtigingen.
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 448b14168e85e75b7ed19e189600186ce11c2902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251815"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Beveiligde toegang tot gegevens in Azure Cosmos DB

In dit artikel vindt u een overzicht van het beveiligen van toegang tot gegevens die zijn opgeslagen in [Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

Azure Cosmos DB gebruikt twee soorten sleutels om gebruikers te verifiëren en toegang te bieden tot de gegevens en bronnen. 

|Type sleutel|Resources|
|---|---|
|[Hoofdtoetsen](#master-keys) |Wordt gebruikt voor beheerdersbronnen: databaseaccounts, databases, gebruikers en machtigingen|
|[Brontokens](#resource-tokens)|Gebruikt voor toepassingsbronnen: containers, documenten, bijlagen, opgeslagen procedures, triggers en UDF's|

<a id="master-keys"></a>

## <a name="master-keys"></a>Hoofdtoetsen

Hoofdsleutels bieden toegang tot alle beheerbronnen voor het databaseaccount. Hoofdtoetsen:

- Geef toegang tot accounts, databases, gebruikers en machtigingen. 
- Kan niet worden gebruikt om gedetailleerde toegang tot containers en documenten te bieden.
- Worden gemaakt tijdens het aanmaken van een account.
- Kan op elk gewenst moment worden geregenereerd.

Elk account bestaat uit twee mastertoetsen: een primaire sleutel en secundaire sleutel. Het doel van dubbele sleutels is, zodat u regenereren, of roll sleutels, het verstrekken van continue toegang tot uw account en gegevens.

Naast de twee hoofdtoetsen voor het Cosmos DB-account zijn er twee alleen-lezen sleutels. Deze alleen-lezen sleutels staan alleen leesbewerkingen op het account toe. Alleen-lezensleutels bieden geen toegang tot bronnen met leesmachtigingen.

Hoofdteksten voor primaire, secundaire, alleen-lezen en leesschrijfmastersleutels kunnen worden opgehaald en geregenereerd met behulp van de Azure-portal. Zie [Toegangssleutels weergeven, kopiëren en regenereren](manage-with-cli.md#regenerate-account-key)voor instructies.

![Toegangsbeheer (IAM) in de Azure-portal - nosql-databasebeveiliging demonstreren](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Het proces van het roteren van uw master key is eenvoudig. Navigeer naar de Azure-portal om uw secundaire sleutel op te halen en vervang vervolgens uw primaire sleutel door uw secundaire sleutel in uw toepassing en draai de primaire sleutel in de Azure-portal.

![Hoofdsleutelrotatie in de Azure-portal - nosql-databasebeveiliging demonstreren](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Codevoorbeeld om een hoofdsleutel te gebruiken

In het volgende codevoorbeeld wordt uitgelegd hoe u een eindpunt en hoofdsleutel van een Cosmos DB-account gebruikt om een DocumentClient te instantiëren en een database te maken.

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

## <a name="resource-tokens"></a>Brontokens<a id="resource-tokens"></a>

Resourcetokens bieden toegang tot de toepassingsbronnen in een database. Resourcetokens:

- Geef toegang tot specifieke containers, partitiesleutels, documenten, bijlagen, opgeslagen procedures, triggers en UDF's.
- Worden gemaakt wanneer een [gebruiker](#users) [machtigingen](#permissions) voor een specifieke resource krijgt.
- Worden opnieuw gemaakt wanneer een machtigingsbron wordt uitgevoerd door POST, GET of PUT-aanroep.
- Gebruik een hash resource token dat speciaal is gemaakt voor de gebruiker, resource en toestemming.
- Zijn tijd gebonden met een aanpasbare geldigheidsperiode. De standaard geldige tijdspanne is één uur. De levensduur van het token kan echter expliciet worden opgegeven, tot een maximum van vijf uur.
- Bied een veilig alternatief voor het geven van de master key.
- Klanten in staat stellen bronnen in het Cosmos DB-account te lezen, schrijven en verwijderen op basis van de machtigingen die ze hebben gekregen.

U een resourcetoken gebruiken (door Cosmos DB-gebruikers en machtigingen te maken) wanneer u toegang wilt bieden tot bronnen in uw Cosmos DB-account aan een client die niet kan worden vertrouwd met de hoofdsleutel.  

Cosmos DB resource tokens bieden een veilig alternatief waarmee clients bronnen in uw Cosmos DB-account kunnen lezen, schrijven en verwijderen op basis van de machtigingen die u hebt verleend, en zonder dat een master- of lees-alleen-sleutel nodig is.

Hier is een typisch ontwerppatroon waarbij resourcetokens kunnen worden aangevraagd, gegenereerd en geleverd aan klanten:

1. Een mid-tier service is ingesteld om een mobiele applicatie te serveren om gebruikersfoto's te delen.
2. De mid-tier service bezit de hoofdsleutel van de Cosmos DB account.
3. De foto-app is geïnstalleerd op mobiele apparaten van eindgebruikers.
4. Bij het inloggen bepaalt de foto-app de identiteit van de gebruiker met de mid-tier service. Dit mechanisme van identiteitsvestiging is zuiver tot de toepassing.
5. Zodra de identiteit is vastgesteld, vraagt de mid-tier service machtigingen aan op basis van de identiteit.
6. De mid-tier service stuurt een resource token terug naar de telefoon app.
7. De telefoon-app kan het resourcetoken blijven gebruiken om rechtstreeks toegang te krijgen tot Cosmos DB-bronnen met de machtigingen die zijn gedefinieerd door het resourcetoken en voor het interval dat is toegestaan door het resourcetoken.
8. Wanneer het resourcetoken verloopt, ontvangen volgende aanvragen een ongeautoriseerde uitzondering van 401.  Op dit punt, de telefoon app herstelt de identiteit en vraagt een nieuwe resource token.

    ![Azure Cosmos DB-brontokenswerkstroom](./media/secure-access-to-data/resourcekeyworkflow.png)

Het genereren en beheren van resourcetokenwordt afgehandeld door de native Cosmos DB-clientbibliotheken; Als u echter REST gebruikt, moet u de voor-/verificatiekoppen construeren. Zie [Toegangscontrole op Cosmos DB Resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) of de broncode van onze [SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) of [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts)voor meer informatie over het maken van verificatiekoppen voor REST.

Zie de [ResourceTokenBroker-app](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)voor een voorbeeld van een service op het middenniveau die wordt gebruikt om brontokens te genereren of te brokeren.

## <a name="users"></a>Gebruikers<a id="users"></a>

Azure Cosmos DB-gebruikers zijn gekoppeld aan een Cosmos-database.  Elke database kan nul of meer Cosmos DB-gebruikers bevatten. In het volgende codevoorbeeld ziet u hoe u een Cosmos DB-gebruiker maakt met behulp van de [Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement).

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> Elke Cosmos DB-gebruiker heeft een methode ReadAsync() die kan worden gebruikt om de lijst met [machtigingen](#permissions) op te halen die aan de gebruiker zijn gekoppeld.

## <a name="permissions"></a>Machtigingen<a id="permissions"></a>

Een machtigingsbron is gekoppeld aan een gebruiker en toegewezen aan de container en op het niveau van de partitiesleutel. Elke gebruiker kan nul of meer machtigingen bevatten. Een machtigingsbron biedt toegang tot een beveiligingstoken dat de gebruiker nodig heeft wanneer hij toegang probeert te krijgen tot een specifieke container of gegevens in een specifieke partitiesleutel. Er zijn twee beschikbare toegangsniveaus die kunnen worden verstrekt door een machtigingsbron:

- Alle: De gebruiker heeft volledige toestemming voor de resource.
- Lees: De gebruiker kan alleen de inhoud van de bron lezen, maar kan geen schrijf-, update- of verwijderingsbewerkingen uitvoeren op de resource.

> [!NOTE]
> Om opgeslagen procedures uit te voeren moet de gebruiker de Alle toestemming hebben op de container waarin de opgeslagen procedure wordt uitgevoerd.

### <a name="code-sample-to-create-permission"></a>Voorbeeld van code om toestemming te maken

In het volgende codevoorbeeld ziet u hoe u een machtigingsbron maakt, het resourcetoken van de machtigingsbron leest en de machtigingen koppelt aan de [gebruiker](#users) die hierboven is gemaakt.

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>Voorbeeld van code om de toestemming voor de gebruiker te lezen

In het volgende codefragment ziet u hoe u de machtiging ophaalt die is gekoppeld aan de gebruiker die hierboven is gemaakt en hoe u namens de gebruiker een nieuwe CosmosClient instantiëren, afgestemd op één partitiesleutel.

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>Gebruikers toevoegen en rollen toewijzen

Als u Azure Cosmos DB-accountlezertoegang wilt toevoegen aan uw gebruikersaccount, voert u een abonnementseigenaar de volgende stappen uit in de Azure-portal.

1. Open de Azure-portal en selecteer uw Azure Cosmos DB-account.
2. Klik op het tabblad **Toegangsbesturingselement (IAM)** en klik vervolgens op **+ Roltoewijzing toevoegen**.
3. Selecteer in het deelvenster **Roltoewijzing toevoegen** in het vak **Rol** de optie **Cosmos DB-accountlezerrol**.
4. Selecteer **in**het vak Toegang toewijzen tot het vak **Azure AD-gebruiker, -groep of -toepassing**.
5. Selecteer de gebruiker, groep of toepassing in uw map waartoe u toegang wilt verlenen.  U de map doorzoeken op weergavenaam, e-mailadres of object-id's.
    De geselecteerde gebruiker, groep of toepassing wordt weergegeven in de lijst met geselecteerde leden.
6. Klik op **Opslaan**.

De entiteit kan nu Azure Cosmos DB-bronnen lezen.

## <a name="delete-or-export-user-data"></a>Gebruikersgegevens verwijderen of exporteren

Met Azure Cosmos DB u persoonlijke gegevens in database of verzamelingen zoeken, selecteren, wijzigen en verwijderen. Azure Cosmos DB biedt API's om persoonlijke gegevens te vinden en te verwijderen, maar het is uw verantwoordelijkheid om de API's te gebruiken en logica te definiëren die nodig is om de persoonlijke gegevens te wissen. Elke multi-model API (SQL, MongoDB, Gremlin, Cassandra, Table) biedt verschillende taal SDKs die methoden bevatten om persoonlijke gegevens te zoeken en te verwijderen. U ook de [functie Time to live (TTL)](time-to-live.md) inschakelen om gegevens automatisch na een bepaalde periode te verwijderen, zonder extra kosten te maken.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [Cosmos DB Database-beveiliging](database-security.md)voor meer informatie over de beveiliging van de Cosmos-database.
- Zie [Toegangsbeheer op Azure Cosmos DB-bronnen](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)voor meer informatie over het maken van Azure Cosmos DB-autorisatietokens.
- Voorbeelden van gebruikersbeheer met gebruikers en machtigingen, [.NET SDK v3-voorbeelden voor gebruikersbeheer](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)
