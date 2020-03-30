---
title: Voorbeelden van tekenreeksclaims voor aangepaste beleidsregels
titleSuffix: Azure AD B2C
description: StringCollection claimt transformatievoorbeelden voor het IEF-schema (Identity Experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6aea537ebff4ae61e00861e6cafe742a7feb165e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186774"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection claimt transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voorbeelden voor het gebruik van de transformaties van tekenreeksverzamelingclaims van het Identity Experience Framework-schema in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie .

## <a name="additemtostringcollection"></a>AdditemtostringCollection

Hiermee voegt u een tekenreeksclaim toe aan een nieuwe claim voor tekenreeksverzameling met unieke waarden.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | item | tekenreeks | Het ClaimType dat aan de uitvoerclaim moet worden toegevoegd. |
| Invoerclaim | verzameling | tekenreeksVerzameling | [Optioneel] Indien opgegeven, kopieert de claimtransformatie de items uit deze verzameling en wordt het item toegevoegd aan het einde van de claim voor uitvoerverzameling. |
| Uitvoerclaim | verzameling | tekenreeksVerzameling | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld, waarbij de waarde is opgegeven in de invoerclaim. |

Gebruik deze claimtransformatie om een tekenreeks toe te voegen aan een nieuwe of bestaande tekenreeksVerzameling. Het wordt vaak gebruikt in een **AAD-UserWriteUsingAlternativeSecurityId** technisch profiel. Voordat een nieuw sociaal account wordt gemaakt, leest **CreateOtherMailsFromEmail** claimtransformatie het ClaimType en voegt de waarde toe aan het **andereMails** ClaimType.

Met de volgende claimtransformatie wordt de **e-mail** ClaimType toegevoegd aan **andereMails** ClaimType.

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

- Invoerclaims:
  - **collectie**:someone@outlook.com[" "]
  - **object**:admin@contoso.com"
- Output claims:
  - **collectie**:someone@outlook.com["admin@contoso.com", " "]

## <a name="addparametertostringcollection"></a>AddparametertostringCollection

Hiermee voegt u een tekenreeksparameter toe aan een nieuwe claim voor tekenreeksverzameling met unieke waarden.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | verzameling | tekenreeksVerzameling | [Optioneel] Indien opgegeven, kopieert de claimtransformatie de items uit deze verzameling en wordt het item toegevoegd aan het einde van de claim voor uitvoerverzameling. |
| Inputparameter | item | tekenreeks | De waarde die aan de outputclaim moet worden toegevoegd. |
| Uitvoerclaim | verzameling | tekenreeksVerzameling | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld, waarbij de waarde is opgegeven in de invoerparameter. |

Gebruik deze claimtransformatie om een tekenreekswaarde toe te voegen aan een nieuwe of bestaande tekenreeksverzameling. In het volgende voorbeeld wordtadmin@contoso.comeen constant e-mailadres ( ) toegevoegd aan de **claim otherMails.**

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

- Invoerclaims:
  - **collectie**:someone@outlook.com[" "]
- Invoerparameters
  - **object**:admin@contoso.com"
- Output claims:
  - **collectie**:someone@outlook.com["admin@contoso.com", " "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Haalt het eerste item uit de meegeleverde tekenreeksverzameling.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | verzameling | tekenreeksVerzameling | De ClaimTypes die worden gebruikt door de claimtransformatie om het artikel te krijgen. |
| Uitvoerclaim | geëxtraheerdItem | tekenreeks | De ClaimTypes die na deze ClaimTransformation worden geproduceerd, zijn ingeroepen. Het eerste item in de collectie. |

In het volgende voorbeeld leest u de **claim otherMails** en retourneert u het eerste item in de **e-mailclaim.**

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

- Invoerclaims:
  - **collectie**:someone@outlook.com["someone@contoso.com", " "]
- Output claims:
  - **geëxtraheerdItem**: "someone@outlook.com


## <a name="stringcollectioncontains"></a>StringCollectionContains

Controleert of een claimtype StringCollection een element bevat

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | tekenreeksVerzameling | Het claimtype dat moet worden doorzocht. |
|Inputparameter|item|tekenreeks|De waarde om te zoeken.|
|Inputparameter|negerenHoofdletter|tekenreeks|Hiermee geeft u op of deze vergelijking het geval moet negeren van de tekenreeksen die worden vergeleken.|
| Uitvoerclaim | outputClaim | booleaans | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. Een booleaanse indicator als de collectie zo'n tekenreeks bevat |

Na voorbeeld wordt `roles` gecontroleerd of het claimtype stringCollection de waarde van **beheerder**bevat.

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

- Invoerclaims:
    - **inputClaim**: ["reader", "auteur", "admin"]
- Invoerparameters:
    - **object**: "Beheerder"
    - **ignoreCase**: "true"
- Output claims:
    - **outputClaim**: "waar"


