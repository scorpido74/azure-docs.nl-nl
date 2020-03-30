---
title: Voorbeelden van algemene claimstransformatie voor aangepast beleid
titleSuffix: Azure AD B2C
description: Algemene voorbeelden van claimtransformatie voor het IEF-schema (Identity Experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188542"
---
# <a name="general-claims-transformations"></a>Algemene claims transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voorbeelden voor het gebruik van algemene claimstransformaties van het Identity Experience Framework-schema in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie .

## <a name="copyclaim"></a>CopyClaim (CopyClaim)

Kopieer de waarde van een claim naar een andere. Beide claims moeten van hetzelfde type zijn.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | tekenreeks, int | Het claimtype dat moet worden gekopieerd. |
| Uitvoerclaim | outputClaim | tekenreeks, int | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. |

Gebruik deze claimtransformatie om een waarde van een tekenreeks of numerieke claim naar een andere claim te kopiëren. In het volgende voorbeeld wordt de externe claimwaarde e-mail naar e-mailclaim overgemaakt.

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

- Invoerclaims:
    - **inputClaim**:bob@contoso.com
- Output claims:
    - **outputClaim**:bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

Controleert of de **inputClaim** bestaat of niet en stelt **outputClaim** in op waar of onwaar dienovereenkomstig.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim |Alle | De inputclaim waarvan het bestaan moet worden geverifieerd. |
| Uitvoerclaim | outputClaim | booleaans | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. |

Gebruik deze claimtransformatie om te controleren of er een claim bestaat of waarde bevat. De retourwaarde is een booleaan die aangeeft of de claim bestaat. Na voorbeeld wordt gecontroleerd of het e-mailadres bestaat.

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

- Invoerclaims:
  - **inputClaim**:someone@contoso.com
- Output claims:
  - **outputClaim**: waar

## <a name="hash"></a>Hash

Hash de verstrekte platte tekst met behulp van het zout en een geheim. Het gebruikte hashing algoritme is SHA-256.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | Leesbare | tekenreeks | De invoerclaim is versleuteld |
| Invoerclaim | Zout | tekenreeks | De zoutparameter. U een willekeurige `CreateRandomString` waarde maken met behulp van claimtransformatie. |
| Inputparameter | randomizerSecret randomizerSecret randomizerSecret randomizer | tekenreeks | Wijst naar een bestaande Azure AD **B2C-beleidssleutel**. Een nieuwe beleidssleutel maken: selecteer in uw Azure AD B2C-tenant onder **Beheren**de optie **Identity Experience Framework**. Selecteer **Beleidssleutels** om de sleutels weer te geven die beschikbaar zijn in uw tenant. Selecteer **Toevoegen**. Selecteer **Handmatig** **voor opties**. Geef een naam op (het voorvoegsel *B2C_1A_* kan automatisch worden toegevoegd.). Voer in het tekstvak **Geheim** elk geheim in dat u wilt gebruiken, zoals 1234567890. Selecteer **Handtekening**voor **Sleutelgebruik**. Selecteer **Maken**. |
| Uitvoerclaim | hash | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld. De claim geconfigureerd `plaintext` in de inputClaim. |

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

- Invoerclaims:
  - **plaintext**:MyPass@word1
  - **zout**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Output claims:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
