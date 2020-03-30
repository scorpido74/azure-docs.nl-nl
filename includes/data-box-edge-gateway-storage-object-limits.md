---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176488"
---
Dit zijn de afmetingen van de Azure-objecten die kunnen worden geschreven. Zorg ervoor dat alle bestanden die zijn geüpload aan deze limieten voldoen.

| Azure-objecttype | Uploadlimiet                                             |
|-------------------|-----------------------------------------------------------|
| Blob blokkeren        | ~ 4,75 TB                                                 |
| Paginablob         | 1 TB <br> Elk bestand dat in de Pagina Blob-indeling wordt geüpload, moet 512 bytes zijn uitgelijnd (een integraal veelvoud), anders mislukt de upload. <br> De VHD en VHDX zijn 512 bytes uitgelijnd. |
| Azure Files         | 1 TB <br> Elk bestand dat in de Pagina Blob-indeling wordt geüpload, moet 512 bytes zijn uitgelijnd (een integraal veelvoud), anders mislukt de upload. <br> De VHD en VHDX zijn 512 bytes uitgelijnd. |

> [!IMPORTANT]
> Het maken van bestanden (ongeacht het opslagtype) is toegestaan tot 5 TB. Als u echter een bestand maakt waarvan de grootte groter is dan de uploadlimiet die in de vorige tabel is gedefinieerd, wordt het bestand niet geüpload. U moet het bestand handmatig verwijderen om de ruimte terug te winnen.