---
title: Datum van voor beelden van claim transformatie voor het Framework met identiteits ervaring van Azure Active Directory B2C | Microsoft Docs
description: Datum voor beelden van claim transformatie voor het Framework van de identiteits ervaring van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 077915705c242805d3709b5d52d445288fa5336a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064354"
---
# <a name="date-claims-transformations"></a>Datum claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van de datum claim transformaties van het Framework voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Controleert of een datum-en tijd claim (teken reeks gegevens type) later is dan een tweede datum en tijd claim (teken reeks gegevens type) en genereert een uitzonde ring.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | leftOperand | string | Type van de eerste claim, dat later moet zijn dan de tweede claim. |
| inputClaim | rightOperand | string | Tweede claim type, dat eerder moet zijn dan de eerste claim. |
| InputParameter | AssertIfEqualTo | boolean | Hiermee wordt aangegeven of deze bevestiging moet worden door gegeven als de linkeroperand gelijk is aan de rechteroperand. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Hiermee geeft u op of deze bevestiging moet worden door gegeven als de juiste operand ontbreekt. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Hiermee geeft u het aantal milliseconden op dat is toegestaan tussen de twee datum tijden om de tijden te bepalen die gelijk zijn (bijvoorbeeld om rekening te houden met Clock scheefheid). |

De **AssertDateTimeIsGreaterThan** -claim transformatie wordt altijd uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md). De meta gegevens van het zelfondertekende technische profiel **DateTimeGreaterThan** bepalen het fout bericht dat de gebruiker aan het technische profiel presenteert.

![AssertStringClaimsAreEqual-uitvoering](./media/date-transformations/assert-execution.png)

In het volgende voor beeld wordt `currentDateTime` de claim met `approvedDateTime` de claim vergeleken. Er wordt een fout gegenereerd `currentDateTime` als deze later `approvedDateTime`is dan. De trans formatie behandelt waarden als gelijk als ze binnen vijf minuten (30000 milliseconden) verschillen.

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

Het `login-NonInteractive` validatie technische profiel roept de `AssertApprovedDateTimeLaterThanCurrentDateTime` trans formatie van claims aan.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Het zelfondertekende technische profiel aanroept het technische profiel voor validatie **aanmelding-niet-interactief** .

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

- Invoer claims:
    - **leftOperand**: 2018-10-2016T15:00:00.0000000 Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000 Z
- Daardoor Fout gegenereerd

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Hiermee wordt een **datum** claim type geconverteerd naar een datum **/tijd** -claim type. De claim transformatie converteert de tijd notatie en voegt 12:00:00 AM toe aan de datum.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | Het claim type dat moet worden geconverteerd. |
| OutputClaim | outputClaim | Datum/tijd | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

In het volgende voor beeld wordt de conversie van de `dateOfBirth` claim (datum gegevens type) naar een `dateOfBirthWithTime` andere claim (gegevens type datetime) gedemonstreerd.

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

- Invoer claims:
    - **inputClaim**: 2019-06-01
- Uitvoer claims:
    - **outputClaim**: 1559347200 (1 juni 2019 12:00:00 uur)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

De huidige UTC-datum en-tijd ophalen en de waarde toevoegen aan een claim type.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | Datum/tijd | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

* Uitvoer claims:
    * **currentDateTime**: 1534418820 (16 augustus 2018 11:27:00 uur)

## <a name="datetimecomparison"></a>DateTimeComparison

Bepalen of een datum/tijd later, eerder of gelijk aan een andere dateTime is. Het resultaat is een nieuwe Boolean claim type Boole met de waarde `true` of `false`.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | Datum/tijd | De eerste datum/tijd om te vergelijken of deze eerder of later is dan de tweede datum/tijd. Null-waarde genereert een uitzonde ring. |
| InputClaim | secondDateTime | Datum/tijd | De tweede datum/tijd om te vergelijken of deze eerder of later is dan de eerste datum/tijd. Null-waarde wordt beschouwd als de huidige datetTime. |
| InputParameter | And | string | Een van de volgende waarden: zelfde, later dan of eerder dan. |
| InputParameter | timeSpanInSeconds | int | De time span toevoegen aan de eerste datum/tijd. |
| OutputClaim | Resultaat | boolean | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

Gebruik deze claim transformatie om te bepalen of twee ClaimTypes gelijk zijn aan, later of eerder dan elkaar. U kunt bijvoorbeeld de laatste keer dat een gebruiker uw service voorwaarden heeft geaccepteerd (TOS) opslaan. Na 3 maanden kunt u de gebruiker vragen om opnieuw toegang te krijgen tot de TOS.
Als u de claim transformatie wilt uitvoeren, moet u eerst de huidige datum/tijd en de laatste keer dat de gebruiker de TOS accepteert, ophalen.

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

- Invoer claims:
    - **firstDateTime**: 2018-01-01T00:00:00.100000 Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000 Z
- Invoer parameters:
    - **operator**: later dan
    - **timeSpanInSeconds**: 7776000 (90 dagen)
- Uitvoer claims:
    - **resultaat**: True
