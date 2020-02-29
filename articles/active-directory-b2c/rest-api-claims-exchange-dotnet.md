---
title: REST API claim uitwisselingen integreren in een gebruikers traject
titleSuffix: Azure AD B2C
description: Integreer REST API claim uitwisselingen in uw Azure AD B2C gebruikers traject als validatie van gebruikers invoer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5976b6ef747b27a5a04c755d47ae4383fc4b2447
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187352"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>REST API claims-uitwisselingen integreren in uw Azure AD B2C gebruikers traject als validatie van gebruikers invoer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met het Framework voor identiteits ervaring, dat Azure Active Directory B2C (Azure AD B2C) ondervindt, kunt u integreren met een REST-API in een reis van een gebruiker. In dit overzicht leert u hoe Azure AD B2C communiceert met .NET Framework-REST services (Web-API).

## <a name="introduction"></a>Inleiding

U kunt met behulp van Azure AD B2C uw eigen bedrijfs logica toevoegen aan een gebruikers reis door uw eigen REST-service aan te roepen. Met het Framework voor identiteits ervaring worden gegevens verzonden naar de REST-service in een claim verzameling met *invoer* en worden gegevens teruggestuurd vanuit de rest van een verzameling *uitvoer claims* . Met de ondersteunings integratie van de REST-service kunt u het volgende doen:

* **Gebruikers invoer valideren**: met deze actie voor komt u dat ongeldige gegevens worden bewaard in azure AD. Als de waarde van de gebruiker ongeldig is, retourneert de REST-service een fout bericht dat de gebruiker de opdracht geeft een vermelding op te geven. U kunt bijvoorbeeld controleren of het e-mail adres van de gebruiker bestaat in de data base van uw klant.
* **Invoer claims overschrijven**: als een gebruiker bijvoorbeeld in alle kleine letters of hoofd letters de voor naam invoert, kunt u de naam alleen met de eerste letter in een letter type Format teren.
* **Verrijkende gebruikers gegevens door verder te integreren met zakelijke line-of-business-toepassingen**: uw rest-service kan het e-mail adres van de gebruiker ontvangen, query's uitvoeren op de data base van de klant en het loyaliteits nummer van de gebruiker retour neren om Azure AD B2C. De retour claims kunnen worden opgeslagen in het Azure AD-account van de gebruiker, geëvalueerd in de volgende *Orchestration-stappen*of opgenomen in het toegangs token.
* **Aangepaste bedrijfs logica uitvoeren**: u kunt push meldingen verzenden, zakelijke data bases bijwerken, een gebruikers migratie proces uitvoeren, machtigingen beheren, data bases controleren en andere acties uitvoeren.

U kunt op de volgende manieren de integratie met de REST-services ontwerpen:

* **Technisch profiel voor validatie**: de aanroep van de rest-service vindt plaats binnen het technische profiel voor validatie van het opgegeven technische profiel. Het validatie-technische profiel valideert de door de gebruiker verschafte gegevens voordat de reis van de gebruiker vooruit gaat. Met het technische profiel voor validatie kunt u het volgende doen:
  * Invoer claims verzenden.
  * Valideer de invoer claims en genereren aangepaste fout berichten.
  * Uitvoer claims verzenden.

* **Claim uitwisseling**: dit ontwerp is vergelijkbaar met het technische profiel voor validatie, maar dit gebeurt in een indelings stap. Deze definitie is beperkt tot:
  * Invoer claims verzenden.
  * Uitvoer claims verzenden.

## <a name="restful-walkthrough"></a>REST-scenario

In dit scenario ontwikkelt u een .NET Framework Web-API die de gebruikers invoer valideert en een loyaliteits nummer van gebruikers biedt. Uw toepassing kan bijvoorbeeld toegang verlenen tot Platinum- *voor delen* op basis van het loyaliteits nummer.

Overzicht:

