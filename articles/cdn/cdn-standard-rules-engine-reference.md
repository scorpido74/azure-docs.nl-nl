---
title: Standaardregels engine reference voor Azure CDN | Microsoft Documenten
description: Referentiedocumentatie voor overeenkomenmet voorwaarden en acties in de standaardregelsengine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259898"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Naslaginformatie over standaardregelengine voor Azure CDN

In de [standaardregelsengine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer overeenkomstvoorwaarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de wedstrijdvoorwaarden en -functies die beschikbaar zijn in de standaardregelsengine voor Azure CDN.

De rules engine is ontworpen om de uiteindelijke autoriteit te zijn over hoe specifieke soorten aanvragen worden verwerkt door Standaard Azure CDN.

**Gemeenschappelijke toepassingen voor de regels**:

- Een aangepast cachebeleid overschrijven of definiëren.
- Omleiden van aanvragen.
- Wijzig HTTP-aanvraag- en antwoordkoppen.

## <a name="terminology"></a>Terminologie

Als u een regel in de engine voor regels wilt definiëren, stelt u [wedstrijdvoorwaarden](cdn-standard-rules-engine-match-conditions.md) en [acties](cdn-standard-rules-engine-actions.md)in:

 ![Azure CDN-regelsstructuur](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Elke regel kan maximaal vier wedstrijdvoorwaarden en drie acties hebben. Elk Azure CDN-eindpunt kan maximaal vijf regels hebben. 

Inbegrepen in de huidige limiet met vijf regels voor een Azure CDN-eindpunt is een *standaardalgemene regel*. De algemene regel heeft geen overeenkomende voorwaarden en acties die zijn gedefinieerd in een algemene regel, worden altijd geactiveerd.

## <a name="syntax"></a>Syntaxis

Hoe speciale tekens in een regel worden behandeld, is afhankelijk van de manier waarop verschillende overeenkomstvoorwaarden en acties tekstwaarden verwerken. Een overeenkomstvoorwaarde of actie kan tekst op een van de volgende manieren interpreteren:

- [Letterlijke waarden](#literal-values)
- [Wildcardwaarden](#wildcard-values)


### <a name="literal-values"></a>Letterlijke waarden

Tekst die wordt geïnterpreteerd als een letterlijke waarde, behandelt alle speciale *tekens, behalve het symbool %,* als onderdeel van de waarde die moet worden gekoppeld aan een regel. Een letterlijke overeenkomstvoorwaarde die `'*'` is ingesteld op, `'*'` wordt bijvoorbeeld alleen voldaan wanneer de exacte waarde wordt gevonden.

Een procentteken wordt gebruikt om URL-codering `%20`aan te geven (bijvoorbeeld).

### <a name="wildcard-values"></a>Wildcardwaarden

Tekst die wordt geïnterpreteerd als een wildcardwaarde, wijst extra betekenis toe aan speciale tekens. In de volgende tabel wordt beschreven hoe specifieke speciale tekens worden geïnterpreteerd in de engine Standaardregels:

Teken | Beschrijving
----------|------------
\ | Een backslash wordt gebruikt om te ontsnappen aan een van de tekens die in deze tabel zijn opgegeven. Een backslash moet direct worden opgegeven voor het speciale teken dat moet worden ontsnapt. De volgende syntaxis ontsnapt bijvoorbeeld aan een sterretje:`\*`
% | Een procentteken wordt gebruikt om URL-codering `%20`aan te geven (bijvoorbeeld).
\* | Een sterretje is een wildcard die een of meer tekens vertegenwoordigt.
schijfruimte | Een spatieteken geeft aan dat een overeenkomende voorwaarde kan worden bepaald door een van de opgegeven waarden of patronen.
enkele aanhalingstekens | Een enkel aanhalingsteken heeft geen speciale betekenis. Een set enkele aanhalingstekens geeft echter aan dat een waarde als een letterlijke waarde moet worden beschouwd. Enkele aanhalingstekens kunnen op de volgende manieren worden gebruikt:<ul><li>Om aan een wedstrijdvoorwaarde te kunnen voldoen wanneer de opgegeven waarde overeenkomt met een deel van de vergelijkingswaarde.  Komt bijvoorbeeld `'ma'` overeen met een van de volgende tekenreeksen: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template. **ma**p</li></ul><li>Om een speciaal teken als een letterlijk teken te kunnen specificeren. U bijvoorbeeld een letterlijk spatieteken opgeven door een spatieteken`' '` in `'<sample value>'`een set aanhalingstekens (of ) te voegen.</li><li>Om een lege waarde te kunnen specificeren. Geef een lege waarde op door een set enkele aanhalingstekens op te geven (**''**).</li></ul>**Belangrijk**:<br /><ul><li>Als de opgegeven waarde geen wildcard bevat, wordt de waarde automatisch beschouwd als een letterlijke waarde. U hoeft geen enkele aanhalingstekens op te geven voor een letterlijke waarde.</li><li>Als een backslash niet wordt gebruikt om aan een ander teken in deze tabel te ontsnappen, wordt de backslash genegeerd wanneer deze is opgegeven in een set enkele aanhalingstekens.</li><li>Een andere manier om een speciaal teken als een letterlijk teken`\`op te geven, is door er aan te ontsnappen met behulp van een backslash ( ).</li></ul>

## <a name="next-steps"></a>Volgende stappen

- [Overeenkomen met de voorwaarden in de standaardregelsmotor](cdn-standard-rules-engine-match-conditions.md)
- [Acties in de standaardregels-engine](cdn-standard-rules-engine-actions.md)
- [HTTPS afdwingen met de standaardregelengine](cdn-standard-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
