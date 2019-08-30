---
title: Aangemeld blijven in Azure Active Directory B2C
description: Meer informatie over het instellen van KMSI (keep me aangemeld) in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 29cdf5e7723113b4673945bf5db3158680a44b79
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147042"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Aanmelden (KMSI) inschakelen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

U kunt de functie aangemeld blijven (KMSI) inschakelen voor gebruikers van uw web-en systeem eigen toepassingen die lokale accounts hebben in uw Azure Active Directory B2C (Azure AD B2C)-map. Deze functie verleent toegang aan gebruikers die terugkeren naar uw toepassing zonder dat ze de gebruikers naam en het wacht woord opnieuw moeten invoeren. Deze toegang wordt ingetrokken wanneer een gebruiker zich afmeldt.

Gebruikers moeten deze optie niet inschakelen op open bare computers.

![Voor beeld van aanmeldings pagina voor aanmelding met het selectie vakje aangemeld blijven](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Vereisten

Een Azure AD B2C-Tenant die is geconfigureerd voor het toestaan van aanmelden via een lokaal account. KMSI wordt niet ondersteund voor accounts van externe ID-providers.

Als u geen Tenant hebt, kunt u er een maken met behulp van [de stappen in de zelf studie: Een Azure Active Directory B2C-tenant maken](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Een inhouds definitie-element toevoegen

Voeg onder het element BuildingBlocks van uw extensie bestand een **ContentDefinitions** -element toe.

1. Voeg onder het element **ContentDefinitions** een **ContentDefinition** -element toe met de id `api.signuporsigninwithkmsi`.
2. Voeg onder het element **ContentDefinition** de elementen **LoadUri**, **RecoveryUri**en **DataUri** toe. De `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` waarde van het element **DataUri** is een door de machine begrijpelijke id waarmee een KMSI-selectie vakje wordt geopend op de aanmeldings pagina's. Deze waarde mag niet worden gewijzigd.

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Een claim provider voor aanmelden toevoegen voor een lokaal account

U kunt aanmelden als een claim provider met behulp van het **ClaimsProvider** -element in het extensie bestand van uw beleid:

1. Open het bestand *TrustFrameworkExtensions. XML* in de werkmap.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element. Het [eerste pakket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) bevat een claim provider voor het aanmelden van een lokaal account.
3. Voeg een **ClaimsProvider** -element toe met de **DisplayName** en **TechnicalProfile** , zoals wordt weer gegeven in het volgende voor beeld:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>De toepassings-id's toevoegen aan uw aangepaste beleid

Voeg de toepassings-id's toe aan het bestand *TrustFrameworkExtensions. XML* .

1. Zoek in het bestand *TrustFrameworkExtensions. XML* het element **TechnicalProfile** met `login-NonInteractive` de id van en het **TechnicalProfile** -element met `login-NonInteractive-PasswordChange` de id en vervang alle waarden van `IdentityExperienceFrameworkAppId` met de toepassings-id van de Framework-toepassing voor identiteits ervaring zoals beschreven in [aan de slag](active-directory-b2c-get-started-custom.md).

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Vervang alle waarden van `ProxyIdentityExperienceFrameworkAppId` door de toepassings-id van de Framework-toepassing voor proxy identiteiten, zoals beschreven in [aan de slag](active-directory-b2c-get-started-custom.md).
3. Sla het bestand met extensies op.

## <a name="create-a-kmsi-enabled-user-journey"></a>Een door KMSI ingeschakelde gebruikers reis maken

Voeg de claim provider voor aanmelden voor een lokaal account toe aan uw gebruikers traject.

1. Open het basis bestand van uw beleid. Bijvoorbeeld *TrustFrameworkBase. XML*.
2. Zoek het element **UserJourneys** en kopieer de volledige inhoud van het **UserJourney** -element dat gebruikmaakt van de `SignUpOrSignIn`id van.
3. Open het extensie bestand. Bijvoorbeeld *TrustFrameworkExtensions. XML* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Plak het hele **UserJourney** -element dat u hebt gekopieerd als onderliggend item van het element **UserJourneys** .
5. Wijzig de waarde van de id voor de nieuwe gebruikers traject. Bijvoorbeeld `SignUpOrSignInWithKmsi`.
6. Ten slotte wijzigt u `api.signuporsigninwithkmsi`in de eerste Orchestration-stap de waarde van **ContentDefinitionReferenceId** in. Met de instelling van deze waarde wordt het selectie vakje in de gebruikers reis ingeschakeld.
7. Sla het extensie bestand op en upload het en zorg ervoor dat alle validaties slagen.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Een Relying Party-bestand maken

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van het bestand *SignUpOrSignIn. XML* in uw werkmap en wijzig de naam ervan. Bijvoorbeeld *SignUpOrSignInWithKmsi. XML*.
2. Open het nieuwe bestand en werk het kenmerk **PolicyId** voor de **TrustFrameworkPolicy** bij met een unieke waarde. Dit is de naam van uw beleid. Bijvoorbeeld `SignUpOrSignInWithKmsi`.
3. Wijzig het kenmerk **ReferenceId** voor het element **DefaultUserJourney** zodat dit overeenkomt met de id van de nieuwe gebruikers traject die u hebt gemaakt. Bijvoorbeeld `SignUpOrSignInWithKmsi`.

    KMSI wordt geconfigureerd met het element **UserJourneyBehaviors** met **SingleSignOn**, **SessionExpiryType**en **SessionExpiryInSeconds** als eerste onderliggende elementen. Het kenmerk **KeepAliveInDays** bepaalt hoe lang de gebruiker aangemeld blijft. In het volgende voor beeld verloopt de KMSI-sessie `7` automatisch na dagen, ongeacht hoe vaak de gebruiker de stille verificatie uitvoert. Als u `0` de KeepAliveInDays-waarde instelt, wordt de KMSI-functionaliteit uitgeschakeld. Deze waarde is `0`standaard ingesteld op. Als de waarde van **SessionExpiryType** is `Rolling`, wordt de KMSI-sessie elke `7` keer dat de gebruiker een stille verificatie uitvoert, door dagen verlengd.  Als `Rolling` is geselecteerd, moet u het aantal dagen beperken tot het minimum.

    De waarde van **SessionExpiryInSeconds** vertegenwoordigt de verloop tijd van een SSO-sessie. Dit wordt intern gebruikt door Azure AD B2C om te controleren of de sessie voor KMSI is verlopen of niet. De waarde van **KeepAliveInDays** bepaalt de verlooptde/maximale leeftijds waarde van de SSO-cookie in de webbrowser. In tegens telling tot **SessionExpiryInSeconds**wordt **KeepAliveInDays** gebruikt om te voor komen dat de browser de cookie wist wanneer deze is gesloten. Een gebruiker kan zich alleen op de achtergrond aanmelden als de SSO-sessie cookie bestaat, die wordt beheerd door **KeepAliveInDays**en niet is verlopen. dit wordt bepaald door **SessionExpiryInSeconds**.

    Als een gebruiker niet in staat is om in te scha kelen op de registratie-en aanmeldings pagina, wordt een sessie verloopt na het tijdstip **dat** is opgegeven door **SessionExpiryInSeconds** is verstreken of is de browser gesloten. Als een gebruiker **blijft aangemeld**, wordt de waarde van **KeepAliveInDays** overschreven door de waarde van **SessionExpiryInSeconds** en wordt de verloop tijd van de sessie gedicteerd. Zelfs als gebruikers de browser sluiten en opnieuw openen, kunnen ze zich nog steeds op de achtergrond aanmelden zolang deze zich binnen de tijd van **KeepAliveInDays**bevinden. Het is raadzaam om de waarde van **SessionExpiryInSeconds** in te stellen op een korte periode (1200 seconden), terwijl de waarde van **KeepAliveInDays** kan worden ingesteld op een relatief lange periode (7 dagen), zoals wordt weer gegeven in het volgende voor beeld:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Sla de wijzigingen op en upload het bestand.
5. Als u het aangepaste beleid dat u hebt geüpload, wilt testen, gaat u in het Azure Portal naar de pagina beleid en selecteert u **nu uitvoeren**.

U kunt [hier](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)het voorbeeld beleid vinden.
