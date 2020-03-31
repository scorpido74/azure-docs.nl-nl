---
title: Computerintensieve Azure VM's gebruiken met Batch
description: Profiteren van hpc- en GPU-virtuele machineformaten in Azure Batch-pools. Meer informatie over afhankelijkheden van het besturingssysteem en bekijk verschillende scenariovoorbeelden.
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/17/2018
ms.author: labrenne
ms.openlocfilehash: d0238e0b5b882748218835d7f06a147d435a9f90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245052"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>RDMA- of GPU-exemplaren gebruiken in batchgroepen

Als u bepaalde batchtaken wilt uitvoeren, u profiteren van Azure VM-formaten die zijn ontworpen voor grootschalige berekeningen. Bijvoorbeeld:

* Als u [MPI-workloads](batch-mpi.md)met meerdere instanties wilt uitvoeren, kiest u H-reeksen of andere formaten met een netwerkinterface voor Remote Direct Memory Access (RDMA). Deze formaten maken verbinding met een InfiniBand-netwerk voor internodecommunicatie, wat MPI-toepassingen kan versnellen. 

* Kies voor CUDA-toepassingen N-serie-formaten met GPU-kaarten (NVIDIA Tesla graphics processing unit).

In dit artikel vindt u richtlijnen en voorbeelden voor het gebruik van enkele gespecialiseerde grootten van Azure in batchgroepen. Zie voor specificaties en achtergronden:

* High performance compute VM-formaten ([Linux,](../virtual-machines/linux/sizes-hpc.md) [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU-enabled VM-formaten[(Linux,](../virtual-machines/linux/sizes-gpu.md) [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> Bepaalde VM-formaten zijn mogelijk niet beschikbaar in de regio's waar u uw Batch-accounts maakt. Zie [Producten die beschikbaar](https://azure.microsoft.com/regions/services/) zijn per regio en Kies een [VM-grootte voor een batchgroep](batch-pool-vm-sizes.md)om te controleren of er een grootte beschikbaar is.

## <a name="dependencies"></a>Afhankelijkheden

De RDMA- of GPU-mogelijkheden van rekenintensieve formaten in Batch worden alleen in bepaalde besturingssystemen ondersteund. (De lijst met ondersteunde besturingssystemen is een subset van de ondersteunde besturingssystemen voor virtuele machines die in deze formaten zijn gemaakt.) Afhankelijk van hoe u uw batchgroep maakt, moet u mogelijk extra stuurprogramma's of andere software op de knooppunten installeren of configureren. In de volgende tabellen worden deze afhankelijkheden samengevat. Zie gekoppelde artikelen voor meer informatie. Zie later in dit artikel voor opties om batchgroepen te configureren.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux-pools - Virtuele machineconfiguratie

| Grootte | Mogelijkheid | Besturingssystemen | Vereiste software | Poolinstellingen |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS, of<br/>CentOS-gebaseerde HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA drivers | Communicatie tussen knooppunten inschakelen, gelijktijdige taakuitvoering uitschakelen |
| [NC, NCv2, NCv3, NDv2 serie](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU (verschilt per serie) | Ubuntu 16.04 LTS, of<br/>CentOS 7.3 of 7.4<br/>(Azure Marketplace) | NVIDIA CUDA- of CUDA Toolkit-stuurprogramma's | N.v.t. | 
| [NV, NVv2 serie](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS, of<br/>CentOS 7.3<br/>(Azure Marketplace) | NVIDIA GRID drivers | N.v.t. |

<sup>*</sup>RDMA-geschikte N-serie maten bevatten ook NVIDIA Tesla GPU's

### <a name="windows-pools---virtual-machine-configuration"></a>Windows-groepen - Configuratie van virtuele machines

| Grootte | Mogelijkheid | Besturingssystemen | Vereiste software | Poolinstellingen |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 of<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 of hoger, of<br/> Intel MPI 5<br/><br/>Windows RDMA-stuurprogramma's | Communicatie tussen knooppunten inschakelen, gelijktijdige taakuitvoering uitschakelen |
| [NC, NCv2, NCv3, ND, NDv2 serie](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU (verschilt per serie) | Windows Server 2016 of <br/>2012 R2 (Azure Marketplace) | NVIDIA CUDA- of CUDA Toolkit-stuurprogramma's| N.v.t. | 
| [NV, NVv2 serie](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 of<br/>2012 R2 (Azure Marketplace) | NVIDIA GRID drivers | N.v.t. |

<sup>*</sup>RDMA-geschikte N-serie maten bevatten ook NVIDIA Tesla GPU's

### <a name="windows-pools---cloud-services-configuration"></a>Windows-groepen - Configuratie van Cloudservices

> [!NOTE]
> N-serieformaten worden niet ondersteund in batchgroepen met de Cloud Service-configuratie.
>

| Grootte | Mogelijkheid | Besturingssystemen | Vereiste software | Poolinstellingen |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | RDMA | Windows Server 2016, 2012 R2, 2012 of<br/>2008 R2 (Guest OS familie) | Microsoft MPI 2012 R2 of hoger, of<br/>Intel MPI 5<br/><br/>Windows RDMA-stuurprogramma's | Communicatie tussen knooppunt's inschakelen,<br/> gelijktijdige taakuitvoering uitschakelen |

## <a name="pool-configuration-options"></a>Configuratieopties voor het bundelen

Als u een gespecialiseerde VM-grootte voor uw batchgroep wilt configureren, hebt u verschillende opties om vereiste software of stuurprogramma's te installeren:

* Kies voor groepen in de configuratie van de virtuele machine een vooraf geconfigureerde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) VM-afbeelding waarop stuurprogramma's en software vooraf zijn geïnstalleerd. Voorbeelden: 

  * [Op CentOS gebaseerde 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) - bevat RDMA-stuurprogramma's en Intel MPI 5.1

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) voor Linux of Windows - bevat NVIDIA CUDA drivers

  * Linux-afbeeldingen voor batchcontainerworkloads die ook GPU- en RDMA-stuurprogramma's bevatten:

    * [CentOS (met GPU- en RDMA-stuurprogramma's) voor Azure Batch-containerpools](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Ubuntu Server (met GPU- en RDMA-stuurprogramma's) voor Azure Batch-containerpools](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Maak een [aangepaste Windows- of Linux VM-afbeelding](batch-sig-images.md) waarop u stuurprogramma's, software of andere instellingen hebt geïnstalleerd die nodig zijn voor de VM-grootte. 

* Maak een [batchtoepassingspakket](batch-application-packages.md) vanuit een stuurprogramma met rits of toepassingsinstallatie en configureer Batch om het pakket te implementeren om knooppunten te poolen en eenmaal te installeren wanneer elk knooppunt wordt gemaakt. Als het toepassingspakket bijvoorbeeld een installatieprogramma is, maakt u een opdrachtregel [voor starttaken](batch-api-basics.md#start-task) om de app in stilte op alle poolknooppunten te installeren. Overweeg een toepassingspakket en een poolstarttaak te gebruiken als uw werkbelasting afhankelijk is van een bepaalde stuurprogrammaversie.

  > [!NOTE] 
  > De starttaak moet worden uitgevoerd met verhoogde (beheerders) machtigingen en moet wachten op succes. Langlopende taken verhogen de tijd om een batchgroep in te richten.
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) configureert automatisch de GPU- en RDMA-stuurprogramma's om transparant te werken met containerized workloads op Azure Batch. Batch Shipyard wordt volledig aangedreven met configuratiebestanden. Er zijn veel voorbeeldreceptconfiguraties beschikbaar die GPU- en RDMA-workloads inschakelen, zoals het [CNTK GPU-recept](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) dat GPU-stuurprogramma's op VM's uit de N-serie vooraf configureert en Microsoft Cognitive Toolkit-software als Een Docker-afbeelding laadt.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Voorbeeld: NVIDIA GPU-stuurprogramma's op Windows NC VM-pool

Als u CUDA-toepassingen wilt uitvoeren op een groep Windows NC-knooppunten, moet u NVDIA GPU-stuurprogramma's installeren. De volgende voorbeeldstappen gebruiken een toepassingspakket om de NVIDIA GPU-stuurprogramma's te installeren. U deze optie kiezen als uw werkbelasting afhankelijk is van een specifieke GPU-stuurprogrammaversie.

1. Download een installatiepakket voor de GPU-stuurprogramma's op Windows Server 2016 van de [NVIDIA-website](https://www.nvidia.com/Download/index.aspx) - bijvoorbeeld [versie 411.82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Sla het bestand lokaal op met een korte naam zoals *GPUDriverSetup.exe*.
2. Maak een zip-bestand van het pakket.
3. Upload het pakket naar uw Batch-account. Zie voor stappen de richtlijnen [voor toepassingspakketten.](batch-application-packages.md) Geef een toepassings-id op, zoals *GPUDriver,* en een versie zoals *411.82*.
1. Maak met behulp van de Batch API's of Azure-portal een pool in de configuratie van de virtuele machine met het gewenste aantal knooppunten en schaal. In de volgende tabel worden voorbeeldinstellingen weergegeven om de NVIDIA GPU-stuurprogramma's in stilte te installeren met behulp van een starttaak:

| Instelling | Waarde |
| ---- | ----- | 
| **Type installatiekopie** | Marketplace (Linux/Windows) |
| **Publisher** | MicrosoftWindowsServer |
| **Bieden** | WindowsServer |
| **Sku** | Datacenter 2016 |
| **Grootte van knooppunt** | NC6-standaard |
| **Referenties voor toepassingspakket** | GPUDriver, versie 411.82 |
| **Taak starten ingeschakeld** | True<br>**Opdrachtregel** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Gebruikersidentiteit** - Groep autouser, beheerder<br/>**Wachten op succes** - True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Voorbeeld: NVIDIA GPU drivers op een Linux NC VM pool

Als u CUDA-toepassingen wilt uitvoeren op een pool van Linux NC-knooppunten, moet u de benodigde NVIDIA Tesla GPU-stuurprogramma's installeren vanuit de CUDA Toolkit. In de volgende voorbeeldstappen wordt een aangepaste Ubuntu 16.04 LTS-afbeelding gemaakt en geïmplementeerd met de GPU-stuurprogramma's:

1. Implementeer een VM uit azure NC-serie met Ubuntu 16.04 LTS. Maak bijvoorbeeld de VM in de regio Us South Central. 
2. Voeg de [NVIDIA GPU Drivers-extensie](../virtual-machines/extensions/hpccompute-gpu-linux.md
) toe aan de VM met behulp van de Azure-portal, een clientcomputer die verbinding maakt met het Azure-abonnement of Azure Cloud Shell. U ook de stappen volgen om verbinding te maken met de VM en [CUDA-stuurprogramma's](../virtual-machines/linux/n-series-driver-setup.md) handmatig installeren.
3. Volg de stappen om een [afbeelding van een gedeelde afbeelding voor de afbeelding van de afbeelding](batch-sig-images.md) voor Batch te maken.
4. Maak een Batch-account in een regio die NC VM's ondersteunt.
5. Maak met behulp van de Batch API's of Azure-portal een pool [met de aangepaste afbeelding](batch-sig-images.md) en met het gewenste aantal knooppunten en schaal. In de volgende tabel worden voorbeeldgroepinstellingen voor de afbeelding weergegeven:

| Instelling | Waarde |
| ---- | ---- |
| **Type installatiekopie** | Aangepaste installatiekopie |
| **Aangepaste afbeelding** | *Naam van de afbeelding* |
| **Knooppuntagent SKU** | batch.node.ubuntu 16.04 |
| **Grootte van knooppunt** | NC6-standaard |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Voorbeeld: Microsoft MPI op een Windows H16r VM-groep

Als u Windows MPI-toepassingen wilt uitvoeren op een groep Azure H16r VM-knooppunten, moet u de HpcVmDrivers-extensie configureren en [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi)installeren. Hier volgen voorbeeldstappen om een aangepaste Windows Server 2016-afbeelding te implementeren met de benodigde stuurprogramma's en software:

1. Implementeer een Azure H16r VM met Windows Server 2016. Maak bijvoorbeeld de VM in de regio VS West. 
2. Voeg de HpcVmDrivers-extensie toe aan de VM door [een Azure PowerShell-opdracht uit](../virtual-machines/sizes-hpc.md) te voeren vanaf een clientcomputer die verbinding maakt met uw Azure-abonnement of azure cloud shell gebruikt. 
1. Maak een verbinding met Extern bureaublad met de VM.
1. Download het [installatiepakket](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) voor de nieuwste versie van Microsoft MPI en installeer Microsoft MPI.
1. Volg de stappen om een [afbeelding van een gedeelde afbeelding voor de afbeelding van de afbeelding](batch-sig-images.md) voor Batch te maken.
1. Maak met behulp van de Batch-API's of Azure-portal een pool [met de Gedeelde afbeeldingsgalerie](batch-sig-images.md) en met het gewenste aantal knooppunten en schaal. In de volgende tabel worden voorbeeldgroepinstellingen voor de afbeelding weergegeven:

| Instelling | Waarde |
| ---- | ---- |
| **Type installatiekopie** | Aangepaste installatiekopie |
| **Aangepaste afbeelding** | *Naam van de afbeelding* |
| **Knooppuntagent SKU** | batch.node.windows amd64 |
| **Grootte van knooppunt** | H16r-standaard |
| **Internode communicatie ingeschakeld** | True |
| **Maximumtaken per knooppunt** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Voorbeeld: Intel MPI op een Linux H16r VM-pool

Als u MPI-toepassingen wilt uitvoeren op een pool van Linux H-serie knooppunten, is een optie het gebruik van de [Op CentOS gebaseerde 7.4 HPC-afbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) van de Azure Marketplace. Linux RDMA drivers en Intel MPI zijn vooraf geïnstalleerd. Deze afbeelding ondersteunt ook Docker-containerworkloads.

Maak met behulp van de Batch API's of Azure-portal een pool met deze afbeelding en met het gewenste aantal knooppunten en schaal. In de volgende tabel worden de instellingen van de voorbeeldgroep weergegeven:

| Instelling | Waarde |
| ---- | ---- |
| **Type installatiekopie** | Marketplace (Linux/Windows) |
| **Publisher** | OpenLogic |
| **Bieden** | CentOS-HPC |
| **Sku** | 7.4 |
| **Grootte van knooppunt** | H16r-standaard |
| **Internode communicatie ingeschakeld** | True |
| **Maximumtaken per knooppunt** | 1 |

## <a name="next-steps"></a>Volgende stappen

* Zie de voorbeelden van [Windows-](batch-mpi.md) of [Linux-taken](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) voor het uitvoeren van MPI-taken in een Azure Batch-groep.

* Zie de [batchshipyard-recepten](https://github.com/Azure/batch-shipyard/) voor voorbeelden van GPU-workloads op Batch.
