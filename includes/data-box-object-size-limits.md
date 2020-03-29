---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244588"
---
Dit zijn de afmetingen van de Azure-objecten die kunnen worden geschreven. Zorg ervoor dat alle bestanden die zijn geüpload aan deze limieten voldoen.

| Azure-objecttype | Standaardlimiet                                             |
|-------------------|-----------------------------------------------------------|
| Blok-blob        | ~ 4,75 TiB                                                 |
| Pagina-blob         | 8 TiB <br> Elk bestand dat in paginablobformaat wordt geüpload moet 512 bytes zijn uitgelijnd (een integraal veelvoud), anders mislukt de upload. <br> VHD en VHDX zijn 512 bytes uitgelijnd. |
| Azure Files        | 1 TiB                                                      |
| Managed Disks     | 4 TiB <br> Zie voor meer informatie over grootte en limieten: <li>[Schaalbaarheidsdoelen van standaard SSD's](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Schaalbaarheidsdoelstellingen van Premium SSD's](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Schaalbaarheidsdoelen van standaard HDD's](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Prijzen en facturering van beheerde schijven](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
