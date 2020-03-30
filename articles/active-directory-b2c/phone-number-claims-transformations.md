---
title: Telefoonnummer claimt transformaties in aangepast beleid
titleSuffix: Azure AD B2C
description: Aangepaste beleidsverwijzing voor transformaties van telefoonnummerclaims in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183925"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Wijzigingen in telefoonnummerclaims definiëren in Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u naslagwerk en voorbeelden voor het gebruik van het telefoonnummerclaimstransformaties van het Identity Experience Framework-schema in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie over claimstransformaties in het algemeen.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConverterenPhoneNumberClaimToString

Hiermee `phoneNumber` converteert u `string` een gegevenstype in een gegevenstype.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | phoneNumber | phoneNumber |  Het ClaimType om te converteren naar een tekenreeks. |
| Uitvoerclaim | phoneNumberString | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld. |

In dit voorbeeld `phoneNumber` wordt de claim cellPhoneNumber met een waardetype omgezet in `string`een celtelefoonclaim met een waardetype van .

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **telefoonnummer**: +11234567890 (telefoonnummer)
- Output claims:
  - **phoneNumberString**: +11234567890 (tekenreeks)


## <a name="convertstringtophonenumberclaim"></a>ConverterenStringToPhoneNumberClaim

Deze claimtransformatie valideert de indeling van het telefoonnummer. Als deze in een geldige indeling is, wijzigt u deze in een standaardindeling die wordt gebruikt door Azure AD B2C. Als het opgegeven telefoonnummer niet in een geldige indeling is, wordt een foutbericht geretourneerd.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | phoneNumberString | tekenreeks |  De string claim voor het telefoonnummer. Het telefoonnummer moet in internationale vorm zijn, compleet met een toonaangevende "+" en landcode. Als invoerclaim `country` wordt verstrekt, is het telefoonnummer in lokale indeling (zonder de landcode). |
| Invoerclaim | land | tekenreeks | [Optioneel] De tekenreeksclaim voor de landcode van het telefoonnummer in iso3166-formaat (de tweeletterige ISO-3166-landcode). |
| Uitvoerclaim | outputClaim | phoneNumber | Het resultaat van deze claims transformatie. |

De **convertstringtophonenumberclaimtransformatie** wordt altijd uitgevoerd vanuit een [validatietechnisch profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md) of [displaycontrole.](display-controls.md) De **UserMessageIfClaimsTransformationInvalidPhoneNumber** zelf geclaimde technische profielmetadata regelt het foutbericht dat aan de gebruiker wordt gepresenteerd.

![Diagram van het uitvoeringspad van foutmeldingen](./media/phone-authentication/assert-execution.png)

U deze claimtransformatie gebruiken om ervoor te zorgen dat de opgegeven tekenreeksclaim een geldig telefoonnummer is. Zo niet, dan wordt er een foutmelding gegenereerd. In het volgende voorbeeld wordt gecontroleerd of het **phoneString** ClaimType inderdaad een geldig telefoonnummer is en wordt het telefoonnummer geretourneerd in de standaard Azure AD B2C-indeling. Anders wordt een foutbericht gegenereerd.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Het zelfverklaarde technische profiel dat het validatietechnische profiel aanroept dat deze claimtransformatie bevat, kan de foutmelding definiëren.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Voorbeeld 1

- Invoerclaims:
  - **phoneNumberString**: 033 456-7890
  - **land**: DK
- Output claims:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>Voorbeeld 2

- Invoerclaims:
  - **phoneNumberString**: +1 (123) 456-7890
- Output claims:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberandCountryCodeFromPhoneNumberString

Hiermee wordt de landcode en het nationale nummer uit de invoerclaim gehaald en wordt optioneel een uitzondering gemaakt als het opgegeven telefoonnummer niet geldig is.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | phoneNumber | tekenreeks | De string claim van het telefoonnummer. Het telefoonnummer moet in internationale vorm zijn, compleet met een toonaangevende "+" en landcode. |
| Inputparameter | throwExceptionOnFailure | booleaans | [Optioneel] Een parameter die aangeeft of er een uitzondering wordt gegenereerd wanneer het telefoonnummer niet geldig is. De standaardwaarde is onwaar. |
| Inputparameter | countryCodeType | tekenreeks | [Optioneel] Een parameter die het type landcode in de uitvoerclaim aangeeft. Beschikbare waarden zijn **CallingCode** (de internationale belcode voor een land, bijvoorbeeld +1) of **ISO3166** (de tweeletterige ISO-3166-landcode). |
| Uitvoerclaim | nationaalAantal | tekenreeks | De string claim voor het nationale nummer van het telefoonnummer. |
| Uitvoerclaim | landCode | tekenreeks | De string claim voor de landcode van het telefoonnummer. |


Als de **GetNationalNumberAndCountryCodeFromPhoneNumberString-claimtransformatie** wordt uitgevoerd vanuit een [validatietechnisch profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md) of een [displaycontrolactie,](display-controls.md#display-control-actions)regelt de **UserMessageIfPhoneNumberNumberParseFailure** zelfgeclaimde technische profielmetagegevens het foutbericht dat aan de gebruiker wordt gepresenteerd.

![Diagram van het uitvoeringspad van foutmeldingen](./media/phone-authentication/assert-execution.png)

U deze claimtransformatie gebruiken om een volledig telefoonnummer op te splitsen in de landcode en het nationale nummer. Als het opgegeven telefoonnummer niet geldig is, u ervoor kiezen om een foutbericht te sturen.

In het volgende voorbeeld wordt geprobeerd het telefoonnummer op te splitsen in nationale nummer- en landcode. Als het telefoonnummer geldig is, wordt het telefoonnummer overschreven door het nationale nummer. Als het telefoonnummer niet geldig is, wordt er geen uitzondering gemaakt en heeft het telefoonnummer nog steeds de oorspronkelijke waarde.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Het zelfverklaarde technische profiel dat het validatietechnische profiel aanroept dat deze claimtransformatie bevat, kan de foutmelding definiëren.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Voorbeeld 1

- Invoerclaims:
  - **telefoonnummer**: +49 (123) 456-7890
- Invoerparameters:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- Output claims:
  - **nationaalNummer**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Voorbeeld 2

- Invoerclaims:
  - **telefoonnummer**: +49 (123) 456-7890
- Invoerparameters
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- Output claims:
  - **nationaalNummer**: 1234567890
  - **countryCode**: +49
