---
title: Ondersteuning van het toevoegen van virtuele Azure-machines aan een bestaande beschikbaarheidsset | Microsoft Docs
description: Dit artikel bevat een ondersteunings matrix over welke VM-reeks u in dezelfde beschikbaarheidsset kunt combi neren
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77122920"
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
