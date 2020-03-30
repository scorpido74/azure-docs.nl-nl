---
title: Een toegankelijke kaarttoepassing maken met Azure Maps | Microsoft Azure Maps
description: In dit artikel leert u hoe u een toepassing met toegankelijkheidsfuncties maken met Microsoft Azure Maps.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473316"
---
# <a name="building-an-accessible-application"></a>Een toegankelijke toepassing bouwen

Meer dan 20% van de internetgebruikers heeft behoefte aan toegankelijke webapplicaties. Als zodanig is het belangrijk om ervoor te zorgen dat uw toepassing is ontworpen zodanig dat elke gebruiker het gemakkelijk kan gebruiken. In plaats van te denken van toegankelijkheid als een set van taken te voltooien, denk aan het als onderdeel van uw algehele gebruikerservaring. Hoe toegankelijker uw toepassing, hoe meer mensen deze kunnen gebruiken. 

Als het gaat om rijke interactieve inhoud zoals een kaart, zijn enkele veelvoorkomende toegankelijkheidsoverwegingen:
- Ondersteuning van de schermlezer voor gebruikers die moeite hebben met het zien van de webapplicatie.
- Hebben meerdere methoden voor interactie met en navigeren in de webtoepassing, zoals muis, aanraken en toetsenbord.
- Zorg ervoor dat het kleurcontrast zodanig is dat kleuren niet samensmelten en moeilijk van elkaar te onderscheiden worden. 

De Azure Maps Web SDK is vooraf gebouwd met veel toegankelijkheidsfuncties, zoals:
- Schermlezerbeschrijvingen wanneer de kaart beweegt en wanneer de gebruiker zich richt op een besturingselement of pop-up.
- Ondersteuning voor muis, aanraking en toetsenbord.
- Ondersteuning voor toegankelijk kleurcontrast in de routekaartstijl.

Volledige toegankelijkheidsgegevens voor alle Microsoft-producten vindt u [hier.](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) Zoek naar 'Azure Maps-web' om het document specifiek voor de Azure Maps Web SDK te vinden. 

## <a name="navigating-the-map"></a>Navigeren op de kaart

Er zijn verschillende manieren waarop de kaart kan worden ingezoomd, gepand, gedraaid en geworpen. De volgende details alle verschillende manieren om de kaart te navigeren.

**De kaart inzoomen**

- Dubbelklik met een muis op de kaart om één niveau in te zoomen.
- Met een muis schuif je door het wiel om de kaart in te zoomen.
- Raak met behulp van een aanraakscherm de kaart aan met twee vingers en knijp samen om uit te zoomen of de vingers uit elkaar te spreiden om in te zoomen.
- Dubbeltik met een aanraakscherm op de kaart om één niveau in te zoomen.
- Gebruik met de kaart gericht`+`het Plusteken`=`( ) of Gelijkenteken ( ) om in één niveau in te zoomen.
- Als de kaart is gericht, gebruikt`-`u het minteken, koppelteken ( ) of Underscore (`_`) om één niveau uit te zoomen.
- Met het zoombesturingselement met een muis, aanraak- of toetsenbordtab/enter-toetsen.
- Houd de `Shift` knop ingedrukt en druk op de linkermuisknop op de kaart en sleep om een gebied uit te trekken om de kaart in te zoomen.

**De kaart pannen**

- Druk met een muis naar beneden met de linkermuisknop op de kaart en sleep in elke richting.
- Raak met behulp van een aanraakscherm de kaart aan en sleep in elke richting.
- Als de kaart is gericht, gebruikt u de pijltoetsen om de kaart te verplaatsen.

**De kaart roteren**

- Druk met de muis naar beneden met de rechtermuisknop op de kaart en sleep naar links of rechts. 
- Raak de kaart met twee vingers aan en draai met een aanraakscherm.
- Gebruik de shifttoets en de pijltoetsen links of rechts met de kaart gericht.
- Met behulp van het rotatiebesturingselement met een muis, aanraak- of toetsenbordtab/enter-toetsen.

**Pitch de kaart**

- Druk met de muis naar beneden met de rechtermuisknop op de kaart en sleep omhoog of omlaag. 
- Raak met behulp van een aanraakscherm de kaart met twee vingers aan en sleep ze samen omhoog of omlaag.
- Gebruik de shifttoets plus de pijl-omhoog of pijl-omlaag met de kaart gericht. 
- Met behulp van de toonhoogte besturingselement met een muis, touch of toetsenbord tab / voer toetsen.

## <a name="change-the-map-style"></a>De kaartstijl wijzigen

Niet alle ontwikkelaars willen dat alle mogelijke kaartstijlen beschikbaar zijn in hun toepassing. Als de ontwikkelaar de stijlkiezercontrole van de kaart weergeeft, kan de gebruiker de kaartstijl wijzigen met de muis, een aanraking of het toetsenbord met het tabblad of de toets invoeren. De ontwikkelaar kan opgeven welke kaartstijlen ze beschikbaar willen maken in het besturingselement voor de kaartstijlkiezer. Ook kan de ontwikkelaar de kaartstijl programmatisch instellen en wijzigen.

