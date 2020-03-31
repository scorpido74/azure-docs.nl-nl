---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Geef het element TechnicalProfiles van een aangepast beleid op in Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264308"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een **TechnicalProfiles** element bevat een set technische profielen ondersteund door de claimprovider. Elke claimprovider moet beschikken over een of meer technische profielen die de eindpunten en de protocollen bepalen die nodig zijn om met de claimprovider te communiceren. Een claimprovider kan meerdere technische profielen hebben.

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
| Id | Ja | Een unieke identificatie van het technische profiel. Het technische profiel kan worden verwezen met behulp van deze id van andere elementen in het beleidsbestand. Bijvoorbeeld **OrchestrationSteps** en **ValidationTechnicalProfile**. |

Het **TechnicalProfile** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Domain | 0:1 | De domeinnaam voor het technische profiel. Als uw technische profiel bijvoorbeeld de Facebook-identiteitsprovider opgeeft, wordt de domeinnaam Facebook.com. |
| DisplayName | 1:1 | De naam van het technische profiel dat aan gebruikers kan worden weergegeven. |
| Beschrijving | 0:1 | De beschrijving van het technische profiel dat aan gebruikers kan worden weergegeven. |
| Protocol | 0:1 | Het protocol dat wordt gebruikt voor de communicatie met de andere partij. |
| Metagegevens | 0:1 | Een verzameling sleutel-/waardeparen die worden gebruikt door het protocol voor het communiceren met het eindpunt in de loop van een transactie. |
| Invoertokenopmaak | 0:1 | De indeling van het invoertoken. Mogelijke `JSON`waarden: `JWT` `SAML11`, `SAML2`, of . De `JWT` waarde vertegenwoordigt een JSON-webtoken volgens de IETF-specificatie. De `SAML11` waarde vertegenwoordigt een SAML 1.1-beveiligingstoken volgens oasis-specificatie.  De `SAML2` waarde vertegenwoordigt een SAML 2.0-beveiligingstoken volgens oasis-specificatie. |
| OutputTokenOpmaak | 0:1 | Het formaat van het uitvoertoken. Mogelijke `JSON`waarden: `JWT` `SAML11`, `SAML2`, of . |
| Cryptografische sleutels | 0:1 | Een lijst met cryptografische sleutels die worden gebruikt in het technische profiel. |
| InputClaimsTransformaties | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claimstransformaties die moeten worden uitgevoerd voordat claims naar de claimprovider of de relying party worden verzonden. |
| Invoerclaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claimtypen die worden opgevat als input in het technische profiel. |
| PersistedClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claimtypen die worden aangehouden door de claimprovider die betrekking heeft op het technische profiel. |
| Weergaveclaims | 0:1 | Een lijst van de eerder gedefinieerde verwijzingen naar claimtypen die worden gepresenteerd door de claimprovider die betrekking heeft op het [zelfverklaarde technische profiel](self-asserted-technical-profile.md). De functie DisplayClaims bevindt zich momenteel in **de preview**. |
| Uitvoerclaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claimtypen die worden opgevat als uitvoer in het technische profiel. |
| OutputClaimsTransformaties | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claimstransformaties die moeten worden uitgevoerd nadat de claims van de claimprovider zijn ontvangen. |
| ValidatieTechnische profielen | 0:n | Een lijst met verwijzingen naar andere technische profielen die het technische profiel gebruikt voor validatiedoeleinden. Zie [validatietechnisch profiel voor](validation-technical-profile.md) meer informatie|
| OnderwerpNaamgevingInfo | 0:1 | Hiermee bepaalt u de productie van de onderwerpnaam in tokens waarbij de onderwerpnaam afzonderlijk van claims wordt opgegeven. Bijvoorbeeld OAuth of SAML.  |
| IncludeInSso | 0:1 |  Of het gebruik van dit technische profiel enkel aanmeldingsgedrag (SSO) voor de sessie moet toepassen of in plaats daarvan expliciete interactie vereist. Dit element is alleen geldig in SelfAsserted-profielen die worden gebruikt binnen een technisch validatieprofiel. Mogelijke waarden: `true` (standaard) of `false`. |
| Claimsuittechnisch profiel opnemen | 0:1 | Een id van een technisch profiel waarvan u wilt dat alle invoer- en uitvoerclaims aan dit technische profiel worden toegevoegd. Het technische profiel waarnaar wordt verwezen, moet in hetzelfde beleidsbestand worden gedefinieerd. |
| InclusiefTechnisch Profiel |0:1 | Een id van een technisch profiel waarvan u wilt dat alle gegevens aan dit technische profiel worden toegevoegd. |
| GebruikTechnicalProfileForSessionManagement | 0:1 | Een ander technisch profiel dat gebruikt kan worden voor sessiemanagement. |
|IngeschakeldVoorGebruikersreizen| 0:1 |Hiermee bepaalt u of het technische profiel wordt uitgevoerd in een gebruikersreis.  |

## <a name="protocol"></a>Protocol

