---
title: Predicaten en PredicaatValidaties
titleSuffix: Azure AD B2C
description: Voorkom dat misvormde gegevens worden toegevoegd aan uw Azure AD B2C-tenant met behulp van aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 887c9432f04cce775e045bb6da83f0af4a4a4bce
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396891"
---
# <a name="predicates-and-predicatevalidations"></a>Predicaten en PredicaatValidaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met de elementen **Predicaten** en **Predicatevaliden** u een validatieproces uitvoeren om ervoor te zorgen dat alleen goed gevormde gegevens worden ingevoerd in uw Azure Active Directory B2C -tenant (Azure AD B2C).

In het volgende diagram ziet u de relatie tussen de elementen:

![Diagram met de relatie Predicaten en Predicaatvalidatie](./media/predicates/predicates.png)

## <a name="predicates"></a>Predicaten

Het **element Predicaat** definieert een basisvalidatie om de `true` `false`waarde van een claimtype en retourzendingen te controleren of . De validatie wordt uitgevoerd met behulp van een opgegeven **methodeelement** en een set **parameterelementen die** relevant zijn voor de methode. Een predicaat kan bijvoorbeeld controleren of de lengte van een tekenreeksclaimwaarde binnen het bereik van de opgegeven minimum- en maximumparameters ligt of dat een tekenreeksclaimwaarde een tekenset bevat. Het element **UserHelpText** biedt een foutbericht voor gebruikers als de controle mislukt. De waarde van **userHelptext-element** kan worden gelokaliseerd met behulp van [taalaanpassing](localization.md).

Het element **Predicaten** moet direct na het element **ClaimsSchema** binnen het element [Bouwstenen](buildingblocks.md) worden weergegeven.

Het element **Predicaten** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Predikaat | 1:n | Een lijst met predicaten. |

