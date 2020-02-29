---
title: Een REST-service beveiligen met behulp van HTTP-basis verificatie
titleSuffix: Azure AD B2C
description: Beveilig uw aangepaste REST API claim uitwisselingen in uw Azure AD B2C met behulp van HTTP-basis verificatie.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 505d92e33606dac327778cae179df44efbfdf853
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183853"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Beveilig uw REST-services met behulp van HTTP-basis verificatie

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In een [gerelateerde Azure AD B2C-artikel](rest-api-claims-exchange-dotnet.md)maakt u een rest-service (Web-API) die kan worden geïntegreerd met de gebruikers ritten van Azure Active Directory B2C (Azure AD B2C) zonder verificatie.

In dit artikel voegt u HTTP Basic-verificatie toe aan uw REST-service zodat alleen geverifieerde gebruikers, waaronder B2C, toegang hebben tot uw API. Met HTTP-basis verificatie stelt u de gebruikers referenties (App-ID en app-geheim) in voor uw aangepaste beleid.

Zie [Basic Authentication in ASP.net Web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication)(Engelstalig) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in de [rest API claim uitwisseling integreren in uw reis artikel van uw Azure AD B2C gebruiker](rest-api-claims-exchange-dotnet.md) .

## <a name="step-1-add-authentication-support"></a>Stap 1: ondersteuning voor verificatie toevoegen

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Stap 1,1: toepassings instellingen toevoegen aan het bestand Web. config van het project

1. Open het Visual Studio-project dat u eerder hebt gemaakt.

2. Voeg de volgende toepassings instellingen toe aan het bestand Web. config onder het element `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Maak een wacht woord en stel vervolgens de `WebApp:ClientSecret` waarde in.

    Voer de volgende Power shell-code uit om een complex wacht woord te genereren. U kunt elke wille keurige waarde gebruiken.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Stap 1,2: OWIN-bibliotheken installeren

Als u wilt beginnen, voegt u de OWIN middleware NuGet-pakketten toe aan het project met behulp van de Visual Studio Package Manager-console:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Stap 1,3: een middleware-klasse voor verificatie toevoegen

Voeg de klasse `ClientAuthMiddleware.cs` toe onder de map *App_Start* . Dit doet u als volgt:

1. Klik met de rechter muisknop op de map *App_Start* , selecteer **toevoegen**en selecteer vervolgens **klasse**.

   ![De klasse ClientAuthMiddleware.cs toevoegen in de map App_Start](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. Typ **ClientAuthMiddleware.cs**in het vak **naam** .

   ![Een nieuwe C# klasse maken in het dialoog venster Nieuw item toevoegen in Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Open het *App_Start \clientauthmiddleware.cs* -bestand en vervang de bestands inhoud door de volgende code:

    ```csharp

    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;

    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }

            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }

            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);

                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }

                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;

                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }

                return Next(environment);
            }

            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;

                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }

                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }

                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);

                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Stap 1,4: een OWIN-opstart klasse toevoegen

