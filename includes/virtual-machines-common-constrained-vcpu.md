---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24fbab2bb637edf6c7e31215cdc7ac82bdb0ebab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176030"
---
Voor sommige databaseworkloads zoals SQL Server of Oracle is een hoog geheugen, opslag en I/O-bandbreedte vereist, maar geen hoge kerntelling. Veel databaseworkloads zijn niet CPU-intensief. Azure biedt bepaalde VM-formaten waar u het VM-vCPU-aantal beperken om de kosten van softwarelicenties te verlagen, terwijl u dezelfde geheugen-, opslag- en I/O-bandbreedte behoudt.

Het aantal vCPU's kan worden beperkt tot een helft of een kwart van de oorspronkelijke VM-grootte. Deze nieuwe VM-formaten hebben een achtervoegsel dat het aantal actieve vCPU's aangeeft om ze gemakkelijker te kunnen identificeren.

De huidige VM-grootte Standard_GS5 wordt bijvoorbeeld geleverd met 32 vCPU's, 448 GB RAM, 64 schijven (tot 256 TB) en 80.000 IoPs of 2 GB/s I/O-bandbreedte. De nieuwe VM-formaten Standard_GS5-16 en Standard_GS5-8 worden geleverd met respectievelijk 16 en 8 actieve vCPU's, terwijl de rest van de specificaties van de Standard_GS5 voor geheugen,opslag en I/O-bandbreedte behouden blijft.

De licentiekosten voor SQL Server of Oracle zijn beperkt tot het nieuwe vCPU-aantal en andere producten moeten in rekening worden gebracht op basis van het nieuwe vCPU-aantal. Dit resulteert in een 50% tot 75% toename van de verhouding tussen de VM specs en actieve (factureerbare) vCPU's. Met deze nieuwe VM-formaten kunnen klantworkloads dezelfde geheugen-, opslag- en I/O-bandbreedte gebruiken en tegelijkertijd de kosten van softwarelicenties optimaliseren. Op dit moment blijven de rekenkosten, waaronder OS-licenties, dezelfde als de oorspronkelijke grootte. Zie [Azure VM-formaten voor meer kosteneffectieve databaseworkloads voor](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)meer informatie.


| Name                | vCPU | Specificaties           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Hetzelfde als M8ms    |
| Standard_M8-4ms     | 4    | Hetzelfde als M8ms    |
| Standard_M16-4ms    | 4    | Hetzelfde als M16ms   |
| Standard_M16-8ms    | 8    | Hetzelfde als M16ms   |
| Standard_M32-8ms    | 8    | Hetzelfde als M32ms   |
| Standard_M32-16ms   | 16   | Hetzelfde als M32ms   |
| Standard_M64-32ms   | 32   | Hetzelfde als M64ms   |
| Standard_M64-16 ms   | 16   | Hetzelfde als M64ms   |
| Standard_M128-64ms  | 64   | Hetzelfde als M128ms  |
| Standard_M128-32ms  | 32   | Hetzelfde als M128ms  |
| Standard_E4-2s_v3   | 2    | Hetzelfde als E4s_v3  |
| Standard_E8 4s_v3   | 4    | Hetzelfde als E8s_v3  |
| Standard_E8-2s_v3   | 2    | Hetzelfde als E8s_v3  |
| Standard_E16-8s_v3  | 8    | Hetzelfde als E16s_v3 |
| Standard_E16-4s_v3  | 4    | Hetzelfde als E16s_v3 |
| Standard_E32-16s_v3 | 16   | Hetzelfde als E32s_v3 |
| Standard_E32-8s_v3  | 8    | Hetzelfde als E32s_v3 |
| Standard_E64-32s_v3 | 32   | Hetzelfde als E64s_v3 |
| Standard_E64-16s_v3 | 16   | Hetzelfde als E64s_v3 |
| Standard_GS4-8      | 8    | Hetzelfde als GS4     |
| Standard_GS4-4      | 4    | Hetzelfde als GS4     |
| Standard_GS5-16     | 16   | Hetzelfde als GS5     |
| Standard_GS5-8      | 8    | Hetzelfde als GS5     |
| Standard_DS11-1_v2  | 1    | Hetzelfde als DS11_v2 |
| Standard_DS12 2_v2  | 2    | Hetzelfde als DS12_v2 |
| Standard_DS12-1_v2  | 1    | Hetzelfde als DS12_v2 |
| Standard_DS13-4_v2  | 4    | Hetzelfde als DS13_v2 |
| Standard_DS13-2_v2  | 2    | Hetzelfde als DS13_v2 |
| Standard_DS14-8_v2  | 8    | Hetzelfde als DS14_v2 |
| Standard_DS14-4_v2  | 4    | Hetzelfde als DS14_v2 |
