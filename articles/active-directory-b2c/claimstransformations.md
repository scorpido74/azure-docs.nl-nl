---
title: ClaimsTransformaties - Azure Active Directory B2C | Microsoft Documenten
description: Definitie van het element ClaimsTransformations in het Identity Experience Framework-schema van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e71d521dce40f6a8ec81286fcc95dc97bf10078c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189733"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het element **ClaimsTransformations** bevat een lijst met functies voor claimtransformatie die kunnen worden gebruikt in gebruikersreizen als onderdeel van een [aangepast beleid](custom-policy-overview.md). Een claimtransformatie zet een bepaalde claim om in een andere claim. In de claimtransformatie geeft u de transformatiemethode op, bijvoorbeeld door een item toe te voegen aan een tekenreeksverzameling of de behuizing van een tekenreeks te wijzigen.

Als u de lijst met functies voor claimtransformatie wilt opnemen die in de gebruikersverplaatsingen kunnen worden gebruikt, moet een XML-element voor ClaimsTransformations worden gedeclareerd onder het gedeelte BuildingBlocks van het beleid.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

Het element **Claimtransformatie** bevat de volgende kenmerken:

| Kenmerk |Vereist | Beschrijving |
| --------- |-------- | ----------- |
| Id |Ja | Een id die wordt gebruikt om de claimtransformatie op unieke wijze te identificeren. De id wordt verwezen vanuit andere XML-elementen in het beleid. |
| Transformatiemethode | Ja | De transformatiemethode die u wilt gebruiken in de claimtransformatie. Elke claimtransformatie heeft zijn eigen waarden. Zie de [referentie voor claimtransformatie](#claims-transformations-reference) voor een volledige lijst met de beschikbare waarden. |

## <a name="claimstransformation"></a>ClaimsTransformatie

Het element **Claimtransformatie** bevat de volgende elementen:

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


| Element | Voorvallen | Beschrijving |
| ------- | -------- | ----------- |
| Invoerclaims | 0:1 | Een lijst met **InputClaim-elementen** die claimtypen opgeven die worden gebruikt als input voor de claimtransformatie. Elk van deze elementen bevat een verwijzing naar een ClaimType die al is gedefinieerd in de sectie ClaimSchema in het beleid. |
| Inputparameters | 0:1 | Een lijst met **inputparameterelementen** die worden geleverd als input voor de claimtransformatie.
| Uitvoerclaims | 0:1 | Een lijst met **OutputClaim-elementen** die claimtypen opgeven die worden geproduceerd nadat de claimtransformatie is ingeroepen. Elk van deze elementen bevat een verwijzing naar een ClaimType dat al is gedefinieerd in de sectie ClaimSchema. |

### <a name="inputclaims"></a>Invoerclaims

Het element **InputClaims** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Invoerclaim | 1:n | Een verwacht invoerclaimtype. |

#### <a name="inputclaim"></a>Invoerclaim

Het element **InputClaim** bevat de volgende kenmerken:

| Kenmerk |Vereist | Beschrijving |
| --------- | ----------- | ----------- |
| Referentienummer van claimtype |Ja | Een verwijzing naar een ClaimType die al is gedefinieerd in de sectie ClaimsSchema in het beleid. |
| TransformationClaimType |Ja | Een id om te verwijzen naar een type transformatieclaim. Elke claimtransformatie heeft zijn eigen waarden. Zie de [referentie voor claimtransformatie](#claims-transformations-reference) voor een volledige lijst met de beschikbare waarden. |

### <a name="inputparameters"></a>Inputparameters

Het element **InputParameters** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Inputparameter | 1:n | Een verwachte invoerparameter. |

#### <a name="inputparameter"></a>Inputparameter

| Kenmerk | Vereist |Beschrijving |
| --------- | ----------- |----------- |
| Id | Ja | Een id die verwijst naar een parameter van de claimtransformatiemethode. Elke claimtransformatiemethode heeft zijn eigen waarden. Zie de tabel voor claimtransformatie voor een volledige lijst met de beschikbare waarden. |
| DataType | Ja | Het type gegevens van de parameter, zoals Tekenreeks, Boolean, Int of DateTime volgens de datatype-opsomming in het XML-schema voor het aangepaste beleid. Dit type wordt gebruikt om rekenkundige bewerkingen correct uit te voeren. Elke claimtransformatie heeft zijn eigen waarden. Zie de [referentie voor claimtransformatie](#claims-transformations-reference) voor een volledige lijst met de beschikbare waarden. |
| Waarde | Ja | Een waarde die letterlijk wordt doorgegeven aan de transformatie. Sommige waarden zijn willekeurig, sommige van hen die u selecteert uit de claimtransformatiemethode. |

### <a name="outputclaims"></a>Uitvoerclaims

Het element **OutputClaims** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Uitvoerclaim | 0:n | Een verwacht uitvoerclaimtype. |

#### <a name="outputclaim"></a>Uitvoerclaim

Het element **OutputClaim** bevat de volgende kenmerken:

| Kenmerk |Vereist | Beschrijving |
| --------- | ----------- |----------- |
| Referentienummer van claimtype | Ja | Een verwijzing naar een ClaimType die al is gedefinieerd in de sectie ClaimsSchema in het beleid.
| TransformationClaimType | Ja | Een id om te verwijzen naar een type transformatieclaim. Elke claimtransformatie heeft zijn eigen waarden. Zie de [referentie voor claimtransformatie](#claims-transformations-reference) voor een volledige lijst met de beschikbare waarden. |

Als invoerclaim en de uitvoerclaim hetzelfde type zijn (tekenreeks of booleaan), u dezelfde invoerclaim gebruiken als de uitvoerclaim. In dit geval verandert de claimtransformatie de invoerclaim met de uitvoerwaarde.

## <a name="example"></a>Voorbeeld

U bijvoorbeeld de laatste versie van uw servicevoorwaarden opslaan die de gebruiker heeft geaccepteerd. Wanneer u de servicevoorwaarden bijwerkt, u de gebruiker vragen de nieuwe versie te accepteren. In het volgende voorbeeld vergelijkt de transformatie van **HastoSVersionChanged** claims de waarde van de **TOSVersion-claim** met de waarde van de **Claim LastTOSAcceptedVersion** en retourneert vervolgens de booleaanse **TOSVersionChanged-claim.**

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

## <a name="claims-transformations-reference"></a>Verwijzing naar claimtransformaties

Zie de volgende referentiepagina's voor voorbeelden van claimstransformaties:

- [Booleaanse](boolean-transformations.md)
- [Datum](date-transformations.md)
- [Geheel getal](integer-transformations.md)
- [Json](json-transformations.md)
- [Algemeen](general-transformations.md)
- [Sociale rekening](social-transformations.md)
- [Tekenreeks](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