* De REST-service (.NET Framework Web-API) ontwikkelen
* De REST-service in de gebruikers reis gebruiken
* Invoer claims verzenden en lezen in uw code
* De voor naam van de gebruiker valideren
* Een loyaliteits nummer terugsturen
* Het loyaliteits nummer toevoegen aan een JSON Web Token (JWT)

## <a name="prerequisites"></a>Vereisten

Volg de stappen in het artikel aan de slag [met aangepaste beleids regels](custom-policy-get-started.md) .

## <a name="step-1-create-an-aspnet-web-api"></a>Stap 1: een ASP.NET-Web-API maken

1. Maak in Visual Studio een project door **bestand** > **Nieuw** > **project**te selecteren.
1. Selecteer in het venster **New Project** de **optie C# Visual** > **Web** > **ASP.net Web Application (.NET Framework)** .
1. Typ in het vak **naam** een naam voor de toepassing (bijvoorbeeld *contoso. AADB2C. API*) en selecteer vervolgens **OK**.

    ![Een nieuw Visual Studio-project maken in Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-create-project.png)

1. Selecteer in het venster **nieuwe ASP.NET-webtoepassing** een **Web API** -of **Azure API-app** -sjabloon.

    ![Een web-API-sjabloon in Visual Studio selecteren](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. Zorg ervoor dat verificatie is ingesteld op **geen verificatie**.
1. Selecteer **OK** om het project te maken.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Stap 2: het REST API-eind punt voorbereiden

### <a name="step-21-add-data-models"></a>Stap 2,1: gegevens modellen toevoegen

De modellen vertegenwoordigen de invoer claims en uitvoer claim gegevens in uw REST-service. Uw code leest de invoer gegevens door het invoer claim model van een JSON-teken reeks naar een C# object (uw model) te deserialiseren. De ASP.NET Web-API deserialeert het uitvoer claim model automatisch terug naar JSON en schrijft vervolgens de geserialiseerde gegevens naar de hoofd tekst van het HTTP-antwoord bericht.

Maak een model dat invoer claims vertegenwoordigt door het volgende te doen:

1. Als Solution Explorer nog niet is geopend, selecteert u > **Solution Explorer** **weer geven** .
1. Klik in Solution Explorer met de rechtermuisknop op de map **Modellen** en selecteer achtereenvolgens **Toevoegen** en **Klasse**.

    ![Menu-item klasse toevoegen geselecteerd in Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-model.png)

1. Geef de klasse een naam `InputClaimsModel`en voeg de volgende eigenschappen toe aan de klasse `InputClaimsModel`:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

1. Maak een nieuw model, `OutputClaimsModel`en voeg de volgende eigenschappen toe aan de klasse `OutputClaimsModel`:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. Maak nog een model, `B2CResponseContent`, dat u gebruikt om fout berichten over invoer validatie te genereren. Voeg de volgende eigenschappen toe aan de klasse `B2CResponseContent`, geef de ontbrekende verwijzingen op en sla het bestand op:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Stap 2,2: een controller toevoegen

In de Web-API is een _controller_ een object waarmee HTTP-aanvragen worden verwerkt. De controller retourneert uitvoer claims of, als de voor naam ongeldig is, een HTTP-fout bericht met een conflict.

1. Klik in Solution Explorer met de rechtermuisknop op de map **Controllers** en selecteer achtereenvolgens **Toevoegen** en **Controller**.

    ![Een nieuwe controller toevoegen in Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. Selecteer in het venster **steigers toevoegen** de optie **Web-API-controller-leeg**en selecteer vervolgens **toevoegen**.

    ![Web-API 2-controller selecteren-leeg in Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. Geef in het venster **controller toevoegen** de naam van de controller **IdentityController**en selecteer vervolgens **toevoegen**.

    ![De naam van de controller in Visual Studio invoeren](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Met de steigers maakt u een bestand met de naam *IdentityController.cs* in de map *controllers* .

1. Als het *IdentityController.cs* -bestand nog niet is geopend, dubbelklikt u erop en vervangt u de code in het bestand door de volgende code:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Stap 3: het project naar Azure publiceren

1. Klik in Solution Explorer met de rechter muisknop op het project **contoso. AADB2C. API** en selecteer vervolgens **publiceren**.

    ![Publiceren naar Microsoft Azure App Service met Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. Selecteer **Microsoft Azure app service**in het venster **publiceren** en selecteer vervolgens **publiceren**.

    ![Nieuwe Microsoft Azure App Service maken met Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Het venster **app service maken** wordt geopend. Hierin maakt u alle benodigde Azure-resources om de ASP.NET-Web-app in azure uit te voeren.

    > [!TIP]
    > Zie [een ASP.net-Web-app maken in azure](../app-service/app-service-web-get-started-dotnet-framework.md)voor meer informatie over het publiceren.

1. Typ in het vak **Web-app-naam** een unieke app-naam (geldige tekens zijn a-z, 0-9 en koppel tekens (-). De URL van de web-app is http://< app_name >. azurewebsites. NET, waarbij *app_name* de naam van uw web-app is. U kunt de automatisch gegenereerde naam accepteren, die uiteraard uniek is.

    ![De App Service eigenschappen configureren](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Selecteer **maken**om Azure-resources te gaan maken.
    Nadat de ASP.NET-Web-app is gemaakt, wordt deze door de wizard gepubliceerd naar Azure en wordt de app vervolgens gestart in de standaard browser.

1. Kopieer de URL van de web-app.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Stap 4: Voeg de nieuwe `loyaltyNumber` claim toe aan het schema van uw TrustFrameworkExtensions. XML-bestand

De `loyaltyNumber` claim is nog niet gedefinieerd in het schema. Voeg een definitie toe binnen het `<BuildingBlocks>`-element, dat u aan het begin van het bestand *TrustFrameworkExtensions. XML* kunt vinden.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Stap 5: een claim provider toevoegen

Elke claim provider moet een of meer technische profielen hebben, die de eind punten en protocollen bepalen die nodig zijn om te communiceren met de claim provider.

Een claim provider kan om verschillende redenen meerdere technische profielen hebben. Er kunnen bijvoorbeeld meerdere technische profielen worden gedefinieerd omdat de claim provider meerdere protocollen ondersteunt, eind punten kunnen verschillende mogelijkheden hebben of kunnen claims bevatten die een groot aantal garantie niveaus hebben. Het kan acceptabel zijn om gevoelige claims vrij te geven in één gebruikers traject, maar niet in een andere.

Het volgende XML-fragment bevat een claim provider knooppunt met twee technische profielen:

* **TechnicalProfile id = "rest-API-aanmelden"** : definieert uw rest-service.
  * `Proprietary` wordt als protocol voor een op REST-gebaseerde provider beschreven.
  * `InputClaims` definieert de claims die van Azure AD B2C naar de REST-service worden verzonden.

    In dit voor beeld wordt de inhoud van de claim `givenName` als `firstName`verzonden naar de REST-service, wordt de inhoud van de claim `surname` naar de REST-service verzonden als `lastName`en wordt `email` verzonden. Het `OutputClaims`-element definieert de claims die worden opgehaald van de REST-service terug naar Azure AD B2C.

* **TechnicalProfile id = "LocalAccountSignUpWithLogonEmail"** : voegt een technisch profiel voor validatie toe aan een bestaand technisch profiel (gedefinieerd in het basis beleid). Tijdens de registratie traject roept het technische profiel voor validatie het voor gaande technische profiel aan. Als de REST-service een HTTP-fout 409 (een conflict fout) retourneert, wordt het fout bericht voor de gebruiker weer gegeven.

Zoek het knoop punt `<ClaimsProviders>` en voeg het volgende XML-fragment toe onder het knoop punt `<ClaimsProviders>`:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>

    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

De opmerkingen hierboven `AuthenticationType` en `AllowInsecureAuthInProduction` u de wijzigingen opgeeft die u moet aanbrengen wanneer u overstapt naar een productie omgeving. Voor meer informatie over het beveiligen van uw REST-Api's voor productie raadpleegt u [beveiligde rest api's met basis verificatie](secure-rest-api-dotnet-basic-auth.md) en [beveiligde rest api's met certificaat verificatie](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Stap 6: de `loyaltyNumber` claim toevoegen aan uw Relying Party-beleids bestand zodat de claim naar uw toepassing wordt verzonden

Bewerk het bestand *SignUpOrSignIn. xml* RELYING Party (RP) en wijzig het element TechnicalProfile id = "PolicyProfile" om het volgende toe te voegen: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Nadat u de nieuwe claim hebt toegevoegd, ziet de Relying Party code er als volgt uit:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Stap 7: het beleid uploaden naar uw Tenant

1. Selecteer in de [Azure Portal](https://portal.azure.com)het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die de Azure AD B2C Tenant bevat.

1. Zoek in het Azure Portal naar en selecteer **Azure AD B2C**.

1. Selecteer een **Framework voor identiteits ervaring**.

1. Open **alle beleids regels**.

1. Selecteer **beleid uploaden**.

1. Selecteer het selectie vakje het **beleid overschrijven als dit bestaat** .

1. Upload het bestand TrustFrameworkExtensions. XML en zorg ervoor dat het validatie wordt door gegeven.

1. Herhaal de vorige stap met het bestand SignUpOrSignIn. XML.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Stap 8: het aangepaste beleid testen met behulp van nu uitvoeren

1. Selecteer **Azure AD B2C instellingen**en ga vervolgens naar **identiteits ervaring-Framework**.

    > [!NOTE]
    > Voor het **uitvoeren van nu** moet ten minste één toepassing vooraf worden geregistreerd op de Tenant. Zie het artikel Azure AD B2C [aan de slag](tutorial-create-tenant.md) of het artikel over het registreren van [toepassingen](tutorial-register-applications.md) voor meer informatie over het registreren van toepassingen.

2. Open **B2C_1A_signup_signin**, het aangepaste beleid RELYING Party (RP) dat u hebt geüpload en selecteer **nu uitvoeren**.

    ![De B2C_1A_signup_signin aangepaste beleids pagina in het Azure Portal](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-run.png)

3. Test het proces door **test** te typen in het vak **naam** .
    In Azure AD B2C wordt boven in het venster een fout bericht weer gegeven.

    ![De invoer validatie van de opgegeven naam testen op de aanmeldings pagina voor aanmelden](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-test.png)

4. In het vak **naam** typt u een naam (anders dan ' test ').
    Azure AD B2C de gebruiker aan te melden en vervolgens een loyaltyNumber naar uw toepassing te verzenden. Noteer het nummer in deze JWT.

```JSON
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

* Nadat u de walkthrough aan de [slag met aangepast beleid](custom-policy-get-started.md) hebt voltooid, wordt u aangeraden om uw scenario te bouwen met behulp van uw eigen aangepaste beleids bestanden. Voor uw referentie hebben we [voorbeeld beleids bestanden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)gegeven.

* U kunt de volledige code downloaden van een [voor beeld van een Visual Studio-oplossing voor referentie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Volgende stappen

Uw volgende taak is het beveiligen van de REST-API met behulp van basis-of verificatie van client certificaten. Raadpleeg de volgende artikelen voor meer informatie over het beveiligen van uw Api's:

* [Beveilig uw REST API met basis verificatie (gebruikers naam en wacht woord)](secure-rest-api-dotnet-basic-auth.md)
* [De REST-API beveiligen met client certificaten](secure-rest-api-dotnet-certificate-auth.md)

Zie Referentie: reactief [technisch profiel](restful-technical-profile.md)voor informatie over alle elementen die beschikbaar zijn in een onderliggend technisch profiel.
