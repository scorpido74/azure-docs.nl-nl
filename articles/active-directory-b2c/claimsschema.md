---
title: Claimschema - Azure Active Directory B2C | Microsoft Documenten
description: Geef het element ClaimSchema van een aangepast beleid op in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4c3b3318e941723ec333597c7e4b3e48710152d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78397814"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het element **ClaimSchema** definieert de claimtypen waarnaar kan worden verwezen als onderdeel van het beleid. Claims schema is de plaats waar u uw claims declareert. Een claim kan voornaam, achternaam, weergavenaam, telefoonnummer en meer zijn. ClaimSchema-element bevat een lijst met **ClaimType-elementen.** Het element **ClaimType** bevat het kenmerk **Id,** de claimnaam.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

Het element **ClaimType** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt voor het claimtype. Andere elementen kunnen deze id gebruiken in het beleid. |

Het **element ClaimType** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | De titel die wordt weergegeven aan gebruikers op verschillende schermen. De waarde kan worden [gelokaliseerd](localization.md). |
| DataType | 1:1 | Het type van de claim. |
| DefaultPartnerClaimTypes DefaultPartnerClaimTypes DefaultPartnerClaimTypes DefaultPartner | 0:1 | De standaardclaimtypen van de partner die moeten worden gebruikt voor een opgegeven protocol. De waarde kan worden overschreven in het **PartnerClaimType** dat is opgegeven in de elementen **InputClaim** of **OutputClaim.** Gebruik dit element om de standaardnaam voor een protocol op te geven.  |
| Maskeren | 0:1 | Een optionele reeks maskertekens die kunnen worden toegepast bij het weergeven van de claim. Het telefoonnummer 324-232-4343 kan bijvoorbeeld worden gemaskeerd als XXX-XXX-4343. |
| UserHelpText | 0:1 | Een beschrijving van het claimtype dat gebruikers kan helpen om het doel ervan te begrijpen. De waarde kan worden [gelokaliseerd](localization.md). |
| UserInputType | 0:1 | Het type invoerbesturingselement dat beschikbaar moet zijn voor de gebruiker bij het handmatig invoeren van de claimgegevens voor het claimtype. Zie de gebruikersinvoertypen die later op deze pagina worden gedefinieerd. |
| AdminHelpTekst | 0:1 | Een beschrijving van het claimtype dat beheerders kunnen helpen om het doel ervan te begrijpen. |
| Beperking | 0:1 | De waardebeperkingen voor deze claim, zoals een reguliere expressie (Regex) of een lijst met acceptabele waarden. De waarde kan worden [gelokaliseerd](localization.md). |
PredicaatValidatieReferentie| 0:1 | Een verwijzing naar een **element PredicaatValidatiesInput.** Met de **elementen PredicateValidationReference** u een validatieproces uitvoeren om ervoor te zorgen dat alleen goed gevormde gegevens worden ingevoerd. Zie [Predicaten voor](predicates.md)meer informatie. |



### <a name="datatype"></a>DataType

Het element **DataType** ondersteunt de volgende waarden:

| Type | Beschrijving |
| ------- | ----------- |
|booleaans|Vertegenwoordigt een Booleaanse (of`true` `false`) waarde.|
|date| Vertegenwoordigt een moment in de tijd, meestal uitgedrukt als een datum van een dag. De waarde van de datum volgt op het ISO 8601-verdrag.|
|Datetime|Vertegenwoordigt een moment in de tijd, meestal uitgedrukt als een datum en tijd van de dag. De waarde van de datum volgt op het ISO 8601-verdrag.|
|duur|Vertegenwoordigt een tijdsinterval in jaren, maanden, dagen, uren, minuten en seconden. De notatie `PnYnMnDTnHnMnS`van `P` is , `N` waar positief aangeeft, of voor negatieve waarde. `nY`is het aantal jaren gevolgd `Y`door een letterlijke . `nMo`is het aantal maanden gevolgd `Mo`door een letterlijke . `nD`is het aantal dagen gevolgd `D`door een letterlijke . Voorbeelden: `P21Y` vertegenwoordigt 21 jaar. `P1Y2Mo`vertegenwoordigt een jaar, en twee maanden. `P1Y2Mo5D`vertegenwoordigt een jaar, twee maanden en vijf dagen.  `P1Y2M5DT8H5M620S`staat voor een jaar, twee maanden, vijf dagen, acht uur, vijf minuten en twintig seconden.  |
|phoneNumber|Vertegenwoordigt een telefoonnummer. |
|int| Vertegenwoordigt getal tussen -2.147.483.648 en 2.147.483.647|
|long| Vertegenwoordigt het getal tussen -9.223.372.036.854.775.808 tot 9.223.372.036.854.775.807 |
|tekenreeks| Hiermee vertegenwoordigt u tekst als een reeks UTF-16-code-eenheden.|
|tekenreeksVerzameling|Vertegenwoordigt een `string`verzameling van .|
|userIdentity| Vertegenwoordigt een gebruikersidentiteit.|
|userIdentity|Vertegenwoordigt een `userIdentity`verzameling van .|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes DefaultPartnerClaimTypes DefaultPartnerClaimTypes DefaultPartner

