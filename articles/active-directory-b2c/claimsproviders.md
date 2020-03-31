---
title: ClaimsProviders - Azure Active Directory B2C | Microsoft Documenten
description: Geef het element ClaimsProvider van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189766"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een claimprovider bevat een reeks [technische profielen.](technicalprofiles.md) Elke claimprovider moet beschikken over een of meer technische profielen die de eindpunten en de protocollen bepalen die nodig zijn om met de claimprovider te communiceren. Een claimprovider kan meerdere technische profielen hebben. Er kunnen bijvoorbeeld meerdere technische profielen worden gedefinieerd omdat de claimprovider meerdere protocollen, verschillende eindpunten met verschillende mogelijkheden of verschillende claims op verschillende betrouwbaarheidsniveaus ondersteunt. Het kan aanvaardbaar zijn om gevoelige claims vrij te geven in een gebruikersreis, maar niet in een andere.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

Het element **ClaimsProviders** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | Een geaccrediteerde claimprovider die kan worden ingezet in verschillende gebruikersreizen. |

## <a name="claimsprovider"></a>ClaimsProvider

Het element **ClaimProvider** bevat de volgende onderliggende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Een tekenreeks die de domeinnaam voor de claimprovider bevat. Als uw claimprovider bijvoorbeeld het technische profiel van Facebook bevat, wordt de domeinnaam Facebook.com. Deze domeinnaam wordt gebruikt voor alle technische profielen die zijn gedefinieerd in de claimprovider, tenzij overschreven door het technische profiel. De domeinnaam kan ook worden verwezen in een **domain_hint**. Zie aanmelden bij **een sociale provider** van Direct aanmelden instellen [met Azure Active Directory B2C](direct-signin.md)voor meer informatie. |
| DisplayName | 1:1 | Een tekenreeks met de naam van de claimprovider. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Een reeks technische profielen ondersteund door de claimprovider |

**ClaimsProvider** organiseert hoe uw technische profielen zich verhouden tot de schadeleverancier. In het volgende voorbeeld wordt de Azure Active Directory-claimprovider weergegeven met de technische azure directory-technische profielen:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

In het volgende voorbeeld ziet u de Facebook-claimprovider met het technische profiel **Facebook-OAUTH.**

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
