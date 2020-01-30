---
title: De Graph API gebruiken in Azure Active Directory B2C
description: Gebruikers in een Azure AD B2C-Tenant beheren door de Azure AD-Graph API aan te roepen en een toepassings-id te gebruiken om het proces te automatiseren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a9e55edcb7c107a3dfa91f61aaa1fea64bc62f21
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848874"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: de Azure AD-Graph API gebruiken

Azure Active Directory B2C (Azure AD B2C)-tenants kunnen duizenden of miljoenen gebruikers hebben. Dit betekent dat veel veelvoorkomende Tenant beheer taken programmatisch moeten worden uitgevoerd. Een goed voorbeeld is gebruikersbeheer.

Mogelijk moet u een bestaande gebruikers opslag migreren naar een B2C-Tenant. Of u wilt op de achtergrond gebruikersregistratie op uw eigen pagina hosten en gebruikersaccounts maken in uw Azure AD B2C-adreslijst. Voor dit soort taken moeten gebruikers accounts kunnen worden gemaakt, gelezen, bijgewerkt en verwijderd. U kunt dergelijke taken uitvoeren met behulp van de Azure AD-Graph API.

Voor B2C-tenants zijn er twee primaire modi om te communiceren met de Graph API:

* Voor **interactieve**, eenmalige taken moet u als Administrator account in de B2C-Tenant fungeren wanneer u de taken uitvoert. In deze modus moet een beheerder zich aanmelden met referenties voordat de beheerder aanroepen naar de Graph API kan uitvoeren.
* Voor **geautomatiseerde**, doorlopende taken moet u een type service account gebruiken dat u opgeeft met de benodigde bevoegdheden voor het uitvoeren van beheer taken. In azure AD kunt u dit doen door een toepassing te registreren en te verifiëren bij Azure AD. Dit wordt gedaan met behulp van een *toepassings-id* die gebruikmaakt van de [OAuth 2,0-client referenties toewijzen](../active-directory/develop/service-to-service.md). In dit geval fungeert de toepassing als zichzelf, niet als gebruiker, om de Graph API aan te roepen.

In dit artikel leert u hoe u de geautomatiseerde use-case uitvoert. U bouwt een .NET 4,5-`B2CGraphClient` op waarmee gebruikers ruwe, lees-, update-en delete-bewerkingen (ruw) uitvoeren. De client heeft een Windows-opdracht regel interface (CLI) waarmee u verschillende methoden kunt aanroepen. De code wordt echter geschreven zodat deze zich op een niet-interactieve, geautomatiseerde manier bevindt.

## <a name="prerequisites"></a>Vereisten

Voordat u toepassingen of gebruikers kunt maken, hebt u een Azure AD B2C-Tenant nodig. Als u er nog geen hebt, [maakt u een Azure Active Directory B2C-Tenant](tutorial-create-tenant.md).

## <a name="register-an-application"></a>Een toepassing registreren

