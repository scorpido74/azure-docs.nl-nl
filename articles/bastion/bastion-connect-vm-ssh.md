---
title: Verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion | Microsoft Docs
description: In dit artikel leert u hoe u verbinding kunt maken met een virtuele Linux-machine met behulp van Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: b88327ea0b5d2958cc1c86fa317415f2441af894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494478"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Via SSH verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion

Dit artikel laat u zien hoe u veilig en naadloos SSHeert met uw virtuele Linux-machines in een virtueel Azure-netwerk. U kunt rechtstreeks verbinding maken met een VM vanuit Azure Portal. Bij gebruik van Azure Bastion hebben VM's geen client, agent of aanvullende software nodig. Zie het [overzicht](bastion-overview.md)voor meer informatie over Azure Bastion.

U kunt Azure Bastion gebruiken om verbinding te maken met een virtuele Linux-machine via SSH. U kunt de gebruikers naam en het wacht woord en de SSH-sleutels voor verificatie gebruiken. U kunt met SSH-sleutels verbinding maken met uw virtuele machine met behulp van:

* Een persoonlijke sleutel die u hand matig invoert
* Een bestand dat de persoonlijke sleutel gegevens bevat

De persoonlijke SSH-sleutel moet een indeling hebben die begint met `"-----BEGIN RSA PRIVATE KEY-----"` en eindigt met `"-----END RSA PRIVATE KEY-----"`.

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Azure bastion-host hebt ingesteld voor het virtuele netwerk waarin de VM zich bevindt. Zie [een Azure bastion-host maken](bastion-create-host-portal.md)voor meer informatie. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om verbinding te maken met elke virtuele machine in dit virtuele netwerk. Wanneer u Bastion gebruikt om verbinding te maken, wordt ervan uitgegaan dat u RDP gebruikt om verbinding te maken met een Windows-VM, en SSH om verbinding te maken met uw Linux-Vm's.

De volgende rollen zijn vereist om een verbinding te maken:

* Rol van lezer op de virtuele machine
* De rol van lezer op de NIC met het privé-IP-adres van de virtuele machine
* Rol van lezer in de Azure Bastion-resource

## <a name="username"></a>Verbinding maken: gebruikers naam en wacht woord gebruiken

1.   Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken**. De VM moet een virtuele Linux-machine zijn wanneer u een SSH-verbinding gebruikt.
1. Nadat u op verbinding maken hebt geklikt, wordt er een zijbalk weer gegeven met drie tabbladen: RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](bastion-create-host-portal.md).

   ![VM-verbinding](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikers naam en het wacht woord voor SSH in voor de virtuele machine.
1. Klik op de knop **verbinding maken** nadat u de sleutel hebt ingevoerd.

## <a name="privatekey"></a>Verbinden: Voer een persoonlijke sleutel hand matig in

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken**. De VM moet een virtuele Linux-machine zijn wanneer u een SSH-verbinding gebruikt.
1. Nadat u op verbinding maken hebt geklikt, wordt er een zijbalk weer gegeven met drie tabbladen: RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](bastion-create-host-portal.md).

   ![VM-verbinding](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikers naam in en selecteer **persoonlijke SSH-sleutel**.
1. Voer uw persoonlijke sleutel in de **persoonlijke SSH-sleutel** van het tekst gebied in (of plak deze direct).
1. Klik op de knop **verbinding maken** nadat u de sleutel hebt ingevoerd.

## <a name="ssh"></a>Verbinding maken: een persoonlijke-sleutel bestand gebruiken

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken**. De VM moet een virtuele Linux-machine zijn wanneer u een SSH-verbinding gebruikt.

   ![VM-verbinding](./media/bastion-connect-vm-ssh/connect.png)
1. Nadat u op verbinding maken hebt geklikt, wordt er een zijbalk weer gegeven met drie tabbladen: RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](bastion-create-host-portal.md).

   ![VM-verbinding](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikers naam in en selecteer een **persoonlijke SSH-sleutel uit het lokale bestand**.
1. Klik op de **Blader** knop (het mappictogram in het lokale bestand).
1. Blader naar het bestand en klik vervolgens op **openen**.
1. Klik op **verbinden** om verbinding te maken met de virtuele machine. Nadat u op verbinding maken hebt geklikt, wordt SSH naar deze virtuele machine rechtstreeks in de Azure Portal geopend. Deze verbinding is meer dan HTML5 via poort 443 van de Bastion-service via het privé-IP-adres van uw virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md)