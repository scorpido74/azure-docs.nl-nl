---
title: Ondersteuning van het toevoegen van virtuele Azure-machines aan een bestaande beschikbaarheidsset | Microsoft Docs
description: Ondersteuning van het toevoegen van Azure-Vm's aan een bestaande beschikbaarheidsset.
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
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155445"
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
