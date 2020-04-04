---
title: Gebruikers beheren met de Microsoft Graph API
titleSuffix: Azure AD B2C
description: Gebruikers beheren in een Azure AD B2C-tenant door de Microsoft Graph API aan te roepen en een toepassingsidentiteit te gebruiken om het proces te automatiseren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 42596ba5470c6062efba4fd1050c1c9745b76e80
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637335"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Azure AD B2C-gebruikersaccounts beheren met Microsoft Graph

Met Microsoft Graph u gebruikersaccounts beheren in uw Azure AD B2C-map door methoden voor maken, lezen, bijwerken en verwijderen in de Microsoft Graph-API te bieden. U een bestaande gebruikerswinkel migreren naar een Azure AD B2C-tenant en andere beheerbewerkingen voor gebruikersaccounts uitvoeren door de Microsoft Graph API aan te roepen.

In de volgende secties worden de belangrijkste aspecten van Azure AD B2C-gebruikersbeheer met de Microsoft Graph-API gepresenteerd. De hier gepresenteerde Microsoft Graph API-bewerkingen, -typen en -eigenschappen zijn een subset van die welke wordt weergegeven in de referentiedocumentatie voor Microsoft Graph API.

## <a name="register-a-management-application"></a>Een beheeraanvraag registreren

Voordat een toepassing voor gebruikersbeheer of een script dat u schrijft, kan communiceren met de bronnen in uw Azure AD B2C-tenant, hebt u een toepassingsregistratie nodig die de machtigingen hiervoor verleent.

Volg de stappen in dit artikel om een toepassingsregistratie te maken die uw beheertoepassing kan gebruiken:

