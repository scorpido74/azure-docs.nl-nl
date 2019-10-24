---
title: Meer informatie over het beveiligen van toegang tot gegevens in Azure Cosmos DB
description: Meer informatie over toegangs beheer concepten in Azure Cosmos DB, waaronder hoofd sleutels, alleen-lezen sleutels, gebruikers en machtigingen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 7e732f1d35097730d4468b43a2d9804fe7a18514
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753177"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Beveiligde toegang tot gegevens in Azure Cosmos DB

Dit artikel bevat een overzicht van het beveiligen van de toegang tot gegevens die zijn opgeslagen in [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB gebruikt twee typen sleutels om gebruikers te verifiëren en toegang te bieden tot de gegevens en bronnen van de gebruiker. 

|Type sleutel|Bronnen|
|---|---|
|[Hoofd sleutels](#master-keys) |Gebruikt voor administratieve resources: database accounts, data bases, gebruikers en machtigingen|
|[Bron tokens](#resource-tokens)|Gebruikt voor toepassings resources: containers, documenten, bijlagen, opgeslagen procedures, triggers en Udf's|

<a id="master-keys"></a>

## <a name="master-keys"></a>Hoofd sleutels 

Hoofd sleutels bieden toegang tot alle beheer resources voor het database account. Hoofd sleutels:  
- Toegang bieden tot accounts, data bases, gebruikers en machtigingen. 
- Kan niet worden gebruikt om nauw keurige toegang tot containers en documenten te bieden.
- Worden gemaakt tijdens het maken van een account.
- Kan op elk gewenst moment opnieuw worden gegenereerd.

Elk account bestaat uit twee hoofd sleutels: een primaire sleutel en secundaire sleutel. Het doel van dubbele sleutels is dat u de sleutel opnieuw kunt genereren of sleutels moet voorzien van een continue toegang tot uw account en gegevens. 

Naast de twee hoofd sleutels voor het Cosmos DB-account, zijn er twee alleen-lezen sleutels. Deze alleen-lezen sleutels staan alleen lees bewerkingen voor het account toe. Alleen-lezen sleutels bieden geen toegang tot het lezen van machtigings bronnen.

De hoofd sleutels primair, secundair, alleen-lezen en lezen/schrijven kunnen worden opgehaald en opnieuw worden gegenereerd met behulp van de Azure Portal. Zie [toegangs sleutels weer geven, kopiëren en opnieuw genereren](manage-with-cli.md#regenerate-account-key)voor instructies.

![Toegangs beheer (IAM) in de Azure Portal-demonstring NoSQL data base Security](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Het proces van het draaien van de hoofd sleutel is eenvoudig. Navigeer naar het Azure Portal om uw secundaire sleutel op te halen, vervang uw primaire sleutel door de secundaire sleutel in uw toepassing en draai vervolgens de primaire sleutel in de Azure Portal.

![Rotatie van hoofd sleutel in de Azure Portal-demonstring NoSQL data base Security](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Code voorbeeld voor gebruik van een hoofd sleutel

In het volgende code voorbeeld ziet u hoe u een Cosmos DB account-eind punt en hoofd sleutel kunt gebruiken om een DocumentClient te instantiëren en een Data Base te maken. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Bron tokens

Bron tokens bieden toegang tot de toepassings resources binnen een Data Base. Bron tokens:
- Toegang bieden tot specifieke containers, partitie sleutels, documenten, bijlagen, opgeslagen procedures, triggers en Udf's.
- Worden gemaakt wanneer aan een [gebruiker](#users) [machtigingen](#permissions) worden verleend voor een specifieke resource.
- Worden opnieuw gemaakt wanneer een machtigings resource wordt toegepast op de aanroep POST, GET of PUT.
- Gebruik een hash-bron token dat specifiek is gemaakt voor de gebruiker, de resource en de machtiging.
- Zijn tijd gebonden aan een aanpas bare geldigheids periode. De standaard geldige time span is een uur. De levens duur van het token kan echter expliciet worden opgegeven, Maxi maal vijf uur.
- Geef een veilig alternatief op voor het geven van de hoofd sleutel. 
- Hiermee kunnen clients resources in het Cosmos DB-account lezen, schrijven en verwijderen op basis van de machtigingen die ze hebben verleend.

U kunt een bron token gebruiken (door Cosmos DB gebruikers en machtigingen te maken) wanneer u toegang wilt verlenen tot resources in uw Cosmos DB account aan een client die niet kan worden vertrouwd met de hoofd sleutel.  

Cosmos DB bron tokens bieden een veilig alternatief waarmee clients resources in uw Cosmos DB account kunnen lezen, schrijven en verwijderen op basis van de machtigingen die u hebt verleend, en zonder dat u een hoofd-of alleen-lezen sleutel hoeft op te geven.

Hier volgt een typisch ontwerp patroon waarbij bron tokens kunnen worden aangevraagd, gegenereerd en geleverd aan clients:

1. Een mid-tier-service is ingesteld voor het gebruik van een mobiele toepassing om gebruikers Foto's te delen. 
2. De mid-tier-service beschikt over de hoofd sleutel van het Cosmos DB-account.
3. De foto-app is geïnstalleerd op mobiele apparaten van eind gebruikers. 
4. Bij het aanmelden brengt de foto-app de identiteit van de gebruiker met de mid-tier-service tot stand. Dit mechanisme voor identiteits inrichting is uitsluitend voor de toepassing.
5. Zodra de identiteit tot stand is gebracht, vraagt de mid-tier-service machtigingen aan op basis van de identiteit.
6. De mid-tier-service stuurt een bron token terug naar de telefoon-app.
7. De telefoon-app kan het bron token blijven gebruiken voor rechtstreekse toegang tot Cosmos DB resources met de machtigingen die zijn gedefinieerd door het bron token en voor het interval dat is toegestaan door het bron token. 
8. Wanneer het bron token verloopt, ontvangen volgende aanvragen een ongeautoriseerde uitzonde ring van 401.  Op dit moment brengt de telefoon-app de identiteit opnieuw tot stand en wordt er een nieuw bron token aangevraagd.

    ![Werk stroom voor Azure Cosmos DB resource tokens](./media/secure-access-to-data/resourcekeyworkflow.png)

Het genereren en beheren van bron tokens worden verwerkt door de systeem eigen Cosmos DB-client bibliotheken. Als u echter REST gebruikt, moet u de aanvraag-en verificatie headers maken. Zie [Access Control op Cosmos DB resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) of de [bron code voor onze sdk's](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js)voor meer informatie over het maken van verificatie headers voor rest.

Zie de [resource token Broker-app](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)voor een voor beeld van een middle-tier service die wordt gebruikt om resource tokens te genereren of Broker.

<a id="users"></a>

## <a name="users"></a>Gebruikers
Cosmos DB gebruikers zijn gekoppeld aan een Cosmos-data base.  Elke Data Base kan nul of meer Cosmos DB gebruikers bevatten.  In het volgende code voorbeeld ziet u hoe u een Cosmos DB-gebruikers bron maakt.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Elke Cosmos DB gebruiker heeft een PermissionsLink-eigenschap die kan worden gebruikt voor het ophalen van de lijst met [machtigingen](#permissions) die aan de gebruiker zijn gekoppeld.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Machtigingen
Een Cosmos DB machtigings resource is gekoppeld aan een Cosmos DB gebruiker.  Elke gebruiker kan nul of meer Cosmos DB machtigingen bevatten.  Een machtigings bron biedt toegang tot een beveiligings token dat de gebruiker nodig heeft om toegang te krijgen tot een specifieke toepassings bron.
Er zijn twee toegangs niveaus beschikbaar die kunnen worden gegeven door een machtigings Bron:

* Alle: de gebruiker heeft volledige machtigingen voor de resource.
* Lezen: de gebruiker kan alleen de inhoud van de resource lezen, maar kan geen schrijf-, bijwerk-of verwijderings bewerkingen uitvoeren op de resource.

> [!NOTE]
> Om Cosmos DB opgeslagen procedures uit te voeren, moet de gebruiker beschikken over de machtiging all voor de container waarin de opgeslagen procedure wordt uitgevoerd.
> 
> 

### <a name="code-sample-to-create-permission"></a>Code voorbeeld voor het maken van machtigingen

In het volgende code voorbeeld ziet u hoe u een machtigings bron maakt, het bron token van de machtigings resource leest en de machtigingen koppelt aan de [gebruiker](#users) die hierboven is gemaakt.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Als u een partitie sleutel voor uw verzameling hebt opgegeven, moet de machtiging voor verzamelings-, document-en bijlage bronnen ook de ResourcePartitionKey bevatten, naast de ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Code voorbeeld voor het lezen van machtigingen voor de gebruiker

Cosmos DB maakt een machtigings feed voor elk gebruikers object beschikbaar om eenvoudig alle machtigings resources te verkrijgen die aan een bepaalde gebruiker zijn gekoppeld.  Het volgende code fragment laat zien hoe u de machtiging kunt ophalen die is gekoppeld aan de gebruiker die hierboven is gemaakt, een lijst met machtigingen maakt en een nieuwe DocumentClient namens de gebruiker maakt.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="add-users-and-assign-roles"></a>Gebruikers toevoegen en rollen toewijzen

Als u Azure Cosmos DB account lezer-toegang wilt toevoegen aan uw gebruikers account, moet u de eigenaar van een abonnement hebben om de volgende stappen uit te voeren in de Azure Portal.

1. Open de Azure Portal en selecteer uw Azure Cosmos DB-account.
2. Klik op het tabblad **toegangs beheer (IAM)** en klik vervolgens op **+ roltoewijzing toevoegen**.
3. In het deel venster **toewijzing van rol toevoegen** selecteert u **Cosmos DB rol van account lezer**in het vak **rol** .
4. Selecteer in het **vak toegang toewijzen aan**de optie **Azure AD-gebruiker,-groep of-toepassing**.
5. Selecteer de gebruiker, groep of toepassing in de map waaraan u toegang wilt verlenen.  U kunt in de map zoeken op de weergave naam, het e-mail adres of de object-id's.
    De geselecteerde gebruiker, groep of toepassing wordt weer gegeven in de lijst geselecteerde leden.
6. Klik op **Opslaan**.

De entiteit kan nu Azure Cosmos DB resources lezen.

## <a name="delete-or-export-user-data"></a>Gebruikers gegevens verwijderen of exporteren
Azure Cosmos DB kunt u zoeken, selecteren, wijzigen en verwijderen van persoonlijke gegevens die zich in data base of verzamelingen bevinden. Azure Cosmos DB biedt Api's om persoons gegevens te vinden en te verwijderen. het is uw verantwoordelijkheid om de Api's te gebruiken en de logica te definiëren die is vereist om de persoonlijke gegevens te wissen. Elke API voor meerdere modellen (SQL, MongoDB, Gremlin, Cassandra, tabel) biedt verschillende taal-Sdk's die methoden bevatten voor het zoeken en verwijderen van persoonlijke gegevens. U kunt ook de [TTL-functie (time to Live)](time-to-live.md) zo instellen dat gegevens na een bepaalde periode automatisch worden verwijderd, zonder dat er extra kosten in rekening worden gebracht.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Volgende stappen
* Zie [Cosmos DB: data base Security](database-security.md)voor meer informatie over de beveiliging van Cosmos-data bases.
* Zie [Access Control op Azure Cosmos DB resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources)voor meer informatie over het maken van Azure Cosmos DB autorisatie tokens.
