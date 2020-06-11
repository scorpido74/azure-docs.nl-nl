---
title: Fout codes voor instanties van Azure spot Vm's en schaal sets
description: Meer informatie over de fout codes die mogelijk worden weer geven bij het gebruik van spot-Vm's en instanties van schaal sets.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 4625a0fa75f992ac18eeb91629af373a426aa955
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84676570"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Fout berichten voor de plaats van virtuele machines en schaal sets

Hier volgen enkele mogelijke fout codes die u kunt ontvangen bij het gebruik van spot-Vm's en schaal sets.


| Sleutel | Bericht | Beschrijving |
|-----|---------|-------------|
| SkuNotAvailable | De aangevraagde laag voor de resource \<resource\> is momenteel niet beschikbaar op de locatie \<location\> voor het abonnement \<subscriptionID\> . Probeer een andere laag of implementeer deze op een andere locatie. | Er is niet voldoende Azure spot-capaciteit op deze locatie om uw VM-of schaalset-exemplaar te maken. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Verwijderings beleid kan alleen worden ingesteld op Azure Spot Virtual Machines. | Deze VM is geen spot-VM, dus u kunt het verwijderings beleid niet instellen. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  De virtuele machine van Azure spot wordt niet ondersteund in de Beschikbaarheidsset. | U moet kiezen voor het gebruik van een spot-VM of voor het gebruik van een virtuele machine in een beschikbaarheidsset. u kunt niet beide opties kiezen. |
| AzureSpotFeatureNotEnabledForSubscription  |  Het abonnement is niet ingeschakeld met de functie voor de ondersteuning van Azure. | Gebruik een abonnement dat ondersteuning biedt voor spot Vm's. |
| VMPriorityCannotBeApplied  |  De opgegeven prioriteits waarde {0} kan niet worden toegepast op de virtuele machine {1} omdat er geen prioriteit is opgegeven tijdens het maken van de virtuele machine. | Geef de prioriteit op wanneer de virtuele machine wordt gemaakt. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Kan de bewerking niet uitvoeren {0} omdat de ingevoerde maximum prijs {1} USD lager is dan de huidige steun prijs ' {2} USD ' voor de VM-grootte van Azure spot ' {3} '. | Selecteer een hoger maximum prijs. Zie de prijs informatie voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie.|
| MaxPriceValueInvalid  |  Ongeldige waarde voor de maximum prijs. De enige ondersteunde waarden voor de maximum prijs zijn-1 of een decimale waarde groter dan nul. De waarde voor de maximale prijs van-1 geeft aan dat de virtuele machine van Azure Spot niet tegen prijs redenen wordt verwijderd. | Voer een geldige maximum prijs in. Zie prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. |
| MaxPriceChangeNotAllowedForAllocatedVMs | De maximale prijs wijziging is niet toegestaan wanneer de virtuele machine {0} momenteel is toegewezen. Maak de toewijzing ongedaan en probeer het opnieuw. | Stop\Deallocate de virtuele machine, zodat u de maximum prijs kunt wijzigen. |
| MaxPriceChangeNotAllowed | De maximale prijs wijziging is niet toegestaan. | U kunt de maximale prijs voor deze virtuele machine niet wijzigen. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure spot wordt niet ondersteund voor deze API-versie. | De API-versie moet 2019-03-01 zijn. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure spot wordt niet ondersteund voor deze VM-grootte {0} . | Selecteer een andere VM-grootte. Zie [Spot Virtual Machines](./linux/spot-vms.md)voor meer informatie. |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  De maximale prijs wordt alleen ondersteund voor Azure Spot Virtual Machines. | Zie [Spot Virtual Machines](./linux/spot-vms.md)voor meer informatie. |
| MoveResourcesWithAzureSpotVMNotSupported  |  De aanvraag voor het verplaatsen van resources bevat een Azure spot-virtuele machine. Dit wordt momenteel niet ondersteund. Raadpleeg de fout Details voor de Id's van de virtuele machine. | U kunt geen spot Vm's verplaatsen. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  De aanvraag resources verplaatsen bevat een Azure spot-schaalset voor virtuele machines. Dit wordt momenteel niet ondersteund. Raadpleeg de fout Details voor de virtuele-machine schaal sets-Id's. | U kunt geen instanties van de set met steun schalen verplaatsen. |
| EphemeralOSDisksNotSupportedForSpotVMs | Tijdelijke besturingssysteem schijven worden niet ondersteund voor spot-Vm's. | Gebruik een reguliere besturingssysteem schijf voor uw spot-VM. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | De virtuele machine van Azure spot wordt niet ondersteund in de Schaalset voor virtuele machines met de modus VM-indeling. | Stel de Orchestration-modus in op virtuele-machine schaal sets om spot instanties te gebruiken. |


**Volgende stappen** Zie [spot virtual machines](./linux/spot-vms.md)voor meer informatie.