---
title: Problemen met de implementatie van windows-virtuele machines in Azure oplossen | Microsoft Documenten
description: Problemen met de implementatie van Windows-virtuele machines in het implementatiemodel azure resource manager oplossen.
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
ms.openlocfilehash: cdbaeb5a97beba342bc471e75d1b07be0d0141ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921408"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Problemen bij het implementeren van virtuele Windows-machines in Azure oplossen

Als u vm-implementatieproblemen (virtual machine) in Azure wilt oplossen, controleert u de [belangrijkste problemen](#top-issues) voor veelvoorkomende fouten en oplossingen.

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.

## <a name="top-issues"></a>Belangrijkste kwesties
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hoe kan ik een windows-clientafbeelding gebruiken en implementeren in Azure?

U Windows 7, Windows 8 of Windows 10 in Azure gebruiken voor dev/testscenario's als u een geschikt Visual Studio-abonnement (voorheen MSDN) hebt. In [dit artikel](../windows/client-images.md) worden de geschiktheidsvereisten beschreven voor het uitvoeren van Windows-client in Azure en het gebruik van de Azure Gallery-afbeeldingen.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hoe kan ik een virtuele machine implementeren met behulp van het Hybrid Use Benefit (HUB)?

Er zijn een paar verschillende manieren om virtuele Windows-machines te implementeren met het Azure Hybrid Use Benefit.

Voor een Enterprise Agreement-abonnement:

• Implementeer VM's uit specifieke Marketplace-afbeeldingen die vooraf zijn geconfigureerd met Azure Hybrid Use Benefit.

Voor enterprise-overeenkomst:

• Upload een aangepaste VM en implementeer met behulp van een Resource Manager-sjabloon of Azure PowerShell.

Zie de volgende bronnen voor meer informatie:

 - [Overzicht van azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Downloadbare veelgestelde vragen](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Azure Hybrid Use Benefit voor Windows Server en Windows Client](../windows/hybrid-use-benefit-licensing.md).

 - [Hoe kan ik het voordeel voor hybride gebruik in Azure gebruiken](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hoe activeer ik mijn maandelijkse tegoed voor Visual studio Enterprise (BizSpark)

Zie dit [artikel](https://azure.microsoft.com/offers/ms-azr-0064p/)om uw maandelijkse tegoed te activeren.

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Enterprise Dev/Test toevoegen aan mijn Enterprise Agreement (EA) om toegang te krijgen tot windows-clientafbeeldingen?

De mogelijkheid om abonnementen te maken op basis van de Enterprise Dev/Test-aanbieding is beperkt tot accounteigenaren die hiervoor toestemming hebben gekregen van een Enterprise Administrator. De accounteigenaar maakt abonnementen via de Azure-accountportal en moet vervolgens actieve Visual Studio-abonnees toevoegen als medebeheerders. Zodat ze de middelen kunnen beheren en gebruiken die nodig zijn voor ontwikkeling en testen. Zie [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) voor meer informatie.

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Mijn stuurprogramma's ontbreken voor mijn Windows N-Serie VM

Stuurprogramma's voor VM's op basis van Windows bevinden zich [hier.](../windows/n-series-driver-setup.md)

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Ik kan geen GPU-exemplaar vinden in mijn VM uit de N-serie

Als u wilt profiteren van de GPU-mogelijkheden van Azure N-serie VM's met Windows Server 2016 of Windows Server 2012 R2, moet u NVIDIA-grafische stuurprogramma's op elke VM installeren na implementatie. Gegevens over het instellen van stuurprogramma's zijn beschikbaar voor [Windows VM's](../windows/n-series-driver-setup.md) en [Linux VM's.](../linux/n-series-driver-setup.md)

## <a name="is-n-series-vms-available-in-my-region"></a>Zijn VM's uit de N-serie beschikbaar in mijn regio?

U de beschikbaarheid bekijken in de [tabel Producten die beschikbaar zijn per regio](https://azure.microsoft.com/regions/services)en de prijzen [hier.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Welke clientafbeeldingen kan ik gebruiken en implementeren in Azure en hoe krijg ik ze?

U Windows 7, Windows 8 of Windows 10 in Azure gebruiken voor dev/testscenario's, mits u een geschikt Visual Studio-abonnement (voorheen MSDN) hebt. 

- Windows 10-afbeeldingen zijn beschikbaar in de Azure Gallery in [in aanmerking komende dev/testaanbiedingen.](../windows/client-images.md#eligible-offers) 
- Visual Studio-abonnees binnen elk type aanbieding kunnen ook adequaat een 64-bits Windows 7-, Windows 8- of Windows 10-afbeelding [voorbereiden en maken](../windows/prepare-for-upload-vhd-image.md) en vervolgens [uploaden naar Azure.](../windows/upload-generalized-managed.md) Het gebruik blijft beperkt tot dev/test door actieve Visual Studio-abonnees.

In [dit artikel](../windows/client-images.md) worden de geschiktheidsvereisten beschreven voor het uitvoeren van Windows-client in Azure en het gebruik van de Azure Gallery-afbeeldingen.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Ik kan de VM Size-familie die ik wil niet zien bij het wijzigen van het formaat van mijn VM.

Wanneer een VM wordt uitgevoerd, wordt deze geïmplementeerd op een fysieke server. De fysieke servers in Azure-regio's zijn gegroepeerd in clusters van algemene fysieke hardware. Het wijzigen van het formaat van een VM waarvoor de VM moet worden verplaatst naar verschillende hardwareclusters, is afhankelijk van het implementatiemodel dat is gebruikt om de VM te implementeren.

- VM's geïmplementeerd in het klassieke implementatiemodel, moet de implementatie van de cloudservice worden verwijderd en opnieuw worden geïmplementeerd om de VM's te wijzigen in een grootte in een andere groottefamilie.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Vm's geïmplementeerd in Resource Manager-implementatiemodel, moet u alle VM's in de beschikbaarheidsset stoppen voordat u de grootte van een VM in de beschikbaarheidsset wijzigt.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>De vermelde VM-grootte wordt niet ondersteund tijdens het implementeren in beschikbaarheidsset.

Kies een grootte die wordt ondersteund op het cluster van de beschikbaarheidsset. Het wordt aanbevolen bij het maken van een beschikbaarheidsset om de grootste VM-grootte te kiezen die u denkt nodig te hebben en dat dit uw eerste implementatie naar de beschikbaarheidsset is.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan ik een bestaande Klassieke VM toevoegen aan een beschikbaarheidsset?

Ja. U een bestaande klassieke vm toevoegen aan een nieuwe of bestaande beschikbaarheidsset. Zie Een [bestaande virtuele machine toevoegen aan een beschikbaarheidsset voor](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)meer informatie.


## <a name="next-steps"></a>Volgende stappen
Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de FORUMS VOOR MSDN Azure en Stack Overflow.](https://azure.microsoft.com/support/forums/)

U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
