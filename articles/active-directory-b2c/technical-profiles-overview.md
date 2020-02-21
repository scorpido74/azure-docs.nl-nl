---
title: Overzicht van technische profielen in aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over hoe technische profielen worden gebruikt in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/11/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3b0e59912d740e30b0e29fb882542f1995ab6f54
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505651"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Over technische profielen in Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een technisch profiel biedt een Framework met een ingebouwd mechanisme om te communiceren met verschillende soorten partijen met behulp van een aangepast beleid in Azure Active Directory B2C (Azure AD B2C). Technische profielen worden gebruikt om te communiceren met uw Azure AD B2C-Tenant, een gebruiker te maken of een gebruikers profiel te lezen. Een technisch profiel kan worden bevestigd om interactie met de gebruiker mogelijk te maken. Verzamel bijvoorbeeld de referenties van de gebruiker om u aan te melden en vervolgens de pagina voor de registratie pagina of het wacht woord opnieuw instellen weer te geven.

## <a name="type-of-technical-profiles"></a>Type technische profielen

Met een technisch profiel kunnen deze soorten scenario's worden ingeschakeld:

- [Azure Active Directory](active-directory-technical-profile.md) : biedt ondersteuning voor het Azure Active Directory B2C gebruikers beheer.
- [JWT-token Uitgever](jwt-issuer-technical-profile.md) : er wordt een JWT-token verzonden dat terugkeert naar de Relying Party-toepassing.
- **Telefoon factor provider** -multi-factor Authentication.
- [OAuth1](oauth1-technical-profile.md) -Federatie met de ID-provider van een OAuth 1,0-protocol.
- [OAuth2](oauth2-technical-profile.md) -Federatie met de ID-provider van een OAuth 2,0-protocol.
- [OpenID Connect Connect](openid-connect-technical-profile.md) -Federation met elke OpenID Connect Connect protocol-ID-provider.
- [Claims transformeren](claims-transformation-technical-profile.md) : claim trans formaties voor uitvoer aanroepen om claims waarden te manipuleren, claims te valideren of standaard waarden in te stellen voor een set uitvoer claims.
- Betrouw bare [provider](restful-technical-profile.md) : oproep naar rest API services, zoals het valideren van gebruikers invoer, verrijkende gebruikers gegevens of het integreren van line-of-business-toepassingen.
- [SAML2](saml-technical-profile.md) -Federatie met een id-provider voor SAML-protocollen.
- [Zelfbevestigend](self-asserted-technical-profile.md) : interactie met de gebruiker. Verzamel bijvoorbeeld de referenties van de gebruiker om u aan te melden, de registratie pagina weer te geven of het wacht woord opnieuw in te stellen.
- [Sessie beheer](custom-policy-reference-sso.md) : verschillende soorten sessies verwerken.
- [Application Insights](../azure-monitor/app/usage-overview.md)
- [Eenmalig wacht woord](one-time-password-technical-profile.md) : biedt ondersteuning voor het beheren van het genereren en verifiëren van een eenmalig wacht woord. 

## <a name="technical-profile-flow"></a>Technische profiel stroom

Alle typen technische profielen delen hetzelfde concept. U verzendt invoer claims, voert claims-trans formatie uit en communiceert met de geconfigureerde partij, zoals een id-provider, REST API of Azure AD-adreslijst Services. Nadat het proces is voltooid, retourneert het technische profiel de uitvoer claims en kan er uitvoer claims trans formatie worden uitgevoerd. In het volgende diagram ziet u hoe de trans formaties en toewijzingen waarnaar wordt verwezen in het technische profiel worden verwerkt. De uitvoer claims van het technische profiel worden direct opgeslagen in de claim Bag, onafhankelijk van de partij waarbij het technische profiel communiceert, nadat elke claim transformatie is uitgevoerd.

