---
title: Expressies schrijven voor kenmerktoewijzingen in Azure AD
description: Meer informatie over het gebruik van expressietoewijzingen om kenmerkwaarden om te zetten in een acceptabele indeling tijdens het geautomatiseerd inrichten van SaaS-app-objecten in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28e591234e28770a90bed827e4d36c6342661dd1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866589"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressies schrijven voor kenmerktoewijzingen in Azure Active Directory
Wanneer u de inrichting configureert voor een SaaS-toepassing, is een van de typen kenmerktoewijzingen die u opgeven een expressietoewijzing. Hiervoor moet u een scriptachtige expressie schrijven waarmee u de gegevens van uw gebruikers omzetten in indelingen die acceptabeler zijn voor de SaaS-toepassing.

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
[Toevoegen](#append) &nbsp; &nbsp; &nbsp; [Replace](#replace) [Mid](#mid) [Left](#left) [Count](#count) &nbsp; &nbsp; [BitAnd](#bitand) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Not](#not) [Join](#join) [CStr](#cstr) &nbsp; [InStr](#instr) &nbsp; [Item](#item) &nbsp; [IIF](#iif) &nbsp; [DateFromNum](#datefromnum) &nbsp; [CBool](#cbool) &nbsp; &nbsp; &nbsp; &nbsp; [Coalesce](#coalesce) &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; [IsString](#isstring) &nbsp; [SelectUniqueValue](#selectuniquevalue) [Guid](#guid) &nbsp; &nbsp; [IsNull](#isnull) &nbsp; [RemoveDuplicates](#removeduplicates) [IsPresent](#ispresent) &nbsp; [ConvertToBase64](#converttobase64) &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [IsNullOrEmpty](#isnullorempty) &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; &nbsp; BitAnd &nbsp; &nbsp; &nbsp; CBool &nbsp; &nbsp; Coalesce &nbsp; &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; Count &nbsp; &nbsp; CStr &nbsp; &nbsp; DateFromNum &nbsp; FormatDateTime &nbsp; &nbsp; Guid &nbsp; IIF &nbsp; InStr &nbsp; IsNull &nbsp; &nbsp; IsNullOrEmpty &nbsp; IsSitem &nbsp; Join &nbsp; Left Mid &nbsp; NormalizeDiacritics Not RemoveDuplicates Replace SelectUniqueValue &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) [Split](#split)&nbsp; [Word](#word) [StripSpaces](#stripspaces) &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) [ToLower](#tolower) SingleapproleAssignment &nbsp; &nbsp; Split &nbsp; &nbsp; StripSpaces&nbsp; Switch&nbsp; toLower&nbsp; toUpper Word&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp; &nbsp; &nbsp; &nbsp;

---
### <a name="append"></a>Toevoegen
**Functie:**<br> Toevoegen(bron, achtervoegsel)

**Beschrijving:**<br> Hiermee wordt een brontekenreekswaarde toegevoegd en wordt het achtervoegsel toegevoegd aan het einde ervan.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Meestal de naam van het kenmerk van het bronobject. |
| **Achtervoegsel** |Vereist |Tekenreeks |De tekenreeks die u wilt toevoegen aan het einde van de bronwaarde. |

---
### <a name="bitand"></a>BitAnd
**Functie:**<br> BitAnd(waarde1, waarde2)

**Beschrijving:**<br> Deze functie zet beide parameters om in de binaire weergave en stelt een beetje in op:

0 - als een of beide van de overeenkomstige bits in waarde1 en waarde2 0 zijn                                                  
1 - als beide van de overeenkomstige bits 1 zijn.                                    

Met andere woorden, het retourneert 0 in alle gevallen, behalve wanneer de bijbehorende bits van beide parameters 1 zijn.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **waarde1** |Vereist |num |Numerieke waarde die moet worden en'ed met waarde2|
| **waarde2** |Vereist |num |Numerieke waarde die moet worden en'ed met waarde1|

**Voorbeeld:**<br>
BitAnd (&HF, &HF7)                                                                                
11110111 EN 00000111 = 00000111 dus BitAnd retourneert 7, de binaire waarde van 00000111

---
### <a name="cbool"></a>CBool CBool
**Functie:**<br> CBool(Expressie)

**Beschrijving:**<br> CBool retourneert een booleaan op basis van de geëvalueerde expressie. Als de expressie evalueert tot een niet-nulwaarde, retourneert CBool True, anders retourneert False..

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Expressie** |Vereist | expressie | Elke geldige expressie |

**Voorbeeld:**<br>
CBool([attribuut1] = [attribuut2])                                                                    
Geeft als resultaat True als beide kenmerken dezelfde waarde hebben.

---
### <a name="coalesce"></a>Samenvoegen
**Functie:**<br> Coalesce(bron1, bron2, ..., defaultValue)

**Beschrijving:**<br> Geeft als resultaat de eerste bronwaarde die niet NULL is. Als alle argumenten NULL zijn en standaardWaarde aanwezig is, wordt de standaardwaarde geretourneerd. Als alle argumenten NULL zijn en standaardWaarde niet aanwezig is, retourneert Coalesce NULL.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **bron1 ... bronN** | Vereist | Tekenreeks |Vereist, variabel aantal keren. Meestal de naam van het kenmerk van het bronobject. |
| **Standaardwaarde** | Optioneel | Tekenreeks | Standaardwaarde die moet worden gebruikt wanneer alle bronwaarden NULL zijn. Kan een lege tekenreeks zijn ("").

---
### <a name="converttobase64"></a>Converttobase64
**Functie:**<br> ConvertToBase64(bron)

**Beschrijving:**<br> De functie ConvertToBase64 converteert een tekenreeks naar een Unicode base64-tekenreeks.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Tekenreeks die moet worden omgezet in basis 64|

**Voorbeeld:**<br>
ConvertToBase64("Hallo wereld!")                                                                                                        
Retourneert "SablagwabABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConverttoUTF8Hex
**Functie:**<br> ConvertToUTF8Hex(bron)

**Beschrijving:**<br> Met de functie ConvertToUTF8Hex wordt een tekenreeks omgezet in een UTF8 Hex-gecodeerde waarde.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Tekenreeks die moet worden omgezet in UTF8 Hex|

**Voorbeeld:**<br>
ConvertToUTF8Hex("Hallo wereld!")                                                                                                         
Geeft als resultaat 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**Functie:**<br> Aantal(kenmerk)

**Beschrijving:**<br> De functie Aantal retourneert het aantal elementen in een kenmerk met meerdere waarden

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Kenmerk** |Vereist |kenmerk |Kenmerk met meerdere waarden met elementen geteld|

---
### <a name="cstr"></a>Cstr
**Functie:**<br> CStr(waarde)

**Beschrijving:**<br> De functie CStr converteert een waarde naar een tekenreeksgegevenstype.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Waarde** |Vereist | numeriek, referentie of booleaan | Kan een numerieke waarde, referentiekenmerk of Booleaan zijn. |

**Voorbeeld:**<br>
CStr([dn])                                                            
Retourneert "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DatumFromNum
**Functie:**<br> DatumUitNum(waarde)

**Beschrijving:**<br> De functie DateFromNum converteert een waarde in de datumnotatie van AD naar een Datumtijdtype.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Waarde** |Vereist | Date | AD-datum die moet worden geconverteerd naar datetime-type |

**Voorbeeld:**<br>
DatumFromNum([laatsteLogonTimestamp])                                                                                                   
Datumuitnum(1296993240000000000)                                                            
Geeft als resultaat een datumtijd van 2012-01-01 23:00:00

---
### <a name="formatdatetime"></a>FormatDateTime
**Functie:**<br> FormatDateTime(bron, invoerOpmaak, uitvoerOpmaak)

**Beschrijving:**<br> Hiermee neemt u een datumtekenreeks uit de ene notatie en zet deze om in een andere indeling.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Meestal de naam van het kenmerk van het bronobject. |
| **invoerOpmaak** |Vereist |Tekenreeks |Verwachte indeling van de bronwaarde. Zie [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)voor ondersteunde indelingen . |
| **Outputformat** |Vereist |Tekenreeks |Opmaak van de uitvoerdatum. |

---
### <a name="guid"></a>GUID
**Functie:**<br> Guid()

**Beschrijving:**<br> De functie Guid genereert een nieuwe willekeurige GUID

---
### <a name="iif"></a>Iif
**Functie:**<br> IIF(voorwaarde, valueIfTrue,valueIfFalse)

**Beschrijving:**<br> De functie IIF retourneert een van een set mogelijke waarden op basis van een bepaalde voorwaarde.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Voorwaarde** |Vereist |Variabele of expressie |Elke waarde of expressie die kan worden geëvalueerd op waar of onwaar. |
| **valueIfTrue** |Vereist |Variabele of tekenreeks | Als de voorwaarde wordt geëvalueerd op waar, wordt de geretourneerde waarde geretourneerd. |
| **waardeAlsFalse** |Vereist |Variabele of tekenreeks |Als de voorwaarde wordt geëvalueerd als false, wordt de geretourneerde waarde geretourneerd.|

**Voorbeeld:**<br>
IIF([land]="USA",[land],[afdeling])

---
### <a name="instr"></a>Instr
**Functie:**<br> InStr(waarde1,value2,start,compareType)

**Beschrijving:**<br> Met de functie InStr wordt het eerste optreden van een subtekenreeks in een tekenreeks gevonden

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **waarde1** |Vereist |Tekenreeks |Tekenreeks die moet worden doorzocht |
| **waarde2** |Vereist |Tekenreeks |Tekenreeks die moet worden gevonden |
| **Start** |Optioneel |Geheel getal |Startpositie om de subtekenreeks te vinden|
| **compareType** |Optioneel |Enum |Kan vbTextCompare of vbBinaryCompare zijn |

**Voorbeeld:**<br>
InStr("De snelle bruine vos","snel")                                                                             
Evalues tot 5

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
Evalueert tot 7

---
### <a name="isnull"></a>Isnull
**Functie:**<br> IsNull(Expressie)

**Beschrijving:**<br> Als de expressie wordt geëvalueerd naar Null, retourneert de functie IsNull true. Voor een kenmerk wordt een Null uitgedrukt door het ontbreken van het kenmerk.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Expressie** |Vereist |expressie |Expressie die moet worden geëvalueerd |

**Voorbeeld:**<br>
IsNull([displayName])                                                                                                
Geeft als resultaat True als het kenmerk niet aanwezig is

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Functie:**<br> IsnullorEmpty(expressie)

**Beschrijving:**<br> Als de expressie null of een lege tekenreeks is, retourneert de functie IsNullOrEmpty true. Voor een kenmerk wordt dit geëvalueerd naar True als het kenmerk afwezig is of aanwezig is, maar een lege tekenreeks is.
Het omgekeerde van deze functie wordt IsPresent genoemd.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Expressie** |Vereist |expressie |Expressie die moet worden geëvalueerd |

**Voorbeeld:**<br>
IsNullOrEmpty([displayName])                                               
Geeft als resultaat True als het kenmerk niet aanwezig is of een lege tekenreeks is

---
### <a name="ispresent"></a>Isaanwezig
**Functie:**<br> IsPresent(Expressie)

**Beschrijving:**<br> Als de expressie evalueert tot een tekenreeks die niet Null is en niet leeg is, retourneert de functie IsPresent true. Het omgekeerde van deze functie wordt IsNullOrEmpty genoemd.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Expressie** |Vereist |expressie |Expressie die moet worden geëvalueerd |

**Voorbeeld:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**Functie:**<br> IsString(Expressie)

**Beschrijving:**<br> Als de expressie kan worden geëvalueerd tot een tekenreekstype, evalueert de functie IsString naar True.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Expressie** |Vereist |expressie |Expressie die moet worden geëvalueerd |

---
### <a name="item"></a>Item
**Functie:**<br> Item(attribuut, index)

**Beschrijving:**<br> De functie Item retourneert één item uit een tekenreeks met meerdere waarden/kenmerk.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Kenmerk** |Vereist |Kenmerk |Kenmerk met meerdere waarden dat moet worden doorzocht |
| **Index** |Vereist |Geheel getal | Indexeren naar een item in de tekenreeks met meerdere waarden|

**Voorbeeld:**<br>
Item([proxyAdressen], 1)

---
### <a name="join"></a>Koppelen
**Functie:**<br> Join(separator, bron1, bron2, ...)

**Beschrijving:**<br> Join() is vergelijkbaar met Append(), behalve dat het meerdere **brontekenreekswaarden** kan combineren tot één tekenreeks en elke waarde wordt gescheiden door een **scheidingstekenreeks.**

Als een van de bronwaarden een kenmerk met meerdere waarden is, wordt elke waarde in dat kenmerk samengevoegd, gescheiden door de scheidingswaarde.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Scheidingsteken** |Vereist |Tekenreeks |Tekenreeks die wordt gebruikt om bronwaarden te scheiden wanneer ze in één tekenreeks worden samengevoegd. Kan "" zijn als er geen scheidingsteken nodig is. |
| **bron1 ... bronN** |Vereist, variabel aantal keren |Tekenreeks |Tekenreekswaarden die moeten worden samengevoegd. |

---
### <a name="left"></a>Links
**Functie:**<br> Links(Tekenreeks, NumChars)

**Beschrijving:**<br> De functie Links retourneert een opgegeven aantal tekens links van een tekenreeks. Als numChars = 0, retourneert u de lege tekenreeks.
Als numChars < 0, retourneert u de invoertekenreeks.
Als tekenreeks null is, retourneert u de lege tekenreeks.
Als tekenreeks minder tekens bevat dan het getal dat is opgegeven in numChars, wordt een tekenreeks die identiek is aan tekenreeks (dat wil zeggen alle tekens in parameter 1) geretourneerd.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Tekenreeks** |Vereist |Kenmerk | De tekenreeks om tekens terug te sturen uit |
| **NumChars NumChars** |Vereist |Geheel getal | Een getal dat het aantal tekens aankaart dat moet terugkeren vanaf het begin (links) van de tekenreeks|

**Voorbeeld:**<br>
Links ("John Doe", 3)                                                            
Retourneert "Joh"

---
### <a name="mid"></a>Mid
**Functie:**<br> Midden(bron, begin, lengte)

**Beschrijving:**<br> Geeft als resultaat een subtekenreeks van de bronwaarde. Een subtekenreeks is een tekenreeks die slechts enkele tekens uit de brontekenreeks bevat.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Meestal de naam van het attribuut. |
| **Start** |Vereist |geheel getal |Index in de **brontekenreeks** waar subtekenreeks moet beginnen. Eerste teken in de tekenreeks heeft een index van 1, het tweede teken heeft index 2, enzovoort. |
| **Lengte** |Vereist |geheel getal |Lengte van de substring. Als de lengte buiten de **brontekenreeks** eindigt, retourneert de functie subtekenreeks van **beginindex** tot het einde van de **brontekenreeks.** |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Functie:**<br> NormaliserenDiacritics(bron)

**Beschrijving:**<br> Vereist één tekenreeksargument. Geeft als resultaat de tekenreeks, maar met diakritische tekens die zijn vervangen door gelijkwaardige niet-diakritische tekens. Meestal wordt gebruikt om voornamen en achternamen met diakritische tekens (accentmarkeringen) om te zetten in wettelijke waarden die kunnen worden gebruikt in verschillende gebruikers-id's, zoals gebruikershoofdnamen, SAM-accountnamen en e-mailadressen.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks | Meestal een voor- of achternaamattribuut. |

---
### <a name="not"></a>Not
**Functie:**<br> Niet(bron)

**Beschrijving:**<br> Hiermee draait u de booleaanse waarde van de **bron**om. Als **de bronwaarde** *" True " is,* retourneert "*False*". Anders geeft het '*Waar '* terug.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Booleaanse tekenreeks |Verwachte **bronwaarden** zijn 'Waar' of 'Onwaar'. |

---
### <a name="numfromdate"></a>NumFromDate NumfromDate
**Functie:**<br> NumFromDate(waarde)

**Beschrijving:**<br> De functie NumFromDate converteert een DatumTijd-waarde naar Active Directory-indeling die vereist is om kenmerken zoals accountExpires in te [stellen.](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires) Gebruik deze functie om DateTime-waarden die zijn ontvangen van HR-apps in de cloud, zoals Workday en SuccessFactors, om te zetten in hun gelijkwaardige AD-weergave. 

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Waarde** |Vereist | Tekenreeks | Datumtekenreeks in de ondersteunde indeling. Zie https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspxvoor ondersteunde indelingen . |

**Voorbeeld:**<br>
* Voorbeeld van werkdag <br>
  Ervan uitgaande dat u het kenmerk *ContractEndDate* van Workday, dat in de indeling *2020-12-31-08:00* is, wilt toewijzen aan het veld *Verloopt* in AD, u deze functie als volgt gebruiken en de tijdzone-compensatie wijzigen om overeen te komen met uw landinstelling. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Voorbeeld van SuccessFactors <br>
  Ervan uitgaande dat u het *kenmerk endDate* van SuccessFactors in kaart wilt brengen, dat in de indeling *M/d/yyyy hh:mm:ss tt* naar *accountExpires* veld in AD is, u deze functie gebruiken en de tijdzone-offset wijzigen om overeen te komen met uw landinstelling.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>Duplicaten verwijderen
**Functie:**<br> Duplicaten verwijderen(kenmerk)

**Beschrijving:**<br> De functie Duplicaten verwijderen neemt een tekenreeks met meerdere waarden en zorgt ervoor dat elke waarde uniek is.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Kenmerk** |Vereist |Kenmerk met meerdere waarden |Kenmerk met meerdere waarden waarbij duplicaten zijn verwijderd|

**Voorbeeld:**<br>
Duplicaten verwijderen([proxyAdressen])                                                                                                       
Retourneert een ontsmet proxyAdres-kenmerk waarbij alle dubbele waarden zijn verwijderd

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

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
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


 - Dit is een functie op het hoogste niveau, het kan niet worden genest.
 - Deze functie kan niet worden toegepast op kenmerken met een overeenkomende prioriteit.   
 - Deze functie is alleen bedoeld om te worden gebruikt voor entry creaties. Wanneer u het kenmerk gebruikt, stelt u de eigenschap **Toewijzing toepassen** in **op Alleen tijdens het maken van objecten**.
 - Deze functie wordt momenteel alleen ondersteund voor 'Workday to Active Directory User Provisioning'. Het kan niet worden gebruikt met andere inrichtingstoepassingen. 


**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Ten minste 2 zijn vereist, geen bovengrens |Tekenreeks | Lijst van unieke regels voor het genereren van waarde om te evalueren. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment (SingleAppRoleAssignment)
**Functie:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beschrijving:**<br> Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die aan een gebruiker voor een bepaalde toepassing zijn toegewezen. Deze functie is vereist om het object appRoleAssignments om te zetten in één rolnaamtekenreeks. Houd er rekening mee dat de beste praktijk is om ervoor te zorgen dat slechts één appRoleAssignment aan één gebruiker tegelijk wordt toegewezen en dat als meerdere rollen zijn toegewezen, de geretourneerde roltekenreeks mogelijk niet voorspelbaar is. 

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Vereist |Tekenreeks |**[appRoleAssignments]** object. |

---
### <a name="split"></a>Splitsen
**Functie:**<br> Splitsen(bron, scheidingsteken)

**Beschrijving:**<br> Splitst een tekenreeks op in een array met meerdere waarden met behulp van het opgegeven scheidingstekenteken.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**bronwaarde** bij te werken. |
| **Scheidingsteken** |Vereist |Tekenreeks |Hiermee geeft u het teken op dat wordt gebruikt om de tekenreeks te splitsen (voorbeeld:",") |

---
### <a name="stripspaces"></a>Stripruimten
**Functie:**<br> StripSpaces(bron)

**Beschrijving:**<br> Hiermee verwijdert u alle spatie (" ") tekens uit de brontekenreeks.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**bronwaarde** bij te werken. |

---
### <a name="switch"></a>Switch
**Functie:**<br> Switch(bron, standaardWaarde, toets1, waarde1, toets2, waarde2, ...)

**Beschrijving:**<br> Wanneer **de bronwaarde** overeenkomt met een **sleutel**, retourneert **de waarde** voor die **sleutel**. Als **de bronwaarde** niet overeenkomt met sleutels, retourneert **standaardWaarde**.  **Sleutel-** en **waardeparameters** moeten altijd in paren worden geleverd. De functie verwacht altijd een even aantal parameters. De functie mag niet worden gebruikt voor referentiële kenmerken zoals manager. 

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |**Bronwaarde** om bij te werken. |
| **Standaardwaarde** |Optioneel |Tekenreeks |Standaardwaarde die moet worden gebruikt wanneer de bron niet overeenkomt met sleutels. Kan een lege tekenreeks zijn (""). |
| **Sleutel** |Vereist |Tekenreeks |**Sleutel** om **de bronwaarde** mee te vergelijken. |
| **Waarde** |Vereist |Tekenreeks |Vervangingswaarde voor de **bron** die overeenkomt met de sleutel. |

---
### <a name="tolower"></a>ToLower
**Functie:**<br> ToLower (bron, cultuur)

**Beschrijving:**<br> Hiermee wordt een *brontekenreekswaarde* gebruikt en wordt deze omgezet in kleine letters met behulp van de opgegeven cultuurregels. Als er geen *cultuurinfo* wordt gespecificeerd, dan zal het invariantcultuur gebruiken.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Meestal naam van het kenmerk van het bronobject |
| **Cultuur** |Optioneel |Tekenreeks |Het formaat voor de cultuurnaam op basis van RFC 4646 is *languagecode2-country/regioncode2*, waarbij *languagecode2* de taalcode van twee letters is en *land/regioncode2* de subcultuurcode van twee letters. Voorbeelden hiervan zijn ja-JP voor Japans (Japan) en en-VS voor Engels (Verenigde Staten). In gevallen waarin een taalcode van twee letters niet beschikbaar is, wordt een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---
### <a name="toupper"></a>Toupper
**Functie:**<br> ToUpper(bron, cultuur)

**Beschrijving:**<br> Hiermee wordt een *brontekenreekswaarde* gebruikt en wordt deze omgezet in hoofdletters met behulp van de opgegeven cultuurregels. Als er geen *cultuurinfo* wordt gespecificeerd, dan zal het invariantcultuur gebruiken.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Vereist |Tekenreeks |Meestal de naam van het kenmerk van het bronobject. |
| **Cultuur** |Optioneel |Tekenreeks |Het formaat voor de cultuurnaam op basis van RFC 4646 is *languagecode2-country/regioncode2*, waarbij *languagecode2* de taalcode van twee letters is en *land/regioncode2* de subcultuurcode van twee letters. Voorbeelden hiervan zijn ja-JP voor Japans (Japan) en en-VS voor Engels (Verenigde Staten). In gevallen waarin een taalcode van twee letters niet beschikbaar is, wordt een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---
### <a name="word"></a>Word
**Functie:**<br> Word(Tekenreeks, Woordnummer, scheidingstekens)

**Beschrijving:**<br> De functie Word retourneert een woord in een tekenreeks, op basis van parameters die de te gebruiken scheidingstekens beschrijven en het woordnummer dat moet worden geretourneerd. Elke reeks tekens in tekenreeks die wordt gescheiden door een van de tekens in de limiters, wordt aangeduid als woorden:

Als getal < 1, wordt de lege tekenreeks geretourneerd.
Als tekenreeks null is, retourneert lege tekenreeks.
Als tekenreeks minder dan getalwoorden bevat of als tekenreeks geen woorden bevat die door de limiters zijn geïdentificeerd, wordt een lege tekenreeks geretourneerd.

**Parameters:**<br> 

| Naam | Vereist/ Herhalen | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Tekenreeks** |Vereist |Kenmerk met meerdere waarden |Tekenreeks om een woord terug te geven van.|
| **WordGetal** |Vereist | Geheel getal | Nummer dat aankaart welk woordnummer moet worden retournerd|
| **Scheidingstekens** |Vereist |Tekenreeks| Een tekenreeks die de scheidingsteken(s) weergeeft die moet worden gebruikt om woorden te identificeren|

**Voorbeeld:**<br>
Woord("De snelle bruine vos",3," ")                                                                                       
Retourneert 'bruin'

Woord("Dit,string!heeft veel scheidingstekens&", 3,",!&#")                                                                       
Retouren "heeft"

---

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

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Stroommailwaarde als niet NULL, anders stromen userPrincipalName
U wilt het e-mailkenmerk doorsturen als deze aanwezig is. Als dit niet het is, wilt u in plaats daarvan de waarde van userPrincipalName doorstromen.

**Expressie:** <br>
`Coalesce([mail],[userPrincipalName])`

**Voorbeeldinvoer/-uitvoer:** <br>

* **INVOER** (e-mail): NULL
* **INVOER** (userPrincipalName):John.Doe@contoso.com"
* **OUTPUT**:John.Doe@contoso.com"

## <a name="related-articles"></a>Verwante artikelen
* [Gebruikersvoorziening/deprovisioning automatiseren naar SaaS-apps](../app-provisioning/user-provisioning.md)
* [Kenmerkentoewijzingen aanpassen voor gebruikersinrichting](../app-provisioning/customize-application-attributes.md)
* [Scoping-filters voor gebruikersinrichting](define-conditional-rules-for-provisioning-user-accounts.md)
* [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](../app-provisioning/use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)
* [Meldingen over accountinrichting](../app-provisioning/user-provisioning.md)
* [Lijst met handleidingen voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
