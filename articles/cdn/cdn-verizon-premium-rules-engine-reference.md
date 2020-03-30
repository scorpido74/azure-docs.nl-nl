---
title: Verwijzing naar de azure CDN-regelsengine | Microsoft Documenten
description: Referentiedocumentatie voor Azure CDN-regels engine komen overeen met voorwaarden en functies.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69996754"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN van Verizon Premium regels engine referentie

In dit artikel worden gedetailleerde beschrijvingen weergegeven van de beschikbare wedstrijdvoorwaarden en -functies voor de [CDN-regelsengine](cdn-verizon-premium-rules-engine.md)(Azure Content Delivery Network).

De regels motor is ontworpen om de uiteindelijke autoriteit over hoe specifieke soorten verzoeken worden verwerkt door het CDN.

**Gemeenschappelijk gebruik**:

- Een aangepast cachebeleid overschrijven of definiëren.
- Verzoeken om gevoelige inhoud beveiligen of weigeren.
- Omleiden van aanvragen.
- Aangepaste logboekgegevens opslaan.

## <a name="terminology"></a>Terminologie

Een regel wordt gedefinieerd door het gebruik van [**voorwaardelijke expressies**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**overeenkomen met voorwaarden**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)en [**functies**](cdn-verizon-premium-rules-engine-reference-features.md). Deze elementen worden gemarkeerd in de volgende afbeelding:

 ![CDN-wedstrijdvoorwaarde](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntaxis

De manier waarop speciale tekens worden behandeld, is afhankelijk van de manier waarop een overeenkomstvoorwaarde of functie omgaat met tekstwaarden. Een overeenkomstvoorwaarde of -functie kan tekst op een van de volgende manieren interpreteren:

1. [**Letterlijke waarden**](#literal-values)
2. [**Wildcardwaarden**](#wildcard-values)
3. [**Reguliere expressies**](#regular-expressions)

### <a name="literal-values"></a>Letterlijke waarden

Tekst die wordt geïnterpreteerd als een letterlijke waarde, behandelt alle speciale tekens, met uitzondering van het symbool %, als een deel van de waarde die moet worden geëvenaard. Met andere woorden, een letterlijke match voorwaarde ingesteld op `\'*'\` is `\'*'\`alleen voldaan wanneer die exacte waarde (dat wil zeggen, ) wordt gevonden.

Een percentagesymbool wordt gebruikt om URL-codering `%20`aan te geven (bijvoorbeeld).

### <a name="wildcard-values"></a>Wildcardwaarden

Tekst die wordt geïnterpreteerd als een wildcardwaarde, wijst extra betekenis toe aan speciale tekens. In de volgende tabel wordt beschreven hoe de volgende set tekens wordt geïnterpreteerd:

Teken | Beschrijving
----------|------------
\ | Een backslash wordt gebruikt om te ontsnappen aan een van de tekens die in deze tabel zijn opgegeven. Een backslash moet direct worden opgegeven voor het speciale teken dat moet worden ontsnapt.<br/>De volgende syntaxis ontsnapt bijvoorbeeld aan een sterretje:`\*`
% | Een percentagesymbool wordt gebruikt om URL-codering `%20`aan te geven (bijvoorbeeld).
\* | Een sterretje is een wildcard die een of meer tekens vertegenwoordigt.
Space | Een spatieteken geeft aan dat een overeenkomende voorwaarde kan worden bepaald door een van de opgegeven waarden of patronen.
"waarde" | Een enkel citaat heeft geen speciale betekenis. Een set enkele aanhalingstekens wordt echter gebruikt om aan te geven dat een waarde moet worden behandeld als een letterlijke waarde. Het kan op de volgende manieren worden gebruikt:<br><br/>- Hiermee kan aan een wedstrijdvoorwaarde worden voldaan wanneer de opgegeven waarde overeenkomt met een deel van de vergelijkingswaarde.  Komt bijvoorbeeld `'ma'` overeen met een van de volgende tekenreeksen: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template. **ma**p<br /><br />- Hiermee kan een speciaal teken als een letterlijk teken worden opgegeven. U bijvoorbeeld een letterlijk spatieteken opgeven door een spatieteken in een `' '` `'sample value'`set enkele aanhalingstekens (dat wil zeggen) om te voegen.<br/>- Hiermee kan een lege waarde worden opgegeven. Geef een lege waarde op door een set enkele aanhalingstekens op te geven (dat wil zeggen '').<br /><br/>**Belangrijk:**<br/>- Als de opgegeven waarde geen wildcard bevat, wordt deze automatisch beschouwd als een letterlijke waarde, wat betekent dat het niet nodig is om een set enkele aanhalingstekens op te geven.<br/>- Als een backslash niet ontsnapt aan een ander teken in deze tabel, wordt deze genegeerd wanneer deze is opgegeven in een set enkele aanhalingstekens.<br/>- Een andere manier om een speciaal teken als een letterlijk teken `\`te specificeren is om te ontsnappen met behulp van een backslash (dat wil zeggen, ).

### <a name="regular-expressions"></a>Reguliere expressies

Reguliere expressies definiëren een patroon dat wordt gezocht binnen een tekstwaarde. Regelmatige expressienotatie definieert specifieke betekenissen voor een verscheidenheid aan symbolen. In de volgende tabel wordt aangegeven hoe speciale tekens worden behandeld aan de andere kant van de voorwaarden en functies die reguliere expressies ondersteunen.

Speciaal teken | Beschrijving
------------------|------------
\ | Een backslash ontsnapt aan het teken volgt het, waardoor dat teken wordt behandeld als een letterlijke waarde in plaats van het nemen van de reguliere expressie betekenis. De volgende syntaxis ontsnapt bijvoorbeeld aan een sterretje:`\*`
% | De betekenis van een percentagesymbool is afhankelijk van het gebruik ervan.<br/><br/> `%{HTTPVariable}`: Met deze syntaxis wordt een HTTP-variabele aangegeven.<br/>`%{HTTPVariable%Pattern}`: Deze syntaxis gebruikt een percentagesymbool om een HTTP-variabele en als scheidingsteken te identificeren.<br />`\%`: Ontsnappen aan een percentage symbool maakt het mogelijk om te worden gebruikt `\%20`als een letterlijke waarde of om URL-codering aan te geven (bijvoorbeeld, ).
\* | Met een sterretje kan het voorgaande teken nul of meer keer worden geëvenaard.
Space | Een spatieteken wordt meestal behandeld als een letterlijk teken.
"waarde" | Enkele aanhalingstekens worden behandeld als letterlijke tekens. Een reeks enkele aanhalingstekens heeft geen speciale betekenis.

Overeenkomen met voorwaarden en functies die reguliere expressies ondersteunen, accepteren patronen die zijn gedefinieerd door Perl Compatible Regular Expressions (PCRE).

## <a name="next-steps"></a>Volgende stappen

- [Regels motor overeenkomen met voorwaarden](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regels engine voorwaardelijke expressies](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regels motorfuncties](cdn-verizon-premium-rules-engine-reference-features.md)
- [HTTP-gedrag overschrijven met de rules engine](cdn-verizon-premium-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
