---
title: Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion
description: In dit artikel leert u hoe u verbinding kunt maken met een virtuele Azure-machine met Windows met behulp van Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8b9653daf945b6a189bc528cd00de832ae97c03b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978124"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion

Met Azure Bastion kunt u veilig en naadloos verbinding maken met uw virtuele machines via SSL rechtstreeks in de Azure Portal. Wanneer u Azure Bastion gebruikt, is voor uw Vm's geen client, agent of extra software vereist. In dit artikel wordt beschreven hoe u verbinding maakt met uw Windows-Vm's. Zie [verbinding maken met een VM met Azure Bastion-Linux](bastion-connect-vm-ssh.md)voor meer informatie over het maken van een verbinding met een virtuele Linux-machine.

Azure Bastion biedt een beveiligde connectiviteit voor alle virtuele machines in een virtueel netwerk waarin deze is ingericht. Azure Bastion beschermt uw virtuele machines tegen blootstelling van RDP-/SSH-poorten aan de buitenwereld, terwijl u toch beveiligde toegang krijgt geboden met behulp van RDP/SSH. Zie het [overzicht](bastion-overview.md) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="install-the-bastion-host"></a>De bastion-host installeren

Zorg ervoor dat u een Azure bastion-host hebt ingesteld voor het virtuele netwerk waarin de virtuele machine zich bevindt. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om verbinding te maken met elke virtuele machine in het virtuele netwerk. Zie [een Azure bastion-host maken](bastion-create-host-portal.md)voor het instellen van een Azure bastion-host.

### <a name="required-roles"></a>Vereiste rollen

De volgende functies zijn vereist om een verbinding te maken:

* De rol van Lezer op de virtuele machine
* De rol van Lezer op de NIC met het privé-IP-adres van de virtuele machine
* De rol van Lezer in de Azure Bastion-resource

### <a name="ports"></a>Poorten

Als u verbinding wilt maken met de Windows-VM, moeten de volgende poorten zijn geopend op uw Windows-VM:

* Binnenkomende poorten: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Verbinding maken

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).
