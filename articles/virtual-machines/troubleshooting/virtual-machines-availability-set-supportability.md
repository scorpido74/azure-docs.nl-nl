---
title: Ondersteuning voor het toevoegen van Azure VM's aan een bestaande beschikbaarheidsset | Microsoft Documenten
description: Dit artikel bevat een ondersteuningsmatrix over welke VM-reeksen u mixen in dezelfde beschikbaarheidsset
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122920"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Ondersteuning voor het toevoegen van Azure VM's aan een bestaande beschikbaarheidsset

U af en toe beperkingen tegenkomen wanneer u nieuwe virtuele machines (VM's) toevoegt aan een bestaande beschikbaarheidsset. In de volgende grafiek wordt beschreven welke VM-reeksen u mixen in dezelfde beschikbaarheidsset.

Hier is de ondersteuningsmatrix om verschillende soorten VM's te mengen:

Beschikbaarheidsset & reeks|Tweede VM|A|Av2|D|Dv2 Dv2|Dv3 Dv3|
|---|---|---|---|---|---|---|
|Eerste VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2 Dv2||OK|OK|OK|OK|OK|
|Dv3 Dv3||OK|OK|OK|OK|OK|

Alle andere series kunnen niet in dezelfde beschikbaarheidsset zijn, omdat ze een specifieke hardware vereisen.

A8/A9 VM-grootte kan niet worden gemengd als gevolg van vereiste op dedicated RDMA backend netwerk.
