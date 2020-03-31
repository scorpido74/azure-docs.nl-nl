---
title: Aanmelden met een LinkedIn-account instellen met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Aanmelden met een LinkedIn-account instellen in Azure Active Directory B2C met behulp van aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188168"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelding instellen met een LinkedIn-account met aangepaste beleidsregels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers van een LinkedIn-account met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

- Voer de stappen uit in [Aan de slag met aangepast beleid in Azure Active Directory B2C](custom-policy-get-started.md).
- LinkedIn-account - Als je er nog geen hebt, [maak je een account aan](https://www.linkedin.com/start/join).
- LinkedIn-pagina - Je hebt een [LinkedIn-pagina](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) nodig om te koppelen aan de LinkedIn-toepassing die je in de volgende sectie maakt.

## <a name="create-an-application"></a>Een app maken

Als u LinkedIn wilt gebruiken als identiteitsprovider in Azure AD B2C, moet u een LinkedIn-toepassing maken.

### <a name="create-app"></a>App maken

1. Meld u aan bij de [LinkedIn applicatiebeheerwebsite](https://www.linkedin.com/secure/developer?newapp=) met uw LinkedIn-accountgegevens.
1. Selecteer **App maken**.
1. Voer een **app-naam**in .
1. Voer een **bedrijfsnaam** in die overeenkomt met een LinkedIn-paginanaam. Maak een LinkedIn-pagina als je er nog geen hebt.
1. (Optioneel) Voer een **URL van het privacybeleid in**. Het moet een geldige URL zijn, maar hoeft geen bereikbaar eindpunt te zijn.
1. Voer een **zakelijke e-mail**in .
1. Een afbeelding **van het app-logo** uploaden. De afbeelding van het logo moet vierkant zijn en de afmetingen moeten ten minste 100x100 pixels zijn.
1. Laat de standaardinstellingen in de sectie **Producten.**
1. Bekijk de informatie die in **juridische termen wordt**gepresenteerd . Als u akkoord gaat met de voorwaarden, schakelt u het selectievakje in.
1. Selecteer **App maken**.

### <a name="configure-auth"></a>Verificatie configureren

1. Selecteer het tabblad **Auth.**
1. Neem de **client-id op.**
1. Onthullen en opnemen van de **Client Secret**.
1. Voeg onder **OAuth 2.0-instellingen**de volgende **URL van omleiding**toe . Vervang `your-tenant` door de naam van uw huurder. Gebruik **alle kleine letters** voor de tenantnaam, zelfs als deze is gedefinieerd met hoofdletters in Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Een beleidssleutel maken

U moet het clientgeheim opslaan dat u eerder hebt opgenomen in uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw tenant.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
5. Selecteer **Beleidstoetsen** en selecteer **Toevoegen**.
6. Kies **Options**voor `Manual`Opties .
7. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `LinkedInSecret`. Het voorvoegsel *B2C_1A_* wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer **in Secret**het clientgeheim in dat u eerder hebt opgenomen.
9. Selecteer **voor sleutelgebruik**. `Signature`
10. Klik **op Maken**.

## <a name="add-a-claims-provider"></a>Een claimprovider toevoegen

Als u wilt dat gebruikers zich aanmelden met een LinkedIn-account, moet u het account definiëren als een claimprovider waarmee Azure AD B2C via een eindpunt kan communiceren. Het eindpunt biedt een reeks claims die worden gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

Definieer een LinkedIn-account als claimprovider door het toe te voegen aan het element **ClaimsProviders** in het extensiebestand van uw beleid.

1. Open het bestand *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** in uw editor. Dit bestand bevindt zich in het [startpakket voor aangepaste beleidsregels die][starter-pack] u hebt gedownload als onderdeel van een van de vereisten.
1. Zoek het element **Claimproviders.** Als het niet bestaat, voeg het onder het hoofdelement.
1. Voeg als volgt een nieuwe **ClaimProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Vervang de waarde van **client_id** door de client-id van de LinkedIn-toepassing die u eerder hebt geregistreerd.
1. Sla het bestand op.

### <a name="add-the-claims-transformations"></a>De claimstransformaties toevoegen

Het LinkedIn technische profiel vereist dat de **ExtractGivenNameFromLinkedInResponse** en **ExtractSurNameFromLinkedInResponse** claims transformaties worden toegevoegd aan de lijst van ClaimsTransformations. Als u geen **element ClaimsTransformations** hebt gedefinieerd in uw bestand, voegt u de bovenliggende XML-elementen toe zoals hieronder wordt weergegeven. De claimtransformaties hebben ook een nieuw claimtype nodig met de naam **nullStringClaim**.

Voeg het element **Bouwstenen** boven aan het bestand *TrustFrameworkExtensions.xml* toe. Zie *TrustFrameworkBase.xml* bijvoorbeeld.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Het extensiebestand uploaden ter verificatie

U hebt nu een beleid geconfigureerd zodat Azure AD B2C weet hoe u met uw LinkedIn-account moet communiceren. Probeer het extensiebestand van uw beleid te uploaden om te bevestigen dat het tot nu toe geen problemen heeft.

1. Selecteer Op de pagina **Aangepast beleid** in uw Azure AD B2C-tenant de optie **Beleid uploaden**.
2. Het **beleid overschrijven inschakelen als het bestaat,** en blader naar en selecteer het bestand *TrustFrameworkExtensions.xml.*
3. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>Registreer de schadeprovider

Op dit moment is de identiteitsprovider ingesteld, maar deze is niet beschikbaar in een van de aanmeldings- of aanmeldingsschermen. Om het beschikbaar te maken, maakt u een duplicaat van een bestaande sjabloongebruikersreis en wijzigt u deze vervolgens zodat deze ook de LinkedIn-identiteitsprovider heeft.

1. Open het bestand *TrustFrameworkBase.xml* in het startpakket.
2. De volledige inhoud van het **UserJourney-element,** dat het element UserJourney bevat, zoeken en kopiëren. `Id="SignUpOrSignIn"`
3. Open het *element TrustFrameworkExtensions.xml* en zoek het element **UserJourneys.** Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **Element UserJourney** die u als onderliggend element van het element **UserJourneys** hebt gekopieerd.
5. Wijzig de naam van de id van de gebruikersreis. Bijvoorbeeld `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>De knop weergeven

Het **element ClaimsProviderSelection** is analoog aan een knop van identiteitsprovider op een aanmeldings- of aanmeldingsscherm. Als u een **ClaimProviderSelection-element** toevoegt voor een LinkedIn-account, wordt er een nieuwe knop weergegeven wanneer een gebruiker op de pagina landt.

1. Zoek het element **OrchestrationStep** dat is onderdeel van `Order="1"` het gebruikerstraject dat u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelections**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in `LinkedInExchange`op een geschikte waarde, bijvoorbeeld:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop koppelen aan een actie

Nu je een knop hebt, moet je deze koppelen aan een actie. De actie is in dit geval dat Azure AD B2C communiceert met een LinkedIn-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die in het gebruikerstraject is meegemaakt. `Order="2"`
2. Voeg het volgende **Element ClaimsExchange** toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId bij** naar de ID van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `LinkedIn-OAUTH`.

3. Sla het *bestand TrustFrameworkExtensions.xml op* en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-tenant. In deze sectie worden optionele stappen weergegeven die u voltooien om een testtoepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het bestand van de relying party bijwerken en testen

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u hebt gemaakt.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInLinkedIn.xml*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** bij met een unieke waarde. Bijvoorbeeld `SignUpSignInLinkedIn`.
3. Werk de waarde van **PublicPolicyUri bij** met de URI voor het beleid. Bijvoorbeeld,`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Werk de waarde van het **kenmerk ReferenceId** bij in **DefaultUserJourney** zodat deze overeenkomt met de id van het nieuwe gebruikerstraject dat u hebt gemaakt (SignUpSignLinkedIn).
5. Sla uw wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Controleer of de Azure AD B2C-toepassing die u hebt gemaakt, is geselecteerd in het veld **Toepassing selecteren** en test deze door nu op **Uitvoeren**te klikken .

## <a name="migration-from-v10-to-v20"></a>Migratie van v1.0 naar v2.0

LinkedIn heeft onlangs [hun API's bijgewerkt van v1.0 naar v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Als u uw bestaande configuratie wilt migreren naar de nieuwe configuratie, gebruikt u de informatie in de volgende secties om de elementen in het technische profiel bij te werken.

### <a name="replace-items-in-the-metadata"></a>Items in de metagegevens vervangen

Werk in het bestaande **element Metagegevens** van het **TechnicalProfile**de volgende **itemelementen** bij uit:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Aan:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Items toevoegen aan de metagegevens

Voeg in de **metagegevens** van het **TechnicalProfile**de volgende **itemelementen** toe:

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>De uitvoerclaims bijwerken

Werk in de bestaande **OutputClaims** van het **TechnicalProfile**de volgende **OutputClaim-elementen** bij uit:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Aan:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Nieuwe outputclaimstransformatie-elementen toevoegen

Voeg in de **OutputClaimsTransformations** van het **TechnicalProfile**de volgende **OutputClaimsTransformation-elementen** toe:

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definieer het nieuwe claimtransformaties- en claimtype

In de laatste stap hebt u nieuwe claimstransformaties toegevoegd die moeten worden gedefinieerd. Als u de claimtransformaties wilt definiëren, voegt u deze toe aan de lijst **met claimstransformaties**. Als u geen **element ClaimsTransformations** hebt gedefinieerd in uw bestand, voegt u de bovenliggende XML-elementen toe zoals hieronder wordt weergegeven. De claimtransformaties hebben ook een nieuw claimtype nodig met de naam **nullStringClaim**.

Het element **Bouwstenen** moet boven aan het bestand worden toegevoegd. Zie het *TrustframeworkBase.xml* als voorbeeld.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Een e-mailadres verkrijgen

Als onderdeel van de LinkedIn-migratie van v1.0 naar v2.0 is een extra aanroep naar een andere API vereist om het e-mailadres te verkrijgen. Als u het e-mailadres tijdens het aanmelden wilt verkrijgen, gaat u als volgt te werk:

1. Voer de bovenstaande stappen uit om Azure AD B2C te laten reageren op LinkedIn om de gebruiker zich te laten aanmelden. Als onderdeel van de federatie ontvangt Azure AD B2C het toegangstoken voor LinkedIn.
2. Sla het LinkedIn-toegangstoken op in een claim. [Zie de instructies hier](idp-pass-through-custom.md).
3. Voeg de volgende claimprovider toe die het `/emailAddress` verzoek doet aan de API van LinkedIn. Om dit verzoek te autoriseren, heb je het LinkedIn-toegangstoken nodig.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Voeg de volgende orchestration-stap toe aan uw gebruikersreis, zodat de API-claimprovider wordt geactiveerd wanneer een gebruiker zich aanmeldt met LinkedIn. Zorg ervoor dat `Order` u het nummer op de juiste manier bijwerkt. Voeg deze stap direct na de orkestratiestap toe die het technische profiel van LinkedIn activeert.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Het verkrijgen van het e-mailadres van LinkedIn tijdens het aanmelden is optioneel. Als u ervoor kiest om de e-mail niet van LinkedIn te verkrijgen, maar er een nodig hebt tijdens het aanmelden, moet de gebruiker het e-mailadres handmatig invoeren en valideren.

Zie het [Startpakket Aangepast beleid](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)voor een volledig voorbeeld van een beleid dat gebruikmaakt van de LinkedIn-identiteitsprovider.

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
