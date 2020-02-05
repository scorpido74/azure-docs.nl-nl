---
title: Algemene voor beelden van claim transformatie voor aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Algemene voor beelden van claim transformatie voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d9730168764f0ba683a246f9ac224c13d3bf31
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982803"
---
# <a name="general-claims-transformations"></a>Algemene claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van algemene claim transformaties van het Framework-schema voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="copyclaim"></a>CopyClaim

Kopieer de waarde van een claim naar een andere. Beide claims moeten van hetzelfde type zijn.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | teken reeks, int | Het claim type dat moet worden gekopieerd. |
| OutputClaim | outputClaim | teken reeks, int | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

Gebruik deze claim transformatie om een waarde van een teken reeks of numerieke claim naar een andere claim te kopiëren. In het volgende voor beeld wordt de claim waarde externalEmail naar een e-mail claim gekopieerd.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim"> 
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **inputClaim**: bob@contoso.com
- Uitvoer claims:
    - **outputClaim**: bob@contoso.com 

## <a name="doesclaimexist"></a>DoesClaimExist

Controleert of de **input claim** bestaat of niet en stelt **output claim** in op waar of onwaar dienovereenkomstig.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Alle | De invoer claim waarvan het bestaan moet worden geverifieerd. |
| OutputClaim | outputClaim | booleaans | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

Gebruik deze claim transformatie om te controleren of een claim bestaat of een wille keurige waarde bevat. De geretourneerde waarde is een Boolean die aangeeft of de claim bestaat. In het volgende voor beeld wordt gecontroleerd of het e-mail adres bestaat.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **inputClaim**: someone@contoso.com
- Uitvoer claims:
  - **output claim**: True

## <a name="hash"></a>Hash

Hash de meegeleverde tekst zonder opmaak met het zout en een geheim. Het hash-algoritme dat wordt gebruikt, is SHA-256.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | sleutel | string | De invoer claim die moet worden versleuteld |
| InputClaim | Salt | string | De Salt-para meter. U kunt een wille keurige waarde maken met behulp van `CreateRandomString` claims trans formatie. |
| InputParameter | randomizerSecret | string | Verwijst naar een bestaande Azure AD B2C **Policy Key**. Een nieuwe beleids sleutel maken: Selecteer in uw Azure AD B2C-Tenant onder **beheren**de optie **identiteits ervaring-Framework**. Selecteer **beleids sleutels** om de sleutels weer te geven die beschikbaar zijn in uw Tenant. Selecteer **Toevoegen**. Voor **Opties**selecteert u **hand matig**. Geef een naam op (de prefix *B2C_1A_* kan automatisch worden toegevoegd.) Voer in het tekstvak **geheim** een geheim in dat u wilt gebruiken, bijvoorbeeld 1234567890. Selecteer voor **sleutel gebruik** **hand tekening**. Selecteer **Maken**. |
| OutputClaim | eigenaar | string | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. De claim die is geconfigureerd in de `plaintext` input claim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **tekst zonder opmaak**: MyPass@word1
  - **Salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Uitvoer claims:
  - **output claim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U =
