---
title: Een gebruikers-vm-afbeelding maken voor de Azure Marketplace
description: Hier worden de stappen en verwijzingen weergegeven die nodig zijn om een vm-afbeelding van de gebruiker te maken.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 49db8c6717cd26886c3b49f8c99fdd2b08e8713d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278004"
---
# <a name="create-a-user-vm-image"></a>Een VM-installatiekopie voor gebruikers maken

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
