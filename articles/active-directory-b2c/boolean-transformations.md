---
title: Boolean-voor beelden van claim transformatie voor aangepast beleid
titleSuffix: Azure AD B2C
description: Boole-voor beelden van claim transformatie voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 130fca4d5894316e7684270ff9d6361e9d9f9dd3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620843"
---
# <a name="boolean-claims-transformations"></a>Booleaanse claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van de Boole-claim transformaties van het Framework-schema voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="andclaims"></a>AndClaims

Voert een en-bewerking uit van twee Booleaanse inputClaims en stelt de output claim in met het resultaat van de bewerking.

| Item  | TransformationClaimType  | Gegevenstype  | Opmerkingen |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | booleaans | Het eerste claim type dat moet worden geëvalueerd. |
| InputClaim | inputClaim2  | booleaans | Het tweede claim type dat moet worden geëvalueerd. |
|OutputClaim | outputClaim | booleaans | De ClaimTypes die wordt geproduceerd nadat deze claim transformatie is aangeroepen (True of false). |

De volgende claims-trans formatie demonstreert hoe en hoe en met de ClaimTypes: `isEmailNotExist`en `isSocialAccount`. De uitvoer claim `presentEmailSelfAsserted` wordt ingesteld op `true` als de waarde van beide invoer claims `true`zijn. In een indelings stap kunt u een voor waarde gebruiken voor het vooraf instellen van een zelfbevestigende pagina, alleen als een e-mail adres voor een sociaal account leeg is.

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
| inputClaim | inputClaim | booleaans | Het claim type dat moet worden bevestigd. |
| InputParameter |valueToCompareTo | booleaans | De waarde die moet worden vergeleken (waar of onwaar). |

De **AssertBooleanClaimIsEqualToValue** -claim transformatie wordt altijd uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md). De meta gegevens van het zelfondertekende technische profiel **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** bepalen het fout bericht dat de gebruiker aan het technische profiel presenteert.

![AssertStringClaimsAreEqual-uitvoering](./media/boolean-transformations/assert-execution.png)

De volgende claim transformatie laat zien hoe u de waarde van een Booleaanse claim type met een `true` waarde controleert. Als de waarde van de `accountEnabled` claim type False is, wordt er een fout bericht gegenereerd.

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


Met het technische profiel voor `login-NonInteractive` validatie wordt de `AssertAccountEnabledIsTrue` claims-trans formatie aangeroepen.
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
- Resultaat: er is een fout opgetreden

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Controleert of de Booleaanse waarde van een claims gelijk is aan `true` of `false`en retourneert het resultaat van de compressie. 

| Item | TransformationClaimType  | Gegevenstype  | Opmerkingen |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | booleaans | Het claim type dat moet worden bevestigd. |
| InputParameter |valueToCompareTo | booleaans | De waarde die moet worden vergeleken (waar of onwaar). |
| OutputClaim | compareResult | booleaans | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |


De volgende claim transformatie laat zien hoe u de waarde van een Booleaanse claim type met een `true` waarde controleert. Als de waarde van de `IsAgeOver21Years` claim type gelijk is aan `true`, retourneert de claim transformatie `true`, anders `false`.

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
      <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: False
- Invoer parameters:
    - **valueToCompareTo**: True
- Uitvoer claims:
    - **compareResult**: False 



## <a name="notclaims"></a>NotClaims

Hiermee wordt een niet-bewerking uitgevoerd van de Booleaanse input claim en wordt de output claim ingesteld met het resultaat van de bewerking.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | booleaans | De claim die moet worden gebruikt. |
| OutputClaim | outputClaim | booleaans | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen (True of false). |

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
| InputClaim | inputClaim1 | booleaans | Het eerste claim type dat moet worden geëvalueerd. |
| InputClaim | inputClaim2 | booleaans | Het tweede claim type dat moet worden geëvalueerd. |
| OutputClaim | outputClaim | booleaans | De ClaimTypes die wordt geproduceerd nadat deze ClaimsTransformation is aangeroepen (True of false). |

De volgende claims transformatie laat zien hoe u `Or` twee Booleaanse ClaimTypes. In de Orchestration-stap kunt u een voor waarde gebruiken om een zelfbevestigende pagina vooraf in te stellen als de waarde van een van de claims `true`is.

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
