---
title: Een technische Azure AD-profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een Azure Active Directory technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f7a6c5872c5e2b7e1b47b40e32ddb047641e8b2e
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944217"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een Azure Active Directory technisch profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het Azure Active Directory gebruikers beheer. In dit artikel worden de specifieke specificaties beschreven van een technisch profiel voor interactie met een claim provider die ondersteuning biedt voor dit gestandaardiseerde protocol.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary`. Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocol-handler bevatten `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

De volgende technische profielen van het [standaard beleid voor Starter Packs](custom-policy-get-started.md#custom-policy-starter-pack) van Azure AD bevatten het **Aad-algemene** technische profiel. De technische profielen van Azure AD geven niet het protocol op omdat het protocol is geconfigureerd in het **Aad-algemeen** Technical profiel:
 
- **Aad-UserReadUsingAlternativeSecurityId** en **Aad-UserReadUsingAlternativeSecurityId-fout** : Zoek een sociaal account in de Directory.
- **Aad-UserWriteUsingAlternativeSecurityId** : een nieuw sociaal account maken.
- **Aad-UserReadUsingEmailAddress** : Zoek een lokaal account in de Directory.
- **Aad-UserWriteUsingLogonEmail** : een nieuw lokaal account maken.
- **Aad-UserWritePasswordUsingObjectId** : een wacht woord van een lokaal account bijwerken.
- **Aad-UserWriteProfileUsingObjectId** : een gebruikers profiel van een lokaal of sociaal account bijwerken.
- **Aad-UserReadUsingObjectId** : een gebruikers profiel van een lokaal of sociaal account lezen.
- **Aad-UserWritePhoneNumberUsingObjectId** : het MFA-telefoon nummer van een lokaal of sociaal account schrijven

In het volgende voor beeld wordt het **Aad-algemene** technische profiel weer gegeven:

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

Het InputClaims-element bevat een claim waarmee een account in de Directory wordt gezocht of een nieuwe wordt gemaakt. Er moet precies één input claim-element in de claim verzameling voor invoer zijn voor alle technische profielen van Azure AD. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in Azure Active Directory.

Als u een bestaand gebruikers account wilt lezen, bijwerken of verwijderen, is de invoer claim een sleutel waarmee het account in azure AD-adres lijst op unieke wijze wordt geïdentificeerd. Bijvoorbeeld **objectId**, **userPrincipalName**, **signInNames. emailAddress**, **signInNames. username**of **alternativeSecurityId**. 

Als u een nieuw gebruikers account wilt maken, is de invoer claim een sleutel waarmee een lokaal of federatief account op unieke wijze wordt geïdentificeerd. Bijvoorbeeld lokale account: **signInNames. emailAddress**of **signInNames. username**. Voor een federatief account: **alternativeSecurityId**.

Het InputClaimsTransformations-element kan een verzameling trans formatie-elementen voor invoer claims bevatten die worden gebruikt voor het wijzigen van de invoer claim of het genereren van een nieuwe.

## <a name="outputclaims"></a>OutputClaims

Het **OutputClaims** -element bevat een lijst met claims die worden geretourneerd door het technische profiel van Azure AD. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in Azure Active Directory. U kunt ook claims toevoegen die niet worden geretourneerd door de Azure Active Directory, op voor waarde dat u het kenmerk `DefaultValue` hebt ingesteld.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

Het **Aad-UserWriteUsingLogonEmail-** technische profiel maakt bijvoorbeeld een lokaal account en retourneert de volgende claims:

- **objectId**, een id van het nieuwe account
- **newuser**, die aangeeft of de gebruiker nieuw is
- **authenticationSource**, waarmee verificatie wordt ingesteld op `localAccountAuthentication`
- **userPrincipalName**: de User Principal name van het nieuwe account
- **signInNames. emailAddress**, de aanmeldings naam van het account, vergelijkbaar met de invoer claim van de **e-mail**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

Het **PersistedClaims** -element bevat alle waarden die door Azure AD moeten worden bewaard met mogelijke toewijzings informatie tussen een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleid en de naam van het Azure AD-kenmerk.

Het **Aad-UserWriteUsingLogonEmail-** technische profiel, waarmee een nieuw lokaal account wordt gemaakt, houdt rekening met de volgende claims:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

De naam van de claim is de naam van het kenmerk Azure AD, tenzij het kenmerk **PartnerClaimType** is opgegeven, dat de naam van het Azure AD-kenmerk bevat.

## <a name="requirements-of-an-operation"></a>Vereisten van een bewerking

- Er moet precies één **input claim** -element aanwezig zijn in de claim verzameling voor alle technische profielen van Azure AD.
- Als de bewerking `Write` of `DeleteClaims`is, moet deze ook worden weer gegeven in een **PersistedClaims** -element.
- De waarde van de **userPrincipalName** -claim moet de indeling van `user@tenant.onmicrosoft.com`hebben.
- De **DisplayName** -claim is vereist en kan geen lege teken reeks zijn.

## <a name="azure-ad-technical-provider-operations"></a>Bewerkingen voor de technische provider van Azure AD

### <a name="read"></a>Lezen

Met de **Lees** bewerking worden gegevens over één gebruikers account gelezen. Als u gebruikers gegevens wilt lezen, moet u een sleutel opgeven als een invoer claim, zoals **objectId**, **userPrincipalName**, **signInNames** (elk type, gebruikers naam en e-mail account) of **alternativeSecurityId**.

Met het volgende technische profiel worden gegevens over een gebruikers account gelezen met de objectId van de gebruiker:

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Schrijven

Met de **Schrijf** bewerking wordt één gebruikers account gemaakt of bijgewerkt. Als u een gebruikers account wilt schrijven, moet u een sleutel opgeven als een invoer claim, zoals **objectId**, **userPrincipalName**, **signInNames. emailAddress**of **alternativeSecurityId**.

Het volgende technische profiel maakt een nieuw sociaal account:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

Met de bewerking **DeleteClaims** wordt de informatie uit een verstrekte lijst met claims gewist. Als u informatie uit claims wilt verwijderen, moet u een sleutel opgeven als een invoer claim, zoals **objectId**, **userPrincipalName**, **signInNames. emailAddress** of **alternativeSecurityId**.

Met het volgende technische profiel worden claims verwijderd:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Met de bewerking **DeleteClaimsPrincipal** wordt één gebruikers account uit de map verwijderd. Als u een gebruikers account wilt verwijderen, moet u een sleutel opgeven als een invoer claim, zoals **objectId**, **userPrincipalName**, **signInNames. emailAddress** of **alternativeSecurityId**.

Met het volgende technische profiel wordt een gebruikers account uit de Directory verwijderd met behulp van de user principal name:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Met het volgende technische profiel wordt een sociaal gebruikers account verwijderd met behulp van **alternativeSecurityId**:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | De bewerking die moet worden uitgevoerd. Mogelijke waarden: `Read`, `Write`, `DeleteClaims`of `DeleteClaimsPrincipal`. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nee | Een fout veroorzaken als het gebruikers object niet in de map bestaat. Mogelijke waarden: `true` of `false`. |
| UserMessageIfClaimsPrincipalDoesNotExist | Nee | Als er een fout is opgetreden (Zie de beschrijving van het kenmerk RaiseErrorIfClaimsPrincipalDoesNotExist), geeft u het bericht op dat moet worden weer gegeven voor de gebruiker als het gebruikers object niet bestaat. De waarde kan worden [gelokaliseerd](localization.md).|
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nee | Er wordt een fout gegenereerd als het gebruikers object al bestaat. Mogelijke waarden: `true` of `false`.|
| UserMessageIfClaimsPrincipalAlreadyExists | Nee | Als er een fout is opgetreden (zie beschrijving van het RaiseErrorIfClaimsPrincipalAlreadyExists-kenmerk), geeft u het bericht op dat moet worden weer gegeven voor de gebruiker als het gebruikers object al bestaat. De waarde kan worden [gelokaliseerd](localization.md).|
| ApplicationObjectId | Nee | De object-id van de toepassing voor extensie kenmerken. Waarde: ObjectId van een toepassing. Zie voor meer informatie [aangepaste kenmerken gebruiken in een aangepast profiel beleid bewerken](custom-policy-custom-attributes.md). |
| ClientId | Nee | De client-id voor toegang tot de Tenant als derde partij. Zie voor meer informatie [aangepaste kenmerken gebruiken in een aangepast profiel beleid bewerken](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true`, of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true`. |














