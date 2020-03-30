---
title: Aanmelden met een Google-account instellen met aangepaste beleidsregels
titleSuffix: Azure AD B2C
description: Aanmelden met een Google-account instellen in Azure Active Directory B2C met behulp van aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d02c86a1ff330aa4003299e1494a164089d8470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188219"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden met een Google-account instellen met aangepaste beleidsregels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers met een Google-account met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

- Voer de stappen uit in het aangepaste [beleid in Active Directory B2C](custom-policy-get-started.md).
- Als u nog geen Google-account hebt, maakt u er een bij [Uw Google-account maken](https://accounts.google.com/SignUp).

## <a name="register-the-application"></a>De aanvraag registreren

Als u aanmelding wilt inschakelen voor gebruikers van een Google-account, moet u een Google-toepassingsproject maken.

1. Meld u aan bij de [Google Developers Console](https://console.developers.google.com/) met uw accountreferenties.
2. Voer een **projectnaam**in, klik op **Maken**en controleer of u het nieuwe project gebruikt.
3. Selecteer **Referenties** in het linkermenu en selecteer **Referenties maken > Oauth-client-id**.
4. Selecteer **Toestemmingsscherm configureren**.
5. Selecteer of geef een geldig **e-mailadres op,** `b2clogin.com` geef een **productnaam** op die aan gebruikers wordt weergegeven, voer geautoriseerde **domeinen**in en klik op **Opslaan**.
6. Selecteer **Webtoepassing** **onder Toepassingstype**.
7. Voer een **naam** in voor uw toepassing.
8. Voer in **Geautoriseerde JavaScript-oorsprong**url's in `https://your-tenant-name.b2clogin.com` en in Geautoriseerde **omleidings-URI's**in `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Vervang uw-tenant-naam door de naam van uw huurder. U moet alle kleine letters gebruiken bij het invoeren van uw tenantnaam, zelfs als de tenant is gedefinieerd met hoofdletters in Azure AD B2C.
8. Klik **op Maken**.
9. Kopieer de waarden van **client-id** en **clientgeheim**. U hebt beide nodig om Google te configureren als identiteitsprovider in uw tenant. Clientgeheim is een belangrijke beveiligingsreferentie.

## <a name="create-a-policy-key"></a>Een beleidssleutel maken

U moet het clientgeheim opslaan dat u eerder hebt opgenomen in uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw tenant.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
5. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
6. Kies **Options**voor `Manual`Opties .
7. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `GoogleSecret`. Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in **Secret**het geheim van uw klant in dat u eerder hebt opgenomen.
9. Selecteer **voor sleutelgebruik**. `Signature`
10. Klik **op Maken**.

## <a name="add-a-claims-provider"></a>Een claimprovider toevoegen

Als u wilt dat gebruikers zich aanmelden met een Google-account, moet u het account definiëren als een claimprovider waarmee Azure AD B2C via een eindpunt kan communiceren. Het eindpunt biedt een reeks claims die worden gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U een Google-account definiëren als een claimprovider door het toe te voegen aan het element **ClaimsProviders** in het uitbreidingsbestand van uw beleid.

1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **Claimproviders.** Als het niet bestaat, voeg het onder het hoofdelement.
3. Voeg als volgt een nieuwe **ClaimProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
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

4. Stel **client_id** in op de toepassings-ID van de registratie van de toepassing.
5. Sla het bestand op.

### <a name="upload-the-extension-file-for-verification"></a>Het extensiebestand uploaden ter verificatie

U hebt uw beleid inmiddels zo geconfigureerd dat Azure AD B2C weet hoe u met uw Azure AD-map moet communiceren. Probeer het extensiebestand van uw beleid te uploaden om te bevestigen dat het tot nu toe geen problemen heeft.

1. Selecteer Op de pagina **Aangepast beleid** in uw Azure AD B2C-tenant de optie **Beleid uploaden**.
2. Het **beleid overschrijven inschakelen als het bestaat,** en blader naar en selecteer het bestand *TrustFrameworkExtensions.xml.*
3. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>Registreer de schadeprovider

Op dit moment is de identiteitsprovider ingesteld, maar deze is niet beschikbaar in een van de aanmeldings-/aanmeldingsschermen. Als u deze beschikbaar wilt maken, maakt u een duplicaat van een bestaande sjabloongebruikersreis en wijzigt u deze vervolgens zodat deze ook de Azure AD-identiteitsprovider heeft.

1. Open het bestand *TrustFrameworkBase.xml* vanuit het startpakket.
2. De volledige inhoud van het **UserJourney-element,** dat het element UserJourney bevat, zoeken en kopiëren. `Id="SignUpOrSignIn"`
3. Open het *element TrustFrameworkExtensions.xml* en zoek het element **UserJourneys.** Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **Element UserJourney** die u als onderliggend element van het element **UserJourneys** hebt gekopieerd.
5. Wijzig de naam van de id van de gebruikersreis. Bijvoorbeeld `SignUpSignInGoogle`.

### <a name="display-the-button"></a>De knop weergeven

Het **element ClaimsProviderSelection** is analoog aan een knop van identiteitsprovider op een aanmeldings-/aanmeldingsscherm. Als u een **ClaimProviderSelection-element** toevoegt voor een Google-account, wordt er een nieuwe knop weergegeven wanneer een gebruiker op de pagina landt.

1. Zoek het element **OrchestrationStep** dat is onderdeel van `Order="1"` het gebruikerstraject dat u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelects**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in `GoogleExchange`op een geschikte waarde, bijvoorbeeld:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop koppelen aan een actie

Nu je een knop hebt, moet je deze koppelen aan een actie. De actie is in dit geval dat Azure AD B2C communiceert met een Google-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die in het gebruikerstraject is meegemaakt. `Order="2"`
2. Voeg het volgende **ClaimExchange-element** toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor id die u voor **TargetClaimsExchangeId hebt**gebruikt:

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId bij** naar de ID van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Google-OAuth`.

3. Sla het *bestand TrustFrameworkExtensions.xml op* en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-tenant. In deze sectie worden optionele stappen weergegeven die u voltooien om een testtoepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het bestand van de relying party bijwerken en testen

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u hebt gemaakt.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInGoogle.xml*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** bij met een unieke waarde. Bijvoorbeeld `SignUpSignInGoogle`.
3. Werk de waarde van **PublicPolicyUri bij** met de URI voor het beleid. Bijvoorbeeld,`http://contoso.com/B2C_1A_signup_signin_google`
4. Werk de waarde van het kenmerk **ReferenceId** bij in **DefaultUserJourney** zodat deze overeenkomt met de id van het nieuwe gebruikerstraject dat u hebt gemaakt (SignUpSignGoogle).
5. Sla uw wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Controleer of de Azure AD B2C-toepassing die u hebt gemaakt, is geselecteerd in het veld **Toepassing selecteren** en test deze door nu op **Uitvoeren**te klikken .
