---
title: Foutcodes voor Azure Spot VM's en schaalsets instanties
description: Meer informatie over foutcodes die u mogelijk zien bij het gebruik van spot-VM's en schaalsetinstanties.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547815"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Foutberichten voor spotVM's en schaalsets

Hier zijn enkele mogelijke foutcodes die u ontvangen bij het gebruik van Spot VM's en schaalsets.


| Sleutel | Bericht | Beschrijving |
|-----|---------|-------------|
| SkuNotAvailable | De aangevraagde laag\<voor\>resource ' resource '\<\>is momenteel\<niet\>beschikbaar op locatie ' locatie ' voor abonnement ' abonnementID '. Probeer een andere laag of implementeer naar een andere locatie. | Er is niet genoeg Azure Spot-capaciteit op deze locatie om uw VM- of schaalsetinstantie te maken. |
| UitzettingsbeleidCanbesetOnlyonAzureSpotVirtualMachines  |  Uitzettingsbeleid kan alleen worden ingesteld op Virtuele Azure-machines. | Deze VM is geen spot-vm, dus u het uitzettingsbeleid niet instellen. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Azure Spot Virtual Machine wordt niet ondersteund in beschikbaarheidsset. | U moet ervoor kiezen om een Spot VM te gebruiken of een VM te gebruiken in een beschikbaarheidsset, u niet beide kiezen. |
| AzureSpotFeatureNotEnabledForSubscription  |  Abonnement niet ingeschakeld met Azure Spot-functie. | Gebruik een abonnement dat Spot VM's ondersteunt. |
| VMPriorityCannotBeApplied  |  De opgegeven prioriteitswaarde '{0}' kan{1}niet worden toegepast op de virtuele machine ' omdat er geen prioriteit is opgegeven toen de virtuele machine werd gemaakt. | Geef de prioriteit op wanneer de vm wordt gemaakt. |
| Spotprijsgroter dan voorzienmaxprice  |  Kan niet uitvoeren{0}' ' omdat{1} de opgegeven max prijs '{2} USD' lager is dan{3}de huidige spotprijs ' USD' voor Azure Spot VM-grootte '. | Selecteer een hogere maximumprijs. Zie prijsinformatie voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie.|
| MaxPriceValueOngeldig  |  Ongeldige maximale prijswaarde. De enige ondersteunde waarden voor de maximale prijs zijn -1 of een decimale meer dan nul. De maximale prijswaarde van -1 geeft aan dat de virtuele Azure Spot-machine om prijsredenen niet wordt uitgezet. | Voer een geldige maximumprijs in. Zie prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. |
| MaxPriceChangeNotAallowedForAllocatedVMs Max | Max prijswijziging is niet toegestaan{0}wanneer de VM ' ' momenteel wordt toegewezen. Gelieve te detoewijzen en probeer het opnieuw. | Stop\Detoewijzen van de VM, zodat u de maximale prijs wijzigen. |
| MaxPriceChangeNotA toegestaan | Max prijswijziging is niet toegestaan. | U de maximale prijs voor deze vm niet wijzigen. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure Spot wordt niet ondersteund voor deze API-versie. | De API-versie moet 2019-03-01 zijn. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure Spot wordt niet ondersteund {0}voor deze VM-grootte. | Selecteer een andere VM-grootte. Zie [Virtuele machines spotten voor](./linux/spot-vms.md)meer informatie. |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  De maximale prijs wordt alleen ondersteund voor Azure Spot Virtual Machines. | Zie [Virtuele machines spotten voor](./linux/spot-vms.md)meer informatie. |
| MoveResourcesWithAzureSpotVMNotOndersteund  |  De aanvraag voor verplaatsingsresources bevat een virtuele Azure Spot-machine. Dit wordt momenteel niet ondersteund. Controleer de foutgegevens voor virtuele machine-id's. | U spotVM's niet verplaatsen. |
| MoveResourcesWithAzureSpotVmssNotOndersteund  |  De aanvraag voor verplaatsingsresources bevat een Azure Spot-set voor virtuele machineschaal. Dit wordt momenteel niet ondersteund. Controleer de foutgegevens voor de ingestelde id's van virtuele machines. | U de instanties van de spotschaalniet verplaatsen. |
| EfemereOSDisksNietondersteundeForSpotVMs | Kortstondige OS-schijven worden niet ondersteund voor Spot VM's. | Gebruik een gewone osschijf voor uw Spot VM. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Azure Spot Virtual Machine wordt niet ondersteund in de virtuele machineschaalset met de VM-orchestration-modus. | Stel de orkestratiemodus in op virtuele machineschaal ingesteld om Spot-exemplaren te gebruiken. |


**Volgende stappen** Zie [virtuele machines ter plaatse voor](./linux/spot-vms.md)meer informatie.