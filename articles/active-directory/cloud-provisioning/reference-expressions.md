---
title: Azure AD Connect cloud provisioning expressions and function reference Azure AD Connect cloud provisioning expressions and function reference Azure AD Connect cloud provisioning expressions and function reference Azure AD
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298612"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressies schrijven voor kenmerktoewijzingen in Azure Active Directory
Wanneer u cloudprovisioning configureert, is een van de typen kenmerktoewijzingen die u opgeven een expressietoewijzing. 

Met de expressietoewijzing u kenmerken aanpassen met behulp van een scriptachtige expressie.  Hierdoor u de on-premises gegevens omzetten in een nieuwe of andere waarde.  U bijvoorbeeld twee kenmerken combineren in één kenmerk, omdat dit ene kenmerk wordt gebruikt door een van uw cloudtoepassingen.

Het volgende document heeft betrekking op de scriptachtige expressies die worden gebruikt om de gegevens te transformeren.  Dit is slechts een deel van het proces.  Vervolgens moet u deze uitdrukking gebruiken en deze in een webverzoek aan uw tenant plaatsen.  Voor meer informatie over dat zie [Transformaties](how-to-transformation.md)

## <a name="syntax-overview"></a>Overzicht van syntaxis
De syntaxis voor expressies voor kenmerktoewijzingen doet denken aan vba-functies (Visual Basic for Applications).

* De volledige expressie moet worden gedefinieerd in termen van functies, die bestaan uit een naam gevolgd door argumenten tussen haakjes: <br>
  *FunctieNaam.`<<argument 1>>``<<argument N>>`*
* U functies in elkaar nesten. Bijvoorbeeld: <br> *FunctionOne(FunctionTwo)`<<argument1>>`*
* U drie verschillende typen argumenten doorgeven aan functies:
  
  1. Kenmerken, die moeten worden ingesloten in vierkante haakjes. Bijvoorbeeld: [attributeName]
  2. String constanten, die moeten worden ingesloten in dubbele aanhalingstekens. Bijvoorbeeld: "Verenigde Staten"
  3. Andere functies. Bijvoorbeeld: FunctionOne(`<<argument1>>`, FunctionTwo)`<<argument2>>`
