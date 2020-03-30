---
title: Aanmelden met een Twitter-account instellen met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Stel aanmelding in met een Twitter-account met behulp van aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5804ded875ef03d7ade4414eb8f08885634748dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051601"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden met een Twitter-account instellen met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers van een Twitter-account met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

- Voer de stappen uit in [Aan de slag met aangepast beleid in Azure Active Directory B2C](custom-policy-get-started.md).
- Als je nog geen [Twitter-account](https://twitter.com/signup)hebt, maak je er een aan meldingspagina op Twitter.

## <a name="create-an-application"></a>Een app maken

Als u Twitter wilt gebruiken als identiteitsprovider in Azure AD B2C, moet u een Twitter-toepassing maken.

1. Meld u aan bij de [website van Twitter Developers](https://developer.twitter.com/en/apps) met uw Twitter-accountreferenties.
2. Selecteer **Een app maken**.
3. Voer een **app-naam** en een **toepassingsbeschrijving in**.
4. Voer in **website-URL**. `https://your-tenant.b2clogin.com` Vervang `your-tenant` door de naam van uw huurder. Bijvoorbeeld `https://contosob2c.b2clogin.com`.
5. Voer voor de URL `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`van **Callback**in . Vervang `your-tenant` door de naam van `your-policy-Id` uw tenantnaam en door de id van uw beleid. Bijvoorbeeld `b2c_1A_signup_signin_twitter`. U moet alle kleine letters gebruiken bij het invoeren van uw tenantnaam, zelfs als de tenant is gedefinieerd met hoofdletters in Azure AD B2C.
6. Lees en accepteer de voorwaarden onder aan de pagina en selecteer **Vervolgens Maken**.
7. Selecteer op de pagina **App-details** de optie **Details bewerken > bewerken,** schakel het selectievakje Aanmelden **met Twitter inschakelen in**en selecteer **Opslaan**.
8. Selecteer **sleutels en tokens** en neem de Consumer **API-sleutel** en de **geheime sleutelwaarden voor** de Consumer API op die later moeten worden gebruikt.

## <a name="create-a-policy-key"></a>Een beleidssleutel maken

U moet de geheime sleutel opslaan die u eerder hebt opgenomen in uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw tenant.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
5. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
6. Kies **Options**voor `Manual`Opties .
7. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `TwitterSecret`. Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in **Secret**het geheim van uw klant in dat u eerder hebt opgenomen.
9. Selecteer **voor sleutelgebruik**. `Encryption`
10. Klik **op Maken**.

## <a name="add-a-claims-provider"></a>Een claimprovider toevoegen

Als u wilt dat gebruikers zich aanmelden met een Twitter-account, moet u het account definiëren als een claimprovider waarmee Azure AD B2C via een eindpunt kan communiceren. Het eindpunt biedt een reeks claims die worden gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U een Twitter-account definiëren als een claimprovider door het toe te voegen aan het element **ClaimsProviders** in het uitbreidingsbestand van uw beleid.

1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **Claimproviders.** Als het niet bestaat, voeg het onder het hoofdelement.
3. Voeg als volgt een nieuwe **ClaimProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Vervang de waarde van **client_id** door de consumentensleutel die u eerder hebt opgenomen.
5. Sla het bestand op.

### <a name="upload-the-extension-file-for-verification"></a>Het extensiebestand uploaden ter verificatie

U hebt uw beleid inmiddels zo geconfigureerd dat Azure AD B2C weet hoe u met uw Twitter-account moet communiceren. Probeer het extensiebestand van uw beleid te uploaden om te bevestigen dat het tot nu toe geen problemen heeft.

1. Selecteer Op de pagina **Aangepast beleid** in uw Azure AD B2C-tenant de optie **Beleid uploaden**.
2. Het **beleid overschrijven inschakelen als het bestaat,** en blader naar en selecteer het bestand *TrustFrameworkExtensions.xml.*
3. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>Registreer de schadeprovider

Op dit moment is de identiteitsprovider ingesteld, maar deze is niet beschikbaar in een van de aanmeldings- of aanmeldingsschermen. Om het beschikbaar te maken, maakt u een duplicaat van een bestaande sjabloongebruikersreis en wijzigt u deze vervolgens zodat deze ook de Twitter-identiteitsprovider heeft.

1. Open het bestand *TrustFrameworkBase.xml* vanuit het startpakket.
2. De volledige inhoud van het **UserJourney-element,** dat het element UserJourney bevat, zoeken en kopiëren. `Id="SignUpOrSignIn"`
3. Open het *element TrustFrameworkExtensions.xml* en zoek het element **UserJourneys.** Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **Element UserJourney** die u als onderliggend element van het element **UserJourneys** hebt gekopieerd.
5. Wijzig de naam van de id van de gebruikersreis. Bijvoorbeeld `SignUpSignInTwitter`.

### <a name="display-the-button"></a>De knop weergeven

Het **element ClaimsProviderSelection** is analoog aan een knop van identiteitsprovider op een aanmeldings- of aanmeldingsscherm. Als u een **ClaimProviderSelection-element** toevoegt voor een Twitter-account, wordt er een nieuwe knop weergegeven wanneer een gebruiker op de pagina landt.

1. Zoek het element **OrchestrationStep** dat is onderdeel van `Order="1"` het gebruikerstraject dat u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelects**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in `TwitterExchange`op een geschikte waarde, bijvoorbeeld:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop koppelen aan een actie

Nu je een knop hebt, moet je deze koppelen aan een actie. De actie is in dit geval dat Azure AD B2C communiceert met een Twitter-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die in het gebruikerstraject is meegemaakt. `Order="2"`
2. Voeg het volgende **Element ClaimsExchange** toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId bij** naar de ID van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Twitter-OAUTH1`.

3. Sla het *bestand TrustFrameworkExtensions.xml op* en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-tenant. In deze sectie worden optionele stappen weergegeven die u voltooien om een testtoepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het bestand van de relying party bijwerken en testen

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u hebt gemaakt.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInTwitter.xml*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** bij met een unieke waarde. Bijvoorbeeld `SignUpSignInTwitter`.
3. Werk de waarde van **PublicPolicyUri bij** met de URI voor het beleid. Bijvoorbeeld,`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Werk de waarde van het kenmerk **ReferenceId** bij in **DefaultUserJourney** zodat deze overeenkomt met de id van het nieuwe gebruikerstraject dat u hebt gemaakt (SignUpSignTwitter).
5. Sla uw wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Controleer of de Azure AD B2C-toepassing die u hebt gemaakt, is geselecteerd in het veld **Toepassing selecteren** en test deze door nu op **Uitvoeren**te klikken .
