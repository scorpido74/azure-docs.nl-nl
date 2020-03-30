---
title: UserJourneys | Microsoft Documenten
description: Geef het element UserJourneys van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227008"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Gebruikersreizen specificeren expliciete paden waardoor een toepassing van een relying party door een beleid de gewenste claims voor een gebruiker kan verkrijgen. De gebruiker wordt door deze paden geleid om de claims op te halen die aan de relying party moeten worden gepresenteerd. Met andere woorden, gebruikersreizen definiëren de bedrijfslogica van wat een eindgebruiker doormaakt terwijl het Azure AD B2C Identity Experience Framework de aanvraag verwerkt.

Deze gebruikersreizen kunnen worden beschouwd als sjablonen die beschikbaar zijn om te voldoen aan de kernbehoefte van de verschillende vertrouwende partijen van de gemeenschap van belang. Gebruikersreizen vergemakkelijken de definitie van het relying party deel van een beleid. Een beleid kan meerdere gebruikersreizen definiëren. Elke gebruikersreis is een opeenvolging van orkestratiestappen.

Als u de gebruikersreizen wilt definiëren die door het beleid worden ondersteund, wordt een **UserJourneys-element** toegevoegd onder het element op het hoogste niveau van het beleidsbestand.

Het element **UserJourneys** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Een gebruikersreis die alle constructies definieert die nodig zijn voor een volledige gebruikersstroom. |

Het element **UserJourney** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id van een gebruikersreis die kan worden gebruikt om ernaar te verwijzen vanuit andere elementen in het beleid. Het element **DefaultUserJourney** van het beleid van de [relying party](relyingparty.md) verwijst naar dit kenmerk. |

Het element **UserJourney** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| OrkestratieStappen | 1:n | Een orkestratiereeks die moet worden gevolgd voor een succesvolle transactie. Elke gebruikersreis bestaat uit een geordende lijst van orkestratiestappen die achter elkaar worden uitgevoerd. Als een stap mislukt, mislukt de transactie. |

## <a name="orchestrationsteps"></a>OrkestratieStappen

Een gebruikersreis wordt weergegeven als een orkestratiereeks die moet worden gevolgd voor een succesvolle transactie. Als een stap mislukt, mislukt de transactie. Deze orkestratiestappen verwijzen naar zowel de bouwstenen als de in het beleidsbestand toegestane claimproviders. Elke orchestration-stap die verantwoordelijk is voor het weergeven of weergeven van een gebruikerservaring, heeft ook een verwijzing naar de bijbehorende inhoudsdefinitie-id.

Orkestratiestappen kunnen voorwaardelijk worden uitgevoerd op basis van randvoorwaarden die zijn gedefinieerd in het element orchestration step. U bijvoorbeeld alleen controleren of een orchestration-stap wordt uitgevoerd als er een specifieke claim bestaat of als een claim gelijk is of niet aan de opgegeven waarde.

Als u de geordende lijst met orchestration-stappen wilt opgeven, wordt een element **OrchestrationSteps** toegevoegd als onderdeel van het beleid. Dit element is vereist.

Het element **OrchestrationSteps** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| OrkestratieStap | 1:n | Een geordende orkestratiestap. |

Het element **OrchestrationStep** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Order` | Ja | De volgorde van de orkestratiestappen. |
| `Type` | Ja | Het type van de orkestratiestap. Mogelijke waarden: <ul><li>**ClaimsProviderSelection** - Geeft aan dat de orchestration stap presenteert verschillende claims providers aan de gebruiker om er een te selecteren.</li><li>**CombinedSignInAndSignUp** - Geeft aan dat de orchestration-stap een gecombineerde aanmeldingspagina voor sociale aanbieders en lokale aanmeldingspagina's voor sociale aanbieders presenteert.</li><li>**ClaimsExchange** - Geeft aan dat de orchestration stap claims uitwisselt met een claimprovider.</li><li>**GetClaims** - Geeft aan dat de orchestration-stap de invoerclaims leest.</li><li>**SendClaims** - Geeft aan dat de orchestration-stap de claims naar de relying party stuurt met een token uitgegeven door een claimuitgever.</li></ul> |
| ContentDefinitionReferenceId | Nee | De id van de [inhoudsdefinitie](contentdefinitions.md) die aan deze orchestration-stap is gekoppeld. Meestal wordt de referentie-id voor inhoudsdefinitie gedefinieerd in het zelfverklaarde technische profiel. Er zijn echter enkele gevallen waarin Azure AD B2C iets moet weergeven zonder een technisch profiel. Er zijn twee voorbeelden - als het type van de `ClaimsProviderSelection` `CombinedSignInAndSignUp`orchestration-stap een van de volgende is: of , Azure AD B2C moet de selectie van de identiteitsprovider weergeven zonder een technisch profiel te hebben. |
| CpimIssuerTechnicalProfileReferenceId | Nee | Het type van de `SendClaims`orkestratiestap is . Deze eigenschap definieert de technische profiel-id van de claimprovider die het token uitgeeft voor de relying party.  Als er afwezig is, wordt er geen token van relying party gemaakt. |


Het element **OrchestrationStep** kan de volgende elementen bevatten:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Voorwaarden | 0:n | Een lijst van randvoorwaarden waaraan moet worden voldaan om de orkestratiestap uit te voeren. |
| ClaimsProviderSelecties | 0:n | Een lijst met selecties van schadeverlener voor de orkestratiestap. |
| ClaimsExchanges | 0:n | Een lijst van claims uitwisselingen voor de orkestratie stap. |

### <a name="preconditions"></a>Voorwaarden

Het element **Randvoorwaarden** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Voorwaarde | 1:n | Afhankelijk van het gebruikte technische profiel, verwijst u de client om naar de selectie van de claimprovider of voert een serveroproep om claims uit te wisselen. |


#### <a name="precondition"></a>Voorwaarde

Het element **Voorwaarde** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Type` | Ja | Het type controle of query dat moet worden uitgevoerd voor deze voorwaarde. De waarde kan **ClaimsExist**zijn , wat aangeeft dat de acties moeten worden uitgevoerd als de opgegeven claims bestaan in de huidige claimset van de gebruiker, of **ClaimEquals**, die aangeeft dat de acties moeten worden uitgevoerd als de opgegeven claim bestaat en de waarde ervan gelijk is aan de opgegeven waarde. |
| `ExecuteActionsIf` | Ja | Gebruik een echte of valse test om te beslissen of de acties in de voorwaarde moeten worden uitgevoerd. |

