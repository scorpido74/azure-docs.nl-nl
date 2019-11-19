---
title: Naslag informatie voor de standaard regels voor Azure CDN | Microsoft Docs
description: Referentie documentatie voor voor waarden en acties in de Standard Rules engine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171567"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Naslag informatie voor de standaard regels voor Azure CDN

In de [standaard regels-engine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer match-voor waarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de voor waarden en functies die beschikbaar zijn in de standaard regels-engine voor Azure CDN.

De regel engine is ontworpen als de definitieve instantie van hoe specifieke typen aanvragen worden verwerkt door de standaard Azure CDN.

**Veelvoorkomende toepassingen voor de regels**:

- Een aangepast cache beleid overschrijft of definieert.
- Aanvragen omleiden.
- Wijzig HTTP-aanvraag-en antwoord headers.

## <a name="terminology"></a>Terminologie

Als u een regel in de regel Engine wilt definiëren, stelt u de voor waarden en [acties](cdn-standard-rules-engine-actions.md)voor [afstemmen](cdn-standard-rules-engine-match-conditions.md) in:

 ![Structuur van Azure CDN-regels](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Elke regel kan Maxi maal vier match voorwaarden en drie acties hebben. Elk Azure CDN-eind punt kan Maxi maal vijf regels bevatten. 

Opgenomen in de huidige limiet van vijf regels voor een Azure CDN-eind punt is een standaard *globale regel*. De algemene regel heeft geen overeenkomende voor waarden en acties die in een globale regel worden gedefinieerd, worden altijd geactiveerd.

## <a name="syntax"></a>Syntaxis

Hoe speciale tekens worden behandeld in een regel, hangt af van de manier waarop de verschillende match voorwaarden en acties tekst waarden afhandelen. Een match-voor waarde of actie kan tekst op een van de volgende manieren interpreteren:

- [Letterlijke waarden](#literal-values)
- [Joker teken waarden](#wildcard-values)


### <a name="literal-values"></a>Letterlijke waarden

Tekst die wordt geïnterpreteerd als een letterlijke waarde, behandelt alle speciale tekens *, met uitzonde ring van het symbool%* als onderdeel van de waarde die moet worden vergeleken in een regel. Bijvoorbeeld: een letterlijke match-voor waarde die is ingesteld op `'*'` is alleen vervuld als de exacte waarde `'*'` is gevonden.

Een procent teken wordt gebruikt om URL-code ring aan te geven (bijvoorbeeld `%20`).

### <a name="wildcard-values"></a>Joker teken waarden

Tekst die wordt geïnterpreteerd als een Joker teken, wijst extra betekenissen toe aan speciale tekens. In de volgende tabel wordt beschreven hoe specifieke speciale tekens worden geïnterpreteerd in de standaard regels-engine:

Teken | Beschrijving
----------|------------
\ | Een back slash wordt gebruikt om een van de tekens die in deze tabel zijn opgegeven, te escapepen. Een back slash moet direct voor het speciale teken worden opgegeven. De volgende syntaxis verescapet bijvoorbeeld een asterisk: `\*`
% | Een procent teken wordt gebruikt om URL-code ring aan te geven (bijvoorbeeld `%20`).
\* | Een asterisk is een Joker teken dat bestaat uit een of meer tekens.
gelijke | Een spatie geeft aan dat aan een voor waarde voor een overeenkomst kan worden voldaan door een van de opgegeven waarden of patronen.
enkele aanhalings tekens | Een enkel aanhalings teken heeft geen speciale betekenis. Een reeks enkele aanhalings tekens geeft echter aan dat een waarde moet worden behandeld als een letterlijke waarde. Enkele aanhalings tekens kunnen op de volgende manieren worden gebruikt:<ul><li>Om aan te geven dat aan een match-voor waarde wordt voldaan wanneer de opgegeven waarde overeenkomt met een deel van de vergelijkings waarde.  `'ma'` komt bijvoorbeeld overeen met een van de volgende teken reeksen: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template. **ma**p</li></ul><li>Als u wilt toestaan dat een speciaal teken wordt opgegeven als een letterlijke teken. U kunt bijvoorbeeld een letterlijke spatie opgeven door een spatie tussen enkele aanhalings tekens (`' '` of `'<sample value>'`) te zetten.</li><li>Om toe te staan dat een lege waarde wordt opgegeven. Geef een lege waarde op door een paar enkele aanhalings tekens ( **' '** ) op te geven.</li></ul>**Belang rijk**:<br /><ul><li>Als de opgegeven waarde geen joker teken bevat, wordt de waarde automatisch beschouwd als een letterlijke waarde. U hoeft geen set met enkele aanhalings tekens voor een letterlijke waarde op te geven.</li><li>Als een back slash niet wordt gebruikt om een ander teken in deze tabel te escapeel, wordt de back slash genegeerd als deze is opgegeven in een reeks enkele aanhalings tekens.</li><li>Een andere manier om een speciaal teken als letterlijke teken op te geven, is door een back slash (`\`) te gebruiken.</li></ul>

## <a name="next-steps"></a>Volgende stappen

- [Voldoen aan de voor waarden in de standaard regels-engine](cdn-standard-rules-engine-match-conditions.md)
- [Acties in de standaard regels-engine](cdn-standard-rules-engine-actions.md)
- [HTTPS afdwingen met behulp van de standaard regels-engine](cdn-standard-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
