---
title: Voor beelden van StringCollection-claim transformatie voor aangepaste beleids regels
titleSuffix: Azure AD B2C
description: StringCollection voor beelden van claim transformatie voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cac7e6feb632456b63b97ead057f9ecaf49322ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729723"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection-claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden voor het gebruik van de teken reeks voor het verzamelen van claim transformaties van het Framework voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Voegt een teken reeks claim toe aan een nieuwe unieke waarde stringCollection claim.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Input claim | item | tekenreeks | Het claim type dat aan de uitvoer claim moet worden toegevoegd. |
| Input claim | verzameling | stringCollection | Beschrijving Als deze optie is opgegeven, worden de items uit deze verzameling gekopieerd en wordt het item toegevoegd aan het einde van de claim van de verzameling van de uitvoer. |
| Output claim | verzameling | stringCollection | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen, met de waarde die is opgegeven in de invoer claim. |

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
  - **item**:admin@contoso.com
- Uitvoer claims:
  - **verzameling**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Voegt een teken reeks parameter toe aan een nieuwe unieke waarde stringCollection claim.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Input claim | verzameling | stringCollection | Beschrijving Als deze optie is opgegeven, worden de items uit deze verzameling gekopieerd en wordt het item toegevoegd aan het einde van de claim van de verzameling van de uitvoer. |
| Parameter | item | tekenreeks | De waarde die moet worden toegevoegd aan de uitvoer claim. |
| Output claim | verzameling | stringCollection | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen, met de waarde die is opgegeven in de invoer parameter. |

Gebruik deze claim transformatie om een teken reeks waarde toe te voegen aan een nieuwe of bestaande stringCollection. In het volgende voor beeld wordt een constant eadmin@contoso.com-mail adres () aan de claim **otherMails** toegevoegd.

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
  - **item**:admin@contoso.com
- Uitvoer claims:
  - **verzameling**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Hiermee wordt het eerste item opgehaald uit de gegeven teken reeks verzameling.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Input claim | verzameling | stringCollection | De ClaimTypes die worden gebruikt door de claim transformatie om het item op te halen. |
| Output claim | extractedItem | tekenreeks | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen. Het eerste item in de verzameling. |

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
  - **extractedItem**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Hiermee wordt gecontroleerd of een StringCollection claim type een-element bevat

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | stringCollection | Het claim type dat moet worden doorzocht. |
|Parameter|item|tekenreeks|De waarde waarnaar moet worden gezocht.|
|Parameter|ignoreCase|tekenreeks|Hiermee geeft u op of deze vergelijking het hoofdletter gebruik moet negeren van de teken reeksen die worden vergeleken.|
| Output claim | Output claim | booleaans | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. Een Booleaanse indicator als de verzameling een dergelijke teken reeks bevat |

In het volgende voor beeld `roles` wordt gecontroleerd of het stringCollection-claim type de waarde **admin**bevat.

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

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Hiermee wordt gecontroleerd of een StringCollection claim type een claim waarde bevat.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Input claim | verzameling | stringCollection | Het claim type dat moet worden doorzocht. |
| Input claim | item|tekenreeks| Het claim type met de waarde die moet worden gezocht.|
|Parameter|ignoreCase|tekenreeks|Hiermee geeft u op of deze vergelijking het hoofdletter gebruik moet negeren van de teken reeksen die worden vergeleken.|
| Output claim | Output claim | booleaans | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. Een Booleaanse indicator als de verzameling een dergelijke teken reeks bevat |

In het volgende voor beeld `roles` wordt gecontroleerd of het claim type stringCollection de `role` waarde van het claim type bevat.

```XML
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Invoer claims:
    - **verzameling**: [' lezer ', ' Auteur ', ' beheerder ']
    - **item**: "beheerder"
- Invoer parameters:
    - **ignoreCase**: "True"
- Uitvoer claims:
    - **output claim**: "True"
