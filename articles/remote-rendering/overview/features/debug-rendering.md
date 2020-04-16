---
title: Foutopsporing
description: Overzicht van foutopsporingsrenderingeffecten aan serverzijde
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: 675f8d988e64ed7b556f154f681ccb53ed1000c6
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394291"
---
# <a name="debug-rendering"></a>Foutopsporing

De debug rendering API biedt een reeks algemene opties om server-side rendering te wijzigen met verschillende debugging effecten.

## <a name="available-debug-rendering-effects"></a>Beschikbare foutopsporingsrendering-effecten

|Instelling                          | Effect                               |
|---------------------------------|:-------------------------------------|
|Frameteller                    | Hiermee wordt een tekstoverlay in de linkerbovenhoek van het kader weergegeven. De tekst toont de huidige server-side frame ID, die voortdurend wordt verhoogd als rendering gaat. |
|Veelhoektelling                    | Hiermee wordt een tekstoverlay in de linkerbovenhoek van het kader weergegeven. De tekst toont de momenteel gerenderde hoeveelheid veelhoeken, dezelfde waarde als die wordt opgevraagd door [prestatiequery's aan de serverzijde](performance-queries.md)| 
|Draadframe                        | Als dit is ingeschakeld, wordt alle objectgeometrie die op de server is geladen, weergegeven in de wireframe-modus. Alleen de randen van polygonen worden rasterd n deze modus. |

Met de volgende code worden deze foutopsporingseffecten mogelijk:

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![Foutopsporingsweergave](./media/debug-rendering.png)

> [!NOTE]
> Alle foutopsporingsrendering-effecten zijn globale instellingen die van invloed zijn op het hele frame.

## <a name="use-cases"></a>Gebruiksvoorbeelden

De API voor foutopsporing is bedoeld voor eenvoudige foutopsporingstaken, zoals het controleren van de serviceverbinding is eigenlijk correct actief. De opties voor het weergeven van tekst zijn rechtstreeks van invloed op de videoframes die naar beneden zijn gestreamd. Als u deze inschakelt, wordt gecontroleerd of nieuwe frames zijn ontvangen en video-gedecodeerd correct.

De verstrekte effecten geven echter geen gedetailleerde introspectie in de gezondheid van de dienstverlening. De [prestatiequery's aan de serverzijde](performance-queries.md) worden aanbevolen voor deze use case.

## <a name="performance-considerations"></a>Prestatieoverwegingen

* Als u de tekstoverlays inschakelt, is er weinig tot geen prestatieoverhead.
* Bovendien het inschakelen van de overlay maakt een niet-triviale prestaties overhead, maar het kan variëren afhankelijk van de scène. Voor complexe scènes kan deze modus ervoor zorgen dat de framerate onder het 60Hz-doel zakt.

## <a name="next-steps"></a>Volgende stappen

* [Weergavemodellen](../../concepts/rendering-modes.md)
* [Prestatiequery's aan serverzijde](performance-queries.md)
