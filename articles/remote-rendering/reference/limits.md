---
title: Beperkingen
description: Code beperkingen voor SDK-functies
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 33f5314c80dc33dbec50dc21a71f4cb507979e12
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427425"
---
# <a name="limitations"></a>Beperkingen

Een aantal functies hebben een grootte, aantal of andere beperkingen.

## <a name="azure-frontend"></a>Azure-front-end

De volgende beperkingen zijn van toepassing op de front-end-API (C++ en C#):
* Totaal aantal [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend) -exemplaren per proces: 16.
* Totaal aantal [AzureSession](/dotnet/api/microsoft.azure.remoterendering.azuresession) -exemplaren per [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend): 16.

## <a name="objects"></a>Objecten

* Totaal aantal toegestane objecten van één type ([entiteit](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md), enzovoort): 16.777.215.
* Totaal aantal toegestane actieve knip abonnementen: 8.

## <a name="geometry"></a>Geometrie

* **Animatie:** Animaties zijn beperkt tot het animeren van afzonderlijke trans formaties van [Game-objecten](../concepts/entities.md). Skeletal-animaties met skins of vertex animaties worden niet ondersteund. Animatie tracks van het bron Asset-bestand blijven niet behouden. In plaats daarvan moeten animaties voor object transformatie worden aangedreven door client code.
* **Aangepaste shaders:** Het ontwerpen van aangepaste shaders wordt niet ondersteund. Alleen ingebouwde [kleur materialen](../overview/features/color-materials.md) of PBR- [materialen](../overview/features/pbr-materials.md) kunnen worden gebruikt.
* **Maximum aantal afzonderlijke materialen** in een asset: 65.535. Voor meer informatie over het automatisch verminderen van het aantal materialen raadpleegt u het hoofd stuk materiaal uit de [duplicatie](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* **Maximale dimensie van één textuur** : 16.384 x 16.384. Grotere bron structuren worden verkleind door het conversie proces.

### <a name="overall-number-of-polygons"></a>Totaal aantal veelhoeken

Het toegestane aantal veelhoeken voor alle geladen modellen is afhankelijk van de grootte van de virtuele machine die wordt door gegeven aan [de rest API voor sessie beheer](../how-tos/session-rest-api.md#create-a-session):

| Server grootte | Maximum aantal veelhoeken |
|:--------|:------------------|
|Standard| 20.000.000 |
|ultieme| geen limiet |

Zie het hoofd stuk [Server grootte](../reference/vm-sizes.md) voor gedetailleerde informatie over deze beperking.

## <a name="platform-limitations"></a>Platform beperkingen

**Windows 10 Desktop**

* Win32/x64 is het enige win32-platform dat wordt ondersteund. Win32/x86 wordt niet ondersteund.

**HoloLens 2**

* De [weer gave van de HW-camera](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) functie wordt niet ondersteund.