---
title: Lokalisatie-Azure Active Directory B2C
description: Geef het lokalisatie-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126757"
---
# <a name="localization"></a>Lokalisatie

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het **lokalisatie** -element biedt ondersteuning voor meerdere land instellingen of talen in het beleid voor de gebruikers ritten. Met de lokalisatie ondersteuning in beleids regels kunt u:

- Stel de expliciete lijst met ondersteunde talen in een beleid in en kies een standaard taal.
- Taalspecifieke teken reeksen en verzamelingen opgeven.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

Het **lokalisatie** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Ingeschakeld | Nee | Mogelijke waarden: `true` of `false`. |

Het **lokalisatie** -element bevat de volgende XML-elementen

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Lijst met ondersteunde talen. |
| LocalizedResources | 0: n | Lijst met gelokaliseerde resources. |

## <a name="supportedlanguages"></a>SupportedLanguages

Het **SupportedLanguages** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Standaardtaal | Ja | De taal die moet worden gebruikt als standaard voor gelokaliseerde resources. |
| MergeBehavior | Nee | Een opsommings waarde van waarden die samen worden samengevoegd met een claim type dat aanwezig is in een bovenliggend beleid met dezelfde id. Gebruik dit kenmerk wanneer u een claim overschrijft die is opgegeven in basis beleid. Mogelijke waarden: `Append`, `Prepend`of `ReplaceAll`. De waarde `Append` geeft aan dat het verzamelen van gegevens moet worden toegevoegd aan het einde van de verzameling die in het bovenliggende beleid is opgegeven. De waarde `Prepend` geeft aan dat het verzamelen van gegevens moet worden toegevoegd vóór de verzameling die in het bovenliggende beleid is opgegeven. De waarde `ReplaceAll` geeft aan dat het verzamelen van gegevens die in het bovenliggende beleid zijn gedefinieerd, moet worden genegeerd, in plaats van de gegevens die in het huidige beleid zijn gedefinieerd. |

### <a name="supportedlanguages"></a>SupportedLanguages

Het **SupportedLanguages** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Geeft inhoud weer die voldoet aan een taal code per RFC 5646-Tags voor het identificeren van talen. |

## <a name="localizedresources"></a>LocalizedResources

Het **LocalizedResources** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt om gelokaliseerde bronnen uniek te identificeren. |

Het **LocalizedResources** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definieert volledige verzamelingen in verschillende cult uren. Een verzameling kan een verschillend aantal items en verschillende teken reeksen voor verschillende cult uren hebben. Voor beelden van verzamelingen zijn de opsommingen die worden weer gegeven in claim typen. Een lijst met landen/regio's wordt bijvoorbeeld weer gegeven aan de gebruiker in een vervolg keuzelijst. |
| LocalizedStrings | 0: n | Hiermee worden alle teken reeksen gedefinieerd, met uitzonde ring van de teken reeksen die in verzamelingen worden weer gegeven, in verschillende cult uren. |

### <a name="localizedcollections"></a>LocalizedCollections

Het **LocalizedCollections** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | Lijst met ondersteunde talen. |

#### <a name="localizedcollection"></a>LocalizedCollection

Het **LocalizedCollection** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ElementType | Ja | Verwijst naar een claim type-element of een gebruikers interface-element in het beleids bestand. |
| ElementId | Ja | Een teken reeks die een verwijzing bevat naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema, dat wordt gebruikt als **element** type is ingesteld op a. |
| TargetCollection | Ja | De doel verzameling. |

Het **LocalizedCollection** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Item | 0: n | Hiermee definieert u een beschik bare optie voor de gebruiker om te selecteren voor een claim in de gebruikers interface, zoals een waarde in een vervolg keuzelijst. |

Het element **item** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Tekst | Ja | De gebruiks vriendelijke weergave teken reeks die moet worden weer gegeven aan de gebruiker in de gebruikers interface voor deze optie. |
| Waarde | Ja | De waarde van de teken reeks claim die is gekoppeld aan het selecteren van deze optie. |
| SelectByDefault | Nee | Hiermee wordt aangegeven of deze optie standaard moet worden geselecteerd in de gebruikers interface. Mogelijke waarden: True of false. |

