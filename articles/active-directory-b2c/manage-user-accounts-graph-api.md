---
title: Gebruikers beheren met de Microsoft Graph-API
titleSuffix: Azure AD B2C
description: Gebruikers in een Azure AD B2C-Tenant beheren door de Microsoft Graph-API aan te roepen en een toepassings-id te gebruiken om het proces te automatiseren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b53405d199072211304e21b681de646c4e41243c
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585625"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Azure AD B2C gebruikers accounts beheren met Microsoft Graph

Met Microsoft Graph kunt u gebruikers accounts in uw Azure AD B2C Directory beheren door methoden voor maken, lezen, bijwerken en verwijderen op te geven in de Microsoft Graph-API. U kunt een bestaande gebruikers opslag migreren naar een Azure AD B2C Tenant en andere beheer bewerkingen voor gebruikers accounts uitvoeren door de Microsoft Graph-API aan te roepen.

In de volgende secties worden de belangrijkste aspecten van Azure AD B2C gebruikers beheer met de Microsoft Graph-API weer gegeven. De Microsoft Graph-API-bewerkingen, typen en eigenschappen die hier worden weer gegeven, zijn een subset van die in de naslag documentatie over de Microsoft Graph-API wordt vermeld.

## <a name="register-a-management-application"></a>Een beheer toepassing registreren

Voordat u een toepassing voor gebruikers beheer of een door u geschreven script kunt gebruiken om te werken met de resources in uw Azure AD B2C Tenant, hebt u een toepassings registratie nodig die de machtigingen verleent.

Volg de stappen in dit artikel om een toepassings registratie te maken die uw beheer toepassing kan gebruiken:

[Azure AD B2C beheren met Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Microsoft Graph bewerkingen voor gebruikers beheer

De volgende gebruikers beheer bewerkingen zijn beschikbaar in de [Microsoft Graph-API](https://docs.microsoft.com/graph/api/resources/user):

- [Een lijst met gebruikers ophalen](https://docs.microsoft.com/graph/api/user-list)
- [Een gebruiker maken](https://docs.microsoft.com/graph/api/user-post-users)
- [Een gebruiker ophalen](https://docs.microsoft.com/graph/api/user-get)
- [Een gebruiker bijwerken](https://docs.microsoft.com/graph/api/user-update)
- [Een gebruiker verwijderen](https://docs.microsoft.com/graph/api/user-delete)

## <a name="user-properties"></a>Gebruikerseigenschappen

### <a name="display-name-property"></a>Eigenschap weergave naam

De `displayName` is de naam die wordt weer gegeven in Azure Portal gebruikers beheer voor de gebruiker, en in het toegangs token dat Azure AD B2C terugkeert naar de toepassing. Deze eigenschap is vereist.

### <a name="identities-property"></a>Eigenschap Identities

Een klant account, een consument, een partner of een burger, kan worden gekoppeld aan deze identiteits typen:

- **Lokale** identiteit: de gebruikers naam en het wacht woord worden lokaal opgeslagen in de Azure AD B2C Directory. We verwijzen vaak naar deze identiteiten als ' lokale accounts '.
- **Federatieve** identiteit: ook wel een *sociale* of *ondernemings* account genoemd, wordt de identiteit van de gebruiker beheerd door een federatieve id-provider zoals Facebook, micro soft, ADFS of Sales Force.

Een gebruiker met een klant account kan zich aanmelden met meerdere identiteiten. Bijvoorbeeld gebruikers naam, e-mail adres, werk nemer-ID, sofi-ID en anderen. Eén account kan meerdere identiteiten, zowel lokaal als sociaal, hebben met hetzelfde wacht woord.

In de Microsoft Graph-API worden lokale en federatieve identiteiten opgeslagen in het kenmerk gebruikers `identities`, dat van het type [objectIdentity][graph-objectIdentity]is. De verzameling `identities` vertegenwoordigt een set met identiteiten die worden gebruikt om u aan te melden bij een gebruikers account. Met deze verzameling kan de gebruiker zich aanmelden bij het gebruikers account met een van de bijbehorende identiteiten.

| Eigenschap   | Type |Beschrijving|
|:---------------|:--------|:----------|
|signInType|tekenreeks| Hiermee geeft u de aanmeldings typen voor gebruikers in uw Directory. Voor een lokaal account: `emailAddress`, `emailAddress1`, `emailAddress2`, `emailAddress3`, `userName`of een ander type dat u wilt. Sociaal account moet worden ingesteld op `federated`.|
|verlener|tekenreeks|Hiermee geeft u de uitgever van de identiteit. Voor lokale accounts (waarbij **signInType** niet `federated`is), is deze eigenschap de lokale standaard domein naam van de B2C-Tenant, bijvoorbeeld `contoso.onmicrosoft.com`. Voor sociale identiteit (waarbij **signInType** is `federated`), is de waarde de naam van de verlener, bijvoorbeeld `facebook.com`|
|issuerAssignedId|tekenreeks|Hiermee geeft u de unieke id op die door de uitgever aan de gebruiker is toegewezen. De combi natie van **verlener** en **issuerAssignedId** moet uniek zijn binnen uw Tenant. Als **signInType** is ingesteld op `emailAddress` of `userName`, vertegenwoordigt het lokale account de aanmeldings naam voor de gebruiker.<br>Wanneer **signInType** is ingesteld op: <ul><li>`emailAddress` (of begint met `emailAddress` zoals `emailAddress1`) **issuerAssignedId** moet een geldig e-mail adres zijn</li><li>`userName` (of een andere waarde), **issuerAssignedId** moet een geldig [lokaal deel zijn van een e-mail adres](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** vertegenwoordigt de unieke id van het federatieve account</li></ul>|

Voor federatieve identiteiten, afhankelijk van de ID-provider, is het **issuerAssignedId** een unieke waarde voor een bepaalde gebruiker per toepassing of ontwikkelings account. Configureer het Azure AD B2C-beleid met dezelfde toepassings-ID die eerder is toegewezen door de sociale provider of een andere toepassing binnen hetzelfde ontwikkelings account.

### <a name="password-profile-property"></a>Eigenschap voor wachtwoord profiel

Voor een lokale identiteit is de eigenschap **passwordProfile** vereist en bevat het wacht woord van de gebruiker. De eigenschap `forceChangePasswordNextSignIn` moet worden ingesteld op `false`.

Voor een federatieve (sociale) identiteit is de eigenschap **passwordProfile** niet vereist.

```JSON
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

### <a name="password-policy-property"></a>Eigenschap voor wachtwoord beleid

Het Azure AD B2C wachtwoord beleid (voor lokale accounts) is gebaseerd op het Azure Active Directory beleid voor [sterke wachtwoord sterkte](../active-directory/authentication/concept-sspr-policy.md) . Voor het beleid voor Azure AD B2C registreren of aanmelden en voor het opnieuw instellen van het wacht woord is de sterke wachtwoord sterkte vereist en wacht woorden niet verlopen.

Als de accounts die u wilt migreren, een zwakkere wachtwoord sterkte hebben dan de [sterke wachtwoord sterkte](../active-directory/authentication/concept-sspr-policy.md) die wordt afgedwongen door Azure AD B2C, kunt u de vereiste voor sterke wacht woorden uitschakelen in scenario's voor gebruikers migratie. Als u het standaard wachtwoord beleid wilt wijzigen, stelt u de eigenschap `passwordPolicies` in op `DisableStrongPassword`. U kunt de aanvraag voor het maken van een gebruiker bijvoorbeeld als volgt wijzigen:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

### <a name="extension-properties"></a>Extensie-eigenschappen

Elke klant gerichte toepassing heeft unieke vereisten voor het verzamelen van de gegevens. Uw Azure AD B2C-Tenant wordt geleverd met een ingebouwde set met gegevens die zijn opgeslagen in eigenschappen, zoals de naam, de voor-en de post code. Met Azure AD B2C kunt u de set eigenschappen die is opgeslagen in elk klant account uitbreiden. Zie [aangepaste kenmerken (gebruikers stromen)](user-flow-custom-attributes.md) en [aangepaste kenmerken (aangepast beleid)](custom-policy-custom-attributes.md)voor meer informatie over het definiëren van aangepaste kenmerken.

Microsoft Graph-API ondersteunt het maken en bijwerken van een gebruiker met extensie kenmerken. Extensie kenmerken in de Graph API worden genoemd met behulp van de Conventie `extension_ApplicationObjectID_attributename`. Bijvoorbeeld:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

## <a name="code-sample"></a>Codevoorbeeld

Dit code voorbeeld is een .NET core-console toepassing die gebruikmaakt van de [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview) om te communiceren met Microsoft Graph-API. De code laat zien hoe u de API aanroept om gebruikers programmatisch te beheren in een Azure AD B2C-Tenant.
U kunt [het voorbeeld archief](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip) downloaden, [door de opslag plaats](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) in github bladeren of de opslag plaats klonen:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Nadat u het code voorbeeld hebt verkregen, configureert u dit voor uw omgeving en bouwt u vervolgens het project:

1. Open het project in [Visual Studio](https://visualstudio.microsoft.com) of [Visual Studio code](https://code.visualstudio.com).
1. Open `src/appsettings.json`.
1. Vervang in het gedeelte `appSettings` `your-b2c-tenant` door de naam van uw Tenant en `Application (client) ID` en `Client secret` door de waarden voor de registratie van uw beheer toepassing (Zie de sectie [een beheer toepassing registreren](#register-a-management-application) in dit artikel).
1. Open een console venster in uw lokale kloon van de opslag plaats, schakel over naar de map `src` en bouw het project:
    ```console
    cd src
    dotnet build
    ```
1. Voer de toepassing uit met de opdracht `dotnet`:

```console
dotnet bin/Debug/netcoreapp3.0/b2c-ms-graph.dll
```

In de toepassing wordt een lijst weer gegeven met opdrachten die u kunt uitvoeren. U kunt bijvoorbeeld alle gebruikers ophalen, één gebruiker ophalen, een gebruiker verwijderen, het wacht woord van een gebruiker bijwerken en bulksgewijs importeren.

### <a name="code-discussion"></a>Code discussie

De voorbeeld code maakt gebruik van de [Microsoft Graph SDK](https://docs.microsoft.com/graph/sdks/sdks-overview), die is ontworpen om het bouwen van hoogwaardige, efficiënte en flexibele toepassingen te vereenvoudigen die toegang hebben tot Microsoft Graph. U hoeft dus geen directe Microsoft Graph-API te maken.

Voor elke aanvraag voor de Microsoft Graph-API is een toegangs token voor verificatie vereist. De oplossing maakt gebruik van het NuGet-pakket [micro soft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) dat een op een verificatie gebaseerd op een op een manier gebaseerde wrapper van de micro soft Authentication Library (MSAL) biedt voor gebruik met de SDK van Microsoft Graph.

De methode `RunAsync` in het _Program.cs_ -bestand:

1. Hiermee worden de toepassings instellingen van het bestand _appSettings. json_ gelezen
1. Initialiseert de authenticatie provider met [OAuth 2,0-client referenties toekenning](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) stroom. Met de client referenties toekenning stroom kan de app een toegangs token verkrijgen om de Microsoft Graph-API aan te roepen.
1. Hiermee stelt u de Microsoft Graph-serviceclient in met de verificatie provider:

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

De geïnitialiseerde *GraphServiceClient* wordt vervolgens in _UserService.cs_ gebruikt om de gebruikers beheer bewerkingen uit te voeren. U kunt bijvoorbeeld een lijst ophalen van de gebruikers accounts in de Tenant:

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

[API-aanroepen maken met behulp van de Microsoft Graph sdk's](https://docs.microsoft.com/graph/sdks/create-requests) bevat informatie over het lezen en schrijven van gegevens van Microsoft Graph, het gebruiken van `$select` om de opgehaalde eigenschappen te bepalen, aangepaste query parameters op te geven en de query parameters `$filter` en `$orderBy` te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Microsoft Graph beschik bare bewerkingen voor Azure AD B2C](microsoft-graph-operations.md)voor een volledige index van de API-bewerkingen voor Microsoft Graph die worden ondersteund voor Azure AD B2C resources.

<!-- LINK -->

[graph-objectIdentity]: https://docs.microsoft.com/graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
