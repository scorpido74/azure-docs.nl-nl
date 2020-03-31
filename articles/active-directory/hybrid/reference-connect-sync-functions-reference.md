---
title: 'Azure AD Connect-synchronisatie: naslaginformatie over functies | Microsoft Documenten'
description: Referentie voor declaratieve inrichtingsexpressies in Azure AD Connect-synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c3102480e316c634930c356ae02f769767b7d08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900044"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Synchronisatie van Azure AD Connect: naslaginformatie over functies
In Azure AD Connect worden functies gebruikt om een kenmerkwaarde te manipuleren tijdens synchronisatie.  
De syntaxis van de functies wordt uitgedrukt met de volgende indeling:  
`<output type> FunctionName(<input type> <position name>, ..)`

Als een functie overbelast is en meerdere syntaxissen accepteert, worden alle geldige syntaxissen weergegeven.  
De functies zijn sterk getypt en ze controleren of het type doorgegeven in overeenkomt met het gedocumenteerde type.  
Als het type niet overeenkomt, wordt er een fout gegenereerd.

De typen worden uitgedrukt met de volgende syntaxis:

* **opslaglocatie** – Binair
* **bool** – Booleaan
* **dt** – UTC Datum/Tijd
* **enum** – Opsomming van bekende constanten
* **exp** – Expressie, die naar verwachting zal evalueren tot een Booleaanse
* **mvbin** – Multi-Valued Binary
* **mvstr** – Multi-Valued String
* **mvref** – Referentie met meerdere waarden
* **getal** – Numeriek
* **ref** – Referentie
* **str** – Tekenreeks
* **var** – Een variant van (bijna) elk ander type
* **leegte** – geeft geen waarde terug

De functies met de types **mvbin**, **mvstr**en **mvref** kunnen alleen werken op multi-gewaardeerde attributen. Functies met **opslaglocatie,** **str**en **ref** werken aan zowel enkelgewaardeerde als multigewaardeerde kenmerken.

## <a name="functions-reference"></a>Functieverwijzing

