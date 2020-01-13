---
title: Teken hulpprogramma's, interactie typen en toetsenbord snel toetsen op kaart | Microsoft Azure kaarten
description: Vormen tekenen en bewerken met behulp van een muis, een aanraak scherm of een toetsen bord in de Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 267579f7b4bbfe026f3aa01b00f01f3b872cf4a6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911657"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Interactie typen en sneltoetsen in de module teken hulpprogramma's

In dit artikel vindt u een overzicht van de verschillende manieren om shapes op een kaart te tekenen en te bewerken met behulp van een muis, een aanraak scherm of sneltoetsen.

De tekening beheerder ondersteunt drie verschillende manieren van interactie met de kaart om vormen te tekenen.

* `click`-coördinaten worden toegevoegd wanneer er met de muis of het aanraken wordt geklikt.
* `freehand `-coördinaten worden toegevoegd wanneer de muis of het aanraak scherm op de kaart wordt gesleept. 
* `hybrid`-coördinaten worden toegevoegd wanneer de muis of het aanraken wordt geklikt of gesleept.

## <a name="how-to-draw-shapes"></a>Vormen tekenen

Hieronder ziet u een overzicht van de verschillende manieren waarop vormen op de kaart kunnen worden getekend. Voordat een shape kan worden getekend, moet de `drawingMode` optie van de tekening Manager worden ingesteld op een ondersteunde teken instelling. Dit kan via een programma worden uitgevoerd, of door op een van de teken knoppen op de werk balk te drukken. De teken modus blijft ingeschakeld, zelfs nadat een vorm is getekend, waardoor het gemakkelijk is om extra vormen van hetzelfde type te tekenen. De teken modus kan via een programma worden opgeslagen in een niet-actieve status of door op de knop huidige teken modus op de werk balk te klikken. 

### <a name="how-to-draw-a-point"></a>Een punt tekenen

Wanneer het tekening beheer zich in `draw-point` teken modus bevindt, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen. Deze methoden werken met alle interactie modi.

**Tekenen starten**
 - Klik op de linkermuisknop of druk op de kaart om een punt aan de kaart toe te voegen. 
 - Als de muis zich boven de kaart bevindt, drukt u op de toets `F` en wordt er een punt toegevoegd met behulp van de coördinaat van waar de muis aanwijzer zich bevindt. Dit biedt een nauw keurige methode voor het toevoegen van een punt aan de kaart, omdat er minder bewegingen met de muis worden veroorzaakt door de beweging van de linkermuisknop.
 - Blijf klikken, aanraken of op `F` om meer punten aan de kaart toe te voegen.
 
**Tekenen volt ooien**
 - Klik op een knop op de werk balk tekenen. 
 - De teken modus programmatisch instellen. 
 - Druk op de toets `C`.

**Tekenen annuleren**
 - Druk op de toets `Escape`.

### <a name="how-to-draw-a-line"></a>Een lijn tekenen

Wanneer het tekening beheer zich in `draw-line` modus bevindt, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de manier waarop de interactie modus is ingesteld.

**Tekenen starten**
 - Klik modus
   * Klik op de linkermuisknop of druk op de kaart om elk punt van een regel op de kaart toe te voegen. Een coördinaat wordt toegevoegd aan de regel voor elke klik/touch. 
   * Als de muis zich boven de kaart bevindt, drukt u op de toets `F` en wordt er een punt toegevoegd met behulp van de coördinaat van waar de muis aanwijzer zich bevindt. Dit biedt een nauw keurige methode voor het toevoegen van een punt aan de kaart, omdat er minder bewegingen met de muis worden veroorzaakt door de beweging van de linkermuisknop.
   * Blijf klikken totdat alle gewenste punten aan de lijn zijn toegevoegd.
 - Free hand-modus
   * Druk op de linkermuisknop of vervolg keuzelijst op de kaart om de muis of het aanraak punt te slepen. Coördinaten worden toegevoegd aan de regel als de muis of het aanraak punt over de kaart wordt verplaatst. Zodra de muis of het aanraak gebeurtenis wordt geactiveerd, wordt de tekening voltooid. De frequentie waarmee coördinaten worden toegevoegd, wordt gedefinieerd door de teken managers `freehandInterval` optie.
 - Hybride modus
   * Scha kelen tussen klikken en Free hand-methoden naar wens tijdens het tekenen van één regel. Klik bijvoorbeeld op een paar punten, houd de muis knop ingedrukt en sleep om een aantal punten toe te voegen en klik vervolgens op enkele andere. 

**Tekenen volt ooien**
 - Hybride modus/klik
   * Dubbel klik op de kaart op het laatste punt. 
   * Klik op een knop op de werk balk tekenen. 
   * De teken modus programmatisch instellen. 
 - Free hand-modus
   * Laat de muis knop of het aanraak punt los.
 - Druk op de toets `C`.

**Tekenen annuleren**
 - Druk op de toets `Escape`.

### <a name="how-to-draw-a-polygon"></a>Een veelhoek tekenen