**Hoog contrast gebruiken**

- Wanneer het kaartbesturingselement is geladen, wordt gecontroleerd of een hoog contrast is ingeschakeld en ondersteunt de browser het.
- Het kaartbesturingselement controleert niet de hoge contrastmodus van het apparaat. Als de apparaatmodus verandert, wordt de kaart niet. Zo moet de gebruiker de kaart opnieuw laden door de pagina te vernieuwen.
- Wanneer een hoog contrast wordt gedetecteerd, schakelt de kaartstijl automatisch over naar een hoog contrast en gebruiken alle ingebouwde besturingselementen een stijl met een hoog contrast. ZoomControl, PitchControl, CompassControl, StyleControl en andere ingebouwde besturingselementen gebruiken bijvoorbeeld een stijl met een hoog contrast.
- Er zijn twee soorten hoog contrast, licht en donker. Als het type hoog contrast kan worden gedetecteerd door de kaartbesturingselementen, wordt het gedrag van de kaart dienovereenkomstig aangepast. Als licht, dan is de grayscale_light kaart stijl zal worden geladen. Als het type niet kan worden gedetecteerd of donker is, wordt de high_contrast_dark stijl geladen.
- Als u aangepaste besturingselementen maakt, is het handig om te weten of de ingebouwde besturingselementen een stijl met een hoog contrast gebruiken. Ontwikkelaars kunnen een css-klasse toevoegen aan de map container div om te controleren. De css klassen die zouden `high-contrast-dark` `high-contrast-light`worden toegevoegd zijn en . Gebruik het als u JavaScript wilt controleren:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

of: gebruik:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Sneltoetsen

De kaart heeft een aantal sneltoetsen ingebouwd die het gemakkelijker maken om de kaart te gebruiken. Deze sneltoetsen werken wanneer de kaart scherp is.

| Sleutel      | Actie                            |
|----------|-----------------------------------|
| `Tab` | Navigeer over de besturingselementen en pop-ups in de kaart. |
| `ESC` | Verplaats de focus van elk element in de kaart naar het kaartelement op het hoogste niveau. |
| `Ctrl` + `Shift` + `D` | Detailniveau schermlezer in- of uitschakelen.  |
| Pijltoets-links | De kaart 100 pixels naar links pannen |
| Pijltoets-rechts | De kaart goed pannen 100 pixels |
| Pijl | De kaart 100 pixels omlaag pannen |
| Pijl-omhoog | De kaart 100 pixels oppannen |
| `Shift`+ pijl-omhoog | Verhoog de toonhoogte met 10 graden |
| `Shift`+ pijl-omlaag | Verlaag de toonhoogte met 10 graden |
| `Shift`+ pijl-rechts | Draai de kaart 15 graden met de klok mee |
| `Shift`+ pijl-links | Draai de kaart 15 graden tegen de klok in |
| Plusteken`+`( <sup>*</sup>) of`=`Gelijken teken ( ) | Inzoomen |
| Minteken, Koppelteken`-` <sup>*</sup>( )`_`of Underscore ( ) | Uitzoomen | 
| `Shift`+ muisslepen op de kaart om gebied te tekenen | Inzoomen op het gebied |

<sup>*</sup>Deze toetssneltoetsen delen meestal dezelfde toets op een toetsenbord. Deze snelkoppelingen zijn toegevoegd om de gebruikerservaring te verbeteren. Het maakt ook niet uit of de gebruiker de shift-toets gebruikt of niet voor deze snelkoppelingen.

## <a name="screen-reader-support"></a>Ondersteuning voor Schermlezer

Gebruikers kunnen navigeren op de kaart met behulp van het toetsenbord. Als een schermlezer wordt uitgevoerd, stelt de kaart de gebruiker op de hoogte van wijzigingen in de status ervan. Gebruikers worden bijvoorbeeld op de hoogte gesteld van kaartwijzigingen wanneer de kaart wordt gepaneerd of ingezoomd. Standaard bevat de kaart vereenvoudigde beschrijvingen die het zoomniveau en de coördinaten van het midden van de kaart uitsluiten. De gebruiker kan het detailniveau van deze beschrijvingen `Ctrl`  +  `Shift`  + schakelen met behulp van de korte snede van `D`het toetsenbord.

