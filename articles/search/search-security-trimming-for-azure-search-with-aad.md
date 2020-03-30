---
title: Beveiligingsfilters om resultaten bij te snijden met Active Directory
titleSuffix: Azure Cognitive Search
description: Toegangsbeheer voor Azure Cognitive Search-inhoud met behulp van beveiligingsfilters en AAD-identiteiten (Azure Active Directory).
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794306"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Beveiligingsfilters voor het bijsnijden van Azure Cognitive Search-resultaten met Active Directory-identiteiten

In dit artikel wordt uitgelegd hoe u aad-beveiligingsidentiteiten (Azure Active Directory) gebruiken, samen met filters in Azure Cognitive Search om zoekresultaten bij te snijden op basis van gebruikersgroepslidmaatschap.

Dit artikel behandelt de volgende taken:
> [!div class="checklist"]
> - Aadgroepen en -gebruikers maken
> - De gebruiker koppelen aan de groep die u hebt gemaakt
> - De nieuwe groepen caches
> - Documenten met gekoppelde groepen indexeren
> - Een zoekaanvraag uitgeven met het filter groep-id's
> 
> [!NOTE]
> Voorbeeldcodefragmenten in dit artikel zijn geschreven in C#. U vindt de volledige broncode [op GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Vereisten

Uw index in Azure Cognitive Search moet een [beveiligingsveld](search-security-trimming-for-azure-search.md) hebben om de lijst met groepsidentiteiten op te slaan die de toegang tot het document hebben gelezen. Deze use case gaat uit van een één-op-één correspondentie tussen een securable item (zoals de universiteitsaanvraag van een individu) en een beveiligingsveld waarin wordt aangegeven wie toegang heeft tot dat item (toelatingspersoneel).

U moet aad-beheerdersmachtigingen hebben die vereist zijn in deze walkthrough voor het maken van gebruikers, groepen en koppelingen in AAD.

Uw aanvraag moet ook geregistreerd zijn bij AAD, zoals beschreven in de volgende procedure.

### <a name="register-your-application-with-aad"></a>Registreer uw sollicitatie bij AAD

Deze stap integreert uw toepassing met AAD met het oog op het accepteren van aanmeldingen van gebruikers- en groepsaccounts. Als u geen AAD-beheerder in uw organisatie bent, moet u mogelijk [een nieuwe tenant maken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) om de volgende stappen uit te voeren.

