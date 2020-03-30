---
title: Verbinding maken met een Windows-vm met Azure Bastion
description: In dit artikel leest u hoe u verbinding maakt met een Azure Virtual Machine waarop Windows wordt uitgevoerd met Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597338"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Verbinding maken met een virtuele Windows-machine met Azure Bastion

Met Azure Bastion u veilig en naadloos rechtstreeks verbinding maken met uw virtuele machines via SSL in de Azure-portal. Wanneer u Azure Bastion gebruikt, hebben uw VM's geen client, agent of extra software nodig. In dit artikel ziet u hoe u verbinding maken met uw Windows-VM's. Zie Verbinding maken met een [VM met Azure Bastion - Linux](bastion-connect-vm-ssh.md)voor informatie over het maken van verbinding met een Linux-vm.

Azure Bastion biedt veilige connectiviteit met alle VM's in het virtuele netwerk waarin het is ingericht. Het gebruik van Azure Bastion beschermt uw virtuele machines tegen het blootstellen van RDP/SSH-poorten aan de buitenwereld, terwijl u toch veilige toegang biedt met RDP/SSH. Zie het [overzicht](bastion-overview.md) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Azure Bastion-host hebt ingesteld voor het virtuele netwerk waarin de VM zich bevindt. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, u deze gebruiken om verbinding te maken met elke virtuele virtuele netwerk. Zie [Een Azure Bastion-host maken](bastion-create-host-portal.md)als u een Azure Bastion-host wilt instellen.

### <a name="required-roles"></a>Vereiste rollen

Om een verbinding te maken, zijn de volgende rollen vereist:

* De rol van de lezer op de virtuele machine
* De rol van de lezer op NIC met privé IP van de virtuele machine
* Leesrol op de Azure Bastion-bron

### <a name="ports"></a>Poorten

Als u verbinding wilt maken met de Windows-VM, moet u de volgende poorten op uw Windows-vm hebben geopend:

* Binnenkomende poorten: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Verbinding maken

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken, klik vervolgens op **Verbinding maken** en selecteer **Bastion** in de vervolgkeuzelijst.

   ![VM-verbinding](./media/bastion-connect-vm-rdp/connect.png)
1. Nadat u op Bastion hebt geklikt, verschijnt er een zijbalk met drie tabbladen : RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, u op de koppeling klikken om Bastion te configureren. Zie [Bastion configureren voor](bastion-create-host-portal.md)configuratie-instructies.

   ![tabblad Bastion](./media/bastion-connect-vm-rdp/bastion.png)
1. Voer op het tabblad Bastion de gebruikersnaam en het wachtwoord voor uw virtuele machine in en klik vervolgens op **Verbinding maken**. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure-portal (via HTML5) met poort 443 en de Bastion-service.

   ![RDP-verbinding](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Volgende stappen

Lees de [veelgestelde vragen over Bastion](bastion-faq.md)
