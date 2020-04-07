---
title: Weergavemodi
description: Beschrijft de verschillende weergavemodi aan de serverzijde
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681699"
---
# <a name="rendering-modes"></a>Weergavemodi

Remote Rendering biedt twee hoofdmodi, **de TileBasedComposition-modus** en **de DepthBasedComposition-modus.** Deze modi bepalen hoe de werkbelasting wordt verdeeld over meerdere GPU's op de server. De modus moet worden opgegeven op verbindingstijd en kan niet worden gewijzigd tijdens runtime.

Beide modi hebben voordelen, maar ook met inherente functiebeperkingen, dus het kiezen van de meest geschikte modus is use case specific.

## <a name="modes"></a>Modi

De twee modi worden nu nader besproken.

### <a name="tilebasedcomposition-mode"></a>'TileBasedComposition'-modus

In **de TileBasedComposition-modus** worden specifieke subrechthoeken (tegels) op het scherm weergegeven. De hoofd-GPU stelt de uiteindelijke afbeelding van de tegels samen voordat deze als videoframe naar de client wordt verzonden. Daarom moeten alle GPU's dezelfde set resources hebben voor rendering, zodat de geladen assets in het geheugen van één GPU moeten passen.

De weergavekwaliteit in deze modus is iets beter dan in **de DepthBasedComposition-modus,** omdat MSAA kan werken aan de volledige geometrie voor elke GPU. De volgende screenshot laat zien dat antialiasing goed werkt voor beide randen hetzelfde:

![MSAA in TileBasedComposition](./media/service-render-mode-quality.png)

Bovendien kan in deze modus elk onderdeel worden overgeschakeld naar een transparant materiaal of worden overgeschakeld naar **see-through-modus** via de [Hiërarchische StateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>'DepthBasedComposition'-modus

In **de DepthBasedComposition-modus** wordt elke betrokken GPU weergegeven met volledige schermresolutie, maar slechts een subset van mazen. De uiteindelijke beeldsamenstelling op de belangrijkste GPU zorgt ervoor dat onderdelen correct worden samengevoegd op basis van hun diepte-informatie. Natuurlijk, geheugen payload is verdeeld over de GPU's, waardoor rendering modellen die niet zou passen in het geheugen van een enkele GPU.Naturally, memory payload is distributed across the GPUU's, waardoor rendering modellen die niet in het geheugen van een enkele GPU passen.

Elke GPU gebruikt MSAA om lokale inhoud te antialiasen. Er kan echter inherente aliasing zijn tussen randen van verschillende GPU's. Dit effect wordt beperkt door het uiteindelijke beeld na bewerking, maar de MSAA-kwaliteit is nog steeds slechter dan in de **TileBasedComposition-modus.**

MSAA artefacten worden geïllustreerd in ![de volgende afbeelding: MSAA in DepthBasedComposition](./media/service-render-mode-balanced.png)

Antialiasing werkt goed tussen de sculptuur en het gordijn, omdat beide delen op dezelfde GPU worden weergegeven. Aan de andere kant, de rand tussen gordijn en muur toont een aantal aliasing, omdat deze twee delen zijn samengesteld uit verschillende GPU's.

De grootste beperking van deze modus is dat geometrieonderdelen niet dynamisch kunnen worden overgeschakeld op transparante materialen en ook niet werkt de **doorzichtige** modus voor de [HierarchicalStateOverrideComponent.](../overview/features/override-hierarchical-state.md) Andere staat overschrijven functies (overzicht, kleurtint, ...) doen werk, dat wel. Ook materialen die tijdens de conversietijd als transparant zijn gemarkeerd, werken in deze modus wel goed.

### <a name="performance"></a>Prestaties

De prestatiekenmerken voor beide modi variëren afhankelijk van de use case, en het is moeilijk te redeneren of algemene aanbevelingen te geven. Als u niet wordt beperkt door de hierboven genoemde beperkingen (geheugen of transparantie/doorzichtigheid), is het raadzaam om beide modi uit te proberen en de prestaties te controleren met behulp van verschillende cameraposities.

## <a name="setting-the-render-mode"></a>De rendermodus instellen

De rendermodus die wordt gebruikt op `AzureSession.ConnectToRuntime` een `ConnectToRuntimeParams`remote rendering VM wordt opgegeven tijdens de .

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>Volgende stappen

* [Sessies](../concepts/sessions.md)
* [Component hiërarchische status overschrijven](../overview/features/override-hierarchical-state.md)
