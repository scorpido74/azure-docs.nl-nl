---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Geef het TechnicalProfiles-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5196615b6b935e4d37565298be03ad315163d132
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264308"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een **TechnicalProfiles** -element bevat een set technische profielen die worden ondersteund door de claim provider. Elke claim provider moet een of meer technische profielen hebben die de eind punten bepalen en de protocollen die nodig zijn om te communiceren met de claim provider. Een claim provider kan meerdere technische profielen hebben.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Het element **TechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
|---------|---------|---------|
| Id | Ja | Een unieke id van het technische profiel. Naar het technische profiel kan worden verwezen via deze id vanuit andere elementen in het beleids bestand. Bijvoorbeeld **OrchestrationSteps** en **ValidationTechnicalProfile**. |

De **TechnicalProfile** bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Domain | 0:1 | De domein naam voor het technische profiel. Als uw technische profiel bijvoorbeeld de Facebook-ID-provider opgeeft, is de domein naam Facebook.com. |
| DisplayName | 1:1 | De naam van het technische profiel dat voor gebruikers kan worden weer gegeven. |
| Beschrijving | 0:1 | De beschrijving van het technische profiel dat kan worden weer gegeven voor gebruikers. |
| Protocol | 0:1 | Het protocol dat wordt gebruikt voor communicatie met de andere partij. |
| Metagegevens | 0:1 | Een verzameling sleutel/waarde-paren die worden gebruikt door het protocol voor communicatie met het eind punt in de loop van een trans actie. |
| InputTokenFormat | 0:1 | De indeling van het invoer token. Mogelijke waarden: `JSON`, `JWT`, `SAML11`of `SAML2`. De `JWT` waarde vertegenwoordigt een JSON Web Token volgens de specificatie van de IETF. De `SAML11` waarde vertegenwoordigt een SAML 1,1-beveiligings token conform de specificatie OASIS.  De `SAML2` waarde vertegenwoordigt een SAML 2,0-beveiligings token conform de specificatie OASIS. |
| OutputTokenFormat | 0:1 | De indeling van het uitvoer token. Mogelijke waarden: `JSON`, `JWT`, `SAML11`of `SAML2`. |
| CryptographicKeys | 0:1 | Een lijst met cryptografische sleutels die worden gebruikt in het technische profiel. |
| InputClaimsTransformations | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim transformaties die moeten worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. |
| InputClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claim typen die in het technische profiel worden opgenomen als invoer. |
| PersistedClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claim typen die door de claim provider worden bewaard en die betrekking hebben op het technische profiel. |
| DisplayClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claim typen die worden gepresenteerd door de claim provider, die betrekking heeft op het [zelfondertekende technische profiel](self-asserted-technical-profile.md). De functie DisplayClaims is momenteel beschikbaar als **Preview-versie**. |
| OutputClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claim typen die worden uitgevoerd als uitvoer in het technische profiel. |
| OutputClaimsTransformations | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim transformaties die moeten worden uitgevoerd nadat de claims zijn ontvangen van de claim provider. |
| ValidationTechnicalProfiles | 0: n | Een lijst met verwijzingen naar andere technische profielen die het technische profiel gebruikt voor validatie doeleinden. Zie voor meer informatie [validatie technische profiel](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Hiermee bepaalt u de productie van de onderwerpnaam in tokens waarin de onderwerpnaam afzonderlijk van claims is opgegeven. Bijvoorbeeld OAuth of SAML.  |
| IncludeInSso | 0:1 |  Of het gebruik van dit technische profiel het gedrag van eenmalige aanmelding (SSO) moet Toep assen voor de sessie, of in plaats daarvan expliciete interactie vereist. Dit element is alleen geldig in SelfAsserted-profielen die worden gebruikt binnen een validatie technische profiel. Mogelijke waarden: `true` (standaard) of `false`. |
| IncludeClaimsFromTechnicalProfile | 0:1 | Een id van een technisch profiel waarvan u wilt dat alle invoer-en uitvoer claims aan dit technische profiel worden toegevoegd. Het technische profiel waarnaar wordt verwezen, moet in hetzelfde beleids bestand worden gedefinieerd. |
| IncludeTechnicalProfile |0:1 | Een id van een technisch profiel waarvan u wilt dat alle gegevens worden toegevoegd aan dit technische profiel. |
| UseTechnicalProfileForSessionManagement | 0:1 | Een ander technisch profiel dat moet worden gebruikt voor sessie beheer. |
|EnabledForUserJourneys| 0:1 |Hiermee wordt bepaald of het technische profiel wordt uitgevoerd in een reis van de gebruiker.  |

## <a name="protocol"></a>Protocol

Het **protocol** element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Naam | Ja | De naam van een geldig protocol dat wordt ondersteund door Azure AD B2C dat wordt gebruikt als onderdeel van het technische profiel. Mogelijke waarden: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `Proprietary`of `None`. |
| Afhandelingsprocedure | Nee | Wanneer de protocol naam is ingesteld op `Proprietary`, geeft u de volledig gekwalificeerde naam op van de assembly die door Azure AD B2C wordt gebruikt om de protocolhandler te bepalen. |

## <a name="metadata"></a>Metagegevens

Een **META** gegevenselement bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Item | 0: n | De meta gegevens die betrekking hebben op het technische profiel. Elk type technisch profiel heeft een andere set meta gegevens items. Zie de sectie met technische profiel typen voor meer informatie. |

### <a name="item"></a>Item

Het element **item** van het **META** gegevenselement bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Sleutel | Ja | De meta gegevens sleutel. Zie elk type technische profiel voor de lijst met meta gegevens items. |

## <a name="cryptographickeys"></a>CryptographicKeys

Het element **CryptographicKeys** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Sleutel | 1: n | Een cryptografische sleutel die wordt gebruikt in dit technische profiel. |

### <a name="key"></a>Sleutel

Het **sleutel** element bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Nee | Een unieke id van een bepaalde sleutel paar waarnaar wordt verwezen vanuit andere elementen in het beleids bestand. |
| StorageReferenceId | Ja | Een id van een opslag sleutel container waarnaar wordt verwezen vanuit andere elementen in het beleids bestand. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

Het element **InputClaimsTransformations** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | De id van een claim transformatie die moet worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. Een claim transformatie kan worden gebruikt om bestaande ClaimsSchema claims te wijzigen of nieuwe te genereren. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Het element **InputClaimsTransformation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een claim transformatie die al is gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="inputclaims"></a>InputClaims

Het element **InputClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Een verwachte invoer claim type. |

### <a name="inputclaim"></a>InputClaim

Het **input claim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | Nee | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim aangegeven door ClaimTypeReferenceId niet bestaat, zodat de resulterende claim kan worden gebruikt als een input claim van het technische profiel. |
| PartnerClaimType | Nee | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven claim type van het beleid toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. De eerste claim naam is bijvoorbeeld ' noemer ', terwijl de partner een claim met de naam ' first_name ' gebruikt. |

## <a name="displayclaims"></a>DisplayClaims

Het element **DisplayClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Een verwachte invoer claim type. |

De functie DislayClaims is momenteel beschikbaar als **Preview-versie**.

### <a name="displayclaim"></a>DisplayClaim

Het **DisplayClaim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Nee | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| DisplayControlReferenceId | Nee | De id van een [Weergave besturings element](display-controls.md) dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Vereist | Nee | Hiermee wordt aangegeven of de weergave claim vereist is. |

De **DisplayClaim** vereist dat u een `ClaimTypeReferenceId` of `DisplayControlReferenceId`opgeeft.

### <a name="persistedclaims"></a>PersistedClaims

Het **PersistedClaims** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Het claim type moet persistent worden gemaakt. |

### <a name="persistedclaim"></a>PersistedClaim

Het **PersistedClaim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | Nee | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim aangegeven door ClaimTypeReferenceId niet bestaat, zodat de resulterende claim kan worden gebruikt als een input claim van het technische profiel. |
| PartnerClaimType | Nee | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven claim type van het beleid toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. De eerste claim naam is bijvoorbeeld ' noemer ', terwijl de partner een claim met de naam ' first_name ' gebruikt. |

## <a name="outputclaims"></a>OutputClaims

Het element **OutputClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| OutputClaim | 1: n | Een verwacht type uitvoer claim. |

### <a name="outputclaim"></a>OutputClaim

Het **output claim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | Nee | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim aangegeven door ClaimTypeReferenceId niet bestaat, zodat de resulterende claim kan worden gebruikt als een input claim van het technische profiel. |
|AlwaysUseDefaultValue |Nee |Het gebruik van de standaard waarde afdwingen.  |
| PartnerClaimType | Nee | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven claim type van het beleid toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. De eerste claim naam is bijvoorbeeld ' noemer ', terwijl de partner een claim met de naam ' first_name ' gebruikt. |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

Het element **OutputClaimsTransformations** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | De id's van claim transformaties die moeten worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. Een claim transformatie kan worden gebruikt om bestaande ClaimsSchema claims te wijzigen of nieuwe te genereren. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Het element **OutputClaimsTransformation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een claim transformatie die al is gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Het element **ValidationTechnicalProfiles** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | De id's van de technische profielen die worden gebruikt, valideren enkele of alle uitvoer claims van het technische profiel waarnaar wordt verwezen. Alle invoer claims van het technische profiel waarnaar wordt verwezen, moeten worden weer gegeven in de uitvoer claims van het referentie-technische profiel. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Het element **ValidationTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

De **SubjectNamingInfo** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimType | Ja | Een id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

Het element **IncludeTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

Het element **UseTechnicalProfileForSessionManagement** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

De **ClaimsProviderSelections** in een gebruikers traject definieert de lijst met opties voor de selectie van claim providers en de bijbehorende volg orde. Met het **EnabledForUserJourneys** -element dat u filtert, welke claim provider beschikbaar is voor de gebruiker. Het **EnabledForUserJourneys** -element bevat een van de volgende waarden:

- Voer **altijd**het technische profiel uit.
- **Nooit**, sla het technische profiel over.
- **OnClaimsExistence** kan alleen worden uitgevoerd wanneer een bepaalde claim is opgegeven in het technische profiel.
- **OnItemExistenceInStringCollectionClaim**, alleen uitvoeren wanneer een item in een teken reeks verzamelings claim voor komt.
- **OnItemAbsenceInStringCollectionClaim** kan alleen worden uitgevoerd wanneer een item niet bestaat in een claim voor een teken reeks verzameling.

Voor het gebruik van **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** of **OnItemAbsenceInStringCollectionClaim**, moet u de volgende meta gegevens opgeven: **ClaimTypeOnWhichToEnable** geeft het type van de claim op dat moet worden geëvalueerd, **ClaimValueOnWhichToEnable** geeft de waarde aan die moet worden vergeleken.

Het volgende technische profiel wordt alleen uitgevoerd als de **identityProviders** -teken reeks verzameling de waarde van `facebook.com`bevat:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
