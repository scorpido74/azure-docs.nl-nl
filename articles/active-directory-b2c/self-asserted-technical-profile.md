---
title: Een zelfgeclaimd technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een zelfgeclaimd technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332509"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een zelfgeclaimd technisch profiel definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Alle interacties in Azure Active Directory B2C (Azure AD B2C) waarbij de gebruiker naar verwachting invoer levert, zijn zelfverklaarde technische profielen. Bijvoorbeeld een aanmeldingspagina, aanmeldingspagina of pagina met het opnieuw instellen van wachtwoorden.

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocolhandlerassemblage die wordt gebruikt door Azure AD B2C, voor zelfverklaarde:`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

In het volgende voorbeeld wordt een zelfgeclaimd technisch profiel voor het aanmelden via e-mail weergegeven:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Invoerclaims

In een zelfgeclaimd technisch profiel u de elementen **InputClaims** en **InputClaimsTransformations** gebruiken om de waarde van de claims die op de zelfverklaarde pagina worden weergegeven (weergaveclaims) vooraf in te vullen. In het profielbeleid voor bewerken leest de gebruikersreis bijvoorbeeld eerst het gebruikersprofiel van de Azure AD B2C-directoryservice, waarna het zelfverklaarde technische profiel de invoerclaims instelt met de gebruikersgegevens die zijn opgeslagen in het gebruikersprofiel. Deze claims worden verzameld uit het gebruikersprofiel en vervolgens gepresenteerd aan de gebruiker die vervolgens de bestaande gegevens kan bewerken.

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

## <a name="display-claims"></a>Claims weergeven

De functie weergaveclaims bevindt zich momenteel in **de preview**.

Het **element DisplayClaims** bevat een lijst met claims die op het scherm moeten worden weergegeven voor het verzamelen van gegevens van de gebruiker. Als u de waarden van weergaveclaims vooraf wilt invullen, gebruikt u de invoerclaims die eerder zijn beschreven. Het element kan ook een standaardwaarde bevatten.

De volgorde van de claims in **DisplayClaims** geeft de volgorde op waarin Azure AD B2C de claims op het scherm rendert. Als u de gebruiker wilt dwingen een waarde voor een specifieke claim `true`op te geven, stelt u het kenmerk **Vereist** van het element **DisplayClaim** in op .

Het **Element ClaimType** in de **displayclaims-verzameling** moet het **element UserInputType** instellen op elk type gebruikersinvoer dat wordt ondersteund door Azure AD B2C. Bijvoorbeeld `TextBox` of `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Een verwijzing toevoegen aan een DisplayControl

In de verzameling weergaveclaims u een verwijzing opnemen naar een [DisplayControl](display-controls.md) die u hebt gemaakt. Een weergavebesturingselement is een element van de gebruikersinterface dat speciale functionaliteit heeft en samenwerkt met de Azure AD B2C back-endservice. Hiermee kan de gebruiker acties uitvoeren op de pagina die een validatietechnisch profiel aan de achterkant aanroepen. Bijvoorbeeld het verifiëren van een e-mailadres, telefoonnummer of klantloyaliteitsnummer.

In het `TechnicalProfile` volgende voorbeeld wordt het gebruik van weergaveclaims met weergavebesturingselementen geïllustreerd.

* De eerste weergaveclaim verwijst `emailVerificationControl` naar het weergavebesturingselement, dat het e-mailadres verzamelt en verifieert.
* De vijfde weergaveclaim verwijst `phoneVerificationControl` naar het weergavebesturingselement, dat een telefoonnummer verzamelt en verifieert.
* De andere weergaveclaims zijn ClaimTypes die van de gebruiker moeten worden verzameld.

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

Zoals gezegd kan een weergaveclaim met een verwijzing naar een weergavebesturingselement zijn eigen validatie uitvoeren, bijvoorbeeld het verifiëren van het e-mailadres. Bovendien ondersteunt de zelfverklaarde pagina het gebruik van een validatietechnisch profiel om de hele pagina te valideren, inclusief eventuele gebruikersinvoer (claimtypen of weergavebesturingselementen), voordat u doorgaat naar de volgende orkestratiestap.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Gebruik van weergaveclaims en uitvoerclaims zorgvuldig combineren

Als u een of meer **DisplayClaim-elementen** opgeeft in een zelfgeclaimd technisch profiel, moet u een DisplayClaim gebruiken voor *elke* claim die u op het scherm wilt weergeven en van de gebruiker wilt verzamelen. Er worden geen uitvoerclaims weergegeven door een zelfgeclaimd technisch profiel dat ten minste één weergaveclaim bevat.

