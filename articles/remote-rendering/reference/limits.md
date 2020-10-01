---
title: Beperkingen
description: Code beperkingen voor SDK-functies
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 61085a5d28fcd74bbf6a393ddc0731e36094a63f
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617495"
---
# <a name="limitations"></a>Beperkingen

Een aantal functies hebben een grootte, aantal of andere beperkingen.

## <a name="azure-frontend"></a>Azure-front-end

* Totaal aantal AzureFrontend-exemplaren per proces: 16.
* Totaal aantal AzureSession-exemplaren per AzureFrontend: 16.

## <a name="objects"></a>Objecten

* Totaal aantal toegestane objecten van één type (entiteit, CutPlaneComponent, enzovoort): 16.777.215.
* Totaal aantal toegestane actieve knip abonnementen: 8.

## <a name="geometry"></a>Geometrie

* **Animatie:** Animaties zijn beperkt tot het animeren van afzonderlijke trans formaties van Game-objecten. Skeletal-animaties met skins of vertex animaties worden niet ondersteund. Animatie tracks van het bron Asset-bestand blijven niet behouden. In plaats daarvan moeten animaties voor object transformatie worden aangedreven door client code.
* **Aangepaste shaders:** Het ontwerpen van aangepaste shaders wordt niet ondersteund. Alleen ingebouwde [kleur materialen](../overview/features/color-materials.md) of PBR- [materialen](../overview/features/pbr-materials.md) kunnen worden gebruikt.
* **Maximum aantal afzonderlijke materialen** in een asset: 65.535. Voor meer informatie over het automatisch verminderen van het aantal materialen raadpleegt u het hoofd stuk materiaal uit de [duplicatie](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* **Maximale dimensie van één textuur**: 16.384 x 16.384. Grotere bron structuren worden verkleind door het conversie proces.

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

* De [weer gave van de HW-camera](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) functie wordt niet ondersteund.
