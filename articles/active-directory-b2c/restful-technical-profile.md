---
title: Een onderliggend technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een onderliggend technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b83a6bacf1c6e392db9dfc65fd737ea28416a6b5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183819"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een onderliggend technisch profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor uw eigen REST-service. Azure AD B2C verzendt gegevens naar de REST-service in een claim verzameling met invoer en ontvangt gegevens terug in een verzameling uitvoer claims. Met de ondersteunings integratie van de REST-service kunt u het volgende doen:

- **Gebruikers invoer valideren** : hiermee voor komt u dat er ongeldige gegevens worden bewaard in azure AD B2C. Als de waarde van de gebruiker ongeldig is, retourneert de REST-service een fout bericht dat de gebruiker de opdracht geeft een vermelding op te geven. U kunt bijvoorbeeld controleren of het e-mail adres van de gebruiker bestaat in de data base van uw klant.
- **Invoer claims overschrijven** : Hiermee kunt u de waarden in invoer claims opnieuw indelen. Als een gebruiker bijvoorbeeld de eerste naam in alle kleine letters of hoofd letters typt, kunt u de naam alleen met de eerste letter in een letter type Format teren.
- **Verrijkende gebruikers gegevens** : Hiermee kunt u verder integreren met zakelijke line-of-business-toepassingen. Zo kan uw REST-service bijvoorbeeld het e-mail adres van de gebruiker ontvangen, een query uitvoeren op de data base van de klant en het loyaliteits nummer van de gebruiker retour neren om Azure AD B2C. De retour claims kunnen worden opgeslagen, geëvalueerd in de volgende Orchestration-stappen of opgenomen in het toegangs token.
- **Aangepaste bedrijfs logica uitvoeren** : maakt het mogelijk om Push meldingen te verzenden, zakelijke data bases bij te werken, een gebruikers migratie proces uit te voeren, machtigingen te beheren, data bases te controleren en andere acties uit te voeren.

Uw beleid kan invoer claims naar uw REST API verzenden. De REST API kan ook uitvoer claims retour neren die u later in uw beleid kunt gebruiken of een fout bericht kan genereren. U kunt op de volgende manieren de integratie met de REST-services ontwerpen:

