---
title: REST API claimt uitwisselingen in B2C-beleid op maat
titleSuffix: Azure AD B2C
description: Een inleiding tot het maken van een Azure AD B2C-gebruikersreis die samenwerkt met RESTful-services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337413"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Rest API-claims uitwisselingen integreren in uw Azure AD B2C-aangepaste beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het Identity Experience Framework, dat ten grondslag ligt aan Azure Active Directory B2C (Azure AD B2C), kan worden geïntegreerd met RESTful API's binnen een gebruikersreis. In dit artikel ziet u hoe u een gebruikersreis maakt die samenwerkt met een RESTful-service met behulp van een [RESTful technisch profiel.](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)

Met Azure AD B2C u uw eigen bedrijfslogica toevoegen aan een gebruikersreis door uw eigen RESTful-service te bellen. Het Identity Experience Framework kan gegevens van uw RESTful-service verzenden en ontvangen om claims uit te wisselen. U kunt bijvoorbeeld:

- **Gebruikersinvoergegevens valideren**. U bijvoorbeeld controleren of het e-mailadres dat door de gebruiker wordt opgegeven in de database van uw klant bestaat en zo niet, een fout bevatten.
- **Procesclaims**. Als een gebruiker zijn voornaam in alle kleine letters of alle hoofdletters invoert, kan uw REST API de naam opmaken met alleen de eerste letter met als hoofdletter en deze retourneren naar Azure AD B2C.
- **Verrijk gebruikersgegevens door verder te integreren met bedrijfsapplicaties.** Uw RESTful-service kan het e-mailadres van de gebruiker ontvangen, de database van de klant opvragen en het loyaliteitsnummer van de gebruiker terugsturen naar Azure AD B2C. Vervolgens kunnen retourclaims worden opgeslagen in het Azure AD-account van de gebruiker, geëvalueerd in de volgende orkestratiestappen of worden opgenomen in het toegangstoken.
- **Aangepaste bedrijfslogica uitvoeren**. U pushmeldingen verzenden, bedrijfsdatabases bijwerken, een gebruikersmigratieproces uitvoeren, machtigingen beheren, databases controleren en andere werkstromen uitvoeren.

