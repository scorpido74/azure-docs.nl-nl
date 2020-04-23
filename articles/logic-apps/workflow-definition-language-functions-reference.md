---
title: Referentiegids voor functies in expressies
description: Naslaggids voor functies in expressies voor Azure Logic Apps en Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: f557753c61af1e57490ae2d10b7f42475bd7c0a6
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870238"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Naslaggids voor het gebruik van functies in expressies voor Azure Logic Apps en Power Automate

Voor werkstroomdefinities in [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en Power [Automate](https://docs.microsoft.com/flow/getting-started)krijgen sommige [expressies](../logic-apps/logic-apps-workflow-definition-language.md#expressions) hun waarden uit runtime-acties die mogelijk nog niet bestaan wanneer uw werkstroom wordt uitgevoerd. Als u naar deze waarden wilt verwijzen of de waarden in deze expressies wilt verwerken, u *functies* gebruiken die worden geleverd door de [taal voor werkstroomdefinitie](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Deze referentiepagina is van toepassing op zowel Azure Logic Apps als Power Automate, maar wordt weergegeven in de azure Logic Apps-documentatie. Hoewel deze pagina specifiek verwijst naar logische apps, werken deze functies voor zowel stromen als logische apps. Zie [Expressies gebruiken in voorwaarden](https://docs.microsoft.com/flow/use-expressions-in-conditions)voor meer informatie over functies en expressies in Power Automate .

U bijvoorbeeld waarden berekenen met behulp van wiskundige functies, zoals de [functie add()](../logic-apps/workflow-definition-language-functions-reference.md#add)wanneer u de som van gehele getallen of drijvers wilt. Hier volgen andere voorbeeldtaken die u met functies uitvoeren:

| Taak | Functiesyntaxis | Resultaat |
| ---- | --------------- | ------ |
| Retourneer een tekenreeks in kleine letters. | toLower('<*tekst*>') <p>Bijvoorbeeld: toLower('Hallo') | "Hallo" |
| Een wereldwijd unieke id (GUID) retourneren. | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Als u functies wilt vinden [op basis van hun algemene doel,](#ordered-by-purpose)bekijkt u de volgende tabellen. Of, voor gedetailleerde informatie over elke functie, zie de [alfabetische lijst](#alphabetical-list).

> [!NOTE]
> In de syntaxis voor parameterdefinities betekent een vraagteken (?) dat na een parameter wordt weergegeven, dat de parameter optioneel is.
> Zie bijvoorbeeld [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Functies in expressies

Als u wilt laten zien hoe u een functie in een `customerName` expressie gebruikt, ziet `accountName` u in dit voorbeeld hoe u de waarde uit de parameter halen en die waarde aan de eigenschap toewijzen met behulp van de functie [parameters()](#parameters) in een expressie:

```json
"accountName": "@parameters('customerName')"
```

Hier volgen enkele andere algemene manieren waarop u functies in expressies gebruiken:

| Taak | Functiesyntaxis in een expressie |
| ---- | -------------------------------- |
| Werk uitvoeren met een item door dat item door te geven aan een functie. | "\@<*functieNaam*>(<*item*>)" |
| 1. Haal de *parameterName's*value `parameters()` met behulp van de geneste functie. </br>2. Voer werk uit met het resultaat door die waarde door te geven aan *functieNaam*. | "\@<*functieNaam*>(parameters('<*parameterName*>')". |
| 1. Haal het resultaat van de geneste *binnenfunctieNaam*. </br>2. Geef het resultaat door aan de functie *Uiterlijk2*. | \@<*"Name2*>(<*functieName*>(<*punt*>))" |
| 1. Haal het resultaat van *functieNaam*. </br>2. Aangezien het resultaat een object met *eigenschapName*is, krijgt u de waarde van die eigenschap. | "\@<*functieNoem>(<* *item*>).<*de eigenschapName*>" |
|||

De `concat()` functie kan bijvoorbeeld twee of meer tekenreekswaarden als parameters aannemen. Deze functie combineert deze tekenreeksen in één tekenreeks.
U ofwel doorgeven in string literals, bijvoorbeeld, Sophia en Owen, zodat u een gecombineerde string, SophiaOwen:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

U ook tekenreekswaarden ophalen uit parameters. In dit `parameters()` voorbeeld wordt `concat()` de `firstName` functie `lastName` in elke parameter en de parameters gebruikt. Vervolgens geeft u de resulterende tekenreeksen door aan de `concat()` functie, zodat u een gecombineerde tekenreeks krijgt, bijvoorbeeld "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Hoe dan ook, beide voorbeelden `customerName` wijzen het resultaat toe aan de eigenschap.

Hier zijn de beschikbare functies besteld door hun algemene doel, of u bladeren door de functies op basis van [alfabetische volgorde](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Tekenreeksfuncties

Om met tekenreeksen te werken, u deze tekenreeksfuncties en ook enkele [verzamelingsfuncties](#collection-functions)gebruiken.
Tekenreeksfuncties werken alleen op tekenreeksen.

| Tekenreeks, functie | Taak |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combineer twee of meer tekenreeksen en retourneer de gecombineerde tekenreeks. |
| [endsMet](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Controleer of een tekenreeks eindigt met de opgegeven subtekenreeks. |
| [opmaakNummer](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Een getal retourneren als tekenreeks op basis van de opgegeven notatie |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Genereer een wereldwijd unieke id (GUID) als tekenreeks. |
| [indexVan](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Geef de startpositie voor een subtekenreeks terug. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | De startpositie voor het laatste optreden van een subtekenreeks retourneren. |
| [Vervangen](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Vervang een subtekenreeks door de opgegeven tekenreeks en retourneer de bijgewerkte tekenreeks. |
| [Split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Retourneer een array die subtekenreeksen bevat, gescheiden door komma's, van een grotere tekenreeks op basis van een opgegeven scheidingstekenteken in de oorspronkelijke tekenreeks. |
| [startMet](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Controleer of een tekenreeks begint met een specifieke subtekenreeks. |
| [Subtekenreeks](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Tekens uit een tekenreeks retourneren, vanaf de opgegeven positie. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Retourneer een tekenreeks in kleine letters. |
| [Toupper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Een tekenreeks retourneren in hoofdletters. |
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Verwijder de voorloop- en naloopwitruimte uit een tekenreeks en retourneer de bijgewerkte tekenreeks. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Verzamelingsfuncties

Als u wilt werken met verzamelingen, over het algemeen arrays, tekenreeksen en soms woordenboeken, u deze verzamelingsfuncties gebruiken.

| Collectie, functie | Taak |
| ------------------- | ---- |
| [Bevat](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Controleer of een verzameling een specifiek item heeft. |
| [Lege](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Controleer of een verzameling leeg is. |
| [Eerste](../logic-apps/workflow-definition-language-functions-reference.md#first) | Retourneer het eerste item uit een verzameling. |
| [Snijpunt](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Retourneer een verzameling met *alleen* de algemene items in de opgegeven verzamelingen. |
| [Item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Wanneer u zich in een herhalende actie over een array bevindt, retourneert u het huidige item in de array tijdens de huidige iteratie van de actie. |
| [Join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Retourneer een tekenreeks met *alle* items uit een array, gescheiden door het opgegeven teken. |
| [Laatste](../logic-apps/workflow-definition-language-functions-reference.md#last) | Retourneer het laatste item uit een verzameling. |
| [Lengte](../logic-apps/workflow-definition-language-functions-reference.md#length) | Het aantal items in een tekenreeks of array retourneren. |
| [Overslaan](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Verwijder items uit de voorkant van een verzameling en retourneer *alle andere* items. |
| [Nemen](../logic-apps/workflow-definition-language-functions-reference.md#take) | Artikelen van de voorkant van een verzameling retourneren. |
| [Unie](../logic-apps/workflow-definition-language-functions-reference.md#union) | Retourneer een verzameling met *alle* items uit de opgegeven verzamelingen. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logische vergelijkingsfuncties

Als u met voorwaarden wilt werken, waarden en expressieresultaten wilt vergelijken of verschillende soorten logica wilt evalueren, u deze logische vergelijkingsfuncties gebruiken.
Zie de [alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige referentie over elke functie.

| Logische vergelijkingsfunctie | Taak |
| --------------------------- | ---- |
| [En](../logic-apps/workflow-definition-language-functions-reference.md#and) | Controleer of alle expressies waar zijn. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Controleer of beide waarden gelijkwaardig zijn. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Controleer of de eerste waarde groter is dan de tweede waarde. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Controleer of de eerste waarde groter is dan of gelijk is aan de tweede waarde. |
| [Als](../logic-apps/workflow-definition-language-functions-reference.md#if) | Controleer of een expressie waar of onwaar is. Retourneer een opgegeven waarde op basis van het resultaat. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Controleer of de eerste waarde lager is dan de tweede waarde. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Controleer of de eerste waarde lager is dan of gelijk is aan de tweede waarde. |
| [Niet](../logic-apps/workflow-definition-language-functions-reference.md#not) | Controleer of een expressie vals is. |
| [Of](../logic-apps/workflow-definition-language-functions-reference.md#or) | Controleer of ten minste één expressie waar is. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Conversiefuncties

Als u het type of de indeling van een waarde wilt wijzigen, u deze conversiefuncties gebruiken.
U bijvoorbeeld een waarde wijzigen van een Booleaanse waarde naar een geheel getal.
Zie [Inhoudstypen verwerken](../logic-apps/logic-apps-content-type.md)voor meer informatie over hoe Logic Apps inhoudstypen tijdens de conversie verwerkt.
Zie de [alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige referentie over elke functie.

| Conversie, functie | Taak |
| ------------------- | ---- |
| [matrix](../logic-apps/workflow-definition-language-functions-reference.md#array) | Retourneer een array uit één opgegeven invoer. Zie [createArray voor](../logic-apps/workflow-definition-language-functions-reference.md#createArray)meerdere ingangen. |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Retourneer de basis64-gecodeerde versie voor een tekenreeks. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Retourneer de binaire versie voor een door basis64 gecodeerde tekenreeks. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Retourneer de tekenreeksversie voor een tekenreeks met basis64.Return the string version for a base64-coded string. |
| [Binaire](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Retourneer de binaire versie voor een invoerwaarde. |
| [booleaans](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Retourneer de Booleaanse versie voor een invoerwaarde. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Retourneer een array van meerdere ingangen. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Retourneer de gegevens URI voor een invoerwaarde. |
| [datauritobinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Retourneer de binaire versie voor een gegevensURI. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Retourneer de tekenreeksversie voor een gegevensURI. |
| [decodeBase64 decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Retourneer de tekenreeksversie voor een tekenreeks met basis64.Return the string version for a base64-coded string. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Retourneer de binaire versie voor een gegevensURI. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Retourneer een tekenreeks die vluchttekens vervangt door gedecodeerde versies. |
| [codeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Retourneer een tekenreeks die URL-onveilige tekens vervangt door escape-tekens. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Een zwevend puntnummer voor een invoerwaarde retourneren. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Retourneer de gehele versie voor een tekenreeks. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | De Json-typewaarde of object (JavaScript Object Notatie) voor een tekenreeks of XML retourneren. |
| [tekenreeks](../logic-apps/workflow-definition-language-functions-reference.md#string) | Retourneer de tekenreeksversie voor een invoerwaarde. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Retourneer de URI-gecodeerde versie voor een invoerwaarde door URL-onveilige tekens te vervangen door escape-tekens. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Retourneer de binaire versie voor een uri-gecodeerde tekenreeks. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Retourneer de tekenreeksversie voor een uri-gecodeerde tekenreeks. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Retourneer de XML-versie voor een tekenreeks. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Wiskundige functies

Om met gehele getallen en drijvers te werken, u deze wiskundige functies gebruiken.
Zie de [alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige referentie over elke functie.

| Wiskundige functie | Taak |
| ------------- | ---- |
| [Toevoegen](../logic-apps/workflow-definition-language-functions-reference.md#add) | Retourneer het resultaat van het toevoegen van twee getallen. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Retourneer het resultaat van het delen van twee getallen. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Retourneer de hoogste waarde van een reeks getallen of een array. |
| [Min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Retourneer de laagste waarde uit een reeks getallen of een array. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Retourneer de rest van het delen van twee getallen. |
| [Mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Retourneer het product van het vermenigvuldigen van twee getallen. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Retourneer een willekeurig geheel getal uit een opgegeven bereik. |
| [Bereik](../logic-apps/workflow-definition-language-functions-reference.md#range) | Retourneer een gehele gehele array die vertrekt vanaf een opgegeven geheel getal. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Retourneer het resultaat van het aftrekken van het tweede getal van het eerste getal. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Datum- en tijdfuncties

Om met datums en tijden te werken, u deze datum- en tijdfuncties gebruiken.
Zie de [alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige referentie over elke functie.

| Datum of tijd, functie | Taak |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Voeg een aantal dagen toe aan een tijdstempel. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Voeg een aantal uren toe aan een tijdstempel. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Voeg een aantal minuten toe aan een tijdstempel. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Voeg een aantal seconden toe aan een tijdstempel. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Voeg een aantal tijdseenheden toe aan een tijdstempel. Zie ook [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [converterenFromutc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converteer een tijdstempel van Universal Time Coordinated (UTC) naar de doeltijdzone. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converteer een tijdstempel van de brontijdzone naar de doeltijdzone. |
| [converterenToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Een tijdstempel converteren van de brontijdzone naar Universal Time Coordinated (UTC). |
| [dayOfMonth (dagOfMonth)](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Retourneer de component dag van de maand van een tijdstempel. |
| [dayOfWeek (dagOfWeek)](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Retourneer de dag van de weekcomponent van een tijdstempel. |
| [dayOfYear (dayOfYear)](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Retourneer de component dag van het jaar van een tijdstempel. |
| [opmaakDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Retourneer de datum van een tijdstempel. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Retourneer de huidige tijdstempel plus de opgegeven tijdeenheden. Zie ook [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Retourneer de huidige tijdstempel minus de opgegeven tijdeenheden. Zie ook [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Breng het begin van de dag terug voor een tijdstempel. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Breng het begin van het uur terug voor een tijdstempel. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Retourneer het begin van de maand voor een tijdstempel. |
| [subtractEnFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Trek een aantal tijdeenheden af van een tijdstempel. Zie ook [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Teken](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Retourneer `ticks` de eigenschapswaarde voor een opgegeven tijdstempel. |
| [utcNu](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Retourneer de huidige tijdstempel als tekenreeks. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Werkstroomfuncties

Met deze werkstroomfuncties u het volgende doen:

* Informatie over een werkstroominstantie op uitvoering.
* Werk met de ingangen die worden gebruikt voor het instantiën van logische apps of -stromen.
* Verwijs naar de uitvoer van triggers en acties.

U bijvoorbeeld verwijzen naar de uitvoer van één actie en die gegevens in een latere actie gebruiken.
Zie de [alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige referentie over elke functie.

| Werkstroomfunctie | Taak |
| ----------------- | ---- |
| [Actie](../logic-apps/workflow-definition-language-functions-reference.md#action) | Retourneer de uitvoer van de huidige actie bij runtime of waarden van andere JSON-naam-en-waardeparen. Zie ook [acties](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Retourneer de `body` uitvoer van een actie bij runtime. Zie ook [lichaam](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actieOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Retourneer de uitvoer van een actie bij runtime. Zie [uitvoer](../logic-apps/workflow-definition-language-functions-reference.md#outputs) en [acties](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Acties](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Retourneer de uitvoer van een actie bij runtime of waarden van andere JSON-naam-en-waardeparen. Zie ook [actie](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [Lichaam](#body) | Retourneer de `body` uitvoer van een actie bij runtime. Zie ook [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiWaarden](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Maak een array met de waarden die overeenkomen met een sleutelnaam in *formuliergegevens* of *formuliergecodeerde* actie-uitvoer. |
| [formDataWaarde](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Retourneer één waarde die overeenkomt met een sleutelnaam in de *formuliergegevens* of *formuliergecodeerde uitvoer*van een actie . |
| [Item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Wanneer u zich in een herhalende actie over een array bevindt, retourneert u het huidige item in de array tijdens de huidige iteratie van de actie. |
| [Items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Wanneer u zich in een foreach- of Until-lus begeeft, retourneert u het huidige item uit de opgegeven lus.|
| [iteratieIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Wanneer u binnen een lus Tot zit, geeft u de indexwaarde voor de huidige iteratie terug. U deze functie gebruiken in geneste tot lussen. |
| [lijstCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Retourneer de "callback URL" die een trigger of actie aanroept. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Retourneer de behuizing voor een specifiek onderdeel in de uitvoer van een actie die meerdere onderdelen heeft. |
| [Uitgangen](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Retourneer de uitvoer van een actie bij runtime. |
| [Parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Retourneer de waarde voor een parameter die wordt beschreven in de werkstroomdefinitie. |
| [Resultaat](../logic-apps/workflow-definition-language-functions-reference.md#result) | Retourneer de inputs en outputs van alle acties binnen `For_each` `Until`de `Scope`opgegeven scoped actie, zoals , en . |
| [Trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Retourneer de uitvoer van een trigger bij runtime of van andere JSON-naam-en-waardeparen. Zie ook [triggerOutputs](#triggerOutputs) en [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Retourneer de `body` uitvoer van een trigger bij runtime. Zie [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Retourneer één waarde die overeenkomt met een sleutelnaam in *formuliergegevens* of *formuliergecodeerde* triggeroutputs. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Breng het lichaam terug voor een specifiek onderdeel in de meerdelige uitvoer van een trigger. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Maak een array waarvan de waarden overeenkomen met een sleutelnaam in *formuliergegevens* of *formuliergecodeerde* triggeroutputs. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Retourneer de uitvoer van een trigger bij runtime of waarden van andere JSON-naam-en-waardeparen. Zie [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Variabelen](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Geef de waarde voor een opgegeven variabele terug. |
| [Workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Retourneer alle details over de werkstroom zelf tijdens de uitvoering. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI parsing functies

Als u wilt werken met uniforme resource-id's (URI's) en verschillende eigenschapswaarden voor deze URI's wilt verkrijgen, u deze URI-parsingsfuncties gebruiken.
Zie de [alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige referentie over elke functie.

| URI parsing, functie | Taak |
| -------------------- | ---- |
| [uriHost (uriHost)](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | De `host` waarde voor een uniforme resource-id (URI) retourneren. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | De `path` waarde voor een uniforme resource-id (URI) retourneren. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | De `path` waarden `query` en waarden voor een uniforme resource-id (URI) retourneren. |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | De `port` waarde voor een uniforme resource-id (URI) retourneren. |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | De `query` waarde voor een uniforme resource-id (URI) retourneren. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | De `scheme` waarde voor een uniforme resource-id (URI) retourneren. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulatiefuncties: JSON & XML

Als u wilt werken met JSON-objecten en XML-knooppunten, u deze manipulatiefuncties gebruiken.
Zie de [alfabetische lijst](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)voor de volledige referentie over elke functie.

| Manipulatie, functie | Taak |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Voeg een eigenschap en de waarde ervan, of naamwaardepaar, toe aan een JSON-object en retourneer het bijgewerkte object. |
| [samensmelten](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Retourneer de eerste niet-null-waarde uit een of meer parameters. |
| [verwijderenEigenschap](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Verwijder een eigenschap uit een JSON-object en retourneer het bijgewerkte object. |
| [Eigenschapinstellen](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Stel de waarde in voor de eigenschap van een JSON-object en retourneer het bijgewerkte object. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Controleer XML op knooppunten of waarden die overeenkomen met een XPath-padtaalexpressie en retourneer de overeenkomende knooppunten of waarden. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Alle functies - alfabetische lijst

In deze sectie worden alle beschikbare functies in alfabetische volgorde weergegeven.

<a name="action"></a>

### <a name="action"></a>action

Retourneer de uitvoer van de *huidige* actie bij runtime of waarden van andere JSON-naam-en-waardeparen, die u aan een expressie toewijzen.
Standaard verwijst deze functie naar het hele actieobject, maar u optioneel een eigenschap opgeven waarvan u de waarde wilt.
Zie ook [acties()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

U `action()` de functie alleen op deze plaatsen gebruiken:

* De `unsubscribe` eigenschap voor een webhook-actie, zodat `subscribe` u toegang hebt tot het resultaat van het oorspronkelijke verzoek
* De `trackedProperties` eigenschap voor een actie
* De `do-until` lusvoorwaarde voor een actie

```
action()
action().outputs.body.<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Eigenschap*> | Nee | Tekenreeks | De naam voor de eigenschap van het actieobject waarvan u de waarde wilt: **naam**, **startTime**, **endTime,** **inputs**, **outputs**, **status**, **code**, **trackingId**en **clientTrackingId**. In de Azure-portal u deze eigenschappen vinden door de details van een specifieke runsgeschiedenis te bekijken. Zie [REST API - Werkstroomuitvoeren acties voor](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get)meer informatie. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*actie-output*> | Tekenreeks | De uitvoer van de huidige actie of eigenschap |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Retourneer de `body` uitvoer van een actie bij runtime.
Steno `actions('<actionName>').outputs.body`voor .
Zie [body()](#body) en [actions()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de `body` gewenste uitvoer van de actie |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*action-body-output*> | Tekenreeks | De `body` uitvoer van de opgegeven actie |
||||

*Voorbeeld*

Dit voorbeeld `body` krijgt de output `Get user`van de Twitter-actie:

```
actionBody('Get_user')
```

En geeft dit resultaat als resultaat:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actieOutputs

Retourneer de uitvoer van een actie bij runtime.  en is steno voor `actions('<actionName>').outputs`. Zie [acties()](#actions). De `actionOutputs()` functie wordt `outputs()` opgelost in de Logic App Designer, dus `actionOutputs()`overweeg om [uitvoer()](#outputs)te gebruiken in plaats van . Hoewel beide functies op `outputs()` dezelfde manier werken, heeft de voorkeur.

```
actionOutputs('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de gewenste uitvoer van de actie |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*Output*> | Tekenreeks | De uitvoer van de opgegeven actie |
||||

*Voorbeeld*

Dit voorbeeld krijgt de output `Get user`van de Twitter-actie:

```
actionOutputs('Get_user')
```

En geeft dit resultaat als resultaat:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>acties

Retourneer de uitvoer van een actie bij runtime of waarden van andere JSON-naam-en-waardeparen, die u aan een expressie toewijzen. Standaard verwijst de functie naar het hele actieobject, maar u optioneel een eigenschap opgeven waarvan u de waarde wilt hebben.
Zie [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)en [body()](#body)voor stenoversies .
Zie [actie()](#action)voor de huidige actie.

> [!NOTE]
> Voorheen kon u `actions()` de functie `conditions` of het element gebruiken wanneer u opgeeft dat een actie is uitgevoerd op basis van de uitvoer van een andere actie. Als u echter expliciet afhankelijkheden tussen acties wilt declareren, `runAfter` moet u nu de eigenschap van de afhankelijke actie gebruiken.
> Zie Fouten vangen `runAfter` en [afhandelen met de eigenschap runAfter](../logic-apps/logic-apps-workflow-definition-language.md)voor meer informatie over de eigenschap.

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor het actieobject waarvan u de uitvoer wilt  |
| <*Eigenschap*> | Nee | Tekenreeks | De naam voor de eigenschap van het actieobject waarvan u de waarde wilt: **naam**, **startTime**, **endTime,** **inputs**, **outputs**, **status**, **code**, **trackingId**en **clientTrackingId**. In de Azure-portal u deze eigenschappen vinden door de details van een specifieke runsgeschiedenis te bekijken. Zie [REST API - Werkstroomuitvoeren acties voor](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get)meer informatie. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*actie-output*> | Tekenreeks | De uitvoer van de opgegeven actie of eigenschap |
||||

*Voorbeeld*

In dit `status` voorbeeld wordt de `Get user` waarde van de eigenschap opgehaald van de Twitter-actie bij runtime:

```
actions('Get_user').outputs.body.status
```

En geeft dit resultaat als resultaat:`"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Retourneer het resultaat van het toevoegen van twee getallen.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Ja | Geheel getal, zweven of gemengd | De getallen die moeten worden toegevoegd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*resultaatsom*> | Geheel getal of zweven | Het resultaat van het toevoegen van de opgegeven getallen |
||||

*Voorbeeld*

In dit voorbeeld worden de opgegeven getallen toegevoegd:

```
add(1, 1.5)
```

En geeft dit resultaat als resultaat:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Voeg een aantal dagen toe aan een tijdstempel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Dagen*> | Ja | Geheel getal | Het positieve of negatieve aantal dagen om toe te voegen |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De tijdstempel plus het opgegeven aantal dagen  |
||||

*Voorbeeld 1*

In dit voorbeeld wordt 10 dagen toegevoegd aan de opgegeven tijdstempel:

```
addDays('2018-03-15T13:00:00Z', 10)
```

En geeft dit resultaat als resultaat:`"2018-03-25T00:00:0000000Z"`

*Voorbeeld 2*

In dit voorbeeld worden vijf dagen van de opgegeven tijdstempel afgetrokken:

```
addDays('2018-03-15T00:00:00Z', -5)
```

En geeft dit resultaat als resultaat:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Voeg een aantal uren toe aan een tijdstempel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Uur*> | Ja | Geheel getal | Het positieve of negatieve aantal uren om toe te voegen |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De tijdstempel plus het opgegeven aantal uren  |
||||

*Voorbeeld 1*

In dit voorbeeld wordt 10 uur toegevoegd aan de opgegeven tijdstempel:

```
addHours('2018-03-15T00:00:00Z', 10)
```

En geeft dit resultaat als resultaat:`"2018-03-15T10:00:0000000Z"`

*Voorbeeld 2*

In dit voorbeeld worden vijf uur afgetrokken van de opgegeven tijdstempel:

```
addHours('2018-03-15T15:00:00Z', -5)
```

En geeft dit resultaat als resultaat:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Voeg een aantal minuten toe aan een tijdstempel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Minuten*> | Ja | Geheel getal | Het positieve of negatieve aantal minuten om toe te voegen |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De tijdstempel plus het opgegeven aantal minuten |
||||

*Voorbeeld 1*

In dit voorbeeld wordt 10 minuten toegevoegd aan de opgegeven tijdstempel:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

En geeft dit resultaat als resultaat:`"2018-03-15T00:20:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt vijf minuten van de opgegeven tijdstempel afgetrokken:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

En geeft dit resultaat als resultaat:`"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Voeg een eigenschap en de waarde ervan, of naamwaardepaar, toe aan een JSON-object en retourneer het bijgewerkte object. Als de eigenschap al bestaat bij runtime, mislukt de functie en wordt er een fout gegenereerd.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waarbij u een eigenschap wilt toevoegen |
| <*Eigenschap*> | Ja | Tekenreeks | De naam voor de eigenschap toe te voegen |
| <*Waarde*> | Ja | Alle | De waarde voor de eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt object*> | Object | Het bijgewerkte JSON-object met de opgegeven eigenschap |
||||

Als u een onderliggende eigenschap wilt toevoegen aan een bestaande eigenschap, gebruikt u de als volgt:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waarbij u een eigenschap wilt toevoegen |
| <*bovenliggende eigenschap*> | Ja | Tekenreeks | De naam voor bovenliggende eigenschap waar u de onderliggende eigenschap wilt toevoegen |
| <*kind-eigendom*> | Ja | Tekenreeks | De naam voor de onderliggende eigenschap die moet worden toegevoegd |
| <*Waarde*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt object*> | Object | Het bijgewerkte JSON-object waarvan u de eigenschap hebt ingesteld |
||||

*Voorbeeld 1*

In dit `middleName` voorbeeld wordt de eigenschap toegevoegd aan een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met behulp van de functie [JSON().](#json) Het object bevat `firstName` `surName` al de eigenschappen en eigenschappen. De functie wijst de opgegeven waarde toe aan de nieuwe eigenschap en retourneert het bijgewerkte object:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Hier is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Voorbeeld 2*

In dit `middleName` voorbeeld wordt de `customerName` onderliggende eigenschap toegevoegd aan de bestaande eigenschap in een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met behulp van de functie [JSON().](#json) De functie wijst de opgegeven waarde toe aan de nieuwe eigenschap en retourneert het bijgewerkte object:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Hier is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Voeg een aantal seconden toe aan een tijdstempel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Seconden*> | Ja | Geheel getal | Het positieve of negatieve aantal seconden om toe te voegen |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De tijdstempel plus het opgegeven aantal seconden  |
||||

*Voorbeeld 1*

In dit voorbeeld worden 10 seconden toegevoegd aan de opgegeven tijdstempel:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

En geeft dit resultaat als resultaat:`"2018-03-15T00:00:10.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld worden vijf seconden afgetrokken van de opgegeven tijdstempel:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

En geeft dit resultaat als resultaat:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Voeg een aantal tijdseenheden toe aan een tijdstempel.
Zie ook [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Interval*> | Ja | Geheel getal | Het aantal opgegeven tijdeenheden dat moet worden toegevoegd |
| <*timeUnit*> | Ja | Tekenreeks | De eenheid van de tijd te gebruiken met *interval*: "Tweede", "Minuut", "Uur", "Dag", "Week", "Maand", "Jaar" |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De tijdstempel plus het opgegeven aantal tijdeenheden  |
||||

*Voorbeeld 1*

In dit voorbeeld wordt één dag aan de opgegeven tijdstempel toegevoegd:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

En geeft dit resultaat als resultaat:`"2018-01-02T00:00:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt één dag aan de opgegeven tijdstempel toegevoegd:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

En retourneert het resultaat met de optionele "D"-indeling:`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>en

Controleer of alle expressies waar zijn.
Retourneer waar wanneer alle expressies waar zijn, of keer false terug wanneer ten minste één expressie onwaar is.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*uitdrukking1*>, <*expressie2*>, ... | Ja | Booleaans | De expressies die u moet controleren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| waar of onwaar | Booleaans | Retourneer waar wanneer alle expressies waar zijn. Retourneer false wanneer ten minste één expressie onjuist is. |
||||

*Voorbeeld 1*

In deze voorbeelden wordt gecontroleerd of de opgegeven Booleaanse waarden allemaal waar zijn:

```
and(true, true)
and(false, true)
and(false, false)
```

En geeft deze resultaten als resultaat:

* Eerste voorbeeld: Beide expressies `true`zijn waar, dus retourneert .
* Tweede voorbeeld: Een expressie is `false`onjuist, dus retourneert .
* Derde voorbeeld: Beide expressies `false`zijn vals, dus retourneert .

*Voorbeeld 2*

In deze voorbeelden wordt gecontroleerd of de opgegeven expressies allemaal waar zijn:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

En geeft deze resultaten als resultaat:

* Eerste voorbeeld: Beide expressies `true`zijn waar, dus retourneert .
* Tweede voorbeeld: Een expressie is `false`onjuist, dus retourneert .
* Derde voorbeeld: Beide expressies `false`zijn vals, dus retourneert .

<a name="array"></a>

### <a name="array"></a>matrix

Retourneer een array uit één opgegeven invoer.
Zie [createArray() voor](#createArray)meerdere ingangen.

```
array('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks voor het maken van een array |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*> waarde]* | Matrix | Een array die de opgegeven invoer bevat |
||||

*Voorbeeld*

In dit voorbeeld wordt een array gemaakt op basis van de tekenreeks 'hallo':

```
array('hello')
```

En geeft dit resultaat als resultaat:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retourneer de basis64-gecodeerde versie voor een tekenreeks.

```
base64('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De invoertekenreeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*base64-tekenreeks*> | Tekenreeks | De basisversie met 64 voor de invoertekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt de tekenreeks 'hallo' omgezet in een tekenreeks met een basis64-code:

```
base64('hello')
```

En geeft dit resultaat als resultaat:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retourneer de binaire versie voor een door basis64 gecodeerde tekenreeks.

```
base64ToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks met basis64 om te converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binair-voor-base64-tekenreeks*> | Tekenreeks | De binaire versie voor de tekenreeks met basis64 |
||||

*Voorbeeld*

In dit voorbeeld wordt de basistekenreeks "aGVsbG8=" geconverteerd naar een binaire tekenreeks:

```
base64ToBinary('aGVsbG8=')
```

En geeft dit resultaat als resultaat:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retourneer de tekenreeksversie voor een met basis64 gecodeerde tekenreeks, die de base64-tekenreeks effectief decodeert.
Gebruik deze functie in plaats van [te decoderenBase64()](#decodeBase64).
Hoewel beide functies op `base64ToString()` dezelfde manier werken, heeft de voorkeur.

```
base64ToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De door basis64 gecodeerde tekenreeks om te decoderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerde base64-tekenreeks*> | Tekenreeks | De tekenreeksversie voor een tekenreeks met basis64 |
||||

*Voorbeeld*

In dit voorbeeld wordt de basis64-gecodeerde tekenreeks "aGVsbG8=" omgezet in slechts een tekenreeks:

```
base64ToString('aGVsbG8=')
```

En geeft dit resultaat als resultaat:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binair

Retourneer de binaire versie voor een tekenreeks.

```
binary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die u wilt converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binaire waarde voor invoer*> | Tekenreeks | De binaire versie voor de opgegeven tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt de tekenreeks 'hallo' geconverteerd naar een binaire tekenreeks:

```
binary('hello')
```

En geeft dit resultaat als resultaat:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Retourneer de `body` uitvoer van een actie bij runtime.
Steno `actions('<actionName>').outputs.body`voor .
Zie [actionBody()](#actionBody) en [acties()](#actions).

```
body('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de `body` gewenste uitvoer van de actie |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*action-body-output*> | Tekenreeks | De `body` uitvoer van de opgegeven actie |
||||

*Voorbeeld*

In dit `body` voorbeeld wordt `Get user` de uitvoer van de Twitter-actie opgehaald:

```
body('Get_user')
```

En geeft dit resultaat als resultaat:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>booleaans

Retourneer de Booleaanse versie voor een waarde.

```
bool(<value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Alle | De waarde die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | De Booleaanse versie voor de opgegeven waarde |
||||

*Voorbeeld*

In deze voorbeelden worden de opgegeven waarden geconverteerd naar Booleaanse waarden:

```
bool(1)
bool(0)
```

En geeft deze resultaten als resultaat:

* Eerste voorbeeld:`true`
* Tweede voorbeeld:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>samensmelten

Retourneer de eerste niet-null-waarde uit een of meer parameters.
Lege tekenreeksen, lege arrays en lege objecten zijn niet null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Ja | Elke, kan mix types | Een of meer items om te controleren op null |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*eerste-niet-null-item*> | Alle | Het eerste item of de waarde die niet null is. Als alle parameters nietig zijn, retourneert deze functie null. |
||||

*Voorbeeld*

In deze voorbeelden wordt de eerste niet-null-waarde teruggegeven aan de opgegeven waarden of null wanneer alle waarden nietig zijn:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

En geeft deze resultaten als resultaat:

* Eerste voorbeeld:`true`
* Tweede voorbeeld:`"hello"`
* Derde voorbeeld:`null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Combineer twee of meer tekenreeksen en retourneer de gecombineerde tekenreeks.

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Ja | Tekenreeks | Ten minste twee tekenreeksen te combineren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Tekenreeks | De tekenreeks die is gemaakt op de gecombineerde invoertekenreeksen |
||||

*Voorbeeld*

In dit voorbeeld worden de tekenreeksen "Hallo" en "Wereld" gecombineerd:

```
concat('Hello', 'World')
```

En geeft dit resultaat als resultaat:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>bevat

Controleer of een verzameling een specifiek item heeft.
Retourneer waar wanneer het item is gevonden of retourneer onwaar wanneer het niet is gevonden.
Deze functie is hoofdlettergevoelig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Deze functie werkt specifiek op deze verzamelingstypen:

* Een *tekenreeks* om een *subtekenreeks* te zoeken
* Een *array* om een *waarde* te vinden
* Een *woordenboek* om een *sleutel* te vinden

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks, array of woordenboek | De collectie om te controleren |
| <*Waarde*> | Ja | Tekenreeks, array of woordenboek, respectievelijk | Het item dat u moet vinden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneer waar wanneer het item is gevonden. Retouronwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de tekenreeks 'hallo wereld' gecontroleerd op de subtekenreeks 'wereld' en wordt true geretourneerd:

```
contains('hello world', 'world')
```

*Voorbeeld 2*

In dit voorbeeld wordt de tekenreeks "hello world" gecontroleerd op het substring "universum" en wordt false geretourneerd:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>converterenFromutc

Converteer een tijdstempel van Universal Time Coordinated (UTC) naar de doeltijdzone.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*destinationTimeZone*> | Ja | Tekenreeks | De naam voor de doeltijdzone. Zie Microsoft Time [Zone Index Waarden](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)voor tijdzonenamen, maar het kan zijn dat u de interpunctie uit de naam van de tijdzone moet verwijderen. |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerde tijdstempel*> | Tekenreeks | De tijdstempel die is omgezet naar de doeltijdzone |
||||

*Voorbeeld 1*

In dit voorbeeld wordt een tijdstempel omgezet naar de opgegeven tijdzone:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

En geeft dit resultaat als resultaat:`"2018-01-01T00:00:00.0000000"`

*Voorbeeld 2*

In dit voorbeeld wordt een tijdstempel geconverteerd naar de opgegeven tijdzone en -indeling:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

En geeft dit resultaat als resultaat:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converteer een tijdstempel van de brontijdzone naar de doeltijdzone.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*sourceTimeZone*> | Ja | Tekenreeks | De naam voor de brontijdzone. Zie Microsoft Time [Zone Index Waarden](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)voor tijdzonenamen, maar het kan zijn dat u de interpunctie uit de naam van de tijdzone moet verwijderen. |
| <*destinationTimeZone*> | Ja | Tekenreeks | De naam voor de doeltijdzone. Zie Microsoft Time [Zone Index Waarden](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)voor tijdzonenamen, maar het kan zijn dat u de interpunctie uit de naam van de tijdzone moet verwijderen. |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerde tijdstempel*> | Tekenreeks | De tijdstempel die is omgezet naar de doeltijdzone |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de brontijdzone omgezet in de doeltijdzone:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

En geeft dit resultaat als resultaat:`"2018-01-01T00:00:00.0000000"`

*Voorbeeld 2*

In dit voorbeeld wordt een tijdzone omgezet in de opgegeven tijdzone en -indeling:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

En geeft dit resultaat als resultaat:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>converterenToUtc

Een tijdstempel converteren van de brontijdzone naar Universal Time Coordinated (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*sourceTimeZone*> | Ja | Tekenreeks | De naam voor de brontijdzone. Zie Microsoft Time [Zone Index Waarden](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)voor tijdzonenamen, maar het kan zijn dat u de interpunctie uit de naam van de tijdzone moet verwijderen. |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geconverteerde tijdstempel*> | Tekenreeks | De tijdstempel die is geconverteerd naar UTC |
||||

*Voorbeeld 1*

In dit voorbeeld wordt een tijdstempel geconverteerd naar UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

En geeft dit resultaat als resultaat:`"2018-01-01T08:00:00.0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt een tijdstempel geconverteerd naar UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

En geeft dit resultaat als resultaat:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Retourneer een array van meerdere ingangen.
Zie [array()](#array)voor afzonderlijke invoerarrays.

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Ja | Elke, maar niet gemengd | Ten minste twee items om de array te maken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Matrix | De array die is gemaakt van alle invoeritems |
||||

*Voorbeeld*

In dit voorbeeld wordt een array van deze ingangen gemaakt:

```
createArray('h', 'e', 'l', 'l', 'o')
```

En geeft dit resultaat als resultaat:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Een gegevensuniforme resource-id (URI) retourneren voor een tekenreeks.

```
dataUri('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die u wilt converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*data-uri*> | Tekenreeks | De gegevens URI voor de invoertekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt een gegevens-URI gemaakt voor de tekenreeks 'hallo':

```
dataUri('hello')
```

En geeft dit resultaat als resultaat:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>datauritobinary

De binaire versie retourneren voor een gegevensuniforme resource-id (URI).
Gebruik deze functie in plaats van [DataUri() te decoderen.](#decodeDataUri)
Hoewel beide functies op `dataUriBinary()` dezelfde manier werken, heeft de voorkeur.

```
dataUriToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De gegevens URI om te converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binair-voor-data-uri*> | Tekenreeks | De binaire versie voor de gegevens URI |
||||

*Voorbeeld*

In dit voorbeeld wordt een binaire versie voor deze gegevens URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En geeft dit resultaat als resultaat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

De tekenreeksversie retourneren voor een gegevensuniforme resource-id (URI).

```
dataUriToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De gegevens URI om te converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Tekenreeks | De tekenreeksversie voor de gegevens-URI |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks voor deze gegevens URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En geeft dit resultaat als resultaat:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retourneer de dag van de maand vanaf een tijdstempel.

```
dayOfMonth('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van de maand*> | Geheel getal | De dag van de maand vanaf de opgegeven tijdstempel |
||||

*Voorbeeld*

In dit voorbeeld wordt het nummer voor de dag van de maand geretourneerd vanaf deze tijdstempel:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

En geeft dit resultaat als resultaat:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retourneer de dag van de week van een tijdstempel.

```
dayOfWeek('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van de week*> | Geheel getal | De dag van de week vanaf de opgegeven tijdstempel waar zondag 0 is, maandag is 1, enzovoort |
||||

*Voorbeeld*

In dit voorbeeld wordt het nummer voor de dag van de week geretourneerd vanaf deze tijdstempel:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

En geeft dit resultaat als resultaat:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retourneer de dag van het jaar van een tijdstempel.

```
dayOfYear('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*dag van het jaar*> | Geheel getal | De dag van het jaar vanaf de opgegeven tijdstempel |
||||

*Voorbeeld*

In dit voorbeeld wordt het aantal dagen van het jaar geretourneerd vanaf deze tijdstempel:

```
dayOfYear('2018-03-15T13:27:36Z')
```

En geeft dit resultaat als resultaat:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64 decodeBase64

Retourneer de tekenreeksversie voor een met basis64 gecodeerde tekenreeks, die de base64-tekenreeks effectief decodeert.
Overweeg [om base64ToString()](#base64ToString) te gebruiken in plaats `decodeBase64()`van .
Hoewel beide functies op `base64ToString()` dezelfde manier werken, heeft de voorkeur.

```
decodeBase64('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De door basis64 gecodeerde tekenreeks om te decoderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerde base64-tekenreeks*> | Tekenreeks | De tekenreeksversie voor een tekenreeks met basis64 |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks voor een tekenreeks met basis64-gecodeerd:

```
decodeBase64('aGVsbG8=')
```

En geeft dit resultaat als resultaat:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

De binaire versie retourneren voor een gegevensuniforme resource-id (URI).
Overweeg het gebruik van [dataUriToBinary()](#dataUriToBinary), in plaats `decodeDataUri()`van .
Hoewel beide functies op `dataUriToBinary()` dezelfde manier werken, heeft de voorkeur.

```
decodeDataUri('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De gegevens-URI-tekenreeks die moet worden gedecodeerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binair-voor-data-uri*> | Tekenreeks | De binaire versie voor een gegevens-URI-tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt de binaire versie voor deze gegevens URI geretourneerd:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

En geeft dit resultaat als resultaat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retourneer een tekenreeks die vluchttekens vervangt door gedecodeerde versies.

```
decodeUriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De string met de escape characters om te decoderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerd-uri*> | Tekenreeks | De bijgewerkte tekenreeks met de gedecodeerde vluchttekens |
||||

*Voorbeeld*

In dit voorbeeld worden de escape-tekens in deze tekenreeks vervangen door gedecodeerde versies:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

En geeft dit resultaat als resultaat:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Retourneer het gehele getalresultaat van het delen van twee getallen.
Zie [mod()](#mod)om het restresultaat te krijgen.

```
div(<dividend>, <divisor>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Dividend*> | Ja | Geheel getal of zweven | Het getal dat moet worden gedeeld door *de deler* |
| <*Deler*> | Ja | Geheel getal of zweven | Het getal dat het *dividend*verdeelt, maar niet 0 kan zijn |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*quotiëntresultaat*> | Geheel getal | Het gehele getal van het delen van het eerste getal door het tweede getal |
||||

*Voorbeeld*

Beide voorbeelden delen het eerste getal door het tweede getal:

```
div(10, 5)
div(11, 5)
```

En geef dit resultaat terug:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>codeUriComponent

Retourneer een door een uniforme resource-id (URI) gecodeerde versie voor een tekenreeks door URL-onveilige tekens te vervangen door vluchttekens.
Overweeg [om uriComponent()](#uriComponent) `encodeUriComponent()`te gebruiken in plaats van .
Hoewel beide functies op `uriComponent()` dezelfde manier werken, heeft de voorkeur.

```
encodeUriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die moet worden geconverteerd naar uri-gecodeerde indeling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gecodeerd-uri*> | Tekenreeks | De URI-gecodeerde tekenreeks met vluchttekens |
||||

*Voorbeeld*

In dit voorbeeld wordt een uri-gecodeerde versie voor deze tekenreeks gesanmeerd:

```
encodeUriComponent('https://contoso.com')
```

En geeft dit resultaat als resultaat:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>leeg

Controleer of een verzameling leeg is.
Retourneer waar wanneer de verzameling leeg is, of retouronwaar wanneer deze niet leeg is.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks, array of object | De collectie om te controleren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourtrue wanneer de collectie leeg is. Retouronwaar wanneer u niet leeg bent. |
||||

*Voorbeeld*

In deze voorbeelden wordt gecontroleerd of de opgegeven verzamelingen leeg zijn:

```
empty('')
empty('abc')
```

En geeft deze resultaten als resultaat:

* Eerste voorbeeld: Geeft een lege tekenreeks `true`door, zodat de functie terugkeert .
* Tweede voorbeeld: Passeert de tekenreeks "abc", zodat de functie terugkeert `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Controleer of een tekenreeks eindigt met een specifieke subtekenreeks.
Retourneer waar wanneer de subtekenreeks wordt gevonden of retourneer false wanneer deze niet is gevonden.
Deze functie is niet hoofdlettergevoelig.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks om te controleren |
| <*zoekTekst*> | Ja | Tekenreeks | De afsluitende subtekenreeks om te zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneer waar wanneer de afsluitende subtekenreeks wordt gevonden. Retouronwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voorbeeld wordt gecontroleerd of de tekenreeks 'hallo wereld' eindigt met de tekenreeks 'wereld':

```
endsWith('hello world', 'world')
```

En geeft dit resultaat als resultaat:`true`

*Voorbeeld 2*

In dit voorbeeld wordt gecontroleerd of de tekenreeks "hello world" eindigt met de tekenreeks 'universum':

```
endsWith('hello world', 'universe')
```

En geeft dit resultaat als resultaat:`false`

<a name="equals"></a>

### <a name="equals"></a>equals

Controleer of beide waarden, expressies of objecten gelijkwaardig zijn.
Retourtrue wanneer beide gelijkwaardig zijn, of false terugkeren wanneer ze niet gelijkwaardig zijn.

```
equals('<object1>', '<object2>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*object1*>, *object2* <> | Ja | Verschillende | De waarden, expressies of objecten die u wilt vergelijken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Return true wanneer beide gelijkwaardig zijn. Retouronwaar wanneer niet gelijkwaardig. |
||||

*Voorbeeld*

In deze voorbeelden wordt nagegaan of de opgegeven ingangen gelijkwaardig zijn.

```
equals(true, 1)
equals('abc', 'abcd')
```

En geeft deze resultaten als resultaat:

* Eerste voorbeeld: Beide waarden zijn gelijkwaardig, zodat de functie terugkeert `true`.
* Tweede voorbeeld: Beide waarden zijn niet gelijkwaardig, dus de functie keert terug. `false`

<a name="first"></a>

### <a name="first"></a>Eerste

Retourneer het eerste item van een tekenreeks of array.

```
first('<collection>')
first([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks of array | De collectie waar het eerste item te vinden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*eerste collectie-item*> | Alle | Het eerste item in de collectie |
||||

*Voorbeeld*

In deze voorbeelden vindt u het eerste item in deze collecties:

```
first('hello')
first(createArray(0, 1, 2))
```

En geef deze resultaten terug:

* Eerste voorbeeld:`"h"`
* Tweede voorbeeld:`0`

<a name="float"></a>

### <a name="float"></a>float

Converteer een tekenreeksversie voor een zwevend-puntnummer naar een daadwerkelijk zwevend puntnummer.
U deze functie alleen gebruiken wanneer u aangepaste parameters doorgeeft aan een app, bijvoorbeeld een logische app of -stroom.

```
float('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks met een geldig zwevend-puntnummer om te converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*float-waarde*> | Drijvend | Het zwevende puntnummer voor de opgegeven tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeksversie voor dit zwevende puntnummer gemaakt:

```
float('10.333')
```

En geeft dit resultaat als resultaat:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>opmaakDateTime

Retourneer een tijdstempel in de opgegeven indeling.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*geformatteerde tijdstempel*> | Tekenreeks | De bijgewerkte tijdstempel in de opgegeven indeling |
||||

*Voorbeeld*

In dit voorbeeld wordt een tijdstempel geconverteerd naar de opgegeven indeling:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

En geeft dit resultaat als resultaat:`"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiWaarden

Retourneer een array met waarden die overeenkomen met een sleutelnaam in de *formuliergegevens* of *formuliergecodeerde* uitvoer van een actie.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De actie waarvan de uitvoer de gewenste waarde heeft |
| <*Sleutel*> | Ja | Tekenreeks | De naam voor de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*array-met-key-waarden*>] | Matrix | Een array met alle waarden die overeenkomen met de opgegeven sleutel |
||||

*Voorbeeld*

In dit voorbeeld wordt een array gemaakt van de waarde van de sleutel 'Onderwerp' in de formuliergegevens of formuliergecodeerde uitvoer van de opgegeven actie:

```
formDataMultiValues('Send_an_email', 'Subject')
```

En retourneert de onderwerptekst in een array, bijvoorbeeld:`["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataWaarde

Retourneer één waarde die overeenkomt met een sleutelnaam in de *formuliergegevens* of *formuliergecodeerde* uitvoer van een actie.
Als de functie meer dan één overeenkomst vindt, wordt er met de functie een fout gegenereerd.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De actie waarvan de uitvoer de gewenste waarde heeft |
| <*Sleutel*> | Ja | Tekenreeks | De naam voor de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*sleutelwaarde*> | Tekenreeks | De waarde in de opgegeven sleutel  |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks gemaakt op basis van de waarde van de sleutel 'Onderwerp' in de formuliergegevens of formuliergecodeerde uitvoer van de opgegeven actie:

```
formDataValue('Send_an_email', 'Subject')
```

En geeft de onderwerptekst als tekenreeks terug, bijvoorbeeld:`"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>opmaakNummer

Retourneer een getal als tekenreeks die is gebaseerd op de opgegeven notatie.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Nummer*> | Ja | Geheel getal of dubbel | De waarde die u wilt opmaken. |
| <*Formaat*> | Ja | Tekenreeks | Een samengestelde opmaaktekenreeks die de indeling aangeeft die u wilt gebruiken. Zie [Standaard numerieke opmaaktekenreeksen](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings)die worden ondersteund door `number.ToString(<format>, <locale>)`. |
| <*Locale*> | Nee | Tekenreeks | De locale te gebruiken `number.ToString(<format>, <locale>)`als ondersteund door . Als dit niet is `en-us`opgegeven, is de standaardwaarde . |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*opgemaakt-nummer*> | Tekenreeks | Het opgegeven getal als tekenreeks in de door u opgegeven notatie. U deze retourwaarde `int` `float`naar een of. |
||||

*Voorbeeld 1*

Stel dat u het `1234567890`getal wilt opmaken. In dit voorbeeld wordt dat getal opgemaakt als de tekenreeks "1.234.567.890,00".

```
formatNumber(1234567890, '{0:0,0.00}', 'en-us')
```

*Voorbeeld 2"

Stel dat u het `1234567890`getal wilt opmaken. In dit voorbeeld wordt het getal opgemaakt met de tekenreeks "1.234.567.890,00".

```
formatNumber(1234567890, '{0:0,0.00}', 'is-is')
```

*Voorbeeld 3*

Stel dat u het `17.35`getal wilt opmaken. In dit voorbeeld wordt het getal opgemaakt met de tekenreeks '$17.35'.

```
formatNumber(17.36, 'C2')
```

*Voorbeeld 4*

Stel dat u het `17.35`getal wilt opmaken. In dit voorbeeld wordt het getal opgemaakt met de tekenreeks "17,35 kr".

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retourneer de huidige tijdstempel plus de opgegeven tijdeenheden.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Interval*> | Ja | Geheel getal | Het aantal opgegeven tijdeenheden dat moet worden toegevoegd |
| <*timeUnit*> | Ja | Tekenreeks | De eenheid van de tijd te gebruiken met *interval*: "Tweede", "Minuut", "Uur", "Dag", "Week", "Maand", "Jaar" |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De huidige tijdstempel plus het opgegeven aantal tijdeenheden |
||||

*Voorbeeld 1*

Stel dat de huidige tijdstempel "2018-03-01T00:00:00.00000000Z" is.
In dit voorbeeld worden vijf dagen aan die tijdstempel toegevoegd:

```
getFutureTime(5, 'Day')
```

En geeft dit resultaat als resultaat:`"2018-03-06T00:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat de huidige tijdstempel "2018-03-01T00:00:00.00000000Z" is.
In dit voorbeeld worden vijf dagen toegevoegd en wordt het resultaat omgezet in de indeling 'D':

```
getFutureTime(5, 'Day', 'D')
```

En geeft dit resultaat als resultaat:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retourneer de huidige tijdstempel minus de opgegeven tijdeenheden.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Interval*> | Ja | Geheel getal | Het aantal opgegeven tijdeenheden dat moet worden afgetrokken |
| <*timeUnit*> | Ja | Tekenreeks | De eenheid van de tijd te gebruiken met *interval*: "Tweede", "Minuut", "Uur", "Dag", "Week", "Maand", "Jaar" |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De huidige tijdstempel minus het opgegeven aantal tijdeenheden |
||||

*Voorbeeld 1*

Stel dat de huidige tijdstempel "2018-02-01T00:00:00.00000000Z" is.
In dit voorbeeld worden vijf dagen van die tijdstempel afgetrokken:

```
getPastTime(5, 'Day')
```

En geeft dit resultaat als resultaat:`"2018-01-27T00:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat de huidige tijdstempel "2018-02-01T00:00:00.00000000Z" is.
In dit voorbeeld worden vijf dagen afgetrokken en wordt het resultaat omgezet in de notatie 'D':

```
getPastTime(5, 'Day', 'D')
```

En geeft dit resultaat als resultaat:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Controleer of de eerste waarde groter is dan de tweede waarde.
Retourneer waar wanneer de eerste waarde meer is, of retouronwaar wanneer minder.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Geheel getal, zweven of tekenreeks | De eerste waarde die controleert of groter is dan de tweede waarde |
| <*Compareto*> | Ja | Geheel getal, zweven of tekenreeks, respectievelijk | De vergelijkingswaarde |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneer waar wanneer de eerste waarde groter is dan de tweede waarde. Retourneer false wanneer de eerste waarde gelijk is aan of lager is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voorbeelden wordt gecontroleerd of de eerste waarde groter is dan de tweede waarde:

```
greater(10, 5)
greater('apple', 'banana')
```

En geef deze resultaten terug:

* Eerste voorbeeld:`true`
* Tweede voorbeeld:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Controleer of de eerste waarde groter is dan of gelijk is aan de tweede waarde.
Retourneer waar wanneer de eerste waarde groter of gelijk is, of retourneer false wanneer de eerste waarde kleiner is.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Geheel getal, zweven of tekenreeks | De eerste waarde die controleert of groter is dan of gelijk is aan de tweede waarde |
| <*Compareto*> | Ja | Geheel getal, zweven of tekenreeks, respectievelijk | De vergelijkingswaarde |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneer waar wanneer de eerste waarde groter is dan of gelijk is aan de tweede waarde. Retourneer false wanneer de eerste waarde lager is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voorbeelden wordt gecontroleerd of de eerste waarde groter of gelijk is aan de tweede waarde:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

En geef deze resultaten terug:

* Eerste voorbeeld:`true`
* Tweede voorbeeld:`false`

<a name="guid"></a>

### <a name="guid"></a>guid

Een wereldwijd unieke id (GUID) genereren als tekenreeks, bijvoorbeeld "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

U ook een andere indeling voor de GUID opgeven dan de standaardindeling ,,D, namelijk 32 cijfers gescheiden door koppeltekens.

```
guid('<format>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Formaat*> | Nee | Tekenreeks | Een enkele [indelingspecificer](https://msdn.microsoft.com/library/97af8hh4) voor de geretourneerde GUID. Standaard is de indeling 'D', maar u 'N', 'D', 'B', 'P' of 'X' gebruiken. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*GUID-waarde*> | Tekenreeks | Een willekeurig gegenereerde GUID |
||||

*Voorbeeld*

In dit voorbeeld wordt dezelfde GUID gegenereerd, maar als 32 cijfers, gescheiden door koppeltekens en tussen haakjes:

```
guid('P')
```

En geeft dit resultaat als resultaat:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Controleer of een expressie waar of onwaar is.
Retourneer een opgegeven waarde op basis van het resultaat.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Expressie*> | Ja | Booleaans | De expressie om te controleren |
| <*valueIfTrue*> | Ja | Alle | De waarde die moet worden retournerd wanneer de expressie waar is |
| <*waardeAlsFalse*> | Ja | Alle | De waarde die moet worden geretourneerd wanneer de expressie vals is |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*opgegeven-rendementswaarde*> | Alle | De opgegeven waarde die retourneert op basis van of de expressie waar of onwaar is |
||||

*Voorbeeld*

Dit voorbeeld `"yes"` wordt geretourneerd omdat de opgegeven expressie true retourneert.
Anders wordt het `"no"`voorbeeld geretourneerd:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexVan

Geef de beginpositie of indexwaarde voor een subtekenreeks terug.
Deze functie is niet hoofdlettergevoelig en indexen beginnen met het getal 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die de subtekenreeks moet vinden |
| <*zoekTekst*> | Ja | Tekenreeks | De subtekenreeks die u moet zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*indexwaarde*>| Geheel getal | De beginpositie of indexwaarde voor de opgegeven subtekenreeks. <p>Als de tekenreeks niet wordt gevonden, voert u het getal -1 terug. |
||||

*Voorbeeld*

In dit voorbeeld wordt de beginindexwaarde voor de subtekenreeks 'wereld' gevonden in de tekenreeks 'hallo wereld':

```
indexOf('hello world', 'world')
```

En geeft dit resultaat als resultaat:`6`

<a name="int"></a>

### <a name="int"></a>int

Retourneer de gehele versie voor een tekenreeks.

```
int('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die u wilt converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*integer-resultaat*> | Geheel getal | De gehele versie voor de opgegeven tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt een gehele versie gemaakt voor de tekenreeks "10":

```
int('10')
```

En geeft dit resultaat als resultaat:`10`

<a name="item"></a>

### <a name="item"></a>item

Wanneer u in een herhalende actie over een array wordt gebruikt, retourneert u het huidige item in de array tijdens de huidige iteratie van de actie.
U ook de waarden uit de eigenschappen van dat item halen.

```
item()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*huidig array-item*> | Alle | Het huidige item in de array voor de huidige iteratie van de actie |
||||

*Voorbeeld*

In dit `body` voorbeeld wordt het element uit het huidige bericht voor de actie 'Send_an_email' in de huidige iteratie van een voor elke lus opgehaald:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Items

Retourneer het huidige item van elke cyclus in een lus voor elke lus.
Gebruik deze functie in de voorelke lus.

```
items('<loopName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*loopNaam*> | Ja | Tekenreeks | De naam voor de voor-elke lus |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Item*> | Alle | Het item uit de huidige cyclus in de opgegeven lus voor elke lus |
||||

*Voorbeeld*

In dit voorbeeld wordt het huidige item uit de opgegeven lus voor elke lus opgehaald:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iteratieIndexes

Geef de indexwaarde voor de huidige iteratie in een lus Tot terug. U deze functie gebruiken in geneste tot lussen. 

```
iterationIndexes('<loopName>')
```

| Parameter | Vereist | Type | Beschrijving | 
| --------- | -------- | ---- | ----------- | 
| <*loopNaam*> | Ja | Tekenreeks | De naam voor de lus Tot | 
||||| 

| Retourwaarde | Type | Beschrijving | 
| ------------ | ---- | ----------- | 
| <*Index*> | Geheel getal | De indexwaarde voor de huidige iteratie binnen de opgegeven lus Tot | 
|||| 

*Voorbeeld* 

In dit voorbeeld wordt een tegenvariabele en stappen die tijdens elke iteratie in een lus Tot worden variabel. In het voorbeeld wordt ook een variabele die de huidige index voor elke iteratie bijhoudt. In de lus Tot wordt tijdens elke iteratie de teller in het voorbeeld verhoogd en wordt de tegenwaarde aan de huidige indexwaarde toewijst en wordt de teller vervolgens verhoogd. Terwijl in de lus, verwijst dit voorbeeld naar `iterationIndexes` de huidige iteratie-index met behulp van de functie:

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

De Json-typewaarde of object (JavaScript Object Notatie) voor een tekenreeks of XML retourneren.

```
json('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks of XML | De tekenreeks of XML die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*JSON-resultaat*> | Json-native type of object | De JSON-native typewaarde of -object voor de opgegeven tekenreeks of XML. Als de tekenreeks null is, retourneert de functie een leeg object. |
||||

*Voorbeeld 1*

In dit voorbeeld wordt deze tekenreeks geconverteerd naar de JSON-waarde:

```
json('[1, 2, 3]')
```

En geeft dit resultaat als resultaat:`[1, 2, 3]`

*Voorbeeld 2*

In dit voorbeeld wordt deze tekenreeks geconverteerd naar JSON:

```
json('{"fullName": "Sophia Owen"}')
```

En geeft dit resultaat als resultaat:

```
{
  "fullName": "Sophia Owen"
}
```

*Voorbeeld 3*

In dit voorbeeld wordt deze XML geconverteerd naar JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

En geeft dit resultaat als resultaat:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>Snijpunt

Retourneer een verzameling met *alleen* de algemene items in de opgegeven verzamelingen.
Als u wilt worden weergegeven in het resultaat, moet een item worden weergegeven in alle verzamelingen die aan deze functie zijn doorgegeven.
Als een of meer items dezelfde naam hebben, wordt het laatste item met die naam in het resultaat weergegeven.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*collectie1*>, <*collectie2*>, ... | Ja | Array of object, maar niet beide | De collecties van waaruit u *alleen* de algemene items wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gemeenschappelijke items*> | Array of Object, respectievelijk | Een verzameling met alleen de algemene items in de opgegeven verzamelingen |
||||

*Voorbeeld*

In dit voorbeeld worden de algemene items in deze arrays gevonden:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

En retourneert een array met *alleen* deze items:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Retourneer een tekenreeks met alle items uit een array en elk teken wordt gescheiden door een *scheidingsteken*.

```
join([<collection>], '<delimiter>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Matrix | De array waaraan de items moeten worden verbonden |
| <*Scheidingsteken*> | Ja | Tekenreeks | De scheidingsteken die tussen elk teken in de resulterende tekenreeks wordt weergegeven |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | Tekenreeks | De resulterende tekenreeks die is gemaakt van alle items in de opgegeven array |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks aanmaakt van alle items in deze array met het opgegeven teken als de scheidingsteken:

```
join(createArray('a', 'b', 'c'), '.')
```

En geeft dit resultaat als resultaat:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>Laatste

Retourneer het laatste item uit een verzameling.

```
last('<collection>')
last([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks of array | De collectie waar het laatste item te vinden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*laatste collectie-item*> | Tekenreeks of array, respectievelijk | Het laatste item in de collectie |
||||

*Voorbeeld*

In deze collecties vindt u het laatste item in deze collecties:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

En geeft deze resultaten als resultaat:

* Eerste voorbeeld:`"d"`
* Tweede voorbeeld:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Geef de beginpositie of indexwaarde voor het laatst voorkomen van een subtekenreeks terug.
Deze functie is niet hoofdlettergevoelig en indexen beginnen met het getal 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die de subtekenreeks moet vinden |
| <*zoekTekst*> | Ja | Tekenreeks | De subtekenreeks die u moet zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*einde-indexwaarde*> | Geheel getal | De beginpositie of indexwaarde voor het laatste optreden van de opgegeven subtekenreeks. <p>Als de tekenreeks niet wordt gevonden, voert u het getal -1 terug. |
||||

*Voorbeeld*

In dit voorbeeld wordt de beginindexwaarde gevonden voor het laatste optreden van de subtekenreeks 'wereld' in de tekenreeks 'hallo wereld':

```
lastIndexOf('hello world', 'world')
```

En geeft dit resultaat als resultaat:`6`

<a name="length"></a>

### <a name="length"></a>lengte

Retourneer het aantal items in een verzameling.

```
length('<collection>')
length([<collection>])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks of array | De collectie met de items om te tellen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*lengte of telling*> | Geheel getal | Het aantal items in de collectie |
||||

*Voorbeeld*

Deze voorbeelden tellen het aantal items in deze collecties:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

En geef dit resultaat terug:`4`

<a name="less"></a>

### <a name="less"></a>less

Controleer of de eerste waarde lager is dan de tweede waarde.
Retourneer waar wanneer de eerste waarde kleiner is, of retourneer false wanneer de eerste waarde meer is.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Geheel getal, zweven of tekenreeks | De eerste waarde die controleert of minder dan de tweede waarde |
| <*Compareto*> | Ja | Geheel getal, zweven of tekenreeks, respectievelijk | Het vergelijkingsitem |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneer waar wanneer de eerste waarde kleiner is dan de tweede waarde. Retourneer false wanneer de eerste waarde gelijk is aan of groter is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voorbeelden wordt gecontroleerd of de eerste waarde kleiner is dan de tweede waarde.

```
less(5, 10)
less('banana', 'apple')
```

En geef deze resultaten terug:

* Eerste voorbeeld:`true`
* Tweede voorbeeld:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Controleer of de eerste waarde lager is dan of gelijk is aan de tweede waarde.
Retourneer waar wanneer de eerste waarde kleiner is dan of gelijk, of retourneer false wanneer de eerste waarde meer is.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Geheel getal, zweven of tekenreeks | De eerste waarde die controleert of minder dan of gelijk is aan de tweede waarde |
| <*Compareto*> | Ja | Geheel getal, zweven of tekenreeks, respectievelijk | Het vergelijkingsitem |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneer waar wanneer de eerste waarde kleiner is dan of gelijk is aan de tweede waarde. Retourneer false wanneer de eerste waarde groter is dan de tweede waarde. |
||||

*Voorbeeld*

In deze voorbeelden wordt gecontroleerd of de eerste waarde kleiner of gelijk is aan de tweede waarde.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

En geef deze resultaten terug:

* Eerste voorbeeld:`true`
* Tweede voorbeeld:`false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>lijstCallbackUrl

Retourneer de "callback URL" die een trigger of actie aanroept.
Deze functie werkt alleen met triggers en acties voor de **httpwebhook-** en **ApiConnectionWebhook-connectortypen,** maar niet met de typen **Handmatige**, **Herhalings-,** **HTTP-** en **APIConnection-elementen.**

```
listCallbackUrl()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Tekenreeks | De terugbel-URL voor een trigger of actie |
||||

*Voorbeeld*

In dit voorbeeld wordt een voorbeeld-terugroep-URL weergegeven die deze functie kan retourneren:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>Max

Retourneer de hoogste waarde uit een lijst of array met getallen die aan beide uiteinden inclusief zijn.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*nummer 1*>, <*nummer 2*>, ... | Ja | Geheel getal, zweven of beide | De set getallen waarvan u de hoogste waarde wilt |
| [<*nummer1*>, <*nummer2*>, ...] | Ja | Array - Geheel getal, zweven of beide | De array met getallen waarvan u de hoogste waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*max-waarde*> | Geheel getal of zweven | De hoogste waarde in de opgegeven array of set getallen |
||||

*Voorbeeld*

Met deze voorbeelden wordt de hoogste waarde opgehaald uit de set getallen en de array:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

En geef dit resultaat terug:`3`

<a name="min"></a>

### <a name="min"></a>min.

Retourneer de laagste waarde uit een reeks getallen of een array.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*nummer 1*>, <*nummer 2*>, ... | Ja | Geheel getal, zweven of beide | De set getallen waarvan u de laagste waarde wilt |
| [<*nummer1*>, <*nummer2*>, ...] | Ja | Array - Geheel getal, zweven of beide | De array met getallen waarvan u de laagste waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*min-waarde*> | Geheel getal of zweven | De laagste waarde in de opgegeven set getallen of opgegeven array |
||||

*Voorbeeld*

Met deze voorbeelden wordt de laagste waarde in de set getallen en de array:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

En geef dit resultaat terug:`1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Retourneer de rest van het delen van twee getallen.
Zie [div()](#div)om het gehele resultaat te krijgen.

```
mod(<dividend>, <divisor>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Dividend*> | Ja | Geheel getal of zweven | Het getal dat moet worden gedeeld door *de deler* |
| <*Deler*> | Ja | Geheel getal of zweven | Het getal dat het *dividend*verdeelt, maar niet 0 kan zijn. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*modulo-resultaat*> | Geheel getal of zweven | De rest van het delen van het eerste getal door het tweede getal |
||||

*Voorbeeld*

In dit voorbeeld wordt het eerste getal verdeeld door het tweede getal:

```
mod(3, 2)
```

En geef dit resultaat terug:`1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Retourneer het product van het vermenigvuldigen van twee getallen.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*vermenigvuldigingsvermenigvuldiging1*> | Ja | Geheel getal of zweven | Het getal dat u moet vermenigvuldigen *met vermenigvuldiging2* |
| <*multiplicand2*> | Ja | Geheel getal of zweven | Het getal dat *vermenigvuldigt en 1* |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*productresultaat*> | Geheel getal of zweven | Het product vermenigvuldigt het eerste getal met het tweede getal |
||||

*Voorbeeld*

In deze voorbeelden wordt het eerste getal gemultiple door het tweede getal:

```
mul(1, 2)
mul(1.5, 2)
```

En geef deze resultaten terug:

* Eerste voorbeeld:`2`
* Tweede voorbeeld`3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Retourneer de behuizing voor een specifiek onderdeel in de uitvoer van een actie die meerdere onderdelen heeft.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de actie met uitvoer met meerdere onderdelen |
| <*Index*> | Ja | Geheel getal | De indexwaarde voor het onderdeel dat u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Lichaam*> | Tekenreeks | Het lichaam voor het opgegeven deel |
||||

<a name="not"></a>

### <a name="not"></a>not

Controleer of een expressie vals is.
Retourneer waar wanneer de expressie onwaar is, of keer vals terug wanneer deze waar is.

```json
not(<expression>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Expressie*> | Ja | Booleaans | De expressie om te controleren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneer waar wanneer de expressie onjuist is. Retourneer false wanneer de expressie waar is. |
||||

*Voorbeeld 1*

In deze voorbeelden wordt gecontroleerd of de opgegeven expressies onjuist zijn:

```json
not(false)
not(true)
```

En geef deze resultaten terug:

* Eerste voorbeeld: de expressie is onjuist, zodat de functie terugkeert `true`.
* Tweede voorbeeld: de expressie is waar, zodat de functie wordt geretourneerd `false`.

*Voorbeeld 2*

In deze voorbeelden wordt gecontroleerd of de opgegeven expressies onjuist zijn:

```json
not(equals(1, 2))
not(equals(1, 1))
```

En geef deze resultaten terug:

* Eerste voorbeeld: de expressie is onjuist, zodat de functie terugkeert `true`.
* Tweede voorbeeld: de expressie is waar, zodat de functie wordt geretourneerd `false`.

<a name="or"></a>

### <a name="or"></a>of

Controleer of ten minste één expressie waar is.
Retourneer waar wanneer ten minste één uitdrukking waar is, of keer false terug wanneer alle onjuist zijn.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*uitdrukking1*>, <*expressie2*>, ... | Ja | Booleaans | De expressies die u moet controleren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar | Booleaans | Retourneer waar wanneer ten minste één expressie waar is. Retourneer false wanneer alle expressies vals zijn. |
||||

*Voorbeeld 1*

In deze voorbeelden wordt gecontroleerd of ten minste één expressie waar is:

```json
or(true, false)
or(false, false)
```

En geef deze resultaten terug:

* Eerste voorbeeld: Ten minste één expressie is `true`waar, zodat de functie terugkeert .
* Tweede voorbeeld: Beide expressies zijn `false`vals, dus de functie keert terug.

*Voorbeeld 2*

In deze voorbeelden wordt gecontroleerd of ten minste één expressie waar is:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

En geef deze resultaten terug:

* Eerste voorbeeld: Ten minste één expressie is `true`waar, zodat de functie terugkeert .
* Tweede voorbeeld: Beide expressies zijn `false`vals, dus de functie keert terug.

<a name="outputs"></a>

### <a name="outputs"></a>Uitgangen

Retourneer de uitvoer van een actie bij runtime. Gebruik deze functie `actionOutputs()`in plaats van `outputs()` in de Logic App Designer. Hoewel beide functies op `outputs()` dezelfde manier werken, heeft de voorkeur.

```
outputs('<actionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Tekenreeks | De naam voor de gewenste uitvoer van de actie |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | -----| ----------- |
| <*Output*> | Tekenreeks | De uitvoer van de opgegeven actie |
||||

*Voorbeeld*

Dit voorbeeld krijgt de output `Get user`van de Twitter-actie:

```
outputs('Get_user')
```

En geeft dit resultaat als resultaat:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Retourneer de waarde voor een parameter die wordt beschreven in de werkstroomdefinitie.

```
parameters('<parameterName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*parameterNaam*> | Ja | Tekenreeks | De naam voor de parameter waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*parameterwaarde*> | Alle | De waarde voor de opgegeven parameter |
||||

*Voorbeeld*

Stel dat u deze JSON-waarde hebt:

```json
{
  "fullName": "Sophia Owen"
}
```

In dit voorbeeld wordt de waarde voor de opgegeven parameter vermeld:

```
parameters('fullName')
```

En geeft dit resultaat als resultaat:`"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Retourneer een willekeurig geheel getal uit een opgegeven bereik, dat alleen aan het begineinde inclusief is.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*minWaarde*> | Ja | Geheel getal | Het laagste gehele getal in het bereik |
| <*maxWaarde*> | Ja | Geheel getal | Het gehele getal dat het hoogste gehele getal volgt in het bereik dat de functie kan retourneren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*willekeurig resultaat*> | Geheel getal | Het willekeurige gehele getal dat wordt geretourneerd uit het opgegeven bereik |
||||

*Voorbeeld*

In dit voorbeeld wordt een willekeurig geheel getal uit het opgegeven bereik verwijderd, met uitzondering van de maximale waarde:

```
rand(1, 5)
```

En geeft als resultaat een `1`van `2` `3`deze getallen als resultaat: , , of`4`

<a name="range"></a>

### <a name="range"></a>Bereik

Retourneer een gehele gehele array die vertrekt vanaf een opgegeven geheel getal.

```
range(<startIndex>, <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Startindex*> | Ja | Geheel getal | Een gehele waarde die de array start als het eerste item |
| <*Tellen*> | Ja | Geheel getal | Het aantal gehele getallen in de array |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*> van bereikresultaat]* | Matrix | De array met gehele getallen die beginnen met de opgegeven index |
||||

*Voorbeeld*

In dit voorbeeld wordt een gehele array gemaakt die begint met de opgegeven index en het opgegeven aantal gehele getallen heeft:

```
range(1, 4)
```

En geeft dit resultaat als resultaat:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>vervangen

Vervang een subtekenreeks door de opgegeven tekenreeks en retourneer de resultaattekenreeks. Deze functie is hoofdlettergevoelig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die de subtekenreeks moet vervangen |
| <*oldTekst*> | Ja | Tekenreeks | De subtekenreeks die moet worden vervangen |
| <*newText*> | Ja | Tekenreeks | De vervangende tekenreeks |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkte tekst*> | Tekenreeks | De bijgewerkte tekenreeks na het vervangen van de subtekenreeks <p>Als de subtekenreeks niet wordt gevonden, retourneert u de oorspronkelijke tekenreeks. |
||||

*Voorbeeld*

In dit voorbeeld wordt de subtekenreeks 'oude' in 'de oude tekenreeks' gevonden en wordt 'oud' vervangen door 'nieuw':

```
replace('the old string', 'old', 'new')
```

En geeft dit resultaat als resultaat:`"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>verwijderenEigenschap

Verwijder een eigenschap uit een object en retourneer het bijgewerkte object. Als de eigenschap die u probeert te verwijderen niet bestaat, wordt het oorspronkelijke object als resultaat geretourneerd.

```
removeProperty(<object>, '<property>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object van waaruit u een eigenschap wilt verwijderen |
| <*Eigenschap*> | Ja | Tekenreeks | De naam voor de eigenschap te verwijderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt object*> | Object | Het bijgewerkte JSON-object zonder de opgegeven eigenschap |
||||

Als u een onderliggende eigenschap uit een bestaande eigenschap wilt verwijderen, gebruikt u de als volgt:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt verwijderen |
| <*bovenliggende eigenschap*> | Ja | Tekenreeks | De naam voor bovenliggende eigenschap met de onderliggende eigenschap die u wilt verwijderen |
| <*kind-eigendom*> | Ja | Tekenreeks | De naam voor de onderliggende eigenschap te verwijderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt object*> | Object | Het bijgewerkte JSON-object waarvan u de onderliggende eigenschap hebt verwijderd |
||||

*Voorbeeld 1*

In dit `middleName` voorbeeld wordt de eigenschap verwijderd van een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met de functie [JSON()](#json) en wordt het bijgewerkte object geretourneerd:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Hier is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Voorbeeld 2*

In dit `middleName` voorbeeld wordt `customerName` de onderliggende eigenschap verwijderd uit een bovenliggende eigenschap in een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met de functie [JSON()](#json) en wordt het bijgewerkte object geretourneerd:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Hier is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Retourneer de ingangen en uitvoer van alle acties die zich `For_each`binnen `Until`de `Scope` opgegeven actie bevinden, zoals een actie of actie. Deze functie is handig om de resultaten van een mislukte actie terug te sturen, zodat u uitzonderingen diagnosticeren en verwerken. Zie [Context en resultaten voor fouten opvragen voor](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)meer informatie.

```
result('<scopedActionName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ja | Tekenreeks | De naam van de scoped actie van waaruit de inputs en outputs van alle innerlijke acties kunnen worden teruggegeven |
||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*arrayobject*> | Arrayobject | Een array die arrays met ingangen en uitvoer bevat van elke actie die wordt weergegeven binnen de opgegeven actie met bereik |
||||

*Voorbeeld*

In dit voorbeeld worden de ingangen en uitvoer van elke `For_each` iteratie van `result()` een HTTP-actie binnen in een lus geretourneerd met behulp van de functie in de `Compose` actie:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Zo kan de voorbeeldarray kijken waar `outputs` het buitenste object de ingangen en uitvoer `For_each` bevat van elke iteratie van de acties binnen de actie.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>Eigenschapinstellen

Stel de waarde in voor de eigenschap van JSON-object en retourneer het bijgewerkte object. Als de eigenschap die u probeert in te stellen niet bestaat, wordt de eigenschap aan het object toegevoegd. Als u een nieuwe eigenschap wilt toevoegen, gebruikt u de functie [addProperty().](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt instellen |
| <*Eigenschap*> | Ja | Tekenreeks | De naam voor de bestaande of nieuwe eigenschap die moet worden ingesteld |
| <*Waarde*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

Als u de onderliggende eigenschap wilt instellen `setProperty()` in een onderliggend object, gebruikt u in plaats daarvan een geneste aanroep. Anders retourneert de functie alleen het onderliggende object als uitvoer.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Object*> | Ja | Object | Het JSON-object waarvan u de eigenschap wilt instellen |
| <*bovenliggende eigenschap*> | Ja | Tekenreeks | De naam voor bovenliggende eigenschap met de onderliggende eigenschap die u wilt instellen |
| <*kind-eigendom*> | Ja | Tekenreeks | De naam voor de onderliggende eigenschap die moet worden ingesteld |
| <*Waarde*> | Ja | Alle | De waarde die moet worden ingesteld voor de opgegeven eigenschap |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*bijgewerkt object*> | Object | Het bijgewerkte JSON-object waarvan u de eigenschap hebt ingesteld |
||||

*Voorbeeld 1*

In dit `surName` voorbeeld wordt de eigenschap ingesteld in een JSON-object, dat wordt geconverteerd van een tekenreeks naar JSON met behulp van de functie [JSON().](#json) De functie wijst de opgegeven waarde toe aan de eigenschap en retourneert het bijgewerkte object:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Hier is het huidige JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Voorbeeld 2*

In dit `surName` voorbeeld wordt `customerName` de onderliggende eigenschap voor de bovenliggende eigenschap in een JSON-object ingesteld, dat wordt omgezet van een tekenreeks naar JSON met de functie [JSON().](#json) De functie wijst de opgegeven waarde toe aan de eigenschap en retourneert het bijgewerkte object:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Hier is het huidige JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Hier is het bijgewerkte JSON-object:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>skip

Verwijder items uit de voorkant van een verzameling en retourneer *alle andere* items.

```
skip([<collection>], <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Matrix | De verzameling waarvan u items wilt verwijderen |
| <*Tellen*> | Ja | Geheel getal | Een positief geheel getal voor het aantal items dat aan de voorzijde moet worden verwijderd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*> van de bijgewerkte verzameling]* | Matrix | De bijgewerkte verzameling na het verwijderen van de opgegeven items |
||||

*Voorbeeld*

In dit voorbeeld wordt één item, het getal 0, verwijderd aan de voorzijde van de opgegeven array:

```
skip(createArray(0, 1, 2, 3), 1)
```

En retourneert deze array met de overige items:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Retourneer een array die subtekenreeksen bevat, gescheiden door komma's, op basis van het opgegeven tekens in de oorspronkelijke tekenreeks.

```
split('<text>', '<delimiter>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die moet worden gescheiden in subtekenreeksen op basis van de opgegeven scheidingsteken in de oorspronkelijke tekenreeks |
| <*Scheidingsteken*> | Ja | Tekenreeks | Het teken in de oorspronkelijke tekenreeks dat moet worden gebruikt als de scheidingsteken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Matrix | Een array die subtekenreeksen van de oorspronkelijke tekenreeks bevat, gescheiden door komma's |
||||

*Voorbeeld*

In dit voorbeeld wordt een array met subtekenreeksen van de opgegeven tekenreeks op basis van het opgegeven teken als de scheidingsteken:

```
split('a_b_c', '_')
```

En retourneert deze array als resultaat:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Breng het begin van de dag terug voor een tijdstempel.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De opgegeven tijdstempel, maar vanaf de nulurenmarkering voor de dag |
||||

*Voorbeeld*

In dit voorbeeld wordt het begin van de dag voor deze tijdstempel gevonden:

```
startOfDay('2018-03-15T13:30:30Z')
```

En geeft dit resultaat als resultaat:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Breng het begin van het uur terug voor een tijdstempel.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De opgegeven tijdstempel, maar vanaf de nulminutenmarkering voor het uur |
||||

*Voorbeeld*

In dit voorbeeld wordt het begin van het uur voor deze tijdstempel gevonden:

```
startOfHour('2018-03-15T13:30:30Z')
```

En geeft dit resultaat als resultaat:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retourneer het begin van de maand voor een tijdstempel.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De opgegeven tijdstempel, maar vanaf de eerste dag van de maand bij de nulurenmarkering |
||||

*Voorbeeld*

In dit voorbeeld wordt het begin van de maand voor deze tijdstempel geretourneerd:

```
startOfMonth('2018-03-15T13:30:30Z')
```

En geeft dit resultaat als resultaat:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Controleer of een tekenreeks begint met een specifieke subtekenreeks.
Retourneer waar wanneer de subtekenreeks wordt gevonden of retourneer false wanneer deze niet is gevonden.
Deze functie is niet hoofdlettergevoelig.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks om te controleren |
| <*zoekTekst*> | Ja | Tekenreeks | De starttekenreeks om te zoeken |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| waar of onwaar  | Booleaans | Retourneer waar wanneer de beginsubtekenreeks wordt gevonden. Retouronwaar wanneer deze niet is gevonden. |
||||

*Voorbeeld 1*

In dit voorbeeld wordt gecontroleerd of de tekenreeks 'hallo wereld' begint met de subtekenreeks 'hallo':

```
startsWith('hello world', 'hello')
```

En geeft dit resultaat als resultaat:`true`

*Voorbeeld 2*

In dit voorbeeld wordt gecontroleerd of de tekenreeks 'hallo wereld' begint met de subtekenreeks 'Groeten':

```
startsWith('hello world', 'greetings')
```

En geeft dit resultaat als resultaat:`false`

<a name="string"></a>

### <a name="string"></a>tekenreeks

Retourneer de tekenreeksversie voor een waarde.

```
string(<value>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Alle | De waarde die moet worden geconverteerd |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*tekenreekswaarde*> | Tekenreeks | De tekenreeksversie voor de opgegeven waarde |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de tekenreeksversie voor dit nummer gemaakt:

```
string(10)
```

En geeft dit resultaat als resultaat:`"10"`

*Voorbeeld 2*

In dit voorbeeld wordt een tekenreeks gemaakt voor het\\opgegeven JSON-object en wordt het backslash-teken ( ) gebruikt als een escape-teken voor het dubbele aanhalingsteken (").

```
string( { "name": "Sophie Owen" } )
```

En geeft dit resultaat als resultaat:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Retourneer het resultaat van het aftrekken van het tweede getal van het eerste getal.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Ja | Geheel getal of zweven | Het getal waaruit de *subtrahend* moet worden afgetrokken |
| <*subtrahend subtrahend*> | Ja | Geheel getal of zweven | Het getal dat moet worden afgetrokken van de *minuend* |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Resultaat*> | Geheel getal of zweven | Het resultaat van het aftrekken van het tweede getal van het eerste getal |
||||

*Voorbeeld*

In dit voorbeeld wordt het tweede getal afgetrokken van het eerste getal:

```
sub(10.3, .3)
```

En geeft dit resultaat als resultaat:`10`

<a name="substring"></a>

### <a name="substring"></a>Subtekenreeks

Tekens uit een tekenreeks retourneren, vanaf de opgegeven positie of index.
Indexwaarden beginnen met het getal 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks waarvan u de gewenste tekens wilt |
| <*Startindex*> | Ja | Geheel getal | Een positief getal gelijk aan of groter dan 0 dat u als startpositie of indexwaarde wilt gebruiken |
| <*Lengte*> | Ja | Geheel getal | Een positief aantal tekens dat u in de subtekenreeks wilt hebben |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*substringresultaat*> | Tekenreeks | Een subtekenreeks met het opgegeven aantal tekens, te beginnen bij de opgegeven indexpositie in de brontekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt een subtekenreeks van vijf tekens van de opgegeven tekenreeks gemaakt, uitgaande van de indexwaarde 6:

```
substring('hello world', 6, 5)
```

En geeft dit resultaat als resultaat:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractEnFromTime

Trek een aantal tijdeenheden af van een tijdstempel.
Zie ook [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks die de tijdstempel bevat |
| <*Interval*> | Ja | Geheel getal | Het aantal opgegeven tijdeenheden dat moet worden afgetrokken |
| <*timeUnit*> | Ja | Tekenreeks | De eenheid van de tijd te gebruiken met *interval*: "Tweede", "Minuut", "Uur", "Dag", "Week", "Maand", "Jaar" |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updated-timestamp updated-timestamp updated-timestamp updated-*> | Tekenreeks | De tijdstempel minus het opgegeven aantal tijdeenheden |
||||

*Voorbeeld 1*

In dit voorbeeld wordt één dag afgetrokken van deze tijdstempel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

En geeft dit resultaat als resultaat:`"2018-01-01T00:00:00:0000000Z"`

*Voorbeeld 2*

In dit voorbeeld wordt één dag afgetrokken van deze tijdstempel:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

En retourneert dit resultaat met behulp van de optionele "D"-indeling:`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>Nemen

Artikelen van de voorkant van een verzameling retourneren.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Collectie*> | Ja | Tekenreeks of array | De collectie waarvan je objecten wilt |
| <*Tellen*> | Ja | Geheel getal | Een positief geheel getal voor het aantal items dat u van voren wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*subset*> of [<*subset*>] | Tekenreeks of array, respectievelijk | Een tekenreeks of array met het opgegeven aantal items aan de voorkant van de oorspronkelijke verzameling |
||||

*Voorbeeld*

In deze voorbeelden wordt het opgegeven aantal items van de voorkant van deze verzamelingen opgehaald:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

En geef deze resultaten terug:

* Eerste voorbeeld:`"abc"`
* Tweede voorbeeld:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Teken

Retourneer `ticks` de eigenschapswaarde voor een opgegeven tijdstempel.
Een *teek* is een interval van 100 nanoseconde.

```
ticks('<timestamp>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tijdstempel*> | Ja | Tekenreeks | De tekenreeks voor een tijdstempel |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*teken-nummer*> | Geheel getal | Het aantal teken sinds de opgegeven tijdstempel |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retourneer een tekenreeks in kleine letters. Als een teken in de tekenreeks geen kleine versie heeft, blijft dat teken ongewijzigd in de geretourneerde tekenreeks.

```
toLower('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die moet terugkeren in kleine letters |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*kleine letters-tekst*> | Tekenreeks | De oorspronkelijke tekenreeks in kleine letters |
||||

*Voorbeeld*

In dit voorbeeld wordt deze tekenreeks omgezet in kleine letters:

```
toLower('Hello World')
```

En geeft dit resultaat als resultaat:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>Toupper

Een tekenreeks retourneren in hoofdletters. Als een teken in de tekenreeks geen hoofdletterversie heeft, blijft dat teken ongewijzigd in de geretourneerde tekenreeks.

```
toUpper('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die moet terugkeren in hoofdletters |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*hoofdletter-tekst*> | Tekenreeks | De oorspronkelijke tekenreeks in hoofdletters |
||||

*Voorbeeld*

In dit voorbeeld wordt deze tekenreeks omgezet in hoofdletters:

```
toUpper('Hello World')
```

En geeft dit resultaat als resultaat:`"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Trigger

Retourneer de uitvoer van een trigger bij runtime of waarden van andere JSON-naam-en-waardeparen, die u aan een expressie toewijzen.

* In de ingangen van een trigger retourneert deze functie de uitvoer van de vorige uitvoering.

* In de voorwaarde van een trigger retourneert deze functie de uitvoer van de huidige uitvoering.

Standaard verwijst de functie naar het volledige triggerobject, maar u optioneel een eigenschap opgeven waarvan u de waarde wilt hebben.
Ook heeft deze functie stenoversies beschikbaar, zie [triggerOutputs()](#triggerOutputs) en [triggerBody()](#triggerBody).

```
trigger()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Tekenreeks | De uitvoer van een trigger tijdens de runtime |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody triggerBody

Retourneer de `body` uitvoer van een trigger bij runtime.
Steno `trigger().outputs.body`voor .
Zie [trigger()](#trigger).

```
triggerBody()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | Tekenreeks | De `body` uitvoer van de trigger |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Retourneer een array met waarden die overeenkomen met een sleutelnaam in de *formuliergegevens* of *formuliergecodeerde* uitvoer van een trigger.

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Sleutel*> | Ja | Tekenreeks | De naam voor de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| [<*array-met-key-waarden*>] | Matrix | Een array met alle waarden die overeenkomen met de opgegeven sleutel |
||||

*Voorbeeld*

In dit voorbeeld wordt een array gemaakt van de sleutelwaarde 'feedUrl' in de formuliergegevens of vormgecodeerde uitvoer van een RSS-trigger:

```
triggerFormDataMultiValues('feedUrl')
```

En retourneert deze array als voorbeeld:`["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Retourneer een tekenreeks met één waarde die overeenkomt met een sleutelnaam in de *formuliergegevens* of *formuliergecodeerde* uitvoer van een trigger.
Als de functie meer dan één overeenkomst vindt, wordt er met de functie een fout gegenereerd.

```
triggerFormDataValue('<key>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Sleutel*> | Ja | Tekenreeks | De naam voor de sleutel waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*sleutelwaarde*> | Tekenreeks | De waarde in de opgegeven sleutel |
||||

*Voorbeeld*

In dit voorbeeld wordt een tekenreeks gemaakt van de sleutelwaarde 'feedUrl' in de formuliergegevens of vormgecodeerde uitvoer van een RSS-trigger:

```
triggerFormDataValue('feedUrl')
```

En retourneert deze tekenreeks als voorbeeld:`"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Retourneer het lichaam voor een specifiek onderdeel in de uitvoer van een trigger die meerdere delen heeft.

```
triggerMultipartBody(<index>)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Index*> | Ja | Geheel getal | De indexwaarde voor het onderdeel dat u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*Lichaam*> | Tekenreeks | Het lichaam voor het opgegeven onderdeel in de meerdelige uitgang van een trigger |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Retourneer de uitvoer van een trigger bij runtime of waarden van andere JSON-naam-en-waardeparen.
Steno `trigger().outputs`voor .
Zie [trigger()](#trigger).

```
triggerOutputs()
```

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Tekenreeks | De uitvoer van een trigger tijdens de runtime  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trim

Verwijder de voorloop- en naloopwitruimte uit een tekenreeks en retourneer de bijgewerkte tekenreeks.

```
trim('<text>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Ja | Tekenreeks | De tekenreeks die de voor- en achterloopwitruimte heeft om te verwijderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updatedText updatedText updatedText updatedText*> | Tekenreeks | Een bijgewerkte versie voor de oorspronkelijke tekenreeks zonder witruimte voor of achter te houden |
||||

*Voorbeeld*

In dit voorbeeld wordt de voorloop- en naloopruimte verwijderd uit de tekenreeks Hello World:

```
trim(' Hello World  ')
```

En geeft dit resultaat als resultaat:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unie

Retourneer een verzameling met *alle* items uit de opgegeven verzamelingen.
Als u wilt worden weergegeven in het resultaat, kan een item worden weergegeven in elke verzameling die aan deze functie wordt doorgegeven. Als een of meer items dezelfde naam hebben, wordt het laatste item met die naam in het resultaat weergegeven.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*collectie1*>, <*collectie2*>, ...  | Ja | Array of object, maar niet beide | De collecties van waaruit u *alle* items wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*updatedCollection updatedCollection updatedCollection updatedCollection*> | Array of Object, respectievelijk | Een verzameling met alle items uit de opgegeven verzamelingen - geen duplicaten |
||||

*Voorbeeld*

In dit voorbeeld worden *alle* items uit deze collecties opgehaald:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

En geeft dit resultaat als resultaat:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Retourneer een door een uniforme resource-id (URI) gecodeerde versie voor een tekenreeks door URL-onveilige tekens te vervangen door vluchttekens.
Gebruik deze functie in plaats van [encodeUriComponent()](#encodeUriComponent).
Hoewel beide functies op `uriComponent()` dezelfde manier werken, heeft de voorkeur.

```
uriComponent('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks die moet worden geconverteerd naar uri-gecodeerde indeling |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gecodeerd-uri*> | Tekenreeks | De URI-gecodeerde tekenreeks met vluchttekens |
||||

*Voorbeeld*

In dit voorbeeld wordt een uri-gecodeerde versie voor deze tekenreeks gesanmeerd:

```
uriComponent('https://contoso.com')
```

En geeft dit resultaat als resultaat:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Retourneer de binaire versie voor een URI-component (uniform resource identifier).

```
uriComponentToBinary('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De uri-gecodeerde tekenreeks die u wilt converteren |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*binair-voor-gecodeerd-uri*> | Tekenreeks | De binaire versie voor de URI-gecodeerde tekenreeks. De binaire inhoud is base64-gecodeerd `$content`en vertegenwoordigd door . |
||||

*Voorbeeld*

In dit voorbeeld wordt de binaire versie voor deze uri-gecodeerde tekenreeks gesanmeerd:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

En geeft dit resultaat als resultaat:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retourneer de tekenreeksversie voor een uri-tekenreeks (uniform resource identifier) en decodeer de uri-gecodeerde tekenreeks.

```
uriComponentToString('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De uri-gecodeerde tekenreeks om te decoderen |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*gedecodeerd-uri*> | Tekenreeks | De gedecodeerde versie voor de URI-gecodeerde tekenreeks |
||||

*Voorbeeld*

In dit voorbeeld wordt de gedecodeerde tekenreeksversie voor deze uri-gecodeerde tekenreeks gedecodeerd:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

En geeft dit resultaat als resultaat:`"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost (uriHost)

De `host` waarde voor een uniforme resource-id (URI) retourneren.

```
uriHost('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI `host` waarvan u waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*hostwaarde*> | Tekenreeks | De `host` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit `host` voorbeeld wordt de waarde voor deze URI gevonden:

```
uriHost('https://www.localhost.com:8080')
```

En geeft dit resultaat als resultaat:`"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

De `path` waarde voor een uniforme resource-id (URI) retourneren.

```
uriPath('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI `path` waarvan u waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*pad-waarde*> | Tekenreeks | De `path` waarde voor de opgegeven URI. Als `path` er geen waarde is, geeft u het teken "/" terug. |
||||

*Voorbeeld*

In dit `path` voorbeeld wordt de waarde voor deze URI gevonden:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

En geeft dit resultaat als resultaat:`"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

De `path` waarden `query` en waarden voor een uniforme resource-id (URI) retourneren.

```
uriPathAndQuery('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI `path` `query` wiens en waarden u wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*path-query-waarde*> | Tekenreeks | De `path` `query` waarden en waarden voor de opgegeven URI. Als `path` u geen waarde opgeeft, geeft u het teken "/" terug. |
||||

*Voorbeeld*

In dit `path` voorbeeld `query` worden de waarden en waarden voor deze URI gevonden:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

En geeft dit resultaat als resultaat:`"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

De `port` waarde voor een uniforme resource-id (URI) retourneren.

```
uriPort('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI `port` waarvan u waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*havenwaarde*> | Geheel getal | De `port` waarde voor de opgegeven URI. Als `port` u geen waarde opgeeft, geeft u de standaardpoort voor het protocol terug. |
||||

*Voorbeeld*

In dit `port` voorbeeld wordt de waarde voor deze URI geretourneerd:

```
uriPort('http://www.localhost:8080')
```

En geeft dit resultaat als resultaat:`8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

De `query` waarde voor een uniforme resource-id (URI) retourneren.

```
uriQuery('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI `query` waarvan u waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*querywaarde*> | Tekenreeks | De `query` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit `query` voorbeeld wordt de waarde voor deze URI geretourneerd:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

En geeft dit resultaat als resultaat:`"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

De `scheme` waarde voor een uniforme resource-id (URI) retourneren.

```
uriScheme('<uri>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Tekenreeks | De URI `scheme` waarvan u waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*systeemwaarde*> | Tekenreeks | De `scheme` waarde voor de opgegeven URI |
||||

*Voorbeeld*

In dit `scheme` voorbeeld wordt de waarde voor deze URI geretourneerd:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

En geeft dit resultaat als resultaat:`"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNu

Retourneer de huidige tijdstempel.

```
utcNow('<format>')
```

Optioneel u een andere indeling opgeven met de *<-indeling*> parameter.


| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Formaat*> | Nee | Tekenreeks | Ofwel een [enkele indeling specificert](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) of een [aangepaste opmaak patroon](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). De standaardnotatie voor de tijdstempel is ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), dat voldoet aan [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) en tijdzone-informatie behoudt. |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*huidige tijdstempel*> | Tekenreeks | De huidige datum en tijd |
||||

*Voorbeeld 1*

Stel dat het vandaag 15 april 2018 is om 13:00 uur.
In dit voorbeeld wordt de huidige tijdstempel opdeins:

```
utcNow()
```

En geeft dit resultaat als resultaat:`"2018-04-15T13:00:00.0000000Z"`

*Voorbeeld 2*

Stel dat het vandaag 15 april 2018 is om 13:00 uur.
In dit voorbeeld wordt de huidige tijdstempel weergegeven met de optionele "D"-indeling:

```
utcNow('D')
```

En geeft dit resultaat als resultaat:`"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Variabelen

Geef de waarde voor een opgegeven variabele terug.

```
variables('<variableName>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ja | Tekenreeks | De naam voor de variabele waarvan u de waarde wilt |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*variabele waarde*> | Alle | De waarde voor de opgegeven variabele |
||||

*Voorbeeld*

Stel dat de huidige waarde voor een variabele 'getalItems' 20 is.
In dit voorbeeld wordt de gehele waarde voor deze variabele:

```
variables('numItems')
```

En geeft dit resultaat als resultaat:`20`

<a name="workflow"></a>

### <a name="workflow"></a>werkstroom

Retourneer alle details over de werkstroom zelf tijdens de uitvoering.

```
workflow().<property>
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Eigenschap*> | Nee | Tekenreeks | De naam voor de werkstroomeigenschap waarvan u de waarde wilt <p>Een werkstroomobject heeft deze eigenschappen: **naam**, **type,** **id,** **locatie**en **uitvoeren**. De waarde **van de eigenschap Run** is ook een object met deze eigenschappen: **naam,** **type**en **id**. |
|||||

*Voorbeeld*

In dit voorbeeld wordt de naam voor de huidige uitvoering van een werkstroom geretourneerd:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Retourneer de XML-versie voor een tekenreeks die een JSON-object bevat.

```
xml('<value>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Waarde*> | Ja | Tekenreeks | De tekenreeks met het JSON-object dat moet worden converteren <p>Het JSON-object moet slechts één hoofdeigenschap hebben, wat geen array kan zijn. <br>Gebruik het backslash-teken (\\) als een escape-teken voor het dubbele aanhalingsteken ("). |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*xml-versie*> | Object | De gecodeerde XML voor de opgegeven tekenreeks of JSON-object |
||||

*Voorbeeld 1*

In dit voorbeeld wordt de XML-versie voor deze tekenreeks gemaakt, die een JSON-object bevat:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

En retourneert dit resultaat XML:

```xml
<name>Sophia Owen</name>
```

*Voorbeeld 2*

Stel dat u dit JSON-object hebt:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

In dit voorbeeld wordt XML gemaakt voor een tekenreeks die dit JSON-object bevat:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

En retourneert dit resultaat XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Controleer XML op knooppunten of waarden die overeenkomen met een XPath-padtaalexpressie en retourneer de overeenkomende knooppunten of waarden. Met een XPath-expressie of gewoon 'XPath' u door een XML-documentstructuur navigeren, zodat u knooppunten of rekenwaarden in de XML-inhoud selecteren.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Vereist | Type | Beschrijving |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Ja | Alle | De XML-tekenreeks om te zoeken naar knooppunten of waarden die overeenkomen met een XPath-expressiewaarde |
| <*Xpath*> | Ja | Alle | De XPath-expressie die wordt gebruikt om overeenkomende XML-knooppunten of -waarden te vinden |
|||||

| Retourwaarde | Type | Beschrijving |
| ------------ | ---- | ----------- |
| <*xml-knooppunt*> | XML | Een XML-knooppunt wanneer slechts één knooppunt overeenkomt met de opgegeven XPath-expressie |
| <*Waarde*> | Alle | De waarde van een XML-knooppunt wanneer slechts één waarde overeenkomt met de opgegeven XPath-expressie |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-of- </br>[<*waarde1*>, <*waarde2*>, ...] | Matrix | Een array met XML-knooppunten of waarden die overeenkomen met de opgegeven XPath-expressie |
||||

*Voorbeeld 1*

In dit voorbeeld worden `<name></name>` knooppunten gevonden die overeenkomen met het knooppunt in de opgegeven argumenten en wordt een array geretourneerd met die knooppuntwaarden:

`xpath(xml(parameters('items')), '/produce/item/name')`

Hier zijn de argumenten:

* De tekenreeks 'items', die deze XML bevat:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  In het voorbeeld wordt de functie [parameters()](#parameters) gebruikt om de XML-tekenreeks uit het argument 'items' te halen, maar moet de tekenreeks ook worden geconverteerd naar XML-indeling met de functie [xml().](#xml)

* Deze XPath-expressie, die wordt doorgegeven als een tekenreeks:

  `"/produce/item/name"`

Hier is de resultaatarray met `<name></name`de knooppunten die overeenkomen met:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Voorbeeld 2*

In dit voorbeeld wordt in dit `<count></count>` voorbeeld knooppunten gevonden die overeenkomen `sum()` met het knooppunt en worden deze knooppuntwaarden met de functie toegevoegd:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

En geeft dit resultaat als resultaat:`30`

*Voorbeeld 3*

In dit voorbeeld vinden beide expressies knooppunten die overeenkomen met het `<location></location>` knooppunt in de opgegeven argumenten, die XML met een naamruimte bevatten. 

> [!NOTE]
>
> Als u in de codeweergave werkt, ontkomt u aan het\\dubbele aanhalingsteken (") met behulp van het teken backslash ( ). 
> U moet bijvoorbeeld escape-tekens gebruiken wanneer u een expressie serialiseert als JSON-tekenreeks. 
> Als u echter in de Logic App Designer- of expressieeditor werkt, hoeft u niet aan het dubbele aanhalingsteken te ontsnappen omdat het backslash-teken automatisch aan de onderliggende definitie wordt toegevoegd, bijvoorbeeld:
> 
> * Codeweergave:`xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Expressieeditor:`xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> De volgende voorbeelden zijn van toepassing op expressies die u in voert in de expressieeditor.

* *Expressie 1*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *Expressie 2*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Hier zijn de argumenten:

* Deze XML, die de xml-documentnaamruimte bevat: `xmlns="http://contoso.com"`

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* XPath-expressie hier:

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

Hier is het resultaatknooppunt `<location></location>` dat overeenkomt met het knooppunt:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Voorbeeld 4*

In dit voorbeeld 3 wordt in `<location></location>` dit voorbeeld de waarde in het knooppunt gevonden:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

En geeft dit resultaat als resultaat:`"Paris"`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [taal voor werkstroomdefinitie](../logic-apps/logic-apps-workflow-definition-language.md)
