---
title: Voorbeelden van datumclaimstransformatie voor aangepast beleid
description: Voorbeelden van datumclaimstransformatie voor het IEF-schema (Identity Experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c02ac9392d6f3f95deef38ff86250e96dfb76d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476685"
---
# <a name="date-claims-transformations"></a>Datumclaims transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voorbeelden voor het gebruik van de datumclaimstransformaties van het Identity Experience Framework-schema in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie .

## <a name="assertdatetimeisgreaterthan"></a>Assertdatetimeisgreaterthan

Hiermee wordt gecontroleerd of één datum- en tijdclaim (tekenreeksgegevenstype) later is dan een tweede datum- en tijdclaim (tekenreeksgegevenstype) en dat er een uitzondering wordt gemaakt.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | leftOperand | tekenreeks | Het type eerste claim, dat later zou moeten zijn dan de tweede claim. |
| Invoerclaim | rightOperand | tekenreeks | Het type tweede claim, dat eerder zou moeten zijn dan de eerste vordering. |
| Inputparameter | AssertifEqualTo | booleaans | Hiermee geeft u op of deze bewering moet worden doorgegeven als de linker operand gelijk is aan de rechter operand. |
| Inputparameter | AssertIfRightOperandIsNietAanwezig | booleaans | Hiermee geeft u op of deze bewering moet worden doorgegeven als het juiste operand ontbreekt. |
| Inputparameter | TreatAsEqualIfWithinMillseconden | int | Hiermee geeft u het aantal milliseconden op waarmee tussen de twee datumtijden rekening moet worden gehouden met de tijden gelijk (bijvoorbeeld om rekening te houden met klokscheeftrekking). |

De **AssertDateTimeIsGreaterThan-claimtransformatie** wordt altijd uitgevoerd vanuit een [validatietechnisch profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md). De **metagegevens van DateTimeGreaterThan** zelf geclaimd e-mailberichten voor technische profielgegevens bepalen de foutmelding die het technische profiel aan de gebruiker presenteert. De foutberichten kunnen worden [gelokaliseerd](localization-string-ids.md#claims-transformations-error-messages).

![AssertstringclaimsAreequal uitvoering](./media/date-transformations/assert-execution.png)

In het volgende `currentDateTime` voorbeeld `approvedDateTime` wordt de claim vergeleken met de claim. Er wordt een `currentDateTime` fout `approvedDateTime`gegenereerd als deze later is dan . De transformatie behandelt waarden als gelijk als ze binnen 5 minuten (30000 milliseconden) verschil zijn.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Het `login-NonInteractive` validatietechnische profiel `AssertApprovedDateTimeLaterThanCurrentDateTime` noemt de claimtransformatie.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Het zelfverklaarde technische profiel noemt het validatie **login-NonInteractive** technisch profiel.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **leftOperand**: 2020-03-01T15:00:00.00000000Z
    - **rightOperand**: 2020-03-01T14:00:00.00000000Z
- Resultaat: Fout gegooid

## <a name="convertdatetodatetimeclaim"></a>Convertdatetodatetimeclaim

Converteert een **Datum** ClaimType naar een **DateTime** ClaimType. De claimtransformatie converteert de tijdnotatie en voegt 12:00:00 uur toe aan de datum.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | date | Het ClaimType dat moet worden geconverteerd. |
| Uitvoerclaim | outputClaim | Datetime | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. |

In het volgende voorbeeld wordt `dateOfBirth` de conversie van de `dateOfBirthWithTime` claim (datumgegevenstype) naar een andere claim (datatype dateTime) aangetoond.

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim**: 2020-15-03
- Output claims:
    - **outputClaim**: 2020-15-03T00:00:00.0000000Z

## <a name="convertdatetimetodateclaim"></a>Convertdatetimetodateclaim

Hiermee converteert u een **DatumTime** ClaimType naar een **datumclaimtype.** Met de claimtransformatie wordt de tijdnotatie van de datum verwijderd.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | Datetime | Het ClaimType dat moet worden geconverteerd. |
| Uitvoerclaim | outputClaim | date | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. |

In het volgende voorbeeld wordt `systemDateTime` de conversie van de claim `systemDate` (dateTime-gegevenstype) naar een andere claim (datumgegevenstype) aangetoond.

```XML
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputClaim**: 2020-15-03T11:34:22.0000000Z
- Output claims:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Haal de huidige UTC-datum en -tijd op en voeg de waarde toe aan een ClaimType.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Uitvoerclaim | currentDateTime | Datetime | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

* Output claims:
    * **currentDateTime**: 2020-15-03T11:40:35.0000000Z

## <a name="datetimecomparison"></a>DateTimeComparison

Bepaal of de ene datumTijd later, eerder of gelijk is aan de andere. Het resultaat is een nieuwe booleaanse ClaimType booleaan met een waarde van `true` of `false`.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | firstDateTime | Datetime | De eerste dateTime om te vergelijken of het eerder of later is dan de tweede dateTime. Null-waarde gooit een uitzondering. |
| Invoerclaim | secondDateTime | Datetime | De tweede dateTime om te vergelijken of het eerder of later is dan de eerste dateTime. Null-waarde wordt behandeld als de huidige datetTime. |
| Inputparameter | operator | tekenreeks | Een van de volgende waarden: hetzelfde, later dan of eerder dan. |
| Inputparameter | timeSpanInSeconden | int | Voeg de tijdspanne toe aan de eerste datum. |
| Uitvoerclaim | result | booleaans | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. |

Gebruik deze claimtransformatie om te bepalen of twee claimtypen gelijk, later of eerder zijn dan elkaar. U bijvoorbeeld de laatste keer opslaan dat een gebruiker uw servicevoorwaarden (TOS) accepteert. Na 3 maanden u de gebruiker vragen om opnieuw toegang te krijgen tot de TOS.
Als u de claimtransformatie wilt uitvoeren, moet u eerst de huidige datumtime krijgen en ook de laatste keer dat de gebruiker de TOS accepteert.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **firstDatetime**: 2020-01-01T00:00:00.100000Z
    - **secondDateTime**: 2020-04-01T00:00:00.100000Z
- Invoerparameters:
    - **operator**: later dan
    - **timeSpanInSeconds**: 7776000 (90 dagen)
- Output claims:
    - **resultaat**: waar
