---
title: Lokalisatie-Azure Active Directory B2C
description: Geef het lokalisatie-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ec9b4e7ce761d524d047f4d12cab9e5b782e6032
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033450"
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

| Kenmerk | Vereist | Description |
| --------- | -------- | ----------- |
| Enabled | Nee | Mogelijke waarden: `true` of `false`. |

Het **lokalisatie** -element bevat de volgende XML-elementen

| Element | Gevallen | Description |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Lijst met ondersteunde talen. |
| LocalizedResources | 0: n | Lijst met gelokaliseerde resources. |

## <a name="supportedlanguages"></a>SupportedLanguages

Het **SupportedLanguages** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Description |
| --------- | -------- | ----------- |
| Standaardtaal | Ja | De taal die moet worden gebruikt als standaard voor gelokaliseerde resources. |
| MergeBehavior | Nee | Een opsommings waarde van waarden die samen worden samengevoegd met een claim type dat aanwezig is in een bovenliggend beleid met dezelfde id. Gebruik dit kenmerk wanneer u een claim overschrijft die is opgegeven in basis beleid. Mogelijke waarden: `Append`, `Prepend`of `ReplaceAll`. De `Append` waarde geeft aan dat het verzamelen van gegevens moet worden toegevoegd aan het einde van de verzameling die in het bovenliggende beleid is opgegeven. De `Prepend` waarde geeft aan dat het verzamelen van gegevens moet worden toegevoegd vóór de verzameling die in het bovenliggende beleid is opgegeven. De `ReplaceAll` waarde geeft aan dat het verzamelen van gegevens die in het bovenliggende beleid zijn gedefinieerd, moet worden genegeerd, in plaats van de gegevens die in het huidige beleid zijn gedefinieerd. |

### <a name="supportedlanguages"></a>SupportedLanguages

Het **SupportedLanguages** -element bevat de volgende elementen:

| Element | Gevallen | Description |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Geeft inhoud weer die voldoet aan een taal code per RFC 5646-Tags voor het identificeren van talen. |

## <a name="localizedresources"></a>LocalizedResources

Het **LocalizedResources** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Description |
| --------- | -------- | ----------- |
| ID | Ja | Een id die wordt gebruikt om gelokaliseerde bronnen uniek te identificeren. |

Het **LocalizedResources** -element bevat de volgende elementen:

| Element | Gevallen | Description |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definieert volledige verzamelingen in verschillende cult uren. Een verzameling kan een verschillend aantal items en verschillende teken reeksen voor verschillende cult uren hebben. Voor beelden van verzamelingen zijn de opsommingen die worden weer gegeven in claim typen. Een lijst met landen/regio's wordt bijvoorbeeld weer gegeven aan de gebruiker in een vervolg keuzelijst. |
| LocalizedStrings | 0: n | Hiermee worden alle teken reeksen gedefinieerd, met uitzonde ring van de teken reeksen die in verzamelingen worden weer gegeven, in verschillende cult uren. |

### <a name="localizedcollections"></a>LocalizedCollections

Het **LocalizedCollections** -element bevat de volgende elementen:

| Element | Gevallen | Description |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | Lijst met ondersteunde talen. |

#### <a name="localizedcollection"></a>LocalizedCollection

Het **LocalizedCollection** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Description |
| --------- | -------- | ----------- |
| ElementType | Ja | Verwijst naar een claim type-element of een gebruikers interface-element in het beleids bestand. |
| ElementId | Ja | Een teken reeks die een verwijzing bevat naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema, dat wordt gebruikt als **element** type is ingesteld op a. |
| TargetCollection | Ja | De doel verzameling. |

Het **LocalizedCollection** -element bevat de volgende elementen:

| Element | Gevallen | Description |
| ------- | ----------- | ----------- |
| Item | 0: n | Hiermee definieert u een beschik bare optie voor de gebruiker om te selecteren voor een claim in de gebruikers interface, zoals een waarde in een vervolg keuzelijst. |

Het element **item** bevat de volgende kenmerken:

| Kenmerk | Vereist | Description |
| --------- | -------- | ----------- |
| Text | Ja | De gebruiks vriendelijke weergave teken reeks die moet worden weer gegeven aan de gebruiker in de gebruikers interface voor deze optie. |
| Value | Ja | De waarde van de teken reeks claim die is gekoppeld aan het selecteren van deze optie. |
| SelectByDefault | Nee | Hiermee wordt aangegeven of deze optie standaard moet worden geselecteerd in de gebruikers interface. Mogelijke waarden: Waar of ONWAAR. |

In het volgende voor beeld ziet u het gebruik van het element **LocalizedCollections** . Het bevat twee **LocalizedCollection** -elementen, een voor Engels en een andere voor Spaans. Stel de **beperkings** verzameling van de claim `Gender` in met een lijst met items voor Engels en Spaans.

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

| Element | Gevallen | Description |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Een gelokaliseerde teken reeks. |

