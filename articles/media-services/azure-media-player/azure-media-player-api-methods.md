---
title: API-methoden voor Azure Media Player
description: Met de Azure Media Player API u via JavaScript met de video communiceren, of de browser de video nu afspeelt via HTML5-video, Flash, Silverlight of andere ondersteunde afspeeltechnologieën.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727266"
---
# <a name="api"></a>API #

Met de Azure Media Player API u via JavaScript met de video communiceren, of de browser de video nu afspeelt via HTML5-video, Flash, Silverlight of andere ondersteunde afspeeltechnologieën.

## <a name="referencing-the-player"></a>Verwijzen naar de speler ##

Als u de API-functies wilt gebruiken, hebt u toegang nodig tot het spelersobject. Gelukkig is het gemakkelijk te krijgen. Je hoeft alleen maar om ervoor te zorgen dat uw video tag heeft een ID. De voorbeeldcode bevat een `vid1`id van . Als je meerdere video's op één pagina hebt, zorg er dan voor dat elke videotag een unieke id heeft.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Als de speler nog niet is geïnitialiseerd via het kenmerk van de gegevensinstelling of een andere methode, zal dit ook de speler initialiseren.

## <a name="wait-until-the-player-is-ready"></a>Wacht tot de speler klaar is ##

De tijd die Azure Media Player nodig heeft om de video en API in te stellen, is afhankelijk van de weergavetechnologie die wordt gebruikt. HTML5 zal vaak veel sneller te laden dan Flash of Silverlight. Om die reden moet de 'ready' functie van de speler worden gebruikt om elke code te activeren waarvoor de API van de speler nodig is.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OF

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API-methoden ##

Nu je toegang hebt tot een kant-en-klare speler, kun je de video beheren, waarden krijgen of reageren op videogebeurtenissen. De namen van de Azure Media Player API-functie proberen de [HTML5-media-API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)te volgen. Het belangrijkste verschil is dat getter / setter functies worden gebruikt voor video-eigenschappen.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Inschrijven voor evenementen ##
Gebeurtenissen moeten direct na het initialiseren van de speler voor de eerste keer worden geregistreerd om ervoor te zorgen dat alle gebeurtenissen op de juiste manier aan de toepassing worden gemeld en moeten buiten het klaar-evenement worden gedaan.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Volgende stappen ##

<!---Some context for the following links goes here--->
- [Snel start azure mediaspeler](azure-media-player-quickstart.md)