- **Validatie technische profiel** : een validatie technisch profiel roept de rest-service aan. Het validatie-technische profiel valideert de door de gebruiker verschafte gegevens voordat de gebruikers traject doorgaat. Met het technische profiel voor validatie wordt een fout bericht weer gegeven op een zelfbevestigende pagina en geretourneerd in uitvoer claims.
- **Claim uitwisseling** : er wordt een aanroep uitgevoerd naar de rest-service via een Orchestration-stap. In dit scenario is er geen gebruikers interface om het fout bericht weer te geven. Als uw REST API een fout retourneert, wordt de gebruiker teruggeleid naar de Relying Party-toepassing met het fout bericht.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary`. Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

In het volgende voor beeld wordt een onderliggend technisch profiel weer gegeven:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die moeten worden verzonden naar de rest API. U kunt de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in de REST API. In het volgende voor beeld ziet u de toewijzing tussen uw beleid en de REST API. De naam van **de claim wordt** verzonden naar de rest API als **FirstName**, **terwijl de achternaam wordt** verzonden als **LastName**. De **e-mail** claim is ingesteld als is.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de rest API worden verzonden.

## <a name="send-a-json-payload"></a>Een JSON-nettolading verzenden

Met het technische profiel van REST API kunt u een complexe JSON-nettolading verzenden naar een eind punt.

Een complexe JSON-nettolading verzenden:

1. Bouw uw JSON-nettolading met de [GenerateJson](json-transformations.md) -claim transformatie.
1. In het REST API technische profiel:
    1. Voeg een invoer claims transformatie toe met een verwijzing naar de trans formatie van de `GenerateJson` claims.
    1. Stel de optie voor het `SendClaimsIn` van meta gegevens in op `body`
    1. Stel de `ClaimUsedForRequestPayload` meta gegevens optie in op de naam van de claim met de JSON-nettolading.
    1. Voeg in de invoer claim een verwijzing toe naar de invoer claim met de JSON-nettolading.

In het volgende voor beeld `TechnicalProfile` een verificatie-e-mail verzenden met behulp van een e-mail service van derden (in dit geval SendGrid).

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

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die zijn geretourneerd door de rest API. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de REST API. U kunt ook claims toevoegen die niet worden geretourneerd door de REST API ID-provider, op voor waarde dat u het kenmerk `DefaultValue` hebt ingesteld.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

In het volgende voor beeld ziet u de claim die wordt geretourneerd door de REST API:

- De **MembershipId** -claim die is toegewezen aan de naam van de **loyaltyNumber** -claim.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de ID-provider:

- De **loyaltyNumberIsNew** -claim waarvoor een standaard waarde is ingesteld op `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ServiceUrl | Ja | De URL van het REST API-eind punt. |
| AuthenticationType | Ja | Het type verificatie dat wordt uitgevoerd door de claim provider voor de REST. Mogelijke waarden: `None`, `Basic`, `Bearer`of `ClientCertificate`. De waarde `None` geeft aan dat de REST API niet anoniem is. De waarde `Basic` geeft aan dat de REST API is beveiligd met HTTP Basic-verificatie. Alleen geverifieerde gebruikers, met inbegrip van Azure AD B2C, hebben toegang tot uw API. De waarde `ClientCertificate` (Recommended) geeft aan dat de REST API de toegang beperkt met behulp van verificatie op basis van client certificaten. Alleen services die de juiste certificaten hebben, bijvoorbeeld Azure AD B2C, hebben toegang tot uw API. De waarde `Bearer` geeft aan dat de REST API de toegang beperkt met behulp van de client OAuth2 Bearer-token. |
| SendClaimsIn | Nee | Hiermee wordt aangegeven hoe de invoer claims worden verzonden naar de claim provider voor de REST. Mogelijke waarden: `Body` (standaard), `Form`, `Header`of `QueryString`. De `Body` waarde is de invoer claim die wordt verzonden in de hoofd tekst van de aanvraag in de JSON-indeling. De `Form` waarde is de invoer claim die in de hoofd tekst van de aanvraag wordt verzonden in een ampersand ' & ' gescheiden sleutel waarde-indeling. De `Header` waarde is de invoer claim die in de aanvraag header wordt verzonden. De `QueryString` waarde is de invoer claim die wordt verzonden in de query teken reeks van de aanvraag. De HTTP-woorden die door elk van beide worden aangeroepen, zijn als volgt:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: ophalen</li><li>`QueryString`: ophalen</li></ul> |
| ClaimsFormat | Nee | Hiermee geeft u de indeling voor de uitvoer claims op. Mogelijke waarden: `Body` (standaard), `Form`, `Header`of `QueryString`. De `Body` waarde is de uitvoer claim die in de hoofd tekst van de aanvraag wordt verzonden in de JSON-indeling. De `Form` waarde is de uitvoer claim die in de hoofd tekst van de aanvraag wordt verzonden in een ampersand ' & ' gescheiden sleutel waarde-indeling. De `Header` waarde is de uitvoer claim die in de aanvraag header wordt verzonden. De `QueryString` waarde is de uitvoer claim die wordt verzonden in de query teken reeks van de aanvraag. |
| ClaimUsedForRequestPayload| Nee | Naam van een teken reeks claim die de payload bevat die naar de REST API moet worden verzonden. |
| DebugMode | Nee | Voert het technische profiel in de foutopsporingsmodus. Mogelijke waarden: `true`of `false` (standaard). In de foutopsporingsmodus kan de REST API meer informatie retour neren. Zie de sectie [fout bericht over het retour neren](#returning-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true`, of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true`. |
| ResolveJsonPathsInJsonTokens  | Nee | Hiermee wordt aangegeven of het technische profiel JSON-paden oplost. Mogelijke waarden: `true`of `false` (standaard). Gebruik deze meta gegevens om gegevens van een genest JSON-element te lezen. Stel in een [output claim](technicalprofiles.md#outputclaims)de `PartnerClaimType` in op het JSON-padcomponent-element dat u wilt uitvoeren. Bijvoorbeeld: `firstName.localized`of `data.0.to.0.email`.|

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Als het type verificatie is ingesteld op `None`, wordt het element **CryptographicKeys** niet gebruikt.

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

Als het type verificatie is ingesteld op `Basic`, bevat het element **CryptographicKeys** de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ja | De gebruikers naam die wordt gebruikt voor verificatie. |
| BasicAuthenticationPassword | Ja | Het wacht woord dat wordt gebruikt voor verificatie. |

In het volgende voor beeld ziet u een technisch profiel met basis verificatie:

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

Als het type verificatie is ingesteld op `ClientCertificate`, bevat het **CryptographicKeys** -element het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClientCertificate | Ja | Het x509-certificaat (RSA key set) dat moet worden gebruikt voor verificatie. |

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

Als het type verificatie is ingesteld op `Bearer`, bevat het **CryptographicKeys** -element het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Nee | Het OAuth 2,0 Bearer-token. |

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

## <a name="returning-error-message"></a>Fout bericht retour neren

Uw REST API moet mogelijk een fout bericht retour neren, zoals ' de gebruiker is niet gevonden in het CRM-systeem '. Als er een fout optreedt, moet de REST API een HTTP 409-fout bericht retour neren (status code voor de conflict reactie) met de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| version | Ja | 1.0.0 |
| status | Ja | 409 |
| code | Nee | Een fout code van de REST-eindpunt provider, die wordt weer gegeven wanneer `DebugMode` is ingeschakeld. |
| requestId | Nee | Een aanvraag-id van de REST-eindpunt provider, die wordt weer gegeven wanneer `DebugMode` is ingeschakeld. |
| userMessage | Ja | Een fout bericht dat wordt weer gegeven aan de gebruiker. |
| developerMessage | Nee | De uitgebreide beschrijving van het probleem en hoe het kan worden opgelost, dat wordt weer gegeven wanneer `DebugMode` is ingeschakeld. |
| moreInfo | Nee | Een URI die verwijst naar extra informatie, die wordt weer gegeven wanneer `DebugMode` is ingeschakeld. |

In het volgende voor beeld ziet u een REST API die een fout bericht retourneert dat is ingedeeld in JSON:

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

In het volgende voor beeld C# ziet u een-klasse die een fout bericht retourneert:

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

Raadpleeg de volgende artikelen voor voor beelden van het gebruik van een reactief technisch profiel:

- [REST API claims-uitwisselingen integreren in uw Azure AD B2C gebruikers traject als validatie van gebruikers invoer](rest-api-claims-exchange-dotnet.md)
- [Beveilig uw REST-services met behulp van HTTP-basis verificatie](secure-rest-api-dotnet-basic-auth.md)
- [Uw REST-service beveiligen met behulp van client certificaten](secure-rest-api-dotnet-certificate-auth.md)
- [Walkthrough: REST API claims-uitwisselingen integreren in uw Azure AD B2C gebruikers door voeren als validatie op gebruikers invoer](custom-policy-rest-api-claims-validation.md)
