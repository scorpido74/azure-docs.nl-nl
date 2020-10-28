---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 10599b47d55a0b4ea1eb79cda3c31a2479efb7c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92756008"
---
U kunt een aantal gegevensschijven koppelen aan een virtuele Azure-machine. Op basis van schaalbaarheid- en prestatiedoelen voor de gegevensschijven van een VM kunt u het aantal en type schijven bepalen dat u nodig hebt om aan uw prestatie- en capaciteitsvereisten te voldoen.

> [!IMPORTANT]
> Voor optimale prestaties moet u het aantal intensief gebruikte schijven dat u aan de virtuele machine koppelt, echter zo laag mogelijk houden. Zo voorkomt u dat u tegen beperkingen aanloopt. Als alle gekoppelde schijven niet tegelijkertijd maximaal worden gebruikt, kan de virtuele machine een groot aantal schijven ondersteunen.

**Voor beheerde Azure-schijven:**

In de volgende tabel ziet u de standaard- en maximumlimieten van het aantal resources per regio per abonnement. De limieten blijven dezelfde, ongeacht of schijven zijn versleuteld met door platform beheerde sleutels of door klant beheerde sleutels. Er is geen limiet voor het aantal beheerde schijven, momentopnamen en afbeelding per resourcegroep.  

> | Resource | Limiet |
> | --- | --- |
> | Standaard beheerde schijven | 50,000 |
> | Standaard beheerde SSD-schijven | 50,000 |
> | Premium beheerde schijven | 50,000 |
> | Standard_LRS-momentopnamen | 50,000 |
> | Standard_ZRS-momentopnamen | 50,000 |
> | Beheerde installatiekopie | 50,000 |

**Voor Standard-opslagaccounts:** Een standaardopslagaccount heeft een maximale totale aanvraagsnelheid van 20.000 IOP's. Het totale aantal IOP's van alle schijven van een virtuele machine in een standaardopslagaccount mag niet groter zijn dan deze limiet.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Voor Premium Storage-accounts:** Premium-opslagaccounts bieden een maximale totale doorvoersnelheid van 50 Gbps. De totale doorvoer op alle schijven in uw virtuele machine mag deze limiet niet overschrijden.