In het volgende voor beeld ziet u het gebruik van het element **LocalizedCollections** . Het bevat twee **LocalizedCollection** -elementen, een voor Engels en een andere voor Spaans. Beide instellen van de **beperkings** verzameling van de claim `Gender` met een lijst met items voor Engels en Spaans.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

Het **LocalizedStrings** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Een gelokaliseerde teken reeks. |

Het **LocalizedString** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ElementType | Ja | Een verwijzing naar een claim type-element of een gebruikers interface-element in het beleid. Mogelijke waarden: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`of `GetLocalizedStringsTransformationClaimType`. De `ClaimType`-waarde wordt gebruikt om een van de claim kenmerken te lokaliseren, zoals opgegeven in de StringId. De `UxElement`-waarde wordt gebruikt om een van de elementen van de gebruikers interface te lokaliseren zoals opgegeven in de StringId. De `ErrorMessage`-waarde wordt gebruikt voor het lokaliseren van een van de systeem fout berichten die zijn opgegeven in de StringId. De `Predicate`-waarde wordt gebruikt om een van de [predicaat](predicates.md) fout berichten te lokaliseren, zoals opgegeven in de StringId. De `InputValidation`-waarde wordt gebruikt om een van de fout berichten van de [PredicateValidation](predicates.md) -groep te lokaliseren zoals opgegeven in de StringId. De `GetLocalizedStringsTransformationClaimType`-waarde wordt gebruikt om gelokaliseerde teken reeksen naar claims te kopiëren. Zie [GetLocalizedStringsTransformation claims Transformation](string-transformations.md#getlocalizedstringstransformation) (Engelstalig) voor meer informatie.  | 
| ElementId | Ja | Als **element type** is ingesteld op `ClaimType`, `Predicate`of `InputValidation`, bevat dit element een verwijzing naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema. |
| StringId | Ja | Als **element type** is ingesteld op `ClaimType`, bevat dit element een verwijzing naar een kenmerk van een claim type. Mogelijke waarden: `DisplayName`, `AdminHelpText`of `PatternHelpText`. De waarde van de `DisplayName` wordt gebruikt om de weergave naam van de claim in te stellen. De waarde van de `AdminHelpText` wordt gebruikt om de naam van de Help-tekst van de claim gebruiker in te stellen. De `PatternHelpText`-waarde wordt gebruikt om de Help-tekst van het claim patroon in te stellen. Als element **type** is ingesteld op `UxElement`, bevat dit element een verwijzing naar een kenmerk van een element van een gebruikers interface. Als element **type** is ingesteld op `ErrorMessage`, wordt met dit element de id van een fout bericht opgegeven. Zie [lokalisatie teken reeks-id's](localization-string-ids.md) voor een volledige lijst met `UxElement`-id's.|


In het volgende voor beeld wordt een gelokaliseerde aanmeldings pagina weer gegeven. Met de eerste drie **LocalizedString** waarden wordt het claim kenmerk ingesteld. De derde wijzigt de waarde van de knop door gaan. De laatste Hiermee wijzigt u het fout bericht.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

In het volgende voor beeld ziet u een gelokaliseerde **UserHelpText** van **predicaat** met id `IsLengthBetween8And64`. En een gelokaliseerde **UserHelpText** van **PredicateGroup** met id `CharacterClasses` van **PredicateValidation** met id `StrongPassword`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Lokalisatie instellen

In dit artikel wordt beschreven hoe u meerdere land instellingen of talen in het beleid voor gebruikers ritten ondersteunt. Lokalisatie vereist drie stappen: Stel de expliciete lijst van de ondersteunde talen in, geef taalspecifieke teken reeksen en verzamelingen op en bewerk de ContentDefinition voor de pagina.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>De expliciete lijst met ondersteunde talen instellen

Voeg onder het element **BuildingBlocks** het **lokalisatie** -element toe met de lijst met ondersteunde talen. In het volgende voor beeld ziet u hoe u de lokalisatie ondersteuning definieert voor zowel Engels (standaard) als Spaans:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor lokalisatie van voor beelden:

- [Taal aanpassing met aangepast beleid in Azure Active Directory B2C](custom-policy-localization.md)
- [Taal aanpassing met gebruikers stromen in Azure Active Directory B2C](user-flow-language-customization.md)
