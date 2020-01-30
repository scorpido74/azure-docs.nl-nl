---
title: ClaimsTransformations-Azure Active Directory B2C | Microsoft Docs
description: De definitie van het element ClaimsTransformations in het Framework met identiteits ervaring van Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66c94f08638895c85836fda37c3ae61f3857ee51
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836691"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het **ClaimsTransformations** -element bevat een lijst met functies voor het transformeren van claims die kunnen worden gebruikt in gebruikers ritten als onderdeel van een [aangepast beleid](custom-policy-overview.md). Een claim transformatie zet een opgegeven claim om in een andere. In de claim transformatie geeft u de transformatie methode op, bijvoorbeeld het toevoegen van een item aan een teken reeks verzameling of het wijzigen van het hoofdletter gebruik van een teken reeks.

Als u de lijst met functies voor het transformeren van claims wilt opnemen die kunnen worden gebruikt in de gebruikers ritten, moet een ClaimsTransformations XML-element worden gedeclareerd onder de sectie BuildingBlocks van het beleid.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

Het **ClaimsTransformation** -element bevat de volgende kenmerken:

| Kenmerk |Verplicht | Beschrijving |
| --------- |-------- | ----------- |
| Id |Ja | Een id die wordt gebruikt om de claim transformatie uniek te identificeren. Er wordt naar de id verwezen vanuit andere XML-elementen in het beleid. |
| TransformationMethod | Ja | De transformatie methode die in de claim transformatie moet worden gebruikt. Elke claim transformatie heeft zijn eigen waarden. Zie de [verwijzing naar claims transformeren](#claims-transformations-reference) voor een volledige lijst met de beschik bare waarden. |

## <a name="claimstransformation"></a>ClaimsTransformation

Het **ClaimsTransformation** -element bevat de volgende elementen:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>                
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Element | Instanties | Beschrijving |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Een lijst met **input claim** -elementen waarmee claim typen worden opgegeven die worden gebruikt als invoer voor de claims-trans formatie. Elk van deze elementen bevat een verwijzing naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleid. |
| InputParameters | 0:1 | Een lijst met **input parameter** -elementen die als invoer voor de claim transformatie worden opgegeven.  
| OutputClaims | 0:1 | Een lijst met **output claim** -elementen die claim typen opgeven die worden geproduceerd nadat de ClaimsTransformation is aangeroepen. Elk van deze elementen bevat een verwijzing naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

Het element **InputClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Een verwachte invoer claim type. |

#### <a name="inputclaim"></a>InputClaim

Het **input claim** -element bevat de volgende kenmerken:

| Kenmerk |Verplicht | Beschrijving |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Ja | Een verwijzing naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleid. |
| TransformationClaimType |Ja | Een id om te verwijzen naar een type transformatie claim. Elke claim transformatie heeft zijn eigen waarden. Zie de [verwijzing naar claims transformeren](#claims-transformations-reference) voor een volledige lijst met de beschik bare waarden. |

### <a name="inputparameters"></a>InputParameters

Het element **Input parameters** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| InputParameter | 1: n | Een verwachte invoer parameter. |

#### <a name="inputparameter"></a>InputParameter

| Kenmerk | Verplicht |Beschrijving |
| --------- | ----------- |----------- |
| Id | Ja | Een id die een verwijzing is naar een para meter van de methode voor het transformeren van claims. Elke claim transformatie methode heeft zijn eigen waarden. Zie de tabel claim transformatie voor een volledige lijst van de beschik bare waarden. |
| Gegevenstype | Ja | Het type gegevens van de para meter, zoals teken reeks, Booleaanse waarde, int of DateTime, zoals wordt bepaald door de opsomming van het gegevens type in het XML-schema van het aangepaste beleid. Dit type wordt gebruikt om reken kundige bewerkingen correct uit te voeren. Elke claim transformatie heeft zijn eigen waarden. Zie de [verwijzing naar claims transformeren](#claims-transformations-reference) voor een volledige lijst met de beschik bare waarden. |
| Waarde | Ja | Een waarde die Verbatim wordt door gegeven aan de trans formatie. Sommige waarden zijn wille keurig, wat u selecteert in de methode voor het transformeren van claims. |

### <a name="outputclaims"></a>OutputClaims

Het element **OutputClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | Een verwacht type uitvoer claim. |

#### <a name="outputclaim"></a>OutputClaim 

Het **output claim** -element bevat de volgende kenmerken:

| Kenmerk |Verplicht | Beschrijving |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Ja | Een verwijzing naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleid.
| TransformationClaimType | Ja | Een id om te verwijzen naar een type transformatie claim. Elke claim transformatie heeft zijn eigen waarden. Zie de [verwijzing naar claims transformeren](#claims-transformations-reference) voor een volledige lijst met de beschik bare waarden. |
 
Als de invoer claim en de uitvoer claim van hetzelfde type (teken reeks of Booleaanse waarde) zijn, kunt u dezelfde invoer claim gebruiken als de uitvoer claim. In dit geval wijzigt de claim transformatie de invoer claim met de uitvoer waarde.

## <a name="example"></a>Voorbeeld

U kunt bijvoorbeeld de laatste versie van uw service voorwaarden opslaan die de gebruiker heeft geaccepteerd. Wanneer u de service voorwaarden bijwerkt, kunt u de gebruiker vragen om de nieuwe versie te accepteren. In het volgende voor beeld vergelijkt de **HasTOSVersionChanged** -claim transformatie de waarde van de **TOSVersion** -claim met de waarde van de **LastTOSAcceptedVersion** -claim en retourneert vervolgens de Boolean **TOSVersionChanged** -claim.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Verwijzing naar claim transformaties

Voor voor beelden van claim transformaties raadpleegt u de volgende referentie pagina's:

- [Boolean](boolean-transformations.md)
- [datum](date-transformations.md)
- [Geheeltallige](integer-transformations.md)
- [JSON](json-transformations.md)
- [Algemeen](general-transformations.md)
- [Sociaal account](social-transformations.md)
- [String](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