Het **LocalizedString** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Description |
| --------- | -------- | ----------- |
| ElementType | Ja | Een verwijzing naar een claim type-element of een gebruikers interface-element in het beleid. Mogelijke waarden: `ClaimType` `UxElement` ,,`Predicate`,of. `ErrorMessage` De `ClaimType` waarde wordt gebruikt om een van de claim kenmerken te lokaliseren, zoals opgegeven in de StringId. De `UxElement` waarde wordt gebruikt voor het lokaliseren van een van de elementen van de gebruikers interface, zoals opgegeven in de StringId. De `ErrorMessage` waarde wordt gebruikt voor het lokaliseren van een van de systeem fout berichten die zijn opgegeven in de StringId. De `Predicate` waarde wordt gebruikt om een van de [predicaat](predicates.md) fout berichten te lokaliseren, zoals opgegeven in de StringId. De `InputValidation` waarde wordt gebruikt om een van de [PredicateValidation](predicates.md) -groeps fout berichten te lokaliseren zoals opgegeven in de StringId. |
| ElementId | Ja | Als **element type** is ingesteld `ClaimType`op `Predicate`,, `InputValidation`of, bevat dit element een verwijzing naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema. |
| StringId | Ja | Als **element type** is ingesteld `ClaimType`op, bevat dit element een verwijzing naar een kenmerk van een claim type. Mogelijke waarden: `DisplayName`, `AdminHelpText`of `PatternHelpText`. De `DisplayName` waarde wordt gebruikt om de weergave naam van de claim in te stellen. De `AdminHelpText` waarde wordt gebruikt om de Help-tekst naam van de claim gebruiker in te stellen. De `PatternHelpText` waarde wordt gebruikt om de Help-tekst van het claim patroon in te stellen. Als element **type** is ingesteld `UxElement`op, bevat dit element een verwijzing naar een kenmerk van een element van een gebruikers interface. Als element **type** is ingesteld `ErrorMessage`op, wordt met dit element de id van een fout bericht opgegeven. Zie [lokalisatie teken reeks-id's](localization-string-ids.md) voor een volledige lijst `UxElement` met id's.|


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

In het volgende voor beeld wordt een gelokaliseerd **UserHelpText** van **predicaat** met `IsLengthBetween8And64`id weer gegeven. En een gelokaliseerde **UserHelpText** van **PredicateGroup** met `CharacterClasses` id **PredicateValidation** met id `StrongPassword`.

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

Voeg onder het element BuildingBlocks het **lokalisatie** -element toe met de lijst met ondersteunde talen. In het volgende voor beeld ziet u hoe u de lokalisatie ondersteuning definieert voor zowel Engels (standaard) als Spaans:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Taalspecifieke teken reeksen en verzamelingen opgeven

Voeg **LocalizedResources** -elementen toe binnen het **lokalisatie** -element nadat het **SupportedLanguages** -element is gesloten. U voegt **LocalizedResources** -elementen toe voor elke pagina (inhouds definitie) en elke taal die u wilt ondersteunen. U kunt de volgende **LocalizedResources** -elementen toevoegen om de Unified registratie-of aanmeldings pagina, registratie en MFA-pagina's (multi-factor Authentication) voor Engels, Spaans en Frank rijk aan te passen.

- Uniforme registratie-of aanmeldings pagina, Engels`<LocalizedResources Id="api.signuporsignin.en">`
- Unified Sign-up-of aanmeldings pagina, Spaans`<LocalizedResources Id="api.signuporsignin.es">`
- Uniforme registratie-of aanmeldings pagina, Frank rijk`<LocalizedResources Id="api.signuporsignin.fr">`
- Registratie, Engels`<LocalizedResources Id="api.localaccountsignup.en">`
- Registratie, Spaans`<LocalizedResources Id="api.localaccountsignup.es">`
- Registratie, Frank rijk`<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, Engels`<LocalizedResources Id="api.phonefactor.en">`
- MFA, Spaans`<LocalizedResources Id="api.phonefactor.es">`
- MFA, Frank rijk`<LocalizedResources Id="api.phonefactor.fr">`

Elk **LocalizedResources** -element bevat alle vereiste **LocalizedStrings** -elementen met meerdere **LocalizedString** -elementen en **LocalizedCollections** -elementen met meerdere **LocalizedCollection** opties.  In het volgende voor beeld wordt de pagina voor aanmeldings pagina's Engels gelokaliseerd toegevoegd:

Opmerking: In dit voor beeld wordt een `Gender` verwijzing `City` naar en claim typen gemaakt. Als u dit voor beeld wilt gebruiken, zorg er dan voor dat u deze claims definieert. Zie [ClaimsSchema](claimsschema.md)voor meer informatie.

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Lokalisatie van de aanmeldings pagina voor Spaans.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>De ContentDefinition voor de pagina bewerken

Voor elke pagina die u wilt lokaliseren, geeft u de taal codes op waarnaar u wilt zoeken in de **ContentDefinition**.

In het volgende voor beeld worden de aangepaste teken reeksen Engels (en) en Spaans (ES) toegevoegd aan de registratie pagina. De **LocalizedResourcesReferenceId** voor elke **LocalizedResourcesReference** is hetzelfde als de land instelling, maar u kunt elke wille keurige teken reeks als de id gebruiken. Voor elke combi natie van taal en pagina wijst u de overeenkomende **LocalizedResources** aan die u eerder hebt gemaakt.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

In het volgende voor beeld wordt de laatste XML weer gegeven:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```
