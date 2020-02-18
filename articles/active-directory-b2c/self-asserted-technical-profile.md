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
ms.date: 02/16/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 37d895e2514d01bdbe73f42e1ba5ea5e4c46bfa1
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2020
ms.locfileid: "77372856"
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

In een zelf-bevestigd technisch profiel kunt u de **InputClaims** -en **InputClaimsTransformations** -elementen gebruiken om de waarde van de claims die worden weer gegeven op de niet-bevestigde pagina (claims weer geven), vooraf in te vullen. In het beleid voor het bewerken van profielen leest de gebruikers traject eerst het gebruikers profiel uit de Azure AD B2C Directory-service. vervolgens stelt het zelfondertekende technische profiel de invoer claims in met de gebruikers gegevens die zijn opgeslagen in het gebruikers profiel. Deze claims worden verzameld uit het gebruikers profiel en vervolgens weer gegeven aan de gebruiker die de bestaande gegevens kan bewerken.

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

## <a name="display-claims"></a>Claims weer geven

De functie voor het weer geven van claims is momenteel beschikbaar als **Preview-versie**.

Het **DisplayClaims** -element bevat een lijst met claims die op het scherm moeten worden weer gegeven voor het verzamelen van gegevens van de gebruiker. Gebruik de invoer claims die eerder zijn beschreven om de waarden van uitvoer claims vooraf in te vullen. Het element kan ook een standaard waarde bevatten.

De volg orde van de claims in **DisplayClaims** geeft aan in welke volg orde Azure AD B2C de claims op het scherm worden weer gegeven. Als u wilt afdwingen dat de gebruiker een waarde voor een specifieke claim opgeeft, stelt u het **vereiste** kenmerk van het **DisplayClaim** -element in op `true`.

