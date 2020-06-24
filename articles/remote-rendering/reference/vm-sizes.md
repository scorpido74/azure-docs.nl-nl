---
title: Formaten van virtuele machines
description: Beschrijft de verschillende VM-grootten die kunnen worden toegewezen
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: aab914caa2647146639aa366f558c80bebcfde54
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84810206"
---
# <a name="vm-sizes"></a>Formaten van virtuele machines

De rendering-service kan worden gebruikt op twee verschillende soorten machines in azure, met de naam `Standard` en `Premium` .

## <a name="polygon-limits"></a>Polygoon limieten

Er is een vaste limiet van **20.000.000 veelhoeken** voor de `Standard` VM-grootte. Er geldt geen beperking voor de `Premium` grootte.

Wanneer de renderer op een standaard-VM aan deze beperking voldoet, wordt de rendering naar een achtergrond met het dambord patroon overgeschakeld:

![Dambord](media/checkerboard.png)

## <a name="allocate-the-vm"></a>De virtuele machine toewijzen

Het gewenste type virtuele machine moet worden opgegeven bij het weer geven van de initialisatie tijd van de sessie. Het kan niet worden gewijzigd in een actieve sessie. De volgende code voorbeelden tonen de locatie waar de VM-grootte moet worden opgegeven:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

Voor het [Power shell-voorbeeld script](../samples/powershell-example-scripts.md)moet de VM-grootte worden opgegeven in het `arrconfig.json` bestand:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Hoe de renderer het aantal veelhoeken evalueert

Het aantal veelhoeken dat wordt overwogen voor de beperkings test, is het aantal veelhoeken dat daad werkelijk wordt door gegeven aan de renderer. Deze geometrie is doorgaans de som van alle geïnstantieerd modellen, maar er zijn ook uitzonde ringen. De volgende geometrie is **niet opgenomen**:
* Geladen model instanties die volledig buiten de weer gave-frustum zijn.
* Modellen of model onderdelen die worden overgeschakeld naar onzichtbaar, met behulp van het [onderdeel hiërarchische status onderdrukking](../overview/features/override-hierarchical-state.md).

Daarom is het mogelijk om een toepassing te schrijven die de `standard` grootte verlaagt van meerdere modellen met een aantal veelhoeken dicht bij de limiet voor elk enkel model. Wanneer de toepassing slechts één model per keer weergeeft, wordt het dambord niet geactiveerd.

### <a name="how-to-determine-the-number-of-polygons"></a>Het aantal veelhoeken bepalen

Er zijn twee manieren om het aantal veelhoeken van een model of scène te bepalen die bijdragen aan de budget limiet van de `standard` VM grootte:
* Haal het JSON-bestand van de [conversie uitvoer](../how-tos/conversion/get-information.md)op in de sectie model conversie en controleer de `numFaces` vermelding in de [ *inputStatistics* ](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Als uw toepassing een dynamische inhoud behandelt, kan het aantal gerenderde veelhoeken dynamisch worden opgevraagd tijdens runtime. Gebruik een [query voor prestatie analyse](../overview/features/performance-queries.md#performance-assessment-queries) en controleer het `polygonsRendered` lid in de `FrameStatistics` struct. De achtergrond van het dambord is altijd vervaagd met enige vertraging om ervoor te zorgen dat de gebruikers actie kan worden uitgevoerd na deze asynchrone query. Gebruikers actie kan bijvoorbeeld model instanties verbergen of verwijderen.

## <a name="pricing"></a>Prijzen

Raadpleeg de pagina met [prijzen voor externe rendering](https://azure.microsoft.com/pricing/details/remote-rendering) voor een gedetailleerde uitsplitsing van de prijzen voor elk type virtuele machine.

## <a name="next-steps"></a>Volgende stappen
* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
* [Model conversie](../how-tos/conversion/model-conversion.md)