| Lijst met functies |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certificaat** | | | | |
| [CertExtensionOïden](#certextensionoids) |[CertFormat CertFormat CertFormat CertFormat](#certformat) |[CertFriendlyName CertFriendlyName CertFriendlyName CertFriendly](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer CertIssuer CertIssuer CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid CertIssuerOid CertIssuerOid](#certissueroid) |[CertKeyAlgoritme](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams CertKeyAlgorithmParams CertKeyAlgorithmParams CertKey](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore CertNotBefore CertNotBefore CertNot](#certnotbefore) | |
| [CertPublicKeyoid CertPublicKeyoid CertPublicKeyoid CertPublic](#certpublickeyoid) |[CertPublicKeyParametersOid CertPublicKeyParametersOid CertPublicKeyParametersOid CertPublic](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersie](#certversion) |[IsCert (IsCert)](#iscert) | | | |
| **Conversie** | | | | |
| [CBool CBool](#cbool) |[Cdate](#cdate) |[CGuid (CGuid)](#cguid) |[ConverterenFromBase64](#convertfrombase64) | |
| [Converttobase64](#converttobase64) |[ConvertfromUTF8Hex](#convertfromutf8hex) |[ConverttoUTF8Hex](#converttoutf8hex) |[CNum (CNum)](#cnum) | |
| [Cref](#cref) |[Cstr](#cstr) |[StringFromGuid (stringFromGuid)](#stringfromguid) |[StringFromSid (StringFromSid)](#stringfromsid) | |
| **Datum/tijd** | | | | |
| [Dateadd](#dateadd) |[DatumFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Nwo](#now) | |
| [NumFromDate NumfromDate](#numfromdate) | | | | |
| **Directory** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapedNComponent](#escapedncomponent) | | |
| **Evaluatie** | | | | |
| [IsBitSet (IsBitSet)](#isbitset) |[IsDate (IsDate)](#isdate) |[Isempty](#isempty) |[IsGuid IsGuid](#isguid) | |
| [Isnull](#isnull) |[IsnullorEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[Isaanwezig](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Berekeningen** | | | | |
| [BitAnd](#bitand) |[Bitor Bitor](#bitor) |[RandomNum](#randomnum) | | |
| **Multi-gewaardeerd** | | | | |
| [Bevat](#contains) |[Count](#count) |[Item](#item) |[ItemorNull](#itemornull) | |
| [Toevoegen](#join) |[Duplicaten verwijderen](#removeduplicates) |[Split](#split) | | |
| **Programmastroom** | | | | |
| [Fout](#error) |[Iif](#iif) |[Selecteren](#select) |[Switch](#switch) | |
| [Waar](#where) |[Met](#with) | | | |
| **Tekst** | | | | |
| [Guid](#guid) |[Instr](#instr) |[Instrrev](#instrrev) |[LCase](#lcase) | |
| [Links](#left) |[Len](#len) |[LTrim (LTrim)](#ltrim) |[Midden](#mid) | |
| [PadLinks](#padleft) |[PadRight (PadRight)](#padright) |[PCase (PCase)](#pcase) |[Vervangen](#replace) | |
| [Vervang Chars](#replacechars) |[Recht](#right) |[RTrim (RTrim)](#rtrim) |[Trim](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

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
`BitAnd(&HF, &HF7)`  
Geeft als resultaat 7 omdat hexadecimale "F" EN "F7" evalueren tot deze waarde.

---
### <a name="bitor"></a>Bitor Bitor
**Beschrijving:**  
De functie BitOr stelt opgegeven bits in op een waarde.

**Syntaxis:**  
`num BitOr(num value1, num value2)`

* value1, value2: numerieke waarden die samen MOETEN zijn OF'ed

**Opmerkingen:**  
Deze functie converteert beide parameters naar de binaire weergave en stelt een beetje in op 1 als een of beide van de bijbehorende bits in masker en vlag 1 zijn en naar 0 als beide van de overeenkomstige bits 0 zijn. Met andere woorden, het retourneert 1 in alle gevallen, behalve wanneer de bijbehorende bits van beide parameters 0 zijn.

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
### <a name="cdate"></a>Cdate
**Beschrijving:**  
De functie CDate retourneert een UTC DateTime van een tekenreeks. DateTime is geen native attribuuttype in Synchronisatie, maar wordt door sommige functies gebruikt.

**Syntaxis:**  
`dt CDate(str value)`

* Waarde: een tekenreeks met een datum, tijd en optioneel tijdzone

**Opmerkingen:**  
De geretourneerde tekenreeks bevindt zich altijd in UTC.

**Voorbeeld:**  
`CDate([employeeStartTime])`  
Retourneert een DateTime op basis van de begintijd van de werknemer

`CDate("2013-01-10 4:00 PM -8")`  
Geeft als resultaat een datumtijd die "2013-01-11 12:00 uur" vertegenwoordigt


---
### <a name="certextensionoids"></a>CertExtensionOïden
**Beschrijving:**  
Geeft als resultaat de Oid-waarden van alle kritieke extensies van een certificaatobject.

**Syntaxis:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certformat"></a>CertFormat CertFormat CertFormat CertFormat
**Beschrijving:**  
Geeft als resultaat de naam van de notatie van dit X.509v3-certificaat.

**Syntaxis:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certfriendlyname"></a>CertFriendlyName CertFriendlyName CertFriendlyName CertFriendly
**Beschrijving:**  
Retourneert de bijbehorende alias voor een certificaat.

**Syntaxis:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certhashstring"></a>CertHashString
**Beschrijving:**  
Geeft als resultaat de SHA1-hashwaarde voor het X.509v3-certificaat als een hexadecimale tekenreeks.

**Syntaxis:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certissuer"></a>CertIssuer CertIssuer CertIssuer CertIssuer
**Beschrijving:**  
Geeft als resultaat de naam van de certificaatautoriteit die het X.509v3-certificaat heeft afgegeven.

**Syntaxis:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Beschrijving:**  
Geeft als resultaat de voorname naam van de certificaatuitgever.

**Syntaxis:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certissueroid"></a>CertIssuerOid CertIssuerOid CertIssuerOid
**Beschrijving:**  
Geeft als resultaat de Oid van de certificaatuitgever.

**Syntaxis:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgoritme
**Beschrijving:**  
Geeft als resultaat de belangrijkste algoritmegegevens voor dit X.509v3-certificaat als tekenreeks.

**Syntaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams CertKeyAlgorithmParams CertKeyAlgorithmParams CertKey
**Beschrijving:**  
Geeft als resultaat de parameters van het sleutelalgoritme voor het X.509v3-certificaat als een hexadecimale tekenreeks.

**Syntaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certnameinfo"></a>CertNameInfo
**Beschrijving:**  
Geeft als resultaat de namen van het onderwerp en de uitgever van een certificaat.

**Syntaxis:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.
*   X509NameType: de x509NameType-waarde voor het onderwerp.
*   inclusiefIssuerName: trouw aan de naam van de uitgever; anders, vals.

---
### <a name="certnotafter"></a>CertNotAfter
**Beschrijving:**  
Geeft als resultaat de datum in lokale tijd waarna een certificaat niet langer geldig is.

**Syntaxis:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certnotbefore"></a>CertNotBefore CertNotBefore CertNotBefore CertNot
**Beschrijving:**  
Geeft als resultaat de datum in lokale tijd waarop een certificaat geldig wordt.

**Syntaxis:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certpublickeyoid"></a>CertPublicKeyoid CertPublicKeyoid CertPublicKeyoid CertPublic
**Beschrijving:**  
Geeft als resultaat de oid van de openbare sleutel voor het X.509v3-certificaat.

**Syntaxis:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid CertPublicKeyParametersOid CertPublicKeyParametersOid CertPublic
**Beschrijving:**  
Geeft als resultaat de oid van de parameters van de openbare sleutel voor het X.509v3-certificaat.

**Syntaxis:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Beschrijving:**  
Geeft als resultaat het serienummer van het X.509v3-certificaat.

**Syntaxis:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Beschrijving:**  
Retourneert de Oid van het algoritme dat wordt gebruikt om de handtekening van een certificaat te maken.

**Syntaxis:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certsubject"></a>CertSubject CertSubject
**Beschrijving:**  
Haalt de onderscheiden naam van een certificaat.

**Syntaxis:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Beschrijving:**  
Geeft als resultaat de voornaam van het onderwerp uit een certificaat.

**Syntaxis:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Beschrijving:**  
Retourneert de Oid van de onderwerpnaam van een certificaat.

**Syntaxis:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certthumbprint"></a>CertThumbprint
**Beschrijving:**  
Geeft als resultaat de duimafdruk van een certificaat.

**Syntaxis:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="certversion"></a>CertVersie
**Beschrijving:**  
Retourneert de X.509-versie van een certificaat.

**Syntaxis:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.

---
### <a name="cguid"></a>CGuid (CGuid)
**Beschrijving:**  
De functie CGuid zet de tekenreeksweergave van een GUID om in de binaire weergave.

**Syntaxis:**  
`bin CGuid(str GUID)`

* Een string geformatteerd in dit patroon: xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx of {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Contains
**Beschrijving:**  
Met de functie Bevat wordt een tekenreeks in een kenmerk met meerdere waarden gevonden

**Syntaxis:**  
`num Contains (mvstring attribute, str search)`- hoofdlettergevoelig  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)`- hoofdlettergevoelig

* kenmerk: het kenmerk met meerdere waarden om te zoeken.
* zoeken: tekenreeks om te vinden in het kenmerk.
* Casetype: CaseInsensitive of CaseSensitive.

Retourneert index in het kenmerk met meerdere waarden waar de tekenreeks is gevonden. 0 wordt geretourneerd als de tekenreeks niet wordt gevonden.

**Opmerkingen:**  
Voor tekenreekskenmerken met meerdere waarden worden subtekenreeksen in de waarden gevonden.  
Voor referentiekenmerken moet de gezochte tekenreeks exact overeenkomen met de waarde die als een overeenkomst moet worden beschouwd.

**Voorbeeld:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Als het kenmerk proxyAdressen een primair e-mailadres heeft (aangegeven in hoofdletters "SMTP:"), retourneert u vervolgens het kenmerk proxyAddress, anders wordt een fout retourneren.

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
### <a name="convertfromutf8hex"></a>ConvertfromUTF8Hex
**Beschrijving:**  
Met de functie ConvertFromUTF8Hex wordt de opgegeven UTF8 Hex-gecodeerde waarde omgezet in een tekenreeks.

**Syntaxis:**  
`str ConvertFromUTF8Hex(str source)`

* bron: UTF8 2-byte gecodeerde steek

**Opmerkingen:**  
Het verschil tussen deze functie en ConvertFromBase64([],UTF8) in die zin dat het resultaat is vriendelijk voor de DN attribuut.  
Deze indeling wordt door Azure Active Directory gebruikt als DN.

**Voorbeeld:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
Retourneert "*Hallo wereld!*"

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
### <a name="cnum"></a>CNum (CNum)
**Beschrijving:**  
De functie CNum neemt een tekenreeks en retourneert een numeriek gegevenstype.

**Syntaxis:**  
`num CNum(str value)`

---
### <a name="cref"></a>Cref
**Beschrijving:**  
Converteert een tekenreeks naar een verwijzingskenmerk

**Syntaxis:**  
`ref CRef(str value)`

**Voorbeeld:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

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
### <a name="dateadd"></a>DateAdd
**Beschrijving:**  
Retourneert een datum met een datum waaraan een opgegeven tijdsinterval is toegevoegd.

**Syntaxis:**  
`dt DateAdd(str interval, num value, dt date)`

* interval: tekenreeksexpressie die het tijdsinterval is dat u wilt toevoegen. De tekenreeks moet een van de volgende waarden hebben:
  * yyyy Jaar
  * q Kwartaal
  * m Maand
  * y Dag van het jaar
  * d Dag
  * w Weekdag
  * ww Week
  * h Uur
  * n Minuut
  * s Tweede
* waarde: het aantal eenheden dat u wilt toevoegen. Het kan positief zijn (om datums in de toekomst te krijgen) of negatief (om datums in het verleden te krijgen).
* datum: datum waarop datum wordt vertegenwoordigd waarop het interval wordt toegevoegd.

**Voorbeeld:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
Hiermee voegt u 3 maanden toe en retourneert een datumtijd die "2001-04-01" vertegenwoordigt.

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
### <a name="dncomponentrev"></a>DNComponentRev
**Beschrijving:**  
De functie DNComponentRev retourneert de waarde van een opgegeven DN-component die van rechts (het einde) gaat.

**Syntaxis:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: het referentiekenmerk dat moet worden geïnterpreteerd
* ComponentNumber - De component in de DN om terug te keren
* Opties: DC – Alle componenten negeren met 'dc='

**Voorbeeld:**  
Als dn is "cn=Joe, ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com" dan  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Beide keren terug ons.

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
### <a name="escapedncomponent"></a>EscapedNComponent
**Beschrijving:**  
De functie EscapeDNComponent neemt één component van een DN en ontsnapt eraan, zodat deze in LDAP kan worden weergegeven.

**Syntaxis:**  
`str EscapeDNComponent(str value)`

**Voorbeeld:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Zorgt ervoor dat het object kan worden gemaakt in een LDAP-map, zelfs als het kenmerk displayName tekens heeft die moeten worden ontsnapt in LDAP.

---
### <a name="formatdatetime"></a>FormatDateTime
**Beschrijving:**  
De functie FormatDateTime wordt gebruikt om een DateTime op te maken met een tekenreeks met een opgegeven indeling

**Syntaxis:**  
`str FormatDateTime(dt value, str format)`

* waarde: een waarde in de datumnotatie
* opmaak: een tekenreeks die de notatie weergeeft die moet worden geconverteerd.

**Opmerkingen:**  
De mogelijke waarden voor de notatie zijn hier te vinden: [Aangepaste datum- en tijdnotaties voor de functie OPMAAK](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Voorbeeld:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
Resultaten in "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
Kan resulteren in "20140905081453.0Z"

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
### <a name="instrrev"></a>Instrrev
**Beschrijving:**  
De functie InStrRev vindt de laatste gebeurtenis van een subtekenreeks in een tekenreeks

**Syntaxis:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: tekenreeks die moet worden doorzocht
* stringmatch: string te vinden
* start: startpositie om de subtekenreeks te vinden
* vergelijken: vbTextCompare of vbBinaryCompare

**Opmerkingen:**  
Geeft als resultaat de positie waar de subtekenreeks is gevonden of 0 als deze niet is gevonden.

**Voorbeeld:**  
`InStrRev("abbcdbbbef","bb")`  
Retourneert 7

---
### <a name="isbitset"></a>IsBitSet (IsBitSet)
**Beschrijving:**  
De functie IsBitSet-tests als een bit is ingesteld of niet

**Syntaxis:**  
`bool IsBitSet(num value, num flag)`

* waarde: een numerieke waarde die wordt geëvalueerd.flag: een numerieke waarde die de bit heeft die moet worden geëvalueerd

**Voorbeeld:**  
`IsBitSet(&HF,4)`  
Geeft als resultaat True omdat bit "4" is ingesteld in de hexadecimale waarde "F"

---
### <a name="isdate"></a>IsDate (IsDate)
**Beschrijving:**  
Als de expressie kan worden geëvalueerd als een DateTime-type, evalueert de functie IsDate naar True.

**Syntaxis:**  
`bool IsDate(var Expression)`

**Opmerkingen:**  
Wordt gebruikt om te bepalen of CDate() succesvol kan zijn.

---
### <a name="iscert"></a>IsCert (IsCert)
**Beschrijving:**  
Retourneert true als de ruwe gegevens kunnen worden geserialiseerd in het certificaatobject .NET X509Certificate2.

**Syntaxis:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Byte array representation of an X.509 certificate. De bytearray kan binair (DER) of Base64-gecodeerde X.509-gegevens zijn.
---
### <a name="isempty"></a>IsEmpty
**Beschrijving:**  
Als het kenmerk aanwezig is in de CS of MV, maar wordt geëvalueerd tot een lege tekenreeks, evalueert de functie IsEmpty naar True.

**Syntaxis:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid IsGuid
**Beschrijving:**  
Als de tekenreeks kan worden omgezet in een GUID, wordt de IsGuid-functie naar true geëvalueerd.

**Syntaxis:**  
`bool IsGuid(str GUID)`

**Opmerkingen:**  
Een GUID wordt gedefinieerd als een tekenreeks volgens een van deze patronen: xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx of {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx}

Wordt gebruikt om te bepalen of CGuid() succesvol kan zijn.

**Voorbeeld:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Als het StrAttribute een GUID-indeling heeft, geeft u een binaire weergave terug, anders wordt een Null retourneren.

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
### <a name="isnumeric"></a>IsNumeric
**Beschrijving:**  
De functie IsNumeriek retourneert een Booleaanse waarde die aangeeft of een expressie kan worden geëvalueerd als een getaltype.

**Syntaxis:**  
`bool IsNumeric(var Expression)`

**Opmerkingen:**  
Wordt gebruikt om te bepalen of CNum() succesvol kan zijn om de expressie te ontleden.

---
### <a name="isstring"></a>IsString
**Beschrijving:**  
Als de expressie kan worden geëvalueerd tot een tekenreekstype, evalueert de functie IsString naar True.

**Syntaxis:**  
`bool IsString(var expression)`

**Opmerkingen:**  
Wordt gebruikt om te bepalen of CStr() succesvol kan zijn om de expressie te ontleden.

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
### <a name="itemornull"></a>ItemorNull
**Beschrijving:**  
De functie ItemOrNull retourneert één item uit een tekenreeks met meerdere waarden/kenmerk.

**Syntaxis:**  
`var ItemOrNull(mvstr attribute, num index)`

* kenmerk: kenmerk met meerdere waarden
* index: index tot een item in de tekenreeks met meerdere waarden.

**Opmerkingen:**  
De functie ItemOrNull is nuttig samen met de functie Bevat, omdat de laatste functie de index retourneert naar een item in het kenmerk met meerdere waarden.

Als de index niet binnen de grenzen valt, geeft u een Null-waarde als resultaat.

---
### <a name="join"></a>Koppelen
**Beschrijving:**  
De functie Join neemt een tekenreeks met meerdere waarden en retourneert een tekenreeks met één waarde met opgegeven scheidingsteken die tussen elk item is ingevoegd.

**Syntaxis:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* kenmerk: kenmerk met meerdere waarden dat tekenreeksen bevat waaraan u moet worden samengevoegd.
* scheidingsteken: elke tekenreeks die wordt gebruikt om de subtekenreeksen in de geretourneerde tekenreeks te scheiden. Als het ruimteteken (" ") wordt weggelaten, wordt het gebruikt. Als Delimiter een tekenreeks met nullengte ("") of Niets is, worden alle items in de lijst samengevoegd zonder scheidingstekens.

**Opmerkingen**  
Er is pariteit tussen de functies Join en Split. De functie Join neemt een array van tekenreeksen en voegt deze samen met behulp van een tekenreeks om een enkele tekenreeks terug te geven. De functie Splitsen neemt een tekenreeks en scheidt deze bij de scheidingsteken om een reeks tekenreeksen terug te sturen. Een belangrijk verschil is echter dat Join tekenreeksen kan samenvoegen met elke delimitertekenreeks, Split kan alleen tekenreeksen scheiden met één tekenscheidingsteken.

**Voorbeeld:**  
`Join([proxyAddresses],",")`  
Kon terugkeren:SMTP:john.doe@contoso.comsmtp:jd@contoso.com" , "

---
### <a name="lcase"></a>LCase
**Beschrijving:**  
De functie LCase converteert alle tekens in een tekenreeks naar kleine letters.

**Syntaxis:**  
`str LCase(str value)`

**Voorbeeld:**  
`LCase("TeSt")`  
Retourneert "test".

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
Geeft als resultaat "Joh".

---
### <a name="len"></a>Len
**Beschrijving:**  
De functie Len retourneert het aantal tekens in een tekenreeks.

**Syntaxis:**  
`num Len(str value)`

**Voorbeeld:**  
`Len("John Doe")`  
Retourneert 8

---
### <a name="ltrim"></a>LTrim (LTrim)
**Beschrijving:**  
Met de functie LTrim worden voorloopwitte spaties uit een tekenreeks verwijderd.

**Syntaxis:**  
`str LTrim(str value)`

**Voorbeeld:**  
`LTrim(" Test ")`  
Retourneert 'Test'

---
### <a name="mid"></a>Mid
**Beschrijving:**  
Met de functie Mid retourneert een opgegeven aantal tekens uit een opgegeven positie in een tekenreeks.

**Syntaxis:**  
`str Mid(str string, num start, num NumChars)`

* tekenreeks: de tekenreeks om tekens terug te sturen uit
* start: een getal dat de beginpositie in de tekenreeks aankaart om tekens terug te sturen
* NumChars: een getal dat het aantal tekens aangeeft dat moet terugkeren vanaf de positie in tekenreeks

**Opmerkingen:**  
NumChars-tekens vanaf de positiestart in tekenreeks retourneren.  
Een tekenreeks met numChars-tekens vanaf positiestart in tekenreeks:

* Als numChars = 0, retourneert u de lege tekenreeks.
* Als numChars < 0, retourneert u de invoertekenreeks.
* Als u de tekenreekslengte > de lengte van de tekenreeks start.
* Als begin <= 0, retourinvoertekenreeks.
* Als tekenreeks null is, retourneert u de lege tekenreeks.

Als er geen numChar-tekens in tekenreeks blijven vanaf het begin van de positie, worden zoveel mogelijk tekens geretourneerd.

**Voorbeeld:**  
`Mid("John Doe", 3, 5)`  
Retourneert "hn Do".

`Mid("John Doe", 6, 999)`  
Retourneert 'Doe'

---
### <a name="now"></a>Now
**Beschrijving:**  
De functie Nu retourneert een DateTime waarin de huidige datum en tijd worden opgegeven, afhankelijk van de systeemdatum en -tijd van uw computer.

**Syntaxis:**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate NumfromDate
**Beschrijving:**  
De functie NumFromDate retourneert een datum in de datumnotatie van AD.

**Syntaxis:**  
`num NumFromDate(dt value)`

**Voorbeeld:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
Retourneert 1296993240000000000000

---
### <a name="padleft"></a>PadLinks
**Beschrijving:**  
De functie PadLeft fungeert met een tekenreeks van een bepaalde lengte met behulp van een opgegeven opvullingsteken.

**Syntaxis:**  
`str PadLeft(str string, num length, str padCharacter)`

* tekenreeks: de string naar pad.
* lengte: een geheel getal dat de gewenste lengte van de tekenreeks vertegenwoordigt.
* padCharacter: een tekenreeks die bestaat uit één teken dat moet worden gebruikt als het padteken

**Opmerkingen:**

* Als de lengte van de tekenreeks minder dan lengte is, wordt padTeken herhaaldelijk toegevoegd aan het begin (links) van de tekenreeks totdat deze een lengte heeft die gelijk is aan de lengte.
* PadCharacter kan een spatieteken zijn, maar het kan geen null-waarde zijn.
* Als de lengte van de tekenreeks gelijk is aan of groter is dan de lengte, wordt de tekenreeks ongewijzigd geretourneerd.
* Als tekenreeks een lengte heeft die groter is dan of gelijk is aan de lengte, wordt een tekenreeks die identiek is aan tekenreeks geretourneerd.
* Als de lengte van de tekenreeks minder dan lengte is, wordt een nieuwe tekenreeks van de gewenste lengte geretourneerd met tekenreeks opgevuld met een padTeken.
* Als tekenreeks null is, retourneert de functie een lege tekenreeks.

**Voorbeeld:**  
`PadLeft("User", 10, "0")`  
Retourneert "000000User".

---
### <a name="padright"></a>PadRight (PadRight)
**Beschrijving:**  
De PadRight-functie fungeert rechts-pads een string naar een bepaalde lengte met behulp van een meegeleverde padding karakter.

**Syntaxis:**  
`str PadRight(str string, num length, str padCharacter)`

* tekenreeks: de string naar pad.
* lengte: een geheel getal dat de gewenste lengte van de tekenreeks vertegenwoordigt.
* padCharacter: een tekenreeks die bestaat uit één teken dat moet worden gebruikt als het padteken

**Opmerkingen:**

* Als de lengte van de tekenreeks minder dan lengte is, wordt padTeken herhaaldelijk toegevoegd aan het einde (rechts) van de tekenreeks totdat deze een lengte heeft die gelijk is aan de lengte.
* padCharacter kan een spatieteken zijn, maar het kan geen null-waarde zijn.
* Als de lengte van de tekenreeks gelijk is aan of groter is dan de lengte, wordt de tekenreeks ongewijzigd geretourneerd.
* Als tekenreeks een lengte heeft die groter is dan of gelijk is aan de lengte, wordt een tekenreeks die identiek is aan tekenreeks geretourneerd.
* Als de lengte van de tekenreeks minder dan lengte is, wordt een nieuwe tekenreeks van de gewenste lengte geretourneerd met tekenreeks opgevuld met een padTeken.
* Als tekenreeks null is, retourneert de functie een lege tekenreeks.

**Voorbeeld:**  
`PadRight("User", 10, "0")`  
Retourneert "User000000".

---
### <a name="pcase"></a>PCase (PCase)
**Beschrijving:**  
Met de functie PCase wordt het eerste teken van elke spatie gedefinieerd woord in een tekenreeks naar hoofdletters en worden alle andere tekens omgezet in kleine letters.

**Syntaxis:**  
`String PCase(string)`

**Opmerkingen:**

* Deze functie biedt momenteel geen juiste behuizing om een woord dat volledig hoofdletters is, zoals een acroniem, om te zetten.

**Voorbeeld:**  
`PCase("TEsT")`  
Retourneert "Test".

`PCase(LCase("TEST"))`  
Retourneert 'Test'

---
### <a name="randomnum"></a>RandomNum
**Beschrijving:**  
De functie RandomNum retourneert een willekeurig getal tussen een opgegeven interval.

**Syntaxis:**  
`num RandomNum(num start, num end)`

* start: een getal dat de ondergrens van de willekeurige waarde identificeert om te genereren
* einde: een getal dat de bovengrens van de willekeurige waarde identificeert om te genereren

**Voorbeeld:**  
`Random(100,999)`  
Kan 734 teruggeven.

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
**Beschrijving:**  
De functie Vervangen vervangt alle exemplaren van een tekenreeks naar een andere tekenreeks.

**Syntaxis:**  
`str Replace(str string, str OldValue, str NewValue)`

* tekenreeks: een tekenreeks om waarden in te vervangen.
* OldValue: de tekenreeks om naar te zoeken en te vervangen.
* NewValue: de tekenreeks om te vervangen.

**Opmerkingen:**  
De functie herkent de volgende speciale monikers:

* \n – Nieuwe regel
* \r – Carriage Return
* \t – Tabblad

**Voorbeeld:**  
`Replace([address],"\r\n",", ")`  
Vervangt CRLF door een komma en ruimte, en kan leiden tot "One Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>Vervang Chars
**Beschrijving:**  
De functie ReplaceChars vervangt alle exemplaren van tekens in de tekenreeks ReplacePattern.

**Syntaxis:**  
`str ReplaceChars(str string, str ReplacePattern)`

* tekenreeks: een tekenreeks om tekens in te vervangen.
* ReplacePattern: een tekenreeks met een woordenboek met tekens die moeten worden vervangen.

De indeling is {source1}:{target1},{source2}:{target2},{sourceN},{targetN} waarbij bron het teken is om de tekenreeks te vinden en te targeten om te vervangen.

**Opmerkingen:**

* De functie neemt elk optreden van gedefinieerde bronnen en vervangt ze door de doelen.
* De bron moet precies één (unicode) teken zijn.
* De bron kan niet leeg of langer zijn dan één teken (parsingerror).
* Het doel kan meerdere tekens hebben, bijvoorbeeld ö:oe, β:ss.
* Het doel kan leeg zijn, wat aangeeft dat het teken moet worden verwijderd.
* De bron is hoofdlettergevoelig en moet exact overeenkomen.
* De , (komma) en : (dubbele punt) zijn gereserveerde tekens en kunnen niet worden vervangen met behulp van deze functie.
* Spaties en andere witte tekens in de tekenreeks ReplacePattern worden genegeerd.

**Voorbeeld:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Retourneert Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
Retourneert "ONeil", het enkele vinkje wordt gedefinieerd om te worden verwijderd.

---
### <a name="right"></a>Rechts
**Beschrijving:**  
De functie Rechts retourneert een opgegeven aantal tekens van rechts (einde) van een tekenreeks.

**Syntaxis:**  
`str Right(str string, num NumChars)`

* tekenreeks: de tekenreeks om tekens terug te sturen uit
* NumChars: een getal dat het aantal tekens aangeeft dat moet terugkeren van het einde (rechts) van de tekenreeks

**Opmerkingen:**  
NumChars-tekens worden geretourneerd vanuit de laatste positie van de tekenreeks.

Een tekenreeks met de laatste numChars-tekens in tekenreeks:

* Als numChars = 0, retourneert u de lege tekenreeks.
* Als numChars < 0, retourneert u de invoertekenreeks.
* Als tekenreeks null is, retourneert u de lege tekenreeks.

Als tekenreeks minder tekens bevat dan het getal dat is opgegeven in NumChars, wordt een tekenreeks die identiek is aan tekenreeks, geretourneerd.

**Voorbeeld:**  
`Right("John Doe", 3)`  
Geeft als resultaat 'Doe'.

---
### <a name="rtrim"></a>RTrim (RTrim)
**Beschrijving:**  
Met de functie RTrim worden slepende witte spaties uit een tekenreeks verwijderd.

**Syntaxis:**  
`str RTrim(str value)`

**Voorbeeld:**  
`RTrim(" Test ")`  
Retourneert "Test".

---
### <a name="select"></a>Selecteer
**Beschrijving:**  
Verwerk alle waarden in een kenmerk met meerdere waarden (of uitvoer van een expressie) op basis van de opgegeven functie.

**Syntaxis:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: vertegenwoordigt een element in het kenmerk met meerdere waarden
* kenmerk: het kenmerk met meerdere waarden
* expressie: een expressie die een verzameling waarden retourneert
* voorwaarde: elke functie die een item in het kenmerk kan verwerken

**Voorbeelden:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Retourneer alle waarden in het kenmerk met meerdere waarden otherPhone nadat koppeltekens (-) zijn verwijderd.

---
### <a name="split"></a>Splitsen
**Beschrijving:**  
De functie Splitsen neemt een tekenreeks gescheiden met een scheidingsteken en maakt het een tekenreeks met meerdere waarden.

**Syntaxis:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* waarde: de tekenreeks met een scheidingstekenteken dat moet worden gescheiden.
* scheidingsteken: enkel teken dat als scheidingsteken moet worden gebruikt.
* limiet: maximum aantal waarden dat kan retourneren.

**Voorbeeld:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Retourneert een tekenreeks met meerdere waarden met 2 elementen die nuttig zijn voor het kenmerk proxyAddress.

---
### <a name="stringfromguid"></a>StringFromGuid (stringFromGuid)
**Beschrijving:**  
De functie StringFromGuid neemt een binaire GUID en converteert deze naar een tekenreeks

**Syntaxis:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid (StringFromSid)
**Beschrijving:**  
De functie StringFromSid converteert een bytearray met een beveiligings-id naar een tekenreeks.

**Syntaxis:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Switch
**Beschrijving:**  
De functie Switch wordt gebruikt om één waarde terug te geven op basis van geëvalueerde voorwaarden.

**Syntaxis:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: Variantexpressie die u wilt evalueren.
* waarde: waarde die moet worden geretourneerd als de overeenkomstige expressie Waar is.

**Opmerkingen:**  
De argumentlijst Switch-functie bestaat uit paren van expressies en waarden. De expressies worden van links naar rechts geëvalueerd en de waarde die is gekoppeld aan de eerste expressie die moet worden geëvalueerd naar True, wordt geretourneerd. Als de onderdelen niet goed zijn gekoppeld, treedt een runtime-fout op.

Als expr1 bijvoorbeeld Waar is, retourneert Switch waarde1. Als expr-1 onwaar is, maar expr-2 waar is, retourneert Switch waarde-2, enzovoort.

Switch retourneert een Niets als:

* Geen van de uitdrukkingen zijn waar.
* De eerste true-expressie heeft een overeenkomstige waarde die Null is.

Switch evalueert alle expressies, ook al retourneert slechts één van deze expressies. Om deze reden moet u op passen voor ongewenste bijwerkingen. Als bijvoorbeeld de evaluatie van een expressie resulteert in een verdeling met nul fouten, treedt er een fout op.

Waarde kan ook de functie Fout zijn, die een aangepaste tekenreeks zou retourneren.

**Voorbeeld:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Geeft als resultaat de taal die in sommige grote steden wordt gesproken, anders wordt een fout geretourneerd.

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
### <a name="ucase"></a>UCase
**Beschrijving:**  
De functie UCase converteert alle tekens in een tekenreeks naar hoofdletters.

**Syntaxis:**  
`str UCase(str string)`

**Voorbeeld:**  
`UCase("TeSt")`  
Retourneert "TEST".

---
### <a name="where"></a>Waar

**Beschrijving:**  
Geeft als resultaat een subset van waarden uit een kenmerk met meerdere waarden (of uitvoer van een expressie) op basis van een specifieke voorwaarde.

**Syntaxis:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: vertegenwoordigt een element in het kenmerk met meerdere waarden
* kenmerk: het kenmerk met meerdere waarden
* voorwaarde: elke expressie die kan worden geëvalueerd op waar of onwaar
* expressie: een expressie die een verzameling waarden retourneert

**Voorbeeld:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Retourneer de certificaatwaarden in het gebruikerscertificaat met meerdere waarden dat niet is verlopen.

---
### <a name="with"></a>With
**Beschrijving:**  
De functie Met biedt een manier om een complexe expressie te vereenvoudigen door een variabele te gebruiken om een subexpressie weer te geven die een of meerdere keren in de complexe expressie wordt weergegeven.

**Syntaxis:**
`With(var variable, exp subExpression, exp complexExpression)`  
* variabele: vertegenwoordigt de subexpressie.
* subexpressie: subexpressie vertegenwoordigd door variabele.
* complexExpressie: een complexe expressie.

**Voorbeeld:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Is functioneel gelijkwaardig aan:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Die alleen verlopen certificaatwaarden retourneert in het kenmerk userCertificate.


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

## <a name="additional-resources"></a>Aanvullende resources
* [Inzicht in verklarende inrichtingsexpressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Synchronisatie van Azure AD Connect: synchronisatieopties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
