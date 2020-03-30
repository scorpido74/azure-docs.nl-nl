---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176449"
---
De taak produceert een JSON-uitvoerbestand met metagegevens over gedetecteerde en bijgehouden gezichten. De metagegevens bevatten coördinaten die de locatie van gezichten aangeven, evenals een face ID-nummer dat de tracking van die persoon aangeeft. Face ID-nummers zijn gevoelig voor reset onder omstandigheden wanneer het frontale gezicht verloren gaat of overlapt in het frame, wat resulteert in een aantal personen krijgen toegewezen meerdere ID's.

De uitvoer JSON bevat de volgende elementen:

### <a name="root-json-elements"></a>Json-elementen voor root

| Element | Beschrijving |
| --- | --- |
| versie |Dit verwijst naar de versie van de Video API. |
| Tijdschaal |"Teken" per seconde van de video. |
| offset |Dit is de tijdscompensatie voor tijdstempels. In versie 1.0 van Video API's is dit altijd 0. In toekomstige scenario's die we ondersteunen, kan deze waarde veranderen. |
| breedte, hight |De breedte en hoogte van het uitvoervideoframe, in pixels.|
| Framerate |Aantal frames per seconde video. |
| [Fragmenten](#fragments-json-elements) |De metagegevens worden samengevoegd in verschillende segmenten, fragmenten genaamd. Elk fragment bevat een start, een duur, een intervalnummer en een of meer gebeurtenissen. |

### <a name="fragments-json-elements"></a>Fragmenten JSON-elementen

|Element|Beschrijving|
|---|---|
| start |De begintijd van de eerste gebeurtenis in 'teken'. |
| duur |De lengte van het fragment, in 'teken'. |
| Index | (Alleen op Azure Media Redactor) definieert de frameindex van de huidige gebeurtenis. |
| interval |Het interval van elke gebeurtenisvermelding in het fragment, in 'teken'. |
| events |Elke gebeurtenis bevat de gezichten die binnen die tijdsduur zijn gedetecteerd en bijgehouden. Het is een scala aan gebeurtenissen. De buitenste matrix vertegenwoordigt een tijdsinterval. De binnenste matrix bestaat uit 0 of meer gebeurtenissen die hebben plaatsgevonden gedurende de interval. Een lege beugel [] betekent dat er geen gezichten zijn gedetecteerd. |
| id |De ID van het gezicht dat wordt gevolgd. Dit getal kan per ongeluk veranderen als een gezicht onopgemerkt wordt. Een bepaald individu moet dezelfde ID hebben in de totale video, maar dit kan niet worden gegarandeerd als gevolg van beperkingen in het detectiealgoritme (occlusie, enz.). |
| x, y |De r-outs X en Y linksboven van het gezichtsselectiekader in een genormaliseerde schaal van 0,0 tot 1,0. <br/>-X- en Y-coördinaten zijn altijd relatief ten opzichte van het landschap, dus als je een portretvideo hebt (of ondersteboven, in het geval van iOS), moet je de coördinaten dienovereenkomstig omzetten. |
| breedte, hoogte |De breedte en hoogte van het gezichtsomsluitende vak in een genormaliseerde schaal van 0,0 tot 1,0. |
| gezichtengedetecteerd |Dit wordt gevonden aan het einde van de JSON resultaten en vat het aantal gezichten dat het algoritme gedetecteerd tijdens de video. Omdat de id's per ongeluk kunnen worden gereset als een gezicht onopgemerkt wordt (bijvoorbeeld het gezicht gaat van het scherm, kijkt weg), kan dit aantal niet altijd gelijk zijn aan het werkelijke aantal gezichten in de video. |
