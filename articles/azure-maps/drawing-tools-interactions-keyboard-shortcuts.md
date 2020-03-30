---
title: Interactietypen voor tekengereedschappen en sneltoetsen op de kaart | Microsoft Azure Maps
description: Vormen tekenen en bewerken met een muis, aanraakscherm of toetsenbord in de Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198289"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Interactietypen en sneltoetsen in de module tekengereedschappen

In dit artikel worden alle verschillende manieren beschreven om vormen te tekenen en te bewerken met een muis, aanraakscherm of sneltoetsen.

De tekenmanager ondersteunt drie verschillende manieren van interactie met de kaart om vormen te tekenen.

* `click`- Coördinaten worden toegevoegd wanneer op de muis of aanraking wordt geklikt.
* `freehand `- Coördinaten worden toegevoegd wanneer de muis of aanraking op de kaart wordt gesleept.
* `hybrid`- Coördinaten worden toegevoegd wanneer de muis of aanraking wordt geklikt of gesleept.

## <a name="how-to-draw-shapes"></a>Vormen tekenen

 Voordat een vorm kan worden `drawingMode` getekend, stelt u de optie van de tekenmanager in op een ondersteunde tekeninstelling. Deze instelling kan worden geprogrammeerd of aangeroepen door op een van de tekenknoppen op de werkbalk te drukken. De tekenmodus blijft ingeschakeld, zelfs nadat een vorm is getekend, waardoor het gemakkelijk is om extra vormen van hetzelfde type te tekenen. Stel de tekenmodus programmatisch in op een niet-actieve status. U ook overschakelen naar een niet-actieve status door op de knop huidige tekenmodi op de werkbalk te klikken.

In de volgende secties worden alle verschillende manieren beschreven waarop vormen op de kaart kunnen worden getekend.

### <a name="how-to-draw-a-point"></a>Hoe maak je een punt te trekken

Wanneer de tekenmanager `draw-point` zich in de tekenmodus bevindt, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen. Deze methoden werken met alle interactiemodi.

**Beginnen met tekenen**
 - Klik op de linkermuisknop of raak op de kaart om een punt aan de kaart toe te voegen. 
 - Als de muis zich boven `F` de kaart bevindt, drukt u op de toets en wordt er een punt toegevoegd aan de coördinaat van de muisaanwijzer. Deze methode biedt een hogere nauwkeurigheid voor het toevoegen van een punt aan de kaart. Er zal minder beweging op de muis als gevolg van de druk beweging van de linker muisknop.
 - Blijf klikken, aanraken of `F` drukken om meer punten aan de kaart toe te voegen.
 
**Tekening voltooien**
 - Klik op een knop op de werkbalk van de tekening. 
 - Stel de tekenmodus programmatisch in. 
 - Druk `C` op de toets.

**Tekening annuleren**
 - Druk `Escape` op de toets.

### <a name="how-to-draw-a-line"></a>Hoe een lijn te trekken

Wanneer de tekenmanager `draw-line` in de modus is, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de interactiemodus.

**Beginnen met tekenen**
 - Klikmodus
   * Klik op de linkermuisknop of raak op de kaart om elk punt van een regel op de kaart toe te voegen. Voor elke klik of aanraking wordt een coördinaat aan de lijn toegevoegd. 
   * Als de muis zich boven `F` de kaart bevindt, drukt u op de toets en wordt er een punt toegevoegd aan de coördinaat van de muisaanwijzer. Deze methode biedt een hogere nauwkeurigheid voor het toevoegen van een punt aan de kaart. Er zal minder beweging op de muis als gevolg van de druk beweging van de linker muisknop.
   * Blijf klikken totdat alle gewenste punten aan de lijn zijn toegevoegd.
 - Vrijehand-modus
   * Druk op de linkermuisknop of raak aan op de kaart en sleep de muis of aanraakpunt rond. Coördinaten worden toegevoegd aan de lijn als de muis of het aanraakpunt beweegt rond de kaart. Zodra de muis- of touch-upgebeurtenis wordt geactiveerd, is de tekening voltooid. De frequentie waarmee coördinaten worden toegevoegd, `freehandInterval` wordt gedefinieerd door de optie tekenbeheerders.
 - Hybride modus
   * Wissel af tussen klik- en vrijehandmethoden, zoals gewenst, terwijl je een enkele lijn tekent. Klik bijvoorbeeld op een paar punten, houd de muis vast en sleep deze om een aantal punten toe te voegen en klik vervolgens op een paar punten meer. 

**Tekening voltooien**
 - Hybride/klikmodus
   * Dubbelklik op de kaart op het laatste punt. 
   * Klik op een knop op de werkbalk van de tekening. 
   * Stel de tekenmodus programmatisch in. 
 - Vrijehand-modus
   * Laat de muisknop of het aanraakpunt los.
 - Druk `C` op de toets.

**Tekening annuleren**
 - Druk `Escape` op de toets.