Voeg een OWIN-opstart klasse toe met de naam `Startup.cs` aan de API. Dit doet u als volgt:
1. Klik met de rechter muisknop op het project, selecteer > **Nieuw item** **toevoegen** en zoek vervolgens naar **OWIN**.

   ![Een OWIN-opstart klasse maken in het dialoog venster Nieuw item toevoegen in Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Open het *Startup.cs* -bestand en vervang de bestands inhoud door de volgende code:

    ```csharp
    using Microsoft.Owin;
    using Owin;

    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Stap 1,5: de identiteits-API-klasse beveiligen

Open Controllers\IdentityController.cs en voeg de label `[Authorize]` toe aan de klasse controller. Met deze tag wordt de toegang tot de controller beperkt tot gebruikers die voldoen aan de autorisatie vereiste.

![De code voor autoriseren toevoegen aan de controller](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Stap 2: publiceren naar Azure

Als u uw project wilt publiceren, klikt u in Solution Explorer met de rechter muisknop op het project **contoso. AADB2C. API** en selecteert u vervolgens **publiceren**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Stap 3: Voeg de services-App-ID en het app-geheim van de REST toe aan Azure AD B2C

Nadat de REST-service is beveiligd door de client-ID (gebruikers naam) en het geheim, moet u de referenties opslaan in uw Azure AD B2C-Tenant. Uw aangepaste beleid bevat de referenties wanneer de services van de REST worden aangeroepen.

### <a name="step-31-add-a-restful-services-client-id"></a>Stap 3,1: een client-ID van de REST-services toevoegen

1. Selecteer in uw Azure AD B2C-Tenant **B2C instellingen** > **Framework voor identiteits ervaring**.


2. Selecteer **beleids sleutels** om de sleutels weer te geven die beschikbaar zijn in uw Tenant.

3. Selecteer **Toevoegen**.

4. Voor **Opties**selecteert u **hand matig**.

5. Typ **B2cRestClientId**voor **naam**.
    De prefix *B2C_1A_* kan automatisch worden toegevoegd.

6. In het vak **geheim** voert u de app-id in die u eerder hebt gedefinieerd.

7. Selecteer voor **sleutel gebruik** **hand tekening**.

8. Selecteer **Maken**.

9. Bevestig dat u de `B2C_1A_B2cRestClientId` sleutel hebt gemaakt.

### <a name="step-32-add-a-restful-services-client-secret"></a>Stap 3,2: Voeg een onderliggend Services-client geheim toe

1. Selecteer in uw Azure AD B2C-Tenant **B2C instellingen** > **Framework voor identiteits ervaring**.

2. Selecteer **beleids sleutels** om de sleutels weer te geven die beschikbaar zijn in uw Tenant.

3. Selecteer **Toevoegen**.

4. Voor **Opties**selecteert u **hand matig**.

5. Typ **B2cRestClientSecret**voor **naam**.
    De prefix *B2C_1A_* kan automatisch worden toegevoegd.

6. Voer in het vak **geheim** het app-geheim in dat u eerder hebt gedefinieerd.

7. Selecteer voor **sleutel gebruik** **hand tekening**.

8. Selecteer **Maken**.

9. Bevestig dat u de `B2C_1A_B2cRestClientSecret` sleutel hebt gemaakt.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Stap 4: het technische profiel voor de ondersteuning van basis verificatie in uw uitbreidings beleid wijzigen

1. Open in uw werkmap het extensie beleids bestand (TrustFrameworkExtensions. XML).

2. Zoek naar het `<TechnicalProfile>` knoop punt dat `Id="REST-API-SignUp"`bevat.

3. Zoek het element `<Metadata>`.

4. Wijzig de *AuthenticationType* in *Basic*als volgt:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Voeg direct na het afsluitende `<Metadata>` element het volgende XML-fragment toe:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Nadat u het fragment hebt toegevoegd, moet uw technische profiel eruitzien zoals in de volgende XML-code:

    ![XML-elementen voor basis verificatie toevoegen aan TechnicalProfile](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Stap 5: het beleid uploaden naar uw Tenant

1. Selecteer in de [Azure Portal](https://portal.azure.com)het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die de Azure AD B2C Tenant bevat.

1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.

1. Selecteer een **Framework voor identiteits ervaring**.

1. Open **alle beleids regels**.

1. Selecteer **beleid uploaden**.

1. Selecteer het selectie vakje het **beleid overschrijven als dit bestaat** .

1. Upload het bestand *TrustFrameworkExtensions. XML* en controleer of het validatie wordt door gegeven.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Stap 6: het aangepaste beleid testen met behulp van nu uitvoeren

1. Open **Azure AD B2C-instellingen**en selecteer vervolgens **Framework voor identiteits ervaring**.

    >[!NOTE]
    >Voor het uitvoeren van nu moet ten minste één toepassing vooraf worden geregistreerd op de Tenant. Zie het artikel Azure AD B2C [aan de slag](tutorial-create-tenant.md) of het artikel over het registreren van [toepassingen](tutorial-register-applications.md) voor meer informatie over het registreren van toepassingen.

2. Open **B2C_1A_signup_signin**, het aangepaste beleid RELYING Party (RP) dat u hebt geüpload en selecteer **nu uitvoeren**.

3. Test het proces door **test** te typen in het vak **naam** .
    In Azure AD B2C wordt boven in het venster een fout bericht weer gegeven.

    ![De invoer validatie van de opgegeven naam in uw identiteits-API testen](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-test.png)

4. In het vak **naam** typt u een naam (anders dan ' test ').
    Azure AD B2C de gebruiker aan te melden en vervolgens een loyaliteits nummer naar uw toepassing te verzenden. Noteer het nummer in dit voor beeld:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>Beschrijving De volledige beleids bestanden en code downloaden

* Nadat u de walkthrough aan de [slag met aangepast beleid](custom-policy-get-started.md) hebt voltooid, wordt u aangeraden om uw scenario te bouwen met behulp van uw eigen aangepaste beleids bestanden. Voor uw referentie hebben we [voorbeeld beleids bestanden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)gegeven.
* U kunt de volledige code downloaden van een [voor beeld van een Visual Studio-oplossing voor referentie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Volgende stappen

* [Client certificaten gebruiken om de REST-API te beveiligen](secure-rest-api-dotnet-certificate-auth.md)
