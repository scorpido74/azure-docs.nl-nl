---
title: Een Lab instellen met Gpu's in Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab met virtuele GPU-machines (graphics processing unit).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2020
ms.author: nicolela
ms.openlocfilehash: adac35bd3f59870f0c164b69548375610e9733b1
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897339"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Een Lab instellen met GPU virtual machines

In dit artikel leest u hoe u de volgende taken kunt uitvoeren:

- U kunt kiezen tussen *visualisatie* *en grafische gpu's (* graphics processing units).
- Zorg ervoor dat de juiste GPU-Stuur Programma's zijn geïnstalleerd.
- Configureer Remote Desktop Protocol-instellingen (RDP) om verbinding te maken met een virtuele GPU-machine (VM).

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Kiezen tussen visualisatie-en reken GPU-grootten
Selecteer op de eerste pagina van de wizard Lab maken in de vervolg keuzelijst **welke grootte van de virtuele machine nodig is?** de grootte van de virtuele machines die voor uw klasse nodig zijn.  

![Scherm afbeelding van het deel venster Nieuw Lab voor het selecteren van een VM-grootte](./media/how-to-setup-gpu/lab-gpu-selection.png)

In dit proces hebt u de mogelijkheid om een **visualisatie** of **reken** gpu's te selecteren.  Het is belang rijk om het type GPU te kiezen dat is gebaseerd op de software die uw studenten zullen gebruiken.  

Zoals beschreven in de volgende tabel is de *reken* GPU-grootte bedoeld voor computerintensieve toepassingen.  Het [diepe leer proces van het type natuurlijke taal verwerking](./class-type-deep-learning-natural-language-processing.md) maakt bijvoorbeeld gebruik van de grootte van de **kleine GPU (Compute)** .  De reken-GPU is geschikt voor dit type klasse, omdat studenten gebruikmaken van een diep geleerde frameworks en hulpprogram ma's die door de [Data Science virtual machine-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) worden verschaft om diepe leer modellen met grote gegevens sets te trainen.

| Grootte | Kernen | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Kleine GPU (Compute) | -&nbsp;6 &nbsp; kernen<br>-&nbsp;56 &nbsp; GB &nbsp; RAM-geheugen  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Deze grootte is het meest geschikt voor computer-intensieve toepassingen, zoals kunst matige intelligentie (AI) en diep gaande lessen. |

De grootte van de *visualisatie* GPU is bedoeld voor grafische intensieve toepassingen.  Bijvoorbeeld, het [type SolidWorks engineering](./class-type-solidworks.md) wordt weer gegeven met behulp van de grootte van de **kleine GPU (visualisatie)** .  De visualisatie GPU is geschikt voor dit type klasse, omdat studenten communiceren met de SolidWorks 3D computer-aided design (CAD)-omgeving voor het model leren en visualiseren van effen objecten.

| Grootte | Kernen | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Kleine GPU (visualisatie) | -&nbsp;6 &nbsp; kernen<br>-&nbsp;56 &nbsp; GB &nbsp; RAM-geheugen  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games en code ring waarbij frameworks zoals OpenGL en DirectX worden gebruikt. |
| Gemiddelde GPU (visualisatie) | -&nbsp;12 &nbsp; kernen<br>-&nbsp;112 &nbsp; GB &nbsp; RAM-geheugen  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games en code ring waarbij frameworks zoals OpenGL en DirectX worden gebruikt. |

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Zorg ervoor dat de juiste GPU-Stuur Programma's zijn geïnstalleerd
Zorg ervoor dat de juiste GPU-Stuur Programma's zijn geïnstalleerd om te profiteren van de GPU-mogelijkheden van uw Lab-Vm's.  Als u in de wizard Lab maken een GPU VM-grootte selecteert, kunt u de optie **GPU-Stuur Programma's installeren** selecteren.  

