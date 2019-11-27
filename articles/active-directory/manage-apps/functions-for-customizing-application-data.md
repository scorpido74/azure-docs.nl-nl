---
title: Expressies schrijven voor kenmerk toewijzingen in azure AD
description: Informatie over het gebruik van expressietoewijzingen kenmerkwaarden omzetten in een acceptabele indeling tijdens de geautomatiseerde inrichting van objecten van de SaaS-app in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f1880a79f7fdb27b407ecb7ed1b761493fe850d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274028"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressies schrijven voor kenmerktoewijzingen in Azure Active Directory
Bij het configureren van inrichting tot een SaaS-toepassing, is een van de typen kenmerktoewijzingen die u kunt opgeven een expressie-toewijzing. Voor deze, moet u een script-achtige-expressie waarmee u uw gebruikers om gegevens te transformeren naar indelingen die meer geschikt is voor de SaaS-toepassing kunt schrijven.

## <a name="syntax-overview"></a>Overzicht van de syntaxis
De syntaxis voor expressies voor kenmerktoewijzingen is doet denken aan van Visual Basic voor toepassingen (VBA)-functies.

* De volledige expressie moet worden gedefinieerd in termen van functies, die bestaan uit een naam, gevolgd door argumenten tussen haakjes: <br>
  *Functie naam (`<<argument 1>>`,`<<argument N>>`)*
