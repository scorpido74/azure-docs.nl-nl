---
title: Lokalisatie - Azure Active Directory B2C
description: Geef het element Lokalisatie van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681422"
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
| ElementType | Ja | Mogelijke waarden: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predicate](#predicate), [InputValidation](#inputvalidation)of [UxElement](#uxelement).   | 
| ElementId | Ja | Als **ElementType** is `ClaimType` `Predicate`ingesteld `InputValidation`op , of , bevat dit element een verwijzing naar een claimtype dat al is gedefinieerd in de sectie ClaimsSchema. |
| StringId (StringId) | Ja | Als **ElementType** is `ClaimType`ingesteld op , bevat dit element een verwijzing naar een kenmerk van een claimtype. Mogelijke `DisplayName`waarden: `AdminHelpText`, `PatternHelpText`, of . De `DisplayName` waarde wordt gebruikt om de naam van de claimweergave in te stellen. De `AdminHelpText` waarde wordt gebruikt om de help-tekstnaam van de claimgebruiker in te stellen. De `PatternHelpText` waarde wordt gebruikt om de helptekst van het claimpatroon in te stellen. Als **ElementType** is `UxElement`ingesteld op , bevat dit element een verwijzing naar een kenmerk van een element van de gebruikersinterface. Als **ElementType** is `ErrorMessage`ingesteld op , geeft dit element de id van een foutbericht op. Zie [Id's van de lokalisatietekenreeks](localization-string-ids.md) voor een volledige lijst met de `UxElement` id's.|

## <a name="elementtype"></a>ElementType

De ElementType-verwijzing naar een claimtype, een claimtransformatie of een element van de gebruikersinterface in het te lokaliseren beleid.

| Element om te lokaliseren | ElementType | ElementId |StringId (StringId) |
| --------- | -------- | ----------- |----------- |
| Naam van identiteitsprovider |`ClaimsProvider`| | De id van het element ClaimsExchange|
| Kenmerken van claimtype|`ClaimType`|Naam van het claimtype| Het kenmerk van de claim die moet worden gelokaliseerd. Mogelijke `AdminHelpText`waarden: `DisplayName` `PatternHelpText`, `UserHelpText`, en .|
|Foutbericht|`ErrorMessage`||De id van het foutbericht |
|Hiermee worden gelokaliseerde tekenreeksen in claims kopieën gemaakt|`GetLocalizedStringsTra nsformationClaimType`||De naam van de uitvoerclaim|
|Het gebruikersbericht van Het predicaat|`Predicate`|De naam van het predicaat| Het kenmerk van het predicaat dat moet worden gelokaliseerd. Mogelijke waarden: `HelpText`.|
|Het bericht van de gebruiker van de groep van de groep van het predicaat|`InputValidation`|De ID van het element PredicaatValidatie.|De ID van het element Predicaatgroep. De predicaatgroep moet een onderliggend kind zijn van het predicaatvalidatieelement zoals gedefinieerd in het ElementId.|
|Elementen van de gebruikersinterface |`UxElement` | | De id van het element gebruikersinterface dat moet worden gelokaliseerd.|

## <a name="examples"></a>Voorbeelden

### <a name="claimsprovider"></a>ClaimsProvider

De waarde van ClaimProvider wordt gebruikt om een van de claimproviders te lokaliseren, de naam van de weergave. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

In het volgende voorbeeld ziet u hoe u de weergavenaam van claimproviders lokaliseren.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

De waarde ClaimType wordt gebruikt om een van de claimkenmerken te lokaliseren. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

In het volgende voorbeeld ziet u hoe u de kenmerken DisplayName, UserHelpText en PatternHelpText van het type e-mailclaim lokaliseren.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>Errormessage

De foutmelding wordt gebruikt om een van de systeemfoutmeldingen te lokaliseren. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

In het volgende voorbeeld ziet u hoe u het foutbericht UserMessageIfClaimsPrincipalAlreadyExists lokaliseren.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

De waarde GetLocalizedStringsTransformationClaimType wordt gebruikt om gelokaliseerde tekenreeksen naar claims te kopiëren. Zie [GetLocalizedStringsTransformation claims transformation voor](string-transformations.md#getlocalizedstringstransformation) meer informatie


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

In het volgende voorbeeld ziet u hoe u uitvoerclaims van de transformatie van de claimtransformatie getlocalizedstringstransformation lokaliseren.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predikaat

De waarde Van Predicaat wordt gebruikt om een van de [foutberichten van het Predicaat](predicates.md) te lokaliseren. 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
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
</Predicates>
```

In het volgende voorbeeld ziet u hoe u de helptekst van predicaten lokaliseren.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>Invoervalidatie

De waarde Invoervalidatie wordt gebruikt om een van de foutberichten van de groep [PredicaatValidatie](predicates.md) te lokaliseren. 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
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
</PredicateValidations>
```

In het volgende voorbeeld ziet u hoe u de helptekst van een predicaatvalidatiegroep lokaliseren.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement (UxElement)

De UxElement-waarde wordt gebruikt om een van de gebruikersinterface-elementen te lokaliseren. In het volgende voorbeeld ziet u hoe u de knoppen Doorgaan lokaliseren en annuleren.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor lokalisatievoorbeelden:

- [Taalaanpassing met aangepast beleid in Azure Active Directory B2C](custom-policy-localization.md)
- [Taalaanpassing met gebruikersstromen in Azure Active Directory B2C](user-flow-language-customization.md)
