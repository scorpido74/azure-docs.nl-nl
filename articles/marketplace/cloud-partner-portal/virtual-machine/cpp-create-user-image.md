---
title: Een VM-installatie kopie van een gebruiker maken voor Azure Marketplace
description: Hierin worden de stappen en verwijzingen vermeld die zijn vereist voor het maken van een VM-installatie kopie van een gebruiker.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9d82d50769925480d461c122096c3919d7e8940d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146574"
---
# <a name="create-a-user-vm-image"></a>Een VM-installatiekopie voor gebruikers maken

> [!IMPORTANT]
> Vanaf 13 april 2020 begint het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in de [technische assets van uw virtuele Azure-machines maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) om uw gemigreerde aanbiedingen te beheren.

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
