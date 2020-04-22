---
title: JSON claimt transformatievoorbeelden voor aangepast beleid
titleSuffix: Azure AD B2C
description: JSON claimt transformatievoorbeelden voor het IEF-schema (Identity Experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b42c2a414333e7ed262441321a808fc45425fc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756754"
---
# <a name="json-claims-transformations"></a>JSON claimt transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voorbeelden voor het gebruik van de JSON-claimtransformaties van het Identity Experience Framework-schema in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie .

## <a name="generatejson"></a>GenerateJson

Gebruik claimwaarden of constanten om een JSON-tekenreeks te genereren. De padtekenreeks na puntnotatie wordt gebruikt om aan te geven waar de gegevens in een JSON-tekenreeks moeten worden ingevoegd. Na splitsing door stippen worden gehele getallen geïnterpreteerd als de index van een JSON-array en worden niet-gehele getallen geïnterpreteerd als de index van een JSON-object.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | Tekenreeks na dotnotatie | tekenreeks | Het JsonPath van de JSON waarin de claimwaarde wordt ingevoegd. |
| Inputparameter | Tekenreeks na dotnotatie | tekenreeks | Het JsonPath van de JSON waarin de constante tekenreekswaarde wordt ingevoegd. |
| Uitvoerclaim | outputClaim | tekenreeks | De gegenereerde JSON-tekenreeks. |

In het volgende voorbeeld wordt een JSON-tekenreeks gegenereerd op basis van de claimwaarde van 'e-mail' en 'otp' en constante tekenreeksen.

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

De volgende claims transformatie outputs een JSON string claim die de body van het verzoek verzonden naar SendGrid (een derde partij e-mail provider). De structuur van het JSON-object wordt gedefinieerd door de id's in puntnotatie van de InputParameters en de TransformationClaimTypes van de InputClaims. Getallen in de dot notatie impliceren arrays. De waarden zijn afkomstig van de waarden van inputclaims en de eigenschappen 'Waarde' van InputParameters.

- Input claims :
  - **e-mail**, transformatie claim type **personalisaties.0.to.0.email**: "someone@example.com
  - **otp**, transformatie claim type **personalisaties.0.dynamic_template_data.otp** "346349"
- Invoerparameter:
  - **template_id**: "d-4c56ffb40fa648b1a6822283df94f60"
  - **from.email**:service@contoso.com"
  - **personalisaties.0.subject** "Contoso-accountverificatiecode"
- Output claim:
  - **requestBody**: JSON-waarde

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Haal een opgegeven element uit een JSON-gegevens.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputJson | tekenreeks | De ClaimTypes die worden gebruikt door de claimtransformatie om het artikel te krijgen. |
| Inputparameter | claimToExtract | tekenreeks | de naam van het te extraheren JSON-element. |
| Uitvoerclaim | geëxtraheerdClaim | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is aangeroepen, de elementwaarde die is opgegeven in de parameter _claimToExtract.The_ ClaimType that is produced after this claims transformation has in voked in in the element value specified in the claimToExtract input parameter. |

In het volgende voorbeeld haalde de `emailAddress` claimtransformatie het element uit de JSON-gegevens:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Iemand"}
- Invoerparameter:
    - **claimToExtract**: emailAddress
- Output claims:
  - **geëxtraheerdClaim**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Ontvang een lijst met opgegeven elementen uit Json-gegevens.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | jsonSourceClaim | tekenreeks | De ClaimTypes die worden gebruikt door de claimtransformatie om de claims te krijgen. |
| Inputparameter | errorOnMissingClaims | booleaans | Hiermee geeft u op of u een fout moet maken als een van de claims ontbreekt. |
| Inputparameter | includeEmptyClaims | tekenreeks | Geef op of lege claims moeten worden opgenomen. |
| Inputparameter | jsonSourceKeyName | tekenreeks | Elementsleutelnaam |
| Inputparameter | jsonSourceValueName | tekenreeks | De waardenaam van het element |
| Uitvoerclaim | Verzameling | string, int, booleaan en datetime |Lijst van claims te halen. De naam van de claim moet gelijk zijn aan die welke is opgegeven in _de inputclaim van JSonSourceClaim._ |

In het volgende voorbeeld haalt de claimtransformatie de volgende claims uit: e-mail (tekenreeks), displayName (tekenreeks), membershipNum (int), actief (booleaan) en geboortedatum (datetime) uit de JSON-gegevens.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoerclaims:
  - **jsonSourceClaim**: [{"key":"email","waarde":"someone@example.com"}, {"key":"displayName","waarde":"Iemand"}, {"key":"membershipNum","waarde":6353399}, {"key":"actief","waarde": true}, {"key":"geboortedatum","waarde":"1980-09-23T00:00:00:00Z"}]
- Invoerparameters:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: sleutel
    - **jsonSourceValueName**: waarde
- Output claims:
  - **e-mail**: " "someone@example.com
  - **displayName**: "Iemand"
  - **lidmaatschapNum**: 6353399
  - **actief**: waar
  - **geboortedatum**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Haalt een opgegeven numeriek (lang) element uit een JSON-gegevens.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputJson | tekenreeks | De ClaimTypes die worden gebruikt door de claimtransformatie om de claim te krijgen. |
| Inputparameter | claimToExtract | tekenreeks | De naam van het JSON-element om uit te pakken. |
| Uitvoerclaim | geëxtraheerdClaim | long | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeroepen, de waarde van het element opgegeven in de _claimToExtract_ invoerparameters. |

In het volgende voorbeeld haalt de `id` claimtransformatie het element uit de JSON-gegevens.

```JSON
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Iemand", "id" : 6353399}
- Invoerparameters
    - **claimToExtract**: id
- Output claims:
    - **geëxtraheerdClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Haalt het eerste element uit een JSON-gegevens.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputJson | tekenreeks | De ClaimTypes die door de claimtransformatie worden gebruikt om het artikel uit de JSON-gegevens te halen. |
| Uitvoerclaim | sleutel | tekenreeks | De eerste elementsleutel in de JSON. |
| Uitvoerclaim | waarde | tekenreeks | De eerste elementwaarde in de JSON. |

In het volgende voorbeeld haalt de claimtransformatie het eerste element (voornaam) uit de JSON-gegevens.

```XML
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- Output claims:
  - **sleutel**: givenName
  - **waarde**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Haalt het eerste element uit een JSON-gegevensarray.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputJsonClaim | tekenreeks | De Claimtypes die door de claimtransformatie worden gebruikt om het item uit de JSON-array te halen. |
| Uitvoerclaim | geëxtraheerdClaim | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen, het eerste element in de JSON-array. |

In het volgende voorbeeld haalt de claimtransformatie het eerste element (e-mailadres) uit de JSON-array `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputJsonClaim**:someone@example.com[" ", "Iemand", 6353399]
- Output claims:
  - **geëxtraheerdClaim**:someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Hiermee converteert u XML-gegevens naar de JSON-indeling.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | xml | tekenreeks | De Claimtypes die door de claimtransformatie worden gebruikt om de gegevens om te zetten van XML naar JSON-indeling. |
| Uitvoerclaim | json | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen, de gegevens in JSON-indeling. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

In het volgende voorbeeld converteert de claimtransformatie de volgende XML-gegevens naar JSON-indeling.

#### <a name="example"></a>Voorbeeld
Invoerclaim:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Output claim:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


