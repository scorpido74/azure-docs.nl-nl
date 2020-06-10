---
title: Beperkingen
description: Code beperkingen voor SDK-functies
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: c35930a93751cc82c3145d414a2a09b3a1e90c53
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659832"
---
# <a name="limitations"></a>Beperkingen

Een aantal functies hebben een grootte, aantal of andere beperkingen.

## <a name="azure-frontend"></a>Azure-front-end

* Totaal aantal AzureFrontend-exemplaren per proces: 16.
* Totaal aantal AzureSession-exemplaren per AzureFrontend: 16.

## <a name="objects"></a>Objecten

* Totaal aantal toegestane objecten van één type (entiteit, CutPlaneComponent, enzovoort): 16.777.215.
* Totaal aantal toegestane actieve knip abonnementen: 8.

## <a name="materials"></a>Materialen

* Totaal aantal toegestane materialen in een Asset: 65.535.

## <a name="overall-number-of-polygons"></a>Totaal aantal veelhoeken

Het toegestane aantal veelhoeken voor alle geladen modellen is afhankelijk van de grootte van de virtuele machine die wordt door gegeven aan [de rest API voor sessie beheer](../how-tos/session-rest-api.md#create-a-session):

| VM-grootte | Maximum aantal veelhoeken |
|:--------|:------------------|
|standaard| 20.000.000 |
|ultieme| geen limiet |


## <a name="platform-limitations"></a>Platform beperkingen

**Windows 10 Desktop**

* UWP/x86 is het enige ondersteunde UWP-platform. UWP/x64 wordt niet ondersteund.
* Win32/x64 is het enige win32-platform dat wordt ondersteund. Win32/x86 wordt niet ondersteund.

**Hololens 2**

* De [weer gave van de HW-camera](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) functie wordt niet ondersteund.
