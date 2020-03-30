---
title: Overzicht van technische profielen in aangepast beleid
titleSuffix: Azure AD B2C
description: Ontdek hoe technische profielen worden gebruikt in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057319"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Informatie over technische profielen in het aangepaste azure directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een technisch profiel biedt een framework met een ingebouwd mechanisme om te communiceren met verschillende soorten partijen met behulp van een aangepast beleid in Azure Active Directory B2C (Azure AD B2C). Technische profielen worden gebruikt om te communiceren met uw Azure AD B2C-tenant, om een gebruiker te maken of om een gebruikersprofiel te lezen. Een technisch profiel kan zelf worden geclaimd om interactie met de gebruiker mogelijk te maken. Verzamel bijvoorbeeld de referenties van de gebruiker om zich aan te melden en maak vervolgens de aanmeldingspagina of pagina met wachtwoordreset weer.

## <a name="type-of-technical-profiles"></a>Type technische profielen

Een technisch profiel maakt dit soort scenario's mogelijk:

- [Application Insights](application-insights-technical-profile.md) - Gebeurtenisgegevens verzenden naar [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) - Biedt ondersteuning voor het Azure Active Directory B2C-gebruikersbeheer.
- [Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md) - biedt ondersteuning voor het verifiëren van een telefoonnummer met behulp van Azure Multi-Factor Authentication (MFA). 
- [Claimstransformatie](claims-transformation-technical-profile.md) - Claimclaimtransformaties voor aanvragen om claimwaarden te manipuleren, claims te valideren of standaardwaarden in te stellen voor een reeks uitvoerclaims.
- [JWT-tokenuitgever](jwt-issuer-technical-profile.md) - zendt een JWT-token uit dat wordt teruggestuurd naar de toepassing van de relying party.
- [OAuth1](oauth1-technical-profile.md) - Federatie met een OAuth 1.0 protocol identiteit provider.
- [OAuth2](oauth2-technical-profile.md) - Federatie met een OAuth 2.0 protocol identiteit provider.
- [Eenmalig wachtwoord](one-time-password-technical-profile.md) - Biedt ondersteuning voor het beheren van het genereren en verifiëren van een eenmalig wachtwoord.
- [OpenID Connect](openid-connect-technical-profile.md) - Federatie met elke OpenID Connect-protocolidentiteitsprovider.
- [Telefoonfactor](phone-factor-technical-profile.md) - Ondersteuning voor het inschrijven en verifiëren van telefoonnummers.
- [RESTful-provider](restful-technical-profile.md) - Call to REST API-services, zoals het valideren van gebruikersinvoer, het verrijken van gebruikersgegevens of het integreren met line-of-business-toepassingen.
- [SAML2](saml-technical-profile.md) - Federatie met elke SAML-protocol identiteitsprovider.
- [SAML-tokenuitgever](saml-issuer-technical-profile.md) - zendt een SAML-token uit dat wordt teruggestuurd naar de toepassing van de relying party.
- [Self-Asserted](self-asserted-technical-profile.md) - Interactie met de gebruiker. Verzamel bijvoorbeeld de referenties van de gebruiker om zich aan te melden, de aanmeldingspagina weer te geven of het opnieuw instellen van het wachtwoord.
- [Sessiebeheer](custom-policy-reference-sso.md) - Behandel verschillende soorten sessies.

## <a name="technical-profile-flow"></a>Technische profielstroom

Alle soorten technische profielen delen hetzelfde concept. U verzendt invoerclaims, voert claimstransformatie uit en communiceert met de geconfigureerde partij, zoals een identiteitsprovider, REST API of Azure AD-directoryservices. Nadat het proces is voltooid, retourneert het technische profiel de uitvoerclaims en kan het uitvoerclaimtransformatie uitvoeren. In het volgende diagram ziet u hoe de transformaties en toewijzingen waarnaar in het technische profiel wordt verwezen, worden verwerkt. Ongeacht de partij met welke overeenkomst het technische profiel communiceert, nadat een claimtransformatie is uitgevoerd, worden de uitvoerclaims van het technische profiel onmiddellijk opgeslagen in de claimzak.

