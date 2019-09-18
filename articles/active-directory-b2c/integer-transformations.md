---
title: Voor beelden van een geheel getal trans formatie voor het Framework van de identiteits ervaring van Azure Active Directory B2C | Microsoft Docs
description: Voor beelden van trans formatie van een geheel getal voor het Framework van de identiteits ervaring van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82f7fecfb35c63d586993fed73a83209782a890
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064217"
---
# <a name="integer-claims-transformations"></a>Geheeltallige claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van trans formaties met een geheel getal voor het Framework van identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Converteert een lang gegevens type naar een teken reeks gegevens type.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | Het claim type dat moet worden geconverteerd naar een teken reeks. |
| OutputClaim | outputClaim | string | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

In dit voor beeld wordt `numericUserId` de claim met het waardetype Long omgezet in een `UserId` claim met het waardetype string.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **inputClaim**: 12334 (lang)
- Uitvoer claims:
    - **outputClaim**: "12334" (teken reeks)

