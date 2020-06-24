---
title: Een Application Insights technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een Application Insights technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201409"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een Application Insights technisch profiel definiëren in een Azure AD B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ondersteunt het rechtstreeks verzenden van gebeurtenis gegevens naar [Application Insights](../azure-monitor/app/app-insights-overview.md) door gebruik te maken van de instrumentatie sleutel die aan Azure AD B2C is gegeven.  Met een Application Insights technisch profiel kunt u gedetailleerde en aangepaste gebeurtenis logboeken ophalen voor uw gebruikers trajecten:

* Krijg inzichten op het gedrag van gebruikers.
* Los uw eigen beleid in ontwikkeling of productie op.
* Prestaties meten.
* Meldingen van Application Insights maken.


## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C voor Application Insights:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

In het volgende voor beeld wordt het algemene Application Insights technische profiel weer gegeven. Andere Application Insights technische profielen bevatten de AzureInsights-common om de configuratie ervan te benutten.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die moeten worden verzonden naar Application Insights. U kunt de naam van uw claim ook toewijzen aan een naam die u wilt weer geven in Application Insights. In het volgende voor beeld ziet u hoe u teleelementen naar Application Insights verzendt. De eigenschappen van een gebeurtenis worden toegevoegd via de syntaxis `{property:NAME}` , waarbij de naam van de eigenschap wordt toegevoegd aan de gebeurtenis. DefaultValue kan een statische waarde zijn of een waarde die wordt opgelost door een van de ondersteunde [claim-resolvers](claim-resolver-overview.md).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar Application Insights worden verzonden.

## <a name="persist-claims"></a>Claims persistent maken

Het element PersistedClaims wordt niet gebruikt.

## <a name="output-claims"></a>Uitvoer claims

De OutputClaims-en OutputClaimsTransformations-elementen worden niet gebruikt.

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het element CryptographicKeys wordt niet gebruikt.


## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| InstrumentationKey| Yes | De Application Insights [instrumentatie sleutel](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key), die wordt gebruikt voor het registreren van de gebeurtenissen. | 
| DeveloperMode| No | Een Booleaanse waarde die aangeeft of de ontwikkelaars modus is ingeschakeld. Mogelijke waarden: `true` of `false` (standaard). Deze meta gegevens bepalen hoe gebeurtenissen worden gebufferd. In een ontwikkel omgeving met mini maal gebeurtenis volume is het inschakelen van de ontwikkelaars modus tot gevolg dat gebeurtenissen direct naar Application Insights worden verzonden.|  
|DisableTelemetry |No |Een Booleaanse waarde die aangeeft of telemetrie moet worden ingeschakeld of niet. Mogelijke waarden: `true` of `false` (standaard).| 


## <a name="next-steps"></a>Volgende stappen

- [Create an Application Insights resource](../azure-monitor/app/create-new-resource.md) (Een Application Insights-resource maken)
- Meer informatie over het [bijhouden van gebruikers gedrag in azure Active Directory B2C met behulp van Application Insights](analytics-with-application-insights.md)
