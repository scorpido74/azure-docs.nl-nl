---
title: Een Lab instellen met Gpu's in Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab met GPU-virtuele machines (graphics processing unit).
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
ms.openlocfilehash: 9f4bc77366aaeaef9c66467e540219763228d317
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450771"
---
# <a name="set-up-a-lab-with-gpu-graphics-processing-unit-virtual-machine-size"></a>Een Lab instellen met GPU (graphics processing unit) grootte van virtuele machine
In dit artikel leest u hoe u de volgende taken kunt uitvoeren:

- Kiezen tussen virtualisatie en reken Gpu's
- Zorg ervoor dat de juiste GPU-Stuur Programma's zijn geïnstalleerd
- RDP-instellingen configureren om verbinding te maken met een GPU-virtuele machine (VM)

## <a name="choose-between-virtualization-and-compute-gpu-sizes"></a>Kiezen tussen virtualisatie en Compute GPU-grootten
Op de eerste pagina van de wizard Lab maken selecteert u de **grootte van de virtuele machines** die nodig zijn voor uw klasse.  

![VM-grootte selecteren](../media/how-to-setup-gpu/lab-gpu-selection.png)

Hier kunt u kiezen tussen **visualisatie** -en **Compute** -gpu's.  Het is belang rijk dat u het juiste type GPU kiest op basis van de software die uw studenten zullen gebruiken.  

Zoals beschreven in de onderstaande tabel, is de **reken** GPU-grootte bedoeld voor computerintensieve toepassingen.  Het [diepe leer proces van het type natuurlijke taal verwerker](./class-type-deep-learning-natural-language-processing.md) toont bijvoorbeeld de grootte van de **kleine GPU (Compute)** .  De **reken** -GPU is geschikt voor dit type klasse, omdat studenten gebruikmaken van diep gaande frameworks en hulpprogram ma's die door de [Data Science virtual machine-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) worden verschaft om diepe leer modellen met grote gegevens sets te trainen.

| Grootte | Kernen | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Kleine GPU (Compute) | <ul><li>6 kernen</li><li>56 GB RAM-GEHEUGEN</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Deze grootte is het meest geschikt voor computer-intensieve toepassingen, zoals kunst matige intelligentie en diep gaande lessen. |

De grootte van **Visualisaties** is bedoeld voor grafische intensieve toepassingen.  Bijvoorbeeld, het [type SolidWorks engineering](./class-type-solidworks.md) wordt weer gegeven met behulp van de grootte van de **kleine GPU (visualisatie)** .  De **visualisatie** GPU is geschikt voor dit type klasse, omdat studenten werken met de SolidWorks 3D computer-aided design (CAD)-omgeving voor het model leren en visualiseren van solide objecten.

| Grootte | Kernen | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Kleine GPU (visualisatie) | <ul><li>6 kernen</li><li>56 GB RAM-GEHEUGEN</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. |
| Gemiddelde GPU (visualisatie) | <ul><li>12 kernen</li><li>112 GB RAM-GEHEUGEN</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. |

## <a name="ensure-the-appropriate-gpu-drivers-are-installed"></a>Zorg ervoor dat de juiste GPU-Stuur Programma's zijn geïnstalleerd
Als u wilt profiteren van de GPU-mogelijkheden van uw Lab-Vm's, moet u ervoor zorgen dat de juiste GPU-Stuur Programma's zijn geïnstalleerd.  In de wizard Lab maken wanneer u een GPU VM-grootte selecteert, is er een optie om **GPU-Stuur Programma's te installeren**.  

![GPU-Stuur Programma's installeren](../media/how-to-setup-gpu/lab-gpu-drivers.png)

Deze optie is standaard **ingeschakeld** en zorgt ervoor dat de *meest recente* Stuur Programma's worden geïnstalleerd voor het type GPU en installatie kopie dat u hebt geselecteerd:
- Wanneer u de grootte van de **Compute** GPU selecteert, worden uw Lab-vm's aangedreven door de [Nvidia Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU.  In dit geval worden de meest recente [CUDA](https://www.nvidia.com/object/io_69526.html) -Stuur Programma's geïnstalleerd om high-performance computing in te scha kelen.
- Wanneer u een grootte van een **visualisatie** -GPU selecteert, worden uw Lab-vm's aangedreven door de [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU en de [raster technologie](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  In dit geval worden de meest recente raster Stuur Programma's geïnstalleerd om grafische intensieve toepassingen mogelijk te maken.

> [!IMPORTANT]
> Als u de beste gebruikers ervaring met **visualisatie** gpu's wilt hebben, moet u ervoor zorgen dat *beide* Stuur Programma's zijn geïnstalleerd *en* de GPU wordt ingeschakeld via RDP-verbindingen.  Zie de stappen in de sectie ' [GPU via RDP-verbinding met Windows-Vm's inschakelen](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms)'.

