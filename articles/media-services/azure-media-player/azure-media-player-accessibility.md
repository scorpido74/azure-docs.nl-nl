---
title: Azure Media Player Toegankelijkheid
description: Meer informatie over de toegankelijkheidsinstellingen van Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726555"
---
# <a name="accessibility"></a>Toegankelijkheid #

Azure Media Player werkt met schermlezermogelijkheden zoals Windows Narrator en Apple OSX/iOS VoiceOver. Er zijn alternatieve tags beschikbaar voor de UI-knoppen en de schermlezer kan deze alternatieve tags lezen wanneer de gebruiker ernaar navigeert. Extra configuraties kunnen worden ingesteld op het niveau van het besturingssysteem.

## <a name="captions-and-subtitles"></a>Bijschriften en ondertitels ##

Op dit moment ondersteunt Azure Media Player momenteel bijschriften voor alleen On-Demand-gebeurtenissen met WebVTT-indeling en live-evenementen met CEA 708. TTML-indeling wordt momenteel niet ondersteund. Met ondertitels en bijschriften kan de speler een breder publiek te bereiken, waaronder mensen met een gehoorbeperking of personen die de video in een andere taal willen volgen. Ondertiteling verhoogt ook de betrokkenheid bij de video, verbetert het begrip ervan en maakt het mogelijk om de video te bekijken op plekken waar geluid niet kan worden afgespeeld, zoals een werkplek.  

## <a name="high-contrast-mode"></a>Modus met hoog contrast ##

De standaardgebruikersinterface in Azure Media Player is compatibel met de meeste weergavemodi voor het hoge contrast van het apparaat/de browser. Configuraties kunnen worden ingesteld op het niveau van het besturingssysteem.

## <a name="mobility-options"></a>Mobiliteitsopties ##

### <a name="tabbing-focus"></a>Tabben-focus ###

Tabbenfocus, geleverd door algemene HTML-standaarden, is beschikbaar in Azure Media Player. Om tabscherpstelling in te `tabindex=0` schakelen, moet u (of een andere waarde als `<video>` u begrijpt `<video ... tabindex=0>...</video>`hoe tabvolgorde wordt beïnvloed in HTML) toe te voegen aan de HTML als volgt: . Op sommige platforms kan de focus voor de besturingselementen alleen aanwezig zijn als de besturingselementen zichtbaar zijn en als het platform deze mogelijkheden ondersteunt.

Zodra tabbenfocus is ingeschakeld, kan de eindgebruiker effectief navigeren en de videospeler bedienen zonder afhankelijk van de muis. Naar elk contextmenu of elk controleerbaar element kan worden genavigeerd door op de tabknop te drukken en geselecteerd met enter- of spatiebalk. Als u op enter- of spatiebalk in een contextmenu gaat, wordt deze uitgebreid zodat de eindgebruiker door kan gaan met tabbing om een menu-item te selecteren. Zodra u de context hebt van het item dat u wilt selecteren, drukt u opnieuw op Enter of de spatiebalk om de selectie te voltooien.

### <a name="hotkeys"></a>Hotkeys ###

Azure Media Player ondersteunt het beheren via toetsenbordhot key. In een webbrowser, de enige manier om de controle van de onderliggende video-element is door het hebben van focus op de speler. Zodra er focus op de speler, hot key kan de speler functionaliteit te controleren.  De volgende tabel beschrijft de verschillende sneltoetsen en het bijbehorende werking:

| Sneltoets              | Gedrag                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Modus volledig scherm inschakelen/uitschakelen voor de speler                                  |
| M/m                  | Volume van de speler dempen/dempen opheffen                                          |
| Pijl omhoog en omlaag    | Volume van de speler verhogen/verlagen                                    |
| Pijl naar links en rechts | Voortgang van de video versnellen/vertragen                                  |
| 0,1,2,3,4,5,6,7,8,9  | Videovoortgang wordt gewijzigd in\- 0% 90% afhankelijk van de toets die wordt ingedrukt |
| Klik op Actie         | Video wordt afgespeeld/onderbroken                                                   |

## <a name="next-steps"></a>Volgende stappen

<!---Some context for the following links goes here--->
- [Snel start azure mediaspeler](azure-media-player-quickstart.md)