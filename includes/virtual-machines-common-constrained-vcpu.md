---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: mimckitt
ms.custom: include file
ms.openlocfilehash: 8ee5238fd8043161271eef2c130a8e0a385a0bc0
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268768"
---
Voor sommige data base-werk belastingen, zoals SQL Server of Oracle, is veel geheugen, opslag ruimte en I/O-band breedte vereist, maar geen hoge kern telling. Veel database werkbelastingen zijn niet CPU-intensief. Azure biedt bepaalde VM-grootten waar u het aantal virtuele machines in de VM kunt beperken om de kosten van software licenties te verlagen en tegelijkertijd hetzelfde geheugen, dezelfde opslag en I/O-band breedte te behouden.

Het aantal vCPU kan worden beperkt tot een halve of een kwart van de oorspronkelijke VM-grootte. Deze nieuwe VM-grootten hebben een achtervoegsel dat het aantal actieve Vcpu's opgeeft, zodat u ze gemakkelijker kunt identificeren.

De huidige VM-grootte Standard_GS5 bijvoorbeeld geleverd met 32 Vcpu's, 448 GB RAM, 64 schijven (Maxi maal 256 TB) en 80.000 IOPs of 2 GB/s aan I/O-band breedte. De nieuwe VM-grootten Standard_GS5-16 en Standard_GS5-8 zijn respectievelijk 16 en 8 actieve Vcpu's, terwijl de rest van de specificaties van de Standard_GS5 voor geheugen, opslag en I/O-band breedte behouden blijven.

De licentie kosten voor SQL Server of Oracle zijn beperkt tot het nieuwe vCPU-aantal en andere producten moeten worden gefactureerd op basis van het nieuwe vCPU aantal. Dit resulteert in een verhoging van 50% tot 75% van de verhouding van de VM-specificaties naar actief (Factureerbaar) Vcpu's. Met deze nieuwe VM-grootten kunnen klant werkbelastingen gebruikmaken van dezelfde geheugen, opslag en I/O-band breedte, terwijl de kosten voor software licenties worden geoptimaliseerd. Op dit moment blijven de reken kosten, met inbegrip van OS-licenties, hetzelfde als de oorspronkelijke grootte. Zie [Azure VM-grootten voor meer informatie over rendabele data base-workloads](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Name                | vCPU | Specificaties           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Hetzelfde als M8 MS    |
| Standard_M8-4 MS     | 4    | Hetzelfde als M8 MS    |
| Standard_M16-4 MS    | 4    | Hetzelfde als M16 MS   |
| Standard_M16-8 MS    | 8    | Hetzelfde als M16 MS   |
| Standard_M32-8 MS    | 8    | Hetzelfde als M32 MS   |
| Standard_M32-16 MS   | 16   | Hetzelfde als M32 MS   |
| Standard_M64-32MS   | 32   | Hetzelfde als M64ms   |
| Standard_M64-16 MS   | 16   | Hetzelfde als M64ms   |
| Standard_M128-64ms  | 64   | Hetzelfde als M128ms  |
| Standard_M128-32MS  | 32   | Hetzelfde als M128ms  |
| Standard_E4-2s_v3   | 2    | Hetzelfde als E4s_v3  |
| Standard_E8-4s_v3   | 4    | Hetzelfde als E8s_v3  |
| Standard_E8-2s_v3   | 2    | Hetzelfde als E8s_v3  |
| Standard_E16-8s_v3  | 8    | Hetzelfde als E16s_v3 |
| Standard_E16-4s_v3  | 4    | Hetzelfde als E16s_v3 |
| Standard_E32-16s_v3 | 16   | Hetzelfde als E32s_v3 |
| Standard_E32-8s_v3  | 8    | Hetzelfde als E32s_v3 |
| Standard_E64-32s_v3 | 32   | Hetzelfde als E64s_v3 |
| Standard_E64-16s_v3 | 16   | Hetzelfde als E64s_v3 |
| Standard_E4-2ds_v4  | 2    | Hetzelfde als E4ds_v4 |
| Standard_E8-4ds_v4  | 4    | Hetzelfde als E8ds_v4 |
| Standard_E8-2ds_v4  | 2    | Hetzelfde als E8ds_v4 |
| Standard_E16-8ds_v4 | 8    | Hetzelfde als E16ds_v4|
| Standard_E16-4ds_v4 | 4    | Hetzelfde als E16ds_v4|
| Standard_E32-16ds_v4| 16   | Hetzelfde als E32ds_v4|
| Standard_E32-8ds_v4 | 8    | Hetzelfde als E32ds_v4|
| Standard_E64-32ds_v4| 32   | Hetzelfde als E64ds_v4|
| Standard_E64-16ds_v4| 16   | Hetzelfde als E64ds_v4|
| Standard_GS4-8      | 8    | Hetzelfde als GS4     |
| Standard_GS4-4      | 4    | Hetzelfde als GS4     |
| Standard_GS5-16     | 16   | Hetzelfde als GS5     |
| Standard_GS5-8      | 8    | Hetzelfde als GS5     |
| Standard_DS11-1_v2  | 1    | Hetzelfde als DS11_v2 |
| Standard_DS12-2_v2  | 2    | Hetzelfde als DS12_v2 |
| Standard_DS12-1_v2  | 1    | Hetzelfde als DS12_v2 |
| Standard_DS13-4_v2  | 4    | Hetzelfde als DS13_v2 |
| Standard_DS13-2_v2  | 2    | Hetzelfde als DS13_v2 |
| Standard_DS14-8_v2  | 8    | Hetzelfde als DS14_v2 |
| Standard_DS14-4_v2  | 4    | Hetzelfde als DS14_v2 |
