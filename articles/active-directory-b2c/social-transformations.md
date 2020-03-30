---
title: Voorbeelden van transformatie van sociale accountclaims voor aangepast beleid
titleSuffix: Azure AD B2C
description: Voorbeelden van transformatievan sociale accountclaims voor het IEF-schema (Identity Experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb713651aca266ab2546ff26c3cd0175a4cbc289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183751"
---
# <a name="social-accounts-claims-transformations"></a>Sociale accounts claimt transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C) `userIdentities` worden identiteiten van sociale accounts opgeslagen in een kenmerk van een **alternatief beveiligingsclaimtype.** Elk item in de **alternatieve SecurityIdCollection** geeft de uitgever (naam `issuerUserId`van de identiteitsprovider, zoals facebook.com) en de , die een unieke gebruikers-id voor de emittent.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

In dit artikel vindt u voorbeelden voor het gebruik van de transformaties van sociale accountclaims van het Identity Experience Framework-schema in Azure AD B2C. Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie .

## <a name="createalternativesecurityid"></a>Alternatieve beveiligingsid maken

Hiermee maakt u een JSON-weergave van de alternatieve Beveiligingsid-eigenschap van de gebruiker die kan worden gebruikt in de aanroepen naar Azure Active Directory. Zie het [AlternativeSecurityId-schema](https://docs.microsoft.com/graph/api/resources/alternativesecurityid) voor meer informatie.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | sleutel | tekenreeks | Het ClaimType dat de unieke gebruikers-id opgeeft die wordt gebruikt door de aanbieder van sociale identiteit. |
| Invoerclaim | identityProvider | tekenreeks | Het ClaimType dat de naam van de sociale accountidentiteitprovider opgeeft, zoals facebook.com. |
| Uitvoerclaim | alternatiefSecurityId | tekenreeks | Het ClaimType dat wordt geproduceerd nadat de claimtransformatie is ingeroepen. Bevat informatie over de identiteit van een gebruiker van een sociaal account. De **emittent** is de `identityProvider` waarde van de vordering. De **issuerUserId** is de `key` waarde van de claim in base64-formaat. |

Gebruik deze claimtransformatie `alternativeSecurityId` om een ClaimType te genereren. Het wordt gebruikt door alle technische profielen `Facebook-OAUTH`van sociale identiteitsaanbieders, zoals . De volgende claimtransformatie ontvangt de gebruikersnaam voor gebruikersen en de naam van de identiteitsprovider. De uitvoer van dit technische profiel is een JSON-tekenreeksindeling die kan worden gebruikt in Azure AD-directoryservices.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **sleutel**: 12334
    - **identityProvider**: Facebook.com
- Output claims:
    - **alternativeSecurityId**: { "emittent": "facebook.com", "issuerUserId": "MTA4MTQ2MdgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Hiermee `AlternativeSecurityId` voegt `alternativeSecurityIdCollection` u een claim toe.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | item | tekenreeks | Het ClaimType dat aan de uitvoerclaim moet worden toegevoegd. |
| Invoerclaim | verzameling | alternatiefSecurityIdCollection | De ClaimTypes die worden gebruikt door de claimtransformatie indien beschikbaar in het beleid. Indien verstrekt, voegt de `item` schadetransformatie het aan het eind van de inzameling toe. |
| Uitvoerclaim | verzameling | alternatiefSecurityIdCollection | De ClaimTypes die na deze ClaimTransformation worden geproduceerd, zijn ingeroepen. De nieuwe collectie die zowel `collection` de `item`items van input als . |

In het volgende voorbeeld wordt een nieuwe sociale identiteit gekoppeld aan een bestaand account. Een nieuwe sociale identiteit koppelen:
1. In de **aad-userreadusingAlternativeSecurityId** en **AAD-UserReadUsingObjectId** technische profielen, output van de gebruiker **alternatieveSecurityIds** claim.
1. Vraag de gebruiker om in te loggen bij een van de identiteitsproviders die niet aan deze gebruiker zijn gekoppeld.
1. Met de transformatie **van CreateAlternativeSecurityId-claims** maakt u een nieuw **alternatief Beveiligingsid-claimtype** met een naam van`AlternativeSecurityId2`
1. Bel de **AddItemToAlternativeSecurityIdCollection** claimt transformatie om de **AlternativeSecurityId2-claim** toe te voegen aan de bestaande **AlternativeSecurityIds-claim.**
1. Blijf de **alternatieve SecurityIds** claimen op het gebruikersaccount

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **object**: { "emittent": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collectie**: [ { "emittent": "live.com", "issuerUserId": "MTA4MTQ2MdgyOTI3MDUyNTYzMjcw" } ]
- Output claims:
    - **collectie**: [ { "emittent": "live.com", "issuerUserId": "MTA4MTQ2MdgyOTI3MDUyNTYzMjcw" }, { "emittent": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Retourneert lijst van emittenten uit de **alternatieve SecurityIdCollection-claim** in een nieuwe **stringCollection-claim.**

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | alternatiefSecurityIdCollection | alternatiefSecurityIdCollection | Het ClaimType dat moet worden gebruikt om de lijst van identiteitsproviders (uitgever) te krijgen. |
| Uitvoerclaim | identityProvidersCollectie | tekenreeksVerzameling | De ClaimTypes die na deze ClaimTransformation worden geproduceerd, zijn ingeroepen. Lijst van identiteitsproviders associëren met de alternatieveSecurityIdCollection-invoerclaim |

De volgende claims transformatie leest de gebruiker **alternativeSecurityIds** claim en haalt de lijst van namen van identiteitsprovideren die zijn gekoppeld aan dat account. Gebruik **uitvoeridentiteitProvidersCollection** om de gebruiker de lijst weer te geven van identiteitsproviders die aan het account zijn gekoppeld. Of filter op de selectiepagina van de identiteitsprovider de lijst met identiteitsproviders op basis van de claim van Output **IdentityProvidersCollection.** De gebruiker kan er dus voor kiezen om een nieuwe sociale identiteit te koppelen die nog niet aan het account is gekoppeld.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoerclaims:
    - **alternativeSecurityIdCollection**: [ { "emittent": "google.com", "issuerUserId": "MTA4MTQ2MdgyOTI3MDUyNTYzMjcw" }, { "emittent": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Output claims:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>AlternativeSecurityidByIdentityProvider verwijderen

Hiermee verwijdert u een **AlternativeSecurityId** uit een **alternatieve SecurityIdCollection-claim.**

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | identityProvider | tekenreeks | Het ClaimType dat de naam van de identiteitsprovider bevat die uit de verzameling moet worden verwijderd. |
| Invoerclaim | verzameling | alternatiefSecurityIdCollection | De ClaimTypes die worden gebruikt door de claimtransformatie. De claimtransformatie verwijdert de identityProvider uit de collectie. |
| Uitvoerclaim | verzameling | alternatiefSecurityIdCollection | De ClaimTypes die na deze ClaimTransformation worden geproduceerd, zijn ingeroepen. De nieuwe collectie, nadat de identityProvider uit de collectie is verwijderd. |

In het volgende voorbeeld wordt een van de sociale identiteit en een bestaand account losgemaakt. Een sociale identiteit ontkoppelen:
1. In de **aad-userreadusingAlternativeSecurityId** en **AAD-UserReadUsingObjectId** technische profielen, output van de gebruiker **alternatieveSecurityIds** claim.
2. Vraag de gebruiker welk sociaal account moet worden verwijderd uit de lijstidentiteitsproviders die aan deze gebruiker zijn gekoppeld.
3. Noem een technisch profiel voor claimtransformatie dat de **RemoveAlternativeSecurityIdByIdentityProvider-claimtransformatie** aanroept, die de geselecteerde sociale identiteit heeft verwijderd met de naam van de identiteitsprovider.
4. Blijf de **alternatieveSecurityIds** claimen op het gebruikersaccount.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **identityProvider**: facebook.com
    - **collectie**: [ { "emittent": "live.com", "issuerUserId": "MTA4MTQ2MdgyOTI3MDUyNTYzMjcw" }, { "emittent": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Output claims:
    - **collectie**: [ { "emittent": "live.com", "issuerUserId": "MTA4MTQ2MdgyOTI3MDUyNTYzMjcw" } ]
