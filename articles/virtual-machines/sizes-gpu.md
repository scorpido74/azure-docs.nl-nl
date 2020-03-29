---
title: Azure VM-formaten - GPU | Microsoft Documenten
description: Hier worden de verschillende GPU-geoptimaliseerde formaten weergegeven die beschikbaar zijn voor virtuele machines in Azure. Bevat informatie over het aantal vCPU's, gegevensschijven en NIC's, evenals opslagdoorvoer en netwerkbandbreedte voor formaten in deze serie.
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913579"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU geoptimaliseerde virtuele machineformaten

GPU geoptimaliseerde VM-formaten zijn gespecialiseerde virtuele machines beschikbaar met enkele of meerdere NVIDIA GPU's. Deze formaten zijn ontworpen voor computerintensieve, grafisch intensieve en visualisatieworkloads. In dit artikel vindt u informatie over het aantal en het type GPU's, vCPU's, gegevensschijven en NIC's. Opslagdoorvoer en netwerkbandbreedte zijn ook opgenomen voor elke grootte in deze groepering.

- [NC-series](nc-series.md), [NCv2-serie](ncv2-series.md), [NCv3-serie](ncv3-series.md) maten zijn geoptimaliseerd voor rekenintensieve en netwerk-intensieve toepassingen en algoritmen. Enkele voorbeelden zijn CUDA- en OpenCL-gebaseerde toepassingen en simulaties, AI en Deep Learning. De NCv3-serie is gericht op high-performance computing workloads met NVIDIA's Tesla V100 GPU. De NC-serie maakt gebruik van de Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) processor, en de NCv2-serie en NCv3-serie VM's maken gebruik van de Intel Xeon E5-2690 v4 (Broadwell) processor.

- [ND-serie](nd-series.md), en [NDv2-serie](ndv2-series.md) maten zijn gericht op training en gevolgtrekking scenario's voor deep learning. Ze maken gebruik van de NVIDIA Tesla P40 GPU en de Intel Xeon E5-2690 v4 (Broadwell) processor. De NDv2-serie maakt gebruik van de Intel Xeon Platinum 8168 (Skylake) processor.

- [NV-serie](nv-series.md) en [NVv3-serie](nvv3-series.md) maten zijn geoptimaliseerd en ontworpen voor externe visualisatie, streaming, gaming, codering en VDI scenario's met behulp van frameworks zoals OpenGL en DirectX. Deze VM's worden ondersteund door de NVIDIA Tesla M60 GPU.

- [NVv4-serie](nvv4-series.md) VM-formaten geoptimaliseerd en ontworpen voor VDI en externe visualisatie. Met partitionerde GPU's biedt NVv4 de juiste grootte voor workloads waarvoor kleinere GPU-resources nodig zijn. Deze VM's worden ondersteund door de AMD Radeon Instinct MI25 GPU. NVv4 VM's ondersteunen momenteel alleen Windows gastbesturingssysteem.

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Om te profiteren van de GPU-mogelijkheden van Azure N-serie VM's, moeten NVIDIA GPU-stuurprogramma's worden geïnstalleerd.

De [NVIDIA GPU Driver Extension](/azure/virtual-machines/extensions/hpccompute-gpu-windows) installeert de juiste NVIDIA CUDA- of GRID-stuurprogramma's op een VM uit de N-serie. Installeer of beheer de extensie met behulp van de Azure-portal of hulpprogramma's zoals Azure PowerShell- of Azure Resource Manager-sjablonen. Bekijk de [NVIDIA GPU Driver Extension-documentatie](/azure/virtual-machines/extensions/hpccompute-gpu-windows) voor ondersteunde besturingssystemen en implementatiestappen. Zie Azure virtual machine [extensions and features](/azure/virtual-machines/extensions/overview)voor algemene informatie over VM-extensies.

Als u ervoor kiest nvidia GPU-stuurprogramma's handmatig te installeren, raadpleegt u de opstelling van [GPU-stuurprogramma's uit de N-serie voor Windows](/azure/virtual-machines/windows/n-series-driver-setup) of [N-serie GPU-stuurprogramma's voor Linux](/azure/virtual-machines/linux/n-series-driver-setup) voor ondersteunde besturingssystemen, stuurprogramma's, installatie en verificatiestappen.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- Zie [Producten die beschikbaar zijn per regio voor](https://azure.microsoft.com/regions/services/)de beschikbaarheid van VM's uit de N-serie.

- Vm's uit de N-serie kunnen alleen worden geïmplementeerd in het implementatiemodel resourcebeheer.

- Vm's uit de N-serie verschillen in het type Azure Storage dat ze voor hun schijven ondersteunen. NC- en NV VM's ondersteunen alleen VM-schijven die worden ondersteund door Standard Disk Storage (HDD). NCv2-, NCv3-, ND-, NDv2- en NVv2 VM's ondersteunen alleen VM-schijven die worden ondersteund door Premium Disk Storage (SSD).

- Als u meer dan een paar VM's uit de N-serie wilt implementeren, u een abonnement op basis van betalen per gebruik of andere aankoopopties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- Mogelijk moet u het coresquotum (per regio) in uw Azure-abonnement verhogen en het afzonderlijke quotum voor NC-, NCv2-, NCv3-, ND-, NDv2-, NV- of NVv2-cores verhogen. Als u een quotumverhoging wilt aanvragen, opent u kosteloos [een online verzoek om klantenondersteuning.](../azure-portal/supportability/how-to-create-azure-support-request.md) Standaardlimieten kunnen variëren afhankelijk van uw abonnementscategorie.

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
