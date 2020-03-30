---
title: Een Aazure AD-technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een Technisch Azure Directory-profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7db47eda47850c1c080b6a49256c8a0b37bb0d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330373"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een Azure Active Directory-technisch profiel definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het Azure Active Directory-gebruikersbeheer. In dit artikel worden de specifieke kenmerken beschreven van een technisch profiel voor interactie met een claimprovider die dit gestandaardiseerde protocol ondersteunt.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`van de protocolhandlerverzameling bevatten.

Het volgen van [aangepaste beleidsstartpakket](custom-policy-get-started.md#custom-policy-starter-pack) Azure AD-technische profielen omvatten het **AAD-Common** technische profiel. De technische azure ad-technische profielen geven het protocol niet op omdat het protocol is geconfigureerd in het **aad-algemene** technische profiel:
 
- **AAD-UserReadUsingAlternativeSecurityId** en **AAD-UserReadUsingAlternativeSecurityId-NoError** - Zoek een sociaal account op in de directory.
- **AAD-UserWriteUsingAlternativeSecurityId** - Maak een nieuw sociaal account.
- **AAD-UserReadUsingEmailAddress** - Zoek een lokaal account op in de directory.
- **AAD-UserWriteUsingLogonEmail** - Maak een nieuw lokaal account.
- **AAD-UserWritePasswordUsingObjectId** - Een wachtwoord van een lokaal account bijwerken.
- **AAD-UserWriteProfileUsingObjectId** - Een gebruikersprofiel van een lokaal of sociaal account bijwerken.
- **AAD-UserReadUsingObjectId** - Lees een gebruikersprofiel van een lokaal of sociaal account.
- **AAD-UserWritePhoneNumberUsingObjectId** - Schrijf het MFA-telefoonnummer van een lokaal of sociaal account

In het volgende voorbeeld ziet u het **aad-gemeenschappelijke** technische profiel:

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

## <a name="inputclaims"></a>Invoerclaims

Het element InputClaims bevat een claim, die wordt gebruikt om een account in de map op te zoeken of een nieuwe account te maken. Er moet precies één InputClaim-element in de invoerclaimverzameling voor alle technische Azure AD-technische profielen staan. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in Azure Active Directory.

Als u een bestaand gebruikersaccount wilt lezen, bijwerken of verwijderen, is de invoerclaim een sleutel die het account in de AD-map van Azure op unieke wijze identificeert. **ObjectId,** **userPrincipalName**, **signInNames.emailAddress**, **signInNames.userName**of **alternativeSecurityId**. 

Om een nieuw gebruikersaccount te maken, is de invoerclaim een sleutel die een lokaal of federatief account identificeert. Bijvoorbeeld, lokaal account: **signInNames.emailAddress**, of **signInNames.userName**. Voor een federatieve account: de **alternatieveSecurityId**.

Het element [InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) kan een verzameling elementen voor transformatie van invoerclaims bevatten die worden gebruikt om de invoerclaim te wijzigen of nieuwe te genereren.

## <a name="outputclaims"></a>Uitvoerclaims

Het element **OutputClaims** bevat een lijst met claims die zijn geretourneerd door het technische azure-profiel van AD. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in Azure Active Directory. U ook claims opnemen die niet worden geretourneerd door de `DefaultValue` Azure Active Directory, zolang u het kenmerk instelt.

Het element [OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

Het technische profiel **AAD-UserWriteUsingLogonEmail** maakt bijvoorbeeld een lokaal account en retourneert de volgende claims:

- **objectId**, dat is id van de nieuwe account
- **newUser**, wat aangeeft of de gebruiker nieuw is
- **authenticationSource**, waarin verificatie wordt ingesteld op`localAccountAuthentication`
- **userPrincipalName**, de gebruikersnaam van het nieuwe account
- **signInNames.emailAddress**, dat is de account aanmeldingsnaam, vergelijkbaar met de **e-mailinvoerclaim**

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

Het element **PersistedClaims** bevat alle waarden die door Azure AD moeten worden gehandhaafd met mogelijke toewijzingsgegevens tussen een claimtype dat al is gedefinieerd in de sectie [ClaimsSchema](claimsschema.md) in het beleid en de naam van het Azure AD-kenmerk.

Het **aad-userwriteusinglogonemail-profiel,** dat een nieuw lokaal account aanmaakt, blijft de volgende beweringen volgen:

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

De naam van de claim is de naam van het Kenmerk Azure AD, tenzij het kenmerk **PartnerClaimType** is opgegeven, dat de naam van het Azure AD-kenmerk bevat.

## <a name="requirements-of-an-operation"></a>Eisen van een operatie

- Er moet precies één **InputClaim-element** in de claimzak zitten voor alle technische Azure AD-profielen.
- In [het artikel over gebruikersprofielkenmerken](user-profile-attributes.md) worden de ondersteunde Azure AD B2C-gebruikersprofielkenmerken beschreven die u gebruiken in de invoerclaims, uitvoerclaims en aanhoudende claims. 
- Als de `Write` bewerking `DeleteClaims`is of , dan moet het ook worden weergegeven in een element **PersistedClaims.**
- De waarde van de **userPrincipalName-claim** `user@tenant.onmicrosoft.com`moet in de vorm van .
- De **displayName-claim** is vereist en kan geen lege tekenreeks zijn.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD-technische providerbewerkingen

### <a name="read"></a>Lezen

De **bewerking Lezen** leest gegevens over één gebruikersaccount. In het volgende technische profiel worden gegevens over een gebruikersaccount gelezen met behulp van de objectId van de gebruiker:

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

Met **de bewerking Schrijven** wordt één gebruikersaccount gemaakt of bijgewerkt. Het volgende technische profiel maakt een nieuw sociaal account:

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

### <a name="deleteclaims"></a>Claims verwijderen

De bewerking **DeleteClaims** verwijdert de informatie uit een opgegeven lijst met claims. In het volgende technische profiel worden claims verwijderd:

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

Met de bewerking **DeleteClaimsPrincipal** wordt één gebruikersaccount uit de map verwijderd. Met het volgende technische profiel wordt een gebruikersaccount uit de map verwijderd met de gebruikersnaam:

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

Het volgende technische profiel verwijdert een sociale gebruikersaccount met behulp van **alternativeSecurityId:**

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
| Bewerking | Ja | De uit te voeren bewerking. Mogelijke `Read`waarden: `Write` `DeleteClaims`, `DeleteClaimsPrincipal`, of . |
| RaiseerrorIfClaimsPrincipalNietExist | Nee | Een fout verhogen als het gebruikersobject niet in de map voorkomt. Mogelijke `true` waarden: `false`of . |
| RaiseerrorIfClaimsPrincipalBestaat al | Nee | Een fout verhogen als het gebruikersobject al bestaat. Mogelijke `true` waarden: `false`of .|
| ToepassingsObjectId | Nee | De toepassingsobject-id voor extensiekenmerken. Waarde: ObjectId van een toepassing. Zie [Aangepaste kenmerken gebruiken in een aangepast profielbewerkingsbeleid](custom-policy-custom-attributes.md)voor meer informatie . |
| ClientId | Nee | De client-id voor toegang tot de tenant als derde partij. Zie [Aangepaste kenmerken gebruiken in een aangepast profielbewerkingsbeleid](custom-policy-custom-attributes.md) voor meer informatie. |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer- en uitvoerclaims geeft u aan of [schadeafhandeling](claim-resolver-overview.md) is opgenomen in het technische profiel. Mogelijke waarden: `true` `false`  , of (standaard). Als u een claimresolver in het technische profiel `true`wilt gebruiken, stelt u dit in op . |

### <a name="ui-elements"></a>UI-elementen
 
De volgende instellingen kunnen worden gebruikt om het foutbericht te configureren dat bij een storing wordt weergegeven. De metagegevens moeten worden geconfigureerd in het [zelfverklaarde](self-asserted-technical-profile.md) technische profiel. De foutberichten kunnen worden [gelokaliseerd](localization.md).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalBestaat al | Nee | Als er een fout moet worden gegenereerd (zie De kenmerkbeschrijving van RaiseErrorIfClaimsPrincipalAlreadyExists), geeft u het bericht op dat aan de gebruiker moet worden weergegeven als het gebruikersobject al bestaat. |
| UserMessageIfClaimsPrincipalNietExist | Nee | Als er een fout moet worden gegenereerd (zie de kenmerkbeschrijving RaiseErrorIfClaimsPrincipalDoesNotExist), geeft u het bericht op dat aan de gebruiker moet worden weergegeven als het gebruikersobject niet bestaat. |


## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel, bijvoorbeeld van het gebruik van Azure AD-technisch profiel:

- [Claims toevoegen en gebruikersinvoer aanpassen met behulp van aangepast beleid in Azure Active Directory B2C](custom-policy-configure-user-input.md)














