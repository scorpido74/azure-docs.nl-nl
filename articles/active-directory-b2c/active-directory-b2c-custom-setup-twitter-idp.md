---
title: Aanmelden met een Twitter-account instellen met behulp van aangepast beleid in Azure Active Directory B2C | Microsoft Docs
description: Stel aanmelden met een Twitter-account in met behulp van aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2ad6ac4c30572f1b475cba2f699f5842ae3c7528
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827002"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden met een Twitter-account instellen met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dit artikel laat u zien hoe u aanmelden voor gebruikers van een Twitter-account kunt inschakelen met behulp van [aangepast beleid](active-directory-b2c-overview-custom.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

- Voer de stappen in aan de [slag met aangepast beleid in azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Als u nog geen Twitter-account hebt, maakt u er een op de [aanmeldings pagina voor Twitter](https://twitter.com/signup).

## <a name="create-an-application"></a>Een toepassing maken

Als u Twitter als een id-provider in Azure AD B2C wilt gebruiken, moet u een Twitter-toepassing maken.

1. Meld u aan bij de website van de [Twitter-ontwikkel aars](https://developer.twitter.com/en/apps) met de referenties van uw Twitter-account.
2. Selecteer **een app maken**.
3. Voer een **app-naam** en een **toepassings beschrijving**in.
4. Voer`https://your-tenant.b2clogin.com`in **website-URL**in. Vervang `your-tenant` door de naam van uw Tenant. Bijvoorbeeld https://contosob2c.b2clogin.com.
5. Voer`https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`in voor de **call back-URL**. Vervang `your-tenant` door de naam van uw Tenant naam en `your-policy-Id` met de id van uw beleid. Bijvoorbeeld `b2c_1A_signup_signin_twitter`. U moet alle kleine letters gebruiken bij het invoeren van de naam van uw Tenant, zelfs als de Tenant is gedefinieerd met hoofd letters in Azure AD B2C.
6. Lees en accepteer de voor waarden aan de onderkant van de pagina en selecteer vervolgens **maken**.
7. Op de **pagina app-Details** selecteert u **Bewerken > Details bewerken**, schakelt u het selectie vakje **Aanmelden met Twitter inschakelen in**en selecteert u vervolgens **Opslaan**.
8. Selecteer **sleutels en tokens** en noteer de **CONSUMer-API-sleutel** en de geheime sleutel waarden voor de **Consumer-API** die u later wilt gebruiken.

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet de geheime sleutel opslaan die u eerder in uw Azure AD B2C-Tenant hebt vastgelegd.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies`Manual`voor **Opties**.
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `TwitterSecret`. Het voor `B2C_1A_` voegsel wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in het **geheim**uw client geheim in dat u eerder hebt vastgelegd.
9. Selecteer`Encryption`voor **sleutel gebruik**.
10. Klik op **Create**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met een Twitter-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een Twitter-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open *TrustFrameworkExtensions. XML*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

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

4. Vervang de waarde van **client_id** door de consumenten sleutel die u eerder hebt vastgelegd.
5. Sla het bestand op.

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Nu hebt u uw beleid zodanig geconfigureerd dat Azure AD B2C weet hoe u kunt communiceren met uw Twitter-account. Upload het extensie bestand van uw beleid alleen om te bevestigen dat er tot nu toe geen problemen zijn.

1. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
2. Schakel **het beleid overschrijven als dit bestaat**in en selecteer vervolgens het *TrustFrameworkExtensions. XML-* bestand.
3. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment is de ID-provider ingesteld, maar is deze niet beschikbaar in de beschik bare registratie-of aanmeldings schermen. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zo dat deze ook de Twitter-ID-provider heeft.

1. Open het bestand *TrustFrameworkBase. XML* van het Starter Pack.
2. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat `Id="SignUpOrSignIn"`bevat.
3. Open *TrustFrameworkExtensions. XML* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
5. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInTwitter`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op een registratie-of aanmeldings scherm. Als u een **ClaimsProviderSelection** -element toevoegt voor een Twitter-account, wordt een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek het **OrchestrationStep** -element dat `Order="1"` is opgenomen in de gebruikers traject die u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelects**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `TwitterExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met een Twitter-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die in `Order="2"` de gebruikers reis zijn opgenomen.
2. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij naar de id van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Twitter-OAUTH1`.

3. Sla het bestand *TrustFrameworkExtensions. XML* op en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-Tenant. In deze sectie vindt u optionele stappen die u kunt uitvoeren om een test toepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn. XML* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInTwitter. XML*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInTwitter`.
3. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld:`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de nieuwe gebruikers traject die u hebt gemaakt (SignUpSignTwitter).
5. Sla de wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Zorg ervoor dat Azure AD B2C toepassing die u hebt gemaakt, is geselecteerd in het veld **toepassing selecteren** en test deze door op **nu uitvoeren**te klikken.
