---
title: Een gebruikers-vm-afbeelding maken voor de Azure Marketplace
description: Hier worden de stappen en verwijzingen weergegeven die nodig zijn om een vm-afbeelding van de gebruiker te maken.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6bbee7f53cb9a61b72bdbbd941a3a0401f5b913b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273950"
---
# <a name="create-a-user-vm-image"></a>Een VM-installatiekopie voor gebruikers maken

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Uw technische assets van Azure Virtual Machine maken](https://aka.ms/AzureVMTechAsset) om uw gemigreerde aanbiedingen te beheren.

In dit artikel worden de twee algemene stappen uitgelegd die nodig zijn om een onbeheerde afbeelding te maken van een gegeneraliseerde VHD.  Referenties worden verstrekt om u te begeleiden door elke stap: het vastleggen van de afbeelding en generaliseren van de afbeelding.


## <a name="capture-the-vm-image"></a>De VM-afbeelding vastleggen

Gebruik de instructies in het volgende artikel over het vastleggen van de VM die overeenkomt met uw toegangsbenadering:

-  PowerShell: [een onbeheerde VM-afbeelding maken van een Azure VM](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [een afbeelding van een virtuele machine of VHD maken](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtuele machines - Vastleggen](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>De VM-afbeelding generaliseren

Omdat u de gebruikersafbeelding hebt gegenereerd van een eerder gegeneraliseerde VHD, moet deze ook worden gegeneraliseerd.  Selecteer nogmaals het volgende artikel dat overeenkomt met uw toegangsmechanisme.  (Mogelijk hebt u uw schijf al gegeneraliseerd toen u deze hebt vastgelegd.)

-  PowerShell: [Generaleer de VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Stap 2: VM-afbeelding maken](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtuele machines - Generaliseren](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Volgende stappen

Vervolgens maakt u [een certificaat](cpp-create-key-vault-cert.md) en slaat u het op in een nieuwe Azure Key Vault.  Dit certificaat is vereist voor het tot stand brengen van een veilige WinRM-verbinding met de VM.