Het **element Protocol** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Name | Ja | De naam van een geldig protocol dat wordt ondersteund door Azure AD B2C dat wordt gebruikt als onderdeel van het technische profiel. `OAuth1`Mogelijke waarden: `OAuth2` `SAML2`, `OpenIdConnect` `Proprietary`, `None`, , of . |
| Handler | Nee | Wanneer de protocolnaam `Proprietary`is ingesteld op , geeft u de volledig gekwalificeerde naam op van de assemblage die wordt gebruikt door Azure AD B2C om de protocolhandler te bepalen. |

## <a name="metadata"></a>Metagegevens

Een **element metagegevens** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Item | 0:n | De metadata die betrekking heeft op het technische profiel. Elk type technisch profiel heeft een andere set metagegevensitems. Zie de sectie technische profieltypen voor meer informatie. |

### <a name="item"></a>Item

Het **item-element** van het element **Metagegevens** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Sleutel | Ja | De metadata sleutel. Zie elk technisch profieltype voor de lijst met metagegevensitems. |

## <a name="cryptographickeys"></a>Cryptografische sleutels

Het element **CryptographicKeys** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Sleutel | 1:n | Een cryptografische sleutel die wordt gebruikt in dit technische profiel. |

### <a name="key"></a>Sleutel

Het element **Sleutel** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Nee | Een unieke id van een bepaald sleutelpaar waarnaar wordt verwezen uit andere elementen in het beleidsbestand. |
| StorageReferenceId | Ja | Een identifer van een opslagsleutelcontainer waarnaar wordt verwezen uit andere elementen in het beleidsbestand. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformaties

Het element **InputClaimsTransformations** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| InputClaimsTransformatie | 1:n | De id van een claimtransformatie die moet worden uitgevoerd voordat claims naar de claimprovider of de relying party worden verzonden. Een claimtransformatie kan worden gebruikt om bestaande ClaimsSchema-claims te wijzigen of nieuwe claims te genereren. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformatie

Het element **InputClaimsTransformation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een claimtransformatie die al is gedefinieerd in het beleidsbestand of het bovenliggende beleidsbestand. |

## <a name="inputclaims"></a>Invoerclaims

Het element **InputClaims** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Invoerclaim | 1:n | Een verwacht invoerclaimtype. |

### <a name="inputclaim"></a>Invoerclaim

Het element **InputClaim** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Referentienummer van claimtype | Ja | De id van een claimtype dat al is gedefinieerd in de sectie ClaimsSchema in het beleidsbestand of het bovenliggende beleidsbestand. |
| Standaardwaarde | Nee | Een standaardwaarde die moet worden gebruikt om een claim te maken als de claim die door ClaimTypeReferenceId wordt vermeld, niet bestaat, zodat de resulterende claim kan worden gebruikt als inputclaim door het technische profiel. |
| PartnerClaimType | Nee | De id van het claimtype van de externe partner waaraan het opgegeven type beleidsclaim wordt toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven type beleidsclaim toegewezen aan het partnerclaimtype met dezelfde naam. Gebruik deze eigenschap wanneer uw claimtypenaam verschilt van de andere partij. De eerste claimnaam is bijvoorbeeld 'givenName', terwijl de partner een claim gebruikt met de naam 'first_name'. |

## <a name="displayclaims"></a>Weergaveclaims

Het element **DisplayClaims** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | Een verwacht invoerclaimtype. |

De functie DislayClaims is momenteel in **preview**.

### <a name="displayclaim"></a>DisplayClaim

Het element **DisplayClaim** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Referentienummer van claimtype | Nee | De id van een claimtype dat al is gedefinieerd in de sectie ClaimsSchema in het beleidsbestand of het bovenliggende beleidsbestand. |
| DisplayControlReferenceId | Nee | De id van een [weergavebesturingselement](display-controls.md) dat al is gedefinieerd in de sectie ClaimsSchema in het beleidsbestand of het bovenliggende beleidsbestand. |
| Vereist | Nee | Geeft aan of de weergaveclaim vereist is. |

De **DisplayClaim** vereist dat `ClaimTypeReferenceId` u `DisplayControlReferenceId`een of .

### <a name="persistedclaims"></a>PersistedClaims

Het element **PersistedClaims** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| PersistedClaim (PersistedClaim) | 1:n | Het claimtype dat blijft bestaan. |

### <a name="persistedclaim"></a>PersistedClaim (PersistedClaim)

Het element **PersistedClaim** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Referentienummer van claimtype | Ja | De id van een claimtype dat al is gedefinieerd in de sectie ClaimsSchema in het beleidsbestand of het bovenliggende beleidsbestand. |
| Standaardwaarde | Nee | Een standaardwaarde die moet worden gebruikt om een claim te maken als de claim die door ClaimTypeReferenceId wordt vermeld, niet bestaat, zodat de resulterende claim kan worden gebruikt als inputclaim door het technische profiel. |
| PartnerClaimType | Nee | De id van het claimtype van de externe partner waaraan het opgegeven type beleidsclaim wordt toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven type beleidsclaim toegewezen aan het partnerclaimtype met dezelfde naam. Gebruik deze eigenschap wanneer uw claimtypenaam verschilt van de andere partij. De eerste claimnaam is bijvoorbeeld 'givenName', terwijl de partner een claim gebruikt met de naam 'first_name'. |

