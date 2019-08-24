---
title: Aanmelding instellen voor een multi tenant Azure AD ID-provider met aangepaste beleids regels in Azure Active Directory B2C | Microsoft Docs
description: Voeg een multi tenant-ID-provider voor Azure AD toe met aangepaste beleids regels Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8524eb8f9a8d220964e5dd1f6f8dc6d1aaf94a6d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980725"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden voor multi tenant-Azure Active Directory instellen met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel wordt beschreven hoe u aanmelden kunt inschakelen voor gebruikers die gebruikmaken van het multi tenant-eind punt voor Azure Active Directory (Azure AD) met behulp van [aangepast beleid](active-directory-b2c-overview-custom.md) in azure AD B2C. Hierdoor kunnen gebruikers van meerdere Azure AD-tenants zich aanmelden bij Azure AD B2C zonder een technische provider voor elke Tenant te configureren. Gast leden in een van deze tenants kunnen zich echter **niet** aanmelden. Hiervoor moet u [elke Tenant afzonderlijk configureren](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com`wordt gebruikt voor de Azure AD-Tenant van de `fabrikamb2c.onmicrosoft.com` organisatie en wordt gebruikt als de Azure AD B2C Tenant in de volgende instructies.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid in azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Een toepassing registreren

Als u het aanmelden voor gebruikers van een specifieke Azure AD-organisatie wilt inschakelen, moet u een toepassing registreren in de Azure AD-Tenant van de organisatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de Directory gebruikt die de Azure AD-Tenant (contoso.com) van de organisatie bevat door te klikken op het **filter Directory en abonnement** in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **alle services** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **app-registraties**.
4. Selecteer **Nieuwe toepassing registreren**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld `Azure AD B2C App`.
6. Selecteer`Web app / API`voor het **toepassings type**.
7. Voor de **aanmeldings-URL**voert u de volgende URL in kleine letters in, waarbij `your-tenant` wordt vervangen door de naam van uw Azure AD B2C Tenant (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Klik op **Create**. Kopieer de **toepassings-id** die u later wilt gebruiken.
9. Selecteer de toepassing en selecteer vervolgens **instellingen**.
10. Selecteer **sleutels**, voer de sleutel beschrijving in, selecteer een duur en klik vervolgens op **Opslaan**. Kopieer de waarde van de sleutel die wordt weer gegeven voor later gebruik.
11. Selecteer onder **instellingen**de optie **Eigenschappen**, stel **multi-tenants** in op `Yes`en klik vervolgens op **Opslaan** .

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet de toepassings sleutel opslaan die u hebt gemaakt in uw Azure AD B2C-Tenant.

1. Zorg ervoor dat u de map met uw Azure AD B2C-tenant door te klikken op de **map- en abonnementsfilter** in het bovenste menu en de map waarin uw tenant te kiezen.
2. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
3. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
4. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
5. Kies`Manual`voor **Opties**.
6. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `ContosoAppSecret`.  Het voor `B2C_1A_` voegsel wordt automatisch toegevoegd aan de naam van uw sleutel.
7. Voer in **geheim**uw toepassings sleutel in die u eerder hebt vastgelegd.
8. Selecteer`Signature`voor **sleutel gebruik**.
9. Klik op **Create**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met behulp van Azure AD, moet u Azure AD definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt Azure AD definiëren als een claim provider door Azure AD toe te voegen aan het **ClaimsProvider** -element in het extensie bestand van uw beleid.

1. Open *TrustFrameworkExtensions. XML*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>

            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="unique_name" />
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

4. Werk onder het element **ClaimsProvider** de waarde voor **domein** bij naar een unieke waarde die kan worden gebruikt om deze te onderscheiden van andere id-providers.
5. Werk onder het element **TechnicalProfile** de waarde voor **DisplayName**bij. Deze waarde wordt weer gegeven op de knop aanmelden op het aanmeldings scherm.
6. Stel **client_id** in op de toepassings-id uit de registratie van de Azure AD mulity-Tenant-app.

### <a name="restrict-access"></a>Toegang beperken

> [!NOTE]
> Als `https://sts.windows.net` u de waarde voor **ValidTokenIssuerPrefixes** gebruikt, kunnen alle Azure AD-gebruikers zich aanmelden bij uw toepassing.

U moet de lijst met geldige token verleners bijwerken en de toegang beperken tot een specifieke lijst met gebruikers van Azure AD-tenants die zich kunnen aanmelden. Als u de waarden wilt verkrijgen, moet u de meta gegevens bekijken voor elk van de specifieke Azure AD-tenants waarvan u wilt dat gebruikers zich aanmelden. De indeling van de gegevens ziet er als volgt uit `https://login.windows.net/your-tenant/.well-known/openid-configuration`:, `your-tenant` waar is uw Azure AD-Tenant naam (contoso.com of een andere Azure AD-Tenant).

1. Open uw browser en ga naar de **meta gegevens** -URL en zoek naar het object van de **Uitgever** en kopieer de waarde ervan. Het moet er als volgt uitzien `https://sts.windows.net/tenant-id/`:.
2. Kopieer en plak de waarde voor de sleutel **ValidTokenIssuerPrefixes** . U kunt meerdere toevoegen door ze te scheiden met een komma. Een voor beeld hiervan is een opmerking in de bovenstaande voor beeld-XML.

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Nu hebt u uw beleid zodanig geconfigureerd dat Azure AD B2C weet hoe u kunt communiceren met uw Azure AD-adres lijst. Upload het extensie bestand van uw beleid alleen om te bevestigen dat er tot nu toe geen problemen zijn.

1. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
2. Schakel **het beleid overschrijven als dit bestaat**in en selecteer vervolgens het *TrustFrameworkExtensions. XML-* bestand.
3. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment is de ID-provider ingesteld, maar is deze niet beschikbaar in de schermen voor aanmelden/aanmelden. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zodat deze ook de Azure AD-ID-provider heeft.

1. Open het bestand *TrustFrameworkBase. XML* van het Starter Pack.
2. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat `Id="SignUpOrSignIn"`bevat.
3. Open *TrustFrameworkExtensions. XML* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
5. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInContoso`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op het scherm aanmelden/aanmelden. Als u een **ClaimsProviderSelection** -element toevoegt voor Azure AD, wordt er een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek het **OrchestrationStep** -element dat `Order="1"` is opgenomen in de gebruikers traject die u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelects**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met Azure AD om een token te ontvangen. Koppel de knop aan een actie door het technische profiel voor uw Azure AD-claim provider te koppelen.

1. Zoek de **OrchestrationStep** die in `Order="2"` de gebruikers reis zijn opgenomen.
2. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de **id** die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij naar de **id** van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Common-AAD`.

3. Sla het bestand *TrustFrameworkExtensions. XML* op en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u in uw Tenant maakt. In deze sectie vindt u optionele stappen die u kunt uitvoeren om een test toepassing te maken als u dit nog niet hebt gedaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt met uw Azure AD B2C-tenant door te klikken op het **Map- en abonnementsfilter** in het bovenste menu en de map te kiezen waarin uw tenant zich bevindt.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Toepassingen** en vervolgens **Toevoegen**.
5. Voer een naam in voor de toepassing, bijvoorbeeld *testapp1*.
6. Voor **Web-app/Web**-API `Yes`selecteert u en voert `https://jwt.ms` u vervolgens in voor de **antwoord-URL**.
7. Klik op **Create**.

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn. XML* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignContoso. XML*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInContoso`.
3. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld:`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de nieuwe gebruikers traject die u hebt gemaakt (SignUpSignContoso).
5. Sla de wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Zorg ervoor dat Azure AD B2C toepassing die u hebt gemaakt, is geselecteerd in het veld **toepassing selecteren** en test deze door op **nu uitvoeren**te klikken.