Zodra u een Azure AD B2C Tenant hebt, moet u uw beheer toepassing registreren met behulp van de [Azure Portal](https://portal.azure.com). U moet deze ook de machtigingen verlenen die nodig zijn voor het uitvoeren van beheer taken namens uw geautomatiseerde script-of beheer toepassing.

### <a name="register-application-in-azure-active-directory"></a>Toepassing registreren in Azure Active Directory

Als u de Azure AD-Graph API wilt gebruiken met uw B2C-Tenant, moet u een toepassing registreren met behulp van de Azure Active Directory registratie werk stroom van de toepassing.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>API-toegangs machtigingen toewijzen

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Client geheim maken

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

U hebt nu een toepassing die toestemming heeft om gebruikers in uw Azure AD B2C-Tenant te *maken*, te *lezen*en bij te *werken* . Ga verder met de volgende sectie om machtigingen voor het *verwijderen* van gebruikers en het *bijwerken van wacht woorden* toe te voegen.

## <a name="add-user-delete-and-password-update-permissions"></a>Machtigingen voor het verwijderen van gebruikers en het bijwerken van wacht woorden toevoegen

De machtiging voor het *lezen en schrijven van Directory gegevens* die u eerder hebt verleend, omvat **niet** de mogelijkheid om gebruikers te verwijderen of hun wacht woord bij te werken.

Als u uw toepassing de mogelijkheid wilt geven om gebruikers te verwijderen of wacht woorden bij te werken, moet u de *gebruiker* de rol van de beheerder verlenen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.
1. Selecteer onder **beheren**de optie **rollen en beheerders**.
1. Selecteer de rol **gebruikers beheerder** .
1. Selecteer **toewijzing toevoegen**.
1. Voer in het tekstvak **selecteren** de naam in van de toepassing die u eerder hebt geregistreerd, bijvoorbeeld *managementapp1*. Selecteer uw toepassing als deze wordt weer gegeven in de zoek resultaten.
1. Selecteer **Toevoegen**. Het kan enkele minuten duren voordat de machtigingen volledig zijn door gegeven.

Uw Azure AD B2C-toepassing heeft nu de extra machtigingen die vereist zijn om gebruikers te verwijderen of hun wacht woord bij te werken in uw B2C-Tenant.

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

Het code voorbeeld is een .NET-console toepassing die gebruikmaakt van de [Active Directory Authentication Library (ADAL)](../active-directory/develop/active-directory-authentication-libraries.md) om te communiceren met Azure AD-Graph API. De code laat zien hoe u de API aanroept om gebruikers programmatisch te beheren in een Azure AD B2C-Tenant.

U kunt [het voor beeld-archief](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) (\*. zip) downloaden of de GitHub-opslag plaats klonen:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Nadat u het code voorbeeld hebt verkregen, configureert u dit voor uw omgeving en bouwt u vervolgens het project:

1. Open de oplossing `B2CGraphClient\B2CGraphClient.sln` in Visual Studio.
1. Open het bestand *app. config* in het project **B2CGraphClient** .
1. Vervang de sectie `<appSettings>` door de volgende XML-code. Vervang `{your-b2c-tenant}` door de naam van uw Tenant en `{Application ID}` en `{Client secret}` met de waarden die u eerder hebt vastgelegd.

    ```xml
    <appSettings>
        <add key="b2c:Tenant" value="{your-b2c-tenant}.onmicrosoft.com" />
        <add key="b2c:ClientId" value="{Application ID}" />
        <add key="b2c:ClientSecret" value="{Client secret}" />
    </appSettings>
    ```

1. Bouw de oplossing. Klik met de rechter muisknop op de **B2CGraphClient** -oplossing in de Solution Explorer en selecteer vervolgens **oplossing opnieuw bouwen**.

Als de build is geslaagd, kunt u de `B2C.exe`-console toepassing vinden in `B2CGraphClient\bin\Debug`.

## <a name="review-the-sample-code"></a>De voorbeeldcode controleren

Als u de B2CGraphClient wilt gebruiken, opent u een opdracht prompt (`cmd.exe`) en wijzigt u de `Debug` Directory van het project. Voer vervolgens de `B2C Help` opdracht uit.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

De `B2C Help` opdracht geeft een korte beschrijving van de beschik bare subopdrachten. Telkens wanneer u een van de subopdrachten aanroept, stuurt `B2CGraphClient` een aanvraag naar de Azure AD-Graph API.

In de volgende secties wordt beschreven hoe de code van de toepassing aanroepen naar de Azure AD-Graph API.

### <a name="get-an-access-token"></a>Een toegangstoken opvragen

Elke aanvraag voor de Azure AD-Graph API vereist een toegangs token voor verificatie. `B2CGraphClient` maakt gebruik van de open-source Active Directory Authentication Library (ADAL) om toegangs tokens te verkrijgen. ADAL maakt het verkrijgen van tokens eenvoudiger door het aanbieden van een helper-API en het uitvoeren van een aantal belang rijke details zoals het opslaan van toegangs tokens in de cache. U hoeft ADAL echter niet te gebruiken om tokens op te halen. U kunt in plaats daarvan tokens ophalen door HTTP-aanvragen hand matig te gebruiken.

> [!NOTE]
> U moet ADAL v2 of hoger gebruiken om toegang te krijgen tot tokens die kunnen worden gebruikt met de Azure AD-Graph API. U kunt ADAL v1 niet gebruiken.

Wanneer `B2CGraphClient` wordt uitgevoerd, wordt er een instantie van de `B2CGraphClient`-klasse gemaakt. Met de constructor voor deze klasse wordt de ADAL-verificatie steiger ingesteld:

```csharp
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Laten we de `B2C Get-User` opdracht als voor beeld gebruiken.

Wanneer `B2C Get-User` wordt aangeroepen zonder aanvullende argumenten, roept de toepassing de `B2CGraphClient.GetAllUsers()`-methode aan. `GetAllUsers()` roept vervolgens `B2CGraphClient.SendGraphGetRequest()`aan, waarmee een HTTP GET-aanvraag naar de Azure AD-Graph API wordt verzonden. Voordat `B2CGraphClient.SendGraphGetRequest()` de GET-aanvraag verzendt, verkrijgt het eerst een toegangs token met behulp van ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);
    ...
```

U kunt een toegangs token voor de Graph API verkrijgen door de methode ADAL `AuthenticationContext.AcquireToken()` aan te roepen. ADAL retourneert vervolgens een `access_token` dat de identiteit van de toepassing vertegenwoordigt.

### <a name="read-users"></a>Gebruikers lezen

Als u een lijst met gebruikers wilt ophalen of een bepaalde gebruiker van de Azure AD-Graph API wilt ontvangen, kunt u een HTTP-`GET` aanvraag verzenden naar het eind punt `/users`. Een aanvraag voor alle gebruikers in een Tenant ziet er als volgt uit:

```HTTP
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Als u deze aanvraag wilt bekijken, voert u de volgende opdracht uit:

 ```cmd
 B2C Get-User
 ```

Er zijn twee belang rijke dingen die u moet weten:

* Het toegangs token dat is verkregen met behulp van ADAL, wordt toegevoegd aan de `Authorization`-header door gebruik te maken van het `Bearer`-schema.
* Voor B2C-tenants moet u de query parameter `api-version=1.6`gebruiken.

Beide gegevens worden verwerkt in de `B2CGraphClient.SendGraphGetRequest()` methode:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Gebruikers accounts voor consumenten maken

Wanneer u gebruikers accounts maakt in uw B2C-Tenant, kunt u een HTTP-`POST` aanvraag verzenden naar het `/users`-eind punt. De volgende HTTP-`POST` aanvraag toont een voor beeld van een gebruiker die moet worden gemaakt in de Tenant.

De meeste eigenschappen in de volgende aanvraag zijn vereist om consumenten gebruikers te maken. De `//` opmerkingen zijn opgenomen voor de illustratie. Neem deze niet op in een daad werkelijke aanvraag.

```HTTP
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                           // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",            // can be 'emailAddress' or 'userName'
            "value": "consumer@fabrikam.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Consumer User",            // a value that can be used for displaying to the end user
    "mailNickname": "cuser",                   // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false  // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

Als u de aanvraag wilt weer geven, voert u een van de volgende opdrachten uit:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

De `Create-User` opdracht neemt als invoer parameter een JSON-bestand op dat een JSON-weer gave van een gebruikers object bevat. Er zijn twee voor beelden van JSON-bestanden in het code voorbeeld: `usertemplate-email.json` en `usertemplate-username.json`. U kunt deze bestanden aanpassen aan uw behoeften. Naast de hierboven genoemde velden zijn er verschillende optionele velden opgenomen in de bestanden.

Zie voor meer informatie over verplichte en optionele velden de [verwijzing entiteit en complex type | Graph API verwijzing](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference).

U kunt zien hoe de POST-aanvraag is geconstrueerd in `B2CGraphClient.SendGraphPostRequest()`:

* Hiermee wordt een toegangs token gekoppeld aan de `Authorization`-header van de aanvraag.
* `api-version=1.6`wordt ingesteld.
* Het bevat het JSON-gebruikers object in de hoofd tekst van de aanvraag.

> [!NOTE]
> Als de accounts die u wilt migreren vanuit een bestaand gebruikers archief een lagere wachtwoord sterkte hebben dan de [sterke wachtwoord sterkte die wordt afgedwongen door Azure AD B2C](user-flow-password-complexity.md), kunt u de vereiste voor sterke wacht woorden uitschakelen met behulp van de `DisableStrongPassword` waarde in de `passwordPolicies` eigenschap. U kunt bijvoorbeeld de vorige aanvraag voor het maken van een gebruiker als volgt wijzigen: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Gebruikers accounts van consumenten bijwerken

Wanneer u gebruikers objecten bijwerkt, is het proces vergelijkbaar met het account dat u gebruikt om gebruikers objecten te maken, maar wordt de HTTP-`PATCH` methode gebruikt:

```HTTP
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"    // this request updates only the user's displayName
}
```

Probeer een gebruiker bij te werken door bepaalde waarden in uw JSON-bestanden te wijzigen en gebruik vervolgens de `B2CGraphClient` om een van de volgende opdrachten uit te voeren:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Controleer de `B2CGraphClient.SendGraphPatchRequest()` methode voor meer informatie over het verzenden van deze aanvraag.

### <a name="search-users"></a>Gebruikers zoeken

U kunt op de volgende manieren zoeken naar gebruikers in uw B2C-Tenant:

* Verwijzing naar de **object-id**van de gebruiker.
* Verwijzing naar de id van de aanmelding, de eigenschap `signInNames`.
* Verwijzing naar een van de geldige OData-para meters. Voor beeld: ' naam ', ' roep ', ' displayName ' enzovoort.

Voer een van de volgende opdrachten uit om een gebruiker te zoeken:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Bijvoorbeeld:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27consumer@fabrikam.com%27)
B2C get-user $filter=givenName%20eq%20%27John%27
B2C get-user $filter=surname%20eq%20%27Doe%27
B2C get-user $filter=displayName%20eq%20%27John%20Doe%27
```