* Functies in elkaar kan worden genest. Bijvoorbeeld: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* U kunt drie verschillende typen argumenten doorgeven in functies:
  
  1. Kenmerken moeten tussen rechte haakjes worden geplaatst. Bijvoorbeeld: [attributeName]
  2. Tekenreeksconstanten moeten tussen dubbele aanhalingstekens worden geplaatst. Bijvoorbeeld: "VS"
  3. Andere functies. Bijvoorbeeld: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Voor tekenreeksconstanten, als u een backslash (\) of een aanhalingsteken (") in de tekenreeks, moet moet deze worden voorafgegaan door het symbool backslash (\). Bijvoorbeeld: "bedrijfs naam: \\" Contoso\\""

## <a name="list-of-functions"></a>Lijst met functies
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [deel](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [niet](#not) &nbsp;&nbsp;&nbsp;&nbsp; [vervangen](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [splitsen](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [toupper](#toupper)

---
### <a name="append"></a>Toevoegen
**Functieassembly**<br> Append(Source, suffix)

**Beschrijvingen**<br> Neemt een tekenreekswaarde bron en het achtervoegsel toegevoegd aan het einde van deze.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject. |
| **achtervoegsel** |Vereist |Tekenreeks |De tekenreeks die u wilt toevoegen aan het einde van de bronwaarde. |

---
### <a name="formatdatetime"></a>formatDateTime
**Functieassembly**<br> FormatDateTime (bron, inputFormat, uitvoerindeling)

**Beschrijvingen**<br> Neemt een tekenreeks met datum van de ene indeling en converteert naar een andere indeling.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject. |
| **inputFormat** |Vereist |Tekenreeks |De verwachte notatie van de bronwaarde. Zie [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)voor ondersteunde indelingen. |
| **Output** |Vereist |Tekenreeks |Indeling van de uitvoerdatum. |

---
### <a name="join"></a>Koppelen
**Functieassembly**<br> Deelnemen aan (scheidingsteken, bron1, bron2,...)

**Beschrijvingen**<br> Samen voegen () is vergelijkbaar met Append (), behalve dat het meerdere **bron** teken reeks waarden kan combi neren in één teken reeks en elke waarde wordt gescheiden door een **scheidings** teken reeks.

Als een van de bron waarden een kenmerk met meerdere waarden is, wordt elke waarde in dat kenmerk samengevoegd, gescheiden door de waarde voor het scheidings teken.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **scheiding** |Vereist |Tekenreeks |De tekenreeks die wordt gebruikt om de bronwaarden scheiden wanneer ze worden samengevoegd tot één tekenreeks. Kan ' ' als er geen scheidingsteken vereist is. |
| **source1 ... Bronn** |Vereist, variabele-aantal keren |Tekenreeks |De tekenreeks die waarden die moeten worden samengevoegd. |

---
### <a name="mid"></a>Mid
**Functieassembly**<br> Mid (bron, start, lengte)

**Beschrijvingen**<br> Retourneert een subtekenreeks van de bronwaarde. Een subtekenreeks is een tekenreeks zijn met slechts enkele van de tekens uit de brontekenreeks.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans de naam van het kenmerk. |
| **start** |Vereist |geheel getal |Index in de **bron** teken reeks waarin de subtekenreeks moet worden gestart. Het eerste teken in de tekenreeks index 1 hebben, tweede teken wordt index 2 hebben, enzovoort. |
| **lange** |Vereist |geheel getal |De lengte van de subtekenreeks. Als de lengte van de **bron** teken reeks eindigt, wordt met de functie subtekenreeks geretourneerd vanuit **Start** index tot het einde van de **bron** teken reeks. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Functieassembly**<br> NormalizeDiacritics(source)

**Beschrijvingen**<br> Vereist een tekenreeksargument. Retourneert de tekenreeks, maar met diakritische tekens vervangen door gelijkwaardige niet-diakritische tekens. Doorgaans gebruikt voor het omzetten van namen van de eerste en laatste diakritische tekens bevatten (accenttekens) in de geldige waarden die kunnen worden gebruikt in verschillende gebruikers-id, zoals de UPN-namen, SAM-accountnamen en e-mailadressen.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks | Meestal een voor naam-of achternaam-kenmerk. |

---
### <a name="not"></a>Not
**Functieassembly**<br> NOT(Source)

**Beschrijvingen**<br> Hiermee wordt de Booleaanse waarde van de **bron**gespiegeld. Als de **bron** waarde '*True*' is, wordt '*False*' geretourneerd. Anders wordt '*True*' geretourneerd.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Booleaanse tekenreeks |Verwachte **bron** waarden zijn ' True ' of ' false '. |

---
### <a name="replace"></a>Vervangen
**Functieassembly**<br> Vervang (bron, oldValue, regexPattern, regexGroupName, vervangende waarde, replacementAttributeName, sjabloon)

**Beschrijvingen**<br>
Vervangt waarden binnen een tekenreeks. Het werkt anders, afhankelijk van de opgegeven parameters:

* Wanneer **oldValue** en **replacementValue** worden gegeven:
  
  * Vervangt alle exemplaren van **oldValue** in de **bron** door **replacementValue**
* Wanneer **oldValue** en **sjabloon** worden gegeven:
  
  * Vervangt alle exemplaren van de **oldValue** in de **sjabloon** door de **bron** waarde
* Wanneer **regexPattern** en **replacementValue** worden gegeven:

  * De functie past de **regexPattern** toe op de **bron** teken reeks en u kunt de regex-groeps namen gebruiken om de teken reeks voor **replacementValue** te maken
* Als **regexPattern**, **regexGroupName**, **replacementValue** worden gegeven:
  
  * De functie past de **regexPattern** toe op de **bron** teken reeks en vervangt alle waarden die overeenkomen met **regexGroupName** met **replacementValue**
* Als **regexPattern**, **regexGroupName**, **replacementAttributeName** worden gegeven:
  
  * Als de **bron** geen waarde heeft, wordt de **bron** geretourneerd
  * Als de **bron** een waarde heeft, past de functie **de regexPattern** toe op de **bron** teken reeks en worden alle waarden die overeenkomen met **regexGroupName** vervangen door de waarde die is gekoppeld aan **replacementAttributeName**

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans naam van het kenmerk van het **bron** object. |
| **oldValue** |Optioneel |Tekenreeks |De waarde die moet worden vervangen in de **bron** of de **sjabloon**. |
| **regexPattern** |Optioneel |Tekenreeks |Regex-patroon voor de waarde die in de **bron**moet worden vervangen. Of, wanneer **replacementPropertyName** wordt gebruikt, patroon om waarde uit **replacementPropertyName**te halen. |
| **regexGroupName** |Optioneel |Tekenreeks |De naam van de groep in **regexPattern**. Alleen wanneer **replacementPropertyName** wordt gebruikt, wordt de waarde van deze groep geëxtraheerd als **replacementValue** van **replacementPropertyName**. |
| **replacementValue** |Optioneel |Tekenreeks |Nieuwe waarde te vervangen door oude met. |
| **replacementAttributeName** |Optioneel |Tekenreeks |Naam van het kenmerk dat moet worden gebruikt voor de vervangings waarde |
| **sjabloon** |Optioneel |Tekenreeks |Als u een **sjabloon** waarde opgeeft, worden de **oude** waarden in de sjabloon gezocht en vervangen door de **bron** waarde. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Functieassembly**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Beschrijvingen**<br> Minimaal twee argumenten die zijn gedefinieerd met behulp van expressies aanmaakregels voor unieke waarde is vereist. De functie evalueert van elke regel en controleert vervolgens of de waarde uniek in de doel-app/directory gegenereerd. De eerste unieke waarde gevonden, worden de geretourneerd. Als alle waarden al in het doel bestaat, wordt de vermelding ophalen verwekt en de reden wordt vastgelegd in de auditlogboeken. Er is geen bovengrens voor het aantal argumenten die kan worden opgegeven.

> [!NOTE]
> - Dit is een functie op het hoogste niveau, kunnen niet worden genest.
> - Deze functie kan niet worden toegepast op kenmerken met een overeenkomende prioriteit.  
> - Deze functie is alleen bedoeld om te worden gebruikt voor bewerkingen voor het item maken. Wanneer u het gebruikt met een-kenmerk, moet u de eigenschap **toewijzing Toep assen** instellen op **alleen tijdens het maken**van een object.
> - Deze functie wordt momenteel alleen ondersteund voor werk dagen Active Directory het inrichten van de gebruiker. Het kan niet worden gebruikt met andere inrichtings toepassingen. 


**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Ten minste zijn 2 afhankelijk van de vereiste, geen hoofdletters |Tekenreeks | Lijst met regels voor het genereren van unieke waarden om te evalueren. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Functieassembly**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beschrijvingen**<br> Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die zijn toegewezen aan een gebruiker voor een bepaalde toepassing. Deze functie is vereist om het appRoleAssignments-object om te zetten in een teken reeks met een enkele rolnaam. Houd er rekening mee dat de best practice ervoor moet zorgen dat er slechts één appRoleAssignment wordt toegewezen aan één gebruiker tegelijk. als er meerdere rollen zijn toegewezen, kan de geretourneerde functie teken reeks mogelijk niet voorspelbaar zijn. 

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Vereist |Tekenreeks |object **[appRoleAssignments]** . |

---
### <a name="split"></a>Splitsen
**Functieassembly**<br> Splitsen (bron, scheidings teken)

**Beschrijvingen**<br> Hiermee splitst u een teken reeks in een matrix met meerdere waarden met behulp van het opgegeven scheidings teken.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**bron** waarde die moet worden bijgewerkt. |
| **vorm** |Vereist |Tekenreeks |Hiermee geeft u het teken op dat wordt gebruikt om de teken reeks te splitsen (bijvoorbeeld: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Functieassembly**<br> StripSpaces(source)

**Beschrijvingen**<br> Verwijdert alle spaties ("") tekens uit de brontekenreeks.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**bron** waarde die moet worden bijgewerkt. |

---
### <a name="switch"></a>Switch
**Functieassembly**<br> Switch (bron, defaultValue, key1, value1, key2, waarde2,...)

**Beschrijvingen**<br> Als de **bron** waarde overeenkomt met een **sleutel**, retourneert **waarde** voor die **sleutel**. Als de **bron** waarde niet overeenkomt met een sleutel, wordt **DefaultValue**geretourneerd.  **Sleutel** -en **waarde** -para meters moeten altijd in paren zijn. De functie verwacht altijd een even aantal parameters.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**Bron** waarde die moet worden bijgewerkt. |
| **Standaard** |Optioneel |Tekenreeks |De standaardwaarde moet worden gebruikt wanneer de bron komt niet overeen met alle sleutels. Lege tekenreeks (""). |
| **sleutel** |Vereist |Tekenreeks |**Sleutel** voor het vergelijken van de **bron** waarde met. |
| **value** |Vereist |Tekenreeks |Vervangings waarde voor de **bron** die overeenkomt met de sleutel. |

---
### <a name="tolower"></a>ToLower
**Functieassembly**<br> ToLower (bron, cultuur)

**Beschrijvingen**<br> Neemt een *bron* teken reeks waarde en converteert deze in kleine letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject |
| **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Functieassembly**<br> ToUpper (bron, cultuur)

**Beschrijvingen**<br> Neemt een *bron* teken reeks waarde en converteert deze in hoofd letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Instellen**<br> 

| Naam | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject. |
| **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

## <a name="examples"></a>Voorbeelden
### <a name="strip-known-domain-name"></a>Bekende domeinnaam van strook/lijn
U moet de domeinnaam van een bekend van e-mailadres van een gebruiker om op te halen van de naam van een gebruiker verwijderen. <br>
Bijvoorbeeld, als het domein 'contoso.com' is, kan u de volgende expressie:

**Expressie** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (mail): "john.doe@contoso.com"
* **Uitvoer**: "John. Splinter"

### <a name="append-constant-suffix-to-user-name"></a>Constante achtervoegsel toevoegen aan de gebruikersnaam van
Als u van een Sandbox met Salesforce gebruikmaakt, moet u mogelijk een extra achtervoegsel toevoegen aan uw gebruikersnamen voordat deze worden gesynchroniseerd.

**Expressie** <br>
`Append([userPrincipalName], ".test")`

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer**: (userPrincipalName): "John.Doe@contoso.com"
* **Uitvoer**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gebruikersalias genereren door samenvoegen van onderdelen van de voornaam en achternaam
U moet een gebruiker alias genereren door te nemen van de eerste 3 letters van de voornaam van de gebruiker en eerste 5 letters van de achternaam van de gebruiker.

**Expressie** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (voor gegeven): John
* **Invoer** (achternaam): "Splinter"
* **Uitvoer**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Diakritische tekens verwijderen uit een tekenreeks
U moet vervangen van tekens lang zijn accenttekens met gelijkwaardige tekens die geen accenttekens bevatten.

**Expressie** <br>
NormalizeDiacritics([givenName])

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (voor OpgegevenNaam): "Zoë"
* **Uitvoer**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Een teken reeks splitsen in een matrix met meerdere waarden
U moet een door komma's gescheiden lijst met teken reeksen maken en deze opsplitsen in een matrix die kan worden aangesloten op een kenmerk met meerdere waarden, zoals het kenmerk PermissionSets van Sales Force. In dit voor beeld is een lijst met machtigingen sets ingevuld in extensionAttribute5 in azure AD.

**Expressie** <br>
Splitsen ([extensionAttribute5], ",")

**Voor beeld van invoer/uitvoer:** <br>

* **Invoer** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Uitvoerdatum als een tekenreeks in een bepaalde indeling
Wilt u datums verzenden naar een SaaS-toepassing in een bepaalde indeling. <br>
U wilt bijvoorbeeld datums voor ServiceNow.

**Expressie** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (extensionAttribute1): "20150123105347.1 z"
* **Uitvoer**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Vervangen door een waarde op basis van vooraf gedefinieerde set opties

U moet voor het definiëren van de tijdzone van de gebruiker op basis van de status opgeslagen in Azure AD. <br>
Als de status code komt niet overeen met een van de vooraf gedefinieerde opties, gebruikt u standaardwaarde van 'Australië/Sydney'.

**Expressie** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (status): "Qld"
* **Uitvoer**: "Australië/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Tekens vervangen met een reguliere expressie
U moet tekens vinden die overeenkomen met een reguliere expressie waarde en deze verwijderen.

**Expressie** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Voor beeld van invoer/uitvoer:**

* **Invoer** (mailnickname: "john_doe72"
* **Uitvoer**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Gegenereerde userPrincipalName-waarde converteren naar kleine letters
In het onderstaande voor beeld wordt de UPN-waarde gegenereerd door het samen voegen van de bron velden PreferredFirstName en PreferredLastName en de functie ToLower werkt op de gegenereerde teken reeks om alle tekens te converteren naar kleine letters. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Voor beeld van invoer/uitvoer:**

* **Invoer** (PreferredFirstName): "John"
* **Invoer** (PreferredLastName): "Smith"
* **Uitvoer**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Genereren van unieke waarde voor kenmerk userPrincipalName (UPN)
Gebaseerd op van de gebruiker voornaam, de tweede voornaam en achternaam, moet u het genereren van een waarde op voor het UPN-kenmerk en controleer de uniek in de-AD-doeldirectory voordat u de waarde toewijzen aan het UPN-kenmerk.

**Expressie** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Voor beeld van invoer/uitvoer:**

* **Invoer** (PreferredFirstName): "John"
* **Invoer** (PreferredLastName): "Smith"
* **Uitvoer**: "John.Smith@contoso.com" als de UPN-waarde van John.Smith@contoso.com niet al in de directory bestaat
* **Uitvoer**: "J.Smith@contoso.com" als de UPN-waarde van John.Smith@contoso.com al in de map bestaat
* **Uitvoer**: "Jo.Smith@contoso.com" als de bovenstaande twee UPN-waarden al aanwezig zijn in de map

## <a name="related-articles"></a>Gerelateerde artikelen
* [Gebruikers inrichting en ongedaan maken van de inrichting van SaaS-apps automatiseren](user-provisioning.md)
* [Kenmerk toewijzingen aanpassen voor het inrichten van gebruikers](customize-application-attributes.md)
* [Filters voor het inrichten van gebruikers in bereik](define-conditional-rules-for-provisioning-user-accounts.md)
* [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)
* [Meldingen voor het inrichten van accounts](user-provisioning.md)
* [Lijst met zelf studies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