1. Ga naar de app Portal voor [**aanvraagregistratie**](https://apps.dev.microsoft.com) >  **converged** > **voeg een app toe.**
2. Voer een naam voor uw toepassing in en klik op **Maken**. 
3. Selecteer uw nieuw geregistreerde aanvraag op de pagina Mijn toepassingen.
4. Kies **Web API**op de pagina voor toepassingregistratie > **Platforms** > **Add Platform**.
5. Ga nog steeds op de pagina voor toepassingsregistratie naar > **Microsoft Graph Permissions** > **Add**.
6. Voeg in Machtigingen selecteren de volgende gedelegeerde machtigingen toe en klik op **OK:**

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph biedt een API waarmee programmatische toegang tot AAD via een REST API. Het codevoorbeeld voor deze walkthrough gebruikt de machtigingen om de Microsoft Graph API aan te roepen voor het maken van groepen, gebruikers en koppelingen. De API's worden ook gebruikt om groeps-id's in de cache te bewaren voor sneller filteren.

## <a name="create-users-and-groups"></a>Gebruikers en groepen maken

Als u zoeken toevoegt aan een gevestigde toepassing, hebt u mogelijk bestaande gebruikers- en groeps-id's in AAD. In dit geval u de volgende drie stappen overslaan. 

Als u echter geen bestaande gebruikers hebt, u Api's van Microsoft Graph gebruiken om de beveiligingsprincipals te maken. In de volgende codefragmenten wordt uitgelegd hoe u id's genereren, die gegevenswaarden worden voor het beveiligingsveld in uw Azure Cognitive Search-index. In onze hypothetische toelatingsaanvraag van de universiteit, zou dit de beveiligingsidentificaties zijn voor toelatingspersoneel.

Gebruikers- en groepslidmaatschap kan zeer vloeiend zijn, vooral in grote organisaties. Code die gebruikers- en groepsidentiteiten bouwt, moet vaak genoeg worden uitgevoerd om wijzigingen in het lidmaatschap van de organisatie op te pikken. Op dezelfde manier vereist uw Azure Cognitive Search-index een vergelijkbaar updateschema om de huidige status van toegestane gebruikers en resources weer te geven.

### <a name="step-1-create-aad-group"></a>Stap 1: [AAD-groep maken](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
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
   
### <a name="step-2-create-aad-user"></a>Stap 2: [AAD-gebruiker maken](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
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

### <a name="step-4-cache-the-groups-identifiers"></a>Stap 4: De groepen-id's cache
Optioneel u, om de netwerklatentie te verminderen, de gebruikersgroepkoppelingen in de cache opslaan, zodat groepen, wanneer een zoekverzoek wordt uitgegeven, worden teruggestuurd uit de cache, waardoor een retourvlucht naar AAD wordt opgeslagen. U [AAD Batch API](https://developer.microsoft.com/graph/docs/concepts/json_batching) gebruiken om één Http-aanvraag met meerdere gebruikers te verzenden en de cache te bouwen.

Microsoft Graph is ontworpen om een groot aantal aanvragen te verwerken. Als er een overweldigend aantal aanvragen optreedt, mislukt Microsoft Graph de aanvraag met HTTP-statuscode 429. Zie [Microsoft Graph throttling voor](https://developer.microsoft.com/graph/docs/concepts/throttling)meer informatie.

## <a name="index-document-with-their-permitted-groups"></a>Indexdocument met de toegestane groepen

Querybewerkingen in Azure Cognitive Search worden uitgevoerd via een Azure Cognitive Search-index. In deze stap importeert een indexeringsbewerking doorzoekbare gegevens in een index, inclusief de id's die als beveiligingsfilters worden gebruikt. 

Azure Cognitive Search verifieert geen gebruikersidentiteiten of biedt geen logica om vast te stellen welke inhoud een gebruiker toestemming heeft om te bekijken. De use case voor security trimming gaat ervan uit dat u de koppeling tussen een gevoelig document en de groeps-id die toegang heeft tot dat document, intact in een zoekindex hebt geïmporteerd. 

In het hypothetische voorbeeld bevat de instantie van de PUT-aanvraag op een Azure Cognitive Search-index het college-essay of -transcript van een sollicitant, samen met de groeps-id die toestemming heeft om die inhoud te bekijken. 

In het algemene voorbeeld dat wordt gebruikt in het codevoorbeeld voor deze walkthrough, ziet de indexactie er mogelijk als volgt uit:

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

## <a name="issue-a-search-request"></a>Een zoekverzoek uitgeven

Voor beveiligingsdoeleinden zijn de waarden in uw beveiligingsveld in de index statische waarden die worden gebruikt voor het opnemen of uitsluiten van documenten in zoekresultaten. Als de groeps-id voor toelatingen bijvoorbeeld "A11B22C33D4-E55F66G77-H88I99JKK" is, worden alle documenten in een Azure Cognitive Search-index met die id in de ingediende beveiliging opgenomen (of uitgesloten) in de zoekresultaten die naar de aanvrager worden teruggestuurd.

Als u documenten wilt filteren die zijn geretourneerd in zoekresultaten op basis van groepen van de gebruiker die de aanvraag uitgeeft, controleert u de volgende stappen.

### <a name="step-1-retrieve-users-group-identifiers"></a>Stap 1: Groeps-id's van de gebruiker ophalen

Als de groepen van de gebruiker nog niet in de cache zijn opgeslagen of als de cache is verlopen, geeft u het [groepsverzoek](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
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

### <a name="step-2-compose-the-search-request"></a>Stap 2: De zoekaanvraag opstellen

Ervan uitgaande dat u het groepslidmaatschap van de gebruiker hebt, u de zoekaanvraag met de juiste filterwaarden opgeven.

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

Het antwoord bevat een gefilterde lijst met documenten, bestaande uit documenten die de gebruiker toestemming heeft om te bekijken. Afhankelijk van hoe u de pagina met zoekresultaten maakt, u visuele aanwijzingen opnemen om de gefilterde resultaatset weer te geven.

## <a name="conclusion"></a>Conclusie

In deze walkthrough hebt u technieken geleerd voor het gebruik van AAD-aanmeldingen om documenten te filteren in Azure Cognitive Search-resultaten, waarbij de resultaten worden bijgemaakt van documenten die niet overeenkomen met het filter dat op de aanvraag wordt verstrekt.

## <a name="see-also"></a>Zie ook

+ [Op identiteit gebaseerdtoegangsbeheer met Azure Cognitive Search-filters](search-security-trimming-for-azure-search.md)
+ [Filters in Azure Cognitive Search](search-filters.md)
+ [Gegevensbeveiliging en toegangscontrole in Azure Cognitive Search-bewerkingen](search-security-overview.md)
