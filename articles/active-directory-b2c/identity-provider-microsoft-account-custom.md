---
title: Aanmelden met een Microsoft-account instellen met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Aangepaste beleidsregels gebruiken om Microsoft Account (MSA) in te schakelen als identiteitsprovider met behulp van het OpenID Connect -protocol (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188114"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelding instellen met een Microsoft-account met aangepaste beleidsregels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel ziet u hoe u aanmelding inschakelt voor gebruikers van een Microsoft-account met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

- Voer de stappen uit in [Aan de slag met aangepast beleid in Azure Active Directory B2C](custom-policy-get-started.md).
- Als u nog geen Microsoft-account hebt, [https://www.live.com/](https://www.live.com/)maakt u er een op .

## <a name="register-an-application"></a>Een toepassing registreren

Als u aanmelding wilt inschakelen voor gebruikers met een Microsoft-account, moet u een toepassing registreren binnen de Azure AD-tenant. De Azure AD-tenant is niet hetzelfde als uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD-tenant bevat.
1. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer een **naam** in voor uw toepassing. *MsAapp1*bijvoorbeeld .
1. Selecteer **accounts in een organisatiemap en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** onder Ondersteunde **accounttypen.**
1. Selecteer Onder **URI omleiden (optioneel)** **web** en voer u `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` het tekstvak in. Vervang `your-tenant-name` door de naam van uw Azure AD B2C-tenant.
1. Selecteer **Registreren**
1. Neem de **toepassings-id (client) op** die wordt weergegeven op de pagina Toepassingsoverzicht. Dit heb je nodig wanneer je de claimprovider in een later gedeelte configureert.
1. Certificaten **& geheimen selecteren**
1. Klik **op Nieuw clientgeheim**
1. Voer een **beschrijving** in voor het geheim, bijvoorbeeld *MSA Application Client Secret*, en klik op **Toevoegen**.
1. Neem het toepassingswachtwoord op dat wordt weergegeven in de kolom **Waarde.** U gebruikt deze waarde in de volgende sectie.

## <a name="configuring-optional-claims"></a>Optionele claims configureren

Als u de `family_name` claims `given_name` en claims van Azure AD wilt ophalen, u optionele claims voor uw toepassing configureren in de gebruikersinterface of toepassingslijst van Azure portal. Zie [Optionele claims voor uw Azure AD-app voor](../active-directory/develop/active-directory-optional-claims.md)meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer **app-registraties**in de sectie **Beheren** .
1. Selecteer de toepassing waarvoor u optionele claims wilt configureren in de lijst.
1. Selecteer **tokenconfiguratie (voorbeeld)** in de sectie **Beheren** .
1. Selecteer **Optionele claim toevoegen**.
1. Selecteer het tokentype dat u wilt configureren.
1. Selecteer de optionele claims die u wilt toevoegen.
1. Klik op**toevoegen**.

## <a name="create-a-policy-key"></a>Een beleidssleutel maken

Nu u de toepassing in uw Azure AD-tenant hebt gemaakt, moet u het clientgeheim van die toepassing opslaan in uw Azure AD B2C-tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies de map met uw tenant.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
1. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
1. Kies **Options**voor `Manual`Opties .
1. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `MSASecret`. Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in **Het geheim**het clientgeheim in dat u in de vorige sectie hebt opgenomen.
1. Selecteer **voor sleutelgebruik**. `Signature`
1. Klik **op Maken**.

## <a name="add-a-claims-provider"></a>Een claimprovider toevoegen

Als u uw gebruikers in staat wilt stellen zich aan te melden met een Microsoft-account, moet u het account definiëren als een claimprovider waarmee Azure AD B2C via een eindpunt kan communiceren. Het eindpunt biedt een reeks claims die worden gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U Azure AD definiëren als een claimprovider door het element **ClaimsProvider** toe te voegen aan het extensiebestand van uw beleid.

1. Open het beleidsbestand *TrustFrameworkExtensions.xml.*
1. Zoek het element **Claimproviders.** Als het niet bestaat, voeg het onder het hoofdelement.
1. Voeg als volgt een nieuwe **ClaimProvider** toe:

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
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

1. Vervang de waarde van **client_id** door de *toepassings-id (Client)* van de Azure AD-toepassing die u eerder hebt geregistreerd.
1. Sla het bestand op.

U hebt nu uw beleid zo geconfigureerd dat Azure AD B2C weet hoe u met uw Microsoft-accounttoepassing communiceren in Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Het extensiebestand uploaden ter verificatie

Upload voordat u verdergaat met het gewijzigde beleid om te bevestigen dat het tot nu toe geen problemen heeft.

1. Navigeer naar uw Azure AD B2C-tenant in de Azure-portal en selecteer **Identity Experience Framework**.
1. Selecteer op de pagina **Aangepast beleid** de optie Aangepast **beleid uploaden**.
1. Het **beleid overschrijven inschakelen als het bestaat,** en blader naar en selecteer het bestand *TrustFrameworkExtensions.xml.*
1. Klik op **Uploaden**.

Als er geen fouten worden weergegeven in de portal, gaat u verder naar de volgende sectie.

## <a name="register-the-claims-provider"></a>Registreer de schadeprovider

Op dit moment hebt u de identiteitsprovider ingesteld, maar deze is nog niet beschikbaar in een van de aanmeldings- of aanmeldingsschermen. Als u deze beschikbaar wilt maken, maakt u een duplicaat van een bestaande gebruikersreis van een sjabloon en wijzigt u deze vervolgens zodat deze ook de Microsoft-accountidentiteitsprovider heeft.

1. Open het bestand *TrustFrameworkBase.xml* vanuit het startpakket.
1. De volledige inhoud van het **UserJourney-element,** dat het element UserJourney bevat, zoeken en kopiëren. `Id="SignUpOrSignIn"`
1. Open het *element TrustFrameworkExtensions.xml* en zoek het element **UserJourneys.** Als het element niet bestaat, voegt u er een toe.
1. Plak de volledige inhoud van het **Element UserJourney** die u als onderliggend element van het element **UserJourneys** hebt gekopieerd.
1. Wijzig de naam van de id van de gebruikersreis. Bijvoorbeeld `SignUpSignInMSA`.

### <a name="display-the-button"></a>De knop weergeven

Het **element ClaimsProviderSelection** is analoog aan een knop van identiteitsprovider op een aanmeldings- of aanmeldingsscherm. Als u een **ClaimProviderSelection-element** toevoegt voor een Microsoft-account, wordt een nieuwe knop weergegeven wanneer een gebruiker op de pagina landt.

1. Zoek in het bestand *TrustFrameworkExtensions.xml* het element `Order="1"` **OrchestrationStep** dat is opgenomen in het gebruikerstraject dat u hebt gemaakt.
1. Voeg onder **ClaimsProviderSelects**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in `MicrosoftAccountExchange`op een geschikte waarde, bijvoorbeeld:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop koppelen aan een actie

Nu je een knop hebt, moet je deze koppelen aan een actie. De actie is in dit geval dat Azure AD B2C communiceert met een Microsoft-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die in het gebruikerstraject is meegemaakt. `Order="2"`
1. Voeg het volgende **Element ClaimsExchange** toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId:**

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** `Id` bij aan die van de waarde in het **TechnicalProfile-element** van de claimprovider die u eerder hebt toegevoegd. Bijvoorbeeld `MSA-OIDC`.

1. Sla het *bestand TrustFrameworkExtensions.xml op* en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-tenant. In deze sectie worden optionele stappen weergegeven die u voltooien om een testtoepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het bestand van de relying party bijwerken en testen

Werk het RP-bestand (relying party) bij waarmee u de gebruikersreis initieert die u hebt gemaakt.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInMSA.xml*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** bij met een unieke waarde. Bijvoorbeeld `SignUpSignInMSA`.
1. Werk de waarde van **PublicPolicyUri bij** met de URI voor het beleid. Bijvoorbeeld,`http://contoso.com/B2C_1A_signup_signin_msa`
1. Werk de waarde van het **kenmerk ReferenceId** bij in **DefaultUserJourney** zodat deze overeenkomt met de id van het gebruikerstraject dat u eerder hebt gemaakt (SignUpSignInMSA).
1. Sla uw wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
1. Controleer of de Azure AD B2C-toepassing die u in de vorige sectie hebt gemaakt (of door de vereisten in te vullen, bijvoorbeeld *webapp1* of *testapp1)* is geselecteerd in het veld **Toepassing selecteren** en test deze door nu op **Uitvoeren**te klikken .
1. Selecteer de knop **Microsoft-account** en meld u aan.

    Als de aanmeldingsbewerking is geslaagd, wordt `jwt.ms` u omgeleid naar welke degecodeerde token wordt weergegeven, vergelijkbaar met:

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
