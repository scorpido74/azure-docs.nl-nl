---
title: Aanvragen voor het verhogen van Azure Resource Manager vCPU-quota
description: Aanvragen voor het verhogen van Azure Resource Manager vCPU-quota
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843664"
---
# <a name="quota-increase-requests"></a>Aanvragen voor quotumverhoging

Resource Manager vCPU-quota voor virtuele machines en virtuele machineschaalsets worden afgedwongen op twee lagen voor elk abonnement, in elke regio.

De eerste laag is de limiet voor de totale regionale vCPU's voor alle VM-reeksen. De tweede laag is de vCPU's limiet voor de VCPU's per VM-reeks, zoals de VCPU's uit de D-reeks. Wanneer een nieuwe virtuele machine moet worden geïmplementeerd, mag de som van nieuw en bestaand vCPU'sgebruik voor die VM-serie niet hoger zijn dan het vCPU-quotum dat voor die specifieke VM-reeks is goedgekeurd. Bovendien mag het totale aantal nieuwe en bestaande vCPU's dat voor alle VM-reeksen is geïmplementeerd, niet hoger zijn dan het totale regionale vCPU-quotum dat voor het abonnement is goedgekeurd. Als een van deze quota wordt overschreden, is de VM-implementatie niet toegestaan.
U een verhoging van de vCPU-quotumlimiet voor de VM-reeks aanvragen via Azure-portal. Een verhoging van het VM-seriequotum verhoogt automatisch de limiet voor de totale regionale vCPU's met hetzelfde bedrag.

Wanneer een nieuw abonnement wordt gemaakt, zijn de standaard total regional vCPU's mogelijk niet gelijk aan de som van standaard vCPU-quota voor alle afzonderlijke VM-reeksen. Dit feit kan resulteren in een abonnement met voldoende quota voor elke afzonderlijke VM-serie die u wilt implementeren. Het zou kunnen ontbreken genoeg quotum voor Total Regional vCPU's voor alle implementaties. In dit geval moet u een verzoek indienen om de limiet voor totale regionale vCPU's expliciet te verhogen. De totale limiet voor regionale vCPU's mag de som van het goedgekeurde quotum voor alle VM-reeksen voor de regio niet overschrijden.

Zie [VCPU-quota voor virtuele machines](../../virtual-machines/windows/quotas.md) en [Azure-abonnements- en servicelimieten, quota en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md).

