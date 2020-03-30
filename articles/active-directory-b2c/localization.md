---
title: Lokalisatie - Azure Active Directory B2C
description: Geef het element Lokalisatie van een aangepast beleid op in Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126757"
---
# <a name="localization"></a>Lokalisatie

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met het element **Lokalisatie** u meerdere landplaatsen of talen ondersteunen in het beleid voor de gebruikersreizen. Met de ondersteuning voor lokalisatie in beleid u:

- Stel de expliciete lijst van de ondersteunde talen in een beleid in en kies een standaardtaal.
- Geef taalspecifieke tekenreeksen en verzamelingen.

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

Het element **Lokalisatie** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Ingeschakeld | Nee | Mogelijke `true` waarden: `false`of . |

Het **element Lokalisatie** bevat volgende XML-elementen

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Ondersteunde talen | 1:n | Lijst met ondersteunde talen. |
| Gelokaliseerde bronnen | 0:n | Lijst met gelokaliseerde bronnen. |

## <a name="supportedlanguages"></a>Ondersteunde talen

Het element **Ondersteunde talen** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| DefaultLanguage | Ja | De taal die moet worden gebruikt als standaard voor gelokaliseerde bronnen. |
| Samenvoeggedrag | Nee | Een opsommingswaarden van waarden die worden samengevoegd met een ClaimType die aanwezig is in een bovenliggend beleid met dezelfde id. Gebruik dit kenmerk wanneer u een claim overschrijft die is opgegeven in het basisbeleid. Mogelijke `Append`waarden: `Prepend`, `ReplaceAll`, of . De `Append` waarde geeft aan dat het verzamelen van de aanwezige gegevens moet worden toegevoegd aan het einde van de verzameling die is opgegeven in het bovenliggende beleid. De `Prepend` waarde geeft aan dat de aanwezige gegevens moeten worden toegevoegd voordat de verzameling is opgegeven in het bovenliggende beleid. De `ReplaceAll` waarde geeft aan dat het verzamelen van gegevens die zijn gedefinieerd in het bovenliggende beleid moet worden genegeerd, waarbij in plaats daarvan de gegevens worden gedefinieerd die in het huidige beleid zijn gedefinieerd. |

### <a name="supportedlanguages"></a>Ondersteunde talen

Het element **Ondersteunde talen** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Ondersteunde taal | 1:n | Hiermee geeft u inhoud weer die voldoet aan een taaltag per RFC 5646 - Tags voor het identificeren van talen. |

## <a name="localizedresources"></a>Gelokaliseerde bronnen

Het element **Gelokaliseerde resources** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt om op unieke wijze gelokaliseerde bronnen te identificeren. |

Het element **Gelokaliseerde resources** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Gelokaliseerde collecties | 0:n | Definieert hele collecties in verschillende culturen. Een collectie kan verschillende aantal items en verschillende snaren voor verschillende culturen. Voorbeelden van verzamelingen zijn de opsommingen die worden weergegeven in claimtypen. Een landen-/regiolijst wordt bijvoorbeeld aan de gebruiker weergegeven in een vervolgkeuzelijst. |
| Gelokaliseerde tekenreeksen | 0:n | Definieert alle tekenreeksen, behalve die tekenreeksen die in verzamelingen worden weergegeven, in verschillende culturen. |

### <a name="localizedcollections"></a>Gelokaliseerde collecties

Het element **Gelokaliseerde collecties** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Gelokaliseerde collectie | 1:n | Lijst met ondersteunde talen. |

#### <a name="localizedcollection"></a>Gelokaliseerde collectie

Het element **Gelokaliseerde collectie** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ElementType | Ja | Verwijst naar een ClaimType-element of een element van de gebruikersinterface in het beleidsbestand. |
| ElementId | Ja | Een tekenreeks die een verwijzing bevat naar een claimtype dat al is gedefinieerd in de sectie ClaimsSchema die wordt gebruikt als **ElementType** is ingesteld op een ClaimType. |
| Doelverzameling | Ja | De doelcollectie. |

Het element **Gelokaliseerde collectie** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Item | 0:n | Hiermee definieert u een beschikbare optie die de gebruiker moet selecteren voor een claim in de gebruikersinterface, zoals een waarde in een vervolgkeuzelijst. |

Het element **Item** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Tekst | Ja | De gebruiksvriendelijke weergavetekenreeks die voor deze optie aan de gebruiker in de gebruikersinterface moet worden getoond. |
| Waarde | Ja | De tekenreeksclaimwaarde die is gekoppeld aan het selecteren van deze optie. |
| Standaard selecteren | Nee | Geeft aan of deze optie standaard moet worden geselecteerd in de gebruikersinterface. Mogelijke waarden: Waar of Onwaar. |

