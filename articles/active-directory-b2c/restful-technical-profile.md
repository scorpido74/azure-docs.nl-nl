---
title: Een RESTful technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een RESTful technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332609"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een RESTful-technisch profiel definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het integreren van uw eigen RESTful-service. Azure AD B2C verzendt gegevens naar de RESTful-service in een invoerclaimverzameling en ontvangt gegevens terug in een verzameling uitvoerclaims. Zie [Rest API-claims uitwisselingen integreren in uw aangepaste Azure AD B2C-beleid](custom-policy-rest-api-intro.md)voor meer informatie.  

## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocolhandlerassemblage `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`die wordt gebruikt door Azure AD B2C: .

In het volgende voorbeeld ziet u een restful technisch profiel:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Invoerclaims

Het element **InputClaims** bevat een lijst met claims die naar de REST-API moeten worden verzonden. U de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in de REST API. In het volgende voorbeeld wordt de toewijzing tussen uw beleid en de REST-API weergegeven. De **givenName-claim** wordt naar de REST API verzonden als **firstName**, terwijl **de achternaam** wordt verzonden als **achternaam**. De **e-mailclaim** is ingesteld zoals het is.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Het element **InputClaimsTransformations** kan een verzameling **inputclaimstransformatie-elementen** bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat ze naar de REST-API worden verzonden.

## <a name="send-a-json-payload"></a>Stuur een JSON payload

Met het technische profiel VAN de REST API u een complexe JSON-payload naar een eindpunt sturen.

Ga als lid van het werk om een complexe JSON-payload te verzenden:

1. Bouw je JSON-payload met de [GenerateJson-claimtransformatie.](json-transformations.md)
1. In het technische profiel VAN de REST API:
    1. Voeg een transformatie van invoerclaims `GenerateJson` toe met een verwijzing naar de claimtransformatie.
    1. De `SendClaimsIn` optie metagegevens instellen op`body`
    1. Stel `ClaimUsedForRequestPayload` de optie metagegevens in op de naam van de claim die de JSON-payload bevat.
    1. Voeg in de invoerclaim een verwijzing toe naar de invoerclaim die de JSON-payload bevat.

