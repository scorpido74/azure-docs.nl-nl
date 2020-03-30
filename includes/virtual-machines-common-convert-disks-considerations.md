---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66416052"
---
* Voor de conversie moet de VM opnieuw worden opgestart. Plan daarom de migratie van uw VM's tijdens een reeds bestaand onderhoudsvenster. 

* De conversie kan niet ongedaan worden gemaakt. 

* Houd er rekening mee dat gebruikers met de rol [Virtuele machinebijdrage](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) niet in staat zullen zijn om de VM-grootte te wijzigen (omdat ze vooraf kunnen worden geürgeürd). Dit komt omdat VM's met beheerde schijven vereisen dat de gebruiker de microsoft.compute/disks/write-machtiging op de OS-schijven moet hebben.

* Test de conversie. Migreer de test-VM voordat u de migratie in de productieomgeving uitvoert.

* Tijdens de conversie moet u de toewijzing van de VM ongedaan maken. De VM ontvangt een nieuw IP-adres wanneer deze na de conversie wordt opgestart. Indien vereist kunt u [een statisch IP-adres toewijzen](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) aan de VM.

* Controleer de minimale versie van de Azure VM-agent die nodig is om het conversieproces te ondersteunen. Zie [Minimale versieondersteuning voor VM-agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voor informatie over het controleren en bijwerken van uw agentversie.