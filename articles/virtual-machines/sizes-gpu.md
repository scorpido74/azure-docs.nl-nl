---
title: Groottes van Azure VM-GPU | Microsoft Docs
description: Geeft een lijst van de verschillende geoptimaliseerde GPU-grootten die beschikbaar zijn voor virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
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
ms.openlocfilehash: 9be3c14bb435aaa1cddf008a27389bb3b1fdbc3d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493538"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Grootte van door GPU geoptimaliseerde virtuele machines

Geoptimaliseerde VM-grootten zijn gespecialiseerde virtuele machines die beschikbaar zijn met één of meer NVIDIA-Gpu's. Deze grootten zijn ontworpen voor computerintensieve, grafisch intensieve en visualisatie werk belastingen. Dit artikel bevat informatie over het aantal en het type Gpu's, Vcpu's, gegevens schijven en Nic's. Opslag doorvoer en netwerk bandbreedte worden ook voor elke grootte in deze groepering opgenomen.

- De grootte van de [NC-serie](nc-series.md), [NCv2-](ncv2-series.md)serie, [NCv3-serie](ncv3-series.md) is geoptimaliseerd voor computerintensieve en netwerkintensieve toepassingen en algoritmen. Enkele voor beelden zijn CUDA en OpenCL toepassingen en simulaties, AI en diep gaande lessen. De NCv3-serie is gericht op werk belastingen met hoge prestaties, met de Tesla V100 GPU van NVIDIA. De NC-serie maakt gebruik van de Intel Xeon E5-2690 v3 2,60 GHz v3-processor (Haswell) en de virtuele machines uit de NCv2-en NCv3-serie gebruiken de Intel Xeon E5-2690 v4-processor (Broadwell).

- [ND-serie](nd-series.md)en [NDv2-serie](ndv2-series.md) zijn gericht op trainings-en inleidende scenario's voor diep gaande lessen. Ze gebruiken de NVIDIA Tesla P40 GPU en de Intel Xeon E5-2690 v4-processor (Broadwell). De NDv2-serie maakt gebruik van de Intel Xeon Platinum 8168-processor (Skylake).

- De grootte van de [nv-](nv-series.md) en [NVv3-serie](nvv3-series.md) is geoptimaliseerd en ontworpen voor externe visualisatie, streaming, games, code ring en VDI-scenario's met behulp van frameworks zoals OpenGL en DirectX. Deze Vm's worden ondersteund door de NVIDIA Tesla M60 GPU.

- [NVv4-serie](nvv4-series.md) VM-grootten geoptimaliseerd en ontworpen voor VDI en externe visualisatie. Met gepartitioneerde Gpu's biedt NVv4 de juiste grootte voor werk belastingen waarvoor kleinere GPU-resources zijn vereist. Deze Vm's worden ondersteund door de AMD Radeon instinct MI25 GPU.

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Om te profiteren van de GPU-mogelijkheden van Vm's uit de Azure N-serie, moeten de NVIDIA GPU-Stuur Programma's zijn geïnstalleerd.

Met de [uitbrei ding NVIDIA GPU-stuur programma](/extensions/hpccompute-gpu-windows.md) worden de juiste NVIDIA-CUDA of raster Stuur Programma's geïnstalleerd op een virtuele machine uit de N-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie de [documentatie over NVIDIA GPU-Stuur Programma's](/extensions/hpccompute-gpu-windows.md) voor ondersteunde besturings systemen en implementaties tappen. Zie [extensies en functies van virtuele Azure-machines](/extensions/overview.md)voor algemene informatie over VM-extensies.

Als u ervoor kiest om de NVIDIA GPU-Stuur Programma's hand matig te installeren, raadpleegt u [het stuur programma voor](/windows/n-series-driver-setup.md) de installatie van de Windows-of [n-Series GPU-](/linux/n-series-driver-setup) stuur programma voor Linux voor ondersteunde besturings systemen, stuur Programma's, installatie en verificaties tappen.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)voor de beschik baarheid van virtuele machines uit de N-serie.

- Vm's uit de N-serie kunnen alleen worden geïmplementeerd in het Resource Manager-implementatie model.

- Vm's uit de N-serie verschillen in het type Azure Storage ze voor hun schijven ondersteunen. NC-en NV-Vm's bieden alleen ondersteuning voor VM-schijven die worden ondersteund door de standaard Disk Storage (HDD). NCv2-, NCv3-, ND-, NDv2-en NVv2-Vm's bieden alleen ondersteuning voor VM-schijven die worden ondersteund door Premium-Disk Storage (SSD).

- Als u meer dan een paar virtuele machines uit de N-serie wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- Mogelijk moet u het quotum voor kernen (per regio) in uw Azure-abonnement verhogen en de afzonderlijke quota voor NC-, NCv2-, NCv3-, ND-, NDv2-, NV-of NVv2-kernen verhogen. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](/../azure-supportability/how-to-create-azure-support-request.md) . De standaard limieten kunnen variëren, afhankelijk van de categorie van uw abonnement.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.