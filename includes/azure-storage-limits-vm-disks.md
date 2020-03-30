---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334672"
---
U een aantal gegevensschijven aan een virtuele Azure-machine koppelen. Op basis van de schaalbaarheids- en prestatiedoelstellingen voor de gegevensschijven van een VM u het aantal en het type schijf bepalen dat u nodig hebt om aan uw prestatie- en capaciteitsvereisten te voldoen.

> [!IMPORTANT]
> Voor optimale prestaties, beperken het aantal sterk gebruikte schijven aan de virtuele machine om mogelijke throttling te voorkomen. Als alle aangesloten schijven niet veel tegelijk worden gebruikt, kan de virtuele machine een groter aantal schijven ondersteunen.

**Voor azure beheerde schijven:**

In de volgende tabel worden de standaard- en maximumlimieten van het aantal resources per regio per abonnement weergegeven. Er is geen limiet voor het aantal beheerde schijven, momentopnamen en afbeeldingen per resourcegroep.  

> | Resource | Limiet |
> | --- | --- |
> | Standaard beheerde schijven | 50,000 |
> | Standaard SSD-beheerde schijven | 50,000 |
> | Premium beheerde schijven | 50,000 |
> | Standard_LRS momentopnamen | 50,000 |
> | Standard_ZRS momentopnamen | 50,000 |
> | Beheerde afbeelding | 50,000 |

* **Voor standaardopslagaccounts:** Een standaardopslagaccount heeft een maximaal totaal aanvraagpercentage van 20.000 IOPS. De totale IOPS voor al uw virtuele machineschijven in een standaardopslagaccount mag deze limiet niet overschrijden.
  
    U ruwweg het aantal sterk gebruikte schijven berekenen dat wordt ondersteund door één standaardopslagaccount op basis van de limiet voor de aanvraagsnelheid. Voor een Basic-laagVM is het maximum aantal veelgebruikte schijven bijvoorbeeld ongeveer 66, wat 20.000/300 IOPS per schijf is. Het maximum aantal sterk gebruikte schijven voor een Standard tier VM is ongeveer 40, dat is 20.000/500 IOPS per schijf. 

* **Voor Premium-opslagaccounts:** Een Premium-opslagaccount heeft een maximale totale doorvoersnelheid van 50 Gbps. De totale doorvoer op alle schijven in uw virtuele machine mag deze limiet niet overschrijden.

