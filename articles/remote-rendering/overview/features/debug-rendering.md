---
title: Debug Rendering
description: Overzicht van foutopsporingsrenderingeffecten aan serverzijde
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868164"
---
# <a name="debug-rendering"></a>Debug Rendering

De debug rendering API biedt een reeks algemene opties om server-side rendering te wijzigen met verschillende debugging effecten.

## <a name="available-debug-rendering-effects"></a>Beschikbare foutopsporingsrendering-effecten

|Instelling                          | Effect                               |
|---------------------------------|:-------------------------------------|
|Frameteller                    | Hiermee wordt een tekstoverlay in de linkerbovenhoek van het kader weergegeven. De tekst toont de huidige server-side frame ID, die voortdurend wordt verhoogd als rendering gaat. |
|Veelhoektelling                    | Hiermee wordt een tekstoverlay in de linkerbovenhoek van het kader weergegeven. De tekst toont de momenteel gerenderde hoeveelheid veelhoeken, dezelfde waarde als die wordt opgevraagd door [prestatiequery's aan de serverzijde](performance-queries.md)| 
|Draadframe                        | Als dit is ingeschakeld, wordt alle objectgeometrie die op de server is geladen, weergegeven in de wireframe-modus. Alleen de randen van veelhoeken worden in deze modus gerasterd. |

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
* Het inschakelen van de wireframe-modus heeft een niet-triviale prestaties overhead, maar het kan variëren afhankelijk van de scène. Voor complexe scènes kan deze modus ervoor zorgen dat de framesnelheid onder het 60 Hz-doel zakt.

## <a name="next-steps"></a>Volgende stappen

* [Weergavemodellen](../../concepts/rendering-modes.md)
* [Prestatiequery's aan serverzijde](performance-queries.md)
