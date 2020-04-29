---
title: Naslag informatie voor Azure CDN-regels-engine | Microsoft Docs
description: Referentie documentatie voor voor waarden en functies voor de Azure CDN regels-engine.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: bda817712faf1f54287e880dc62ef2b08273ff42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253387"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Referentie voor Azure CDN van Verizon Premium-regels engine

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare matching voorwaarden en-functies voor de [regels engine](cdn-verizon-premium-rules-engine.md)van Azure Content Delivery Network (CDN).

De regel engine is ontworpen als de definitieve autoriteit voor het verwerken van specifieke typen aanvragen door het CDN.

**Veelvoorkomende toepassingen**:

- Een aangepast cache beleid overschrijft of definieert.
- Aanvragen voor gevoelige inhoud beveiligen of weigeren.
- Aanvragen omleiden.
- Aangepaste logboek gegevens opslaan.

## <a name="terminology"></a>Terminologie

Een regel is gedefinieerd met behulp van [**voorwaardelijke expressies**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**matching voorwaarden**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)en [**onderdelen**](cdn-verizon-premium-rules-engine-reference-features.md). Deze elementen zijn gemarkeerd in de volgende afbeelding:

 ![Voor waarde voor CDN-overeenkomst](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntaxis

De manier waarop speciale tekens worden behandeld, is afhankelijk van hoe een overeenkomst voorwaarde of-functie tekst waarden verwerkt. Een match-voor waarde of functie kan tekst op een van de volgende manieren interpreteren:

1. [**Letterlijke waarden**](#literal-values)
2. [**Joker teken waarden**](#wildcard-values)
3. [**Reguliere expressies**](#regular-expressions)

### <a name="literal-values"></a>Letterlijke waarden

Tekst die wordt geïnterpreteerd als een letterlijke waarde, behandelt alle speciale tekens, met uitzonde ring van het symbool%, als onderdeel van de waarde die moet worden aangepast. Met andere woorden, een letterlijke match-voor `\'*'\` waarde die is ingesteld op is alleen vervuld wanneer die exacte `\'*'\`waarde (dat wil zeggen) wordt gevonden.

Een percentage symbool wordt gebruikt om URL-code ring aan te geven `%20`(bijvoorbeeld).

### <a name="wildcard-values"></a>Joker teken waarden

Tekst die wordt geïnterpreteerd als een Joker teken, wijst extra betekenissen toe aan speciale tekens. In de volgende tabel wordt beschreven hoe de volgende reeks tekens wordt geïnterpreteerd:

Teken | Beschrijving
----------|------------
\ | Een back slash wordt gebruikt om een van de tekens die in deze tabel zijn opgegeven, te escapepen. Een back slash moet direct voor het speciale teken worden opgegeven.<br/>De volgende syntaxis verescapet bijvoorbeeld een asterisk:`\*`
% | Een percentage symbool wordt gebruikt om URL-code ring aan te geven `%20`(bijvoorbeeld).
\* | Een asterisk is een Joker teken dat bestaat uit een of meer tekens.
Space | Een spatie geeft aan dat aan een voor waarde voor een overeenkomst kan worden voldaan door een van de opgegeven waarden of patronen.
Value | Een enkele aanhaling heeft geen speciale betekenis. Er wordt echter een set met enkele aanhalings tekens gebruikt om aan te geven dat een waarde moet worden behandeld als een letterlijke waarde. Dit kan op de volgende manieren worden gebruikt:<br><br/>-Hiermee kan een match-voor waarde worden vervuld wanneer de opgegeven waarde overeenkomt met een deel van de vergelijkings waarde.  Bijvoorbeeld, `'ma'` komt overeen met een van de volgende teken reeksen: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p. gif<br/>/business/template. **ma**p<br /><br />-Hiermee kan een speciaal teken worden opgegeven als een letterlijke teken. U kunt bijvoorbeeld een letterlijke spatie opgeven door een spatie te plaatsen binnen een set met enkele aanhalings tekens ( `' '` of `'sample value'`).<br/>-Hiermee kan een lege waarde worden opgegeven. Geef een lege waarde op door een set met enkele aanhalings tekens (,) op te geven.<br /><br/>**Belang rijk**<br/>-Als de opgegeven waarde geen joker teken bevat, wordt deze automatisch beschouwd als een letterlijke waarde, wat betekent dat het niet nodig is om een set met enkele aanhalings tekens op te geven.<br/>-Als een back slash geen ander teken in deze tabel weglaat, wordt deze genegeerd wanneer deze is opgegeven binnen een set met enkele aanhalings tekens.<br/>-Een andere manier om een speciaal teken op te geven als een letterlijke teken, is het escapeel te maken `\`met behulp van een back slash (dat wil zeggen).

### <a name="regular-expressions"></a>Reguliere expressies

Reguliere expressies definiëren een patroon dat wordt doorzocht in een tekst waarde. De notatie reguliere expressie definieert specifieke betekenissen voor diverse symbolen. In de volgende tabel wordt aangegeven hoe speciale tekens worden behandeld door matching voorwaarden en functies die reguliere expressies ondersteunen.

Speciaal teken | Beschrijving
------------------|------------
\ | Met een back slash wordt het teken dat het volgt, als een letterlijke waarde gezien in plaats van de normale expressie betekenis. De volgende syntaxis verescapet bijvoorbeeld een asterisk:`\*`
% | De betekenis van een percentage symbool is afhankelijk van het gebruik.<br/><br/> `%{HTTPVariable}`: Met deze syntaxis wordt een HTTP-variabele aangeduid.<br/>`%{HTTPVariable%Pattern}`: In deze syntaxis wordt een percentage symbool gebruikt om een HTTP-variabele en als scheidings teken te identificeren.<br />`\%`: Als u een percentage tekent, kan dit worden gebruikt als letterlijke waarde of om URL-code ring aan te `\%20`geven (bijvoorbeeld).
\* | Met een sterretje kan het voorafgaande teken nul of meer keer worden gevonden.
Space | Een spatie wordt meestal beschouwd als een letterlijke teken.
Value | Enkele aanhalings tekens worden beschouwd als letterlijke letters. Een set met enkele aanhalings tekens heeft geen speciale betekenis.

Voor waarden en functies die reguliere expressies ondersteunen, accepteren patronen die zijn gedefinieerd door perl compatibele reguliere expressies (PCRE).

## <a name="next-steps"></a>Volgende stappen

- [Overeenkomende voor waarden voor regel engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Voorwaardelijke expressies van regel engine](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Functies van de engine voor regels](cdn-verizon-premium-rules-engine-reference-features.md)
- [HTTP-gedrag negeren met de regel engine](cdn-verizon-premium-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