### <a name="manually-install-drivers"></a>Stuur Programma's hand matig installeren
Mogelijk hebt u scenario's waarin u een andere versie van de Stuur Programma's moet installeren dan de nieuwste versie.  De stappen in deze sectie laten zien hoe u hand matig de juiste Stuur Programma's installeert, afhankelijk van of u een **Compute** -of **visualisatie** -GPU gebruikt.

#### <a name="compute-gpu-drivers"></a>Reken GPU-Stuur Programma's
Volg deze stappen voor het hand matig installeren van Stuur Programma's voor de grootte van de **Compute** GPU:
1. Wanneer u [uw Lab maakt](./how-to-manage-classroom-labs.md), moet u de instelling **GPU-Stuur Programma's installeren** in de wizard Lab maken **uitschakelen** .
1. Nadat uw Lab is gemaakt, maakt u verbinding met de VM van de sjabloon om de juiste Stuur Programma's te installeren.
   ![NVIDIA-Stuur Programma's downloaden](../media/how-to-setup-gpu/nvidia-driver-download.png) 
   1. Open de [pagina drivers downloaden van NVIDIA](https://www.nvidia.com/Download/index.aspx)in uw browser.
   2. Stel het **product type** in op **Tesla**.
   3. Stel de **product reeks** in op de **K-serie**.
   4. Stel het **product** in op **Tesla K80**.
   5. Stel het **besturings systeem** in op basis van het type basis installatie kopie dat u hebt geselecteerd bij het maken van uw Lab.
   6. Stel de **CUDA-werkset** in op de versie van de CUDA-Stuur Programma's die u nodig hebt.
   7. Klik op **zoeken** om uw stuur Programma's te vinden.
   8. Klik op **downloaden** om het installatie programma te downloaden.
   9. Voer het installatie programma uit zodat de Stuur Programma's op de sjabloon-VM worden geïnstalleerd.  
2. Controleer of de Stuur Programma's correct zijn geïnstalleerd door de stappen in de sectie ' [geïnstalleerde Stuur Programma's valideren](how-to-setup-lab-gpu.md#validate-installed-drivers)' te volgen. 
3. Nadat u de Stuur Programma's en andere software hebt geïnstalleerd die voor uw klasse zijn vereist, kunt u op **publiceren** klikken om de vm's van uw studenten te maken.

> [!NOTE]
> Als u een Linux-installatie kopie gebruikt, raadpleegt u ook de volgende stappen om de Stuur Programma's te installeren nadat u het installatie programma hebt gedownload: [Installeer CUDA-Stuur Programma's in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="visualization-gpu-drivers"></a>Visualisatie GPU-Stuur Programma's
Volg deze stappen om stuur Programma's hand matig te installeren voor de grootte van de **visualisatie** GPU:
1. Wanneer u [uw Lab maakt](./how-to-manage-classroom-labs.md), moet u de instelling **GPU-Stuur Programma's installeren** in de wizard Lab maken **uitschakelen** .
1. Nadat uw Lab is gemaakt, maakt u verbinding met de VM van de sjabloon om de juiste Stuur Programma's te installeren.
1. Installeer de raster Stuur Programma's die door micro soft worden meegeleverd met de sjabloon-VM door de volgende stappen uit te voeren:
   -  [Windows NVIDIA-raster Stuur Programma's](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA-raster Stuur Programma's](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Start de sjabloon-VM opnieuw op.
1. Controleer of de Stuur Programma's correct zijn geïnstalleerd door de stappen in de sectie ' [geïnstalleerde Stuur Programma's valideren](how-to-setup-lab-gpu.md#validate-installed-drivers)' te volgen.
1. Configureer RDP-instellingen om GPU in te scha kelen door de stappen in de sectie ' [GPU via RDP-verbinding met Windows-Vm's inschakelen ' te](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms)volgen.
1. Nadat u de Stuur Programma's en andere software hebt geïnstalleerd die voor uw klasse zijn vereist, kunt u op **publiceren** klikken om de vm's van uw studenten te maken.

### <a name="validate-installed-drivers"></a>Geïnstalleerde Stuur Programma's valideren
In de stappen in deze sectie wordt beschreven hoe u controleert of uw GPU-Stuur Programma's goed zijn geïnstalleerd.

#### <a name="windows-images"></a>Windows-installatie kopieën
1.  Volg de stappen in het artikel waarin wordt uitgelegd hoe u de [installatie van een stuur programma kunt controleren in Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Als u een **visualisatie** -GPU gebruikt, kunt u ook het volgende doen:
    - Open het **configuratie scherm van NVIDIA** om uw GPU-instellingen te bekijken en aan te passen.  Als u deze instellingen wilt openen, opent u **configuratie scherm > hardware** en selecteert u het **NVIDIA-configuratie scherm**.
      ![Het NVIDIA-configuratie scherm openen](../media/how-to-setup-gpu/control-panel-nvidia-settings.png) 
     - Bekijk uw GPU-prestaties met behulp van **taak beheer**.  Open **taak beheer** en selecteer het tabblad **prestaties** om dit weer te geven. ![ Prestaties van taak beheer GPU](../media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > De instellingen van het **configuratie scherm van NVIDIA** kunnen alleen worden gebruikt voor **visualisatie** gpu's.  Als u het **NVIDIA-configuratie scherm** probeert te openen voor een **Compute** -GPU, wordt de volgende fout weer **gegeven: nVidia-weer gave-instellingen zijn niet beschikbaar.  Er wordt momenteel geen weer gave gebruikt die is gekoppeld aan een NVIDIA GPU.**  Op dezelfde manier wordt de GPU-prestatie gegevens in **taak beheer** alleen gegeven voor **visualisatie** gpu's.

#### <a name="linux-images"></a>Linux-installatie kopieën
Volg de stappen in het artikel waarin wordt uitgelegd hoe u de [installatie van een stuur programma op Linux kunt controleren](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>GPU via RDP-verbinding met Windows-Vm's inschakelen
Wanneer u RDP (Remote Desktop Protocol) gebruikt om verbinding te maken met een virtuele Windows-machine die is aangedreven door een **visualisatie** GPU, moet u een extra configuratie uitvoeren zodat de GPU wordt gebruikt voor het renderen van afbeeldingen (anders wordt de CPU gebruikt).

De onderstaande stappen moeten worden uitgevoerd op de sjabloon-VM.

1. Configureer eerst RDP-instellingen voor het gebruik van de GPU:

   1. Volg de stappen in dit artikel om te zien hoe u de [rendering van GPU-versnelde apps kunt configureren](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering).

   2. Volg de stappen in dit artikel waarin wordt uitgelegd hoe u [GPU-versnelde frame codering kunt configureren](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding).

2. Controleer vervolgens de configuratie: 

   1. Volg de stappen in dit artikel waarin wordt uitgelegd hoe u de [rendering van apps met GPU kunt controleren](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering).

   2.  Volg de stappen in dit artikel waarin wordt uitgelegd hoe u [GPU-versnelde frame codering kunt controleren](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

3. Ten slotte hebt u de Stuur Programma's geïnstalleerd en beschikt u over de RDP-instellingen die zijn geconfigureerd voor het gebruik van uw GPU.  Nadat u andere software hebt geïnstalleerd die voor uw klasse is vereist, kunt u op **publiceren** klikken om de vm's van uw studenten te maken.  Wanneer uw studenten verbinding maken met hun virtuele machines met behulp van RDP, wordt hun bureau blad weer gegeven met behulp van de GPU van de virtuele machine.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Leslokaallabs maken en beheren](how-to-manage-classroom-labs.md)
- [Type SolidWorks computer-aided design (CAD)-klasse](class-type-solidworks.md)
- [Type MATLAB-klasse](class-type-matlab.md)



