---
title: Azure-netwerk latentiestatistieken voor rondreizen | Microsoft Documenten
description: Meer informatie over latentiestatistieken voor rondreis tussen Azure-regio's.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886943"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure-netwerk latentiestatistieken voor rondreis

Azure bewaakt continu de latentie (snelheid) van kerngebieden van zijn netwerk met behulp van interne monitoringtools en metingen die worden verzameld door [ThousandEyes](https://thousandeyes.com), een synthetische bewakingsservice van derden.

## <a name="how-are-the-measurements-collected"></a>Hoe worden de metingen verzameld?

De latentiemetingen worden verzameld van ThousandEyes-agents, gehost in Azure-cloudregio's wereldwijd, die continu netwerksondes onderling verzenden in intervallen van 1 minuut. De maandelijkse latentiestatistieken zijn afgeleid van het gemiddelde van de verzamelde monsters voor de maand.

## <a name="march-2020-round-trip-latency-figures"></a>Cijfers ronde reis van maart 2020

De maandelijkse gemiddelde retourtijden tussen Azure-regio's van de afgelopen 31 dagen (eindigend op 31 maart 2020) worden hieronder weergegeven. De volgende metingen worden aangedreven door [ThousandEyes](https://thousandeyes.com).

[![Azure-latentiestatistieken voor verschillende regio's](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).
