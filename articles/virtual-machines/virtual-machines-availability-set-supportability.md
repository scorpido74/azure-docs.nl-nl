---
title: Ondersteuning van het toevoegen van virtuele Azure-machines aan een bestaande beschikbaarheidsset | Microsoft Docs
description: Dit artikel bevat een ondersteunings matrix over welke VM-reeks u in dezelfde beschikbaarheidsset kunt combi neren
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028417"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Ondersteuning van het toevoegen van Azure-Vm's aan een bestaande beschikbaarheidsset

U kunt af en toe beperkingen ondervinden bij het toevoegen van nieuwe virtuele machines (Vm's) aan een bestaande beschikbaarheidsset. De volgende tabel bevat informatie over de VM-reeks die u kunt combi neren in dezelfde beschikbaarheidsset.

Dit is de ondersteunings matrix voor het combi neren van verschillende typen Vm's:

Beschikbaarheidsset & serie|Tweede VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Eerste VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Alle andere reeksen kunnen niet in dezelfde beschikbaarheidsset worden opgegeven omdat hiervoor een specifieke hardware is vereist.

De A8/A9 VM-grootte kan niet worden gemengd vanwege een vereiste op een toegewezen RDMA-back-end-netwerk.
