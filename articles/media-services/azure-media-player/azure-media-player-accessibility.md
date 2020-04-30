---
title: Azure Media Player toegankelijkheid
description: Meer informatie over de toegankelijkheids instellingen van de Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726555"
---
# <a name="accessibility"></a>Toegankelijkheid #

Azure Media Player werkt met mogelijkheden voor scherm lezers, zoals Windows Verteller en Apple OSX/iOS Voice. Alternatieve tags zijn beschikbaar voor de UI-knoppen en de scherm lezer kan deze alternatieve tags lezen wanneer de gebruiker ernaar navigeert. Aanvullende configuraties kunnen worden ingesteld op het niveau van het besturings systeem.

## <a name="captions-and-subtitles"></a>Bijschriften en ondertitels ##

Op dit moment ondersteunt Azure Media Player momenteel alleen bijschriften voor gebeurtenissen op aanvraag met WebVTT-indeling en live-gebeurtenissen met behulp van CEA 708. De TTML-indeling wordt momenteel niet ondersteund. Met ondertitels en bijschriften kan de speler een breder publiek te bereiken, waaronder mensen met een gehoorbeperking of personen die de video in een andere taal willen volgen. Ondertiteling verhoogt ook de betrokkenheid bij de video, verbetert het begrip ervan en maakt het mogelijk om de video te bekijken op plekken waar geluid niet kan worden afgespeeld, zoals een werkplek.  

## <a name="high-contrast-mode"></a>Modus Hoog contrast ##

De standaard GEBRUIKERSINTERFACE in Azure Media Player is compatibel met de meeste weergave modi apparaat/browser hoog contrast. Configuraties kunnen worden ingesteld op het niveau van het besturings systeem.

## <a name="mobility-options"></a>Mobiliteits opties ##

### <a name="tabbing-focus"></a>Focus op tab ###

De focus van de tabbladen, die door algemene HTML-standaarden wordt verschaft, is beschikbaar in Azure Media Player. Als u de focus van het tabblad wilt inschakelen, `tabindex=0` moet u (of een andere waarde als u begrijpt hoe de volg orde van het tabblad wordt `<video>` beïnvloed in HTML `<video ... tabindex=0>...</video>`) worden toegevoegd aan de HTML zoals:. Op sommige platforms is de focus voor de besturings elementen alleen aanwezig als de besturings elementen zichtbaar zijn en als het platform deze mogelijkheden ondersteunt.

Zodra de focus is ingeschakeld, kan de eind gebruiker de video speler effectief navigeren en beheren zonder dat dit afhankelijk is van de muis. U kunt aan elk context menu of aan te geven element navigeren door te klikken op de knop met het tabblad en op ENTER of de spatie balk hebt geselecteerd. Wanneer u op ENTER of de spatie balk op een context menu klikt, wordt deze uitvouwen zodat de eind gebruiker door kan gaan met de tab-toets om een menu opdracht te selecteren. Zodra u de context hebt van het item dat u wilt selecteren, drukt u opnieuw op Enter of de spatiebalk om de selectie te voltooien.

### <a name="hotkeys"></a>Sneltoetsen ###

Azure Media Player ondersteunt de besturing via toetsenbord snel toetsen. In een webbrowser is de enige manier om het onderliggende video-element te beheren door de focus op de speler te hebben. Zodra er zich de focus op de speler bevindt, kan de functie van de speler door de sneltoets worden beheerd.  De volgende tabel beschrijft de verschillende sneltoetsen en het bijbehorende werking:

| Sneltoets              | Gedrag                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Modus volledig scherm inschakelen/uitschakelen voor de speler                                  |
| M/m                  | Volume van de speler dempen/dempen opheffen                                          |
| Pijl omhoog en omlaag    | Volume van de speler verhogen/verlagen                                    |
| Pijl naar links en rechts | Voortgang van de video versnellen/vertragen                                  |
| 0,1,2,3,4,5,6,7,8,9  | De voortgang van de video wordt gewijzigd in\- 0% 90%, afhankelijk van de toets die wordt ingedrukt |
| Klik op actie         | Video wordt afgespeeld/onderbroken                                                   |

## <a name="next-steps"></a>Volgende stappen

<!---Some context for the following links goes here--->
- [Snelstartgids Azure Media Player](azure-media-player-quickstart.md)