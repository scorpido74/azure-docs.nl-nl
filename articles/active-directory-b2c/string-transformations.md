---
title: Voorbeelden van voor transformatie van tekenreeksclaims voor aangepast beleid
titleSuffix: Azure AD B2C
description: Voorbeelden van tekenreeksclaimstransformatie voor het IEF-schema (Identity Experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f08107874598a68fb5ce2a1a8a98b6a81d7b94d4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756795"
---
# <a name="string-claims-transformations"></a>Tekenreeksclaimstransformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voorbeelden voor het gebruik van de tekenreeksclaimstransformaties van het Identity Experience Framework-schema in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie .

## <a name="assertstringclaimsareequal"></a>AssertstringclaimsAreequal

Vergelijk twee claims en gooi een uitzondering als ze niet gelijk zijn volgens de opgegeven vergelijkingsinvoerClaim1, inputClaim2 en stringComparison.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim1 | tekenreeks | Eerste claim type, dat moet worden vergeleken. |
| Invoerclaim | inputClaim2 | tekenreeks | Tweede claim type, die moet worden vergeleken. |
| Inputparameter | tekenreeksVergelijking | tekenreeks | tekenreeksvergelijking, een van de waarden: Ordinal, OrdinalIgnoreCase. |

De **assertstringclaimsareequal-claimtransformatie** wordt altijd uitgevoerd vanuit een [validatietechnisch profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md)of een [DisplayConrtol](display-controls.md). De `UserMessageIfClaimsTransformationStringsAreNotEqual` metagegevens van een zelfgeclaimd technisch profiel bepalen de foutmelding die aan de gebruiker wordt gepresenteerd. De foutberichten kunnen worden [gelokaliseerd](localization-string-ids.md#claims-transformations-error-messages).


![AssertstringclaimsAreequal uitvoering](./media/string-transformations/assert-execution.png)

U deze claimtransformatie gebruiken om ervoor te zorgen dat twee ClaimTypes dezelfde waarde hebben. Zo niet, dan wordt er een foutmelding gegenereerd. In het volgende voorbeeld wordt gecontroleerd of het **strongAuthenticationEmailAddress** ClaimType gelijk is aan **email** ClaimType. Anders wordt een foutbericht gegooid.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Het **login-NonInteractive** validatie technische profiel roept de **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** claims transformatie.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Het zelfverklaarde technische profiel noemt het validatie **login-NonInteractive** technisch profiel.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Invoerparameters:
  - **stringComparison**: ordinalIgnoreCase
- Resultaat: Fout gegooid

## <a name="changecase"></a>ChangeCase

Hiermee wijzigt u het geval van de verstrekte claim in de onder- of hoofdletter, afhankelijk van de operator.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim1 | tekenreeks | Het ClaimType dat moet worden gewijzigd. |
| Inputparameter | toCase | tekenreeks | Een van de `LOWER` volgende `UPPER`waarden: of . |
| Uitvoerclaim | outputClaim | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld. |

Gebruik deze claimtransformatie om een tekenreeks ClaimType te wijzigen in de onderste of bovenste letters.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **e-mail**:SomeOne@contoso.com
- Invoerparameters:
    - **toCase**: LOWER
- Output claims:
  - **e-mail**:someone@contoso.com

## <a name="createstringclaim"></a>StringClaim maken

Hiermee maakt u een tekenreeksclaim op basis van de opgegeven invoerparameter in de transformatie.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
|----- | ----------------------- | --------- | ----- |
| Inputparameter | waarde | tekenreeks | De touw die moet worden ingesteld. Deze invoerparameter ondersteunt [tekenreeksclaimstransformatieexpressies](string-transformations.md#string-claim-transformations-expressions). |
| Uitvoerclaim | createdClaim | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld, waarbij de waarde is opgegeven in de invoerparameter. |

Gebruik deze claimtransformatie om een tekenreeks ClaimType-waarde in te stellen.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerparameter:
    - **waarde**: Contoso servicevoorwaarden...
- Output claims:
    - **createdClaim**: Het TOS ClaimType bevat de "Contoso terms of service..." Waarde.

## <a name="compareclaims"></a>Claims vergelijken

Bepaal of de ene tekenreeksclaim gelijk is aan de andere. Het resultaat is een nieuwe booleaanse `true` `false`ClaimType met een waarde van of .

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim1 | tekenreeks | Eerste claimtype, dat moet worden vergeleken. |
| Invoerclaim | inputClaim2 | tekenreeks | Tweede claimtype, dat moet worden vergeleken. |
| Inputparameter | operator | tekenreeks | Mogelijke `EQUAL` waarden: `NOT EQUAL`of . |
| Inputparameter | negerenHoofdletter | booleaans | Hiermee geeft u op of deze vergelijking het geval moet negeren van de tekenreeksen die worden vergeleken. |
| Uitvoerclaim | outputClaim | booleaans | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld. |

Gebruik deze claimtransformatie om te controleren of een claim gelijk is aan een andere claim. De volgende claimstransformatie controleert bijvoorbeeld of de waarde van de **e-mailclaim** gelijk is aan de claim **Verified.Email.**

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Invoerparameters:
    - **operator**: NIET GELIJK
    - **ignoreCase**: true
- Output claims:
    - **outputClaim**: waar

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Hiermee bepaalt u of een claimwaarde gelijk is aan de waarde van de invoerparameter.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim1 | tekenreeks | Het type van de claim, dat moet worden vergeleken. |
| Inputparameter | operator | tekenreeks | Mogelijke `EQUAL` waarden: `NOT EQUAL`of . |
| Inputparameter | Compareto | tekenreeks | tekenreeksvergelijking, een van de waarden: Ordinal, OrdinalIgnoreCase. |
| Inputparameter | negerenHoofdletter | booleaans | Hiermee geeft u op of deze vergelijking het geval moet negeren van de tekenreeksen die worden vergeleken. |
| Uitvoerclaim | outputClaim | booleaans | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld. |

U deze claimtransformatie gebruiken om te controleren of een claim gelijk is aan een waarde die u hebt opgegeven. De volgende claimstransformatie controleert bijvoorbeeld of de waarde van de claim `v1` **VanUseConsentVersion** gelijk is aan .

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld
- Invoerclaims:
    - **inputClaim1**: v1
- Invoerparameters:
    - **compareTo**: V1
    - **exploitant**: GELIJK
    - **ignoreCase**: true
- Output claims:
    - **outputClaim**: waar

## <a name="createrandomstring"></a>Willekeurige tekenreeks maken

Hiermee maakt u een willekeurige tekenreeks met behulp van de random number generator. Als de random number `integer`generator van het type is, mogen eventueel een zaadparameter en een maximumaantal worden verstrekt. Met een optionele parameter voor tekenreeksformaat kan de uitvoer worden opgemaakt en een optionele parameter base64 geeft aan of de uitvoer base64-gecodeerde randomGeneratorType [guid, integer] outputClaim (Tekenreeks) is.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Inputparameter | randomGeneratorType | tekenreeks | Hiermee geeft u de `GUID` willekeurige waarde op `INTEGER` die moet worden gegenereerd (globale unieke ID) of (een getal). |
| Inputparameter | tekenreeksOpmaak | tekenreeks | [Optioneel] De willekeurige waarde opmaken. |
| Inputparameter | base64 | booleaans | [Optioneel] Converteer de willekeurige waarde naar base64. Als tekenreeksnotatie wordt toegepast, wordt de waarde na tekenreeksnotatie gecodeerd naar base64. |
| Inputparameter | maximumAantal | int | [Optioneel] Alleen `INTEGER` voor randomGeneratorType. Geef het maximumaantal op. |
| Inputparameter | Zaad  | int | [Optioneel] Alleen `INTEGER` voor randomGeneratorType. Geef het zaad op voor de willekeurige waarde. Opmerking: hetzelfde zaad levert dezelfde volgorde van willekeurige getallen op. |
| Uitvoerclaim | outputClaim | tekenreeks | De ClaimTypes die worden geproduceerd nadat deze claimtransformatie is ingeroepen. De willekeurige waarde. |

Het volgende voorbeeld genereert een globale unieke ID. Deze claimtransformatie wordt gebruikt om de willekeurige UPN (gebruikersnaamnaam) te maken.

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Voorbeeld

- Invoerparameters:
    - **randomGeneratorType**: GUID
- Output claims:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Volgende voorbeeld genereert een gehele willekeurige waarde tussen 0 en 1000. De waarde wordt opgemaakt met OTP_{willekeurige waarde}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerparameters:
    - **randomGeneratorType**: GEHELE GETAL
    - **maximumAantal**: 1000
    - **stringFormat**: OTP_{0}
    - **basis64**: vals
- Output claims:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Een claim opmaken volgens de tekenreeks voor de opgegeven indeling. Deze transformatie maakt `String.Format` gebruik van de C#-methode.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim |tekenreeks |Het ClaimType dat fungeert {0} als parameter voor tekenreeksindeling. |
| Inputparameter | tekenreeksOpmaak | tekenreeks | De tekenreeksnotatie, {0} inclusief de parameter. Deze invoerparameter ondersteunt [tekenreeksclaimstransformatieexpressies](string-transformations.md#string-claim-transformations-expressions).  |
| Uitvoerclaim | outputClaim | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld. |

Gebruik deze claimtransformatie om een {0}tekenreeks op te maken met één parameter . In het volgende voorbeeld wordt een **userPrincipalName aanmaken**. Alle technische profielen van sociale `Facebook-OAUTH` identiteitsprovideren, zoals aanroepen de **CreateUserPrincipalName** om een **userPrincipalName**te genereren.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Invoerparameters:
    - **stringFormat**:{0}cpim_ @{RelyingPartyTenantId}
- Output claims:
  - **outputClaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Maak twee claims op basis van de opgegeven formaattekenreeks. Deze transformatie maakt `String.Format` gebruik van de C#-methode.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim |tekenreeks | Het ClaimType dat fungeert {0} als parameter voor tekenreeksindeling. |
| Invoerclaim | inputClaim | tekenreeks | Het ClaimType dat fungeert {1} als parameter voor tekenreeksindeling. |
| Inputparameter | tekenreeksOpmaak | tekenreeks | De tekenreeksindeling, {0} inclusief {1} de parameters en parameters. Deze invoerparameter ondersteunt [tekenreeksclaimstransformatieexpressies](string-transformations.md#string-claim-transformations-expressions).   |
| Uitvoerclaim | outputClaim | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeschakeld. |

Gebruik deze claimtransformatie om een tekenreeks {0} {1}op te maken met twee parameters en . In het volgende voorbeeld wordt een **displayName** gemaakt met de opgegeven indeling:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Invoerparameters:
    - **stringFormat** {0} :{1}
- Output claims:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformatie

Kopieert gelokaliseerde tekenreeksen in claims.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Uitvoerclaim | De naam van de gelokaliseerde tekenreeks | tekenreeks | Lijst van claimtypen die zijn geproduceerd nadat deze claimtransformatie is ingeroepen. |

Ga als het gaat om de transformatie van de claimtransformatie van GetLocalizedStringsTransformation:

1. Definieer een [lokalisatietekenreeks](localization.md) en koppel deze aan een [zelf-beweerd-technisch profiel](self-asserted-technical-profile.md).
1. Het `ElementType` `LocalizedString` element moet worden `GetLocalizedStringsTransformationClaimType`ingesteld op .
1. Het `StringId` is een unieke id die u definieert en deze later gebruikt in uw claimtransformatie.
1. Geef in de claimtransformatie de lijst op met claims die moeten worden ingesteld met de gelokaliseerde tekenreeks. Het `ClaimTypeReferenceId` is een verwijzing naar een ClaimType dat al is gedefinieerd in de sectie ClaimsSchema in het beleid. Het `TransformationClaimType` is de naam van de gelokaliseerde `StringId` tekenreeks `LocalizedString` zoals gedefinieerd in het element.
1. Maak in een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md)of een [weergavecontrole-invoer-](display-controls.md) of uitvoerclaimtransformatie een verwijzing naar uw claimtransformatie.

![GetLocalizedStringsTransformatie](./media/string-transformations/get-localized-strings-transformation.png)

In het volgende voorbeeld wordt het e-mailonderwerp, de hoofdtekst, uw codebericht en de handtekening van de e-mail opzoekt op lokale tekenreeksen. Deze claims worden later gebruikt door aangepaste sjabloon voor e-mailverificatie.

Definieer gelokaliseerde tekenreeksen voor Engels (standaard) en Spaans.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

De claimtransformatie stelt de waarde van het *claimtype onderwerp in* op de waarde van de `StringId` *email_subject*.

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Output claims:
  - **onderwerp**: Contoso-accountverificatiecode
  - **bericht:** Bedankt voor het verifiëren van je account!
  - **codeIntro**: Uw code is
  - **handtekening**: Oprecht


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromGelokaliseerde verzameling

Een item opzoeken uit een **claimbeperkingsverzameling.**

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | mapFromClaim | tekenreeks | De claim die de tekst bevat die moet worden opgezocht in de **restrictiesDie claimt ValueClaim** met de **beperkingsverzameling.**  |
| Uitvoerclaim | beperkingValueClaim | tekenreeks | De claim die de **collectie Beperking** bevat. Nadat de claimtransformatie is ingeroepen, bevat de waarde van deze claim de waarde van het geselecteerde item. |

In het volgende voorbeeld wordt de beschrijving van het foutbericht opzoekt op basis van de foutsleutel. De **claim responseMsg** bevat een verzameling foutmeldingen die aan de eindgebruiker moeten worden weergegeven of die naar de relying party moeten worden verzonden.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
De claimtransformatie zoekt de tekst van het item op en retourneert de waarde ervan. Als de beperking wordt `<LocalizedCollection>`gelokaliseerd met behulp van , geeft de claimtransformatie de gelokaliseerde waarde als resultaat.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **mapFromClaim**: B2C_V1_90001
- Output claims:
    - **beperkingValueClaim**: U zich niet aanmelden omdat u minderjarig bent.

## <a name="lookupvalue"></a>Opzoekwaarde

Zoek een claimwaarde op uit een lijst met waarden op basis van de waarde van een andere claim.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | invoerParameterId | tekenreeks | De claim die de opzoekwaarde bevat |
| Inputparameter | |tekenreeks | Verzameling van inputParameters. |
| Inputparameter | errorOnFailedLookup | booleaans | Bepalen of een fout wordt geretourneerd wanneer er geen bijpassende lookup optreedt. |
| Uitvoerclaim | invoerParameterId | tekenreeks | De ClaimTypes die worden geproduceerd nadat deze claimtransformatie is ingeroepen. De waarde van `Id`de matching . |

In het volgende voorbeeld wordt de domeinnaam in een van de verzamelingen van invoerParameters opzoekt. De claimtransformatie zoekt de domeinnaam in de id op en retourneert de waarde ervan (een toepassings-id).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputParameterId**: test.com
- Invoerparameters:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Output claims:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Wanneer `errorOnFailedLookup` de invoerparameter `true`is ingesteld op , wordt de transformatie **van de Claimse LookupValue** altijd uitgevoerd vanuit een [technisch validatieprofiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelfgeclaimd technisch profiel](self-asserted-technical-profile.md)of een [DisplayConrtol](display-controls.md). De `LookupNotFound` metagegevens van een zelfgeclaimd technisch profiel bepalen de foutmelding die aan de gebruiker wordt gepresenteerd.

![AssertstringclaimsAreequal uitvoering](./media/string-transformations/assert-execution.png)

In het volgende voorbeeld wordt de domeinnaam in een van de verzamelingen van invoerParameters opzoekt. De claimtransformatie zoekt de domeinnaam in de id op en retourneert de waarde ervan (een toepassings-id) of geeft een foutbericht.

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputParameterId**: live.com
- Invoerparameters:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Fout:
    - Er is geen overeenkomst gevonden voor de waarde van de invoerclaim in de lijst met invoerparameter-id's en foutOnFailedLookup is waar.


## <a name="nullclaim"></a>NullClaim NullClaim

Maak de waarde van een bepaalde claim schoon.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Uitvoerclaim | claim_to_null | tekenreeks | De waarde van de claim is ingesteld op NULL. |

Gebruik deze claimtransformatie om onnodige gegevens uit de claimpropertybag te verwijderen, zodat de sessiecookie kleiner is. In het volgende voorbeeld `TermsOfService` wordt de waarde van het claimtype verwijderd.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoerclaims:
    - **outputClaim:** Welkom bij Contoso App. Als u doorgaat met browsen en deze website gebruiken, gaat u ermee akkoord om te voldoen aan en gebonden te zijn aan de volgende algemene voorwaarden...
- Output claims:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomein

Hier krijgt u het domeingedeelte van een e-mailadres.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | Emailaddress | tekenreeks | Het ClaimType dat het e-mailadres bevat. |
| Uitvoerclaim | domein | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze claimtransformatie is ingeroepen - het domein. |

Gebruik deze claimtransformatie om de domeinnaam te ontwenen naar het @-symbool van de gebruiker. De volgende claimtransformatie laat zien hoe u de domeinnaam ontleeden uit een **e-mailclaim.**

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **emailAddress:**joe@outlook.com
- Output claims:
    - **domein**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Hiermee wordt gecontroleerd `claimToMatch` `matchTo` of een tekenreeksclaim en invoerparameter gelijk zijn `outputClaimIfMatched` en worden de uitvoerclaims ingesteld op de `true` waarde `false` die aanwezig is in de invoerparameter, samen met de claim voor de uitvoer van resultaten vergelijken, die moet worden ingesteld als of op basis van het resultaat van de vergelijking.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | tekenreeks | Het claimtype, dat moet worden vergeleken. |
| Inputparameter | matchTo | tekenreeks | De reguliere uitdrukking aan te passen. |
| Inputparameter | outputClaimIfMatched | tekenreeks | De waarde die moet worden ingesteld als tekenreeksen gelijk zijn. |
| Inputparameter | extractGroepen | booleaans | [Optioneel] Hiermee geeft u op of de Regex-overeenkomst groepenwaarden moet extraheren. Mogelijke waarden: `true` `false` , of (standaard). | 
| Uitvoerclaim | outputClaim | tekenreeks | Als reguliere expressie overeenkomt, bevat deze `outputClaimIfMatched` uitvoerclaim de waarde van de invoerparameter. Of null, als er geen overeenkomst. |
| Uitvoerclaim | regexCompareResultClaim | booleaans | Het type claimtype van de reguliere expressie `true` matcht met resultaatuitvoer, dat moet worden ingesteld als of `false` op basis van het resultaat van matching. |
| Uitvoerclaim| De naam van de claim| tekenreeks | Als de parameter extractGroups invoer is ingesteld op true, wordt de lijst weergegeven met claimtypen die zijn geproduceerd nadat deze claimtransformatie is aangeroepen. De naam van het claimType moet overeenkomen met de regex-groepsnaam. | 

### <a name="example-1"></a>Voorbeeld 1

Hiermee wordt gecontroleerd of het opgegeven telefoonnummer geldig is, op basis van het reguliere expressiepatroon van het telefoonnummer.

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoerclaims:
    - **claimToMatch**: "64854114520"
- Invoerparameters:
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**: "isPhone"
- Output claims:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>Voorbeeld 2

Controleert of het opgegeven e-mailadres geldig is en retourneert de e-mailalias.

```XML
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoerclaims:
    - **claimToMatch**:emily@contoso.com"
- Invoerparameters:
    - **matchTo**:`(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isEmail"
    - **uittrekselGroepen**: waar
- Output claims:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: true
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetclaimsifstringsAreEqual

Hiermee wordt gecontroleerd `matchTo` of een tekenreeksclaim en invoerparameter gelijk zijn `stringMatchMsg` `stringMatchMsgCode` en worden de uitvoerclaims ingesteld op de waarde `true` `false` die aanwezig is in en invoerparameters, samen met de claim voor het vergelijken van de resultaatuitvoer, die moet worden ingesteld als of op basis van het resultaat van de vergelijking.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | tekenreeks | Het claimtype, dat moet worden vergeleken. |
| Inputparameter | matchTo | tekenreeks | De string die `inputClaim`moet worden vergeleken met . |
| Inputparameter | tekenreeksVergelijking | tekenreeks | Mogelijke `Ordinal` waarden: `OrdinalIgnoreCase`of . |
| Inputparameter | stringMatchMsg | tekenreeks | Eerste waarde die moet worden ingesteld als tekenreeksen gelijk zijn. |
| Inputparameter | stringMatchMsgCode | tekenreeks | Tweede waarde die moet worden ingesteld als tekenreeksen gelijk zijn. |
| Uitvoerclaim | outputClaim1 | tekenreeks | Als tekenreeksen gelijk zijn, bevat `stringMatchMsg` deze uitvoerclaim de waarde van de invoerparameter. |
| Uitvoerclaim | outputClaim2 | tekenreeks | Als tekenreeksen gelijk zijn, bevat `stringMatchMsgCode` deze uitvoerclaim de waarde van de invoerparameter. |
| Uitvoerclaim | stringCompareResultClaim | booleaans | Het type claimtype van de resultatenuitvoer, dat moet worden ingesteld als `true` of `false` op basis van het resultaat van de vergelijking. |

U deze claimtransformatie gebruiken om te controleren of een claim gelijk is aan de waarde die u hebt opgegeven. De volgende claimstransformatie controleert bijvoorbeeld of de waarde van de claim `v1` **VanUseConsentVersion** gelijk is aan . Zo ja, wijzig `v2`dan de waarde in .

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim**: v1
- Invoerparameters:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: De TOS is geüpgraded naar v2
- Output claims:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: De TOS wordt geüpgraded naar v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetclaimsifStringsMatch

Hiermee wordt gecontroleerd `matchTo` of een tekenreeksclaim en invoerparameter gelijk zijn `outputClaimIfMatched` en worden de uitvoerclaims ingesteld op de `true` waarde `false` die aanwezig is in de invoerparameter, samen met de claim voor de uitvoer van resultaten vergelijken, die moet worden ingesteld als of op basis van het resultaat van de vergelijking.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | claimToMatch | tekenreeks | Het claimtype, dat moet worden vergeleken. |
| Inputparameter | matchTo | tekenreeks | De tekenreeks die moet worden vergeleken met inputClaim. |
| Inputparameter | tekenreeksVergelijking | tekenreeks | Mogelijke `Ordinal` waarden: `OrdinalIgnoreCase`of . |
| Inputparameter | outputClaimIfMatched | tekenreeks | De waarde die moet worden ingesteld als tekenreeksen gelijk zijn. |
| Uitvoerclaim | outputClaim | tekenreeks | Als tekenreeksen gelijk zijn, bevat `outputClaimIfMatched` deze uitvoerclaim de waarde van de invoerparameter. Of null, als de snaren niet overeenkomen. |
| Uitvoerclaim | stringCompareResultClaim | booleaans | Het type claimtype van de resultatenuitvoer, dat moet worden ingesteld als `true` of `false` op basis van het resultaat van de vergelijking. |

De volgende claimstransformatie controleert bijvoorbeeld of de waarde van `Minor`de claim **ageGroup** gelijk is aan . Zo ja, geef `B2C_V1_90001`de waarde terug naar .

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **claimToMatch**: Minor
- Invoerparameters:
    - **matchTo**: Minor
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Output claims:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: waar


## <a name="stringcontains"></a>Tekenreeksbevat

Bepaal of er een opgegeven subtekenreeks optreedt binnen de invoerclaim. Het resultaat is een nieuwe booleaanse `true` `false`ClaimType met een waarde van of . `true`als de waardeparameter binnen deze `false`tekenreeks optreedt, anders .

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | tekenreeks | Het claimtype, dat moet worden doorzocht. |
|Inputparameter|bevat|tekenreeks|De waarde om te zoeken.|
|Inputparameter|negerenHoofdletter|tekenreeks|Hiermee geeft u op of deze vergelijking het geval moet negeren van de tekenreeks die wordt vergeleken.|
| Uitvoerclaim | outputClaim | tekenreeks | Het ClaimType dat wordt geproduceerd nadat deze ClaimTransformation is ingeroepen. Een booleaanse indicator als de subtekenreeks optreedt binnen de invoerclaim. |

Gebruik deze claimtransformatie om te controleren of een tekenreeksclaimtype een subtekenreeks bevat. Controleert in het `roles` volgende voorbeeld of het tekenreeksclaimtype de waarde van **beheerder**bevat .

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim**: "Admin, Approver, Editor"
- Invoerparameters:
    - **bevat**: 'beheerder',
    - **ignoreCase**: true
- Output claims:
    - **outputClaim**: waar

## <a name="stringsubstring"></a>TekenreeksSubtekenreeks

Hiermee worden delen van een tekenreeksclaimtype uitgepakt, te beginnen bij het teken op de opgegeven positie, en wordt het opgegeven aantal tekens geretourneerd.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | tekenreeks | Het claimtype, dat de tekenreeks bevat. |
| Inputparameter | Startindex | int | De op nul gebaseerde begintekenpositie van een subtekenreeks in dit exemplaar. |
| Inputparameter | lengte | int | Het aantal tekens in de subtekenreeks. |
| Uitvoerclaim | outputClaim | booleaans | Een tekenreeks die gelijk is aan de subtekenreeks van de lengte die begint bij startIndex in dit geval, of Leeg als startIndex gelijk is aan de lengte van deze instantie en de lengte nul is. |

Neem bijvoorbeeld het voorvoegsel van het telefoonnummer land op.


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim**: "+1644114520"
- Invoerparameters:
    - **startIndex**: 0
    - **lengte**: 2
- Output claims:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>Stringvervangen

Hiermee zoekt u een tekenreeks voor het claimtype naar een opgegeven waarde en retourneert een nieuwe tekenreeks voor claimtypen waarin alle exemplaren van een opgegeven tekenreeks in de huidige tekenreeks worden vervangen door een andere opgegeven tekenreeks.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | tekenreeks | Het claimtype, dat de tekenreeks bevat. |
| Inputparameter | Oldvalue | tekenreeks | De string die moet worden doorzocht. |
| Inputparameter | newValue | tekenreeks | De tekenreeks om alle exemplaren van`oldValue` |
| Uitvoerclaim | outputClaim | booleaans | Een tekenreeks die gelijk is aan de huidige tekenreeks, behalve dat alle exemplaren van oldValue worden vervangen door newValue. Als oldValue niet wordt gevonden in de huidige instantie, retourneert de methode de huidige instantie ongewijzigd. |

Een telefoonnummer bijvoorbeeld normaliseren door de `-` tekens te verwijderen


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Voorbeeld

- Invoerclaims:
    - **inputClaim**: "+164-411-452-054"
- Invoerparameters:
    - **oldValue**: "-"
    - **lengte**: ""
- Output claims:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin (StringJoin)

Hiermee worden de elementen van een opgegeven claimtype voor tekenreeksverzameling samengevoegd met behulp van de opgegeven scheidingsteken tussen elk element of lid.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | tekenreeksVerzameling | Een collectie die de strings bevat om te concateneren. |
| Inputparameter | scheidingsteken | tekenreeks | De tekenreeks die u als scheidingspunt `,`moet gebruiken, zoals komma. |
| Uitvoerclaim | outputClaim | tekenreeks | Een tekenreeks die bestaat uit `inputClaim` de leden van `delimiter` de tekenreeksverzameling, afgebakend door de invoerparameter. |

In het volgende voorbeeld wordt een tekenreeksverzameling met gebruikersrollen aangewend en wordt deze omgezet naar een tekenreeks voor de scheidingsteken van komma's. U deze methode gebruiken om een tekenreeksverzameling op te slaan in het Azure AD-gebruikersaccount. Wanneer u het account later uit de `StringSplit` map leest, gebruikt u de tekenreeks om de tekenreeks voor de afzondering van komma's om te zetten in tekenreeksverzameling.

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputClaim**: [ "Admin", "Auteur", "Reader" ]
- Invoerparameters:
  - **de grensbegrenzer**: ","
- Output claims:
  - **outputClaim**: "Admin, Auteur, Reader"


## <a name="stringsplit"></a>StringSplit (StringSplit)

Retourneert een tekenreeksarray die de subtekenreeksen in deze instantie bevat die worden afgebakend door elementen van een opgegeven tekenreeks.

| Item | TransformationClaimType | Gegevenstype | Opmerkingen |
| ---- | ----------------------- | --------- | ----- |
| Invoerclaim | inputClaim | tekenreeks | Een tekenreeksclaimtype dat de subtekenreeksen bevat die moeten worden gesplitst. |
| Inputparameter | scheidingsteken | tekenreeks | De tekenreeks die u als scheidingspunt `,`moet gebruiken, zoals komma. |
| Uitvoerclaim | outputClaim | tekenreeksVerzameling | Een tekenreeksverzameling waarvan de elementen de subtekenreeksen in `delimiter` deze tekenreeks bevatten die worden afgebakend door de invoerparameter. |

In het volgende voorbeeld wordt een reeks gebruikersrollen van komma's aangewend en wordt deze omgezet in een tekenreeksverzameling.

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoerclaims:
  - **inputClaim**: "Admin, Auteur, Reader"
- Invoerparameters:
  - **de grensbegrenzer**: ","
- Output claims:
  - **outputClaim**: [ "Admin", "Auteur", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>Tekenreeksclaimtransformatiesexpressies
Expressies voor claimtransformaties in het aangepaste azure b2c-beleid bieden contextinformatie over de tenant-id en technische profiel-id.

  | Expressie | Beschrijving | Voorbeeld |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | De technische profielId naam. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | De tenant-ID van het beleid van de relying party. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | De tenant-id van het vertrouwenskader. | your-tenant.onmicrosoft.com |