## <a name="outputclaims"></a>Uitvoerclaims

Het element **OutputClaims** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Uitvoerclaim | 1:n | Een verwacht uitvoerclaimtype. |

### <a name="outputclaim"></a>Uitvoerclaim

Het element **OutputClaim** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Referentienummer van claimtype | Ja | De id van een claimtype dat al is gedefinieerd in de sectie ClaimsSchema in het beleidsbestand of het bovenliggende beleidsbestand. |
| Standaardwaarde | Nee | Een standaardwaarde die moet worden gebruikt om een claim te maken als de claim die door ClaimTypeReferenceId wordt vermeld, niet bestaat, zodat de resulterende claim kan worden gebruikt als inputclaim door het technische profiel. |
|AlwaysUseDefaultValue AlwaysUseDefaultValue AlwaysUseDefaultValue AlwaysUse |Nee |Forceer het gebruik van de standaardwaarde.  |
| PartnerClaimType | Nee | De id van het claimtype van de externe partner waaraan het opgegeven type beleidsclaim wordt toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven type beleidsclaim toegewezen aan het partnerclaimtype met dezelfde naam. Gebruik deze eigenschap wanneer uw claimtypenaam verschilt van de andere partij. De eerste claimnaam is bijvoorbeeld 'givenName', terwijl de partner een claim gebruikt met de naam 'first_name'. |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformaties

Het element **OutputClaimsTransformations** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| OutputClaimsTransformatie | 1:n | De id's van claimstransformaties die moeten worden uitgevoerd voordat claims naar de claimprovider of de relying party worden verzonden. Een claimtransformatie kan worden gebruikt om bestaande ClaimsSchema-claims te wijzigen of nieuwe claims te genereren. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformatie

Het element **OutputClaimsTransformation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een claimtransformatie die al is gedefinieerd in het beleidsbestand of het bovenliggende beleidsbestand. |

## <a name="validationtechnicalprofiles"></a>ValidatieTechnische profielen

Het element **ValidatieTechnischeProfielen** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| ValidatieTechnisch Profiel | 1:n | De id's van technische profielen die worden gebruikt, valideren sommige of alle uitvoerclaims van het verwijzende technische profiel. Alle inputclaims van het technische profiel waarnaar wordt verwezen, moeten worden weergegeven in de uitvoerclaims van het verwijzende technische profiel. |

### <a name="validationtechnicalprofile"></a>ValidatieTechnisch Profiel

Het element **ValidationTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel dat al is gedefinieerd in het beleidsbestand of het bovenliggende beleidsbestand. |

## <a name="subjectnaminginfo"></a>OnderwerpNaamgevingInfo

De **SubjectNamingInfo** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimType | Ja | Een id van een claimtype dat al is gedefinieerd in de sectie ClaimsSchema in het beleidsbestand. |

## <a name="includetechnicalprofile"></a>InclusiefTechnisch Profiel

Het element **IncludeTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel dat al is gedefinieerd in het beleidsbestand of het bovenliggende beleidsbestand. |

## <a name="usetechnicalprofileforsessionmanagement"></a>GebruikTechnicalProfileForSessionManagement

Het element **UseTechnicalProfileForSessionManagement** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel dat al is gedefinieerd in het beleidsbestand of het bovenliggende beleidsbestand. |

## <a name="enabledforuserjourneys"></a>IngeschakeldVoorGebruikersreizen

De **ClaimsProviderSelections** in een gebruikersreis definieert de lijst met selectieopties van claimprovideren en hun volgorde. Met het element **EnabledForUserJourneys** filtert u, welke claimprovider beschikbaar is voor de gebruiker. Het element **EnabledForUserJourneys** bevat een van de volgende waarden:

- **Altijd,** voer het technische profiel uit.
- **Sla nooit**het technische profiel over.
- **OnClaimsExistence** wordt alleen uitgevoerd wanneer een bepaalde claim, gespecificeerd in het technische profiel, bestaat.
- **OnItemExistInStringCollectionClaim**, alleen uitvoeren wanneer een item bestaat in een tekenreeksverzamelingclaim.
- **OnItemAbsenceInStringCollectionClaim** wordt alleen uitgevoerd wanneer een item niet bestaat in een tekenreeksverzamelingsclaim.

Met Behulp van **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** of **OnItemAbsenceInStringCollectionClaim**, vereist u de volgende metagegevens: **ClaimTypeOnWhichToEnable** geeft het type van de claim op dat moet worden geëvalueerd, **ClaimValueOnWhichToEnable** geeft de waarde op die moet worden vergeleken.

Het volgende technische profiel wordt alleen uitgevoerd als de `facebook.com` **tekenreeksverzameling identityProviders** de waarde bevat van :

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
