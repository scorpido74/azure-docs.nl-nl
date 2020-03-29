---
title: Problemen met de implementatie van linux-virtuele machines in Azure oplossen | Microsoft Documenten
description: Problemen met de implementatie van Linux-virtuele machines in het azure resource beheer-implementatiemodel oplossen.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 5a8a58a3935b7cd5efb8565f7e3278ccaae4f4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921433"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Problemen bij het implementeren van virtuele Linux-machines in Azure oplossen

Als u vm-implementatieproblemen (virtual machine) in Azure wilt oplossen, controleert u de [belangrijkste problemen](#top-issues) voor veelvoorkomende fouten en oplossingen.

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.

## <a name="top-issues"></a>Belangrijkste kwesties
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Het cluster kan de gevraagde VM-grootte niet ondersteunen
\<eigenschappen supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Probeer de aanvraag opnieuw met een kleiner VM-formaat.
- Als de grootte van de gevraagde vm niet kan worden gewijzigd:
    - Stop alle VM's in de beschikbaarheidsset. Klik op **Resourcegroepen** > uw resourcegroep > **Resources** > uw beschikbaarheidsset > **virtuele machines** > uw virtuele machine > **Stoppen**.
    - Nadat alle VM's stoppen, maakt u de VM in de gewenste grootte.
    - Start eerst de nieuwe vm en selecteer vervolgens elk van de gestopte VM's en klik op Start.


## <a name="the-cluster-does-not-have-free-resources"></a>Het cluster beschikt niet over gratis bronnen
\<eigenschappen supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Probeer het verzoek later opnieuw.
- Als de nieuwe virtuele machine deel kan uitmaken van een andere beschikbaarheidsset
    - Een VM maken in een andere beschikbaarheidsset (in dezelfde regio).
    - Voeg de nieuwe vm toe aan hetzelfde virtuele netwerk.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hoe activeer ik mijn maandelijkse tegoed voor Visual studio Enterprise (BizSpark)

Zie dit [artikel](https://azure.microsoft.com/offers/ms-azr-0064p/)om uw maandelijkse tegoed te activeren.

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Waarom kan ik het GPU-stuurprogramma niet installeren voor een Ubuntu NV VM?

Momenteel is Linux GPU-ondersteuning alleen beschikbaar op Azure NC VM's met Ubuntu Server 16.04 LTS. Zie [GPU-stuurprogramma's instellen voor Vm's uit de N-serie met Linux](../linux/n-series-driver-setup.md)instellen voor meer informatie.

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Mijn drivers ontbreken voor mijn Linux N-Series VM

Drivers voor Linux-gebaseerde VM's bevinden zich [hier.](../linux/n-series-driver-setup.md) 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Ik kan geen GPU-exemplaar vinden in mijn VM uit de N-serie

Als u wilt profiteren van de GPU-mogelijkheden van Azure N-serie VM's met Windows Server 2016 of Windows Server 2012 R2, moet u NVIDIA-grafische stuurprogramma's op elke VM installeren na implementatie. Gegevens over het instellen van stuurprogramma's zijn beschikbaar voor [Windows VM's](../windows/n-series-driver-setup.md) en [Linux VM's.](../linux/n-series-driver-setup.md)

## <a name="is-n-series-vms-available-in-my-region"></a>Zijn VM's uit de N-serie beschikbaar in mijn regio?

U de beschikbaarheid bekijken in de [tabel Producten die beschikbaar zijn per regio](https://azure.microsoft.com/regions/services)en de prijzen [hier.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Ik kan de VM Size-familie die ik wil niet zien bij het wijzigen van het formaat van mijn VM.

Wanneer een VM wordt uitgevoerd, wordt deze geïmplementeerd op een fysieke server. De fysieke servers in Azure-regio's zijn gegroepeerd in clusters van algemene fysieke hardware. Het wijzigen van het formaat van een VM waarvoor de VM moet worden verplaatst naar verschillende hardwareclusters, is afhankelijk van het implementatiemodel dat is gebruikt om de VM te implementeren.

- VM's geïmplementeerd in het klassieke implementatiemodel, moet de implementatie van de cloudservice worden verwijderd en opnieuw worden geïmplementeerd om de VM's te wijzigen in een grootte in een andere groottefamilie.

- Vm's geïmplementeerd in Resource Manager-implementatiemodel, moet u alle VM's in de beschikbaarheidsset stoppen voordat u de grootte van een VM in de beschikbaarheidsset wijzigt.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>De vermelde VM-grootte wordt niet ondersteund tijdens het implementeren in beschikbaarheidsset.

Kies een grootte die wordt ondersteund op het cluster van de beschikbaarheidsset. Het wordt aanbevolen bij het maken van een beschikbaarheidsset om de grootste VM-grootte te kiezen die u denkt nodig te hebben en dat dit uw eerste implementatie naar de beschikbaarheidsset is.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Welke Linux-distributies/versies worden ondersteund op Azure?

U vindt de lijst op Linux op [Azure-endorsed Distributions](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan ik een bestaande Klassieke VM toevoegen aan een beschikbaarheidsset?

Ja. U een bestaande klassieke vm toevoegen aan een nieuwe of bestaande beschikbaarheidsset. Zie Een [bestaande virtuele machine toevoegen aan een beschikbaarheidsset voor](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)meer informatie.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Volgende stappen
Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/)

U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
