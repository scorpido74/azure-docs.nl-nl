---
title: Voorbeelden van Booleaanse claimstransformatie voor aangepast beleid
titleSuffix: Azure AD B2C
description: Booleaanse claimstransformatievoorbeelden voor het IEF-schema (Identity Experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 007d613a1f170a0ee278a838c92ade2fce9c6dec
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529203"
---
# <a name="boolean-claims-transformations"></a>Booleaanse claims transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voorbeelden voor het gebruik van de booleaanse claimtransformaties van het Identity Experience Framework-schema in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie .

## <a name="andclaims"></a>AndClaims AndClaims

Hiermee voert u twee booleaanse invoerClaims uit en stelt u de outputClaim in met het resultaat van de bewerking.

| Item  | TransformationClaimType  | Gegevenstype  | Opmerkingen |
|-------| ------------------------ | ---------- | ----- |
| Invoerclaim | inputClaim1 | booleaans | Het eerste ClaimType dat is geëvalueerd. |
| Invoerclaim | inputClaim2  | booleaans | Het tweede ClaimType dat moet worden geëvalueerd. |
|Uitvoerclaim | outputClaim | booleaans | De ClaimTypes die worden geproduceerd nadat deze claimtransformatie is ingeroepen (waar of onwaar). |

De volgende claims transformatie laat zien hoe en `isEmailNotExist`twee `isSocialAccount`booleaanse ClaimTypes: , en . De uitvoerclaim `presentEmailSelfAsserted` is `true` ingesteld op als de `true`waarde van beide inputclaims . In een orkestratiestap u een voorwaarde gebruiken om een zelfverklaarde pagina vooraf in te stellen, alleen als een e-mail met een sociaal account leeg is.

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

- Invoerclaims:
    - **inputClaim1**: waar
    - **inputClaim2**: false
- Output claims:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>Assertbooleanclaimisequaltovalue

Hiermee controleert u of booleaanse waarden van twee claims gelijk zijn en wordt er een uitzondering gemaakt als dit niet het is.

| Item | TransformationClaimType  | Gegevenstype  | Opmerkingen |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | booleaans | Het ClaimType dat moet worden geclaimd. |
| Inputparameter |valueToCompareTo | booleaans | De waarde om te vergelijken (waar of onwaar). |

De **assertbooleanclaimisequaltowaarde-claimtransformatie** wordt altijd uitgevoerd vanuit een [validatietechnisch profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md). De **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** zelfgeclaimde technische profielmetagegevens regelt de foutmelding die het technische profiel aan de gebruiker presenteert. De foutberichten kunnen worden [gelokaliseerd](localization-string-ids.md#claims-transformations-error-messages).

![AssertstringclaimsAreequal uitvoering](./media/boolean-transformations/assert-execution.png)

De volgende claimtransformatie laat zien hoe u de waarde `true` van een booleaanse ClaimType met een waarde controleren. Als de waarde `accountEnabled` van het ClaimType onjuist is, wordt er een foutbericht gegenereerd.

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


Het `login-NonInteractive` validatietechnische profiel `AssertAccountEnabledIsTrue` noemt de claimtransformatie.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Het zelfverklaarde technische profiel noemt het validatie **login-NonInteractive** technisch profiel.

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

- Invoerclaims:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Resultaat: Fout gegooid

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Hiermee controleert u of de booleaanse waarde van een claim gelijk is aan `true` of `false`, en retourneert u het resultaat van de compressie.

| Item | TransformationClaimType  | Gegevenstype  | Opmerkingen |
| ---- | ------------------------ | ---------- | ----- |
| Invoerclaim | inputClaim | booleaans | Het ClaimType dat moet worden geclaimd. |
| Inputparameter |valueToCompareTo | booleaans | De waarde om te vergelijken (waar of onwaar). |
| Uitvoerclaim | compareResult compareResult compareResult compareResult | booleaans | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. |


De volgende claimtransformatie laat zien hoe u de waarde `true` van een booleaanse ClaimType met een waarde controleren. Als de waarde `IsAgeOver21Years` van het `true`ClaimType gelijk `true`is `false`aan , keert de claimtransformatie terug , anders .

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

- Invoerclaims:
    - **inputClaim**: false
- Invoerparameters:
    - **valueToCompareTo**: true
- Output claims:
    - **compareResult**: false



## <a name="notclaims"></a>Aanspraken

Hiermee voert u de niet-bewerking van de booleaanse invoerClaim uit en stelt u de outputClaim in met het resultaat van de bewerking.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | booleaans | De claim om te worden geëxploiteerd. |
| Uitvoerclaim | outputClaim | booleaans | De ClaimTypes die worden geproduceerd na deze ClaimsTransformation is ingeroepen (waar of onwaar). |

Gebruik deze claimtransformatie om logische ontkenning uit te voeren op een claim.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim**: false
- Output claims:
    - **outputClaim**: waar

## <a name="orclaims"></a>OrClaims

Berekent een Of van twee booleaanse invoerClaims en stelt de outputClaim in met het resultaat van de bewerking.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim1 | booleaans | Het eerste ClaimType dat is geëvalueerd. |
| Invoerclaim | inputClaim2 | booleaans | Het tweede ClaimType dat moet worden geëvalueerd. |
| Uitvoerclaim | outputClaim | booleaans | De ClaimTypes die worden geproduceerd nadat deze ClaimTransformation is ingeroepen (waar of onwaar). |

De volgende claims transformatie `Or` laat zien hoe twee booleaanse ClaimTypes. In de orchestration-stap u een voorwaarde gebruiken om een zelfverklaarde pagina vooraf `true`in te stellen, als de waarde van een van de claims is.

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
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim1**: waar
    - **inputClaim2**: false
- Output claims:
    - **outputClaim**: waar
