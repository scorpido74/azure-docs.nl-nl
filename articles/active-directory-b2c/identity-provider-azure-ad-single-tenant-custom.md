---
title: Aanmelden met een Azure AD-account instellen met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Stel aanmelden met een Azure Active Directory-account in Azure Active Directory B2C met aangepaste beleids regels.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06ba565de8ca24c8c0baa576b74e70035384be09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388422"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden met een Azure Active Directory-account instellen met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel wordt beschreven hoe u aanmelden voor gebruikers van een Azure Active Directory-organisatie (Azure AD) inschakelt met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid in azure Active Directory B2C](custom-policy-get-started.md).


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet de toepassings sleutel opslaan die u hebt gemaakt in uw Azure AD B2C-Tenant.

1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Onder **beleids regels**selecteert u **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** `Manual` .
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `ContosoAppSecret`.  Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van de sleutel wanneer deze wordt gemaakt, zodat de verwijzing in de XML in de volgende sectie wordt *B2C_1A_ContosoAppSecret*.
1. Voer in het **geheim**uw client geheim in dat u eerder hebt vastgelegd.
1. Selecteer voor **sleutel gebruik** `Signature` .
1. Selecteer **Maken**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met behulp van Azure AD, moet u Azure AD definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt Azure AD definiëren als een claim provider door Azure AD toe te voegen aan het **ClaimsProvider** -element in het extensie bestand van uw beleid.

1. Open het *TrustFrameworkExtensions.xml* -bestand.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Werk onder het element **ClaimsProvider** de waarde voor **domein** bij naar een unieke waarde die kan worden gebruikt om deze te onderscheiden van andere id-providers. Bijvoorbeeld `Contoso`. U hoeft geen `.com` aan het einde van deze domein instelling te zetten.
5. Werk onder het element **ClaimsProvider** de waarde voor **DisplayName** bij naar een beschrijvende naam voor de claim provider. Deze waarde wordt momenteel niet gebruikt.

### <a name="update-the-technical-profile"></a>Het technische profiel bijwerken

Als u een token van het Azure AD-eind punt wilt ophalen, moet u de protocollen definiëren die Azure AD B2C moet gebruiken om te communiceren met Azure AD. Dit wordt gedaan binnen het **TechnicalProfile** -element van **ClaimsProvider**.

1. Werk de ID van het **TechnicalProfile** -element bij. Deze ID wordt gebruikt om te verwijzen naar dit technische profiel van andere onderdelen van het beleid, bijvoorbeeld `OIDC-Contoso` .
1. Werk de waarde voor **DisplayName**bij. Deze waarde wordt weer gegeven op de knop aanmelden op het aanmeldings scherm.
1. De waarde voor de **Beschrijving**bijwerken.
1. Azure AD maakt gebruik van het OpenID Connect Connect-protocol, dus zorg ervoor dat de waarde voor **protocol** is `OpenIdConnect` .
1. Stel de waarde van de **meta gegevens** in op `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , waarbij `tenant-name` uw Azure AD-Tenant naam is. Bijvoorbeeld: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Stel **client_id** van de toepassings-id in voor de registratie van de toepassing.
1. Werk onder **CryptographicKeys**de waarde van **StorageReferenceId** bij naar de naam van de beleids sleutel die u eerder hebt gemaakt. Bijvoorbeeld `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Nu hebt u uw beleid zodanig geconfigureerd dat Azure AD B2C weet hoe u kunt communiceren met uw Azure AD-adres lijst. Upload het extensie bestand van uw beleid alleen om te bevestigen dat er tot nu toe geen problemen zijn.

1. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
1. Schakel **het beleid overschrijven als dit bestaat**in en selecteer vervolgens het *TrustFrameworkExtensions.xml* bestand.
1. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>De claim provider registreren

De ID-provider is op dit moment ingesteld, maar is nog niet beschikbaar op de aanmeldings pagina's van de registratie/aanmelding. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zodat deze ook de Azure AD-ID-provider heeft:

1. Open het *TrustFrameworkBase.xml* -bestand in het Starter Pack.
1. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat bevat `Id="SignUpOrSignIn"` .
1. Open de *TrustFrameworkExtensions.xml* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
1. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
1. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInContoso`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op een registratie-en aanmeldings pagina. Als u een **ClaimsProviderSelection** -element toevoegt voor Azure AD, wordt er een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek het **OrchestrationStep** -element dat is opgenomen `Order="1"` in de gebruikers traject die u hebt gemaakt in *TrustFrameworkExtensions.xml*.
1. Voeg onder **ClaimsProviderSelections**het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met Azure AD om een token te ontvangen. Koppel de knop aan een actie door het technische profiel voor uw Azure AD-claim provider te koppelen:

1. Zoek de **OrchestrationStep** die `Order="2"` in de gebruikers reis zijn opgenomen.
1. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de **id** die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij naar de **id** van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `OIDC-Contoso`.

1. Sla het *TrustFrameworkExtensions.xml* bestand op en upload het opnieuw voor verificatie.

## <a name="create-an-azure-ad-b2c-application"></a>Een Azure AD B2C-toepassing maken

Communicatie met Azure AD B2C vindt plaats via een toepassing die u registreert in uw B2C-Tenant. In deze sectie vindt u optionele stappen die u kunt uitvoeren om een test toepassing te maken als u dit nog niet hebt gedaan.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInContoso.xml*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInContoso`.
1. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de gebruikers traject die u eerder hebt gemaakt. Bijvoorbeeld *SignUpSignInContoso*.
1. Sla de wijzigingen op en upload het bestand.
1. Selecteer onder **aangepast beleid**het nieuwe beleid in de lijst.
1. Selecteer in de vervolg keuzelijst **toepassing selecteren** de Azure AD B2C toepassing die u eerder hebt gemaakt. Bijvoorbeeld *testapp1*.
1. Kopieer het **eind punt voor nu uitvoeren** en open het in een persoonlijk browser venster, bijvoorbeeld Incognito-modus in Google Chrome of een InPrivate-venster in micro soft Edge. Als u in een persoonlijk browser venster opent, kunt u de volledige gebruikers traject testen door geen gebruik te maken van de momenteel in de cache opgeslagen Azure AD-referenties.
1. Selecteer de knop aanmelden bij Azure AD, bijvoorbeeld contoso- *werk nemer*, en voer de referenties in voor een gebruiker in uw Azure AD-organisatie-Tenant. U wordt gevraagd de toepassing te autoriseren en vervolgens gegevens voor uw profiel op te geven.

Als het aanmelden is gelukt, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat wordt geretourneerd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

Wanneer u werkt met aangepaste beleids regels, hebt u mogelijk aanvullende informatie nodig bij het oplossen van problemen met een beleid tijdens de ontwikkeling ervan.

Als u problemen wilt oplossen, kunt u het beleid tijdelijk in ' ontwikkelaars modus ' plaatsen en logboeken verzamelen met Azure-toepassing Insights. Ontdek hoe u in [Azure Active Directory B2C: Logboeken verzamelt](troubleshoot-with-application-insights.md).
