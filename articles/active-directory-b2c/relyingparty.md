---
title: RelyingParty - Azure Active Directory B2C | Microsoft Documenten
description: Geef het element RelyingParty van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 221ed3169fff78a2721e91023036593570fbd723
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637798"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het element **RelyingParty** geeft het gebruikerstraject op dat moet worden afgedwongen voor de huidige aanvraag voor Azure Active Directory B2C (Azure AD B2C). Het specificeert ook de lijst van claims die de relying party (RP) applicatie nodig heeft als onderdeel van het uitgegeven token. Een RP-toepassing, zoals een web-, mobiele of desktoptoepassing, roept het RP-beleidsbestand aan. In het RP-beleidsbestand wordt een specifieke taak uitgevoerd, zoals aanmelden, het opnieuw instellen van een wachtwoord of het bewerken van een profiel. Meerdere toepassingen kunnen hetzelfde RP-beleid gebruiken en één toepassing kan meerdere beleidsregels gebruiken. Alle RP-toepassingen ontvangen hetzelfde token met claims en de gebruiker gaat door dezelfde gebruikersreis.

In het volgende voorbeeld wordt een **element RelyingParty** weergegeven in het *B2C_1A_signup_signin* beleidsbestand:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Het optionele **ElementingParty-element** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| StandaardUserJourney | 1:1 | Het standaardgebruikerstraject voor de RP-toepassing. |
| UserJourneyBehaviors | 0:1 | Het bereik van het gebruikerstrajectgedrag. |
| Technisch Profiel | 1:1 | Een technisch profiel dat wordt ondersteund door de RP-applicatie. Het technische profiel biedt een contract voor de RP-toepassing om contact op te nemen met Azure AD B2C. |

## <a name="defaultuserjourney"></a>StandaardUserJourney

Het `DefaultUserJourney` element geeft een verwijzing op naar de id van het gebruikerstraject dat gewoonlijk wordt gedefinieerd in het beleid Basis of Extensies. In de volgende voorbeelden wordt de aanmeldings- of aanmeldingsreis weergegeven die is opgegeven in het element **RelyingParty:**

*B2C_1A_signup_signin* beleid:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* of *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Het element **DefaultUserJourney** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van de gebruikersreis in het beleid. Zie [gebruikersreizen voor](userjourneys.md) meer informatie |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Het element **UserJourneyBehaviors** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Singlesignon Singlesignon | 0:1 | Het bereik van het sso-sessiegedrag (single sign-on) van een gebruikersreis. |
| SessionExpiryType |0:1 | Het verificatiegedrag van de sessie. Mogelijke `Rolling` waarden: `Absolute`of . De `Rolling` waarde (standaard) geeft aan dat de gebruiker blijft aangemeld zolang de gebruiker voortdurend actief is in de toepassing. De `Absolute` waarde geeft aan dat de gebruiker gedwongen wordt om opnieuw te verifiëren na de periode die is opgegeven door de levensduur van de toepassingssessie. |
| SessionExpiryInSeconds | 0:1 | De levensduur van de sessiecookie van Azure AD B2C is opgegeven als een geheel getal dat is opgeslagen in de browser van de gebruiker bij succesvolle verificatie. |
| ReisInzichten | 0:1 | De instrumentatiesleutel Azure Application Insights die moet worden gebruikt. |
| ContentDefinitionParameters | 0:1 | De lijst met sleutelwaardeparen die moeten worden toegevoegd aan de inhoudsdefinitie belasting URI. |
|ScriptExecution| 0:1| De ondersteunde [JavaScript-uitvoeringsmodi.](javascript-samples.md) Mogelijke `Allow` waarden: `Disallow` of (standaard).

### <a name="singlesignon"></a>Singlesignon Singlesignon