In het volgende voorbeeld ziet u het gebruik van het element **Gelokaliseerde verzamelingen.** Het bevat twee **gelokaliseerde Collection** elementen, een voor Engels en een andere voor het Spaans. Beide stellen de **beperkingsverzameling** van de claim `Gender` in met een lijst met items voor Engels en Spaans.

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

### <a name="localizedstrings"></a>Gelokaliseerde tekenreeksen

Het element **Gelokaliseerde tekenreeksen** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Gelokaliseerde tekenreeks | 1:n | Een gelokaliseerde tekenreeks. |

Het element **Gelokaliseerde tekenreeks** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ElementType | Ja | Een verwijzing naar een element van claimtype of een element van de gebruikersinterface in het beleid. `ClaimType`Mogelijke waarden: `UxElement` `ErrorMessage`, `Predicate`, `GetLocalizedStringsTransformationClaimType`, of . De `ClaimType` waarde wordt gebruikt om een van de claimkenmerken te lokaliseren, zoals opgegeven in de StringId. De `UxElement` waarde wordt gebruikt om een van de gebruikersinterface-elementen te lokaliseren zoals opgegeven in de StringId. De `ErrorMessage` waarde wordt gebruikt om een van de systeemfoutmeldingen te lokaliseren zoals opgegeven in de StringId. De `Predicate` waarde wordt gebruikt om een van de [foutberichten van Het Predicaat](predicates.md) te lokaliseren, zoals opgegeven in de StringId. De `InputValidation` waarde wordt gebruikt om een van de foutberichten van de [groep PredicateValidation](predicates.md) te lokaliseren zoals opgegeven in de StringId. De `GetLocalizedStringsTransformationClaimType` waarde wordt gebruikt om gelokaliseerde tekenreeksen te kopiëren naar claims. Zie [GetLocalizedStringsTransformation claims transformation voor](string-transformations.md#getlocalizedstringstransformation) meer informatie  | 
| ElementId | Ja | Als **ElementType** is `ClaimType` `Predicate`ingesteld `InputValidation`op , of , bevat dit element een verwijzing naar een claimtype dat al is gedefinieerd in de sectie ClaimsSchema. |
| StringId (StringId) | Ja | Als **ElementType** is `ClaimType`ingesteld op , bevat dit element een verwijzing naar een kenmerk van een claimtype. Mogelijke `DisplayName`waarden: `AdminHelpText`, `PatternHelpText`, of . De `DisplayName` waarde wordt gebruikt om de naam van de claimweergave in te stellen. De `AdminHelpText` waarde wordt gebruikt om de help-tekstnaam van de claimgebruiker in te stellen. De `PatternHelpText` waarde wordt gebruikt om de helptekst van het claimpatroon in te stellen. Als **ElementType** is `UxElement`ingesteld op , bevat dit element een verwijzing naar een kenmerk van een element van de gebruikersinterface. Als **ElementType** is `ErrorMessage`ingesteld op , geeft dit element de id van een foutbericht op. Zie [Id's van de lokalisatietekenreeks](localization-string-ids.md) voor een volledige lijst met de `UxElement` id's.|


In het volgende voorbeeld wordt een gelokaliseerde aanmeldingspagina weergegeven. De eerste drie **gelokaliseerde tekenreekswaarden** stellen het claimkenmerk in. De derde wijzigt de waarde van de knop Doorgaan. De laatste verandert het foutbericht.

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

In het volgende voorbeeld wordt een **Predicate** gelokaliseerde `IsLengthBetween8And64` **userHelptext** van Predicaat met id weergegeven. En een gelokaliseerde **UserHelpText** van `CharacterClasses` **PredicateGroup** met `StrongPassword`id van **PredicateValidation** met id .

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

In dit artikel ziet u hoe u meerdere landzetten of talen ondersteunen in het beleid voor gebruikersreizen. Lokalisatie vereist drie stappen: de expliciete lijst met ondersteunde talen instellen, taalspecifieke tekenreeksen en verzamelingen aanbieden en de ContentDefinition voor de pagina bewerken.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>De expliciete lijst met ondersteunde talen instellen

Voeg onder het element **Bouwstenen** het element **Lokalisatie** toe met de lijst met ondersteunde talen. In het volgende voorbeeld ziet u hoe u de lokalisatieondersteuning voor zowel Engels (standaard) als Spaans definieert:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor lokalisatievoorbeelden:

- [Taalaanpassing met aangepast beleid in Azure Active Directory B2C](custom-policy-localization.md)
- [Taalaanpassing met gebruikersstromen in Azure Active Directory B2C](user-flow-language-customization.md)
