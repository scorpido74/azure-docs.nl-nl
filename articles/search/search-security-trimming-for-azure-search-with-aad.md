---
title: Beveiligings filters voor het afkappen van resultaten met behulp van Active Directory-Azure Search
description: Toegangs beheer voor Azure Search inhoud met behulp van beveiligings filters en de identiteit van Azure Active Directory (AAD).
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 8bcc1dcd1d86c0ca18ed03dc60834884a42a39c9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186529"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Beveiligings filters voor het verkleinen van Azure Search resultaten met behulp van Active Directory-identiteiten

In dit artikel wordt beschreven hoe u met behulp van Azure Active Directory (AAD)-beveiligings identiteiten samen met filters in Azure Search Zoek resultaten kunt knippen op basis van lidmaatschap van de gebruikers groep.

Dit artikel behandelt de volgende taken:
> [!div class="checklist"]
> - AAD-groepen en-gebruikers maken
> - De gebruiker koppelen aan de groep die u hebt gemaakt
> - De nieuwe groepen in de cache opslaan
> - Documenten indexeren met gekoppelde groepen
> - Een zoek opdracht met groeps-id's filteren
> 
> [!NOTE]
> Voorbeeld code fragmenten in dit artikel zijn geschreven in C#. U vindt de volledige broncode [op GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Vereisten

De index in Azure Search moet een [beveiligings veld](search-security-trimming-for-azure-search.md) bevatten voor het opslaan van de lijst met groeps-id's met lees toegang tot het document. In dit voor beeld wordt uitgegaan van een een-op-een-correspondentie tussen een beveiligbaar item (zoals een college van een persoon) en een beveiligings veld dat bepaalt wie toegang heeft tot dat item (Admissions).

U moet beschikken over AAD-beheerders machtigingen die zijn vereist in dit overzicht voor het maken van gebruikers, groepen en koppelingen in AAD.

Uw toepassing moet ook zijn geregistreerd bij AAD, zoals beschreven in de volgende procedure.

### <a name="register-your-application-with-aad"></a>Uw toepassing registreren bij AAD

Deze stap integreert uw toepassing met AAD voor het accepteren van aanmeldingen van gebruikers-en groeps accounts. Als u geen AAD-beheerder bent in uw organisatie, moet u mogelijk [een nieuwe Tenant maken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) om de volgende stappen uit te voeren.