Het element **SingleSignOn** bevat in het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bereik | Ja | De reikwijdte van het enkele aanmeldingsgedrag. Mogelijke `Suppressed`waarden: `Tenant` `Application`, `Policy`, of . De `Suppressed` waarde geeft aan dat het gedrag wordt onderdrukt en dat de gebruiker altijd wordt gevraagd om een selectie van identiteitsprovideren.  De `Tenant` waarde geeft aan dat het gedrag wordt toegepast op alle beleidsregels in de tenant. Een gebruiker die bijvoorbeeld door twee beleidsreizen voor een tenant navigeert, wordt niet gevraagd om een selectie van een identiteitsprovider. De `Application` waarde geeft aan dat het gedrag wordt toegepast op alle beleidsregels voor de toepassing die de aanvraag maakt. Een gebruiker die bijvoorbeeld door twee beleidsreizen voor een toepassing navigeert, wordt niet gevraagd om een selectie van een identiteitsprovider. De `Policy` waarde geeft aan dat het gedrag alleen van toepassing is op een beleid. Een gebruiker die bijvoorbeeld door twee beleidsreizen voor een vertrouwenskader navigeert, wordt gevraagd om een selectie van identiteitsprovideren bij het schakelen tussen beleid. |
| KeepAliveinDays | Ja | Hiermee bepaalt u hoe lang de gebruiker is aangemeld. Als u de waarde instelt op 0, schakelt u de KMSI-functionaliteit uit. Zie [Mij ingetekend houden](custom-policy-keep-me-signed-in.md)voor meer informatie. |
|EnforceIdTokenHintOnLogout| Nee|  Forceer een eerder uitgegeven ID-token naar het afmeldpunt als een hint over de huidige geverifieerde sessie van de eindgebruiker met de client. Mogelijke waarden: `false` (standaard) of `true`. Zie [Webaanmelding met OpenID Connect](openid-connect.md)voor meer informatie.  |


## <a name="journeyinsights"></a>ReisInzichten

Het element **JourneyInsights** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| TelemetrieMotor | Ja | De waarde `ApplicationInsights`moet . |
| Instrumentatiesleutel | Ja | De tekenreeks die de instrumentatietoets bevat voor het element toepassingsinzichten. |
| DeveloperMode | Ja | Mogelijke `true` waarden: `false`of . Als `true`, versnelt Application Insights de telemetrie via de verwerkingspijplijn. Deze instelling is goed voor de ontwikkeling, maar beperkt bij hoge volumes De gedetailleerde activiteitslogboeken zijn alleen ontworpen om te helpen bij de ontwikkeling van aangepaste beleidsregels. Gebruik de ontwikkelingsmodus niet in de productie. Logs verzamelen alle claims die tijdens de ontwikkeling van en naar de identiteitsproviders worden verzonden. Als deze in de productie wordt gebruikt, neemt de ontwikkelaar de verantwoordelijkheid op zich voor PII (Privéidentificeerbare informatie) die wordt verzameld in het app-inzichtenlogboek waarvan hij eigenaar is. Deze gedetailleerde logboeken worden alleen verzameld `true`wanneer deze waarde is ingesteld op .|
| ClientIngeschakeld | Ja | Mogelijke `true` waarden: `false`of . Als `true`, stuurt de Application Insights client-side script voor het bijhouden van pagina weergave en client-side fouten. |
| ServerIngeschakeld | Ja | Mogelijke `true` waarden: `false`of . Als `true`, stuurt de bestaande UserJourneyRecorder JSON als een aangepaste gebeurtenis naar Application Insights. |
| TelemetrieVersie | Ja | De waarde `1.0.0`moet . |

Zie [Logboeken verzamelen voor](troubleshoot-with-application-insights.md) meer informatie

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Met behulp van aangepast beleid in Azure AD B2C u een parameter verzenden in een querytekenreeks. Door de parameter door te geven aan uw HTML-eindpunt, kunt u de pagina-inhoud dynamisch wijzigen. U kunt bijvoorbeeld de achtergrondafbeelding op de registratie- of aanmeldingspagina van Azure AD B2C wijzigen, op basis van een parameter die u doorgeeft vanuit uw web- of mobiele toepassing. Azure AD B2C geeft de querytekenreeksparameters door aan uw dynamische HTML-bestand, zoals het aspx-bestand.

In het volgende voorbeeld `campaignId` wordt een `hawaii` parameter met de naam waarde van in de querytekenreeks doorgegeven:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Het element **ContentDefinitionParameters** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Een tekenreeks die het sleutelwaardepaar bevat dat is toegevoegd aan de queryreeks van een inhoudsdefinitie, belast URI. |

