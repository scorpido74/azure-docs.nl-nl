---
title: Meer informatie over Orchestration-modi voor schaal sets voor virtuele machines in azure
description: Meer informatie over Orchestration-modi voor schaal sets voor virtuele machines in Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279064"
---
# <a name="orchestration-mode-preview"></a>Orchestration-modus (preview-versie)

Virtuele-machine schaal sets bieden een logische groepering van door het platform beheerde virtuele machines. Met schaal sets kunt u een configuratie model voor virtuele machines maken, automatisch extra exemplaren toevoegen of verwijderen op basis van CPU-of geheugen belasting en automatisch upgraden naar de meest recente versie van het besturings systeem. In de meeste gevallen kunt u met schaal sets virtuele machines maken met behulp van een VM-configuratie model dat is opgegeven op het moment dat de schaalset wordt gemaakt. de schaalset kan alleen virtuele machines beheren die impliciet zijn gemaakt op basis van het configuratie model.

Met de Orchestration-modus schalen (preview) kunt u nu kiezen of de schaalset virtuele machines moet organiseren die expliciet worden gemaakt buiten een configuratie model van een schaalset, of dat er impliciet virtuele-machine-instanties zijn gemaakt op basis van de configuratie model. De Orchestration-modus voor schaal sets helpt u ook bij het ontwerpen van uw VM-infra structuur voor hoge Beschik baarheid door de implementatie van deze Vm's in fout domeinen en Beschikbaarheidszones.


Virtuele-machine schaal sets ondersteunen 2 verschillende indelings modi:

- ScaleSetVM: virtuele-machine-instanties die zijn toegevoegd aan de schaalset zijn gebaseerd op het configuratie model van de schaalset. De levens cyclus van de virtuele machine-instantie-maken, bijwerken, verwijderen, wordt beheerd door de schaalset.
- VM (virtuele machines): virtuele machines die buiten de schaalset zijn gemaakt, kunnen expliciet worden toegevoegd aan de schaalset. 
 

> [!IMPORTANT]
> De Orchestration-modus wordt gedefinieerd wanneer u de schaalset maakt en kan later niet worden gewijzigd of bijgewerkt. 
> 
> Deze functie van virtuele-machine schaal sets is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="orchestration-modes"></a>Indelingsmodi

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| VM-configuratie model      | Geen                                       | Verplicht |
| Nieuwe VM toevoegen aan Schaalset  | Vm's worden expliciet toegevoegd aan de schaalset wanneer de virtuele machine wordt gemaakt. | Vm's worden impliciet gemaakt en toegevoegd aan de schaalset op basis van het VM-configuratie model, het aantal instanties en de regels voor automatisch schalen | |
| VM verwijderen                   | Vm's moeten afzonderlijk worden verwijderd. de schaalset wordt niet verwijderd als er virtuele machines in de set aanwezig zijn. | Vm's kunnen afzonderlijk worden verwijderd. Als u de schaalset verwijdert, worden alle VM-exemplaren verwijderd.  |
| Vm's koppelen/loskoppelen           | Niet ondersteund                              | Niet ondersteund |
| Levens cyclus van instanties (maken via verwijderen) | Vm's en hun artefacten (zoals schijven en Nic's) kunnen onafhankelijk worden beheerd. | Instanties en hun artefacten (zoals schijven en Nic's) zijn impliciet voor de instanties van de schaalset die ze maken. Ze kunnen niet afzonderlijk buiten de schaalset worden losgekoppeld of beheerd |
| Foutdomeinen               | Kan fout domeinen definiëren. 2 of 3 op basis van regionale ondersteuning en 5 voor de beschikbaarheids zone. | Kan fout domeinen definiëren van 1 tot en met 5 |
| Updatedomeinen              | Update domeinen worden automatisch toegewezen aan fout domeinen | Update domeinen worden automatisch toegewezen aan fout domeinen |
| Beschikbaarheidszones          | Ondersteunt regionale implementaties of virtuele machines in één beschikbaarheids zone | Ondersteunt regionale implementatie of meerdere Beschikbaarheidszones; Kan de strategie voor zone verdeling definiëren |
| Automatisch schalen                   | Niet ondersteund                              | Ondersteund |
| OS-upgrade                  | Niet ondersteund                              | Ondersteund |
| Model updates               | Niet ondersteund                              | Ondersteund |
| Besturings element exemplaar            | Volledig VM-besturings element. Vm's hebben een volledig gekwalificeerde URI die ondersteuning biedt voor het volledige bereik van Azure VM-beheer mogelijkheden (zoals Azure Policy, Azure Backup en Azure Site Recovery) | Vm's zijn afhankelijke resources van de schaalset. Exemplaren kunnen alleen via de schaalset worden geopend voor beheer. |
| Instantie model              | Model definitie voor micro soft. Compute/informatie. | Model definitie voor micro soft. Compute/VirtualMachineScaleSets/informatie. |
| Capaciteit                    | Er kan een lege schaalset worden gemaakt. Maxi maal 200 Vm's kunnen worden toegevoegd aan de schaalset | Schaal sets kunnen worden gedefinieerd met een aantal exemplaren van 0-1000 |
| Verplaatsen                        | Ondersteund                                  | Ondersteund |
| Enkelvoudige plaatsings groep = = onwaar | Niet ondersteund                          | Ondersteund |


## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van beschikbaarheids opties](availability.md)voor meer informatie.