Het element **claim** type in de **DisplayClaims** -verzameling moet het **UserInputType** -element instellen op elk type gebruikers invoer dat door Azure AD B2C wordt ondersteund. Bijvoorbeeld `TextBox` of `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Een verwijzing naar een DisplayControl toevoegen

In de claim verzameling weer geven kunt [u een verwijzing naar een weer gave](display-controls.md) die u hebt gemaakt, toevoegen. Een weergave besturings element is een gebruikers interface-element dat speciale functionaliteit heeft en samenwerkt met de Azure AD B2C back-end-service. Hiermee kan de gebruiker acties uitvoeren op de pagina die een validatie technische profiel aan de back-end aanroept. U kunt bijvoorbeeld een e-mail adres, telefoon nummer of loyaliteits nummer van de klant controleren.

Het volgende voor beeld `TechnicalProfile` illustreert het gebruik van weer gave claims met besturings elementen voor weer gave.

* De eerste weergave claim verwijst naar het `emailVerificationControl`-weergave besturings element waarmee het e-mail adres wordt verzameld en geverifieerd.
* De vijfde weergave claim verwijst naar het `phoneVerificationControl`-weergave besturings element waarmee een telefoon nummer wordt verzameld en geverifieerd.
* De andere weer gave claims worden ClaimTypes van de gebruiker verzameld.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Zoals vermeld, kan een weergave claim met een verwijzing naar een weergave besturings element een eigen validatie uitvoeren, bijvoorbeeld het e-mail adres controleren. Daarnaast ondersteunt de zelfbevestigende pagina het gebruik van een validatie technische profiel voor het valideren van de volledige pagina, inclusief eventuele gebruikers invoer (claim typen of besturings elementen weer geven), voordat u verdergaat met de volgende Orchestration-stap.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Gebruik zorgvuldig combi neren van claims en uitvoer claims

Als u een of meer **DisplayClaim** -elementen opgeeft in een zelf-bevestigd technisch profiel, moet u een DisplayClaim gebruiken voor *elke* claim die u op het scherm wilt weer geven en van de gebruiker wilt verzamelen. Er worden geen uitvoer claims weer gegeven met een niet-bevestigd technisch profiel dat ten minste één weergave claim bevat.

Bekijk het volgende voor beeld waarin een `age` claim wordt gedefinieerd als een **uitvoer** claim in een basis beleid. Voordat u de weer gave claims aan het zelfondertekende technische profiel toevoegt, wordt de `age` claim op het scherm weer gegeven voor het verzamelen van gegevens van de gebruiker:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Als een blad beleid dat deze basis overneemt, geeft u `officeNumber` als een **weergave** claim op:

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

De `age` claim in het basis beleid wordt niet meer op het scherm weer gegeven voor de gebruiker. Dit is het effectief ' verborgen '. Als u de `age` claim wilt weer geven en de leeftijds waarde van de gebruiker wilt verzamelen, moet u een `age` **DisplayClaim**toevoegen.

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die moeten worden geretourneerd naar de volgende Orchestration-stap. Het kenmerk **DefaultValue** wordt alleen gebruikt als de claim nooit is ingesteld. Als deze in een vorige Orchestration-stap is ingesteld, wordt de standaard waarde niet van kracht, zelfs niet als de gebruiker de waarde leeg laat. Als u het gebruik van een standaard waarde wilt forceren, stelt u het kenmerk **AlwaysUseDefaultValue** in op `true`.

> [!NOTE]
> In eerdere versies van het Framework voor identiteits ervaring (IEF) werden uitvoer claims gebruikt voor het verzamelen van gegevens van de gebruiker. Als u gegevens wilt verzamelen van de gebruiker, gebruikt u in plaats daarvan een **DisplayClaims** -verzameling.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="when-you-should-use-output-claims"></a>Wanneer u uitvoer claims moet gebruiken

In een niet-bevestigd technisch profiel retourneert de claim verzameling output de claims naar de volgende Orchestration-stap.

U moet uitvoer claims gebruiken wanneer:

- **Claims worden uitgevoerd door trans formatie van uitvoer claims**.
- Het **instellen van een standaard waarde in een uitvoer claim** zonder het verzamelen van gegevens van de gebruiker of het retour neren van de gegevens uit het technische profiel voor validatie. Met het door de `LocalAccountSignUpWithLogonEmail` zelf bevestigde technische profiel wordt de **uitgevoerde-SelfAsserted-invoer** claim ingesteld op `true`.
- **Een technisch profiel voor validatie retourneert de uitvoer claims** -uw technische profiel kan een validatie technische profiel aanroepen dat enkele claims retourneert. U kunt de claims inbellen en terugsturen naar de volgende indelings stappen in de gebruikers reis. Als u zich bijvoorbeeld aanmeldt met een lokaal account, wordt het door de zelf bevestigde technische profiel met de naam `SelfAsserted-LocalAccountSignin-Email` het technische profiel voor validatie aanroepen met de naam `login-NonInteractive`. Dit technische profiel valideert de gebruikers referenties en retourneert ook het gebruikers profiel. Zoals ' userPrincipalName ', ' displayName ', ' naam ' en ' Achternaam '.
- **Een besturings element voor weer gave retourneert de uitvoer claims** -uw technische profiel bevat mogelijk een verwijzing naar een [besturings element voor weer gave](display-controls.md). Het besturings element weer gave retourneert enkele claims, zoals het geverifieerde e-mail adres. U kunt de claims inbellen en terugsturen naar de volgende indelings stappen in de gebruikers reis. De functie voor het weer geven van besturings elementen is momenteel beschikbaar als **Preview-versie**.

In het volgende voor beeld wordt het gebruik van een zelf-bevestigd technisch profiel gedemonstreerd dat zowel claims als uitvoer claims gebruikt.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
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

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| setting. operatingMode <sup>1</sup>| Nee | Voor een aanmeldings pagina bepaalt deze eigenschap het gedrag van het veld gebruikers naam, zoals invoer validatie en fout berichten. Verwachte waarden: `Username` of `Email`.  |
| AllowGenerationOfClaimsWithNullValues| Nee| Er kan een claim met een null-waarde worden gegenereerd. Zo wordt in een case-gebruiker geen selectie vakje geselecteerd.|
| ContentDefinitionReferenceId | Ja | De id van de [inhouds definitie](contentdefinitions.md) die aan dit technische profiel is gekoppeld. |
| EnforceEmailVerification | Nee | Voor de registratie of het profiel bewerken, wordt een e-mail verificatie afgedwongen. Mogelijke waarden: `true` (standaard) of `false`. |
| setting.retryLimit | Nee | Hiermee bepaalt u het aantal keren dat een gebruiker de gegevens kan opgeven die worden gecontroleerd op basis van het technische profiel voor validatie. Bijvoorbeeld: een gebruiker probeert zich aan te melden met een account dat al bestaat en het probeert tot de limiet is bereikt.
| SignUpTarget <sup>1</sup>| Nee | De Exchange-doel-id voor de aanmelding. Wanneer de gebruiker op de knop registratie klikt, wordt Azure AD B2C de opgegeven Exchange-id uitgevoerd. |
| setting.showCancelButton | Nee | Hiermee wordt de knop Annuleren weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting. showContinueButton | Nee | Hiermee wordt de knop door gaan weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting. showSignupLink <sup>2</sup>| Nee | Hiermee wordt de knop registratie weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting. forgotPasswordLinkLocation <sup>2</sup>| Nee| Hiermee wordt de verg eten wachtwoord koppeling weer gegeven. Mogelijke waarden: `AfterInput` (standaard) de koppeling wordt weer gegeven aan de onderkant van de pagina, of `None` verwijdert de koppeling verg eten wacht woord.| 
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true`, of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true`. |

Opmerkingen:
1. Beschikbaar voor inhouds definitie [DataUri](contentdefinitions.md#datauri) type van `unifiedssp`, of `unifiedssd`.
1. Beschikbaar voor inhouds definitie [DataUri](contentdefinitions.md#datauri) type van `unifiedssp`, of `unifiedssd`. [Versie](page-layout.md) 1.1.0 en hoger van de pagina-indeling.

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het element **CryptographicKeys** wordt niet gebruikt.