1. Ga naar de >  app > voor het registreren van de [**toepassings registratie Portal**](https://apps.dev.microsoft.com)**een app toevoegen**.
2. Voer een naam in voor uw toepassing en klik vervolgens op **maken**. 
3. Selecteer de zojuist geregistreerde toepassing op de pagina mijn toepassingen.
4. Kies op de pagina Toepassings registratie > **platforms** > **platform toevoegen**de optie **Web-API**.
5. Ga nog steeds op de pagina Toepassings registratie naar > **Microsoft Graph machtigingen** > **toevoegen**.
6. Voeg in machtigingen selecteren de volgende gedelegeerde machtigingen toe en klik vervolgens op **OK**:

   + **Directory.ReadWrite.All**
   + **Group. ReadWrite. all**
   + **User. ReadWrite. all**

Microsoft Graph biedt een API die programmatische toegang tot AAD via een REST API mogelijk maakt. In het code voorbeeld voor deze walkthrough worden de machtigingen gebruikt voor het aanroepen van de Microsoft Graph-API voor het maken van groepen, gebruikers en koppelingen. De Api's worden ook gebruikt om groeps-id's in de cache op te slaan voor snellere filtering.

## <a name="create-users-and-groups"></a>Gebruikers en groepen maken

Als u een zoek opdracht toevoegt aan een vastgelegde toepassing, hebt u mogelijk bestaande gebruikers-en groeps-id's in AAD. In dit geval kunt u de volgende drie stappen overs Laan. 

Als u echter geen bestaande gebruikers hebt, kunt u Microsoft Graph-Api's gebruiken om de beveiligings-principals te maken. De volgende code fragmenten laten zien hoe u id's genereert. dit worden gegevens waarden voor het beveiligings veld in uw Azure Search-index. In onze hypothetische toepassing voor colleges is dit de beveiligings-id's voor admission medewerkers.

Gebruikers-en groepslid maatschappen kunnen zeer onvoorzichtig zijn, met name voor grote organisaties. Code die gebruikers-en groeps-id's bouwt, moet vaak genoeg worden uitgevoerd om wijzigingen in het lidmaatschap van de organisatie op te halen. Op dezelfde manier moet uw Azure Search-index een vergelijk bare update planning hebben om de huidige status van toegestane gebruikers en resources weer te geven.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>Stap 1: [Aad-groep](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) maken 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-userhttpsdocsmicrosoftcomgraphapiuser-post-usersviewgraph-rest-10"></a>Stap 2: [Aad-gebruiker](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0) maken
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Stap 3: Gebruiker en groep koppelen
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Stap 4: De groeps-id's in de cache opslaan
Als u de netwerk latentie wilt beperken, kunt u de groeps beleidsobjecten van de gebruiker in de cache opslaan zodat er groepen worden geretourneerd uit de cache, waarbij een retour naar AAD wordt opgeslagen. U kunt [Aad batch API](https://developer.microsoft.com/graph/docs/concepts/json_batching) gebruiken om één HTTP-aanvraag met meerdere gebruikers te verzenden en de cache te bouwen.

Microsoft Graph is ontworpen om een groot aantal aanvragen af te handelen. Als er sprake is van een overweldigend aantal aanvragen, mislukt de aanvraag met de HTTP-status code 429 van Microsoft Graph. Zie [Microsoft Graph beperking](https://developer.microsoft.com/graph/docs/concepts/throttling)voor meer informatie.

## <a name="index-document-with-their-permitted-groups"></a>Document indexeren met de toegestane groepen

Query bewerkingen in Azure Search worden uitgevoerd via een Azure Search index. In deze stap importeert een indexerings bewerking Doorzoek bare gegevens in een index, inclusief de id's die als beveiligings filters worden gebruikt. 

Azure Search verifieert geen gebruikers identiteiten of biedt logica om te bepalen welke inhoud een gebruiker mag weer geven. Bij het gebruik van beveiligings beperking wordt ervan uitgegaan dat u de koppeling tussen een gevoelig document en de groeps-id die toegang heeft tot het document, hebt geïmporteerd in een zoek index. 

In het hypothetische voor beeld bevat de hoofd tekst van de PUT-aanvraag voor een Azure Search index het College of transcript van een sollicitant, samen met de groeps-id die gemachtigd is om die inhoud weer te geven. 

In het algemene voor beeld dat in het code voorbeeld voor dit scenario wordt gebruikt, kan de index actie er als volgt uitzien:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Een zoek opdracht uitgeven

Voor beveiligings beperking zijn de waarden in uw beveiligings veld in de index statische waarden die worden gebruikt voor het opnemen of uitsluiten van documenten in Zoek resultaten. Als de groeps-id voor Admissions bijvoorbeeld "A11B22C33D44-E55F66G77-H88I99JKK" is, worden alle documenten in een Azure Search index met die id in het beveiligings document opgenomen (of uitgesloten) in de zoek resultaten die worden teruggestuurd naar de aanvrager.

Als u documenten wilt filteren die zijn geretourneerd in de zoek resultaten op basis van groepen van de gebruiker die de aanvraag heeft verzonden, raadpleegt u de volgende stappen.

### <a name="step-1-retrieve-users-group-identifiers"></a>Stap 1: Groeps-id's van gebruiker ophalen

Als de gebruikers groepen nog niet in de cache zijn opgeslagen of de cache is verlopen, geeft u de aanvraag voor de [groep](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) op
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Stap 2: De zoek aanvraag opstellen

Ervan uitgaande dat u het lidmaatschap van de groep van de gebruiker hebt, kunt u de zoek opdracht uitgeven met de juiste filter waarden.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Stap 3: De resultaten verwerken

Het antwoord bevat een gefilterde lijst met documenten, die bestaan uit degene die de gebruiker mag bekijken. Afhankelijk van hoe u de pagina met zoek resultaten bouwt, wilt u mogelijk visuele aanwijzingen toevoegen aan de gefilterde resultatenset.

## <a name="conclusion"></a>Conclusie

In dit scenario hebt u technieken geleerd voor het gebruik van AAD-aanmeldingen om documenten te filteren in Azure Search resultaten, waardoor de resultaten van documenten die niet overeenkomen met het filter dat is opgegeven in de aanvraag, worden afgekapt.

## <a name="see-also"></a>Zie ook

+ [Toegangs beheer op basis van een id met behulp van Azure Search filters](search-security-trimming-for-azure-search.md)
+ [Filters in Azure Search](search-filters.md)
+ [Gegevens beveiliging en toegangs beheer in Azure Search bewerkingen](search-security-overview.md)
