---
title: Virtuele machines importeren uit een ander lab in Azure DevTest Labs
description: In dit artikel wordt beschreven hoe u virtuele machines uit een ander lab importeert naar het huidige lab in Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759513"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Virtuele machines importeren uit een ander lab in Azure DevTest Labs
In dit artikel vindt u informatie over het importeren van virtuele machines uit een ander lab in uw lab.

## <a name="scenarios"></a>Scenario's
Hier volgen enkele scenario's waarin u VM's van het ene lab naar het andere lab moet importeren:

- Een persoon in het team verhuist naar een andere groep binnen de onderneming en wil de ontwikkelaar desktop te nemen om het nieuwe team DevTest Labs.
- De groep heeft een [quotum op abonnementsniveau](../azure-resource-manager/management/azure-subscription-service-limits.md) bereikt en wil de teams opsplitsen in een paar abonnementen
- Het bedrijf verhuist naar Express Route (of een andere nieuwe netwerktopologie) en het team wil de Virtuele Machines verplaatsen om deze nieuwe infrastructuur te gebruiken

## <a name="solution-and-constraints"></a>Oplossing en beperkingen
Met deze functie u VM's importeren in het ene lab (bron) naar een ander lab (bestemming). U optioneel een nieuwe naam geven voor de doel-VM in het proces. Het importproces omvat alle afhankelijkheden zoals schijven, schema's, netwerkinstellingen, enzovoort.

Het proces duurt enige tijd en wordt beïnvloed door de volgende factoren:

- Aantal/grootte van de schijven die aan de bronmachine zijn gekoppeld (omdat het een kopieerbewerking is en geen verplaatsingsbewerking)
- Afstand tot de bestemming (bijvoorbeeld de regio Oost-VS naar Zuidoost-Azië).

Zodra het proces is voltooid, blijft de bron Virtual Machine afsluiten en wordt de nieuwe in het doellab uitgevoerd.

Er zijn twee belangrijke beperkingen om rekening mee te houden bij het importeren van VM's van het ene lab naar een ander lab:

- De invoer van virtuele machines voor abonnementen en in verschillende regio's wordt ondersteund, maar de abonnementen moeten worden gekoppeld aan dezelfde Azure Active Directory-tenant.
- Virtuele machines mogen zich niet in een claimbare status in het bronlab begeven.
- U bent de eigenaar van de VM in het bronlab en eigenaar van het lab in het doellab.
- Momenteel wordt deze functie alleen ondersteund via Powershell en REST API.

## <a name="use-powershell"></a>PowerShell gebruiken
Download ImportVirtualMachines.ps1-bestand van de [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). U het script gebruiken om één VM of alle VM's in het bronlab in het doellab te importeren.

### <a name="use-powershell-to-import-a-single-vm"></a>PowerShell gebruiken om één virtuele machine te importeren
Voor het uitvoeren van dit powershell-script moet u de bron-VM en het doellab identificeren en optioneel een nieuwe naam leveren die voor de doelmachine moet worden gebruikt:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>PowerShell gebruiken om alle VM's in het bronlab te importeren
Als de bronvirtuele machine niet is opgegeven, importeert het script automatisch alle VM's in de DevTest Labs.  Bijvoorbeeld:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST gebruiken om een VM te importeren
De REST oproep is eenvoudig. U geeft voldoende informatie om de bron- en bestemmingsbronnen te identificeren. Vergeet niet dat de bewerking plaatsvindt op de resource van het doellab.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Beleid instellen voor een lab](devtest-lab-get-started-with-lab-policies.md)
- [Veelgestelde vragen](devtest-lab-faq.md)
