---
title: Overzicht van de Azure Compute-eenheid | Microsoft Docs
description: Overzicht van het concept van de Azure-reken eenheden. De ACU biedt een manier om de CPU-prestaties in azure Sku's te vergelijken.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 8e406aea10c15a97a7e66bbdc3c1d889c5bd2c52
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493863"
---
# <a name="azure-compute-unit-acu"></a>Azure Compute-eenheid (ACU)

Het concept van de Azure Compute Unit (ACU) biedt een manier om Compute-prestaties (CPU) te vergelijken tussen Azure-Sku's. Op die manier kunt u eenvoudig bepalen welke SKU hoogstwaarschijnlijk aan uw prestatiebehoeften zal voldoen. ACU is momenteel gestandaardiseerd op 100 voor een kleine virtuele machine (Standard_A1). Alle andere SKU's geven vervolgens weer hoeveel sneller die SKU een standaardbenchmark ongeveer kan uitvoeren.

> [!IMPORTANT]
> De ACU is slechts een richtlijn. De resultaten voor uw workload kunnen verschillen.
<br>

| SKU-familie | ACU \ vCPU | vCPU: Core |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1-A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5-A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2-A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2-A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8-A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1-D14](sizes-previous-gen.md) |160-250 | 1:1 |
| [D1_v2-D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1-DS14](sizes-previous-gen.md) |160-250 | 1:1 |
| [DS1_v2-DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2-F72s_v2](fsv2-series.md) |195 - 210* | 2:1\*\*\* |
| [F1-F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [D11-F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1-G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1-GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 - 216** | 1:1 |
| [KM](hc-series.md) |297 - 315* | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2-L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160-180 | 2:1\*\*\* |

\* ACUs gebruik Intel® Turbo-technologie om de CPU-frequentie te verhogen en een hogere prestatie te bieden.  De omvang van de prestatie verhoging kan variëren op basis van de VM-grootte, werk belasting en andere workloads die op dezelfde host worden uitgevoerd.
\* * ACUs gebruik AMD® Boost-technologie om de CPU-frequentie te verhogen en een betere prestaties te leveren.  De omvang van de prestatie verhoging kan variëren op basis van de VM-grootte, werk belasting en andere workloads die op dezelfde host worden uitgevoerd.
Hyper-Threaded en geschikt voor het uitvoeren van geneste virtualisatie

Hier vindt u koppelingen naar meer informatie over de verschillende grootten:

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Geoptimaliseerde opslag](sizes-storage.md)