![Diagram dat de technische profielstroom illustreert](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Single sign-on (SSO) session management** - Herstelt de sessiestatus van het technische profiel met behulp van [SSO-sessiebeheer](custom-policy-reference-sso.md).
1. **Input claims transformatie** - Input claims van elke input [claims transformatie](claimstransformations.md) worden opgehaald uit de claim bag.  De uitvoerclaims van een transformatie van inputclaims kunnen inputclaims zijn van een volgende transformatie van inputclaims.
1. **Input claims** - Claims worden opgehaald uit de claimbag en worden gebruikt voor het technische profiel. Een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md) gebruikt bijvoorbeeld de invoerclaims om de uitvoerclaims die de gebruiker biedt vooraf in te vullen. Een technisch profiel van de REST API gebruikt de invoerclaims om invoerparameters naar het REST API-eindpunt te verzenden. Azure Active Directory gebruikt invoerclaim als een unieke id om een account te lezen, bij te werken of te verwijderen.
1. **Technische profieluitvoering** - Het technische profiel wisselt de claims uit met de geconfigureerde partij. Bijvoorbeeld:
    - Doorverwijzen naar de identiteitsprovider om de aanmelding te voltooien. Na een succesvolle aanmelding keert de gebruiker terug en gaat de uitvoering van het technische profiel verder.
    - Roep een REST API aan terwijl u parameters verzendt als InputClaims en informatie terugkrijgt als OutputClaims.
    - Het gebruikersaccount maken of bijwerken.
    - Hiermee verzendt en verifieert u het MFA-sms-bericht.
1. **Technische profielen voor validatie** - Een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md) kan [validatietechnische profielen](validation-technical-profile.md)aanroepen. Het validatietechnische profiel valideert de gegevens die door de gebruiker zijn geprofileerd en retourneert een foutmelding of Ok, met of zonder uitvoerclaims. Voordat Azure AD B2C bijvoorbeeld een nieuw account maakt, wordt gecontroleerd of de gebruiker al bestaat in de directoryservices. U een REST API-technisch profiel aanroepen om uw eigen bedrijfslogica toe te voegen.<p>De omvang van de uitvoerclaims van een validatietechnisch profiel is beperkt tot het technische profiel dat een beroep doet op het technische profiel van de validatie. en andere validatietechnische profielen onder hetzelfde technische profiel. Als u de uitvoerclaims in de volgende orchestration-stap wilt gebruiken, moet u de uitvoerclaims toevoegen aan het technische profiel dat het technische validatieprofiel aanroept.
1. **Output claims** - Claims worden teruggestuurd naar de claims bag. U deze claims gebruiken in de volgende stap voor orkestraties of uitvoerclaimstransformaties.
1. **Output claims transformaties** - Input claims van elke output [claims transformatie](claimstransformations.md) worden opgehaald uit de claims bag. De uitvoerclaims van het technische profiel uit de vorige stappen kunnen inputclaims zijn van een transformatie van uitvoerclaims. Na de uitvoering worden de uitvoerclaims weer in de schadezak gestopt. De outputclaims van een transformatie van uitvoerclaims kunnen ook inputclaims zijn van een volgende transformatie van uitvoerclaims.
1. **Single sign-on (SSO) sessiemanagement** - Blijft de gegevens van het technische profiel aan houden voor de sessie, met behulp van [SSO-sessiebeheer](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Opname van technisch profiel

Een technisch profiel kan een ander technisch profiel bevatten om instellingen te wijzigen of nieuwe functionaliteit toe te voegen.  Het `IncludeTechnicalProfile` element is een verwijzing naar het basistechnische profiel waaruit een technisch profiel is afgeleid. Er is geen limiet op het aantal niveaus.

Het **aad-userreadusingalternativesecurityid-noerror-profiel** bevat bijvoorbeeld het **AAD-UserReadUsingAlternativeSecurityId**. Met dit technische `RaiseErrorIfClaimsPrincipalDoesNotExist` profiel `true`wordt het metagegevensitem ingesteld op , en wordt een fout gegenereerd als een sociaal account niet in de map bestaat. **AAD-UserReadUsingAlternativeSecurityId-NoError** overschrijft dit gedrag en schakelt dat foutbericht uit.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** bevat `AAD-Common` het technische profiel.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Zowel **AAD-UserReadUsingAlternativeSecurityId-NoError** als **AAD-UserReadUsingAlternativeSecurityId** specificeren niet het vereiste **protocolelement,** omdat het is opgegeven in het **AAD-Common** technische profiel.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