In het `TechnicalProfile` volgende voorbeeld wordt een verificatie-e-mail verzonden met behulp van een e-mailservice van derden (in dit geval SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Outputclaims

Het element **OutputClaims** bevat een lijst met claims die zijn geretourneerd door de REST API. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de REST-API. U ook claims opnemen die niet worden geretourneerd door de REST `DefaultValue` API-identiteitsprovider, zolang u het kenmerk instelt.

Het element **OutputClaimsTransformations** kan een verzameling **outputclaimstransformatie-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe claims te genereren.

In het volgende voorbeeld wordt de claim weergegeven die is geretourneerd door de REST API:

- De **claim MembershipId** die is toegewezen aan de naam van de **loyaliteitsclaimNummer.**

Het technische profiel retourneert ook claims, die niet worden geretourneerd door de identiteitsprovider:

- De **claim loyaltyNumberIsNew** met een `true`standaardwaarde ingesteld op .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ServiceUrl (ServiceUrl) | Ja | De URL van het REST API-eindpunt. |
| AuthenticationType | Ja | Het type verificatie dat wordt uitgevoerd door de RESTful-claimprovider. Mogelijke `None`waarden: `Basic` `Bearer`, `ClientCertificate`, of . De `None` waarde geeft aan dat de REST API niet anoniem is. De `Basic` waarde geeft aan dat de REST API is beveiligd met HTTP basic authentication. Alleen geverifieerde gebruikers, waaronder Azure AD B2C, hebben toegang tot uw API. De `ClientCertificate` (aanbevolen) waarde geeft aan dat de REST API de toegang beperkt met behulp van clientcertificaatverificatie. Alleen services met de juiste certificaten, bijvoorbeeld Azure AD B2C, hebben toegang tot uw API. De `Bearer` waarde geeft aan dat de REST API de toegang beperkt met behulp van client OAuth2 Bearer token. |
| AllowInsecureAuthInProduction| Nee| Hiermee geeft `AuthenticationType` u aan `none` of deze`DeploymentMode` kan worden ingesteld in `Production`de productieomgeving (van het [Vertrouwenskaderbeleid](trustframeworkpolicy.md) is ingesteld op of niet is opgegeven). Mogelijke waarden: waar of onwaar (standaard). |
| Claimsin verzenden | Nee | Hiermee geeft u op hoe de invoerclaims naar de RESTful-claimprovider worden verzonden. Mogelijke `Body` waarden: `Form`(standaard), , `Header`of `QueryString`. De `Body` waarde is de invoerclaim die in de aanvraaginstantie in JSON-indeling wordt verzonden. De `Form` waarde is de invoerclaim die in de aanvraaginstantie wordt verzonden in een gescheiden sleutelwaardenotatie van ampersand '&'. De `Header` waarde is de invoerclaim die wordt verzonden in de aanmeldingskop. De `QueryString` waarde is de invoerclaim die wordt verzonden in de querytekenreeks voor aanvragen. De DOOR elk van beide http-werkwoorden worden als volgt aangeroepen:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Nee | Momenteel niet gebruikt, kan worden genegeerd. |
| ClaimUsedForRequestPayload| Nee | Naam van een tekenreeksclaim die de payload bevat die naar de REST-API moet worden verzonden. |
| Foutopsporingsmodus | Nee | Voert het technische profiel uit in de foutopsporingsmodus. Mogelijke waarden: `true` `false` , of (standaard). In de foutopsporingsmodus kan de REST API meer informatie retourneren. Zie de sectie [Foutbericht retourneren.](#returning-error-message) |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer- en uitvoerclaims geeft u aan of [schadeafhandeling](claim-resolver-overview.md) is opgenomen in het technische profiel. Mogelijke waarden: `true` `false`  , of (standaard). Als u een claimresolver in het technische profiel `true`wilt gebruiken, stelt u dit in op . |
| ResolveJsonPathsInJsonTokens  | Nee | Geeft aan of het technische profiel JSON-paden oplost. Mogelijke waarden: `true` `false` , of (standaard). Gebruik deze metagegevens om gegevens uit een genest JSON-element te lezen. Stel in een [OutputClaim](technicalprofiles.md#outputclaims)het `PartnerClaimType` JSON-padelement in dat u wilt uitvoeren. Bijvoorbeeld: `firstName.localized`, `data.0.to.0.email`of .|
| UseClaimasBearerToken| Nee| De naam van de claim die het token aan toonder bevat.|

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Als het type verificatie `None`is ingesteld op , wordt het element **CryptographicKeys** niet gebruikt.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Als het type verificatie `Basic`is ingesteld op , bevat het element **CryptographicKeys** de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| BasicAuthenticationGebruikersnaam | Ja | De gebruikersnaam die wordt gebruikt om te verifiëren. |
| BasicAuthenticationPassword BasicAuthenticationPassword | Ja | Het wachtwoord dat wordt gebruikt om te verifiëren. |

In het volgende voorbeeld wordt een technisch profiel met basisverificatie weergegeven:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Als het type verificatie `ClientCertificate`is ingesteld op , bevat het element **CryptographicKeys** het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Clientcertificaat | Ja | De X509-certificaat (RSA-sleutelset) die moet worden gebruikt om te verifiëren. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Als het type verificatie `Bearer`is ingesteld op , bevat het element **CryptographicKeys** het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Nee | De OAuth 2.0 bearer token. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Foutbericht retourneren

Uw REST API moet mogelijk een foutbericht retourneren, zoals 'De gebruiker is niet in het CRM-systeem gevonden'. Als er een fout optreedt, moet de REST API een HTTP 4xx-foutbericht retourneren, zoals 400 (slechte aanvraag) of 409 (conflict)-statuscode. De antwoordtekst bevat een foutbericht dat is opgemaakt in JSON:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| versie | Ja | Uw REST API-versie. Bijvoorbeeld: 1.0.1 |
| status | Ja | Moet 409 zijn |
| code | Nee | Een foutcode van de RESTful-eindpuntprovider, `DebugMode` die wordt weergegeven wanneer is ingeschakeld. |
| requestId | Nee | Een aanvraag-id van de RESTful-eindpuntprovider, `DebugMode` die wordt weergegeven wanneer is ingeschakeld. |
| userMessage | Ja | Een foutbericht dat aan de gebruiker wordt weergegeven. |
| ontwikkelaarMessage | Nee | De uitgebreide beschrijving van het probleem en hoe het `DebugMode` op te lossen, die wordt weergegeven wanneer is ingeschakeld. |
| meerInfo | Nee | Een URI die wijst op aanvullende informatie, die wordt weergegeven wanneer `DebugMode` is ingeschakeld. |


In het volgende voorbeeld wordt een klasse C#weergegeven die een foutbericht retourneert:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor voorbeelden van het gebruik van een RESTful technisch profiel:

- [Rest API-claims uitwisselingen integreren in uw Azure AD B2C-aangepaste beleid](custom-policy-rest-api-intro.md)
- [Walkthrough: Integratie van REST API-claims uitwisselingen in uw Azure AD B2C-gebruikersreis als validatie van gebruikersinvoer](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: Rest API-claims uitwisselingen toevoegen aan aangepast beleid in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Uw REST API-services beveiligen](secure-rest-api.md)