![Scherm afbeelding van de ' nieuwe Lab ' met de optie GPU-Stuur Programma's installeren](./media/how-to-setup-gpu/lab-gpu-drivers.png)

Zoals in de voor gaande afbeelding wordt weer gegeven, is deze optie standaard ingeschakeld, zodat u zeker weet dat de *meest recente* Stuur Programma's zijn geïnstalleerd voor het type GPU en de installatie kopie die u hebt geselecteerd.
- Wanneer u een *Compute* GPU-grootte selecteert, worden uw Lab-vm's aangedreven door de [Nvidia Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU.  In dit geval worden de meest recente [CUDA-Stuur programma's (Unified Device Architecture)](https://www.nvidia.com/object/io_69526.html) geïnstalleerd, waardoor computers met hoge prestaties kunnen worden uitgevoerd.
- Wanneer u een grootte van een *visualisatie* -GPU selecteert, worden uw Lab-vm's aangedreven door de [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU en de [raster technologie](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  In dit geval zijn de meest recente raster Stuur Programma's geïnstalleerd, waardoor het gebruik van grafische intensieve toepassingen mogelijk wordt.

> [!IMPORTANT]
> Als u de beste gebruikers ervaring met *visualisatie* gpu's wilt hebben, moet u ervoor zorgen dat *beide* Stuur Programma's zijn geïnstalleerd *en* de GPU wordt ingeschakeld via RDP-verbindingen. Zie de sectie [GPU via RDP-verbinding met Windows-Vm's inschakelen](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) in dit artikel voor meer informatie.

### <a name="install-the-drivers-manually"></a>De Stuur Programma's hand matig installeren
Mogelijk moet u een andere versie van een stuur programma installeren dan de nieuwste versie.  In deze sectie wordt beschreven hoe u de juiste Stuur Programma's hand matig installeert, afhankelijk van of u een *Compute* GPU of een *visualisatie* -GPU gebruikt.

#### <a name="install-the-compute-gpu-drivers"></a>De compute GPU-Stuur Programma's installeren

Ga als volgt te werk om stuur Programma's hand matig te installeren voor de reken GPU-grootte:

1. Schakel in de wizard Lab maken, wanneer u [uw Lab maakt](./how-to-manage-classroom-labs.md), de instelling **GPU-Stuur Programma's installeren** uit.

1. Nadat uw Lab is gemaakt, maakt u verbinding met de VM van de sjabloon om de juiste Stuur Programma's te installeren.

   ![Scherm afbeelding van de pagina met Down loads voor NVIDIA-Stuur Programma's](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. Ga in een browser naar de [pagina met Down loads voor nvidia-Stuur Programma's](https://www.nvidia.com/Download/index.aspx).  
   b. Stel het **product type** in op **Tesla**.  
   c. Stel de **product reeks** in op de **K-serie**.  
   d. Stel het **besturings systeem** in op basis van het type basis installatie kopie dat u hebt geselecteerd tijdens het maken van uw Lab.  
   e. Stel de **CUDA-werkset** in op de versie van het CUDA-stuur programma dat u nodig hebt.  
   f. Selecteer **zoeken** om uw stuur Programma's te zoeken.  
   g. Selecteer **downloaden** om het installatie programma te downloaden.  
   h. Voer het installatie programma uit zodat de Stuur Programma's op de sjabloon-VM worden geïnstalleerd.  
1. Controleer of de Stuur Programma's correct zijn geïnstalleerd door de instructies in de sectie [de geïnstalleerde Stuur Programma's valideren](how-to-setup-lab-gpu.md#validate-the-installed-drivers) te volgen. 
1. Nadat u de Stuur Programma's en andere software hebt geïnstalleerd die voor uw klasse zijn vereist, selecteert u **publiceren** om de vm's van uw studenten te maken.

> [!NOTE]
> Als u een Linux-installatie kopie gebruikt nadat u het installatie programma hebt gedownload, installeert u de Stuur Programma's door de instructies in [CUDA-Stuur Programma's installeren in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms)te volgen.

#### <a name="install-the-visualization-gpu-drivers"></a>De visualisatie GPU-Stuur Programma's installeren

Ga als volgt te werk om stuur Programma's hand matig te installeren voor de grootte van de visualisatie GPU:

1. Schakel in de wizard Lab maken, wanneer u [uw Lab maakt](./how-to-manage-classroom-labs.md), de instelling **GPU-Stuur Programma's installeren** uit.
1. Nadat uw Lab is gemaakt, maakt u verbinding met de VM van de sjabloon om de juiste Stuur Programma's te installeren.
1. Installeer de raster Stuur Programma's die door micro soft worden meegeleverd met de sjabloon-VM door de instructies voor uw besturings systeem te volgen:
   -  [Windows NVIDIA-raster Stuur Programma's](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA-raster Stuur Programma's](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Start de sjabloon-VM opnieuw op.
1. Controleer of de Stuur Programma's correct zijn geïnstalleerd door de instructies in de sectie [de geïnstalleerde Stuur Programma's valideren](how-to-setup-lab-gpu.md#validate-the-installed-drivers) te volgen.
1. Configureer RDP-instellingen om de GPU-verbinding in te scha kelen door de instructies in de sectie [GPU via RDP-verbinding met Windows-Vm's inschakelen te](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) volgen.
1. Nadat u de Stuur Programma's en andere software hebt geïnstalleerd die voor uw klasse zijn vereist, selecteert u **publiceren** om de vm's van uw studenten te maken.

### <a name="validate-the-installed-drivers"></a>De geïnstalleerde Stuur Programma's valideren
In deze sectie wordt beschreven hoe u controleert of uw GPU-Stuur Programma's goed zijn geïnstalleerd.

#### <a name="windows-images"></a>Windows-installatie kopieën
1.  Volg de instructies in de sectie ' installatie van Stuur Programma's controleren ' van [NVIDIA GPU-Stuur Programma's installeren op vm's uit de N-serie waarop Windows wordt uitgevoerd](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Als u een *visualisatie* -GPU gebruikt, kunt u ook het volgende doen:
    - Bekijk en wijzig uw GPU-instellingen in het NVIDIA-configuratie scherm. Hiertoe selecteert u **Hardware**in **het configuratie scherm van Windows**en selecteert u vervolgens **NVIDIA-configuratie scherm**.

      ![Scherm afbeelding van het configuratie scherm van Windows met de koppeling van het NVIDIA-configuratie scherm](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Bekijk uw GPU-prestaties met behulp van **taak beheer**.  Hiertoe selecteert u het tabblad **prestaties** en selecteert u vervolgens de optie **GPU** .

       ![Scherm opname van het tabblad GPU-prestaties van taak beheer](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > De instellingen van het configuratie scherm van NVIDIA zijn alleen toegankelijk voor *visualisatie* gpu's.  Als u het NVIDIA-configuratie scherm probeert te openen voor een compute-GPU, wordt de volgende fout weer gegeven: "NVIDIA-weergave-instellingen zijn niet beschikbaar.  Er wordt momenteel geen weer gave gebruikt die is gekoppeld aan een NVIDIA-GPU.  Op dezelfde manier wordt de GPU-prestatie gegevens in taak beheer alleen gegeven voor visualisatie Gpu's.

#### <a name="linux-images"></a>Linux-installatie kopieën
Volg de instructies in de sectie ' installatie van Stuur Programma's controleren ' van [NVIDIA GPU-Stuur Programma's installeren op vm's met N-serie waarop Linux wordt uitgevoerd](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>GPU via RDP-verbinding met Windows-Vm's inschakelen
Wanneer u RDP gebruikt om verbinding te maken met een virtuele Windows-machine die is voorzien van een *visualisatie* GPU, moet u een aantal extra configuratie stappen uitvoeren, zodat de GPU wordt gebruikt voor het renderen van afbeeldingen. Anders wordt de CPU gebruikt om grafische afbeeldingen weer te geven.

Ga als volgt te werk op de sjabloon-VM:

1. Configureer RDP-instellingen voor het gebruik van de GPU.

   a. Volg de instructies in [rendering van GPU-versnelde apps configureren](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering).  
   b. Volg de instructies in [GPU-versnelde frame codering configureren](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding).

1. Controleer de configuratie. 

   a. Volg de instructies in de [rendering van GPU-versnelde apps controleren](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering).  
   b. Volg de instructies in [GPU-versnelde frame codering controleren](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

1. U hebt nu de Stuur Programma's geïnstalleerd en de RDP-instellingen die zijn geconfigureerd voor het gebruik van uw GPU.  Nadat u de andere software hebt geïnstalleerd die voor uw klasse is vereist, kunt u **publiceren** selecteren om de vm's van uw studenten te maken.  

Wanneer uw studenten verbinding maken met hun virtuele machines met behulp van RDP, worden hun bureau bladen weer gegeven op basis van de GPU van hun virtuele machines.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Leslokaallabs maken en beheren](how-to-manage-classroom-labs.md)
- [Type SolidWorks computer-aided design (CAD)-klasse](class-type-solidworks.md)
- [Het klassen type MATLAB (matrix laboratorium)](class-type-matlab.md)



