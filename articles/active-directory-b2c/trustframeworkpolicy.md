---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Documenten
description: Geef het element TrustFrameworkPolicy van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c964a7bde0b7db9357c73fc79d2df3170075fcc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186383"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een aangepast beleid wordt weergegeven als een of meer XML-opgemaakte bestanden, die naar elkaar verwijzen in een hiërarchische keten. De XML-elementen definiëren elementen van het beleid, zoals het claimschema, claimstransformaties, inhoudsdefinities, claimproviders, technische profielen, gebruikersreis en orkestratiestappen. Elk beleidsbestand wordt gedefinieerd in het element **TrustFrameworkPolicy** op het hoogste niveau van een beleidsbestand.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


Het element **TrustFrameworkPolicy** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
|---------- | -------- | ----------- |
| PolicySchemaVersie | Ja | De schemaversie die moet worden gebruikt om het beleid uit te voeren. De waarde moet`0.3.0.0` |
| TenantObjectId | Nee | De unieke object-id van de Azure Active Directory B2C (Azure AD B2C) tenant. |
| TenantId | Ja | De unieke id van de tenant waartoe dit beleid behoort. |
| PolicyId | Ja | De unieke id voor het beleid. Deze identificatieid moet vooraf zijn vastgesteld door *B2C_1A_* |
| PublicPolicyUri | Ja | Uri voor het beleid, dat een combinatie is van de tenant-id en de beleids-ID. |
| Implementatiemodus | Nee | Mogelijke waarden: `Production` `Development`, of . `Production` is de standaardwaarde. Gebruik deze eigenschap om uw beleid te debuggen. Zie [Logboeken verzamelen voor](troubleshoot-with-application-insights.md)meer informatie. |
| UserJourneyRecorderEndpoint | Nee | Het eindpunt dat wordt gebruikt wanneer `Development` **DeploymentMode** is ingesteld op . De waarde `urn:journeyrecorder:applicationinsights`moet . Zie [Logboeken verzamelen voor](troubleshoot-with-application-insights.md)meer informatie. |


In het volgende voorbeeld ziet u hoe u het element **TrustFrameworkPolicy** opgeeft:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Overervingsmodel

Deze typen beleidsbestanden worden meestal gebruikt in een gebruikersreis:

- Een **basisbestand** dat de meeste definities bevat. Om u te helpen bij het oplossen van problemen en het langdurig onderhouden van uw beleid, wordt aanbevolen om een minimum aantal wijzigingen aan te brengen in dit bestand.
- Een **extensiesbestand** met de unieke configuratiewijzigingen voor uw tenant. Dit beleidsbestand is afgeleid van het bestand Base. Gebruik dit bestand om nieuwe functionaliteit toe te voegen of bestaande functionaliteit te overschrijven. Gebruik dit bestand bijvoorbeeld om te reageren op nieuwe identiteitsproviders.
- Een **RP-bestand (Relying Party)** dat het afzonderlijke taakgerichte bestand is dat rechtstreeks wordt aangeroepen door de toepassing van de relying party, zoals uw web-, mobiele of desktoptoepassingen. Elke unieke taak, zoals aanmelden of aanmelden, wachtwoordopnieuw instellen of profielbewerken, vereist een eigen RP-beleidsbestand. Dit beleidsbestand is afgeleid van het bestand Extensies.

Een relying party-toepassing roept het RP-beleidsbestand aan om een specifieke taak uit te voeren. Bijvoorbeeld om de aanmeldingsstroom te starten. Het Identity Experience Framework in Azure AD B2C voegt alle elementen toe uit het basisbestand en vervolgens vanuit het bestand Extensies en ten slotte vanuit het RP-beleidsbestand om het huidige beleid te assembleren. Elementen van hetzelfde type en dezelfde naam in het RP-bestand overschrijven deze elementen in de extensies en Extensies overschrijven Base. In het volgende diagram ziet u de relatie tussen de beleidsbestanden en de toepassingen van de relying party.