Overweeg het volgende voorbeeld `age` waarin een claim wordt gedefinieerd als een **uitvoerclaim** in een basisbeleid. Voordat u displayclaims toevoegt aan het zelfverklaarde `age` technische profiel, wordt de claim op het scherm weergegeven voor het verzamelen van gegevens van de gebruiker:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Als een bladbeleid dat deze `officeNumber` basis erft, vervolgens wordt opgegeven als een **weergaveclaim:**

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

De `age` claim in het basisbeleid wordt niet meer op het scherm gepresenteerd aan de gebruiker - het is effectief 'verborgen'. Als u `age` de claim wilt weergeven en de `age` leeftijdswaarde van de gebruiker wilt innen, moet u een **DisplayClaim**toevoegen.

## <a name="output-claims"></a>Outputclaims

Het element **Uitvoerclaims** bevat een lijst met claims die moeten worden geretourneerd naar de volgende orchestration-stap. Het kenmerk **DefaultValue** wordt alleen van kracht als de claim nooit is ingesteld. Als deze is ingesteld in een vorige orchestration-stap, wordt de standaardwaarde niet van kracht, zelfs niet als de gebruiker de waarde leeg laat. Als u het gebruik van een standaardwaarde wilt `true`forceren, stelt u het kenmerk **AlwaysUseDefaultValue** in op .

Om veiligheidsredenen is een`UserInputType` wachtwoordclaimwaarde (ingesteld op `Password`) alleen beschikbaar voor de zelfverklaarde technische profielen van het technische profiel dat is bevestigd. U geen wachtwoordclaim gebruiken in de volgende orkestratiestappen. 

> [!NOTE]
> In eerdere versies van het Identity Experience Framework (IEF) werden uitvoerclaims gebruikt om gegevens van de gebruiker te verzamelen. Als u gegevens van de gebruiker wilt verzamelen, gebruikt u in plaats daarvan een **DisplayClaims-verzameling.**

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

### <a name="when-you-should-use-output-claims"></a>Wanneer u uitvoerclaims moet gebruiken

In een zelfgeclaimd technisch profiel retourneert de output claims-collectie de claims naar de volgende orchestration-stap.

Uitvoerclaims gebruiken wanneer:

- **Claims zijn output door transformatie van outputclaims**.
- **Een standaardwaarde instellen in een uitvoerclaim** zonder gegevens van de gebruiker te verzamelen of de gegevens terug te sturen van het technische validatieprofiel. Het `LocalAccountSignUpWithLogonEmail` zelfverklaarde technische profiel stelt de **uitgevoerde-SelfAsserted-Input** claim in op `true`.
- **Een validatietechnisch profiel retourneert de uitvoerclaims** - Uw technische profiel kan een validatietechnisch profiel aanroepen dat sommige claims retourneert. U de claims opborrelen en deze terugbrengen naar de volgende orkestratiestappen in de gebruikersreis. Wanneer u zich bijvoorbeeld aanmeldt met een lokaal account, `SelfAsserted-LocalAccountSignin-Email` roept het zelfgeclaimde technische profiel met de naam het technische validatieprofiel met de naam `login-NonInteractive`. Dit technische profiel valideert de gebruikersreferenties en retourneert ook het gebruikersprofiel. Zoals 'userPrincipalName', 'displayName', 'givenName' en 'surName'.
- **Een weergavebesturingselement retourneert de uitvoerclaims** - Uw technische profiel kan een verwijzing hebben naar een [weergavebesturingselement](display-controls.md). Het weergavebesturingselement retourneert bepaalde claims, zoals het geverifieerde e-mailadres. U de claims opborrelen en deze terugbrengen naar de volgende orkestratiestappen in de gebruikersreis. De functie displaybesturingselement is momenteel in **preview**.

In het volgende voorbeeld wordt het gebruik van een zelfgeclaimd technisch profiel aangetoond dat zowel weergaveclaims als uitvoerclaims gebruikt.

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

## <a name="persist-claims"></a>Aanhoudende claims

Het element PersistedClaims wordt niet gebruikt. Het zelfgeclaimde technische profiel houdt de gegevens niet voort aan Azure AD B2C. In plaats daarvan wordt een oproep gedaan naar een technisch validatieprofiel dat verantwoordelijk is voor het voortbestaan van de gegevens. Het aanmeldingsbeleid gebruikt bijvoorbeeld `LocalAccountSignUpWithLogonEmail` het zelfgeclaimde technische profiel om het nieuwe gebruikersprofiel te verzamelen. Het `LocalAccountSignUpWithLogonEmail` technische profiel roept het technische validatieprofiel aan om het account te maken in Azure AD B2C.

## <a name="validation-technical-profiles"></a>Technische profielen valideren

