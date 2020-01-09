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
ms.openlocfilehash: 93b8387d4453a3d83bcce55c739548d914545f2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430071"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Expressies schrijven voor kenmerktoewijzingen in Azure Active Directory
Bij het configureren van inrichting tot een SaaS-toepassing, is een van de typen kenmerktoewijzingen die u kunt opgeven een expressie-toewijzing. Voor deze, moet u een script-achtige-expressie waarmee u uw gebruikers om gegevens te transformeren naar indelingen die meer geschikt is voor de SaaS-toepassing kunt schrijven.

## <a name="syntax-overview"></a>Overzicht van de syntaxis
De syntaxis voor expressies voor kenmerktoewijzingen is doet denken aan van Visual Basic voor toepassingen (VBA)-functies.

* De volledige expressie moet worden gedefinieerd in termen van functies, die bestaan uit een naam, gevolgd door argumenten tussen haakjes: <br>
  *Functie naam (`<<argument 1>>`,`<<argument N>>`)*
* Functies in elkaar kan worden genest. Bijvoorbeeld: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* U kunt drie verschillende typen argumenten doorgeven in functies:
  
  1. Kenmerken moeten tussen rechte haakjes worden geplaatst. Bijvoorbeeld: [attributeName]
  2. Tekenreeksconstanten moeten tussen dubbele aanhalingstekens worden geplaatst. Bijvoorbeeld: "VS"
  3. Andere functies. Bijvoorbeeld: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Voor tekenreeksconstanten, als u een backslash (\) of een aanhalingsteken (") in de tekenreeks, moet moet deze worden voorafgegaan door het symbool backslash (\). Bijvoorbeeld: "bedrijfs naam: \\" Contoso\\""

## <a name="list-of-functions"></a>Lijst met functies
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Toevoegen
**Functie:**<br> Append(Source, suffix)

**Beschrijving:**<br> Neemt een tekenreekswaarde bron en het achtervoegsel toegevoegd aan het einde van deze.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject. |
| **suffix** |Verplicht |Tekenreeks |De tekenreeks die u wilt toevoegen aan het einde van de bronwaarde. |

---
### <a name="bitand"></a>BitAnd
**Functie:**<br> BitAnd (waarde1, waarde2)

**Beschrijving:**<br> Deze functie converteert beide para meters naar de binaire weer gave en stelt een bit in op:

0: als een of beide van de corresponderende bits in waarde1 en Value2 0 zijn                                                  
1: als beide corresponderende bits 1 zijn.                                    

Met andere woorden: het retourneert 0 in alle gevallen, behalve wanneer de overeenkomstige bits van beide para meters 1 zijn.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Value1** |Verplicht |num |Numerieke waarde die moet worden AND'ed met Value2|
| **enzovoort** |Verplicht |num |Numerieke waarde die moet worden AND'ed met waarde1|

**Voorbeeld:**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 en 00000111 = 00000111 so BitAnd retourneert 7, de binaire waarde van 00000111

---
### <a name="cbool"></a>CBool
**Functie:**<br> CBool (expr)

**Beschrijving:**<br> CBool retourneert een Booleaanse waarde op basis van de geëvalueerde expressie. Als de expressie resulteert in een waarde die niet gelijk is aan nul, retourneert CBool True, anders wordt false geretourneerd.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expressie** |Verplicht | expression | Een geldige expressie |

**Voorbeeld:**<br>
CBool ([attribute1] = [attribute2])                                                                    
Retourneert waar als beide kenmerken dezelfde waarde hebben.

---
### <a name="coalesce"></a>Coalesce
**Functie:**<br> Coalesce (source1, source2,..., defaultValue)

**Beschrijving:**<br> Retourneert de eerste bron waarde die niet NULL is. Als alle argumenten NULL zijn en defaultValue aanwezig is, wordt de defaultValue geretourneerd. Als alle argumenten NULL zijn en defaultValue niet aanwezig is, retourneert Coalesce NULL.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **bron1... bronN** | Verplicht | Tekenreeks |Vereist, variabele-aantal keren. Doorgaans de naam van het kenmerk van het bronobject. |
| **defaultValue** | Optioneel | Tekenreeks | De standaard waarde die moet worden gebruikt wanneer alle bron waarden NULL zijn. Lege tekenreeks ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Functie:**<br> ConvertToBase64 (bron)

**Beschrijving:**<br> De functie ConvertToBase64 converteert een teken reeks naar een Unicode base64-teken reeks.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |De teken reeks die moet worden geconverteerd naar basis 64|

**Voorbeeld:**<br>
ConvertToBase64 ("Hallo wereld!")                                                                                                        
Retourneert "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Functie:**<br> ConvertToUTF8Hex (bron)

**Beschrijving:**<br> De functie ConvertToUTF8Hex converteert een teken reeks naar een UTF8 hexadecimale waarde.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |Teken reeks die moet worden geconverteerd naar UTF8 hex|

**Voorbeeld:**<br>
ConvertToUTF8Hex ("Hallo wereld!")                                                                                                         
Retourneert 48656C6C6F20776F726C6421

---
### <a name="count"></a>Aantal
**Functie:**<br> Count (kenmerk)

**Beschrijving:**<br> De functie Count retourneert het aantal elementen in een kenmerk met meerdere waarden

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **geschreven** |Verplicht |-kenmerk |Kenmerk met meerdere waarden waarvoor elementen worden geteld|

---
### <a name="cstr"></a>CStr
**Functie:**<br> CStr (waarde)

**Beschrijving:**<br> De functie CStr zet een waarde om in een teken reeks gegevens type.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **value** |Verplicht | Numeriek, verwijzing of Booleaanse waarde | Dit kan een numerieke waarde, een referentie kenmerk of een Boolean zijn. |

**Voorbeeld:**<br>
CStr ([DN])                                                            
Retourneert "CN = Joe, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Functie:**<br> DateFromNum (waarde)

**Beschrijving:**<br> Met de functie DateFromNum wordt een waarde in de datum notatie van AD geconverteerd naar een DateTime-type.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **value** |Verplicht | Datum | De AD-datum die moet worden geconverteerd naar een DateTime-type |

**Voorbeeld:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Retourneert een datum/tijd die 2012-01-01 23:00:00 vertegenwoordigt

---
### <a name="formatdatetime"></a>formatDateTime
**Functie:**<br> FormatDateTime (bron, inputFormat, uitvoerindeling)

**Beschrijving:**<br> Neemt een tekenreeks met datum van de ene indeling en converteert naar een andere indeling.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject. |
| **inputFormat** |Verplicht |Tekenreeks |De verwachte notatie van de bronwaarde. Zie voor ondersteunde indelingen [ https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **Uitvoerindeling** |Verplicht |Tekenreeks |Indeling van de uitvoerdatum. |

---
### <a name="guid"></a>GUID
**Functie:**<br> GUID ()

**Beschrijving:**<br> De functie-GUID genereert een nieuwe wille keurige GUID

---
### <a name="instr"></a>InStr
**Functie:**<br> InStr (waarde1, waarde2, begin, compareType)

**Beschrijving:**<br> De functie InStr zoekt het eerste exemplaar van een subtekenreeks in een teken reeks

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Value1** |Verplicht |Tekenreeks |Teken reeks die moet worden doorzocht |
| **enzovoort** |Verplicht |Tekenreeks |Teken reeks die moet worden gevonden |
| **start** |Optioneel |Geheel getal |Begin positie om de subtekenreeks te vinden|
| **compareType** |Optioneel |Enum |Kan vbTextCompare of vbBinaryCompare zijn |

**Voorbeeld:**<br>
InStr ("Quick Brown Fox", "snel")                                                                             
Evalues tot 5

InStr (' herhaald ', ' e ', 3, vbBinaryCompare)                                                                                  
Evalueert tot 7

---
### <a name="isnull"></a>IsNull
**Functie:**<br> IsNull (expressie)

**Beschrijving:**<br> Als de expressie resulteert in null, retourneert de functie IsNull de waarde True. Voor een kenmerk wordt een null-waarde uitgedrukt door het ontbreken van het kenmerk.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expressie** |Verplicht |expression |Expressie die moet worden geëvalueerd |

**Voorbeeld:**<br>
IsNull ([displayName])                                                                                                
Retourneert waar als het kenmerk niet aanwezig is

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Functie:**<br> IsNullOrEmpty (expr)

**Beschrijving:**<br> Als de expressie Null of een lege teken reeks is, retourneert de functie IsNullOrEmpty ' True '. Voor een-kenmerk resulteert dit in waar als het kenmerk ontbreekt of aanwezig is, maar een lege teken reeks is.
De inverse van deze functie heeft de naam IsPresent.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expressie** |Verplicht |expression |Expressie die moet worden geëvalueerd |

**Voorbeeld:**<br>
IsNullOrEmpty ([displayName])                                               
Retourneert waar als het kenmerk niet aanwezig is of een lege teken reeks is

---
### <a name="ispresent"></a>IsPresent
**Functie:**<br> IsNullOrEmpty (expr)

**Beschrijving:**<br> Als de expressie resulteert in een teken reeks die niet null en niet leeg is, retourneert de functie IsPresent de waarde True. De inverse van deze functie heeft de naam IsNullOrEmpty.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expressie** |Verplicht |expression |Expressie die moet worden geëvalueerd |

**Voorbeeld:**<br>
Switch (IsPresent ([directManager]), [directManager], IsPresent ([skiplevelManager]), [skiplevelManager], IsPresent ([Director]), [Director])

---
### <a name="isstring"></a>IsString
**Functie:**<br> IsString (expr)

**Beschrijving:**<br> Als de expressie kan worden geëvalueerd als een teken reeks type, wordt de functie IsString geëvalueerd als True.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **expressie** |Verplicht |expression |Expressie die moet worden geëvalueerd |

---
### <a name="item"></a>Item
**Functie:**<br> Item (kenmerk, index)

**Beschrijving:**<br> De functie item retourneert één item uit een teken reeks/kenmerk met meerdere waarden.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **geschreven** |Verplicht |Kenmerk |Kenmerk met meerdere waarden dat moet worden doorzocht |
| **index** |Verplicht |Geheel getal | Index naar een item in de teken reeks met meerdere waarden|

**Voorbeeld:**<br>
Item ([proxyAddresses], 1)

---
### <a name="join"></a>Koppelen
**Functie:**<br> Deelnemen aan (scheidingsteken, bron1, bron2,...)

**Beschrijving:**<br> Join() is vergelijkbaar met Append(), behalve dat deze meerdere kunt combineren **bron** tekenreeks waarden in één tekenreeks en elke waarde wordt gescheiden door een **scheidingsteken** tekenreeks.

Als een van de bron waarden een kenmerk met meerdere waarden is, wordt elke waarde in dat kenmerk samengevoegd, gescheiden door de waarde voor het scheidings teken.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **separator** |Verplicht |Tekenreeks |De tekenreeks die wordt gebruikt om de bronwaarden scheiden wanneer ze worden samengevoegd tot één tekenreeks. Kan ' ' als er geen scheidingsteken vereist is. |
| **bron1... bronN** |Vereist, variabele-aantal keren |Tekenreeks |De tekenreeks die waarden die moeten worden samengevoegd. |

---
### <a name="left"></a>Links
**Functie:**<br> Left (teken reeks, NumChars)

**Beschrijving:**<br> De functie Left retourneert een opgegeven aantal tekens vanaf de linkerkant van een teken reeks. Als numChars = 0, retourneert u een lege teken reeks.
Als numChars < 0, wordt de invoer teken reeks geretourneerd.
Als teken reeks null is, retourneert een lege teken reeks.
Als teken reeks minder tekens bevat dan het getal dat is opgegeven in numChars, wordt een teken reeks geretourneerd die identiek is aan de teken reeks (dat wil zeggen, met alle tekens in para meter 1).

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **String** |Verplicht |Kenmerk | De teken reeks waaruit tekens moeten worden opgehaald |
| **NumChars** |Verplicht |Geheel getal | Een getal waarmee het aantal tekens wordt aangegeven dat moet worden geretourneerd vanaf het begin (links) van de teken reeks|

**Voorbeeld:**<br>
Left ("John splinter", 3)                                                            
Retourneert "Joh"

---
### <a name="mid"></a>Mid
**Functie:**<br> Mid (bron, start, lengte)

**Beschrijving:**<br> Retourneert een subtekenreeks van de bronwaarde. Een subtekenreeks is een tekenreeks zijn met slechts enkele van de tekens uit de brontekenreeks.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |Doorgaans de naam van het kenmerk. |
| **start** |Verplicht |geheel getal |Indexeren de **bron** tekenreeks waar de subtekenreeks moet beginnen. Het eerste teken in de tekenreeks index 1 hebben, tweede teken wordt index 2 hebben, enzovoort. |
| **Lengte** |Verplicht |geheel getal |De lengte van de subtekenreeks. Als de lengte buiten eindigt de **bron** tekenreeks, functie retourneert de subtekenreeks uit **start** index tot het einde van **bron** tekenreeks. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Functie:**<br> NormalizeDiacritics(source)

**Beschrijving:**<br> Vereist een tekenreeksargument. Retourneert de tekenreeks, maar met diakritische tekens vervangen door gelijkwaardige niet-diakritische tekens. Doorgaans gebruikt voor het omzetten van namen van de eerste en laatste diakritische tekens bevatten (accenttekens) in de geldige waarden die kunnen worden gebruikt in verschillende gebruikers-id, zoals de UPN-namen, SAM-accountnamen en e-mailadressen.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks | Meestal een voor naam-of achternaam-kenmerk. |

---
### <a name="not"></a>Not
**Functie:**<br> NOT(Source)

**Beschrijving:**<br> Draait u de Booleaanse waarde van de **bron**. Als **bron** waarde is '*waar*", retourneert '*False*'. Anders retourneert '*waar*'.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Booleaanse tekenreeks |Verwachte **bron** waarden zijn ' True ' of ' false '. |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Functie:**<br> RemoveDuplicates (kenmerk)

**Beschrijving:**<br> De functie RemoveDuplicates gebruikt een teken reeks met meerdere waarden en zorg ervoor dat elke waarde uniek is.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **geschreven** |Verplicht |Kenmerk met meerdere waarden |Kenmerk met meerdere waarden waarvoor duplicaten worden verwijderd|

**Voorbeeld:**<br>
RemoveDuplicates ([proxyAddresses])                                                                                                       
Retourneert een gezuiverd proxyAddress attribuut-kenmerk waarbij alle dubbele waarden zijn verwijderd

---
### <a name="replace"></a>Vervangen
**Functie:**<br> Vervang (bron, oldValue, regexPattern, regexGroupName, vervangende waarde, replacementAttributeName, sjabloon)

**Beschrijving:**<br>
Vervangt waarden binnen een tekenreeks. Het werkt anders, afhankelijk van de opgegeven parameters:

* Wanneer **oldValue** en **vervangende waarde** vindt:
  
  * Vervangt alle exemplaren van **oldValue** in de **bron** door **replacementValue**
* Wanneer **oldValue** en **sjabloon** vindt:
  
  * Vervangt alle instanties van de **oldValue** in de **sjabloon** met de **bron** waarde
* Wanneer **regexPattern** en **replacementValue** worden gegeven:

  * De functie past de **regexPattern** toe op de **bron** teken reeks en u kunt de regex-groeps namen gebruiken om de teken reeks voor **replacementValue** te maken
* Wanneer **regexPattern**, **regexGroupName**, **vervangende waarde** vindt:
  
  * De functie past de **regexPattern** toe op de **bron** teken reeks en vervangt alle waarden die overeenkomen met **regexGroupName** met **replacementValue**
* Als **regexPattern**, **regexGroupName**, **replacementAttributeName** worden gegeven:
  
  * Als **bron** heeft geen waarde **bron** wordt geretourneerd
  * Als de **bron** een waarde heeft, past de functie **de regexPattern** toe op de **bron** teken reeks en worden alle waarden die overeenkomen met **regexGroupName** vervangen door de waarde die is gekoppeld aan **replacementAttributeName**

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |Doorgaans naam van het kenmerk van het **bron** object. |
| **oldValue** |Optioneel |Tekenreeks |Waarde die moet worden vervangen **bron** of **sjabloon**. |
| **regexPattern** |Optioneel |Tekenreeks |Regex-patroon voor de waarde die moet worden vervangen **bron**. Of, wanneer **replacementPropertyName** wordt gebruikt, patroon om waarde uit **replacementPropertyName**te halen. |
| **regexGroupName** |Optioneel |Tekenreeks |Naam van de groep binnen **regexPattern**. Alleen wanneer **replacementPropertyName** wordt gebruikt, wordt de waarde van deze groep geëxtraheerd als **replacementValue** van **replacementPropertyName**. |
| **replacementValue** |Optioneel |Tekenreeks |Nieuwe waarde te vervangen door oude met. |
| **replacementAttributeName** |Optioneel |Tekenreeks |Naam van het kenmerk dat moet worden gebruikt voor de vervangings waarde |
| **sjabloon** |Optioneel |Tekenreeks |Als u een **sjabloon** waarde opgeeft, worden de **oude** waarden in de sjabloon gezocht en vervangen door de **bron** waarde. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Functie:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Beschrijving:**<br> Minimaal twee argumenten die zijn gedefinieerd met behulp van expressies aanmaakregels voor unieke waarde is vereist. De functie evalueert van elke regel en controleert vervolgens of de waarde uniek in de doel-app/directory gegenereerd. De eerste unieke waarde gevonden, worden de geretourneerd. Als alle waarden al in het doel bestaat, wordt de vermelding ophalen verwekt en de reden wordt vastgelegd in de auditlogboeken. Er is geen bovengrens voor het aantal argumenten die kan worden opgegeven.

> [!NOTE]
> - Dit is een functie op het hoogste niveau, kunnen niet worden genest.
> - Deze functie kan niet worden toegepast op kenmerken met een overeenkomende prioriteit.  
> - Deze functie is alleen bedoeld om te worden gebruikt voor bewerkingen voor het item maken. Wanneer u deze met een kenmerk, stel de **toepassen toewijzing** eigenschap **alleen tijdens het maken van databaseobject**.
> - Deze functie wordt momenteel alleen ondersteund voor werk dagen Active Directory het inrichten van de gebruiker. Het kan niet worden gebruikt met andere inrichtings toepassingen. 


**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **uniqueValueRule1... uniqueValueRuleN** |Ten minste zijn 2 afhankelijk van de vereiste, geen hoofdletters |Tekenreeks | Lijst met regels voor het genereren van unieke waarden om te evalueren. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Functie:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Beschrijving:**<br> Retourneert één appRoleAssignment uit de lijst met alle appRoleAssignments die zijn toegewezen aan een gebruiker voor een bepaalde toepassing. Deze functie is vereist om het appRoleAssignments-object om te zetten in een teken reeks met een enkele rolnaam. Houd er rekening mee dat de best practice ervoor moet zorgen dat er slechts één appRoleAssignment wordt toegewezen aan één gebruiker tegelijk. als er meerdere rollen zijn toegewezen, kan de geretourneerde functie teken reeks mogelijk niet voorspelbaar zijn. 

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Verplicht |Tekenreeks |**[appRoleAssignments]**  object. |

---
### <a name="split"></a>Splitsen
**Functie:**<br> Splitsen (bron, scheidings teken)

**Beschrijving:**<br> Hiermee splitst u een teken reeks in een matrix met meerdere waarden met behulp van het opgegeven scheidings teken.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |**bron** waarde om bij te werken. |
| **delimiter** |Verplicht |Tekenreeks |Hiermee geeft u het teken op dat wordt gebruikt om de teken reeks te splitsen (bijvoorbeeld: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Functie:**<br> StripSpaces(source)

**Beschrijving:**<br> Verwijdert alle spaties ("") tekens uit de brontekenreeks.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |**bron** waarde om bij te werken. |

---
### <a name="switch"></a>Switch
**Functie:**<br> Switch (bron, defaultValue, key1, value1, key2, waarde2,...)

**Beschrijving:**<br> Wanneer **bron** waarde komt overeen met een **sleutel**, retourneert **waarde** voor die **sleutel**. Als **bron** waarde komt niet overeen met alle sleutels, retourneert **defaultValue**.  **Sleutel** en **waarde** parameters moeten altijd zijn verkrijgbaar in paren. De functie verwacht altijd een even aantal parameters. De functie mag niet worden gebruikt voor referentiële kenmerken, zoals Manager. 

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |**bron** waarde om bij te werken. |
| **defaultValue** |Optioneel |Tekenreeks |De standaardwaarde moet worden gebruikt wanneer de bron komt niet overeen met alle sleutels. Lege tekenreeks (""). |
| **sleutel** |Verplicht |Tekenreeks |**Sleutel** vergelijken **bron** met de waarde. |
| **value** |Verplicht |Tekenreeks |Vervangende waarde voor de **bron** die overeenkomt met de sleutel. |

---
### <a name="tolower"></a>ToLower
**Functie:**<br> ToLower (bron, cultuur)

**Beschrijving:**<br> Neemt een *bron* teken reeks waarde en converteert deze in kleine letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject |
| **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Functie:**<br> ToUpper (bron, cultuur)

**Beschrijving:**<br> Neemt een *bron* teken reeks waarde en converteert deze in hoofd letters met de opgegeven cultuur regels. Als er geen *cultuur* gegevens zijn opgegeven, wordt de invariante cultuur gebruikt.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **Bron** |Verplicht |Tekenreeks |Doorgaans de naam van het kenmerk van het bronobject. |
| **culturele** |Optioneel |Tekenreeks |De notatie voor de cultuur naam op basis van RFC 4646 is *languagecode2-Country/regioncode2*, waarbij *languagecode2* de taal code van twee letters is en *land/regioncode2* de subcultuurcode van twee letters is. Voor beelden zijn ja-JP voor Japans (Japan) en en-US voor Engels (Verenigde Staten). In gevallen waarin een taal code van twee letters niet beschikbaar is, wordt er een code van drie letters gebruikt die is afgeleid van ISO 639-2.|

---
### <a name="word"></a>Word
**Functie:**<br> Woord (teken reeks, WordNumber, scheidings tekens)

**Beschrijving:**<br> De functie Word retourneert een woord dat deel uitmaakt van een teken reeks, op basis van para meters die de scheidings tekens beschrijven die moeten worden gebruikt en het woord nummer dat moet worden geretourneerd. Elke teken reeks in een teken reeks gescheiden door de tekens van de spaties wordt aangeduid als woorden:

Als getal < 1 retourneert een lege teken reeks.
Als de teken reeks null is, wordt een lege teken reeks geretourneerd.
Als teken reeks minder dan cijfer woorden bevat, of teken reeks geen woorden die zijn geïdentificeerd door scheidings tekens, wordt een lege teken reeks geretourneerd.

**Parameters:**<br> 

| Name | Vereiste / herhalende | Type | Opmerkingen |
| --- | --- | --- | --- |
| **String** |Verplicht |Kenmerk met meerdere waarden |De teken reeks waarmee een woord moet worden geretourneerd.|
| **WordNumber** |Verplicht | Geheel getal | Nummer waarmee het woord nummer moet worden geretourneerd|
| **scheidings tekens** |Verplicht |Tekenreeks| Een teken reeks die de scheidings tekens vertegenwoordigt die moeten worden gebruikt voor het identificeren van woorden|

**Voorbeeld:**<br>
Word ("Quick Brown Fox", 3, "")                                                                                       
Retourneert ' bruin '

Word ("This, string! heeft & veel scheidings tekens", 3, ",! & #")                                                                       
Retourneert "has"

---

## <a name="examples"></a>Voorbeelden
### <a name="strip-known-domain-name"></a>Bekende domeinnaam van strook/lijn
U moet de domeinnaam van een bekend van e-mailadres van een gebruiker om op te halen van de naam van een gebruiker verwijderen. <br>
Bijvoorbeeld, als het domein 'contoso.com' is, kan u de volgende expressie:

**Expressie:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Voorbeeld van invoer / uitvoer:** <br>

* **INVOER** (e-mail): "john.doe@contoso.com"
* **UITVOER**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Constante achtervoegsel toevoegen aan de gebruikersnaam van
Als u van een Sandbox met Salesforce gebruikmaakt, moet u mogelijk een extra achtervoegsel toevoegen aan uw gebruikersnamen voordat deze worden gesynchroniseerd.

**Expressie:** <br>
`Append([userPrincipalName], ".test")`

**Voorbeeld van invoer/uitvoer:** <br>

* **INVOER**: (userPrincipalName): "John.Doe@contoso.com"
* **UITVOER**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gebruikersalias genereren door samenvoegen van onderdelen van de voornaam en achternaam
U moet een gebruiker alias genereren door te nemen van de eerste 3 letters van de voornaam van de gebruiker en eerste 5 letters van de achternaam van de gebruiker.

**Expressie:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Voorbeeld van invoer/uitvoer:** <br>

* **INVOER** (givenName): "John"
* **INVOER** (voornaam): "Doe"
* **UITVOER**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Diakritische tekens verwijderen uit een tekenreeks
U moet vervangen van tekens lang zijn accenttekens met gelijkwaardige tekens die geen accenttekens bevatten.

**Expressie:** <br>
NormalizeDiacritics([givenName])

**Voorbeeld van invoer/uitvoer:** <br>

* **INVOER** (givenName): "Zoë"
* **UITVOER**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Een teken reeks splitsen in een matrix met meerdere waarden
U moet een door komma's gescheiden lijst met teken reeksen maken en deze opsplitsen in een matrix die kan worden aangesloten op een kenmerk met meerdere waarden, zoals het kenmerk PermissionSets van Sales Force. In dit voor beeld is een lijst met machtigingen sets ingevuld in extensionAttribute5 in azure AD.

**Expressie:** <br>
Splitsen ([extensionAttribute5], ",")

**Voorbeeld van invoer/uitvoer:** <br>

* **Invoer** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Output**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Uitvoerdatum als een tekenreeks in een bepaalde indeling
Wilt u datums verzenden naar een SaaS-toepassing in een bepaalde indeling. <br>
U wilt bijvoorbeeld datums voor ServiceNow.

**Expressie:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Voorbeeld van invoer/uitvoer:**

* **INVOER** (extensionAttribute1): "20150123105347.1Z"
* **UITVOER**: "23-01-2015"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Vervangen door een waarde op basis van vooraf gedefinieerde set opties

U moet voor het definiëren van de tijdzone van de gebruiker op basis van de status opgeslagen in Azure AD. <br>
Als de status code komt niet overeen met een van de vooraf gedefinieerde opties, gebruikt u standaardwaarde van 'Australië/Sydney'.

**Expressie:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Voorbeeld van invoer/uitvoer:**

* **INVOER** (status): "QLD"
* **UITVOER**: "Australië/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Tekens vervangen met een reguliere expressie
U moet tekens vinden die overeenkomen met een reguliere expressie waarde en deze verwijderen.

**Expressie:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Voorbeeld van invoer/uitvoer:**

* **Invoer** (mailnickname: "john_doe72"
* **Uitvoer**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Gegenereerde userPrincipalName-waarde converteren naar kleine letters
In het onderstaande voor beeld wordt de UPN-waarde gegenereerd door het samen voegen van de bron velden PreferredFirstName en PreferredLastName en de functie ToLower werkt op de gegenereerde teken reeks om alle tekens te converteren naar kleine letters. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Voorbeeld van invoer/uitvoer:**

* **INVOER** (PreferredFirstName): "John"
* **INVOER** (PreferredLastName): 'Smith'
* **Uitvoer**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Genereren van unieke waarde voor kenmerk userPrincipalName (UPN)
Gebaseerd op van de gebruiker voornaam, de tweede voornaam en achternaam, moet u het genereren van een waarde op voor het UPN-kenmerk en controleer de uniek in de-AD-doeldirectory voordat u de waarde toewijzen aan het UPN-kenmerk.

**Expressie:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Voorbeeld van invoer/uitvoer:**

* **INVOER** (PreferredFirstName): "John"
* **INVOER** (PreferredLastName): 'Smith'
* **UITVOER**: "John.Smith@contoso.com' als UPN-waarde van John.Smith@contoso.com nog niet bestaat in de map
* **UITVOER**: "J.Smith@contoso.com' als UPN-waarde van John.Smith@contoso.com al bestaat in de map
* **UITVOER**: "Jo.Smith@contoso.com' als de bovenstaande twee UPN-waarden al in de map bestaat

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Stroom-mail waarde indien niet NULL, anders flow userPrincipalName
U wilt het kenmerk mail stroomren als het aanwezig is. Als dat niet het geval is, wilt u in plaats daarvan de waarde van userPrincipalName door lopen.

**Expressie:** <br>
`Coalesce([mail],[userPrincipalName])`

**Voorbeeld van invoer/uitvoer:** <br>

* **Invoer** (mail): null
* **Invoer** (userPrincipalName): "John.Doe@contoso.com"
* **UITVOER**: "John.Doe@contoso.com"

## <a name="related-articles"></a>Gerelateerde artikelen
* [Gebruiker inrichting/ongedaan maken van inrichting voor SaaS-toepassingen automatiseren](user-provisioning.md)
* [Kenmerktoewijzingen voor het inrichten van gebruikers aan te passen](customize-application-attributes.md)
* [Bereikfilters toevoegen voor het inrichten van gebruikers](define-conditional-rules-for-provisioning-user-accounts.md)
* [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)
* [Meldingen over accountinrichting](user-provisioning.md)
* [Lijst met zelfstudies over het integreren van SaaS-Apps](../saas-apps/tutorial-list.md)
