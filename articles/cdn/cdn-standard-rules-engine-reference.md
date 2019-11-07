---
title: Naslag informatie over de Azure CDN Standard-regel engine | Microsoft Docs
description: Referentie documentatie voor voor waarden en acties voor het Azure CDN van de standaard regels-engine.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615934"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Azure CDN van de referentie van de micro soft Rules engine

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare matching voorwaarden en-functies voor de [standaard regels](cdn-standard-rules-engine.md)van de Azure Content Delivery Network (CDN).

De regel engine is ontworpen als de definitieve autoriteit voor het verwerken van specifieke typen aanvragen door het CDN.

**Veelvoorkomende toepassingen**:

- Een aangepast cache beleid overschrijft of definieert.
- Aanvragen omleiden.
- HTTP-aanvraag en-antwoord headers wijzigen

## <a name="terminology"></a>Terminologie

Een regel wordt gedefinieerd door het gebruik van [**matching voorwaarden**](cdn-standard-rules-engine-match-conditions.md)en [**acties**](cdn-standard-rules-engine-actions.md). Deze elementen zijn gemarkeerd in de volgende afbeelding:

 ![Structuur van CDN-regels](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Elke regel kan Maxi maal vier match voorwaarden en drie acties hebben. Er zijn Maxi maal 5 regels per CDN-eind punt. Daarnaast is er een regel aanwezig die standaard de **globale regel**wordt genoemd. Dit is een regel zonder match-voor waarden, waarbij de acties die binnen worden gedefinieerd, altijd geactiveerd zijn. Deze regel is opgenomen in de huidige limiet van 5 regels.

## <a name="syntax"></a>Syntaxis

De manier waarop speciale tekens worden behandeld, is afhankelijk van hoe een match-voor waarde of actopm tekst waarden verwerkt. Een match-voor waarde of functie kan tekst op een van de volgende manieren interpreteren:

1. [**Letterlijke waarden**](#literal-values)
2. [**Joker teken waarden**](#wildcard-values)


### <a name="literal-values"></a>Letterlijke waarden

Tekst die wordt geïnterpreteerd als een letterlijke waarde, behandelt alle speciale tekens, met uitzonde ring van het symbool%, als onderdeel van de waarde die moet worden aangepast. Met andere woorden, een letterlijke match-voor waarde die is ingesteld op `\'*'\` wordt alleen voldaan als de exacte waarde (dat wil zeggen, `\'*'\`) wordt gevonden.

Een procent teken wordt gebruikt om URL-code ring aan te geven (bijvoorbeeld `%20`).

### <a name="wildcard-values"></a>Joker teken waarden

Tekst die wordt geïnterpreteerd als een Joker teken, wijst extra betekenissen toe aan speciale tekens. In de volgende tabel wordt beschreven hoe de volgende reeks tekens wordt geïnterpreteerd:

Ring | Beschrijving
----------|------------
\ | Een back slash wordt gebruikt om een van de tekens die in deze tabel zijn opgegeven, te escapepen. Een back slash moet direct voor het speciale teken worden opgegeven.<br/>De volgende syntaxis verescapet bijvoorbeeld een asterisk: `\*`
% | Een procent teken wordt gebruikt om URL-code ring aan te geven (bijvoorbeeld `%20`).
\* | Een asterisk is een Joker teken dat bestaat uit een of meer tekens.
Ruimte | Een spatie geeft aan dat aan een voor waarde voor een overeenkomst kan worden voldaan door een van de opgegeven waarden of patronen.
Value | Een enkele aanhaling heeft geen speciale betekenis. Er wordt echter een set met enkele aanhalings tekens gebruikt om aan te geven dat een waarde moet worden behandeld als een letterlijke waarde. Dit kan op de volgende manieren worden gebruikt:<br><br/>-Hiermee kan een match-voor waarde worden vervuld wanneer de opgegeven waarde overeenkomt met een deel van de vergelijkings waarde.  `'ma'` komt bijvoorbeeld overeen met een van de volgende teken reeksen: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p. gif<br/>/business/template. **ma**p<br /><br />-Hiermee kan een speciaal teken worden opgegeven als een letterlijke teken. U kunt bijvoorbeeld een letterlijke spatie opgeven door een spatie te plaatsen binnen een set met enkele aanhalings tekens (`' '` of `'sample value'`).<br/>-Hiermee kan een lege waarde worden opgegeven. Geef een lege waarde op door een set met enkele aanhalings tekens (,) op te geven.<br /><br/>**Belangrijk:**<br/>-Als de opgegeven waarde geen joker teken bevat, wordt deze automatisch beschouwd als een letterlijke waarde, wat betekent dat het niet nodig is om een set met enkele aanhalings tekens op te geven.<br/>-Als een back slash geen ander teken in deze tabel weglaat, wordt deze genegeerd wanneer deze is opgegeven binnen een set met enkele aanhalings tekens.<br/>-Een andere manier om een speciaal teken op te geven als een letterlijke teken, is het escapeel te maken met behulp van een back slash (`\`).

## <a name="next-steps"></a>Volgende stappen

- [Voldoen aan de standaard regels voor de engine](cdn-standard-rules-engine-match-conditions.md)
- [Engine acties voor standaard regels](cdn-standard-rules-engine-actions.md)
- [HTTPS afdwingen met de standaard regels-engine](cdn-standard-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
