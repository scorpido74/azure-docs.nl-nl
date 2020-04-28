---
title: ClaimsProviders-Azure Active Directory B2C | Microsoft Docs
description: Geef het ClaimsProvider-element van een aangepast beleid in Azure Active Directory B2C op.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78189766"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een claim provider bevat een set [technische profielen](technicalprofiles.md). Elke claim provider moet een of meer technische profielen hebben die de eind punten bepalen en de protocollen die nodig zijn om te communiceren met de claim provider. Een claim provider kan meerdere technische profielen hebben. Er kunnen bijvoorbeeld meerdere technische profielen worden gedefinieerd, omdat de claim provider meerdere protocollen, verschillende eind punten met verschillende mogelijkheden ondersteunt of verschillende claims op verschillende verzekerings niveaus uitgeeft. Het kan acceptabel zijn om gevoelige claims te vrijgeven in één gebruikers traject, maar niet in een andere.

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

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | Een erkende claim provider die kan worden gebruikt in verschillende trajecten van de gebruiker. |

## <a name="claimsprovider"></a>ClaimsProvider

Het **ClaimsProvider** -element bevat de volgende onderliggende elementen:

| Element | Instanties | Beschrijving |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Een teken reeks die de domein naam voor de claim provider bevat. Als uw claim provider bijvoorbeeld het technische profiel voor Facebook bevat, is de domein naam Facebook.com. Deze domein naam wordt gebruikt voor alle technische profielen die zijn gedefinieerd in de claim provider, tenzij deze wordt overschreven door het technische profiel. Er kan ook worden verwezen naar de domein naam in een **domain_hint**. Zie voor meer informatie de sectie **Aanmelden omleiden naar een sociale provider** van het [rechtstreeks aanmelden instellen met behulp van Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Een teken reeks die de naam van de claim provider bevat. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Een set technische profielen die worden ondersteund door de claim provider |

**ClaimsProvider** organiseert hoe uw technische profielen verband houden met de claim provider. In het volgende voor beeld ziet u de Azure Active Directory claim provider met de Azure Active Directory technische profielen:

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

In het volgende voor beeld wordt de Facebook-claim provider weer gegeven met het **Facebook-OAUTH-** technische profiel.

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
