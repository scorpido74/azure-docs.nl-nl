---
title: Azure AD Connect Cloud Provisioning-expressies en functie verwijzing
description: Verwijzing
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51c14fd7f427c29c47521a7355309e62ab2254ca
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298612"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressies schrijven voor kenmerk toewijzingen in Azure Active Directory
Wanneer u Cloud inrichting configureert, is een van de typen kenmerk toewijzingen die u kunt opgeven een expressie toewijzing. 

Met de expressie toewijzing kunt u kenmerken aanpassen met behulp van een script achtige expressie.  Zo kunt u de on-premises gegevens transformeren naar een nieuwe of een andere waarde.  U kunt bijvoorbeeld twee kenmerken samen voegen tot één kenmerk, omdat dit één kenmerk wordt gebruikt door een van uw Cloud toepassingen.

Het volgende document geldt voor de script achtige expressies die worden gebruikt om de gegevens te transformeren.  Dit maakt deel uit van het proces.  Vervolgens moet u deze expressie gebruiken en deze in een webaanvraag naar uw Tenant plaatsen.  Zie trans [formaties](how-to-transformation.md) voor meer informatie.

## <a name="syntax-overview"></a>Syntaxis overzicht
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
| Lijst met functies | Beschrijving |
|-----|----|
|[Toevoegen](#append)|Neemt een tekenreekswaarde bron en het achtervoegsel toegevoegd aan het einde van deze.|
|[BitAnd](#bitand)|De functie BitAnd stelt opgegeven bits in op een waarde.|
|[CBool](#cbool)|De functie CBool retourneert een Booleaanse waarde op basis van de geëvalueerde expressie|
|[ConvertFromBase64](#convertfrombase64)|De functie ConvertFromBase64 converteert de opgegeven base64-gecodeerde waarde naar een reguliere teken reeks.|
|[ConvertToBase64](#converttobase64)|De functie ConvertToBase64 converteert een teken reeks naar een Unicode base64-teken reeks. |
|[ConvertToUTF8Hex](#converttoutf8hex)|De functie ConvertToUTF8Hex converteert een teken reeks naar een UTF8 hexadecimale waarde.|
|[Aantal](#count)|De functie Count retourneert het aantal elementen in een kenmerk met meerdere waarden|
|[CStr](#cstr)|De functie CStr wordt geconverteerd naar een teken reeks gegevens type.|
|[DateFromNum](#datefromnum)|Met de functie DateFromNum wordt een waarde in de datum notatie van AD geconverteerd naar een DateTime-type.|
|[DNComponent](#dncomponent)|De functie DNComponent retourneert de waarde van een opgegeven DN-onderdeel van links.|
|[Optreedt](#error)|De functie Error wordt gebruikt om een aangepaste fout te retour neren.|
|[FormatDateTime](#formatdatetime) |Neemt een tekenreeks met datum van de ene indeling en converteert naar een andere indeling.| 
|[GPT](#guid)|Met de functie-GUID wordt een nieuwe wille keurige GUID gegenereerd.|           
|[IIF](#iif)|De functie IIF retourneert een van een set mogelijke waarden op basis van een opgegeven voor waarde.|
|[InStr](#instr)|De functie InStr vindt het eerste exemplaar van een subtekenreeks in een teken reeks.|
|[IsNull](#isnull)|Als de expressie resulteert in null, retourneert de functie IsNull de waarde True.|
|[IsNullOrEmpty](#isnullorempty)|Als de expressie Null of een lege teken reeks is, retourneert de functie IsNullOrEmpty ' True '.|         
|[IsPresent](#ispresent)|Als de expressie resulteert in een teken reeks die niet null en niet leeg is, retourneert de functie IsPresent de waarde True.|    
|[IsString](#isstring)|Als de expressie kan worden geëvalueerd als een teken reeks type, wordt de functie IsString geëvalueerd als True.|
|[Item](#item)|De functie item retourneert één item uit een teken reeks/kenmerk met meerdere waarden.|
|[Koppelen](#join) |Samen voegen () is vergelijkbaar met Append (), behalve dat het meerdere **bron** teken reeks waarden kan combi neren in één teken reeks en elke waarde wordt gescheiden door een **scheidings** teken reeks.| 
|[Gebleven](#left)|De functie Left retourneert een opgegeven aantal tekens vanaf de linkerkant van een teken reeks.|
|[Klemstoonstreepje](#mid) |Retourneert een subtekenreeks van de bronwaarde. Een subtekenreeks is een tekenreeks zijn met slechts enkele van de tekens uit de brontekenreeks.|
|[NormalizeDiacritics](#normalizediacritics)|Vereist een tekenreeksargument. Retourneert de tekenreeks, maar met diakritische tekens vervangen door gelijkwaardige niet-diakritische tekens.|
|[Ten](#not) |Hiermee wordt de Booleaanse waarde van de **bron**gespiegeld. Als de **bron** waarde '*True*' is, wordt '*False*' geretourneerd. Anders wordt '*True*' geretourneerd.| 
|[RemoveDuplicates](#removeduplicates)|De functie RemoveDuplicates gebruikt een teken reeks met meerdere waarden en zorg ervoor dat elke waarde uniek is.| 
|[Vervangen](#replace) |Vervangt waarden binnen een tekenreeks. | 
|[SelectUniqueValue](#selectuniquevalue)|Minimaal twee argumenten die zijn gedefinieerd met behulp van expressies aanmaakregels voor unieke waarde is vereist. De functie evalueert van elke regel en controleert vervolgens of de waarde uniek in de doel-app/directory gegenereerd.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die zijn toegewezen aan een gebruiker voor een bepaalde toepassing.| 
|[Delen](#split)|Hiermee splitst u een teken reeks in een matrix met meerdere waarden met behulp van het opgegeven scheidings teken.|
|[StringFromSID](#stringfromsid)|Met de functie StringFromSid wordt een byte matrix met een beveiligings-id omgezet in een teken reeks.| 
|[StripSpaces](#stripspaces) |Verwijdert alle spaties ("") tekens uit de brontekenreeks.| 
|[/Tijdnotatie](#switch)|Als de **bron** waarde overeenkomt met een **sleutel**, retourneert **waarde** voor die **sleutel**. | 
|[ToLower](#tolower)|Neemt een *bron* teken reeks waarde en converteert deze in kleine letters met de opgegeven cultuur regels.| 
|[ToUpper](#toupper)|Neemt een *bron* teken reeks waarde en converteert deze in hoofd letters met de opgegeven cultuur regels.|
|[Interne](#trim)|De functie Trim verwijdert voor loop-en volg spaties uit een teken reeks.|
|[Werk](#word)|De functie Word retourneert een woord dat deel uitmaakt van een teken reeks, op basis van para meters die de scheidings tekens beschrijven die moeten worden gebruikt en het woord nummer dat moet worden geretourneerd.|

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
### <a name="bitand"></a>BitAnd
**Beschrijvingen**  
De functie BitAnd stelt opgegeven bits in op een waarde.

**Syntaxis**  
`num BitAnd(num value1, num value2)`

* waarde1, waarde2: numerieke waarden die samen moeten worden AND'ed

**Opmerkingen**  
Deze functie converteert beide para meters naar de binaire weer gave en stelt een bit in op:

* 0: als een of beide van de corresponderende bits in *waarde1* en *Value2* 0 zijn
* 1: als beide corresponderende bits 1 zijn.

Met andere woorden: het retourneert 0 in alle gevallen, behalve wanneer de overeenkomstige bits van beide para meters 1 zijn.

**Voorbeeld:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Retourneert 7 omdat de hexadecimale waarde "F" en "F7" resulteren in deze waarden.

---

### <a name="cbool"></a>CBool
**Beschrijvingen**  
De functie CBool retourneert een Booleaanse waarde op basis van de geëvalueerde expressie

**Syntaxis**  
`bool CBool(exp Expression)`

**Opmerkingen**  
Als de expressie resulteert in een waarde die niet gelijk is aan nul, retourneert CBool True, anders wordt false geretourneerd.

**Voorbeeld:**  
`CBool([attrib1] = [attrib2])`  

Retourneert waar als beide kenmerken dezelfde waarde hebben.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Beschrijvingen**  
De functie ConvertFromBase64 converteert de opgegeven base64-gecodeerde waarde naar een reguliere teken reeks.

**Syntaxis**  
`str ConvertFromBase64(str source)`-gaat uit van Unicode voor code ring  
`str ConvertFromBase64(str source, enum Encoding)`

* Bron: base64-gecodeerde teken reeks  
* Encoding: Unicode, ASCII, UTF8

**Voorbeeld**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Beide voor beelden retour neren '*Hello World!* '

---
### <a name="converttobase64"></a>ConvertToBase64
**Beschrijvingen**  
De functie ConvertToBase64 converteert een teken reeks naar een Unicode base64-teken reeks.  
Hiermee wordt de waarde van een matrix met gehele getallen geconverteerd naar de equivalente teken reeks weergave die is gecodeerd met base-64 cijfers.

**Syntaxis**  
`str ConvertToBase64(str source)`

**Voorbeeld:**  
`ConvertToBase64("Hello world!")`  
Retourneert "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Beschrijvingen**  
De functie ConvertToUTF8Hex converteert een teken reeks naar een UTF8 hexadecimale waarde.

**Syntaxis**  
`str ConvertToUTF8Hex(str source)`

**Opmerkingen**  
De uitvoer indeling van deze functie wordt gebruikt door Azure Active Directory als DN-kenmerk indeling.

**Voorbeeld:**  
`ConvertToUTF8Hex("Hello world!")`  
Retourneert 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**Beschrijvingen**  
De functie Count retourneert het aantal elementen in een kenmerk met meerdere waarden

**Syntaxis**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Beschrijvingen**  
De functie CStr wordt geconverteerd naar een teken reeks gegevens type.

**Syntaxis**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* waarde: kan een numerieke waarde, een referentie kenmerk of een Boolean zijn.

**Voorbeeld:**  
`CStr([dn])`  
Kan ' CN = Joe, DC = contoso, DC = com ' retour neren

---
### <a name="datefromnum"></a>DateFromNum
**Beschrijvingen**  
Met de functie DateFromNum wordt een waarde in de datum notatie van AD geconverteerd naar een DateTime-type.

**Syntaxis**  
`dt DateFromNum(num value)`

**Voorbeeld:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Retourneert een datum/tijd die 2012-01-01 23:00:00 vertegenwoordigt

---
### <a name="dncomponent"></a>DNComponent
**Beschrijvingen**  
De functie DNComponent retourneert de waarde van een opgegeven DN-onderdeel van links.

**Syntaxis**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: het referentie kenmerk dat moet worden geïnterpreteerd
* ComponentNumber: het onderdeel in de DN dat moet worden geretourneerd

**Voorbeeld:**  
`DNComponent(CRef([dn]),1)`  
Als DN "CN = Joe, OE =...," retourneert Joe

---
### <a name="error"></a>Fout
**Beschrijvingen**  
De functie Error wordt gebruikt om een aangepaste fout te retour neren.

**Syntaxis**  
`void Error(str ErrorMessage)`

**Voorbeeld:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Als het kenmerk AccountName niet aanwezig is, genereert een fout op het object.

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
### <a name="guid"></a>Guid
**Beschrijvingen**  
De functie-GUID genereert een nieuwe wille keurige GUID

**Syntaxis**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Beschrijvingen**  
De functie IIF retourneert een van een set mogelijke waarden op basis van een opgegeven voor waarde.

**Syntaxis**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* voor waarde: elke waarde of expressie die als waar of ONWAAR kan worden geëvalueerd.
* valueIfTrue: als de voor waarde wordt geëvalueerd als waar, wordt de geretourneerde waarde.
* valueIfFalse: als de voor waarde wordt geëvalueerd als onwaar, wordt de geretourneerde waarde.

**Voorbeeld:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Als de gebruiker een interne is, retourneert de alias van een gebruiker waaraan ' t-' is toegevoegd. anders wordt de alias van de gebruiker geretourneerd.

---
### <a name="instr"></a>InStr
**Beschrijvingen**  
De functie InStr zoekt het eerste exemplaar van een subtekenreeks in een teken reeks

**Syntaxis**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: teken reeks die moet worden doorzocht
* stringmatch: teken reeks die moet worden gevonden
* starten: begin positie om de subtekenreeks te zoeken
* Compare: vbTextCompare of vbBinaryCompare

**Opmerkingen**  
Retourneert de positie waar de subtekenreeks is gevonden of 0 als deze niet is gevonden.

**Voorbeeld:**  
`InStr("The quick brown fox","quick")`  
Evalues tot 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Evalueert tot 7

---
### <a name="isnull"></a>IsNull
**Beschrijvingen**  
Als de expressie resulteert in null, retourneert de functie IsNull de waarde True.

**Syntaxis**  
`bool IsNull(var Expression)`

**Opmerkingen**  
Voor een kenmerk wordt een null-waarde uitgedrukt door het ontbreken van het kenmerk.

**Voorbeeld:**  
`IsNull([displayName])`  
Retourneert waar als het kenmerk niet aanwezig is in de CS of MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Beschrijvingen**  
Als de expressie Null of een lege teken reeks is, retourneert de functie IsNullOrEmpty ' True '.

**Syntaxis**  
`bool IsNullOrEmpty(var Expression)`

**Opmerkingen**  
Voor een-kenmerk resulteert dit in waar als het kenmerk ontbreekt of aanwezig is, maar een lege teken reeks is.  
De inverse van deze functie heeft de naam IsPresent.

**Voorbeeld:**  
`IsNullOrEmpty([displayName])`  
Retourneert waar als het kenmerk niet aanwezig is of een lege teken reeks in de CS of MV is.

---
### <a name="ispresent"></a>IsPresent
**Beschrijvingen**  
Als de expressie resulteert in een teken reeks die niet null en niet leeg is, retourneert de functie IsPresent de waarde True.

**Syntaxis**  
`bool IsPresent(var expression)`

**Opmerkingen**  
De inverse van deze functie heeft de naam IsNullOrEmpty.

**Voorbeeld:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Beschrijvingen**  
De functie item retourneert één item uit een teken reeks/kenmerk met meerdere waarden.

**Syntaxis**  
`var Item(mvstr attribute, num index)`

* kenmerk: kenmerk met meerdere waarden
* index: index naar een item in de teken reeks met meerdere waarden.

**Opmerkingen**  
De functie item is handig in combi natie met de functie contains, aangezien de laatste functie de index retourneert naar een item in het kenmerk met meerdere waarden.

Genereert een fout als de index buiten het bereik valt.

**Voorbeeld:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Retourneert het primaire e-mail adres.

---
### <a name="isstring"></a>IsString
**Beschrijvingen**  
Als de expressie kan worden geëvalueerd als een teken reeks type, wordt de functie IsString geëvalueerd als True.

**Syntaxis**  
`bool IsString(var expression)`

**Opmerkingen**  
Wordt gebruikt om te bepalen of CStr () kan worden uitgevoerd om de expressie te parseren.

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
### <a name="left"></a>Gebleven
**Beschrijvingen**  
De functie Left retourneert een opgegeven aantal tekens vanaf de linkerkant van een teken reeks.

**Syntaxis**  
`str Left(str string, num NumChars)`

* teken reeks: de teken reeks waaruit tekens moeten worden opgehaald
* NumChars: een nummer dat het aantal tekens aangeeft dat moet worden geretourneerd vanaf het begin (links) van de teken reeks

**Opmerkingen**  
Een teken reeks met de eerste numChars tekens in de teken reeks:

* Als numChars = 0, retourneert u een lege teken reeks.
* Als numChars < 0, wordt de invoer teken reeks geretourneerd.
* Als teken reeks null is, retourneert een lege teken reeks.

Als teken reeks minder tekens bevat dan het getal dat is opgegeven in numChars, wordt een teken reeks geretourneerd die identiek is aan de teken reeks (dat wil zeggen, met alle tekens in para meter 1).

**Voorbeeld:**  
`Left("John Doe", 3)`  
Retourneert `Joh`.

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
### <a name="removeduplicates"></a>RemoveDuplicates
**Beschrijvingen**  
De functie RemoveDuplicates gebruikt een teken reeks met meerdere waarden en zorg ervoor dat elke waarde uniek is.

**Syntaxis**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Voorbeeld:**  
`RemoveDuplicates([proxyAddresses])`  
Retourneert een gezuiverd proxyAddress attribuut-kenmerk waarbij alle dubbele waarden zijn verwijderd.

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
  |--- | --- | --- | --- |
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
### <a name="stringfromsid"></a>StringFromSid
**Beschrijvingen**  
Met de functie StringFromSid wordt een byte matrix met een beveiligings-id omgezet in een teken reeks.

**Syntaxis**  
`str StringFromSid(bin ObjectSID)`  

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
   | **Bron** |Vereist |Tekenreeks |Te controleren **bron** waarde. |
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

### <a name="toupper"></a>toUpper
**Functieassembly**<br> ToUpper (bron, cultuur)

**Beschrijvingen**<br> Neemt een *bron* teken reeks waarde en converteert deze in hoofd letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Instellen**<br> 

  | Naam | Vereiste / herhalende | Type | Opmerkingen |
  | --- | --- | --- | --- |
  | **Bron** |Vereist |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject. |
  | **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---

### <a name="trim"></a>interne
**Beschrijvingen**  
De functie Trim verwijdert voor loop-en volg spaties uit een teken reeks.

**Syntaxis**  
`str Trim(str value)`  

**Voorbeeld:**  
`Trim(" Test ")`  
Retourneert ' test '.

`Trim([proxyAddresses])`  
Hiermee verwijdert u voor loop-en volg spaties voor elke waarde in het kenmerk proxyAddress attribuut.

---
### <a name="word"></a>Word
**Beschrijvingen**  
De functie Word retourneert een woord dat deel uitmaakt van een teken reeks, op basis van para meters die de scheidings tekens beschrijven die moeten worden gebruikt en het woord nummer dat moet worden geretourneerd.

**Syntaxis**  
`str Word(str string, num WordNumber, str delimiters)`

* teken reeks: de teken reeks waaruit een woord moet worden geretourneerd.
* WordNumber: een getal dat aangeeft welk woord nummer moet worden geretourneerd.
* scheidings tekens: een teken reeks die de af te schrijven (en) voor het identificeren van woorden aangeeft

**Opmerkingen**  
Elke teken reeks in een teken reeks gescheiden door de tekens van de spaties wordt aangeduid als woorden:

* Als getal < 1 retourneert een lege teken reeks.
* Als de teken reeks null is, wordt een lege teken reeks geretourneerd.

Als teken reeks minder dan cijfer woorden bevat, of teken reeks geen woorden die zijn geïdentificeerd door scheidings tekens, wordt een lege teken reeks geretourneerd.

**Voorbeeld:**  
`Word("The quick brown fox",3," ")`  
Retourneert ' bruin '

`Word("This,string!has&many separators",3,",!&#")`  
Retourneert ' has '

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


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)
