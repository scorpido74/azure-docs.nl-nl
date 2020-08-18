---
title: Beperkingen
description: Code beperkingen voor SDK-functies
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 5c4c6be7c8a6287c5a8ec6a60121751ff9fc0dc9
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509224"
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

* Totaal aantal toegestane materialen in een Asset: 65.535.
* Maximale dimensie van één textuur: 16.384 x 16.384. Grotere bron structuren worden omlaag geschaald door het conversie proces.

## <a name="overall-number-of-polygons"></a>Totaal aantal veelhoeken

Het toegestane aantal veelhoeken voor alle geladen modellen is afhankelijk van de grootte van de virtuele machine die wordt door gegeven aan [de rest API voor sessie beheer](../how-tos/session-rest-api.md#create-a-session):

| Server grootte | Maximum aantal veelhoeken |
|:--------|:------------------|
|standaard| 20.000.000 |
|ultieme| geen limiet |

Zie het hoofd stuk [Server grootte](../reference/vm-sizes.md) voor meer gedetailleerde informatie over deze beperking.

## <a name="platform-limitations"></a>Platform beperkingen

**Windows 10 Desktop**

* UWP/x86 is het enige ondersteunde UWP-platform. UWP/x64 wordt niet ondersteund.
* Win32/x64 is het enige win32-platform dat wordt ondersteund. Win32/x86 wordt niet ondersteund.

**Hololens 2**

* De [weer gave van de HW-camera](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) functie wordt niet ondersteund.