![Diagram dat de technische profiel stroom illustreert](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **Eenmalige aanmelding (SSO) sessie beheer** : Hiermee wordt de sessie status van het technische profiel hersteld met behulp van [SSO-sessie beheer](custom-policy-reference-sso.md). 
1. **Invoer claims trans formatie** -invoer claims van elke invoer [claim transformatie](claimstransformations.md) worden opgehaald uit de verzameling claims.  De uitvoer claims van een invoer claims transformatie kunnen invoer claims van een volgende invoer claim transformatie zijn.
1. **Invoer claims** : claims worden opgehaald uit de claim Bag en worden gebruikt voor het technische profiel. Een [zelfbevestigend technisch profiel](self-asserted-technical-profile.md) gebruikt bijvoorbeeld de invoer claims om de uitvoer claims die de gebruiker levert, vooraf in te vullen. Een REST API technisch profiel gebruikt de invoer claims voor het verzenden van invoer parameters naar het REST API eind punt. Azure Active Directory gebruikt een invoer claim als een unieke id voor het lezen, bijwerken of verwijderen van een account.
1. **Uitvoering van technisch profiel** -het technische profiel waarmee de claims worden uitgewisseld met de geconfigureerde partij. Bijvoorbeeld:
    - Leid de gebruiker om naar de ID-provider om de aanmelding te volt ooien. Nadat het aanmelden is geslaagd, keert de gebruiker terug en wordt de uitvoering van het technische profiel voortgezet.
    - Aanroepen van een REST API tijdens het verzenden van para meters als InputClaims en het ophalen van informatie als OutputClaims.
    - Het gebruikers account maken of bijwerken.
    - Hiermee wordt het MFA-tekst bericht verzonden en gecontroleerd.
1. **Technische profielen voor validatie** : een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md) kan [technische profielen voor validatie](validation-technical-profile.md)aanroepen. Het technische profiel voor validatie valideert de gegevens die door de gebruiker worden geprofilerd en retourneert een fout bericht of OK, met of zonder uitvoer claims. Voordat Azure AD B2C bijvoorbeeld een nieuw account maakt, wordt gecontroleerd of de gebruiker al in de Directory Services bestaat. U kunt een REST API technisch profiel aanroepen om uw eigen bedrijfs logica toe te voegen.<p>Het bereik van de uitvoer claims van een validatie technische profiel is beperkt tot het technische profiel dat het technische profiel voor validatie aanroept. en andere validatie technische profielen onder hetzelfde technische profiel. Als u de uitvoer claims in de volgende Orchestration-stap wilt gebruiken, moet u de uitvoer claims toevoegen aan het technische profiel dat het technische profiel voor validatie aanroept.
1. **Uitvoer claims** : claims worden teruggestuurd naar de Bag van claims. U kunt deze claims gebruiken in de volgende indelings stap of trans formaties uitvoer claims.
1. **Uitvoer claim transformaties** -invoer claims van elke uitvoer [claims transformatie](claimstransformations.md) worden opgehaald uit de verzameling claims. De uitvoer claims van het technische profiel uit de vorige stappen kunnen invoer claims van een uitvoer claim transformatie zijn. Na de uitvoering worden de uitvoer claims weer in de claims-Bag geplaatst. De uitvoer claims van een trans formatie voor uitvoer claims kunnen ook invoer claims van een volgende uitvoer claims transformatie zijn.
1. **Beheer van sessie voor eenmalige aanmelding (SSO)** : persistente gegevens van het technische profiel blijven in de sessie met behulp van [SSO-sessie beheer](custom-policy-reference-sso.md).


## <a name="technical-profile-inclusion"></a>Technisch profiel opnemen

Een technisch profiel kan een ander technisch profiel bevatten om instellingen te wijzigen of nieuwe functionaliteit toe te voegen.  Het `IncludeTechnicalProfile`-element is een verwijzing naar het basis-technische profiel van waaruit een technisch profiel is afgeleid. Er is geen limiet voor het aantal niveaus. 

Het **Aad-UserReadUsingAlternativeSecurityId-error-** technisch profiel bevat bijvoorbeeld de **Aad-UserReadUsingAlternativeSecurityId**. Met dit technische profiel wordt het `RaiseErrorIfClaimsPrincipalDoesNotExist` meta gegevens item ingesteld op `true`, en wordt er een fout gegenereerd als er geen sociaal account in de directory bestaat. **Aad-UserReadUsingAlternativeSecurityId-fout** onderdrukt dit gedrag en schakelt dat fout bericht uit.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**Aad-UserReadUsingAlternativeSecurityId** bevat het technische profiel van `AAD-Common`.

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

Zowel **Aad-UserReadUsingAlternativeSecurityId-** mis en **Aad-UserReadUsingAlternativeSecurityId** geven niet het vereiste **protocol** element op omdat het is opgegeven in het **Aad-algemene** technische profiel.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
