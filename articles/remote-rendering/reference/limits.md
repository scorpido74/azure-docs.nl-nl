---
title: Limieten
description: Codebeperkingen voor SDK-functies
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680334"
---
# <a name="limits"></a>Limieten

Een aantal functies hebben grootte- of aantalbeperkingen vanwege interne details van het lopende systeem.

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