De **DefaultPartnerClaimTypes** kunnen het volgende element bevatten:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Protocol | 1:n | Lijst met protocollen met de typenaam standaardpartnerclaim. |

Het **element Protocol** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Name | Ja | De naam van een geldig protocol dat wordt ondersteund door Azure AD B2C. Mogelijke waarden zijn: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Ja | De naam van het claimtype die moet worden gebruikt. |

In het volgende voorbeeld wordt de **claim** toegewezen aan `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, met OpenIdConnect en OAuth2, wanneer het Identity Experience Framework samenwerkt met een `family_name`SAML2-identiteitsprovider of een aanvraag voor een relying party.

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Als gevolg hiervan zendt het JWT-token uitgegeven door `family_name` Azure AD B2C de **naamnaam**ClaimType uit .

```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Maskeren

Het element **Masker** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Type` | Ja | Het type claimmasker. Mogelijke `Simple` waarden: `Regex`of . De `Simple` waarde geeft aan dat een eenvoudig tekstmasker wordt toegepast op het regelgedeelte van een tekenreeksclaim. De `Regex` waarde geeft aan dat een reguliere expressie wordt toegepast op de tekenreeksclaim als geheel.  Als `Regex` de waarde is opgegeven, moet ook een optioneel kenmerk worden gedefinieerd met de reguliere expressie die moet worden gebruikt. |
| `Regex` | Nee | Als **`Type`** ingesteld `Regex`is op , geeft u de normale expressie op die u wilt gebruiken.

In het volgende voorbeeld wordt een `Simple` **claim PhoneNumber** geconfigureerd met het masker:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Het Identity Experience Framework geeft het telefoonnummer weer terwijl u de eerste zes cijfers verbergt:

![Telefoonnummer claim weergegeven in browser met eerste zes cijfers gemaskeerd door Xs](./media/claimsschema/mask.png)

In het volgende voorbeeld wordt een `Regex` **AlternateEmail-claim** geconfigureerd met het masker:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Het Identity Experience Framework geeft alleen de eerste letter van het e-mailadres en de e-maildomeinnaam weer:

![E-mailclaim weergegeven in browser met tekens gemaskeerd door sterretjes](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Beperking

Het element **Beperking** kan het volgende kenmerk bevatten:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Samenvoeggedrag | Nee | De methode die wordt gebruikt om opsommingswaarden samen te voegen met een ClaimType in een bovenliggend beleid met dezelfde id. Gebruik dit kenmerk wanneer u een claim overschrijft die is opgegeven in het basisbeleid. Mogelijke `Append`waarden: `Prepend`, `ReplaceAll`, of . De `Append` waarde is een verzameling gegevens die moet worden toegevoegd aan het einde van de verzameling die is opgegeven in het bovenliggende beleid. De `Prepend` waarde is een verzameling gegevens die moet worden toegevoegd voordat de verzameling is opgegeven in het bovenliggende beleid. De `ReplaceAll` waarde is een verzameling gegevens die zijn opgegeven in het bovenliggende beleid dat moet worden genegeerd. |

Het **element Beperking** bevat de volgende elementen:

| Element | Voorvallen | Beschrijving |
| ------- | ----------- | ----------- |
| Inventarisatie | 1:n | De beschikbare opties in de gebruikersinterface die de gebruiker voor een claim kan selecteren, zoals een waarde in een vervolgkeuzelijst. |
| Patroon | 1:1 | De reguliere uitdrukking te gebruiken. |

#### <a name="enumeration"></a>Inventarisatie

Met **het element Opsomming somordedes** beschikbare opties voor de gebruiker om te `CheckboxMultiSelect` `DropdownSingleSelect`selecteren `RadioSingleSelect`voor een claim in de gebruikersinterface, zoals een waarde in een , , of . U ook beschikbare opties definiëren en lokaliseren met element [Gelokaliseerde collecties.](localization.md#localizedcollections) Als u een item wilt opzoeken uit een verzameling **claim-opsommingen,** gebruikt u [GetMappedValueFromLocalizedCollection-claimtransformatie.](string-transformations.md#getmappedvaluefromlocalizedcollection)

Het element **Opsomming bevat** de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Tekst | Ja | De weergavetekenreeks die voor deze optie aan de gebruiker in de gebruikersinterface wordt weergegeven. |
|Waarde | Ja | De claimwaarde die is gekoppeld aan het selecteren van deze optie. |
| Standaard selecteren | Nee | Geeft aan of deze optie standaard moet worden geselecteerd in de gebruikersinterface. Mogelijke waarden: Waar of Onwaar. |

In het volgende voorbeeld wordt een claim voor een `New York`lijst met stedenvervolgkeuzelijst geconfigureerd met een standaardwaarde die is ingesteld op: **city**

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Vervolgkeuzelijst met een standaardwaarde ingesteld op New York:

![Vervolgkeuzebesturingselement dat in de browser wordt weergegeven en de standaardwaarde wordt weergegeven](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Patroon

Het element **Patroon** kan de volgende kenmerken bevatten:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Reguliere expressie | Ja | De reguliere expressie die dit type beweert, moet overeenkomen om geldig te zijn. |
| HelpTekst | Nee | Een foutbericht voor gebruikers als de reguliere expressiecontrole mislukt. |

In het volgende voorbeeld wordt een **e-mailclaim** geconfigureerd met regelmatige expressieinvoervalidatie en Help-tekst:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Het Identity Experience Framework geeft de e-mailadresclaim weer met validatie van e-mailformaatinvoer:

![TextBox met foutbericht geactiveerd door regex-beperking](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C ondersteunt verschillende gebruikersinvoertypen, zoals een tekstvak, wachtwoord en vervolgkeuzelijst die kunnen worden gebruikt bij het handmatig invoeren van claimgegevens voor het claimtype. U moet het **UserInputType** opgeven wanneer u informatie van de gebruiker verzamelt met behulp van een [zelf-beweerde technisch profiel](self-asserted-technical-profile.md) en [display-besturingselementen](display-controls.md).

De beschikbare gebruikersinvoertypen van het **UserInputType-element:**

| UserInputType | Ondersteund claimtype | Beschrijving |
| --------- | -------- | ----------- |
|SelectievakjeMultiSelect| `string` |Keuzelijst voor meerdere selecties. De claimwaarde wordt weergegeven in een tekenreeks kommascheiding van de geselecteerde waarden. |
|DateTimeDropdown | `date`, `dateTime` |Vervolgkeuzeafzetten om een dag, maand en jaar te selecteren. |
|VervolgkeuzelijstEénselecteren |`string` |Het vervolgkeuzevak selecteren selecteert. De claimwaarde is de geselecteerde waarde.|
|E-mailbox | `string` |Invoerveld e-mail. |
|Alinea | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Een veld dat alleen tekst weergeeft in een alineatag. |
|Wachtwoord | `string` |Wachtwoordtekstvak.|
|RadioEnkelselecteren |`string` | Verzameling keuzerondjes. De claimwaarde is de geselecteerde waarde.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Alleen-lezen tekstvak. |
|Tekstvak |`boolean`, `int`, `string` |Tekstvak met één regel. |


#### <a name="textbox"></a>Tekstvak

Het invoertype **TextBox-gebruikers** wordt gebruikt om een tekstvak met één regel op te geven.

![TextBox met eigenschappen die zijn opgegeven in claimtype](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>E-mailbox

Het invoertype Van De Gebruiker van **EmailBox** wordt gebruikt om een basisgebied van e-mailinput te verstrekken.

![EmailBox met eigenschappen die zijn opgegeven in claimtype](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Wachtwoord

Het invoertype **Wachtwoord** wordt gebruikt om een wachtwoord op te nemen dat door de gebruiker is ingevoerd.

![Claimtype gebruiken met wachtwoord](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

Het invoertype **van de gebruiker DateTimeDropdown** wordt gebruikt om een reeks vervolgkeuzevoorbeelden te bieden om een dag, maand en jaar te selecteren. U elementen Predicaten en Predicatenvalidatiegebruiken gebruiken om de minimum- en maximumdatumwaarden te bepalen. Zie de sectie **Een datumbereik** van [Predicaten en Predicaten](predicates.md)configureren voor meer informatie.

![Claimtype gebruiken met datetimedropdown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioEnkelselecteren

Het type **radiosingleselect-invoertype** wordt gebruikt om een verzameling keuzerondjes te bieden waarmee de gebruiker één optie kan selecteren.

![Claimtype gebruiken met radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>VervolgkeuzelijstEénselecteren

Het type **vervolgkeuzetype DropdownSingleSelect** wordt gebruikt om een vervolgkeuzelijst te bieden waarmee de gebruiker één optie kan selecteren.

![Claimtype gebruiken met dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>SelectievakjeMultiSelect

Het type gebruikersinvoer van **CheckboxMultiSelect** wordt gebruikt om een verzameling selectievakjes op te geven waarmee de gebruiker meerdere opties kan selecteren.

![Claimtype gebruiken met selectievakjeMultiselect](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

Het invoertype **Alleen-lezen** wordt gebruikt om een alleen-lezen veld te bieden om de claim en waarde weer te geven.

![Claimtype gebruiken met alleen-lezen](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Alinea

Het invoertype **Alinea-gebruiker** wordt gebruikt om een veld op te geven dat alleen tekst in een alineatag weergeeft.  Bijvoorbeeld &lt;p-tekst&lt;&gt;&gt;/p . Een **alinea-invoertype** `OutputClaim` zelfgeclaimd technisch profiel moet `Required` het `false` kenmerk (standaard) instellen.

![Claimtype gebruiken met alinea](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