Een validatietechnisch profiel wordt gebruikt voor het valideren van sommige of alle uitvoerclaims van het verwijzende technische profiel. De inputclaims van het validatietechnische profiel moeten worden weergegeven in de uitvoerclaims van het zelfverklaarde technische profiel. Het technische validatieprofiel valideert de gebruikersinvoer en kan een fout naar de gebruiker retourneren.

Het technische profiel voor validatie kan elk technisch profiel in het beleid zijn, zoals [Azure Active Directory](active-directory-technical-profile.md) of een TECHNISCHE REST [API-profiel.](restful-technical-profile.md) In het vorige `LocalAccountSignUpWithLogonEmail` voorbeeld valideert het technische profiel dat de signinName niet in de map voorkomt. Als dit niet het probleem is, maakt het technische profiel van de validatie een lokaal account en retourneert het objectId, authenticationSource, newUser. Het `SelfAsserted-LocalAccountSignin-Email` technische profiel `login-NonInteractive` roept het validatietechnische profiel aan om de gebruikersreferenties te valideren.

U ook een REST API-technisch profiel aanroepen met uw bedrijfslogica, invoerclaims overschrijven of gebruikersgegevens verrijken door verder te integreren met zakelijke bedrijfstoepassingen. Zie [Technisch profiel valideren](validation-technical-profile.md) voor meer informatie

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| Nee | Voor een aanmeldingspagina bepaalt deze eigenschap het gedrag van het gebruikersnaamveld, zoals invoervalidatie en foutberichten. Verwachte `Username` waarden: `Email`of .  |
| AllowGenerationofClaimsWithNullValues| Nee| Toestaan om een claim te genereren met null-waarde. In een geval schakelt de gebruiker bijvoorbeeld geen selectievakje in.|
| ContentDefinitionReferenceId | Ja | De id van de [inhoudsdefinitie](contentdefinitions.md) die aan dit technische profiel is gekoppeld. |
| Verificatie van e-mail afdwingen | Nee | Voor aanmelden of profielbewerken dwingt u verificatie via e-mail af. Mogelijke waarden: `true` (standaard) of `false`. |
| instelling.opnieuw proberenLimiet | Nee | Hiermee bepaalt u het aantal keren dat een gebruiker kan proberen de gegevens te verstrekken die zijn gecontroleerd aan de hand van een technisch validatieprofiel. Een gebruiker probeert zich bijvoorbeeld aan te melden met een account dat al bestaat en blijft proberen totdat de limiet is bereikt.
| AanmeldenTarget <sup>1</sup>| Nee | De inschrijfdoeluitwisselings-id. Wanneer de gebruiker op de aanmeldingsknop klikt, voert Azure AD B2C de opgegeven exchange-id uit. |
| instelling.showCancelButton | Nee | Hiermee wordt de knop Annuleren weergegeven. Mogelijke waarden: `true` (standaard) of`false` |
| instelling.showContinueButton | Nee | Hiermee wordt de knop Doorgaan weergegeven. Mogelijke waarden: `true` (standaard) of`false` |
| instelling.weergevenAankoppelen2 <sup>2</sup>| Nee | Hiermee wordt de aanmeldingsknop weergegeven. Mogelijke waarden: `true` (standaard) of`false` |
| instelling.forgotPasswordLinkLocatie <sup>2</sup>| Nee| Hiermee wordt de koppeling met vergeten wachtwoorden weergegeven. Mogelijke waarden: `AfterInput` (standaard) de koppeling wordt onder aan `None` de pagina weergegeven of verwijdert de koppeling vergeten wachtwoord.|
| in-inschakelen.enableRememberMe <sup>2</sup>| Nee| Hiermee wordt het selectievakje Mij ondertekend in het selectievakje [Houden.](custom-policy-keep-me-signed-in.md) Mogelijke waarden: `true` `false` , of (standaard). |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer- en uitvoerclaims geeft u aan of [schadeafhandeling](claim-resolver-overview.md) is opgenomen in het technische profiel. Mogelijke waarden: `true` `false`  , of (standaard). Als u een claimresolver in het technische profiel `true`wilt gebruiken, stelt u dit in op . |

Opmerkingen:
1. Beschikbaar voor het type [DataUri-type](contentdefinitions.md#datauri) van `unifiedssp`gegevensdefinitie van of `unifiedssd`.
1. Beschikbaar voor het type [DataUri-type](contentdefinitions.md#datauri) van `unifiedssp`gegevensdefinitie van of `unifiedssd`. [Pagina-indeling versie](page-layout.md) 1.1.0 en hoger.

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element **CryptographicKeys** wordt niet gebruikt.
