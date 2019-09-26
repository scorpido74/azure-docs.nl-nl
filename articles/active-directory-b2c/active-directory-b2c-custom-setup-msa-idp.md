---
title: Micro soft-account (MSA) toevoegen als een id-provider met behulp van aangepast beleid in Azure Active Directory B2C
description: Een voor beeld van het gebruik van micro soft als id-provider met het OpenID Connect Connect (OIDC)-protocol.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 854d45f8eb023436756d7a51c141f5eecab14db7
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315167"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden instellen met een Microsoft-account aangepaste beleids regels gebruiken in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel wordt beschreven hoe u aanmelden voor gebruikers vanaf een Microsoft-account inschakelt met behulp van [aangepast beleid](active-directory-b2c-overview-custom.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

- Voer de stappen in aan de [slag met aangepast beleid in azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Als u nog geen Microsoft-account hebt, maakt u er een [https://www.live.com/](https://www.live.com/)op.

## <a name="add-an-application"></a>Een toepassing toevoegen

Als u aanmelden voor gebruikers met een Microsoft-account wilt inschakelen, moet u een toepassing registreren in de Azure AD-Tenant. De Azure AD-Tenant is niet hetzelfde als uw Azure AD B2C-Tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD-Tenant bevat.
1. Kies **alle services** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **app-registraties**.
1. Selecteer **nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. Bijvoorbeeld *MSAapp1*.
1. Onder **ondersteunde account typen**selecteert u **accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** .
1. Onder **omleidings-URI (optioneel)** selecteert u `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **Web** en voert u in het tekstvak in. Vervang `your-tenant-name` door de naam van uw Azure AD B2C-Tenant.
1. Selecteer **registreren**
1. Noteer de **id van de toepassing (client)** die wordt weer gegeven op de overzichts pagina van de toepassing. U hebt deze nodig wanneer u de claim provider in een latere sectie configureert.
1. **Certificaten & geheimen** selecteren
1. Klik op **Nieuw client geheim**
1. Voer een **Beschrijving** in voor het geheim, bijvoorbeeld *MSA-toepassings client geheim*, en klik vervolgens op **toevoegen**.
1. Noteer het toepassings wachtwoord dat wordt weer gegeven in de kolom **waarde** . U gebruikt deze waarde in de volgende sectie.

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

Nu u de toepassing hebt gemaakt in uw Azure AD-Tenant, moet u het client geheim van die toepassing opslaan in uw Azure AD B2C-Tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies`Manual`voor **Opties**.
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `MSASecret`. Het voor `B2C_1A_` voegsel wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in het **geheim**het client geheim in dat u in de vorige sectie hebt vastgelegd.
1. Selecteer`Signature`voor **sleutel gebruik**.
1. Klik op **Create**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat uw gebruikers zich kunnen aanmelden met een Microsoft-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunt communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt Azure AD definiëren als een claim provider door het element **ClaimsProvider** toe te voegen aan het extensie bestand van uw beleid.

1. Open het beleids bestand *TrustFrameworkExtensions. XML* .
1. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
1. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Vervang de waarde van **client_id** door de *client-id* van de Azure AD-toepassing die u eerder hebt vastgelegd.
1. Sla het bestand op.

U hebt nu uw beleid geconfigureerd zodat Azure AD B2C weet hoe u kunt communiceren met uw Microsoft-account toepassing in azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Voordat u doorgaat, moet u het gewijzigde beleid uploaden om te bevestigen dat het geen problemen tot nu toe heeft voorgedaan.

1. Navigeer naar uw Azure AD B2C-Tenant in de Azure Portal en selecteer **identiteits ervaring-Framework**.
1. Op de pagina **aangepast beleid** selecteert u **aangepast beleid uploaden**.
1. Schakel **het beleid overschrijven als dit bestaat**in en selecteer vervolgens het *TrustFrameworkExtensions. XML-* bestand.
1. Klik op **Uploaden**.

Als er geen fouten worden weer gegeven in de portal, gaat u verder met de volgende sectie.

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment hebt u de ID-provider ingesteld, maar deze is nog niet beschikbaar in een van de registratie-en aanmeld schermen. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zodat deze ook de Microsoft-account-ID-provider heeft.

1. Open het bestand *TrustFrameworkBase. XML* van het Starter Pack.
1. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat `Id="SignUpOrSignIn"`bevat.
1. Open *TrustFrameworkExtensions. XML* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
1. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
1. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInMSA`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op een registratie-of aanmeldings scherm. Als u een **ClaimsProviderSelection** -element toevoegt voor een Microsoft-account, wordt er een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek in het bestand *TrustFrameworkExtensions. XML* het element **OrchestrationStep** op dat is `Order="1"` opgenomen in de gebruikers traject die u hebt gemaakt.
1. Voeg onder **ClaimsProviderSelects**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met een Microsoft-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die in `Order="2"` de gebruikers reis zijn opgenomen.
1. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij zodat deze overeenkomt `Id` met de waarde in het **TechnicalProfile** -element van de claim provider die u eerder hebt toegevoegd. Bijvoorbeeld `MSA-OIDC`.

1. Sla het bestand *TrustFrameworkExtensions. XML* op en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn. XML* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInMSA. XML*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInMSA`.
1. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld:`http://contoso.com/B2C_1A_signup_signin_msa`
1. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de gebruikers traject die u eerder hebt gemaakt (SignUpSignInMSA).
1. Sla de wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
1. Zorg ervoor dat Azure AD B2C toepassing die u hebt gemaakt in de vorige sectie (of door het volt ooien van de vereisten, bijvoorbeeld *webapp1* of *testapp1*) is geselecteerd in het veld **toepassing selecteren** en test deze door te klikken op **nu uitvoeren** .
1. Selecteer de knop **micro soft-account** en meld u aan.

    Als de aanmeldings bewerking is geslaagd, wordt u omgeleid zodat `jwt.ms` de gedecodeerde token wordt weer gegeven, vergelijkbaar met:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
