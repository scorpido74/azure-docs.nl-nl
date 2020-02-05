---
title: Voor beelden van StringCollection-claim transformatie voor aangepaste beleids regels
titleSuffix: Azure AD B2C
description: StringCollection voor beelden van claim transformatie voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f91db91eff3320691a5979d9453bf515ccd59a2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982293"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection-claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden voor het gebruik van de teken reeks voor het verzamelen van claim transformaties van het Framework voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Voegt een teken reeks claim toe aan een nieuwe stringCollection-claim.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Het claim type dat aan de uitvoer claim moet worden toegevoegd. |
| InputClaim | innings | stringCollection | Beschrijving Als deze optie is opgegeven, worden de items uit deze verzameling gekopieerd en wordt het item toegevoegd aan het einde van de claim van de verzameling van de uitvoer. |
| OutputClaim | innings | stringCollection | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

Gebruik deze claim transformatie om een teken reeks toe te voegen aan een nieuwe of bestaande stringCollection. Dit wordt vaak gebruikt in een **Aad-UserWriteUsingAlternativeSecurityId-** technisch profiel. Voordat een nieuw sociaal account wordt gemaakt, leest **CreateOtherMailsFromEmail** claims Transform het claim type en voegt de waarde toe aan het **otherMails** claim type.

De volgende claim transformatie voegt het **e-mail** claim type toe aan **otherMails** claim type.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **verzameling**: ["someone@outlook.com"]
  - **item**: "admin@contoso.com"
- Uitvoer claims:
  - **verzameling**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Voegt een teken reeks parameter toe aan een nieuwe stringCollection-claim.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | innings | stringCollection | Beschrijving Als deze optie is opgegeven, worden de items uit deze verzameling gekopieerd en wordt het item toegevoegd aan het einde van de claim van de verzameling van de uitvoer. |
| InputParameter | item | string | De waarde die moet worden toegevoegd aan de uitvoer claim. |
| OutputClaim | innings | stringCollection | De ClaimTypes die wordt geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

Gebruik deze claim transformatie om een teken reeks waarde toe te voegen aan een nieuwe of bestaande stringCollection. In het volgende voor beeld wordt een constant e-mail adres (admin@contoso.com) aan de claim **otherMails** toegevoegd.

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **verzameling**: ["someone@outlook.com"]
- Invoer parameters
  - **item**: "admin@contoso.com"
- Uitvoer claims:
  - **verzameling**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Hiermee wordt het eerste item opgehaald uit de gegeven teken reeks verzameling.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | innings | stringCollection | De ClaimTypes die worden gebruikt door de claim transformatie om het item op te halen. |
| OutputClaim | extractedItem | string | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen. Het eerste item in de verzameling. |

In het volgende voor beeld wordt de **otherMails** -claim gelezen en wordt het eerste item in de **e-mail** claim geretourneerd.

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **verzameling**: ["someone@outlook.com", "someone@contoso.com"]
- Uitvoer claims:
  - **extractedItem**:someone@outlook.com


## <a name="stringcollectioncontains"></a>StringCollectionContains

Hiermee wordt gecontroleerd of een StringCollection claim type een-element bevat

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Het claim type dat moet worden doorzocht. |
|InputParameter|item|string|De waarde waarnaar moet worden gezocht.|
|InputParameter|ignoreCase|string|Hiermee geeft u op of deze vergelijking het hoofdletter gebruik moet negeren van de teken reeksen die worden vergeleken.|
| OutputClaim | outputClaim | booleaans | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. Een Booleaanse indicator als de verzameling een dergelijke teken reeks bevat |

In het volgende voor beeld wordt gecontroleerd of het claim type `roles` stringCollection de waarde **admin**bevat.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

- Invoer claims:
    - **input claim**: [' lezer ', ' Auteur ', ' beheerder ']
- Invoer parameters:
    - **item**: "beheerder"
    - **ignoreCase**: "True"
- Uitvoer claims:
    - **output claim**: "True"


