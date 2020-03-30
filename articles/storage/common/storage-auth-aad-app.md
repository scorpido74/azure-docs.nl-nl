---
title: Een token van Azure AD aanschaffen voor het toestaan van aanvragen van een clienttoepassing
titleSuffix: Azure Storage
description: Gebruik Azure Active Directory om te verifiëren vanuit een clienttoepassing, een OAuth 2.0-token te verkrijgen en aanvragen voor Azure Blob-opslag en wachtrijopslag te autoriseren.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268481"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Een token van Azure AD aanschaffen voor het toestaan van aanvragen van een clienttoepassing

Een belangrijk voordeel van het gebruik van Azure Active Directory (Azure AD) met Azure Blob-opslag of wachtrijopslag is dat uw referenties niet langer in uw code hoeven te worden opgeslagen. In plaats daarvan u een OAuth 2.0-toegangstoken aanvragen bij het Microsoft-identiteitsplatform (voorheen Azure AD). Azure AD verifieert de beveiligingsprincipal (een gebruiker, groep of serviceprincipal) waarop de toepassing wordt uitgevoerd. Als verificatie slaagt, retourneert Azure AD het toegangstoken naar de toepassing en kan de toepassing vervolgens het toegangstoken gebruiken om aanvragen te autoriseren voor Azure Blob-opslag of wachtrijopslag.

In dit artikel ziet u hoe u uw native toepassing of webtoepassing configureert voor verificatie met Microsoft-identiteitsplatform 2.0. Het codevoorbeeld bevat .NET, maar andere talen gebruiken een vergelijkbare benadering. Zie [Microsoft Identity Platform (v2.0) overzicht voor](../../active-directory/develop/v2-overview.md)meer informatie over Microsoft identity platform 2.0.

Zie [Toegang tot Azure Active Directory-webtoepassingen autoriseren met behulp van de OAuth 2.0-codesubsidiestroom](../../active-directory/develop/v2-oauth2-auth-code-flow.md)voor een overzicht van de oauth 2.0-codesubsidiestroom.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Een rol toewijzen aan een Azure AD-beveiligingsprincipal