Het element **ContentDefinitionParameter** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Name | Ja | De naam van het sleutelwaardepaar. |

Zie [De gebruikersinterface configureren met dynamische inhoud met aangepaste beleidsregels voor](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) meer informatie.

## <a name="technicalprofile"></a>Technisch Profiel

Het element **TechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | De waarde `PolicyProfile`moet . |

Het **TechnicalProfile** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | De tekenreeks die de naam van het technische profiel bevat. |
| Beschrijving | 0:1 | De tekenreeks die de beschrijving van het technische profiel bevat. |
| Protocol | 1:1 | Het protocol dat voor de federatie wordt gebruikt. |
| Metagegevens | 0:1 | Het verzamelen van *item* van sleutel-/waardeparen die door het protocol worden gebruikt voor het communiceren met het eindpunt in de loop van een transactie om de interactie tussen de relying party en andere deelnemers aan de community te configureren. |
| Uitvoerclaims | 1:1 | Een lijst met claimtypen die worden gebruikt als uitvoer in het technische profiel. Elk van deze elementen bevat een verwijzing naar een **ClaimType** dat al is gedefinieerd in de sectie **Claimschema** of in een beleid waaruit dit beleidsbestand overneemt. |
| OnderwerpNaamgevingInfo | 1:1 | De onderwerpnaam die in tokens wordt gebruikt. |

Het **element Protocol** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Name | Ja | De naam van een geldig protocol dat wordt ondersteund door Azure AD B2C dat wordt gebruikt als onderdeel van het technische profiel. Mogelijke `OpenIdConnect` waarden: `SAML2`of . De `OpenIdConnect` waarde vertegenwoordigt de OpenID Connect 1.0-protocolstandaard volgens de OpenID-funderingsspecificatie. De `SAML2` vertegenwoordigt de SAML 2.0 protocol standaard volgens OASIS specificatie. |

## <a name="outputclaims"></a>Uitvoerclaims

Het element **OutputClaims** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Uitvoerclaim | 0:n | De naam van een verwacht claimtype in de ondersteunde lijst voor het beleid waarop de relying party zich abonneert. Deze claim dient als output voor het technische profiel. |

Het element **OutputClaim** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Referentienummer van claimtype | Ja | Een verwijzing naar een **ClaimType** die al is gedefinieerd in de sectie **ClaimsSchema** in het beleidsbestand. |
| Standaardwaarde | Nee | Een standaardwaarde die kan worden gebruikt als de claimwaarde leeg is. |
| PartnerClaimType | Nee | Hiermee verzendt u de claim in een andere naam zoals geconfigureerd in de ClaimType-definitie. |

### <a name="subjectnaminginfo"></a>OnderwerpNaamgevingInfo

Met het element **SubjectNameingInfo** bepaalt u de waarde van het tokenonderwerp:
- **JWT token** `sub` - de claim. Dit is een principal waarover het token informatie claimt, zoals de gebruiker van een toepassing. Deze waarde is onveranderlijk en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het kan worden gebruikt om veilige autorisatiecontroles uit te voeren, bijvoorbeeld wanneer het token wordt gebruikt om toegang te krijgen tot een bron. Standaard wordt de onderwerpclaim gevuld met de object-id van de gebruiker in de map. Zie [Token, sessie en eenmalige aanmeldingsconfiguratie](session-behavior.md)voor meer informatie.
- **SAML-token** `<Subject><NameID>` - het element dat het onderwerpelement identificeert.

Het element **SubjectNamingInfo** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimType | Ja | Een verwijzing naar de **PartnerClaimType**van een uitvoerclaim . De uitvoerclaims moeten worden gedefinieerd in de **verzameling OutputClaims van relying** party. |

In het volgende voorbeeld ziet u hoe u een afhankelijke partij van OpenID Connect definieert. De informatie over de onderwerpnaam is geconfigureerd als : `objectId`

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Het JWT-token `sub` bevat de claim met de objectid van de gebruiker:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