De **elementen Voorwaarde** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Waarde | 1:n | Een ClaimTypeReferenceId waarvoor u moet worden opgevraagd. Een ander waardeelement bevat de waarde die moet worden gecontroleerd.</li></ul>|
| Actie | 1:1 | De actie die moet worden uitgevoerd als de voorwaarde controle binnen een orchestration stap waar is. Als de waarde `Action` van `SkipThisOrchestrationStep`de waarde `OrchestrationStep` is ingesteld op, mag de bijbehorende niet worden uitgevoerd. |

#### <a name="preconditions-examples"></a>Voorbeelden van randvoorwaarden

De volgende randvoorwaarden controleren of de objectId van de gebruiker bestaat. In het gebruikerstraject heeft de gebruiker ervoor gekozen om zich aan te melden met een lokaal account. Als de objectId bestaat, slaat u deze orchestration-stap over.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

De volgende voorwaarden controleren of de gebruiker zich heeft aangemeld met een sociaal account. Er wordt geprobeerd het gebruikersaccount in de map te vinden. Als de gebruiker zich aanmeldt of zich aanmeldt met een lokaal account, slaat u deze orchestration-stap over.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Randvoorwaarden kunnen meerdere randvoorwaarden controleren. In het volgende voorbeeld wordt gecontroleerd of er 'objectId' of 'e-mail' bestaat. Als de eerste voorwaarde waar is, wordt de reis overgeslagen naar de volgende orkestratiestap.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelectie

Een orchestration-stap `ClaimsProviderSelection` `CombinedSignInAndSignUp` van het type of kan een lijst met claimsproviders bevatten waarmee een gebruiker zich kan aanmelden. De volgorde van de `ClaimsProviderSelections` elementen in de elementen bepaalt de volgorde van de identiteitsproviders die aan de gebruiker worden gepresenteerd.

Het element **ClaimsProviderSelections** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| ClaimsProviderSelectie | 1:n | Hiermee biedt u de lijst met claimproviders die kunnen worden geselecteerd.|

Het element **ClaimsProviderSelections** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Weergaveoptie| Nee | Hiermee bepaalt u het gedrag van een aanvraag waarin één selectie van een claimprovider beschikbaar is. Mogelijke waarden: `DoNotShowSingleProvider` (standaard) , wordt de gebruiker direct doorgestuurd naar de federatieve identiteitsprovider. Of `ShowSingleProvider` Azure AD B2C presenteert de aanmeldingspagina met de selectie van de enkele identiteitsprovider. Als u dit kenmerk wilt gebruiken, moet de [inhoudsdefinitieversie](page-layout.md) en hoger zijn. `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0`|

Het element **ClaimsProviderSelection** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId TargetClaimsExchangeId | Nee | De id van de claimuitwisseling, die wordt uitgevoerd in de volgende orchestration-stap van de selectie van de claimprovider. Dit kenmerk of het kenmerk ValidationClaimsExchangeId moeten worden opgegeven, maar niet beide. |
| ValidatieClaimsExchangeId | Nee | De id van de claimuitwisseling, die wordt uitgevoerd in de huidige orchestration-stap om de selectie van de claimprovider te valideren. Dit kenmerk of het kenmerk TargetClaimsExchangeId moeten worden opgegeven, maar niet beide. |

### <a name="claimsproviderselection-example"></a>Voorbeeld van ClaimsProviderSelection

In de volgende orkestratiestap kan de gebruiker ervoor kiezen om zich aan te melden bij Facebook, LinkedIn, Twitter, Google of een lokaal account. Als de gebruiker een van de aanbieders van sociale identiteit selecteert, wordt de `TargetClaimsExchangeId` tweede orkestratiestap uitgevoerd met de geselecteerde claimuitwisseling die in het kenmerk is opgegeven. De tweede orkestratiestap leidt de gebruiker door naar de aanbieder van sociale identiteit om het aanmeldingsproces te voltooien. Als de gebruiker ervoor kiest zich aan te melden bij het lokale account, blijft Azure AD B2C op dezelfde orchestration-stap (dezelfde aanmeldingspagina of aanmeldingspagina) en slaat u de tweede orkestratiestap over.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
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
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

Het element **ClaimsExchanges** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | Afhankelijk van het gebruikte technische profiel leidt u de client om volgens de geselecteerde ClaimsProviderSelection of voert u een serveroproep om claims uit te wisselen. |

Het element **ClaimsExchange** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id van de stap voor het uitwisselen van claims. De id wordt gebruikt om te verwijzen naar de claimuitwisseling van een selectiestap van een claimprovider in het beleid. |
| Technisch profielReferentieid | Ja | De id van het technische profiel dat moet worden uitgevoerd. |
