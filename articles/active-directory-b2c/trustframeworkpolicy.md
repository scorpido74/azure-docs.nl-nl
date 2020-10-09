---
title: TrustFrameworkPolicy-Azure Active Directory B2C | Microsoft Docs
description: Geef het TrustFrameworkPolicy-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29eddbcfb7c0da98e5438f968dd3976b77a44680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203092"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een aangepast beleid wordt weer gegeven als een of meer XML-indelings bestanden die naar elkaar in een hiërarchische keten verwijzen. Met de XML-elementen worden elementen van het beleid gedefinieerd, zoals het claim schema, claim transformaties, inhouds definities, claim providers, technische profielen, gebruikers traject en indelings stappen. Elk beleids bestand wordt gedefinieerd in het **TrustFrameworkPolicy** -element van het hoogste niveau van een beleids bestand.

```xml
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


Het **TrustFrameworkPolicy** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ja | De schema versie die moet worden gebruikt om het beleid uit te voeren. De waarde moet `0.3.0.0` |
| TenantObjectId | Nee | De unieke object-id van de Azure Active Directory B2C (Azure AD B2C)-Tenant. |
| TenantId | Ja | De unieke id van de Tenant waartoe dit beleid behoort. |
| PolicyId | Ja | De unieke id voor het beleid. Deze id moet worden voorafgegaan door *B2C_1A_* |
| PublicPolicyUri | Ja | De URI voor het beleid, dat een combi natie is van de Tenant-ID en de beleids-ID. |
| Als Deployment mode | Nee | Mogelijke waarden: `Production` , of `Development` . `Production` is de standaardwaarde. Gebruik deze eigenschap om fouten in uw beleid op te sporen. Zie [Logboeken verzamelen](troubleshoot-with-application-insights.md)voor meer informatie. |
| UserJourneyRecorderEndpoint | Nee | Het eind punt dat wordt gebruikt wanneer **als Deployment mode** is ingesteld op `Development` . De waarde moet zijn `urn:journeyrecorder:applicationinsights` . Zie [Logboeken verzamelen](troubleshoot-with-application-insights.md)voor meer informatie. |


In het volgende voor beeld ziet u hoe u het **TrustFrameworkPolicy** -element opgeeft:

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

## <a name="inheritance-model"></a>Overname model

Deze typen beleids bestanden worden doorgaans gebruikt in een gebruikers traject:

- Een **basis** bestand dat de meeste definities bevat. Als u hulp nodig hebt bij het oplossen van problemen en het onderhoud op lange termijn van uw beleid, kunt u het beste een minimum aantal wijzigingen aanbrengen in dit bestand.
- Een **extensie** bestand dat de unieke configuratie wijzigingen voor uw Tenant bevat. Dit beleids bestand is afgeleid van het basis bestand. Gebruik dit bestand om nieuwe functionaliteit toe te voegen of bestaande functionaliteit te overschrijven. Gebruik dit bestand bijvoorbeeld om met nieuwe id-providers te communiceren.
- Een **RP-bestand (Relying Party)** dat het bestand met de taak focus is dat rechtstreeks wordt aangeroepen door de Relying Party toepassing, zoals uw web-, mobiele of bureaublad toepassingen. Voor elke unieke taak, zoals aanmelden of aanmelden, wacht woord opnieuw instellen of profiel bewerken, is een eigen RP-beleids bestand vereist. Dit beleids bestand is afgeleid van het extensies-bestand.

Een Relying Party toepassing roept het RP-beleids bestand aan om een specifieke taak uit te voeren. Bijvoorbeeld om de aanmeldings stroom te initiëren. In het Framework voor identiteits ervaring in Azure AD B2C worden alle elementen eerst van het basis bestand en vervolgens uit het bestand extensies en ten slotte vanuit het bestand met de RP-beleids regel toegevoegd om het huidige beleid in werking te stellen. Elementen van hetzelfde type en dezelfde naam in het RP-bestand overschrijven die elementen in de uitbrei dingen en de basis van uitbrei dingen voor extensies. In het volgende diagram ziet u de relatie tussen de beleids bestanden en de Relying Party toepassingen.

![Diagram van het overname model voor het vertrouwens raamwerk beleid](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Het overname model is als volgt:

- Het bovenliggende beleid en het onderliggende beleid zijn van hetzelfde schema.
- Het onderliggende beleid op elk niveau kan overnemen van het bovenliggende beleid en het uitbreiden door nieuwe elementen toe te voegen.
- Er is geen limiet voor het aantal niveaus.

Zie [aan de slag met aangepaste beleids regels](custom-policy-get-started.md)voor meer informatie.

## <a name="base-policy"></a>Basis beleid

Als u een beleid van een ander beleid wilt overnemen, moet u een **BasePolicy** -element declareren onder het element **TrustFrameworkPolicy** van het beleids bestand. Het element **BasePolicy** is een verwijzing naar het basis beleid van waaruit dit beleid is afgeleid.

Het **BasePolicy** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | --------|
| TenantId | 1:1 | De id van uw Azure AD B2C-Tenant. |
| PolicyId | 1:1 | De id van het bovenliggende beleid. |


In het volgende voor beeld ziet u hoe u een basis beleid opgeeft. Dit **B2C_1A_TrustFrameworkExtensions** beleid wordt afgeleid van het **B2C_1A_TrustFrameworkBase** -beleid.

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

## <a name="policy-execution"></a>Uitvoering van beleid

Een Relying Party toepassing, zoals een web-, mobiele of bureaublad toepassing, roept het [beleid voor Relying Party (RP)](relyingparty.md)aan. Het RP-beleids bestand voert een specifieke taak uit, zoals het aanmelden, het opnieuw instellen van een wacht woord of het bewerken van een profiel. Met het RP-beleid wordt de lijst met claims geconfigureerd die de Relying Party toepassing ontvangt als onderdeel van het token dat wordt uitgegeven. Meerdere toepassingen kunnen hetzelfde beleid gebruiken. Alle toepassingen ontvangen hetzelfde token met claims en de gebruiker verloopt over hetzelfde traject van de gebruiker. Eén toepassing kan meerdere beleids regels gebruiken.

In het RP-beleids bestand geeft u het element **DefaultUserJourney** op dat verwijst naar de [UserJourney](userjourneys.md). De gebruikers traject wordt meestal gedefinieerd in het basis-of uitbrei ding beleid.

B2C_1A_signup_signin beleid:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase of B2C_1A_TrustFrameworkExtensionPolicy:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Een gebruikers traject definieert de bedrijfs logica van wat een gebruiker doorloopt. Elke gebruikers traject is een set Orchestration-stappen die een reeks acties uitvoert, in volg orde van de verificatie-en informatie verzameling.

Het **SocialAndLocalAccounts** -beleids bestand in het [Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack) bevat de SignUpOrSignIn-, ProfileEdit-, PasswordReset-gebruikers ritten. U kunt meer gebruikers ritten toevoegen voor andere scenario's, zoals het wijzigen van een e-mail adres of het koppelen en ontkoppelen van een sociaal account.

De Orchestration-stappen kunnen een [technisch profiel](technicalprofiles.md)aanroepen. Een technisch profiel biedt een Framework met een ingebouwd mechanisme om te communiceren met verschillende soorten partijen. Een technisch profiel kan bijvoorbeeld onder andere de volgende acties uitvoeren:

- Een gebruikers ervaring weer geven.
- Gebruikers toestaan zich aan te melden met sociale of een Enter prise-account, zoals Facebook, Microsoft-account, Google, Sales Force of een andere ID-provider.
- Telefoon verificatie voor MFA instellen.
- Gegevens lezen en schrijven van een Azure AD B2C-identiteits opslag.
- Een aangepaste rest API-service aanroepen.

![Diagram waarin de stroom voor het uitvoeren van beleid wordt weer gegeven](./media/trustframeworkpolicy/custom-policy-execution.png)

 Het **TrustFrameworkPolicy** -element bevat de volgende elementen:

- BasePolicy zoals hierboven is beschreven
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
