---
title: VCPU-aanvragen voor verg Roten quota Azure Resource Manager | Microsoft Docs
description: VCPU-aanvragen voor quota verhogen Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897102"
---
# <a name="quota-increase-requests"></a>Aanvragen voor quotumverhoging

Resource Manager-vCPU quota voor virtuele machines en virtuele-machine schaal sets worden afgedwongen op twee lagen voor elk abonnement, in elke regio. 

De eerste laag is de totale regionale Vcpu's limiet (in alle VM-reeksen) en de tweede laag is de limiet van de VM-reeks Vcpu's (zoals de Vcpu's van de D-serie). Telkens wanneer een nieuwe VM moet worden geïmplementeerd, mag de som van het nieuwe en bestaande Vcpu's-gebruik voor die VM-reeks niet groter zijn dan het vCPU-quotum dat is goedgekeurd voor die bepaalde VM-reeks. Verder mag het totale nieuwe en bestaande vCPU aantal dat is geïmplementeerd in alle VM-reeksen, niet groter zijn dan het totale regionale Vcpu's-quotum dat is goedgekeurd voor het abonnement. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan.
U kunt een toename van de Vcpu's-quotum limiet voor de VM-serie aanvragen van Azure Portal. Een toename in het quotum van de VM-reeks verhoogt automatisch de totale regionale Vcpu's limiet met hetzelfde bedrag. 

Wanneer een nieuw abonnement wordt gemaakt, is de standaard regionale Vcpu's mogelijk niet gelijk aan de som van de standaard vCPU-quota voor alle afzonderlijke VM-reeksen. Dit kan resulteren in een abonnement met voldoende quota voor elke afzonderlijke VM-reeks die u wilt implementeren, maar niet genoeg quotum voor het totale regionale Vcpu's voor alle implementaties. In dit geval moet u een aanvraag indienen om de limiet voor de totale regionale Vcpu's expliciet te verhogen. De limiet voor het totale regionale Vcpu's kan niet groter zijn dan de som van goedgekeurde quota voor alle VM-reeksen voor de regio.

Meer informatie over quota's vindt u op de pagina [vCPU quota van de virtuele machine](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) en de pagina [Azure-abonnement en service limieten](https://aka.ms/quotalimits) . 

