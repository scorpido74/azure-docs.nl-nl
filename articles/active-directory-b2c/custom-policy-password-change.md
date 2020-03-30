---
title: Wachtwoordwijziging configureren met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over hoe u gebruikers in staat stelt hun wachtwoord te wijzigen met behulp van aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c351f8a95110a32c53c68c5eb6095918578bc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189171"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Wachtwoordwijziging configureren met aangepaste beleidsregels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C) u gebruikers die zijn aangemeld met een lokaal account hun wachtwoord kunnen wijzigen zonder hun authenticiteit te hoeven bewijzen via e-mailverificatie. Als de sessie verloopt tegen de tijd dat de gebruiker de wachtwoordwijzigingsstroom krijgt, wordt deze gevraagd zich opnieuw aan te melden. In dit artikel ziet u hoe u wachtwoordwijziging configureert in [aangepast beleid.](custom-policy-overview.md) Het is ook mogelijk om [selfservice wachtwoordreset](user-flow-self-service-password-reset.md) voor gebruikersstromen te configureren.

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Aan de slag met aangepast beleid in Active Directory B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>De elementen toevoegen

1. Open het bestand *TrustframeworkExtensions.xml* en voeg het volgende `oldPassword` **Element ClaimType** toe met een id van het element [ClaimsSchema:](claimsschema.md)

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Een [Element ClaimProvider](claimsproviders.md) bevat het technische profiel dat de gebruiker verifieert. Voeg de volgende claimproviders toe aan het element **ClaimsProviders:**

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Vervang `IdentityExperienceFrameworkAppId` door de toepassings-id van de IdentityExperienceFramework-toepassing die u hebt gemaakt in de vereiste zelfstudie. Vervang `ProxyIdentityExperienceFrameworkAppId` door de toepassings-ID van de ProxyIdentityExperienceFramework-toepassing die u ook eerder hebt gemaakt.

3. Het element [UserJourney](userjourneys.md) definieert het pad dat de gebruiker volgt bij interactie met uw toepassing. Voeg het element **UserJourneys** toe als het niet bestaat `PasswordChange`met de **UserJourney** die is geïdentificeerd als:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Sla het beleidsbestand *TrustFrameworkExtensions.xml* op.
5. Kopieer het *bestand ProfileEdit.xml* dat u hebt gedownload met het startpakket en noem het *ProfielEditPasswordChange.xml*.
6. Open het nieuwe bestand en werk het kenmerk **PolicyId** bij met een unieke waarde. Deze waarde is de naam van uw beleid. Bijvoorbeeld *B2C_1A_profile_edit_password_change*.
7. Wijzig het kenmerk `<DefaultUserJourney>` **ReferenceId** in de id van het nieuwe gebruikerstraject dat u hebt gemaakt. *Bijvoorbeeld PasswordChange*.
8. Sla uw wijzigingen op.

U vindt het voorbeeldbeleid [hier.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)

## <a name="test-your-policy"></a>Uw beleid testen

Wanneer u uw toepassingen in Azure AD B2C test, kan het handig `https://jwt.ms` zijn om het Azure AD B2C-token terug te laten keren om de claims in de toepassing ervan te kunnen bekijken.

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Identity Experience Framework**.
5. Klik op de pagina Aangepast beleid op **Beleid uploaden**.
6. Selecteer **Het beleid overschrijven als het bestaat**en zoek en selecteer het bestand *TrustframeworkExtensions.xml.*
7. Klik op **Uploaden**.
8. Herhaal stap 5 tot en met 7 voor het bestand van de relying party, zoals *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Voer het beleid uit

1. Open het beleid dat u hebt gewijzigd. Bijvoorbeeld *B2C_1A_profile_edit_password_change*.
2. Selecteer **voor Toepassing**uw toepassing die u eerder hebt geregistreerd. Als u het token wilt `https://jwt.ms`zien, moet de URL van het **antwoord** worden weergegeven .
3. Klik op **Nu uitvoeren**. Meld u aan bij de acouunt die u eerder hebt gemaakt. U moet nu de mogelijkheid hebben om het wachtwoord te wijzigen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u de complexiteit van wachtwoorden [configureren met behulp van aangepast beleid in Azure Active Directory B2C](custom-policy-password-complexity.md).
