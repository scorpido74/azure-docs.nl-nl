---
title: Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion | Microsoft Docs
description: In dit artikel leert u hoe u verbinding kunt maken met een virtuele Azure-machine met Windows met behulp van Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: dc741007c7de8d8e24f9c0f9e4e0c03306d036a4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498351"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion

In dit artikel wordt beschreven hoe u veilig en naadloos RDP kunt gebruiken voor uw Windows-Vm's in een virtueel Azure-netwerk met behulp van Azure Bastion. U kunt rechtstreeks verbinding maken met een VM vanuit Azure Portal. Bij gebruik van Azure Bastion hebben VM's geen client, agent of aanvullende software nodig. Zie het [overzicht](bastion-overview.md)voor meer informatie over Azure Bastion.

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Azure bastion-host hebt ingesteld voor het virtuele netwerk waarin de VM zich bevindt. Zie [een Azure bastion-host maken](bastion-create-host-portal.md)voor meer informatie. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om verbinding te maken met elke virtuele machine in dit virtuele netwerk. Bastion gaat ervan uit dat u RDP gebruikt om verbinding te maken met een virtuele Windows-machine, en SSH om verbinding te maken met uw Linux-Vm's. Zie [verbinding maken met een VM-Linux](bastion-connect-vm-ssh.md)voor meer informatie over verbinding met een virtuele Linux-machine.

De volgende rollen zijn vereist om een verbinding te maken:

* Rol van lezer op de virtuele machine
* De rol van lezer op de NIC met het privé-IP-adres van de virtuele machine
* Rol van lezer in de Azure Bastion-resource

## <a name="rdp"></a>Verbinding maken via RDP

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken**. De VM moet een virtuele Windows-machine zijn wanneer u een RDP-verbinding gebruikt.

   ![VM-verbinding](./media/bastion-connect-vm-rdp/connect.png)
1. Nadat u op verbinding maken hebt geklikt, wordt er een zijbalk weer gegeven met drie tabbladen: RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, kunt u klikken op de koppeling om Bastion te configureren. Zie [Bastion configureren](bastion-create-host-portal.md)voor configuratie-instructies.

   ![VM-verbinding](./media/bastion-connect-vm-rdp/bastion.png)
1. Op het tabblad Bastion, de gebruikers naam en het wacht woord voor uw virtuele machine en klik vervolgens op **verbinding maken**. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

   ![VM-verbinding](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md)