Het **element Predicaat** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt voor het predicaat. Andere elementen kunnen deze id gebruiken in het beleid. |
| Methode | Ja | Het methodetype dat moet worden gebruikt voor validatie. Mogelijke waarden: [IsLengthRange](#islengthrange), [MatchesRegex](#matchesregex), [IncludesCharacters](#includescharacters), of [IsDateRange](#isdaterange).  |
| HelpTekst | Nee | Een foutbericht voor gebruikers als de controle mislukt. Deze tekenreeks kan worden gelokaliseerd met behulp van het [aanpassen van](localization.md) de taal |

Het **element Predicaat** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | (Afgeschaft) Een foutbericht voor gebruikers als de controle mislukt. |
| Parameters | 1:1 | De parameters voor het methodetype van de tekenreeksvalidatie. |

Het element **Parameters** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Parameter | 1:n | De parameters voor het methodetype van de tekenreeksvalidatie. |

Het element **Parameter** bevat de volgende kenmerken:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Id | 1:1 | De id van de parameter. |

### <a name="predicate-methods"></a>Predicaatmethoden

#### <a name="islengthrange"></a>IsLengthRange (IsLengthRange)

De methode IsLengthRange controleert of de lengte van een tekenreeksclaimwaarde binnen het bereik van de opgegeven minimum- en maximumparameters ligt. Het predicaatelement ondersteunt de volgende parameters:

| Parameter | Vereist | Beschrijving |
| ------- | ----------- | ----------- |
| Maximum | Ja | Het maximum aantal tekens dat kan worden ingevoerd. |
| Minimum | Ja | Het minimum aantal tekens dat moet worden ingevoerd. |


In het volgende voorbeeld wordt een `Minimum` IsLengthRange-methode met de parameters weergegeven en `Maximum` die het lengtebereik van de tekenreeks opgeeft:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>WedstrijdenRegex

De methode MatchesRegex controleert of een tekenreeksclaimwaarde overeenkomt met een reguliere expressie. Het predicaatelement ondersteunt de volgende parameters:

| Parameter | Vereist | Beschrijving |
| ------- | ----------- | ----------- |
| Reguliere expressie | Ja | Het reguliere expressiepatroon dat overeenkomt. |

In het volgende `MatchesRegex` voorbeeld wordt `RegularExpression` een methode met de parameter weergegeven die een reguliere expressie opgeeft:

```XML
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>InclusiefTekens

De methode IncludesCharacters controleert of een tekenreeksclaimwaarde een tekenset bevat. Het predicaatelement ondersteunt de volgende parameters:

| Parameter | Vereist | Beschrijving |
| ------- | ----------- | ----------- |
| Tekenset | Ja | De set tekens die kunnen worden ingevoerd. Kleine `a-z`letters, `A-Z`hoofdletters, cijfers `0-9`of een lijst met symbolen, `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!`zoals . |

In het volgende `IncludesCharacters` voorbeeld wordt `CharacterSet` een methode met de parameter weergegeven die de set tekens opgeeft:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange IsDateRange

De methode IsDateRange controleert of een datumclaimwaarde zich bevindt tussen een bereik van minimale en maximale opgegeven parameters. Het predicaatelement ondersteunt de volgende parameters:

| Parameter | Vereist | Beschrijving |
| ------- | ----------- | ----------- |
| Maximum | Ja | De grootst mogelijke datum die kan worden ingevoerd. De indeling van `yyyy-mm-dd` de datum `Today`volgt conventie, of . |
| Minimum | Ja | De kleinst mogelijke datum die kan worden ingevoerd. De indeling van `yyyy-mm-dd` de datum `Today`volgt conventie, of .|

In het volgende `IsDateRange` voorbeeld wordt `Minimum` `Maximum` een methode met de parameters `yyyy-mm-dd` `Today`weergegeven en die het datumbereik opgeeft met een notatie van en .

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicaatValidaties

Terwijl de predicaten de validatie definiëren om te controleren op een claimtype, groepeert de **PredicateValidations** een set predicaten om een gebruikersinvoervalidatie te vormen die kan worden toegepast op een claimtype. Elk **Element PredicaatValidatie** bevat een set **PredicaatGroep-elementen** die een set **PredicateReference-elementen** bevatten die naar een **predicaat**verwijst. Om de validatie te halen, moet de waarde van de claim alle tests van een predicaat onder alle **Predicaatgroep** met hun set **van PredicaatReference-elementen** doorstaan.

Het element **PredicaatValidaties** moet direct na het element **Predicaten** binnen het element [Bouwstenen](buildingblocks.md) worden weergegeven.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

Het element **PredicaatValidaties** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| PredicaatValidatie | 1:n | Een lijst met predicaatvalidatie. |

Het element **PredicaatValidatie** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt voor de predicaatvalidatie. Het element **ClaimType** kan deze id gebruiken in het beleid. |

Het element **PredicaatValidatie** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Predicaatgroepen | 1:n | Een lijst met predicaatgroepen. |

Het element **PredicateGroups** bevat het volgende element:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| PredicaatGroep | 1:n | Een lijst met predicaten. |

Het element **PredicaatGroep** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt voor de predicaatgroep.  |

Het **element PredicaatGroep** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  Een beschrijving van het predicaat dat nuttig kan zijn voor gebruikers om te weten welke waarde ze moeten typen. |
| PredicaatVerwijzingen | 1:n | Een lijst met predicaatreferenties. |

Het element **PredicaatVerwijzingen** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| MatchAtleast | Nee | Hiermee geeft u op dat de waarde moet overeenkomen met ten minste dat veel predicaatdefinities voor de te accepteren invoer. Als deze niet is opgegeven, moet de waarde overeenkomen met alle predicaatdefinities. |

Het element **PredicaatReferenties** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| PredicaatReferentie | 1:n | Een verwijzing naar een predicaat. |

Het **element PredicaatReferentie** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt voor de predicaatvalidatie.  |


## <a name="configure-password-complexity"></a>Complexiteit van wachtwoorden configureren

Met **Predicaten** en **PredicateValidationsInput** u de complexiteitsvereisten beheren voor wachtwoorden die door een gebruiker worden verstrekt bij het maken van een account. Azure AD B2C gebruikt standaard sterke wachtwoorden. Azure AD B2C ondersteunt ook configuratieopties om de complexiteit van wachtwoorden te beheren die klanten kunnen gebruiken. U de complexiteit van wachtwoorden definiëren met behulp van deze predicaatelementen:

- **IsLengthBetween8And64** met `IsLengthRange` behulp van de methode, valideert dat het wachtwoord moet worden tussen de 8 en 64 tekens.
- **Kleine letters** `IncludesCharacters` met behulp van de methode, valideert dat het wachtwoord een kleine letter bevat.
- **Hoofdletters** met `IncludesCharacters` behulp van de methode, valideert dat het wachtwoord een hoofdletter bevat.
- **Getal** met `IncludesCharacters` behulp van de methode, valideert dat het wachtwoord een cijfer bevat.
- **Symbool** met `IncludesCharacters` behulp van de methode, valideert dat het wachtwoord een van de verschillende symbooltekens bevat.
- **Pin** met `MatchesRegex` behulp van de methode, valideert dat het wachtwoord alleen nummers bevat.
- **AllowedAADCharacters** met `MatchesRegex` behulp van de methode, valideert dat het wachtwoord alleen ongeldig teken werd verstrekt.
- **Niet toegestaanWhitespace** `MatchesRegex` met behulp van de methode, valideert dat het wachtwoord niet begint of eindigt met een witruimteteken.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Nadat u de basisvalidaties hebt gedefinieerd, u ze combineren en een set wachtwoordbeleidsregels maken die u in uw beleid gebruiken:

- **SimplePassword** valideert de DisallowedWhitespace, AllowedAADCharacters en IsLengthBetween8And64
- **StrongPassword** valideert de DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. De laatste `CharacterClasses` groep voert een extra `MatchAtLeast` set predicaten uit met ingesteld op 3. Het gebruikerswachtwoord moet tussen 8 en 16 tekens liggen en drie van de volgende tekens: Kleine letters, Hoofdletters, Nummer of Symbool.
- **CustomPassword** valideert alleen DisallowedWhitespace, AllowedAADCharacters. De gebruiker kan dus elk wachtwoord met elke lengte opgeven, zolang de tekens geldig zijn.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Voeg in uw claimtype het **element PredicaatValidatieReferentie** toe en geef de id op als een van de predicaatvalidaties, zoals SimplePassword, StrongPassword of CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Als volgt ziet u hoe de elementen worden georganiseerd wanneer Azure AD B2C het foutbericht weergeeft:

![Voorbeeld van het voorbeeld van het voorbeeld van de complexiteit van het predicaat en de complexiteit van het wachtwoord van Predicaat en PredicaatGroep](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Een datumbereik configureren

Met de elementen **Predicaten** en **PredicatenValidatiekunt** u de minimum- en maximumdatumwaarden van het **UserInputType** beheren met behulp van een `DateTimeDropdown`. Maak hiervoor een **predicaat** met `IsDateRange` de methode en geef de minimale en maximale parameters.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Voeg een **PredicaatValidatie** toe `DateRange` met een verwijzing naar het predicaat.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Voeg in uw claimtype het element **PredicaatValidatieReferentie** toe en geef de id op als `CustomDateRange`.

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van wachtwoordcomplexiteit met behulp van aangepast beleid in Azure Active Directory B2C](custom-policy-password-complexity.md) met behulp van predicaatvalidaties.