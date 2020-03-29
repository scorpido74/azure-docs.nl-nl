---
title: Meer informatie over orchestration-modi voor virtuele machineschaalsets in Azure
description: Meer informatie over orchestration-modi voor virtuele machineschaalsets in Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279064"
---
# <a name="orchestration-mode-preview"></a>Orchestration-modus (voorbeeld)

Virtuele machines schaalsets bieden een logische groepering van platform-beheerde virtuele machines. Met schaalsets maakt u een configuratiemodel voor virtuele machines, voegt u automatisch extra exemplaren toe of verwijdert u deze op basis van CPU- of geheugenbelasting en upgradet u automatisch naar de nieuwste versie van het besturingssysteem. Met schaalsets u traditioneel virtuele machines maken met behulp van een VM-configuratiemodel dat wordt geleverd op het moment van het maken van de schaalset, en de schaalset kan alleen virtuele machines beheren die impliciet zijn gemaakt op basis van het configuratiemodel.

Met de schaalsetorchestration-modus (voorbeeld) u nu kiezen of de schaalset virtuele machines moet orkestreren die expliciet buiten een configuratiemodel van een schaalset zijn gemaakt, of virtuele machine-exemplaren die impliciet zijn gemaakt op basis van de configuratiemodel. Met de orkestratiemodus van scaleset u ook uw VM-infrastructuur ontwerpen voor hoge beschikbaarheid door deze VM's in foutdomeinen en beschikbaarheidszones te implementeren.


Virtuele machineschaalsets ondersteunen 2 verschillende orkestratiemodi:

- ScaleSetVM – Virtuele machine-exemplaren die aan de schaalset zijn toegevoegd, zijn gebaseerd op het configuratiemodel voor schaalsets. De levenscyclus van virtuele machine-exemplaar's - maken, bijwerken, verwijderen - wordt beheerd door de schaalset.
- VM (virtuele machines) – Virtuele machines die buiten de schaalset zijn gemaakt, kunnen expliciet aan de schaalset worden toegevoegd. 
 

> [!IMPORTANT]
> De orkestratiemodus wordt gedefinieerd wanneer u de schaalset maakt en kan later niet worden gewijzigd of bijgewerkt. 
> 
> Deze functie van virtuele machineschaalsets is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.


## <a name="orchestration-modes"></a>Indelingsmodi

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| VM-configuratiemodel      | Geen                                       | Vereist |
| Nieuwe VM toevoegen aan schaalset  | VM's worden expliciet toegevoegd aan de schaalset wanneer de VM wordt gemaakt. | VM's worden impliciet gemaakt en toegevoegd aan de schaalset op basis van het VM-configuratiemodel, het aantal instance's en de regels Voor automatisch schalen | |
| VM verwijderen                   | VM's moeten afzonderlijk worden verwijderd, de schaalset wordt niet verwijderd als er VM's in zitten. | VM's kunnen afzonderlijk worden verwijderd, als u de schaalset verwijdert, worden alle VM-exemplaren verwijderd.  |
| VM's koppelen/loskoppelen           | Niet ondersteund                              | Niet ondersteund |
| Levenscyclus van instantie (maken via verwijdering) | VM's en hun artefacten (zoals schijven en NIC's) kunnen onafhankelijk worden beheerd. | Instanties en hun artefacten (zoals schijven en NIC's) zijn impliciet voor de schaalset-instanties die ze maken. Ze kunnen niet losworden of afzonderlijk worden beheerd buiten de schaalset |
| Foutdomeinen               | Kan foutdomeinen definiëren. 2 of 3 op basis van regionale ondersteuning en 5 voor beschikbaarheidszone. | Kan foutdomeinen definiëren die van 1 tot en met 5 gaan |
| Updatedomeinen              | Updatedomeinen worden automatisch toegewezen aan foutdomeinen | Updatedomeinen worden automatisch toegewezen aan foutdomeinen |
| Beschikbaarheidszones          | Ondersteunt regionale implementatie of VM's in één beschikbaarheidszone | Ondersteunt regionale implementatie of meerdere beschikbaarheidszones; Kan de zone balanceringsstrategie definiëren |
| Automatisch schalen                   | Niet ondersteund                              | Ondersteund |
| OS-upgrade                  | Niet ondersteund                              | Ondersteund |
| Modelupdates               | Niet ondersteund                              | Ondersteund |
| Instantiebesturingselement            | Volledige VM-controle. VM's hebben volledig gekwalificeerde URI die het volledige scala aan Azure VM-beheermogelijkheden ondersteunen (zoals Azure Policy, Azure Backup en Azure Site Recovery) | VM's zijn afhankelijke resources van de schaalset. Instanties zijn alleen toegankelijk voor beheer via de schaalset. |
| Instantiemodel              | Microsoft.Compute/VirtualMachines-modeldefinitie. | Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines-modeldefinitie. |
| Capaciteit                    | Er kan een lege schaalset worden gemaakt; maximaal 200 VM's kunnen worden toegevoegd aan de schaalset | Schaalsets kunnen worden gedefinieerd met een instantietelling 0 - 1000 |
| Verplaatsen                        | Ondersteund                                  | Ondersteund |
| Groep voor enkele plaatsing == false | Niet ondersteund                          | Ondersteund |


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie het [overzicht van beschikbaarheidsopties](availability.md).