* Voor tekenreeksconstanten moet, als u een backslash ( \ ) of aanhalingsteken ( " ) in de tekenreeks nodig hebt, deze worden ontsnapt met het symbool backslash ( \ ). Bijvoorbeeld: "Bedrijfsnaam: \\"Contoso\\""

## <a name="list-of-functions"></a>Lijst met functies
| Lijst met functies | Beschrijving |
|-----|----|
|[Toevoegen](#append)|Hiermee wordt een brontekenreekswaarde toegevoegd en wordt het achtervoegsel toegevoegd aan het einde ervan.|
|[BitAnd](#bitand)|De functie BitAnd stelt opgegeven bits in op een waarde.|
|[CBool CBool](#cbool)|De functie CBool retourneert een Booleaan op basis van de geëvalueerde expressie|
|[ConverterenFromBase64](#convertfrombase64)|Met de functie ConvertFromBase64 wordt de opgegeven basiswaarde 64 omgezet in een gewone tekenreeks.|
|[Converttobase64](#converttobase64)|De functie ConvertToBase64 converteert een tekenreeks naar een Unicode base64-tekenreeks. |
|[ConverttoUTF8Hex](#converttoutf8hex)|Met de functie ConvertToUTF8Hex wordt een tekenreeks omgezet in een UTF8 Hex-gecodeerde waarde.|
|[Count](#count)|De functie Aantal retourneert het aantal elementen in een kenmerk met meerdere waarden|
|[Cstr](#cstr)|De functie CStr wordt omgezet in een tekenreeksgegevenstype.|
|[DatumFromNum](#datefromnum)|De functie DateFromNum converteert een waarde in de datumnotatie van AD naar een Datumtijdtype.|
|[DNComponent](#dncomponent)|De functie DNComponent retourneert de waarde van een opgegeven DN-component die van links gaat.|
|[Fout](#error)|De functie Fout wordt gebruikt om een aangepaste fout te retourneren.|
|[FormatDateTime](#formatdatetime) |Hiermee neemt u een datumtekenreeks uit de ene notatie en zet deze om in een andere indeling.| 
|[Guid](#guid)|De functie Guid genereert een nieuwe willekeurige GUID.|           
|[Iif](#iif)|De functie IIF retourneert een van een set mogelijke waarden op basis van een bepaalde voorwaarde.|
|[Instr](#instr)|Met de functie InStr wordt het eerste optreden van een subtekenreeks in een tekenreeks gevonden.|
|[Isnull](#isnull)|Als de expressie wordt geëvalueerd naar Null, retourneert de functie IsNull true.|
|[IsnullorEmpty](#isnullorempty)|Als de expressie null of een lege tekenreeks is, retourneert de functie IsNullOrEmpty true.|         
|[Isaanwezig](#ispresent)|Als de expressie evalueert tot een tekenreeks die niet Null is en niet leeg is, retourneert de functie IsPresent true.|    
|[IsString](#isstring)|Als de expressie kan worden geëvalueerd tot een tekenreekstype, evalueert de functie IsString naar True.|
|[Item](#item)|De functie Item retourneert één item uit een tekenreeks met meerdere waarden/kenmerk.|
|[Toevoegen](#join) |Join() is vergelijkbaar met Append(), behalve dat het meerdere **brontekenreekswaarden** kan combineren tot één tekenreeks en elke waarde wordt gescheiden door een **scheidingstekenreeks.**| 
|[Links](#left)|De functie Links retourneert een opgegeven aantal tekens links van een tekenreeks.|
|[Midden](#mid) |Geeft als resultaat een subtekenreeks van de bronwaarde. Een subtekenreeks is een tekenreeks die slechts enkele tekens uit de brontekenreeks bevat.|
|[NormalizeDiacritics](#normalizediacritics)|Vereist één tekenreeksargument. Geeft als resultaat de tekenreeks, maar met diakritische tekens die zijn vervangen door gelijkwaardige niet-diakritische tekens.|
|[Not](#not) |Hiermee draait u de booleaanse waarde van de **bron**om. Als **de bronwaarde** *" True " is,* retourneert "*False*". Anders geeft het '*Waar '* terug.| 
|[Duplicaten verwijderen](#removeduplicates)|De functie Duplicaten verwijderen neemt een tekenreeks met meerdere waarden en zorgt ervoor dat elke waarde uniek is.| 
|[Vervangen](#replace) |Hiermee worden waarden binnen een tekenreeks vervangen. | 
|[SelecteerUnieke Waarde](#selectuniquevalue)|Vereist een minimum van twee argumenten, die unieke regels voor het genereren van waarde gedefinieerd met behulp van expressies. De functie evalueert elke regel en controleert vervolgens de waarde die is gegenereerd op uniciteit in de doel-app/map.| 
|[SingleAppRoleAssignment (SingleAppRoleAssignment)](#singleapproleassignment)|Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die aan een gebruiker voor een bepaalde toepassing zijn toegewezen.| 
|[Split](#split)|Splitst een tekenreeks op in een array met meerdere waarden met behulp van het opgegeven scheidingstekenteken.|
|[StringFromSID (StringFromSID)](#stringfromsid)|De functie StringFromSid converteert een bytearray met een beveiligings-id naar een tekenreeks.| 
|[Stripruimten](#stripspaces) |Hiermee verwijdert u alle spatie (" ") tekens uit de brontekenreeks.| 
|[Switch](#switch)|Wanneer **de bronwaarde** overeenkomt met een **sleutel**, retourneert **de waarde** voor die **sleutel**. | 
|[ToLower](#tolower)|Hiermee wordt een *brontekenreekswaarde* gebruikt en wordt deze omgezet in kleine letters met behulp van de opgegeven cultuurregels.| 
|[Toupper](#toupper)|Hiermee wordt een *brontekenreekswaarde* gebruikt en wordt deze omgezet in hoofdletters met behulp van de opgegeven cultuurregels.|
|[Trim](#trim)|Met de functie Bijsnijden worden voorloop- en achterliggende witte spaties uit een tekenreeks verwijderd.|
|[Word](#word)|De functie Word retourneert een woord in een tekenreeks, op basis van parameters die de te gebruiken scheidingstekens beschrijven en het woordnummer dat moet worden geretourneerd.|

---
### <a name="append"></a>Toevoegen
**Functie:**<br> Toevoegen(bron, achtervoegsel)

**Beschrijving:**<br> Hiermee wordt een brontekenreekswaarde toegevoegd en wordt het achtervoegsel toegevoegd aan het einde ervan.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Meestal de naam van het kenmerk van het bronobject. |
   | **Achtervoegsel** |Vereist |Tekenreeks |De tekenreeks die u wilt toevoegen aan het einde van de bronwaarde. |

---
### <a name="bitand"></a>BitAnd
**Beschrijving:**  
De functie BitAnd stelt opgegeven bits in op een waarde.

**Syntaxis:**  
`num BitAnd(num value1, num value2)`

* value1, value2: numerieke waarden die samen moeten worden en'ed

**Opmerkingen:**  
Deze functie zet beide parameters om in de binaire weergave en stelt een beetje in op:

* 0 - als een of beide van de overeenkomstige bits in *waarde1* en *waarde2* 0 zijn
* 1 - als beide van de overeenkomstige bits 1 zijn.

Met andere woorden, het retourneert 0 in alle gevallen, behalve wanneer de bijbehorende bits van beide parameters 1 zijn.

**Voorbeeld:**  
 
 `BitAnd(&HF, &HF7)`</br>
 Geeft als resultaat 7 omdat hexadecimale "F" EN "F7" evalueren tot deze waarde.

---

### <a name="cbool"></a>CBool CBool
**Beschrijving:**  
De functie CBool retourneert een Booleaan op basis van de geëvalueerde expressie

**Syntaxis:**  
`bool CBool(exp Expression)`

**Opmerkingen:**  
Als de expressie evalueert tot een niet-nulwaarde, retourneert CBool True, anders retourneert False.

**Voorbeeld:**  
`CBool([attrib1] = [attrib2])`  

Geeft als resultaat True als beide kenmerken dezelfde waarde hebben.

---
### <a name="convertfrombase64"></a>ConverterenFromBase64
**Beschrijving:**  
Met de functie ConvertFromBase64 wordt de opgegeven basiswaarde 64 omgezet in een gewone tekenreeks.

**Syntaxis:**  
`str ConvertFromBase64(str source)`- gaat uit van Unicode voor codering  
`str ConvertFromBase64(str source, enum Encoding)`

* bron: Base64 gecodeerde tekenreeks  
* Codering: Unicode, ASCII, UTF8

**Voorbeeld**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Beide voorbeelden keren terug "*Hallo wereld!*"

---
### <a name="converttobase64"></a>Converttobase64
**Beschrijving:**  
De functie ConvertToBase64 converteert een tekenreeks naar een Unicode base64-tekenreeks.  
Hiermee converteert u de waarde van een matrix van gehele getallen naar de gelijkwaardige tekenreeksweergave die is gecodeerd met basiscijfers 64.

**Syntaxis:**  
`str ConvertToBase64(str source)`

**Voorbeeld:**  
`ConvertToBase64("Hello world!")`  
Retourneert "SablagwabABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConverttoUTF8Hex
**Beschrijving:**  
Met de functie ConvertToUTF8Hex wordt een tekenreeks omgezet in een UTF8 Hex-gecodeerde waarde.

**Syntaxis:**  
`str ConvertToUTF8Hex(str source)`

**Opmerkingen:**  
De uitvoerindeling van deze functie wordt door Azure Active Directory gebruikt als Kenmerknotatie van DN.

**Voorbeeld:**  
`ConvertToUTF8Hex("Hello world!")`  
Geeft als resultaat 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**Beschrijving:**  
De functie Aantal retourneert het aantal elementen in een kenmerk met meerdere waarden

**Syntaxis:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>Cstr
**Beschrijving:**  
De functie CStr wordt omgezet in een tekenreeksgegevenstype.

**Syntaxis:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* waarde: kan een numerieke waarde, referentiekenmerk of Booleaan zijn.

**Voorbeeld:**  
`CStr([dn])`  
Kon terugkeren "cn = Joe, dc = contoso, dc =com"

---
### <a name="datefromnum"></a>DatumFromNum
**Beschrijving:**  
De functie DateFromNum converteert een waarde in de datumnotatie van AD naar een Datumtijdtype.

**Syntaxis:**  
`dt DateFromNum(num value)`

**Voorbeeld:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
Geeft als resultaat een datumtijd van 2012-01-01 23:00:00

---
### <a name="dncomponent"></a>DNComponent
**Beschrijving:**  
De functie DNComponent retourneert de waarde van een opgegeven DN-component die van links gaat.

**Syntaxis:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: het referentiekenmerk dat moet worden geïnterpreteerd
* ComponentNummer: de component in de DN om terug te keren

**Voorbeeld:**  
`DNComponent(CRef([dn]),1)`  
Als dn is "cn = Joe, ou =...," het keert Joe

---
### <a name="error"></a>Fout
**Beschrijving:**  
De functie Fout wordt gebruikt om een aangepaste fout te retourneren.

**Syntaxis:**  
`void Error(str ErrorMessage)`

**Voorbeeld:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Als het kenmerk accountName niet aanwezig is, gooit u een fout op het object.

---
### <a name="formatdatetime"></a>FormatDateTime
**Functie:**<br> FormatDateTime(bron, invoerOpmaak, uitvoerOpmaak)

**Beschrijving:**<br> Hiermee neemt u een datumtekenreeks uit de ene notatie en zet deze om in een andere indeling.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Meestal de naam van het kenmerk van het bronobject. |
   | **invoerOpmaak** |Vereist |Tekenreeks |Verwachte indeling van de bronwaarde. Zie [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)voor ondersteunde indelingen . |
   | **Outputformat** |Vereist |Tekenreeks |Opmaak van de uitvoerdatum. |

---
### <a name="guid"></a>GUID
**Beschrijving:**  
De functie Guid genereert een nieuwe willekeurige GUID

**Syntaxis:**  
`str Guid()`

---
### <a name="iif"></a>Iif
**Beschrijving:**  
De functie IIF retourneert een van een set mogelijke waarden op basis van een bepaalde voorwaarde.

**Syntaxis:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* voorwaarde: elke waarde of expressie die kan worden geëvalueerd op waar of onwaar.
* valueIfTrue: Als de voorwaarde wordt geëvalueerd op waar, wordt de geretourneerde waarde geretourneerd.
* valueIfFalse: Als de voorwaarde wordt geëvalueerd als false, wordt de geretourneerde waarde geretourneerd.

**Voorbeeld:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Als de gebruiker een stagiair is, retourneert de alias van een gebruiker met "t-" toegevoegd aan het begin van het, anders retourneert de gebruiker alias zoals het is.

---
### <a name="instr"></a>Instr
**Beschrijving:**  
Met de functie InStr wordt het eerste optreden van een subtekenreeks in een tekenreeks gevonden

**Syntaxis:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: tekenreeks die moet worden doorzocht
* stringmatch: string te vinden
* start: startpositie om de subtekenreeks te vinden
* vergelijken: vbTextCompare of vbBinaryCompare

**Opmerkingen:**  
Geeft als resultaat de positie waar de subtekenreeks is gevonden of 0 als deze niet is gevonden.

**Voorbeeld:**  
`InStr("The quick brown fox","quick")`  
Evalues tot 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Evalueert tot 7

---
### <a name="isnull"></a>Isnull
**Beschrijving:**  
Als de expressie wordt geëvalueerd naar Null, retourneert de functie IsNull true.

**Syntaxis:**  
`bool IsNull(var Expression)`

**Opmerkingen:**  
Voor een kenmerk wordt een Null uitgedrukt door het ontbreken van het kenmerk.

**Voorbeeld:**  
`IsNull([displayName])`  
Geeft als resultaat True als het kenmerk niet aanwezig is op de CS of MV.

---
### <a name="isnullorempty"></a>IsnullorEmpty
**Beschrijving:**  
Als de expressie null of een lege tekenreeks is, retourneert de functie IsNullOrEmpty true.

**Syntaxis:**  
`bool IsNullOrEmpty(var Expression)`

**Opmerkingen:**  
Voor een kenmerk wordt dit geëvalueerd naar True als het kenmerk afwezig is of aanwezig is, maar een lege tekenreeks is.  
Het omgekeerde van deze functie wordt IsPresent genoemd.

**Voorbeeld:**  
`IsNullOrEmpty([displayName])`  
Geeft als resultaat True als het kenmerk niet aanwezig is of een lege tekenreeks is in de CS of MV.

---
### <a name="ispresent"></a>Isaanwezig
**Beschrijving:**  
Als de expressie evalueert tot een tekenreeks die niet Null is en niet leeg is, retourneert de functie IsPresent true.

**Syntaxis:**  
`bool IsPresent(var expression)`

**Opmerkingen:**  
Het omgekeerde van deze functie wordt IsNullOrEmpty genoemd.

**Voorbeeld:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Beschrijving:**  
De functie Item retourneert één item uit een tekenreeks met meerdere waarden/kenmerk.

**Syntaxis:**  
`var Item(mvstr attribute, num index)`

* kenmerk: kenmerk met meerdere waarden
* index: index tot een item in de tekenreeks met meerdere waarden.

**Opmerkingen:**  
De functie Item is nuttig samen met de functie Bevat, omdat de laatste functie de index retourneert naar een item in het kenmerk met meerdere waarden.

Er wordt een fout gegenereerd als de index buiten de grenzen valt.

**Voorbeeld:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Geeft als resultaat het primaire e-mailadres.

---
### <a name="isstring"></a>IsString
**Beschrijving:**  
Als de expressie kan worden geëvalueerd tot een tekenreekstype, evalueert de functie IsString naar True.

**Syntaxis:**  
`bool IsString(var expression)`

**Opmerkingen:**  
Wordt gebruikt om te bepalen of CStr() succesvol kan zijn om de expressie te ontleden.

---
### <a name="join"></a>Koppelen
**Functie:**<br> Join(separator, bron1, bron2, ...)

**Beschrijving:**<br> Join() is vergelijkbaar met Append(), behalve dat het meerdere **brontekenreekswaarden** kan combineren tot één tekenreeks en elke waarde wordt gescheiden door een **scheidingstekenreeks.**

Als een van de bronwaarden een kenmerk met meerdere waarden is, wordt elke waarde in dat kenmerk samengevoegd, gescheiden door de scheidingswaarde.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Scheidingsteken** |Vereist |Tekenreeks |Tekenreeks die wordt gebruikt om bronwaarden te scheiden wanneer ze in één tekenreeks worden samengevoegd. Kan "" zijn als er geen scheidingsteken nodig is. |
   | **bron1 ... bronN** |Vereist, variabel aantal keren |Tekenreeks |Tekenreekswaarden die moeten worden samengevoegd. |

---
### <a name="left"></a>Links
**Beschrijving:**  
De functie Links retourneert een opgegeven aantal tekens links van een tekenreeks.

**Syntaxis:**  
`str Left(str string, num NumChars)`

* tekenreeks: de tekenreeks om tekens terug te sturen uit
* NumChars: een getal dat het aantal tekens aangeeft dat moet terugkeren vanaf het begin (links) van de tekenreeks

**Opmerkingen:**  
Een tekenreeks met de eerste numChars-tekens in tekenreeks:

* Als numChars = 0, retourneert u de lege tekenreeks.
* Als numChars < 0, retourneert u de invoertekenreeks.
* Als tekenreeks null is, retourneert u de lege tekenreeks.

Als tekenreeks minder tekens bevat dan het getal dat is opgegeven in numChars, wordt een tekenreeks die identiek is aan tekenreeks (dat wil zeggen alle tekens in parameter 1) geretourneerd.

**Voorbeeld:**  
`Left("John Doe", 3)`  
Retourneert `Joh`.

---
### <a name="mid"></a>Mid
**Functie:**<br> Midden(bron, begin, lengte)

**Beschrijving:**<br> Geeft als resultaat een subtekenreeks van de bronwaarde. Een subtekenreeks is een tekenreeks die slechts enkele tekens uit de brontekenreeks bevat.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Meestal de naam van het attribuut. |
   | **Start** |Vereist |geheel getal |Index in de **brontekenreeks** waar subtekenreeks moet beginnen. Eerste teken in de tekenreeks heeft een index van 1, het tweede teken heeft index 2, enzovoort. |
   | **Lengte** |Vereist |geheel getal |Lengte van de substring. Als de lengte buiten de **brontekenreeks** eindigt, retourneert de functie subtekenreeks van **beginindex** tot het einde van de **brontekenreeks.** |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Functie:**<br> NormaliserenDiacritics(bron)

**Beschrijving:**<br> Vereist één tekenreeksargument. Geeft als resultaat de tekenreeks, maar met diakritische tekens die zijn vervangen door gelijkwaardige niet-diakritische tekens. Meestal wordt gebruikt om voornamen en achternamen met diakritische tekens (accentmarkeringen) om te zetten in wettelijke waarden die kunnen worden gebruikt in verschillende gebruikers-id's, zoals gebruikershoofdnamen, SAM-accountnamen en e-mailadressen.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks | Meestal een voor- of achternaamattribuut. |

---
### <a name="not"></a>Not
**Functie:**<br> Niet(bron)

**Beschrijving:**<br> Hiermee draait u de booleaanse waarde van de **bron**om. Als **de bronwaarde** *" True " is,* retourneert "*False*". Anders geeft het '*Waar '* terug.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Booleaanse tekenreeks |Verwachte **bronwaarden** zijn 'Waar' of 'Onwaar'. |

---
### <a name="removeduplicates"></a>Duplicaten verwijderen
**Beschrijving:**  
De functie Duplicaten verwijderen neemt een tekenreeks met meerdere waarden en zorgt ervoor dat elke waarde uniek is.

**Syntaxis:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Voorbeeld:**  
`RemoveDuplicates([proxyAddresses])`  
Retourneert een ontsmet proxyAdreskenmerk waarbij alle dubbele waarden zijn verwijderd.

---
### <a name="replace"></a>Vervangen
**Functie:**<br> Vervangen(bron, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Beschrijving:**<br>
Hiermee worden waarden binnen een tekenreeks vervangen. Het werkt anders, afhankelijk van de opgegeven parameters:

* Wanneer **oldValue** en **vervangingWaarde** zijn verstrekt:
  
  * Vervangt alle exemplaren van **oudWaarde** in de **bron** door **vervangingWaarde**
* Wanneer **oldValue** en **sjabloon** zijn opgegeven:
  
  * Vervangt alle exemplaren van de **oudeWaarde** in de **sjabloon** door de **bronwaarde**
* Wanneer **regexPattern** en **vervangingwaarde** worden verstrekt:

  * De functie past de **regexPattern** toe op de **brontekenreeks** en u de regex-groepsnamen gebruiken om de tekenreeks te construeren voor **vervangingWaarde**
* Wanneer **regexPattern**, **regexGroupName**, **vervangingswaarde** wordt verstrekt:
  
  * De functie past de **regexPattern** toe op de **brontekenreeks** en vervangt alle waarden die overeenkomen met **regexGroupName** door **vervangingWaarde**
* Wanneer **regexPattern**, **regexGroupName**, **vervangendeAttribuutName** worden verstrekt:
  
  * Als **bron** geen waarde heeft, wordt **de bron** geretourneerd
  * Als **bron** een waarde heeft, past de functie de **regexPattern** toe op de **brontekenreeks** en vervangt alle waarden die overeenkomen met **regexGroupName** door de waarde die is gekoppeld aan **vervangendeKenmerken**

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Meestal de naam van het kenmerk van het **bronobject.** |
   | **Oldvalue** |Optioneel |Tekenreeks |Waarde die moet worden vervangen in **bron** of **sjabloon**. |
   | **regexPatroon** |Optioneel |Tekenreeks |Regex-patroon voor de waarde die in **de bron**moet worden vervangen . Of, wanneer **vervangenPropertyName** wordt gebruikt, patroon om waarde te extraheren uit **vervangenPropertyName**. |
   | **regexGroupName** |Optioneel |Tekenreeks |Naam van de groep in **regexPattern**. Alleen wanneer **vervangenPropertyName** wordt gebruikt, halen we de waarde van deze groep als **vervangingWaarde** uit **vervangendePropertyName**. |
   | **vervangingWaarde** |Optioneel |Tekenreeks |Nieuwe waarde om oude te vervangen met. |
   | **vervangingAttribuutNaam** |Optioneel |Tekenreeks |Naam van het kenmerk dat moet worden gebruikt voor vervangingswaarde |
   | **Sjabloon** |Optioneel |Tekenreeks |Wanneer **de sjabloonwaarde** wordt opgegeven, zoeken we naar **oldValue** in de sjabloon en vervangen deze door **bronwaarde.** |

---
### <a name="selectuniquevalue"></a>SelecteerUnieke Waarde
**Functie:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Beschrijving:**<br> Vereist een minimum van twee argumenten, die unieke regels voor het genereren van waarde gedefinieerd met behulp van expressies. De functie evalueert elke regel en controleert vervolgens de waarde die is gegenereerd op uniciteit in de doel-app/map. De eerste unieke waarde gevonden zal zijn degene die is geretourneerd. Als alle waarden al in het doel bestaan, wordt de vermelding geblokkeerd en wordt de reden geregistreerd in de controlelogboeken. Er is geen bovengrens aan het aantal argumenten dat kan worden verstrekt.

> [!NOTE]
> - Dit is een functie op het hoogste niveau, het kan niet worden genest.
> - Deze functie kan niet worden toegepast op kenmerken met een overeenkomende prioriteit.  
> - Deze functie is alleen bedoeld om te worden gebruikt voor entry creaties. Wanneer u het kenmerk gebruikt, stelt u de eigenschap **Toewijzing toepassen** in **op Alleen tijdens het maken van objecten**.
> - Deze functie wordt momenteel alleen ondersteund voor 'Workday to Active Directory User Provisioning'. Het kan niet worden gebruikt met andere inrichtingstoepassingen. 


**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Ten minste 2 zijn vereist, geen bovengrens |Tekenreeks | Lijst van unieke regels voor het genereren van waarde om te evalueren. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment (SingleAppRoleAssignment)
**Functie:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beschrijving:**<br> Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die aan een gebruiker voor een bepaalde toepassing zijn toegewezen. Deze functie is vereist om het object appRoleAssignments om te zetten in één rolnaamtekenreeks. Houd er rekening mee dat de beste praktijk is om ervoor te zorgen dat slechts één appRoleAssignment aan één gebruiker tegelijk wordt toegewezen en dat als meerdere rollen zijn toegewezen, de geretourneerde roltekenreeks mogelijk niet voorspelbaar is. 

**Parameters:**<br> 

  | Name | Vereist/ Herhalen | Type | Opmerkingen |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |Vereist |Tekenreeks |**[appRoleAssignments]** object. |

---
### <a name="split"></a>Splitsen
**Functie:**<br> Splitsen(bron, scheidingsteken)

**Beschrijving:**<br> Splitst een tekenreeks op in een array met meerdere waarden met behulp van het opgegeven scheidingstekenteken.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |**bronwaarde** bij te werken. |
   | **Scheidingsteken** |Vereist |Tekenreeks |Hiermee geeft u het teken op dat wordt gebruikt om de tekenreeks te splitsen (voorbeeld:",") |

---
### <a name="stringfromsid"></a>StringFromSid (StringFromSid)
**Beschrijving:**  
De functie StringFromSid converteert een bytearray met een beveiligings-id naar een tekenreeks.

**Syntaxis:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>Stripruimten
**Functie:**<br> StripSpaces(bron)

**Beschrijving:**<br> Hiermee verwijdert u alle spatie (" ") tekens uit de brontekenreeks.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |**bronwaarde** bij te werken. |

---
### <a name="switch"></a>Switch
**Functie:**<br> Switch(bron, standaardWaarde, toets1, waarde1, toets2, waarde2, ...)

**Beschrijving:**<br> Wanneer **de bronwaarde** overeenkomt met een **sleutel**, retourneert **de waarde** voor die **sleutel**. Als **de bronwaarde** niet overeenkomt met sleutels, retourneert **standaardWaarde**.  **Sleutel-** en **waardeparameters** moeten altijd in paren worden geleverd. De functie verwacht altijd een even aantal parameters.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |**Bronwaarde** om te controleren. |
   | **Standaardwaarde** |Optioneel |Tekenreeks |Standaardwaarde die moet worden gebruikt wanneer de bron niet overeenkomt met sleutels. Kan een lege tekenreeks zijn (""). |
   | **Sleutel** |Vereist |Tekenreeks |**Sleutel** om **de bronwaarde** mee te vergelijken. |
   | **value** |Vereist |Tekenreeks |Vervangingswaarde voor de **bron** die overeenkomt met de sleutel. |

---
### <a name="tolower"></a>ToLower
**Functie:**<br> ToLower (bron, cultuur)

**Beschrijving:**<br> Hiermee wordt een *brontekenreekswaarde* gebruikt en wordt deze omgezet in kleine letters met behulp van de opgegeven cultuurregels. Als er geen *cultuurinfo* wordt gespecificeerd, dan zal het invariantcultuur gebruiken.

**Parameters:**<br> 

   | Name | Vereist/ Herhalen | Type | Opmerkingen |
   | --- | --- | --- | --- |
   | **Bron** |Vereist |Tekenreeks |Meestal naam van het kenmerk van het bronobject |
   | **Cultuur** |Optioneel |Tekenreeks |Het formaat voor de cultuurnaam op basis van RFC 4646 is *languagecode2-country/regioncode2*, waarbij *languagecode2* de taalcode van twee letters is en *land/regioncode2* de subcultuurcode van twee letters. Voorbeelden hiervan zijn ja-JP voor Japans (Japan) en en-VS voor Engels (Verenigde Staten). In gevallen waarin een taalcode van twee letters niet beschikbaar is, wordt een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---

### <a name="toupper"></a>Toupper
**Functie:**<br> ToUpper(bron, cultuur)

**Beschrijving:**<br> Hiermee wordt een *brontekenreekswaarde* gebruikt en wordt deze omgezet in hoofdletters met behulp van de opgegeven cultuurregels. Als er geen *cultuurinfo* wordt gespecificeerd, dan zal het invariantcultuur gebruiken.

**Parameters:**<br> 

  | Name | Vereist/ Herhalen | Type | Opmerkingen |
  | --- | --- | --- | --- |
  | **Bron** |Vereist |Tekenreeks |Meestal de naam van het kenmerk van het bronobject. |
  | **Cultuur** |Optioneel |Tekenreeks |Het formaat voor de cultuurnaam op basis van RFC 4646 is *languagecode2-country/regioncode2*, waarbij *languagecode2* de taalcode van twee letters is en *land/regioncode2* de subcultuurcode van twee letters. Voorbeelden hiervan zijn ja-JP voor Japans (Japan) en en-VS voor Engels (Verenigde Staten). In gevallen waarin een taalcode van twee letters niet beschikbaar is, wordt een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---

### <a name="trim"></a>Trim
**Beschrijving:**  
Met de functie Bijsnijden worden voorloop- en achterliggende witte spaties uit een tekenreeks verwijderd.

**Syntaxis:**  
`str Trim(str value)`  

**Voorbeeld:**  
`Trim(" Test ")`  
Retourneert "Test".

`Trim([proxyAddresses])`  
Hiermee verwijdert u voorloop- en achterstandsruimten voor elke waarde in het kenmerk proxyAddress.

---
### <a name="word"></a>Word
**Beschrijving:**  
De functie Word retourneert een woord in een tekenreeks, op basis van parameters die de te gebruiken scheidingstekens beschrijven en het woordnummer dat moet worden geretourneerd.

**Syntaxis:**  
`str Word(str string, num WordNumber, str delimiters)`

* tekenreeks: de tekenreeks om een woord terug te geven.
* WordNumber: een getal dat aankaart welk woordnummer moet worden retournerd.
* delimiters: een tekenreeks die de scheidingsteken(s) vertegenwoordigt die moet worden gebruikt om woorden te identificeren

**Opmerkingen:**  
Elke reeks tekens in tekenreeks die wordt gescheiden door een van de tekens in de limiters, wordt aangeduid als woorden:

* Als getal < 1, wordt de lege tekenreeks geretourneerd.
* Als tekenreeks null is, retourneert lege tekenreeks.

Als tekenreeks minder dan getalwoorden bevat of als tekenreeks geen woorden bevat die door de limiters zijn geïdentificeerd, wordt een lege tekenreeks geretourneerd.

**Voorbeeld:**  
`Word("The quick brown fox",3," ")`  
Retourneert 'bruin'

`Word("This,string!has&many separators",3,",!&#")`  
Zou terugkeren "heeft"

## <a name="examples"></a>Voorbeelden
### <a name="strip-known-domain-name"></a>Bekende domeinnaam strippen
U moet een bekende domeinnaam uit de e-mail van een gebruiker halen om een gebruikersnaam te verkrijgen. <br>
Als het domein bijvoorbeeld 'contoso.com' is, u de volgende expressie gebruiken:

**Expressie:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Voorbeeldinvoer/uitvoer:** <br>

* **INPUT** (e-mail): "john.doe@contoso.com
* **OUTPUT**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Constant achtervoegsel toevoegen aan gebruikersnaam
Als u een Salesforce Sandbox gebruikt, moet u mogelijk een extra achtervoegsel toevoegen aan al uw gebruikersnamen voordat u deze synchroniseert.

**Expressie:** <br>
`Append([userPrincipalName], ".test")`

**Voorbeeldinvoer/-uitvoer:** <br>

* **INVOER**: (userPrincipalName): "John.Doe@contoso.com
* **OUTPUT**:John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gebruikersalias genereren door delen van voor- en achternaam te
U moet een gebruikersalias genereren door de eerste 3 letters van de voornaam van de gebruiker en de eerste 5 letters van de achternaam van de gebruiker te nemen.

**Expressie:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Voorbeeldinvoer/-uitvoer:** <br>

* **INPUT** (givenName): "John"
* **INPUT** (achternaam): "Doe"
* **OUTPUT**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Diacritics uit een tekenreeks verwijderen
U moet tekens met accentmarkeringen vervangen door gelijkwaardige tekens die geen accentmarkeringen bevatten.

**Expressie:** <br>
NormaliseerDiacritics([givenName])

**Voorbeeldinvoer/-uitvoer:** <br>

* **INPUT** (givenName): "Zoë"
* **OUTPUT**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Een tekenreeks splitsen in een array met meerdere waarden
U moet een door komma's afgebakende lijst met tekenreeksen opnemen in een array die kan worden aangesloten op een kenmerk met meerdere waarden, zoals het kenmerk PermissionSets van Salesforce. In dit voorbeeld is een lijst met machtigingssets ingevuld in extensieAttribute5 in Azure AD.

**Expressie:** <br>
Splitsen([extensieAttribuut5], ",")

**Voorbeeldinvoer/-uitvoer:** <br>

* **INPUT** (extensieAttribuut5): "PermissionSetOne, PermisionSetTwo"
* **UITVOER**: ["PermissionSetone", "PermissionSettwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Uitvoerdatum als tekenreeks in een bepaalde indeling
U wilt datums naar een SaaS-toepassing in een bepaalde indeling verzenden. <br>
U wilt bijvoorbeeld datums opmaken voor ServiceNow.

**Expressie:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Voorbeeldinvoer/-uitvoer:**

* **INPUT** (extensieAttribuut1): "20150123105347.1Z"
* **OUTPUT**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Een waarde vervangen op basis van vooraf gedefinieerde set opties

U moet de tijdzone van de gebruiker definiëren op basis van de statuscode die is opgeslagen in Azure AD. <br>
Als de statuscode niet overeenkomt met een van de vooraf gedefinieerde opties, gebruikt u de standaardwaarde 'Australië/Sydney'.

**Expressie:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Voorbeeldinvoer/-uitvoer:**

* **INVOER** (status): "QLD"
* **OUTPUT**: "Australië/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Tekens vervangen met een normale expressie
U moet tekens vinden die overeenkomen met een normale expressiewaarde en deze verwijderen.

**Expressie:** <br>

Vervangen([mailNickname], ,,[a-zA-Z_]*",, "",

**Voorbeeldinvoer/-uitvoer:**

* **INPUT** (mailNickname: "john_doe72"
* **OUTPUT**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>UPN-waarde (generated userPrincipalName) converteren naar kleine letters
In het onderstaande voorbeeld wordt de UPN-waarde gegenereerd door de bronvelden PreferredFirstName en PreferredLastName samen te stellen en werkt de functie ToLower op de gegenereerde tekenreeks om alle tekens om te zetten in kleine letters. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Voorbeeldinvoer/-uitvoer:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **OUTPUT**:john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Unieke waarde genereren voor het kenmerk USERPrincipalName (UPN)
Op basis van de voornaam, de tweede naam en achternaam van de gebruiker moet u een waarde voor het KENMERK UPN genereren en controleren op de uniciteit ervan in de doel-AD-map voordat u de waarde toekent aan het kenmerk UPN.

**Expressie:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Voorbeeldinvoer/-uitvoer:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **UITVOER**:John.Smith@contoso.com" " als John.Smith@contoso.com upn-waarde van niet al bestaat in de directory
* **UITVOER**:J.Smith@contoso.com" " als John.Smith@contoso.com upn-waarde van al bestaat in de directory
* **UITVOER**:Jo.Smith@contoso.com" " als de bovenstaande twee UPN-waarden al in de map staan


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