Alle aanvullende informatie die op de basiskaart wordt geplaatst, moet dezelfde tekstuele informatie bevatten voor gebruikers van schermlezers. Zorg ervoor dat u waar nodig [toegankelijke rich internettoepassingen (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/), alt en titelkenmerken toevoegt. 

## <a name="make-popups-keyboard-accessible"></a>Pop-ups-toetsenbord toegankelijk maken

Een markering of symbool wordt vaak gebruikt om een locatie op de kaart weer te geven. Aanvullende informatie over de locatie wordt meestal weergegeven in een pop-up wanneer de gebruiker interactie heeft met de markering. In de meeste toepassingen worden pop-ups weergegeven wanneer een gebruiker op een markering klikt of tikt. Als u klikt en tikt, moet de gebruiker echter respectievelijk een muis en een aanraakscherm gebruiken. Een goede gewoonte is om pop-ups toegankelijk te maken bij het gebruik van een toetsenbord. Deze functionaliteit kan worden bereikt door een pop-up voor elk gegevenspunt te maken en deze aan de kaart toe te voegen. 

In het volgende voorbeeld worden punten van interesses op de kaart geladen met behulp van een symboollaag en wordt een pop-up toegevoegd aan de kaart voor elk punt van belang. Een verwijzing naar elke pop-up wordt opgeslagen in de eigenschappen van elk gegevenspunt. Het kan ook worden opgehaald voor een markering, zoals wanneer er op een markering wordt geklikt. Wanneer u zich op de kaart concentreert, kan de gebruiker door elke pop-up op de kaart drukken wanneer hij op de tabtoets drukt.

<br/>

<iframe height='500' scrolling='no' title='Een toegankelijke toepassing maken' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de Pen Maak een toegankelijke<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>toepassing</a> van Azure Maps ( ) op <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Aanvullende toegankelijkheidstips

Hier volgen enkele aanvullende tips om uw webmapping-toepassing toegankelijker te maken.

- Als u veel interactieve puntengegevens op de kaart weergeeft, u overwegen de rommel te verminderen en clustering te gebruiken. 
- Zorg ervoor dat de verhouding kleurcontrast tussen tekst/symbolen en achtergrondkleuren 4,5:1 of meer is.
- Houd uw schermlezer (ARIA, alt en titelkenmerken) berichten kort, beschrijvend en zinvol. Vermijd onnodig jargon en afkortingen.
- Probeer berichten die naar de schermlezer worden verzonden te optimaliseren om korte zinvolle informatie te bieden die gemakkelijk voor de gebruiker te verteren is. Als u bijvoorbeeld de schermlezer op een hoge frequentie wilt bijwerken, bijvoorbeeld wanneer de kaart wordt verplaatst, u de volgende punten uitvoeren:
    - Wacht tot de kaart klaar is met bewegen om de schermlezer bij te werken.
    - Geef de updates eens in de paar seconden af. 
    - Combineer berichten op een logische manier. 
- Vermijd het gebruik van kleur als enige manier om informatie over te brengen. Gebruik tekst, pictogrammen of patronen om de kleur aan te vullen of te vervangen. Enkele overwegingen:
    - Als u een bellenlaag gebruikt om de relatieve waarde tussen gegevenspunten weer te geven, u overwegen de straal van elke bel te schalen, de bel te kleuren of beide. 
    - Overweeg een symboollaag te gebruiken met verschillende pictogrammen voor verschillende metrische categorieën, zoals driehoeken, sterren en vierkanten. De symboollaag ondersteunt ook het schalen van de grootte van het pictogram. Er kan ook een tekstlabel worden weergegeven.
    - Als lijngegevens worden weergegeven, kan de breedte worden gebruikt om het gewicht of de grootte weer te geven. Een dash-array patroon kan worden gebruikt om verschillende categorieën lijnen weer te geven. Een symboollaag kan worden gebruikt in combinatie met een lijn om pictogrammen langs de lijn te bedekken. Het gebruik van een pijlpictogram is handig om de stroom of richting van de lijn weer te geven.
    - Als het weergeven van veelhoekgegevens een patroon, zoals strepen, kan worden gebruikt als alternatief voor kleur. 
- Sommige visualisaties, zoals heatmaps, tegellagen en afbeeldingslagen, zijn niet toegankelijk voor gebruikers met een visuele beperking. Enkele overwegingen:
    - Laat de schermlezer beschrijven wat de laag wordt weergegeven wanneer deze aan de kaart wordt toegevoegd. Als er bijvoorbeeld een tegellaag voor weerradarwordt weergegeven, laat de schermlezer zeggen: 'Weerradargegevens zijn bedekt op de kaart'.
- Beperk de hoeveelheid functionaliteit waarvoor een muismuiszweeft nodig is. Deze functionaliteiten zijn niet toegankelijk voor gebruikers die een toetsenbord of aanraakapparaat gebruiken om met uw toepassing te communiceren. Let op, het is nog steeds een goede gewoonte om een hover stijl voor interactieve inhoud, zoals klikbare pictogrammen, links en knoppen.
- Probeer door uw toepassing te navigeren met het toetsenbord. Zorg ervoor dat het bestellen van tabbladen logisch is.
- Als u sneltoetsen maakt, probeert u deze te beperken tot twee toetsen of minder. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over toegankelijkheid in de Web SDK-modules.

> [!div class="nextstepaction"]
> [Toegankelijkheid tekengereedschappen](drawing-tools-interactions-keyboard-shortcuts.md)

Meer informatie over het ontwikkelen van toegankelijke apps met Microsoft Learn:

> [!div class="nextstepaction"]
> [Leerpad voor toegankelijkheid in actie digitale badge](https://ready.azurewebsites.net/learning/track/2940)

Bekijk deze handige toegankelijkheidstools:
> [!div class="nextstepaction"]
> [Toegankelijke apps ontwikkelen](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [WAI-ARIA Overzicht](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Web Toegankelijkheid evaluatietool (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim-kleurcontrastcontrole](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Geen Koffie Vision Simulator](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