### <a name="how-to-draw-a-polygon"></a>Een veelhoek tekenen

Wanneer de tekenmanager `draw-polygon` in de modus is, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de interactiemodus.

**Beginnen met tekenen**
 - Klikmodus
   * Klik op de linkermuisknop of raak op de kaart om elk punt van een veelhoek op de kaart toe te voegen. Voor elke klik of aanraking wordt een coördinaat toegevoegd aan de veelhoek. 
   * Als de muis zich boven `F` de kaart bevindt, drukt u op de toets en wordt er een punt toegevoegd aan de coördinaat van de muisaanwijzer. Deze methode biedt een hogere nauwkeurigheid voor het toevoegen van een punt aan de kaart. Er zal minder beweging op de muis als gevolg van de druk beweging van de linker muisknop.
   * Blijf klikken totdat alle gewenste punten zijn toegevoegd aan de veelhoek.
 - Vrijehand-modus
   * Druk op de linkermuisknop of raak aan op de kaart en sleep de muis of aanraakpunt rond. Coördinaten worden toegevoegd aan de veelhoek terwijl de muis of het aanraakpunt over de kaart beweegt. Zodra de muis- of touch-upgebeurtenis wordt geactiveerd, is de tekening voltooid. De frequentie waarmee coördinaten worden toegevoegd, `freehandInterval` wordt gedefinieerd door de optie tekenbeheerders.
 - Hybride modus
   * Wissel tussen klik- en vrijehandmethoden, zoals gewenst, terwijl je één veelhoek tekent. Klik bijvoorbeeld op een paar punten, houd de muis vast en sleep deze om een aantal punten toe te voegen en klik vervolgens op een paar punten meer. 

**Tekening voltooien**
 - Hybride/klikmodus
   * Dubbelklik op de kaart op het laatste punt. 
   * Klik op het eerste punt in de veelhoek.
   * Klik op een knop op de werkbalk van de tekening. 
   * Stel de tekenmodus programmatisch in. 
 - Vrijehand-modus
   * Laat de muisknop of het aanraakpunt los.
 - Druk `C` op de toets.

**Tekening annuleren**
 - Druk `Escape` op de toets.

### <a name="how-to-draw-a-rectangle"></a>Een rechthoek tekenen

Wanneer de tekenmanager `draw-rectangle` in de modus is, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de interactiemodus. De gegenereerde vorm volgt de [uitgebreide GeoJSON-specificatie voor rechthoeken.](extend-geojson.md#rectangle)

**Beginnen met tekenen**
 - Druk op de linkermuisknop of raak omlaag op de kaart om de eerste hoek van de rechthoek toe te voegen en sleep om de rechthoek te maken. 

**Tekening voltooien**
 - Laat de muisknop of het aanraakpunt los.
 - Stel de tekenmodus programmatisch in. 
 - Druk `C` op de toets.

**Tekening annuleren**
 - Druk `Escape` op de toets.

### <a name="how-to-draw-a-circle"></a>Een cirkel tekenen

Wanneer de tekenmanager `draw-circle` in de modus is, kunnen de volgende acties worden uitgevoerd om punten op de kaart te tekenen, afhankelijk van de interactiemodus. De gegenereerde vorm volgt de [uitgebreide GeoJSON-specificatie voor cirkels.](extend-geojson.md#circle)

**Beginnen met tekenen**
 - Druk op de linkermuisknop of raak omlaag op de kaart om het midden van de cirkel toe te voegen en sleep geef de cirkels een straal. 

**Tekening voltooien**
 - Laat de muisknop of het aanraakpunt los.
 - Stel de tekenmodus programmatisch in. 
 - Druk `C` op de toets.

**Tekening annuleren**
 - Druk `Escape` op de toets.

## <a name="keyboard-shortcuts"></a>Sneltoetsen

De tekengereedschappen ondersteunen sneltoetsen. Deze sneltoetsen zijn functioneel wanneer de kaart scherp is.

| Sleutel      | Actie                            |
|----------|-----------------------------------|
| `C` | Hiermee voltooit u alle tekenen die aan de gang is en stelt u de tekenmodus in op niet actief. Focus wordt verplaatst naar kaartelement op het hoogste niveau.  |
| `Escape` | Hiermee annuleert u alle tekenen die aan de gang zijn en stelt u de tekenmodus in op niet actief. Focus wordt verplaatst naar kaartelement op het hoogste niveau.  |
| `F` | Hiermee voegt u een coördinaat toe aan een punt, lijn of veelhoek als de muis zich boven de kaart bevindt. Gelijkwaardige actie van het klikken op de kaart wanneer in klik of hybride modus. Deze snelkoppeling zorgt voor nauwkeurigere en snellere tekeningen. U de ene hand gebruiken om de muis en de andere te plaatsen om op de knop te drukken zonder de muis van het drukgebaar te bewegen. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de lessen in de module tekengereedschappen:

> [!div class="nextstepaction"]
> [Tekenmanager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Werkbalk Tekenen](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
