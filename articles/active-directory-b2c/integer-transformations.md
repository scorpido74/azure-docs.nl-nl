---
title: Voorbeelden van integere claimstransformatie voor aangepast beleid
titleSuffix: Azure AD B2C
description: Voorbeelden van integere claimstransformatie voor het IEF-schema (Identity Experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187675"
---
# <a name="integer-claims-transformations"></a>Integer claims transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voorbeelden voor het gebruik van de integer claimstransformaties van het Identity Experience Framework-schema in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie .

## <a name="convertnumbertostringclaim"></a>ConverterenNumberToStringClaim

Hiermee converteert u een lang gegevenstype in een tekenreeksgegevenstype.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | long | Het ClaimType om te converteren naar een tekenreeks. |
| Uitvoerclaim | outputClaim | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. |

In dit voorbeeld `numericUserId` wordt de claim met een `UserId` waardetype lang omgezet in een claim met een waardetype tekenreeks.

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

- Invoerclaims:
    - **inputClaim**: 12334 (lang)
- Output claims:
    - **outputClaim**: "12334" (tekenreeks)

