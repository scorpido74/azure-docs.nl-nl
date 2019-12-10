---
title: Een zelf-bevestigd technisch profiel in een aangepast beleid definiëren
titleSuffix: Azure AD B2C
description: Definieer een zelf-bevestigd technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bfa8982fb49b31540d1926bdeb75a96dc1d79cf0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950898"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een zelf-bevestigd technisch profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Alle interacties in Azure Active Directory B2C (Azure AD B2C) waar de gebruiker wordt verwacht om invoer te bieden, zijn zelfondertekende technische profielen. Bijvoorbeeld een aanmeldings pagina, een aanmeldings pagina of een pagina voor het opnieuw instellen van een wacht woord.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary`. Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C, voor zelfbevestigend: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

In het volgende voor beeld ziet u een zelf-bevestigd technisch profiel voor het aanmelden via e-mail:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Invoer claims

In een niet-bevestigd technisch profiel kunt u de **InputClaims** -en **InputClaimsTransformations** -elementen gebruiken om de waarde van de claims die worden weer gegeven op de door uzelf bevestigde pagina (uitvoer claims), vooraf in te vullen. In het beleid voor het bewerken van profielen leest de gebruikers traject eerst het gebruikers profiel uit de Azure AD B2C Directory-service. vervolgens stelt het zelfondertekende technische profiel de invoer claims in met de gebruikers gegevens die zijn opgeslagen in het gebruikers profiel. Deze claims worden verzameld uit het gebruikers profiel en vervolgens weer gegeven aan de gebruiker die de bestaande gegevens kan bewerken.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die moeten worden gepresenteerd voor het verzamelen van gegevens van de gebruiker. Als u de uitvoer claims vooraf wilt invullen met een aantal waarden, gebruikt u de ingevoerde claims die eerder zijn beschreven. Het element kan ook een standaard waarde bevatten. De volg orde van de claims in **OutputClaims** bepaalt de volg orde waarin de claims op het scherm Azure AD B2C worden weer gegeven. Het kenmerk **DefaultValue** wordt alleen gebruikt als de claim nog nooit is ingesteld. Maar als de gebruiker de waarde leeg heeft, wordt de standaard waarde niet van kracht als deze eerder is ingesteld in een vorige Orchestration-stap. Als u het gebruik van een standaard waarde wilt forceren, stelt u het kenmerk **AlwaysUseDefaultValue** in op `true`. Als u wilt afdwingen dat de gebruiker een waarde voor een specifieke uitvoer claim opgeeft, stelt u het **vereiste** kenmerk van het **OutputClaims** -element in op `true`.

Het element **claim** type in de **OutputClaims** -verzameling moet het **UserInputType** -element instellen op een invoer van de gebruiker die wordt ondersteund door Azure AD B2C, zoals `TextBox` of `DropdownSingleSelect`. Of het element **output claim** moet een **DefaultValue**instellen.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

