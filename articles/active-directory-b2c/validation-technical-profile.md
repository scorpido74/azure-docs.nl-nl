---
title: Een validatietechnisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Claimen valideren met behulp van een validatietechnisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1eaf159149bb353b1cf0474aad5bc233decddc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481565"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een validatietechnisch profiel definiëren in een aangepast Azure Directory B2C-beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een validatietechnisch profiel is een gewoon technisch profiel van elk protocol, zoals [Azure Active Directory](active-directory-technical-profile.md) of een REST [API.](restful-technical-profile.md) Het technische validatieprofiel retourneert uitvoerclaims of retourneert 4xx HTTP-statuscode met de volgende gegevens. Zie [Foutbericht retourneren](restful-technical-profile.md#returning-error-message) voor meer informatie

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

De omvang van de uitvoerclaims van een technisch validatieprofiel is beperkt tot het [zelfverklaarde technische profiel](self-asserted-technical-profile.md) dat een beroep doet op het technische validatieprofiel en de technische validatieprofielen. Als u de uitvoerclaims in de volgende orchestration-stap wilt gebruiken, voegt u de uitvoerclaims toe aan het zelfverklaarde technische profiel dat het technische validatieprofiel aanroept.

Validatietechnische profielen worden uitgevoerd in de volgorde die ze worden weergegeven in het element **ValidatieTechnischeProfielen.** U in een technisch validatieprofiel configureren of de uitvoering van eventuele volgende technische validatieprofielen moet worden voortgezet als het technische validatieprofiel een fout oplevert of succesvol is.

Een validatietechnisch profiel kan voorwaardelijk worden uitgevoerd op basis van randvoorwaarden die zijn gedefinieerd in het element **ValidatieTechnicalProfile.** U bijvoorbeeld controleren of er een specifieke claim bestaat of dat een claim al dan niet gelijk is aan de opgegeven waarde.

Een zelfgeclaimd technisch profiel kan een validatietechnisch profiel definiëren dat moet worden gebruikt voor het valideren van sommige of alle uitvoerclaims. Alle invoerclaims van het technische profiel waarnaar wordt verwezen, moeten worden weergegeven in de uitvoerclaims van het technische profiel van de verwijzingnaar de validatie.

> [!NOTE]
> Alleen zelfverklaarde technische profielen kunnen gebruik maken van technische validatieprofielen. Als u de uitvoerclaims van niet-zelfverklaarde technische profielen moet valideren, u overwegen een extra orkestratiestap in uw gebruikersreis te gebruiken om het technische profiel dat verantwoordelijk is voor de validatie te kunnen aanpassen.

## <a name="validationtechnicalprofiles"></a>ValidatieTechnische profielen

Het element **ValidatieTechnischeProfielen** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| ValidatieTechnisch Profiel | 1:n | Een technisch profiel dat moet worden gebruikt voor het valideren van sommige of alle outputclaims van het verwijzende technische profiel. |

Het element **ValidationTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel dat al is gedefinieerd in het beleid of het bovenliggende beleid. |
|Continueonfout|Nee| Als u aangeeft of de validatie van latere technische validatieprofielen moet worden voortgezet als dit technische validatieprofiel een fout oplevert. Mogelijke `true` waarden: `false` of (standaard wordt de verwerking van verdere validatieprofielen gestopt en er wordt een fout geretourneerd). |
|ContinueOnSuccess | Nee | Als u aangeeft of de validatie van latere validatieprofielen moet worden voortgezet als dit technische validatieprofiel slaagt. Mogelijke `true` waarden: `false`of . De standaardinstelling is, `true`wat betekent dat de verwerking van verdere validatieprofielen wordt voortgezet. |

Het element **ValidationTechnicalProfile** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Voorwaarden | 0:1 | Een lijst met randvoorwaarden waaraan moet worden voldaan om het validatietechnische profiel uit te voeren. |

Het element **Voorwaarde** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Type` | Ja | Het type controle of query dat moet worden uitgevoerd voor de voorwaarde. Ofwel `ClaimsExist` is opgegeven om ervoor te zorgen dat acties moeten worden uitgevoerd als `ClaimEquals` de opgegeven claims bestaan in de huidige claimset van de gebruiker, of is gespecificeerd dat de acties moeten worden uitgevoerd als de opgegeven claim bestaat en de waarde ervan gelijk is aan de opgegeven waarde. |
| `ExecuteActionsIf` | Ja | Geeft aan of de acties in de voorwaarde moeten worden uitgevoerd als de test waar of onwaar is. |

Het **element Voorwaarde** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Waarde | 1:n | De gegevens die door de controle worden gebruikt. Als het type van `ClaimsExist`deze controle is, geeft dit veld een ClaimTypeReferenceId op waarvoor u wilt query.If the type of this check is, this field specifies a ClaimTypeReferenceId to query for. Als het type `ClaimEquals`controle is, geeft dit veld een ClaimTypeReferenceId op waar naar wordt opgevraagd. Terwijl een ander waardeelement de te controleren waarde bevat.|
| Actie | 1:1 | De actie die moet worden ondernomen als de voorwaarde controle binnen een orchestration stap waar is. De waarde **Action** van de `SkipThisValidationTechnicalProfile`actie is ingesteld op . Hiermee geeft u op dat het bijbehorende technische profiel voor validatie niet mag worden uitgevoerd. |

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld worden de technische validatieprofielen gebruikt:

1. Het eerste technische validatieprofiel controleert de gebruikersreferenties en gaat niet verder als er een fout optreedt, zoals een ongeldige gebruikersnaam of een slecht wachtwoord.
2. Het volgende technische validatieprofiel wordt niet uitgevoerd als de userType-claim niet bestaat `Partner`of als de waarde van de userType is. Het technische validatieprofiel probeert het gebruikersprofiel uit de interne klantendatabase te lezen en door te gaan als er een fout optreedt, zoals rest-API-service die niet beschikbaar is of interne fouten.
3. Het laatste technische validatieprofiel wordt niet uitgevoerd als de userType-claim niet heeft `Customer`bestaan of als de waarde van de userType is. Het technische validatieprofiel probeert het gebruikersprofiel uit de interne partnerdatabase te lezen en gaat door als er een fout optreedt, zoals rest-API-service die niet beschikbaar is of interne fouten.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