Als u een beveiligingsprincipal wilt verifiëren van uw Azure Storage-toepassing, configureert u eerst RBAC-instellingen (Role-based Access Control) voor die beveiligingsprincipal. Azure Storage definieert ingebouwde RBAC-rollen die machtigingen voor containers en wachtrijen omvatten. Wanneer de RBAC-rol is toegewezen aan een beveiligingsprincipal, krijgt die beveiligingsprincipal toegang tot die bron. Zie [Toegangsrechten voor Azure Blob- en Queue-gegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Uw toepassing registreren bij een Azure AD-tenant

De eerste stap in het gebruik van Azure AD om toegang tot opslagbronnen te autoriseren, is het registreren van uw clienttoepassing met een Azure AD-tenant van de [Azure-portal.](https://portal.azure.com) Wanneer u uw clienttoepassing registreert, geeft u informatie over de toepassing aan Azure AD. Azure AD biedt vervolgens een client-id (ook wel *een toepassings-id*genoemd) die u gebruikt om uw toepassing te koppelen aan Azure AD tijdens de runtime. Zie [Hoofdobjecten voor toepassingen en services in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)voor meer informatie over de client-id.

Als u uw Azure Storage-toepassing wilt registreren, voert u de stappen uit die worden weergegeven in [Quickstart: Een toepassing registreren met het Microsoft-identiteitsplatform.](../../active-directory/develop/quickstart-configure-app-access-web-apis.md) In de volgende afbeelding worden veelvoorkomende instellingen weergegeven voor het registreren van een webtoepassing:

![Schermafbeelding van het registreren van uw opslagtoepassing bij Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Als u uw aanvraag registreert als een native toepassing, u een geldige URI opgeven voor de **Omleiding URI.** Voor native toepassingen hoeft deze waarde geen echte URL te zijn. Voor webtoepassingen moet de omleidinguri een geldige URI zijn, omdat hiermee de URL wordt opgegeven waaraan tokens worden verstrekt.

Nadat u uw aanvraag hebt geregistreerd, ziet u de toepassings-ID (of client-ID) onder **Instellingen:**

![Schermafbeelding van de client-id](./media/storage-auth-aad-app/app-registration-client-id.png)

Zie Toepassingen integreren met Azure Active [Directory](../../active-directory/develop/quickstart-v2-register-an-app.md)voor meer informatie over het registreren van een toepassing bij Azure AD.

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Uw geregistreerde app-machtigingen verlenen aan Azure Storage

Geef vervolgens uw toepassingsmachtigingen om Azure Storage API's aan te roepen. Met deze stap kan uw toepassing aanvragen voor Azure Storage autoriseren met Azure AD.

1. Selecteer **API-machtigingen weergeven**op de pagina **Overzicht** voor uw geregistreerde toepassing .
1. Selecteer in de sectie **API-machtigingen** de optie **Een machtiging toevoegen** en kies Microsoft **API's**.
1. Selecteer **Azure Storage** in de lijst met resultaten om het venster **API-machtigingen aanvragen** weer te geven.
1. Onder **Welk type machtigingen heeft uw toepassing vereist?** **Delegated permissions** Deze optie is standaard voor u geselecteerd.
1. Schakel in de sectie **Machtigingen selecteren** van het deelvenster **Api-machtigingen aanvragen** het selectievakje naast **user_impersonation**in en klik vervolgens op **Machtigingen toevoegen**.

    ![Schermafbeelding van machtigingen voor opslag](media/storage-auth-aad-app/registered-app-permissions-1.png)

In het deelvenster **API-machtigingen** wordt nu weergegeven dat uw geregistreerde Azure AD-toepassing toegang heeft tot zowel Microsoft Graph als de Azure Storage. Machtigingen worden automatisch verleend aan Microsoft Graph wanneer u uw app voor het eerst registreert bij Azure AD.

![Schermafbeelding van machtigingen voor register-apps](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Een klantgeheim maken

De toepassing heeft een geheim van de klant nodig om zijn identiteit te bewijzen bij het aanvragen van een token. Voer de volgende stappen uit om het clientgeheim toe te voegen:

1. Navigeer naar uw app-registratie in de Azure-portal.
1. Selecteer de instelling **Certificaten & geheimen.**
1. Klik **onder Klantgeheimen**op **Nieuw clientgeheim** om een nieuw geheim te maken.
1. Geef een beschrijving voor het geheim en kies het gewenste verloopinterval.
1. Kopieer onmiddellijk de waarde van het nieuwe geheim naar een veilige locatie. De volledige waarde wordt slechts één keer aan u weergegeven.

    ![Schermafbeelding van clientgeheim](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Clientbibliotheken voor tokenacquisitie

Zodra u uw toepassing hebt geregistreerd en machtigingen hebt verleend voor toegang tot gegevens in Azure Blob-opslag of wachtrijopslag, u code aan uw toepassing toevoegen om een beveiligingsprincipal te verifiëren en een OAuth 2.0-token te verkrijgen. Als u het token wilt verifiëren en aanschaffen, u een van de verificatiebibliotheken van het [Microsoft-identiteitsplatform](../../active-directory/develop/reference-v2-libraries.md) gebruiken of een andere open-sourcebibliotheek die OpenID Connect 1.0 ondersteunt. Uw toepassing kan vervolgens het toegangstoken gebruiken om een aanvraag te autoriseren tegen Azure Blob-opslag of wachtrijopslag.

Zie het gedeelte [verificatiestromen](/en-us/azure/active-directory/develop/msal-authentication-flows) van de inhoud van de [Microsoft-verificatiebibliotheek](/azure/active-directory/develop/msal-overview)voor een lijst met scenario's waarvoor tokens worden ondersteund.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Bekende waarden voor verificatie met Azure AD

Als u een beveiligingsprincipal wilt verifiëren met Azure AD, moet u enkele bekende waarden in uw code opnemen.

### <a name="azure-ad-authority"></a>Azure AD-autoriteit

Voor de openbare cloud van Microsoft is de basis-Azure AD-autoriteit als volgt, waarbij *tenant-id* uw Active Directory-tenant-id (of directory-id) is:

`https://login.microsoftonline.com/<tenant-id>/`

De tenant-id identificeert de Azure AD-tenant die moet worden gebruikt voor verificatie. Het wordt ook wel de directory-ID genoemd. Als u de tenant-id wilt ophalen, navigeert u naar de **pagina Overzicht** voor uw app-registratie in de Azure-portal en kopieert u de waarde van daaruit.

### <a name="azure-storage-resource-id"></a>Azure Storage-bron-id

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Voorbeeld van .NET-code: een blokblob maken

In het codevoorbeeld ziet u hoe u een toegangstoken van Azure AD ophalen. Het toegangstoken wordt gebruikt om de opgegeven gebruiker te verifiëren en vervolgens een verzoek om een blokblob te maken. Volg eerst de stappen die in de voorgaande secties zijn beschreven om dit voorbeeld te laten werken.

Als u het token wilt aanvragen, hebt u de volgende waarden uit de registratie van uw app nodig:

- De naam van uw Azure AD-domein. Haal deze waarde op op de **overzichtspagina** van uw Azure Active Directory.
- De tenant (of directory) ID. Haal deze waarde op op de **overzichtspagina** van uw app-registratie.
- De client (of applicatie) ID. Haal deze waarde op op de **overzichtspagina** van uw app-registratie.
- De klant omleiding URI. Haal deze waarde op uit de **verificatie-instellingen** voor uw app-registratie.
- De waarde van het geheim van de klant. Haal deze waarde op van de locatie waarnaar u deze eerder hebt gekopieerd.

### <a name="create-a-storage-account-and-container"></a>Een opslagaccount en container maken

Als u het codevoorbeeld wilt uitvoeren, maakt u een opslagaccount binnen hetzelfde abonnement als uw Azure Active Directory. Maak vervolgens een container aan binnen dat opslagaccount. De voorbeeldcode maakt een blokblob in deze container.

Wijs vervolgens expliciet de rol **Opslagblobgegevensinzender** toe aan het gebruikersaccount waaronder u de voorbeeldcode uitvoert. Zie [Toegang verlenen tot Azure blob- en wachtrijgegevens met RBAC in de Azure-portal](storage-auth-aad-rbac-portal.md)voor instructies over het toewijzen van deze rol in de Azure-portal.

> [!NOTE]
> Wanneer u een Azure Storage-account maakt, krijgt u niet automatisch machtigingen toegewezen voor toegang tot gegevens via Azure AD. U moet uzelf expliciet een RBAC-rol toewijzen voor Azure Storage. U het toewijzen op het niveau van uw abonnement, resourcegroep, opslagaccount of container of wachtrij.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Een webtoepassing maken die toegang tot Blob-opslag autoriseert met Azure AD

Wanneer uw toepassing toegang krijgt tot Azure Storage, doet deze dit namens de gebruiker, wat betekent dat blob- of wachtrijbronnen worden geopend met behulp van de machtigingen van de gebruiker die is aangemeld. Als u dit codevoorbeeld wilt proberen, hebt u een webtoepassing nodig die de gebruiker vraagt zich aan te melden met een Azure AD-identiteit. U uw eigen account maken of de voorbeeldtoepassing van Microsoft gebruiken.

Een voltooide voorbeeldwebtoepassing die een token verwerft en deze gebruikt om een blob in Azure Storage te maken, is beschikbaar op [GitHub.](https://aka.ms/aadstorage) Het controleren en uitvoeren van het voltooide voorbeeld kan handig zijn om de codevoorbeelden te begrijpen. Zie de sectie getiteld Weergave en het [voltooide voorbeeld voor](#view-and-run-the-completed-sample)instructies over het uitvoeren van het voltooide voorbeeld.

#### <a name="add-references-and-using-statements"></a>Referenties toevoegen en instructies gebruiken  

Installeer vanuit Visual Studio de Azure Storage-clientbibliotheek. Selecteer in het menu **Tools** de optie **NuGet Package Manager** en vervolgens **Package Manager Console**. Typ de volgende opdrachten in het consolevenster om de benodigde pakketten uit de Azure Storage-clientbibliotheek voor .NET te installeren:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Voeg vervolgens het volgende toe met behulp van instructies aan het HomeController.cs-bestand:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Een blokblob maken

Voeg het volgende codefragment toe om een blokblob te maken:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Als u blob- en wachtrijbewerkingen wilt autoriseren met een OAuth 2.0-token, moet u HTTPS gebruiken.

In het bovenstaande voorbeeld verwerkt de .NET-clientbibliotheek de autorisatie van de aanvraag om de blokblob te maken. Azure Storage-clientbibliotheken voor andere talen verwerken ook de autorisatie van de aanvraag voor u. Als u echter een Azure Storage-bewerking aanroept met een OAuth-token met behulp van de REST-API, moet u de aanvraag autoriseren met behulp van het OAuth-token.

Als u blob- en wachtrijservicebewerkingen wilt aanroepen met OAuth-toegangstokens, geeft u het toegangstoken door in de kop **Autorisatie** met het systeem **Drager** en geeft u een serviceversie van 2017-11-09 of hoger op, zoals in het volgende voorbeeld wordt weergegeven:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Een OAuth-token ophalen van Azure AD

Voeg vervolgens een methode toe die namens de gebruiker een token van Azure AD opvraagt. Deze methode definieert de ruimte waarvoor machtigingen moeten worden verleend. Zie Machtigingen en toestemming in het eindpunt van [het Microsoft-identiteitsplatform voor](../../active-directory/develop/v2-permissions-and-consent.md)meer informatie over machtigingen en scopes.

Gebruik de resource-id om de scope te construeren waarvoor het token moet worden aangeschaft. In het voorbeeld wordt het bereik opgebouwd met behulp `user_impersonation` van de resource-id samen met de ingebouwde scope, wat aangeeft dat het token namens de gebruiker wordt aangevraagd.

Houd er rekening mee dat u de gebruiker mogelijk een interface moet presenteren waarmee de gebruiker toestemming kan geven om het token namens de gebruiker aan te vragen. Wanneer toestemming nodig is, vangt het voorbeeld de **MsalUiRequiredException** en roept een andere methode aan om het verzoek om toestemming te vergemakkelijken:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Toestemming is het proces van een gebruiker die namens hen toestemming verleent voor een toepassing om toegang te krijgen tot beveiligde bronnen. Het Microsoft-identiteitsplatform 2.0 ondersteunt incrementele toestemming, wat betekent dat een beveiligingsprincipal in eerste instantie een minimumset machtigingen kan aanvragen en machtigingen in de loop van de tijd kan toevoegen als dat nodig is. Wanneer uw code een toegangstoken aanvraagt, geeft u het bereik op `scope` van machtigingen die uw app op een bepaald moment in de parameter nodig heeft. Zie de sectie met de titel **Incrementele en dynamische toestemming** in Waarom bijwerken naar [microsoft-identiteitsplatform (v2.0)?](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)

Met de volgende methode worden de verificatie-eigenschappen voor het aanvragen van incrementele toestemming gemaakt:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Het voltooide voorbeeld weergeven en uitvoeren

Als u de voorbeeldtoepassing wilt uitvoeren, kloont u deze eerst of downloadt u deze van [GitHub.](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal) Werk vervolgens de toepassing bij zoals beschreven in de volgende secties.

### <a name="provide-values-in-the-settings-file"></a>Waarden opgeven in het instellingenbestand

Werk vervolgens het *appsettings.json-bestand* bij met uw eigen waarden, als volgt:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>De naam van het opslagaccount en de container bijwerken

Werk in het *HomeController.cs-bestand* de URI bij die verwijst naar de blokblob om de naam van uw opslagaccount en container te gebruiken:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Impliciete subsidiestroom inschakelen

Als u het voorbeeld wilt uitvoeren, moet u mogelijk de impliciete subsidiestroom voor uw app-registratie configureren. Volg deze stappen:

1. Navigeer naar uw app-registratie in de Azure-portal.
1. Selecteer in de sectie Beheren de **instelling Verificatie.**
1. Schakel **onder Geavanceerde instellingen**in de sectie Impliciete **subsidie** de selectievakjes in om toegangstokens en ID-tokens in te schakelen, zoals in de volgende afbeelding wordt weergegeven:

    ![Schermafbeelding van het inschakelen van instellingen voor impliciete subsidiestroom](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>De poort bijwerken die wordt gebruikt door localhost

Wanneer u het voorbeeld uitvoert, moet u mogelijk de omleidingsURI bijwerken die is opgegeven in uw app-registratie om de *localhost-poort* te gebruiken die bij runtime is toegewezen. Voer de volgende stappen uit om de omleiding uri bij te werken om de toegewezen poort te gebruiken:

1. Navigeer naar uw app-registratie in de Azure-portal.
1. Selecteer in de sectie Beheren de **instelling Verificatie.**
1. Bewerk onder **URI's omleiden**de poort die overeenkomt met de poort die wordt gebruikt door de voorbeeldtoepassing, zoals in de volgende afbeelding wordt weergegeven:

    ![Schermafbeelding van omleidings-URI's voor app-registratie](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [Microsoft-identiteitsplatform voor](https://docs.microsoft.com/azure/active-directory/develop/)meer informatie over het Microsoft-identiteitsplatform.
- Zie [Toegangsrechten tot opslaggegevens beheren met RBAC](storage-auth-aad-rbac.md)voor meer informatie over RBAC-rollen voor Azure-opslag.
- Zie [Toegang tot blobs en wachtrijen verifiëren met Azure Active Directory en beheerde identiteiten voor Azure Resources voor Azure Resources](storage-auth-aad-msi.md)voor meer informatie over beheerde identiteiten voor Azure-resources.
