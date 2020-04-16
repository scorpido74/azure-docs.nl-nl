---
title: Beperkingen
description: Codebeperkingen voor SDK-functies
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417688"
---
# <a name="limitations"></a>Beperkingen

Een aantal functies hebben grootte, telling of andere beperkingen.

## <a name="azure-frontend"></a>Azure Frontend

* Totaal AzureFrontend-exemplaren per proces: 16.
* Totaal aantal AzureSession-exemplaren per AzureFrontend: 16.

## <a name="objects"></a>Objecten

* Totaal toegestane objecten van één type (Entiteit, CutPlaneComponent, enz.): 16.777.215.
* Totaal toegestane actieve gesneden vliegtuigen: 8.

## <a name="materials"></a>Materialen

* Totaal toegestane materialen in een asset: 65.535.

## <a name="overall-number-of-polygons"></a>Totaal aantal polygonen

Het toegestane aantal veelhoeken voor alle geladen modellen is afhankelijk van de grootte van de VM, zoals doorgegeven aan [de REST API voor sessiebeheer:](../how-tos/session-rest-api.md#create-a-session)

| VM-grootte | Maximum aantal veelhoeken |
|:--------|:------------------|
|Standaard| 20 miljoen |
|Premium| geen limiet |


## <a name="platform-limitations"></a>Platformbeperkingen

**Windows 10-bureaublad**

* De implementatie van "PC Standalone" van Unity wordt niet ondersteund. Gebruik in plaats daarvan UWP.
* UWP/x86 is het enige ondersteunde UWP-platform. UWP/x64 wordt niet ondersteund.

**Hololens 2**

* De [render van PV-camera](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) functie wordt niet ondersteund.