[Azure AD B2C beheren met Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Microsoft Graph-bewerkingen voor gebruikersbeheer

De volgende gebruikersbeheerbewerkingen zijn beschikbaar in de [Microsoft Graph-API:](https://docs.microsoft.com/graph/api/resources/user)

- [Een lijst met gebruikers](https://docs.microsoft.com/graph/api/user-list)
- [Een gebruiker maken](https://docs.microsoft.com/graph/api/user-post-users)
- [Een gebruiker krijgen](https://docs.microsoft.com/graph/api/user-get)
- [Een gebruiker bijwerken](https://docs.microsoft.com/graph/api/user-update)
- [Een gebruiker verwijderen](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Gebruikerseigenschappen

### <a name="display-name-property"></a>Weergavenaam, eigenschap

Het `displayName` is de naam die moet worden weergegeven in azure portal gebruikersbeheer voor de gebruiker en in het toegangstoken Azure AD B2C terugkeert naar de toepassing. Deze accommodatie is vereist.

### <a name="identities-property"></a>Eigenschappen van identiteiten

Een klantenaccount, dat een consument, partner of burger kan zijn, kan aan deze identiteitstypen worden gekoppeld:

- **Lokale** identiteit : de gebruikersnaam en het wachtwoord worden lokaal opgeslagen in de Azure AD B2C-map. We noemen deze identiteiten vaak 'lokale accounts'.
- **Federatieve** identiteit - Ook wel bekend als een *sociale* of *zakelijke* accounts, wordt de identiteit van de gebruiker beheerd door een federatieve identiteitsprovider zoals Facebook, Microsoft, ADFS of Salesforce.

Een gebruiker met een klantaccount kan zich met meerdere identiteiten aanmelden. Bijvoorbeeld gebruikersnaam, e-mail, werknemers-ID, overheids-ID, en anderen. Eén account kan meerdere identiteiten hebben, zowel lokaal als sociaal, met hetzelfde wachtwoord.

In de Microsoft Graph API worden zowel lokale als federatieve identiteiten opgeslagen in het gebruikerskenmerk, `identities` dat van het type [objectIdentity][graph-objectIdentity]is. De `identities` verzameling vertegenwoordigt een reeks identiteiten die worden gebruikt om u aan te melden bij een gebruikersaccount. Met deze verzameling kan de gebruiker zich aanmelden bij het gebruikersaccount met een van de bijbehorende identiteiten.

| Eigenschap   | Type |Beschrijving|
|:---------------|:--------|:----------|
|signInType|tekenreeks| Hiermee geeft u de aanmeldingstypen van de gebruiker op in uw map. Voor lokale `emailAddress`account: `emailAddress2` `emailAddress3`, `userName`, `emailAddress1`, , of een ander type dat u wilt. Sociale rekening moet `federated`worden ingesteld op .|
|uitgever|tekenreeks|Hiermee geeft u de uitgever van de identiteit op. Voor lokale accounts (waarbij **signInType** dat niet `federated`is), is deze eigenschap `contoso.onmicrosoft.com`bijvoorbeeld de standaarddomeinnaam van de lokale B2C-tenant. Voor sociale identiteit (waar `federated` **signInType** is) is de waarde de naam van de emittent, bijvoorbeeld`facebook.com`|
|issuerAssignedId|tekenreeks|Hiermee geeft u de unieke id op die door de uitgever aan de gebruiker is toegewezen. De combinatie van **emittent** en **emittentAssignedId** moet uniek zijn binnen uw tenant. Voor een lokaal account, wanneer **signInType** is ingesteld op `emailAddress` of `userName`, vertegenwoordigt het de aanmeldingsnaam voor de gebruiker.<br>Wanneer **signInType** is ingesteld op: <ul><li>`emailAddress`(of begint `emailAddress` `emailAddress1`met like) **uitgeverAssignedId** moet een geldig e-mailadres zijn</li><li>`userName`(of een andere waarde), **moet issuerAssignedId** een geldig [lokaal onderdeel van een e-mailadres](https://tools.ietf.org/html/rfc3696#section-3) zijn</li><li>`federated`, **vertegenwoordigt issuerAssignedId** de federatieve account unieke id</li></ul>|

De volgende **eigenschap Identiteiten,** met een lokale accountidentiteit met een aanmeldingsnaam, een e-mailadres als aanmelding en met een sociale identiteit. 

 ```JSON
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Voor federatieve identiteiten, afhankelijk van de identiteitsprovider, is de **issuerAssignedId** een unieke waarde voor een bepaalde gebruiker per toepassings- of ontwikkelingsaccount. Configureer het Azure AD B2C-beleid met dezelfde toepassings-id die eerder door de sociale provider of een andere toepassing binnen hetzelfde ontwikkelingsaccount is toegewezen.

### <a name="password-profile-property"></a>Eigenschap Wachtwoordprofiel

Voor een lokale identiteit is de eigenschap **passwordProfile** vereist en bevat het wachtwoord van de gebruiker. De `forceChangePasswordNextSignIn` accommodatie moet `false`instellen op .

Voor een federatieve (sociale) identiteit is de eigenschap **passwordProfile** niet vereist.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>De eigenschap Wachtwoordbeleid

Het Azure AD B2C-wachtwoordbeleid (voor lokale accounts) is gebaseerd op het azure Active [Directory-beleid voor sterke wachtwoordsterkte.](../active-directory/authentication/concept-sspr-policy.md) Het azure AD B2C-aanmeldings- of aanmeldings- en wachtwoordresetbeleid vereist deze sterke wachtwoordsterkte en verloopt niet voor wachtwoorden.

Als de accounts die u wilt migreren in scenario's voor gebruikersmigratie een zwakkere wachtwoordsterkte hebben dan de [sterke wachtwoordsterkte](../active-directory/authentication/concept-sspr-policy.md) die wordt afgedwongen door Azure AD B2C, u de sterke wachtwoordvereiste uitschakelen. Als u het standaardwachtwoordbeleid `passwordPolicies` wilt `DisableStrongPassword`wijzigen, stelt u de eigenschap in op . U bijvoorbeeld het gebruikersverzoek maken als volgt wijzigen:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Uitbreidingseigenschappen

Elke klantgerichte toepassing heeft unieke vereisten voor de te verzamelen informatie. Uw Azure AD B2C-tenant wordt geleverd met een ingebouwde set informatie die is opgeslagen in eigenschappen, zoals Voornaam, Achternaam, Plaats en Postcode. Met Azure AD B2C u de set eigenschappen die in elk klantaccount zijn opgeslagen, uitbreiden. Zie [aangepaste kenmerken (gebruikersstromen)](user-flow-custom-attributes.md) en [aangepaste kenmerken (aangepaste beleidsregels)](custom-policy-custom-attributes.md)voor meer informatie over het definiëren van aangepaste kenmerken.

Microsoft Graph API ondersteunt het maken en bijwerken van een gebruiker met extensiekenmerken. Extensiekenmerken in de Graph API worden `extension_ApplicationObjectID_attributename`benoemd met behulp van de conventie . Bijvoorbeeld:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Codevoorbeeld

Dit codevoorbeeld is een .NET Core-consoletoepassing die de [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) gebruikt om te communiceren met de Microsoft Graph API. De code laat zien hoe u de API aanroept om gebruikers in een Azure AD B2C-tenant programmatisch te beheren.
U [het voorbeeldarchief](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), [door de opslagplaats](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) op GitHub bladeren of de repository klonen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Nadat u het codevoorbeeld hebt verkregen, configureert u het voor uw omgeving en bouwt u het project:

1. Open het project in [Visual Studio](https://visualstudio.microsoft.com) of Visual [Studio Code](https://code.visualstudio.com).
1. Open `src/appsettings.json`.
1. Vervang `appSettings` in de `your-b2c-tenant` sectie de naam van `Application (client) ID` `Client secret` uw huurder en de waarden voor uw registratie van beheeraanvragen (zie het gedeelte [Een beheeraanvraag](#register-a-management-application) van dit artikel registreren).
1. Open een consolevenster in de lokale kloon van `src` de repo, schakel over naar de map en bouw het project:
    ```console
    cd src
    dotnet build
    ```
1. Voer de toepassing `dotnet` uit met de opdracht:

    ```console
    dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
    ```

De toepassing geeft een lijst weer met opdrachten die u uitvoeren. Download bijvoorbeeld alle gebruikers, ontvang één gebruiker, verwijder een gebruiker, werk het wachtwoord van een gebruiker bij en importeer in bulk.

### <a name="code-discussion"></a>Codediscussie

De voorbeeldcode maakt gebruik van de [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview), die is ontworpen om het bouwen van hoogwaardige, efficiënte en veerkrachtige toepassingen die toegang hebben tot Microsoft Graph te vereenvoudigen.

Voor elk verzoek tot de Microsoft Graph API is een toegangstoken voor verificatie vereist. De oplossing maakt gebruik van het [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet-pakket dat een op verificatiescenario gebaseerde wrapper van de Microsoft Authentication Library (MSAL) biedt voor gebruik met de Microsoft Graph SDK.

De `RunAsync` methode in het _Program.cs_ bestand:

1. Hiermee leest u toepassingsinstellingen uit het _bestand appsettings.json_
1. Initialiseert de auth-provider met behulp van [OAuth 2.0-clientreferenties.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) Met de subsidiestroom voor clientreferenties kan de app een toegangstoken krijgen om de Microsoft Graph-API aan te roepen.
1. Hiermee stelt u de Microsoft Graph-serviceclient in bij de auth-provider:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

De geïnitialiseerde *GraphServiceClient* wordt vervolgens gebruikt in _UserService.cs_ om de gebruikersbeheerbewerkingen uit te voeren. Bijvoorbeeld het verkrijgen van een lijst met de gebruikersaccounts in de tenant:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Api-aanroepen uitvoeren met behulp van de Microsoft Graph-SDKs](https://docs.microsoft.com/graph/sdks/create-requests) bevat `$select` informatie over het lezen en schrijven van `$filter` `$orderBy` informatie uit Microsoft Graph, gebruiken om de geretourneerde eigenschappen te beheren, aangepaste queryparameters op te geven en de queryparameters te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Microsoft Graph-bewerkingen die beschikbaar zijn voor Azure AD B2C-bewerkingen die beschikbaar zijn voor Azure AD B2C voor](microsoft-graph-operations.md)een volledige index van de API-bewerkingen van Microsoft Graph die worden ondersteund voor Azure AD B2C- bronnen.

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
