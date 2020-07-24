---
title: Naamgevings conventies voor Azure VM-groottes
description: Hierin worden de naamgevings conventies beschreven die worden gebruikt voor Azure VM-grootten
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131715"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Grootte conventies voor virtuele machines van Azure

Deze pagina geeft een overzicht van de naamgevings conventies die worden gebruikt voor virtuele Azure-machines. Vm's gebruiken deze naamgevings regels om verschillende functies en specificaties te vermelden.

## <a name="naming-convention-explanation"></a>Uitleg over naamgevings Conventie

**[Family]**  +  **[Subfamilie *]**  +  **[aantal vcpu's]**  +  **[Additieve functies]**  +  **[Type Accelerator *]**  +  **[Versie]**

|Waarde | Uitleg|
|---|---|
| Standard, Basic of experimentele | ' Standaard ' is de standaard waarde die is toegewezen voor alle GA VM-grootten | 
| Familie | Hiermee wordt de serie van de VM-serie aangegeven| 
| * Subfamilie | Wordt alleen gebruikt voor gespecialiseerde VM-differentiaties|
| aantal Vcpu's| Hiermee wordt het aantal Vcpu's van de virtuele machine aangeduid |
| Additieve functies | Een of meer kleine letters die additieve functies aanduiden, zoals: <br> a = AMD-gebaseerde processor <br> d = schijf (lokale tijdelijke schijf is aanwezig); Dit geldt voor nieuwere Azure-Vm's, Zie [Ddv4 en Ddsv4-Series](./ddv4-ddsv4-series.md) <br> h = slaap stand mogelijk <br> i = geïsoleerd <br> l = weinig geheugen <br> m = geheugen intensief <br> t = klein geheugen <br> r = RDMA <br> s = Premium Storage mogelijk, met inbegrip van het mogelijke gebruik van [Ultra-SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) (Opmerking: sommige nieuwere grootten zonder het kenmerk s kunnen nog steeds ondersteuning bieden voor Premium Storage, bijvoorbeeld M128, M64, enzovoort).<br> |
| * Type Accelerator | Hiermee wordt het type hardware-Accelerator aangegeven in de gespecialiseerde/GPU-Sku's. Alleen de nieuwe gespecialiseerde/GPU Sku's die worden gestart vanuit Q3 2020, hebben de hardware-accelerator in de naam. |
| Versie | Hiermee wordt de versie van de serie van de VM-familie aangeduid |

## <a name="example-breakdown"></a>Voor beeld van uitsplitsing

**[Family]**  +  **[Subfamilie *]**  +  **[aantal vcpu's]**  +  **[Additieve functies]**  +  **[Type Accelerator *]**  +  **[Versie]**

### <a name="example-1-m416ms_v2"></a>Voor beeld 1: M416ms_v2

|Waarde | Uitleg|
|---|---|
| Familie | M | 
| aantal Vcpu's | 416 |
| Additieve functies | m = geheugen intensief <br> s = Premium Storage mogelijk |
| Versie | v2 |

### <a name="example-2-nv16as_v4"></a>Voor beeld 2: NV16as_v4

|Waarde | Uitleg|
|---|---|
| Familie | N | 
| Subfamilie | V |
| aantal Vcpu's | 16 |
| Additieve functies | a = AMD-gebaseerde processor <br> s = Premium Storage mogelijk |
| Versie | 4 |

### <a name="example-3-nc4as_t4_v3"></a>Voor beeld 3: NC4as_T4_v3

|Waarde | Uitleg|
|---|---|
| Familie | N | 
| Subfamilie | C |
| aantal Vcpu's | 4 |
| Additieve functies | a = AMD-gebaseerde processor <br> s = Premium Storage mogelijk |
| Type Accelerator | T4 |
| Versie | v3 |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beschik bare [VM-grootten](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) in Azure. 