![Diagram van een RESTful service claims exchange](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Een RESTful-service bellen

De interactie omvat een informatie-uitwisseling van claims tussen de REST API-claims en Azure AD B2C. U de integratie met de RESTful-services op de volgende manieren ontwerpen:

- **Technisch profiel validatie**. De oproep naar de RESTful-service vindt plaats binnen een [validatietechnisch profiel](validation-technical-profile.md) van het opgegeven [zelfverklaarde technische profiel](self-asserted-technical-profile.md)of een [verificatieweergavecontrole](display-control-verification.md) van een [weergavebesturingselement](display-controls.md). Het validatietechnische profiel valideert de door de gebruiker verstrekte gegevens voordat de gebruikersreis verder gaat. Met het validatietechnische profiel u:

  - Stuur claims naar uw REST API.
  - Claimen valideren en aangepaste foutberichten plaatsen die aan de gebruiker worden weergegeven.
  - Stuur claims van de REST API terug naar de volgende orkestratiestappen.

- **Claims uitwisseling**. Een directe claimuitwisseling kan worden geconfigureerd door rechtstreeks vanuit een orchestration-stap van een [gebruikersreis](userjourneys.md)een technische REST API-profiel aan te roepen. Deze definitie is beperkt tot:

  - Stuur claims naar uw REST API.
  - Claimen en aangepaste foutberichten plaatsen die naar de toepassing worden teruggestuurd.
  - Stuur claims van de REST API terug naar de volgende orkestratiestappen.

U een REST API-aanroep toevoegen bij elke stap in het gebruikerstraject dat wordt gedefinieerd door een aangepast beleid. U bijvoorbeeld een REST API aanroepen:

- Tijdens het aanmelden, net voordat Azure AD B2C de referenties valideert.
- Direct na het aanmelden.
- Voordat Azure AD B2C een nieuw account in de map maakt.
- Na Azure maakt AD B2C een nieuw account in de map.
- Voordat Azure AD B2C een toegangstoken uitgeeft.

![Technische profielverzameling valideren](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Gegevens verzenden

In het [technische profiel RESTful](restful-technical-profile.md)bevat het `InputClaims` element een lijst met claims die naar uw RESTful-service moeten worden verzonden. U de naam van uw claim toewijzen aan de naam die is gedefinieerd in de RESTful-service, een standaardwaarde instellen en [claimresolvers](claim-resolver-overview.md)gebruiken.

U configureren hoe de invoerclaims naar de RESTful-claimprovider worden verzonden met behulp van het kenmerk SendClaimsIn. De mogelijke waarden zijn:

- **Body**, verzonden in de HTTP POST-aanvraaginstantie in JSON-indeling.
- **Formulier**, verzonden in de HTTP POST-aanvraaginstantie in een meteen '&' gescheiden sleutelwaardeformaat.
- **Koptekst**, verzonden in de koptekst van de HTTP GET-aanvraag.
- **QueryString**, verzonden in de querytekenreeks HTTP GET-aanvraag.

Wanneer de **optie Body** is geconfigureerd, u met het technische profiel VAN de REST API een complex JSON-laadvermogen naar een eindpunt verzenden. Zie [Een JSON-payload verzenden voor](restful-technical-profile.md#send-a-json-payload)meer informatie.

## <a name="receiving-data"></a>Gegevens ontvangen

Het `OutputClaims` element van het [technische profiel RESTful](restful-technical-profile.md) bevat een lijst met claims die zijn geretourneerd door de REST API. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de REST-API. U ook claims opnemen die niet worden geretourneerd door de REST API-identiteitsprovider, zolang u het kenmerk DefaultValue instelt.

De output claims ontleed door de RESTful claims provider altijd verwachten dat een platte JSON Body reactie ontleden, zoals:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

De uitvoerclaims moeten er als volgt uitzien:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Als u een geneste JSON-bodyrespons wilt ontleden, stelt u de metagegevens van ResolveJsonPathsInJsonTokens in op true. Stel in de uitvoerclaim het PartnerClaimType in op het JSON-padelement dat u wilt uitvoeren.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


De uitvoerclaims moeten er als volgt uitzien:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Beveiligingsoverwegingen

U moet uw REST API-eindpunt beschermen, zodat alleen geverifieerde clients ermee kunnen communiceren. De REST API moet een HTTPS-eindpunt gebruiken. Stel de metagegevens van AuthenticationType in op een van de volgende verificatiemethoden:

- **Clientcertificaat** beperkt de toegang met behulp van clientcertificaatverificatie. Alleen services met de juiste certificaten hebben toegang tot uw API. U slaat het clientcertificaat op in een Azure AD B2C-beleidssleutel. Meer informatie over het [beveiligen van uw RESTful-service met clientcertificaten.](secure-rest-api.md#https-client-certificate-authentication)
- **Basic** beveiligt de REST API met HTTP basic authentication. Alleen geverifieerde gebruikers, waaronder Azure AD B2C, hebben toegang tot uw API. De gebruikersnaam en het wachtwoord worden opgeslagen in Azure AD B2C-beleidssleutels. Meer informatie over het [beveiligen van uw RESTful-services met http-basisverificatie.](secure-rest-api.md#http-basic-authentication)
- **Drager** beperkt de toegang met behulp van een client OAuth2 access token. Het toegangstoken wordt opgeslagen in een Azure AD B2C-beleidssleutel. Meer informatie over het [beveiligen van uw RESTful-service met behulp van Bearer-token.](secure-rest-api.md#oauth2-bearer-authentication)

## <a name="rest-api-platform"></a>REST API-platform
Uw REST API kan worden gebaseerd op elk platform en geschreven in elke programmeertaal, zolang deze veilig is en claims kan verzenden en ontvangen zoals gespecificeerd in [restful technisch profiel.](restful-technical-profile.md)

## <a name="localize-the-rest-api"></a>De REST-API lokaliseren
In een RESTful technisch profiel u de taal/landing van de huidige sessie verzenden en indien nodig een gelokaliseerd foutbericht insturen. Met behulp van de [claimresolver](claim-resolver-overview.md)u een contextuele claim verzenden, zoals de gebruikerstaal. In het volgende voorbeeld wordt een RESTful technisch profiel weergegeven dat dit scenario aantoont.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Foutberichten verwerken

Uw REST-API moet mogelijk een foutbericht retourneren, zoals 'De gebruiker is niet in het CRM-systeem gevonden'. Als er een fout optreedt, moet de REST API een HTTP 409-foutbericht (Statuscode conflictrespons) retourneren. Zie voor meer informatie het [technische profiel van RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Dit kan alleen worden bereikt door een REST API technisch profiel aan te roepen vanuit een validatietechnisch profiel. Hierdoor kan de gebruiker de gegevens op de pagina corrigeren en de validatie opnieuw uitvoeren bij het indienen van de pagina.

Een HTTP 409-antwoord is vereist om te voorkomen dat eventuele latere validatietechnische profielen binnen deze orkestratiestap worden verwerkt.

Als u rechtstreeks vanuit een gebruikersreis naar een REST API-technisch profiel verwijst, wordt de gebruiker met het desbetreffende foutbericht teruggestuurd naar de toepassing van de relying party.

## <a name="publishing-your-rest-api"></a>Uw REST-API publiceren

De aanvraag voor uw REST API-service is afkomstig van Azure AD B2C-servers. De REST API-service moet worden gepubliceerd naar een openbaar toegankelijk HTTPS-eindpunt. De REST API-aanroepen komen van een IP-adres van een Azure-datacenter.

Ontwerp uw REST API-service en de onderliggende componenten (zoals de database en het bestandssysteem) om zeer beschikbaar te zijn.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor voorbeelden van het gebruik van een RESTful technisch profiel:

- [Walkthrough: Integratie van REST API-claims uitwisselingen in uw Azure AD B2C-gebruikersreis als validatie van gebruikersinvoer](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: Rest API-claims uitwisselingen toevoegen aan aangepast beleid in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Uw REST API-services beveiligen](secure-rest-api.md)
- [Referentie: RESTful technisch profiel](restful-technical-profile.md)