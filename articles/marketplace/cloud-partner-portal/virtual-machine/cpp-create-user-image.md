---
title: Een VM-installatie kopie van een gebruiker maken voor Azure Marketplace
description: Hierin worden de stappen en verwijzingen vermeld die zijn vereist voor het maken van een VM-installatie kopie van een gebruiker.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e63f09dc538c5e66b244826cf3b5f92ac388b6a9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818703"
---
# <a name="create-a-user-vm-image"></a>Een VM-installatiekopie voor gebruikers maken

In dit artikel worden de twee algemene stappen beschreven die nodig zijn voor het maken van een onbeheerde installatie kopie van een gegeneraliseerde VHD.  U vindt de volgende informatie om u te helpen bij elke stap: de installatie kopie vastleggen en de installatie kopie generalize.


## <a name="capture-the-vm-image"></a>De VM-installatie kopie vastleggen

Volg de instructies in het volgende artikel over het vastleggen van de virtuele machine die overeenkomt met uw toegangs benadering:

-  Power shell: [een niet-beheerde VM-installatie kopie maken op basis van een virtuele Azure-machine](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [een installatie kopie van een virtuele machine of VHD maken](../../../virtual-machines/linux/capture-image.md)
-  API: [virtual machines-vastleggen](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>De VM-installatie kopie generaliseren

Omdat u de gebruikers installatie kopie van een eerder gegeneraliseerde VHD hebt gegenereerd, moet deze ook worden gegeneraliseerd.  Selecteer opnieuw het volgende artikel dat overeenkomt met uw toegangs mechanisme.  (Mogelijk hebt u uw schijf al gegeneraliseerd toen u deze hebt vastgelegd.)

-  Power shell: [de virtuele machine generaliseren](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [stap 2: VM-installatie kopie maken](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [virtual machines-generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Volgende stappen

Vervolgens gaat u [een certificaat maken](cpp-create-key-vault-cert.md) en opslaan in een nieuwe Azure Key Vault.  Dit certificaat is vereist voor het tot stand brengen van een beveiligde WinRM-verbinding met de virtuele machine.