Wanneer het tekening beheer zich in `draw-polygon` modus bevindt, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de manier waarop de interactie modus is ingesteld.

**Tekenen starten**
 - Klik modus
   * Klik op de linkermuisknop of druk op de kaart om elk punt van een veelhoek op de kaart toe te voegen. Er wordt voor elke klik/touch een coördinaat toegevoegd aan de veelhoek. 
   * Als de muis zich boven de kaart bevindt, drukt u op de toets `F` en wordt er een punt toegevoegd met behulp van de coördinaat van waar de muis aanwijzer zich bevindt. Dit biedt een nauw keurige methode voor het toevoegen van een punt aan de kaart, omdat er minder bewegingen met de muis worden veroorzaakt door de beweging van de linkermuisknop.
   * Blijf klikken totdat alle gewenste punten aan de veelhoek zijn toegevoegd.
 - Free hand-modus
   * Druk op de linkermuisknop of vervolg keuzelijst op de kaart om de muis of het aanraak punt te slepen. Coördinaten worden toegevoegd aan de veelhoek als de muis of het aanraak punt zich verplaatst rond de kaart. Zodra de muis of het aanraak gebeurtenis wordt geactiveerd, wordt de tekening voltooid. Houd er rekening mee dat de frequentie waarmee coördinaten worden toegevoegd, wordt gedefinieerd door de teken managers `freehandInterval` optie.
 - Hybride modus
   * Scha kelen tussen klikken en Free hand-methoden naar wens tijdens het tekenen van één veelhoek. Klik bijvoorbeeld op een paar punten, houd de muis knop ingedrukt en sleep om een aantal punten toe te voegen en klik vervolgens op enkele andere. 

**Tekenen volt ooien**
 - Hybride modus/klik
   * Dubbel klik op de kaart op het laatste punt. 
   * Klik op het eerste punt in de veelhoek.
   * Klik op een knop op de werk balk tekenen. 
   * De teken modus programmatisch instellen. 
 - Free hand-modus
   * Laat de muis knop of het aanraak punt los.
 - Druk op de toets `C`.

**Tekenen annuleren**
 - Druk op de toets `Escape`.

### <a name="how-to-draw-a-rectangle"></a>Een rechthoek tekenen

Wanneer het tekening beheer zich in `draw-rectangle` modus bevindt, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de manier waarop de interactie modus is ingesteld. De gegenereerde vorm volgt de [uitgebreide GEOjson-specificatie voor rechthoeken](extend-geojson.md#rectangle).

**Tekenen starten**
 - Druk op de linkermuisknop of vervolg keuzelijst op de kaart om de eerste hoek van de rechthoek toe te voegen en sleep om de rechthoek te maken. 

**Tekenen volt ooien**
 - Laat de muis knop of het aanraak punt los.
 - De teken modus programmatisch instellen. 
 - Druk op de toets `C`.

**Tekenen annuleren**
 - Druk op de toets `Escape`.

### <a name="how-to-draw-a-circle"></a>Een cirkel tekenen

Wanneer het tekening beheer zich in `draw-circle` modus bevindt, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de manier waarop de interactie modus is ingesteld. De gegenereerde vorm volgt de [uitgebreide GEOjson-specificatie voor cirkels](extend-geojson.md#circle).

**Tekenen starten**
 - Houd de linkermuisknop ingedrukt of vervolg op de kaart om het midden van de cirkel toe te voegen en sleep de cirkels een straal geven. 

**Tekenen volt ooien**
 - Laat de muis knop of het aanraak punt los.
 - De teken modus programmatisch instellen. 
 - Druk op de toets `C`.

**Tekenen annuleren**
 - Druk op de toets `Escape`.

## <a name="keyboard-shortcuts"></a>Toetsencombinaties

De teken hulpprogramma's ondersteunen sneltoetsen waarmee u gemakkelijk shapes kunt tekenen en bewerken op de kaart. Deze sneltoetsen werken op het moment dat de kaart de focus heeft.

| Sleutel      | Actie                            |
|----------|-----------------------------------|
| `C` | Hiermee wordt de actieve tekening voltooid en wordt de teken modus ingesteld op inactief. De focus wordt verplaatst naar het map-element op het hoogste niveau.  |
| `Escape` | Hiermee wordt de actieve tekening geannuleerd en wordt de teken modus ingesteld op inactief. De focus wordt verplaatst naar het map-element op het hoogste niveau.  |
| `F` | Voegt een coördinaat toe aan een punt, lijn of veelhoek als de muis zich boven de kaart bevindt. Equivalente actie van klikken op de kaart wanneer u klikt of op de hybride modus. Met deze snelkoppeling kunt u nauw keurige en snellere tekeningen gebruiken, omdat u de muis ook kunt positioneren en andere om op de knop te drukken zonder dat de muis van de beweging van het tikken wordt verplaatst. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen in de module teken hulpprogramma's:

> [!div class="nextstepaction"]
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Werk balk tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)