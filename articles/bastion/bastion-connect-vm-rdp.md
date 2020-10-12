---
title: Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion
description: In dit artikel leert u hoe u verbinding kunt maken met een virtuele Azure-machine met Windows met behulp van Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744320"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion

Met Azure Bastion kunt u veilig en naadloos verbinding maken met uw virtuele machines via SSL rechtstreeks in de Azure Portal. Wanneer u Azure Bastion gebruikt, is voor uw Vm's geen client, agent of extra software vereist. In dit artikel wordt beschreven hoe u verbinding maakt met uw Windows-Vm's. Zie [verbinding maken met een VM met Azure Bastion-Linux](bastion-connect-vm-ssh.md)voor meer informatie over het maken van een verbinding met een virtuele Linux-machine.

Azure Bastion biedt een beveiligde connectiviteit voor alle virtuele machines in een virtueel netwerk waarin deze is ingericht. Azure Bastion beschermt uw virtuele machines tegen blootstelling van RDP-/SSH-poorten aan de buitenwereld, terwijl u toch beveiligde toegang krijgt geboden met behulp van RDP/SSH. Zie het [overzicht](bastion-overview.md) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Azure bastion-host hebt ingesteld voor het virtuele netwerk waarin de virtuele machine zich bevindt. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om verbinding te maken met elke virtuele machine in het virtuele netwerk. Zie [een Azure bastion-host maken](bastion-create-host-portal.md)voor het instellen van een Azure bastion-host.

### <a name="required-roles"></a>Vereiste rollen

De volgende functies zijn vereist om een verbinding te maken:

* Rol van lezer op de virtuele machine
* De rol van lezer op de NIC met het privé-IP-adres van de virtuele machine
* Rol van lezer in de Azure Bastion-resource

### <a name="ports"></a>Poorten

Als u verbinding wilt maken met de Windows-VM, moeten de volgende poorten zijn geopend op uw Windows-VM:

* Binnenkomende poorten: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Verbinding maken

1. Open [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken** en selecteer **Bastion** in de vervolg keuzelijst.

   ![VM-verbinding](./media/bastion-connect-vm-rdp/connect.png)
1. Nadat u op Bastion hebt geklikt, wordt er een zijbalk weer gegeven met drie tabbladen: RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, kunt u klikken op de koppeling om Bastion te configureren. Zie [Bastion configureren](bastion-create-host-portal.md)voor configuratie-instructies.

   ![tabblad Bastion](./media/bastion-connect-vm-rdp/bastion.png)
1. Voer op het tabblad Bastion de gebruikers naam en het wacht woord in voor uw virtuele machine en klik vervolgens op **verbinding maken**. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

   ![Verbinding maken met RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md)
