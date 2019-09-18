---
title: Voor beelden van Boole-betalings transformatie voor het identiteits ervaring Framework-schema van Azure Active Directory B2C | Microsoft Docs
description: Boole-voor beelden van claim transformatie voor het identiteits ervaring Framework-schema van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da4fc4704ee72210e180ef95fe6a821c8d116fa2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064565"
---
# <a name="boolean-claims-transformations"></a>Booleaanse claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van de Boole-claim transformaties van het Framework-schema voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="andclaims"></a>AndClaims

Voert een en-bewerking uit van twee Booleaanse inputClaims en stelt de output claim in met het resultaat van de bewerking.

| Item  | TransformationClaimType  | Gegevenstype  | Opmerkingen |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | Het eerste claim type dat moet worden geëvalueerd. |
| InputClaim | inputClaim2  | boolean | Het tweede claim type dat moet worden geëvalueerd. |
|OutputClaim | outputClaim | boolean | De ClaimTypes die wordt geproduceerd nadat deze claim transformatie is aangeroepen (True of false). |

De volgende claims-trans formatie demonstreert hoe en hoe en met `isEmailNotExist`de ClaimTypes `isSocialAccount`: en. De uitvoer claim `presentEmailSelfAsserted` wordt ingesteld op `true` als de waarde `true`van beide invoer claims. In een indelings stap kunt u een voor waarde gebruiken voor het vooraf instellen van een zelfbevestigende pagina, alleen als een e-mail adres voor een sociaal account leeg is.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **inputClaim1**: True
    - **inputClaim2**: False
- Uitvoer claims:
    - **output claim**: False


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Controleert of Boole-waarden van twee claims gelijk zijn en genereert een uitzonde ring als dat niet het geval is.

| Item | TransformationClaimType  | Gegevenstype  | Opmerkingen |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | Het claim type dat moet worden bevestigd. |
| InputParameter |valueToCompareTo | boolean | De waarde die moet worden vergeleken (waar of onwaar). |

De **AssertBooleanClaimIsEqualToValue** -claim transformatie wordt altijd uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md). De meta gegevens van het zelfondertekende technische profiel **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** bepalen het fout bericht dat de gebruiker aan het technische profiel presenteert.

![AssertStringClaimsAreEqual-uitvoering](./media/boolean-transformations/assert-execution.png)

De volgende claims transformatie laat zien hoe u de waarde van een Booleaanse claim type met een `true` waarde kunt controleren. Als de waarde van het `accountEnabled` claim type is ingesteld op False, wordt een fout bericht gegenereerd.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Het `login-NonInteractive` validatie technische profiel roept de `AssertAccountEnabledIsTrue` trans formatie van claims aan.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Het zelfondertekende technische profiel aanroept het technische profiel voor validatie **aanmelding-niet-interactief** .

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: False
    - **valueToCompareTo**: True
- Daardoor Fout gegenereerd

## <a name="notclaims"></a>NotClaims

Hiermee wordt een niet-bewerking uitgevoerd van de Booleaanse input claim en wordt de output claim ingesteld met het resultaat van de bewerking.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | De claim die moet worden gebruikt. |
| OutputClaim | outputClaim | boolean | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen (True of false). |

Gebruik deze claim transformatie om logische ontkenning op een claim uit te voeren.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: False
- Uitvoer claims:
    - **output claim**: True

## <a name="orclaims"></a>OrClaims

Hiermee worden een of twee Booleaanse inputClaims berekend en wordt de output claim ingesteld met het resultaat van de bewerking.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | Het eerste claim type dat moet worden geëvalueerd. |
| InputClaim | inputClaim2 | boolean | Het tweede claim type dat moet worden geëvalueerd. |
| OutputClaim | outputClaim | boolean | De ClaimTypes die wordt geproduceerd nadat deze ClaimsTransformation is aangeroepen (True of false). |

De volgende claims transformatie laat zien hoe `Or` twee Booleaanse ClaimTypes. In de Orchestration-stap kunt u een voor waarde gebruiken voor het vooraf instellen van een zelfbevestigende pagina, als de waarde van een van de claims `true`is.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **inputClaim1**: True
    - **inputClaim2**: False
- Uitvoer claims:
    - **output claim**: True

