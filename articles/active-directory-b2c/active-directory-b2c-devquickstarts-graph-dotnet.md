---
title: De Graph API gebruiken in Azure Active Directory B2C | Microsoft Docs
description: Het aanroepen van de Graph API voor een B2C-Tenant met behulp van een toepassings-id om het proces te automatiseren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7fcbbbfcc2192160ca852538c015a365518e448
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065947"
---
# <a name="azure-ad-b2c-use-the-azure-ad-graph-api"></a>Azure AD B2C: De Azure AD Graph API gebruiken

>[!NOTE]
> U moet de [Azure AD-Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-operations-overview) gebruiken om gebruikers in een Azure AD B2C Directory te beheren. Dit wijkt af van de Microsoft Graph-API. Klik [hier](https://blogs.msdn.microsoft.com/aadgraphteam/2016/07/08/microsoft-graph-or-azure-ad-graph/) voor meer informatie.

Azure Active Directory B2C (Azure AD B2C)-tenants moeten vaak erg groot zijn. Dit betekent dat veel veelvoorkomende Tenant beheer taken programmatisch moeten worden uitgevoerd. Een goed voorbeeld is gebruikersbeheer. Mogelijk moet u een bestaande gebruikers opslag migreren naar een B2C-Tenant. Of u wilt op de achtergrond gebruikersregistratie op uw eigen pagina hosten en gebruikersaccounts maken in uw Azure AD B2C-adreslijst. Voor dit soort taken moet u gebruikersaccounts kunnen maken, lezen, bijwerken en verwijderen. U kunt deze taken uitvoeren met behulp van de Azure AD-Graph API.

Voor B2C-tenants zijn er twee primaire modi om te communiceren met de Graph API.

* Voor interactieve, eenmalige taken moet u als Administrator account in de B2C-Tenant fungeren wanneer u de taken uitvoert. In deze modus moet een beheerder zich aanmelden met referenties voordat de beheerder aanroepen naar de Graph API kan uitvoeren.
* Voor geautomatiseerde, doorlopende taken moet u een type service account gebruiken dat u opgeeft met de benodigde bevoegdheden voor het uitvoeren van beheer taken. In azure AD kunt u dit doen door een toepassing te registreren en te verifiëren bij Azure AD. Dit wordt gedaan met behulp van een **toepassings-id** die gebruikmaakt van de [OAuth 2,0-client referenties toewijzen](../active-directory/develop/service-to-service.md). In dit geval fungeert de toepassing als zichzelf, niet als gebruiker, om de Graph API aan te roepen.

In dit artikel leert u hoe u de geautomatiseerde procedure kunt uitvoeren. U maakt een .net 4,5 `B2CGraphClient` waarmee gebruikers ruwe, voor het maken, lezen, bijwerken en verwijderen van bewerkingen worden uitgevoerd. De client heeft een Windows-opdracht regel interface (CLI) waarmee u verschillende methoden kunt aanroepen. De code wordt echter geschreven zodat deze zich in een niet-interactieve, geautomatiseerde manier bevindt.

## <a name="get-an-azure-ad-b2c-tenant"></a>Een Azure AD B2C-Tenant ophalen
Voordat u toepassingen of gebruikers kunt maken, hebt u een Azure AD B2C-Tenant nodig. Als u al een Tenant hebt, gaat u aan de [slag met Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-your-application-in-your-tenant"></a>Uw toepassing registreren in uw Tenant
Nadat u een B2C-Tenant hebt, moet u uw toepassing registreren met behulp van de [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Als u de Graph API wilt gebruiken met uw B2C-Tenant, moet u een toepassing registreren via de service voor *app-registraties* in het menu Azure Portal, **niet** in azure AD B2C's- *toepassingen* . In de volgende instructies wordt u naar het juiste menu geleid. U kunt bestaande B2C-toepassingen die u hebt geregistreerd in het menu van Azure AD B2C's- *toepassingen* niet opnieuw gebruiken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Kies uw Azure AD B2C-Tenant door uw account te selecteren in de rechter bovenhoek van de pagina.
3. Kies in het navigatie deel venster aan de linkerkant **alle services**, klik op **app-registraties**en klik op **nieuwe registratie**.
4. Volg de aanwijzingen op het scherm en maak een nieuwe toepassing.
    1. Een passende naam toevoegen
    2. Selecteer **alleen accounts in deze organisatie Directory**
    3. Selecteer **Web** als het toepassings type en geef **een aanmeldings-URL** op `https://B2CGraphAPI`(bijvoorbeeld) omdat deze niet relevant is voor dit voor beeld.
    4. Klik op registreren.
5. De toepassing wordt nu weer gegeven in de lijst met toepassingen. Klik erop om de **toepassings-id** (ook wel client-id genoemd) te verkrijgen. Kopieer de app zoals u deze nodig hebt in een latere sectie.
6. Klik in het menu instellingen op **certificaten & geheimen**.
7. Klik in de sectie **client geheimen** op **Nieuw client geheim**, geef een beschrijving op voor het geheim, selecteer een duur en klik vervolgens op **toevoegen**. Kopieer de waarde van het geheim (ook wel bekend als client geheim) voor gebruik in een latere sectie.

## <a name="configure-create-read-and-update-permissions-for-your-application"></a>Machtigingen voor maken, lezen en bijwerken configureren voor uw toepassing
Nu moet u uw toepassing zodanig configureren dat alle vereiste machtigingen voor het maken, lezen, bijwerken en verwijderen van gebruikers worden verkregen.

1. Selecteer uw toepassing in het menu app-registraties van Azure Portal.
2. Klik in het menu instellingen op **vereiste machtigingen**.
3. Klik in het menu vereiste machtigingen op **Windows Azure Active Directory**.
4. Selecteer in het menu toegang inschakelen de machtiging **Lees-en schrijf** toegang tot de Directory gegevens van **toepassings machtigingen** en klik op **Opslaan**.
5. Klik ten slotte weer in het menu vereiste machtigingen op de knop **machtigingen verlenen** .

U hebt nu een toepassing die toestemming heeft om gebruikers te maken, te lezen en bij te werken van uw B2C-Tenant.

> [!NOTE]
> Het kan enkele minuten duren voordat het verlenen van machtigingen is voltooid.
>
>

## <a name="configure-delete-or-update-password-permissions-for-your-application"></a>Machtigingen voor het verwijderen of bijwerken van wacht woorden voor uw toepassing configureren
Op dit moment bevat de machtiging *gegevens lezen en schrijven* **niet** de mogelijkheid om gebruikers te verwijderen of gebruikers wachtwoorden bij te werken. Als u uw toepassing de mogelijkheid wilt geven om gebruikers te verwijderen of wacht woorden bij te werken, moet u deze extra stappen uitvoeren die gebruikmaken van Power shell, anders kunt u door gaan naar de volgende sectie.

Eerst installeert u de [Azure AD Power shell v1-module (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)als u deze nog niet hebt geïnstalleerd:

```powershell
Install-Module MSOnline
```

Nadat u de Power shell-module hebt geïnstalleerd, maakt u verbinding met uw Azure AD B2C-Tenant.

> [!IMPORTANT]
> U moet een B2C-Tenant beheerders account gebruiken dat **lokaal** is voor de B2C-Tenant. Deze accounts zien er als volgt myusername@myb2ctenant.onmicrosoft.comuit:.

```powershell
Connect-MsolService
```

Nu gebruiken we de **toepassings-id** in het onderstaande script om de toepassing de beheerdersrol van het gebruikers account toe te wijzen. Deze rollen hebben bekende id's, dus u hoeft alleen maar uw **toepassings-id** in het onderstaande script in te voeren.

```powershell
$applicationId = "<YOUR_APPLICATION_ID>"
$sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId
Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal
```

Uw toepassing heeft nu ook machtigingen voor het verwijderen van gebruikers of het bijwerken van wacht woorden van uw B2C-Tenant.

## <a name="download-configure-and-build-the-sample-code"></a>De voorbeeld code downloaden, configureren en bouwen
Down load eerst de voorbeeld code en ontvang deze actief. Daarna gaan we hier nader op letten.  U kunt [de voorbeeld code downloaden als een zip-bestand](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). U kunt de app ook klonen naar een map van uw keuze:

```cmd
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Open de `B2CGraphClient\B2CGraphClient.sln` Visual Studio-oplossing in Visual Studio. Open het `B2CGraphClient` bestand `App.config`in het project. Vervang de drie app-instellingen door uw eigen waarden:

```xml
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Key from above}" />
</appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Klik vervolgens met de rechter muisknop op `B2CGraphClient` de oplossing en bouw het voor beeld opnieuw op. Als dat lukt, hebt u nu een `B2C.exe` uitvoerbaar bestand in. `B2CGraphClient\bin\Debug`

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Maak gebruikers ruwe bewerkingen met behulp van de Graph API
Als u de B2CGraphClient wilt gebruiken, `cmd` opent u een Windows-opdracht prompt en wijzigt `Debug` u de map in de map. Voer vervolgens de `B2C Help` opdracht uit.

```cmd
cd B2CGraphClient\bin\Debug
B2C Help
```

Hiermee wordt een korte beschrijving van elke opdracht weer gegeven. Telkens wanneer u een van deze opdrachten aanroept `B2CGraphClient` , wordt een aanvraag voor de Azure AD-Graph API.

### <a name="get-an-access-token"></a>Een toegangstoken opvragen
Voor elke aanvraag voor de Graph API is een toegangs token voor verificatie vereist. `B2CGraphClient`maakt gebruik van open source Active Directory Authentication Library (ADAL) om toegangs tokens te verkrijgen. ADAL maakt het verkrijgen van tokens eenvoudiger door een eenvoudige API te bieden en te zorgen voor een aantal belang rijke details, zoals het opslaan van toegangs tokens in de cache. U hoeft ADAL echter niet te gebruiken om tokens op te halen. U kunt ook tokens ophalen door HTTP-aanvragen te vervaardigen.

> [!NOTE]
> Dit code voorbeeld maakt gebruik van ADAL v2 om te communiceren met de Graph API.  U moet ADAL v2 of v3 gebruiken om toegang te krijgen tot tokens die kunnen worden gebruikt met de Azure AD-Graph API.
>
>

Wanneer `B2CGraphClient` wordt uitgevoerd, wordt er een instantie van `B2CGraphClient` de klasse gemaakt. De constructor voor deze klasse stelt een ADAL-verificatie steiger in:

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

De `B2C Get-User` opdracht wordt als voor beeld gebruikt. Wanneer `B2C Get-User` wordt aangeroepen zonder extra invoer, roept de CLI de `B2CGraphClient.GetAllUsers(...)` -methode aan. Deze methode roept `B2CGraphClient.SendGraphGetRequest(...)`, waarmee een HTTP GET-aanvraag naar de Graph API wordt verzonden. Voordat `B2CGraphClient.SendGraphGetRequest(...)` de GET-aanvraag wordt verzonden, haalt deze eerst een toegangs token op met behulp van ADAL:

```csharp
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

U kunt een toegangs token voor de Graph API verkrijgen door de methode ADAL `AuthenticationContext.AcquireToken(...)` aan te roepen. ADAL retourneert vervolgens een `access_token` waarde die de identiteit van de toepassing vertegenwoordigt.

### <a name="read-users"></a>Gebruikers lezen
Als u een lijst met gebruikers wilt ophalen of een bepaalde gebruiker wilt ophalen uit de Graph API, kunt u een http- `GET` aanvraag verzenden naar `/users` het eind punt. Een aanvraag voor alle gebruikers in een Tenant ziet er als volgt uit:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Als u deze aanvraag wilt bekijken, voert u de volgende opdracht uit:

 ```cmd
 B2C Get-User
 ```

Er zijn twee belang rijke dingen die u moet weten:

* Het toegangs token dat via ADAL is verkregen, wordt `Authorization` toegevoegd aan de header `Bearer` met behulp van het schema.
* Voor B2C-tenants moet u de query parameter `api-version=1.6`gebruiken.

Beide Details worden behandeld in de `B2CGraphClient.SendGraphGetRequest(...)` -methode:

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
Wanneer u gebruikers accounts maakt in uw B2C-Tenant, kunt u een http `POST` -aanvraag verzenden `/users` naar het eind punt:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",                // a value that can be used for displaying to the end user
    "mailNickname": "joec",                        // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

De meeste van deze eigenschappen in deze aanvraag zijn vereist om consumenten gebruikers te maken. Klik [hier](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)voor meer informatie. Houd er rekening `//` mee dat de opmerkingen zijn opgenomen voor de afbeelding. Neem deze niet op in een daad werkelijke aanvraag.

Als u de aanvraag wilt weer geven, voert u een van de volgende opdrachten uit:

```cmd
B2C Create-User ..\..\..\usertemplate-email.json
B2C Create-User ..\..\..\usertemplate-username.json
```

De `Create-User` opdracht neemt een JSON-bestand als invoer parameter. Dit bevat een JSON-weer gave van een gebruikers object. Er zijn twee voor beelden van json-bestanden in de voorbeeld `usertemplate-email.json` code `usertemplate-username.json`: en. U kunt deze bestanden aanpassen aan uw behoeften. Naast de hierboven genoemde velden zijn er verschillende optionele velden die u kunt gebruiken, opgenomen in deze bestanden. Meer informatie over de optionele velden vindt u in de [verwijzing naar de Azure AD Graph API-entiteit](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity).

U kunt zien hoe de POST-aanvraag is geconstrueerd in `B2CGraphClient.SendGraphPostRequest(...)`.

* Er wordt een toegangs token aan de `Authorization` header van de aanvraag gekoppeld.
* Het wordt `api-version=1.6`ingesteld.
* Het bevat het JSON-gebruikers object in de hoofd tekst van de aanvraag.

> [!NOTE]
> Als de accounts die u wilt migreren vanuit een bestaand gebruikers archief een lagere wachtwoord sterkte hebben dan de [sterke wachtwoord sterkte die door Azure AD B2C wordt afgedwongen](/previous-versions/azure/jj943764(v=azure.100)), kunt u de vereiste voor sterke wacht `DisableStrongPassword` woorden uitschakelen met de waarde in de `passwordPolicies`eigenschap. U kunt bijvoorbeeld de hierboven beschreven aanvraag voor het maken van een gebruiker als volgt `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`wijzigen:.
>
>

### <a name="update-consumer-user-accounts"></a>Gebruikers accounts van consumenten bijwerken
Wanneer u gebruikers objecten bijwerkt, is het proces vergelijkbaar met de-account die u gebruikt om gebruikers objecten te maken. Maar dit proces maakt gebruik van `PATCH` de HTTP-methode:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer"                // this request updates only the user's displayName
}
```

Probeer een gebruiker bij te werken door de JSON-bestanden bij te werken met nieuwe gegevens. U kunt vervolgens gebruiken `B2CGraphClient` om een van de volgende opdrachten uit te voeren:

```cmd
B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspecteer `B2CGraphClient.SendGraphPatchRequest(...)` de methode voor meer informatie over het verzenden van deze aanvraag.

### <a name="search-users"></a>Gebruikers zoeken
U kunt op een aantal manieren zoeken naar gebruikers in uw B2C-Tenant. Een, met behulp van de object-id van de gebruiker of twee, met behulp van de aanmelding van de `signInNames` gebruiker (bijvoorbeeld de eigenschap).

Voer een van de volgende opdrachten uit om te zoeken naar een specifieke gebruiker:

```cmd
B2C Get-User <user-object-id>
B2C Get-User <filter-query-expression>
```

Hier volgen enkele voor beelden:

```cmd
B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Gebruikers verwijderen
Het proces voor het verwijderen van een gebruiker is eenvoudig. Gebruik de http `DELETE` -methode en maak de URL met de juiste object-id:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Als u een voor beeld wilt weer geven, voert u deze opdracht in en bekijkt u de aanvraag voor verwijderen die wordt afgedrukt op de-console:

```cmd
B2C Delete-User <object-id-of-user>
```

Inspecteer `B2CGraphClient.SendGraphDeleteRequest(...)` de methode voor meer informatie over het verzenden van deze aanvraag.

U kunt naast gebruikers beheer veel andere acties uitvoeren met de Azure AD-Graph API. De [Naslag informatie voor Azure AD-Graph API](/previous-versions/azure/ad/graph/api/api-catalog) bevat details over elke actie, samen met voorbeeld aanvragen.

## <a name="use-custom-attributes"></a>Aangepaste kenmerken gebruiken
De meeste consumenten toepassingen moeten bepaalde soorten aangepaste gebruikers profiel gegevens opslaan. U kunt dit op een manier doen door een aangepast kenmerk te definiëren in uw B2C-Tenant. U kunt dat kenmerk vervolgens op dezelfde manier behandelen als elke andere eigenschap van een gebruikers object. U kunt het kenmerk bijwerken, het kenmerk verwijderen, query's uitvoeren op het kenmerk, het kenmerk verzenden als een claim in aanmeldings tokens en meer.

Als u een aangepast kenmerk wilt definiëren in uw B2C-Tenant, raadpleegt u de [verwijzing naar het aangepaste kenmerk B2C](active-directory-b2c-reference-custom-attr.md).

U kunt de aangepaste kenmerken die zijn gedefinieerd in uw B2C-Tenant `B2CGraphClient`weer geven met behulp van:

```cmd
B2C Get-B2C-Application
B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

De uitvoer van deze functies toont de details van elk aangepast kenmerk, zoals:

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

U kunt de volledige naam, zoals `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, gebruiken als eigenschap voor uw gebruikers objecten.  Werk uw JSON-bestand bij met de nieuwe eigenschap en een waarde voor de eigenschap en voer vervolgens de volgende handelingen uit:

```cmd
B2C Update-User <object-id-of-user> <path-to-json-file>
```

`B2CGraphClient`Met kunt u een service toepassing hebben waarmee uw B2C-Tenant gebruikers programmatisch kunnen worden beheerd. `B2CGraphClient`maakt gebruik van een eigen toepassings identiteit voor verificatie bij de Azure AD-Graph API. Er worden ook tokens verkregen met behulp van een client geheim. Als u deze functionaliteit in uw toepassing opneemt, moet u enkele belang rijke punten voor B2C-apps onthouden:

* U moet de toepassing de juiste machtigingen verlenen in de Tenant.
* Voor Taan moet u ADAL (niet MSAL) gebruiken om toegangs tokens te verkrijgen. (U kunt ook protocol berichten rechtstreeks verzenden zonder een bibliotheek te gebruiken.)
* Wanneer u de Graph API aanroept, `api-version=1.6`gebruikt u.
* Wanneer u consumenten gebruikers maakt en bijwerkt, zijn er enkele eigenschappen vereist, zoals hierboven wordt beschreven.

Als u vragen of aanvragen hebt voor acties die u wilt uitvoeren met behulp van de Graph API op uw B2C-Tenant, laat u een opmerking over dit artikel of een probleem in de GitHub-code voorbeeld opslagplaats.

