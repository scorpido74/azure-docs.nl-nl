---
title: VM-installatiekopiën maken voor uw GPU-apparaat in Azure Stack Edge
description: Hierin wordt beschreven hoe u Linux- of Windows-VM-installatiekopieën maakt voor gebruik met uw GPU-apparaat in Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 83332c3bfa0b2b99d7333fa679fb8d398aecf8bd
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268907"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-device"></a>Aangepaste VM-installatiekopiën maken voor uw Azure Stack Edge-apparaat

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Om VM's te implementeren op uw Azure Stack Edge-apparaat, moet u aangepaste VM-installatiekopieën kunnen maken om VM's mee te maken. In dit artikel worden de stappen beschreven die nodig zijn voor het maken van aangepaste Linux- of Windows VM-installatiekopieën die u kunt gebruiken om VM's te implementeren op uw Azure Stack Edge-apparaat.

## <a name="vm-image-workflow"></a>Werkstroom voor VM-installatiekopie

Voor deze werkstroom moet u een virtuele machine maken in Azure, de VM aanpassen, generaliseren en vervolgens de VHD downloaden die hoort bij de virtuele machine. Deze gegeneraliseerde VHD wordt geüpload naar Azure Stack Edge en er wordt een beheerde schijf gemaakt op basis van die VHD. Vervolgens wordt er een installatiekopie gemaakt van de beheerde schijf en tot slot worden er op basis van die installatiekopie VM's gemaakt.   

Ga voor meer informatie naar [Een VM op uw Azure Stack Edge-apparaat implementeren met behulp van Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Een aangepaste VM-installatiekopie voor Windows maken

Voer de volgende stappen uit om een installatiekopie voor een Windows-VM te maken.

1. Maak een virtuele Windows-machine. Voor meer informatie gaat u naar [Zelfstudie: Windows-VM's maken en beheren met Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Download een bestaande besturingssysteemschijf.

    - Volg de stappen in [Een VHD downloaden](../virtual-machines/windows/download-vhd.md).

    - Gebruik de volgende `sysprep`-opdracht in plaats van wat wordt beschreven in de voorgaande procedure.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       U kunt ook verwijzen naar [Overzicht van Sysprep (systeemvoorbereiding)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Gebruik deze VHD om nu een virtuele machine te maken en te implementeren op uw Azure Stack Edge-apparaat.

## <a name="create-a-linux-custom-vm-image"></a>Een aangepaste VM-installatiekopie voor Linux maken

Voer de volgende stappen uit om een installatiekopie voor een Linux-VM te maken.

1. Maak een virtuele Linux-machine. Voor meer informatie gaat u naar [Zelfstudie: Virtuele Linux-machines maken en beheren met de Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

2. [Download een bestaande besturingssysteemschijf.](../virtual-machines/linux/download-vhd.md)

Gebruik deze VHD om nu een virtuele machine te maken en te implementeren op uw Azure Stack Edge-apparaat. U kunt de volgende twee Azure Marketplace-installatiekopieën gebruiken om aangepaste Linux-installatiekopieën te maken:

|Itemnaam  |Description  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server is de meest populaire Linux-distributie voor cloudomgevingen ter wereld.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux is een van de populairste Linux-distributies.     |credativ|

Ga voor een volledige lijst met installatiekopiën op de Azure Marketplace die kunnen werken (nog niet getest) naar [Azure Marketplace-items die beschikbaar zijn voor Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Volgende stappen

[Implementeer VM's op uw Azure Stack Edge-apparaat](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