### <a name="delete-users"></a>Gebruikers verwijderen

Als u gebruikers wilt verwijderen, gebruikt u de HTTP-`DELETE` methode en maakt u de URL met de object-ID van de gebruiker:

```HTTP
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Als u een voor beeld wilt weer geven, voert u deze opdracht in en bekijkt u de aanvraag voor verwijderen die wordt afgedrukt op de-console:

```cmd
B2C Delete-User <object-id-of-user>
```

Controleer de `B2CGraphClient.SendGraphDeleteRequest()` methode voor meer informatie over het verzenden van deze aanvraag.

U kunt naast gebruikers beheer veel andere acties uitvoeren met de Azure AD-Graph API. De [Naslag informatie voor Azure AD-Graph API](/previous-versions/azure/ad/graph/api/api-catalog) bevat details over elke actie, samen met voorbeeld aanvragen.

## <a name="use-custom-attributes"></a>Aangepaste kenmerken gebruiken

De meeste consumenten toepassingen moeten bepaalde soorten aangepaste gebruikers profiel gegevens opslaan. Een van de manieren waarop u dit kunt doen is het definiëren van een aangepast kenmerk in uw B2C-Tenant. U kunt dat kenmerk vervolgens op dezelfde manier behandelen als elke andere eigenschap van een gebruikers object. U kunt het kenmerk bijwerken, het kenmerk verwijderen, query's uitvoeren op het kenmerk, het kenmerk verzenden als een claim in aanmeldings tokens en meer.

Als u een aangepast kenmerk wilt definiëren in uw B2C-Tenant, raadpleegt u de [verwijzing naar het aangepaste kenmerk B2C](user-flow-custom-attributes.md).

U kunt de aangepaste kenmerken die zijn gedefinieerd in uw B2C-Tenant weer geven met behulp van de volgende `B2CGraphClient` opdrachten:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

De uitvoer toont de details van elk aangepast kenmerk. Bijvoorbeeld:

```json
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

U kunt de volledige naam, zoals `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, gebruiken als eigenschap voor uw gebruikers objecten. Werk uw JSON-bestand bij met de nieuwe eigenschap en een waarde voor de eigenschap en voer vervolgens de volgende handelingen uit:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

## <a name="next-steps"></a>Volgende stappen

Als u `B2CGraphClient`gebruikt, hebt u een service toepassing waarmee u de B2C-Tenant gebruikers programmatisch kunt beheren. `B2CGraphClient` gebruikt een eigen toepassings-id om te verifiëren bij de Azure AD-Graph API. Er worden ook tokens verkregen met behulp van een client geheim.

Als u deze functionaliteit in uw eigen toepassing opneemt, moet u enkele belang rijke punten onthouden voor B2C-toepassingen:

* Ken de vereiste machtigingen toe aan de toepassing in de Tenant.
* Wanneer u de Graph API aanroept, gebruikt u `api-version=1.6`.
* Wanneer u consumenten gebruikers maakt en bijwerkt, zijn er enkele eigenschappen vereist, zoals hierboven wordt beschreven.