![Diagram met het overervingsmodel voor het vertrouwenskaderbeleid](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Het overervingsmodel ziet er als volgt uit:

- Het bovenliggende beleid en het onderliggende beleid hebben hetzelfde schema.
- Het onderliggende beleid op elk niveau kan overerven van het bovenliggende beleid en uitbreiden door nieuwe elementen toe te voegen.
- Er is geen limiet op het aantal niveaus.

Zie [Aan de slag met aangepast beleid](custom-policy-get-started.md)voor meer informatie .

## <a name="base-policy"></a>Basisbeleid

Als u een beleid wilt overnemen van een ander beleid, moet een element **BasePolicy** worden gedeclareerd onder het element **TrustFrameworkPolicy** van het beleidsbestand. Het element **BasePolicy** is een verwijzing naar het basisbeleid waaruit dit beleid is afgeleid.

Het element **Basisbeleid** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | --------|
| TenantId | 1:1 | De id van uw Azure AD B2C-tenant. |
| PolicyId | 1:1 | De id van het bovenliggende beleid. |


In het volgende voorbeeld ziet u hoe u een basisbeleid opgeeft. Dit **B2C_1A_TrustFrameworkExtensions** beleid is afgeleid van het **B2C_1A_TrustFrameworkBase** beleid.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Beleidsuitvoering

Een toepassing van relying party, zoals een web-, mobiele of desktoptoepassing, noemt het beleid van de [relying party (RP).](relyingparty.md) In het RP-beleidsbestand wordt een specifieke taak uitgevoerd, zoals aanmelden, het opnieuw instellen van een wachtwoord of het bewerken van een profiel. Het RP-beleid configureert de lijst met claims die de toepassing van de relying party ontvangt als onderdeel van het token dat wordt uitgegeven. Meerdere toepassingen kunnen hetzelfde beleid gebruiken. Alle toepassingen ontvangen hetzelfde token met claims, en de gebruiker gaat door dezelfde gebruikersreis. Eén toepassing kan meerdere beleidsregels gebruiken.

In het RP-beleidsbestand geeft u het element **DefaultUserJourney** op, dat naar de [UserJourney](userjourneys.md)verwijst. De gebruikersreis wordt meestal gedefinieerd in het beleid Basis of Extensies.

B2C_1A_signup_signin beleid:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase of B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Een gebruikersreis definieert de bedrijfslogica van wat een gebruiker doormaakt. Elke gebruikersreis is een reeks orkestratiestappen die een reeks acties uitvoert, in volgorde op het gebied van verificatie en het verzamelen van informatie.

Het beleidsbestand **SocialAndLocalAccounts** in het [startpakket](custom-policy-get-started.md#custom-policy-starter-pack) bevat de gebruikersreizen SignUpOrSignIn, ProfileEdit, PasswordReset. U meer gebruikersreizen toevoegen voor andere scenario's, zoals het wijzigen van een e-mailadres of het koppelen en ontkoppelen van een sociaal account.

De orkestratiestappen kunnen een [technisch profiel](technicalprofiles.md)aanroepen. Een technisch profiel biedt een kader met een ingebouwd mechanisme om te communiceren met verschillende soorten partijen. Een technisch profiel kan bijvoorbeeld deze acties uitvoeren:

- Een gebruikerservaring weergeven.
- Gebruikers toestaan zich aan te melden met een sociaal of een bedrijfsaccount, zoals Facebook, Microsoft-account, Google, Salesforce of een andere identiteitsprovider.
- Telefoonverificatie instellen voor MFA.
- Gegevens lezen en schrijven van en naar een Azure AD B2C-identiteitsarchief.
- Bel een aangepaste Rustgevende API-service.

![Diagram met de uitvoeringsstroom van het beleid](./media/trustframeworkpolicy/custom-policy-execution.png)

 Het element **TrustFrameworkPolicy** bevat de volgende elementen:

- Basisbeleid zoals hierboven aangegeven
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