De volgende uitvoer claim is altijd ingesteld op `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Toepassing

Er zijn vier scenario's voor uitvoer claims:

- **Het verzamelen van de uitvoer claims van de gebruiker** : wanneer u gegevens moet verzamelen van de gebruiker, zoals geboorte datum, moet u de claim toevoegen aan de **OutputClaims** -verzameling. De claims die aan de gebruiker worden gepresenteerd, moeten de **UserInputType**opgeven, zoals `TextBox` of `DropdownSingleSelect`. Als het zelfvoorziene technische profiel een validatie technisch profiel bevat dat dezelfde claim uitvoert, Azure AD B2C de claim niet aan de gebruiker weer gegeven. Als er voor de gebruiker geen uitvoer claim aanwezig is, Azure AD B2C over overs laan van het technische profiel.
- Het **instellen van een standaard waarde in een uitvoer claim** -zonder gegevens te verzamelen van de gebruiker of het retour neren van de gegevens uit het technische profiel voor validatie. Met het door de `LocalAccountSignUpWithLogonEmail` zelf bevestigde technische profiel wordt de **uitgevoerde-SelfAsserted-invoer** claim ingesteld op `true`.
- **Een technisch profiel voor validatie retourneert de uitvoer claims** -uw technische profiel kan een validatie technische profiel aanroepen dat enkele claims retourneert. U kunt de claims inbellen en terugsturen naar de volgende indelings stappen in de gebruikers reis. Als u zich bijvoorbeeld aanmeldt met een lokaal account, wordt het door de zelf bevestigde technische profiel met de naam `SelfAsserted-LocalAccountSignin-Email` het technische profiel voor validatie aanroepen met de naam `login-NonInteractive`. Dit technische profiel valideert de gebruikers referenties en retourneert ook het gebruikers profiel. Zoals ' userPrincipalName ', ' displayName ', ' naam ' en ' Achternaam '.
- **De claims uitvoeren via trans formatie van uitvoer claims**

In het volgende voor beeld demonstreert het `LocalAccountSignUpWithLogonEmail` zelfondertekende technische profiel het gebruik van uitvoer claims en sets **uitgevoerde SelfAsserted-invoer** naar `true`. De `objectId`, `authenticationSource``newUser` claims worden uitgevoerd op het `AAD-UserWriteUsingLogonEmail` validatie technische profiel en worden niet weer gegeven aan de gebruiker.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Claims persistent maken

Als het **PersistedClaims** -element ontbreekt, worden de gegevens niet persistent gemaakt met het zelfondertekende technische profiel dat Azure AD B2C. In plaats daarvan wordt een aanroep uitgevoerd naar een technische profiel voor validatie dat verantwoordelijk is voor het persistent maken van de gegevens. Het aanmeldings beleid gebruikt bijvoorbeeld het `LocalAccountSignUpWithLogonEmail` zelfondertekende technische profiel om het nieuwe gebruikers profiel te verzamelen. Met het technische profiel van `LocalAccountSignUpWithLogonEmail` wordt het validatie technische profiel aangeroepen om het account in Azure AD B2C te maken.

## <a name="validation-technical-profiles"></a>Validatie van technische profielen

Een validatie technische profiel wordt gebruikt voor het valideren van een aantal of alle uitvoer claims van het referentie-technische profiel. De invoer claims van het technische profiel voor validatie moeten worden weer gegeven in de uitvoer claims van het zelfondertekende technische profiel. Het validatie-technische profiel valideert de gebruikers invoer en kan een fout naar de gebruiker retour neren.

Het technische profiel voor validatie kan elk technisch profiel in het beleid zijn, zoals [Azure Active Directory](active-directory-technical-profile.md) of een [rest API](restful-technical-profile.md) technische profielen. In het vorige voor beeld wordt met het technische profiel van `LocalAccountSignUpWithLogonEmail` gecontroleerd of de signinName niet in de directory bestaat. Als dat niet het geval is, maakt het technische profiel voor validatie een lokaal account en retourneert het objectId, authenticationSource, newUser. Met het technische profiel van de `SelfAsserted-LocalAccountSignin-Email` wordt het `login-NonInteractive` validatie technische profiel aangeroepen om de gebruikers referenties te valideren.

U kunt ook een REST API technisch profiel aanroepen met uw bedrijfs logica, invoer claims overschrijven of gebruikers gegevens verrijken door verder te integreren met zakelijke line-of-business-toepassingen. Zie voor meer informatie [validatie technische profiel](validation-technical-profile.md)

## <a name="metadata"></a>Metagegevens

| Kenmerk | Verplicht | Beschrijving |
| --------- | -------- | ----------- |
| setting. showContinueButton | Nee | Hiermee wordt de knop door gaan weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting.showCancelButton | Nee | Hiermee wordt de knop Annuleren weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting. operatingMode | Nee | Voor een aanmeldings pagina bepaalt deze eigenschap het gedrag van het veld gebruikers naam, zoals invoer validatie en fout berichten. Verwachte waarden: `Username` of `Email`. |
| ContentDefinitionReferenceId | Ja | De id van de [inhouds definitie](contentdefinitions.md) die aan dit technische profiel is gekoppeld. |
| EnforceEmailVerification | Nee | Voor de registratie of het profiel bewerken, wordt een e-mail verificatie afgedwongen. Mogelijke waarden: `true` (standaard) of `false`. |
| setting.showSignupLink | Nee | Hiermee wordt de knop registratie weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting.retryLimit | Nee | Hiermee bepaalt u het aantal keren dat een gebruiker de gegevens kan opgeven die worden gecontroleerd op basis van het technische profiel voor validatie. Bijvoorbeeld: een gebruiker probeert zich aan te melden met een account dat al bestaat en het probeert tot de limiet is bereikt.
| SignUpTarget | Nee | De Exchange-doel-id voor de aanmelding. Wanneer de gebruiker op de knop registratie klikt, wordt Azure AD B2C de opgegeven Exchange-id uitgevoerd. |

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het element **CryptographicKeys** wordt niet gebruikt.













