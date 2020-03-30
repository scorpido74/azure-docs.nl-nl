---
title: Azure-netwerk latentiestatistieken voor rondreizen | Microsoft Documenten
description: Meer informatie over latentiestatistieken voor rondreis tussen Azure-regio's.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082932"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure-netwerk latentiestatistieken voor rondreis

Azure bewaakt continu de latentie (snelheid) van kerngebieden van zijn netwerk met behulp van interne monitoringtools en metingen die worden verzameld door [ThousandEyes](https://thousandeyes.com), een synthetische bewakingsservice van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentiemetingen worden verzameld van ThousandEyes-agents, gehost in Azure-cloudregio's wereldwijd, die continu netwerksondes onderling verzenden in intervallen van 1 minuut. De maandelijkse latentiestatistieken zijn afgeleid van het gemiddelde van de verzamelde monsters voor de maand.

## <a name="february-2020-round-trip-latency-figures"></a>Cijfers rondereis van februari 2020

De maandelijkse gemiddelde retourtijden tussen Azure-regio's van de afgelopen 29 dagen (eindigend op 29 februari 2020) worden hieronder weergegeven. De volgende metingen worden aangedreven door [ThousandEyes](https://thousandeyes.com).

[![Azure-latentiestatistieken voor verschillende regio's](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
