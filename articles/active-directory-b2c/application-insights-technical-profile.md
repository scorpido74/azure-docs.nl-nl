---
title: Een technisch profiel van Application Insights definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel van Application Insights in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108573"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een technisch profiel van Application Insights definiëren in een aangepast Azure AD B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ondersteunt het rechtstreeks verzenden van gebeurtenisgegevens naar [Application Insights](../azure-monitor/app/app-insights-overview.md) met behulp van de instrumentatiesleutel die wordt verstrekt aan Azure AD B2C.  Met een technisch profiel van Application Insights u gedetailleerde en aangepaste gebeurtenislogboeken voor uw gebruikersreizen krijgen om:

* Krijg inzicht in het gedrag van gebruikers.
* Problemen met uw eigen beleid in ontwikkeling of productie oplossen.
* Meet de prestaties.
* Meldingen maken vanuit Application Insights.


## <a name="protocol"></a>Protocol

Het kenmerk **Naam** van het element `Proprietary` **Protocol** moet worden ingesteld op . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocolhandlerassemblage die wordt gebruikt door Azure AD B2C voor toepassingsinzichten:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

In het volgende voorbeeld wordt het algemene technische profiel van Application Insights weergegeven. Andere technische profielen van Application Insights omvatten de AzureInsights-Common om gebruik te maken van de configuratie.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Invoerclaims

Het element **InputClaims** bevat een lijst met claims die naar Application Insights moeten worden verzonden. U de naam van uw claim ook toewijzen aan een naam die u liever wilt weergeven in Application Insights. In het volgende voorbeeld ziet u hoe u telemetrieën naar Application Insights verzendt. Eigenschappen van een gebeurtenis worden `{property:NAME}`toegevoegd via de syntaxis , waarbij de eigenschap NAAM aan de gebeurtenis wordt toegevoegd. DefaultValue kan een statische waarde zijn of een waarde die is opgelost door een van de ondersteunde [claimresolvers](claim-resolver-overview.md).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

Het element **InputClaimsTransformations** kan een verzameling **inputclaimstransformatie-elementen** bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat ze naar Application Insights worden verzonden.

## <a name="persist-claims"></a>Aanhoudende claims

Het element PersistedClaims wordt niet gebruikt.

## <a name="output-claims"></a>Outputclaims

De elementen OutputClaims en OutputClaimsTransformations worden niet gebruikt.

## <a name="cryptographic-keys"></a>Cryptografische toetsen

Het element CryptographicKeys wordt niet gebruikt.


## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Instrumentatiesleutel| Ja | De [instrumentatiesleutel](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights , die wordt gebruikt voor het registreren van de gebeurtenissen. | 
| DeveloperMode| Nee | Een Booleaan die aangeeft of de ontwikkelaarsmodus is ingeschakeld. Mogelijke `true` waarden: `false` of (standaard). Met deze metagegevens bepaalt hoe gebeurtenissen worden gebufferd. In een ontwikkelomgeving met minimaal gebeurtenisvolume, waardoor de ontwikkelaarsmodus resulteert in gebeurtenissen die onmiddellijk naar Application Insights worden verzonden.|  
|Telemetrie uitschakelen |Nee |Een Booleaan die aangeeft of telemetrie moet worden ingeschakeld of niet. Mogelijke `true` waarden: `false` of (standaard).| 


## <a name="next-steps"></a>Volgende stappen

- [Een Application Insights-resource maken](../azure-monitor/app/create-new-resource.md)
- Meer informatie over het [bijhouden van gebruikersgedrag in Azure Active Directory B2C met Behulp van Application Insights](analytics-with-application-insights.md)
