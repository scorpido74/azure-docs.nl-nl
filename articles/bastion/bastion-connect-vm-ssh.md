---
title: Verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion
description: In dit artikel leert u hoe u verbinding kunt maken met een virtuele Linux-machine met behulp van Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 98f1f3d02d8433dd88fed844a587a961ea9f8c82
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84744286"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Via SSH verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion

Dit artikel laat u zien hoe u veilig en naadloos SSHeert met uw virtuele Linux-machines in een virtueel Azure-netwerk. U kunt rechtstreeks vanuit het Azure Portal verbinding maken met een virtuele machine. Wanneer u Azure Bastion gebruikt, is er geen client, agent of extra software vereist voor Vm's. Zie het [overzicht](bastion-overview.md)voor meer informatie over Azure Bastion.

U kunt Azure Bastion gebruiken om verbinding te maken met een virtuele Linux-machine via SSH. U kunt de gebruikers naam en het wacht woord en de SSH-sleutels voor verificatie gebruiken. U kunt met SSH-sleutels verbinding maken met uw virtuele machine met behulp van:

* Een persoonlijke sleutel die u hand matig invoert
* Een bestand dat de persoonlijke sleutel gegevens bevat

De persoonlijke SSH-sleutel moet een indeling hebben die begint met `"-----BEGIN RSA PRIVATE KEY-----"` en eindigt met `"-----END RSA PRIVATE KEY-----"` .

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Azure bastion-host hebt ingesteld voor het virtuele netwerk waarin de VM zich bevindt. Zie [een Azure bastion-host maken](bastion-create-host-portal.md)voor meer informatie. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om verbinding te maken met elke virtuele machine in dit virtuele netwerk. 

Wanneer u Bastion gebruikt om verbinding te maken, wordt ervan uitgegaan dat u RDP gebruikt om verbinding te maken met een Windows-VM, en SSH om verbinding te maken met uw Linux-Vm's. Zie [verbinding maken met een VM-Windows](bastion-connect-vm-rdp.md)voor meer informatie over het maken van een verbinding met een virtuele Windows-machine.

### <a name="required-roles"></a>Vereiste rollen

De volgende rollen zijn vereist om een verbinding te maken:

* Rol van lezer op de virtuele machine
* De rol van lezer op de NIC met het privé-IP-adres van de virtuele machine
* Rol van lezer in de Azure Bastion-resource

### <a name="ports"></a>Poorten

Om via SSH verbinding te maken met de virtuele Linux-machine, moeten de volgende poorten zijn geopend op uw VM:

* Binnenkomende poort: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Verbinding maken: gebruikers naam en wacht woord gebruiken

1. Open [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken** en selecteer **Bastion** in de vervolg keuzelijst.

   ![Verbinding maken](./media/bastion-connect-vm-ssh/connect.png)
1. Nadat u op Bastion hebt geklikt, wordt er een zijbalk weer gegeven met drie tabbladen: RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikers naam en het wacht woord voor SSH in voor de virtuele machine.
1. Klik op de knop **verbinding maken** nadat u de sleutel hebt ingevoerd.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Verbinden: Voer een persoonlijke sleutel hand matig in

1. Open [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken** en selecteer **Bastion** in de vervolg keuzelijst.

   ![Verbinding maken](./media/bastion-connect-vm-ssh/connect.png)
1. Nadat u op Bastion hebt geklikt, wordt er een zijbalk weer gegeven met drie tabbladen: RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikers naam in en selecteer **persoonlijke SSH-sleutel**.
1. Voer uw persoonlijke sleutel in de **persoonlijke SSH-sleutel** van het tekst gebied in (of plak deze direct).
1. Klik op de knop **verbinding maken** nadat u de sleutel hebt ingevoerd.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Verbinding maken: een persoonlijke-sleutel bestand gebruiken

1. Open [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken** en selecteer **Bastion** in de vervolg keuzelijst.

   ![Verbinding maken](./media/bastion-connect-vm-ssh/connect.png)
1. Nadat u op Bastion hebt geklikt, wordt er een zijbalk weer gegeven met drie tabbladen: RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikers naam in en selecteer een **persoonlijke SSH-sleutel uit het lokale bestand**.
1. Klik op de **Blader** knop (het mappictogram in het lokale bestand).
1. Blader naar het bestand en klik vervolgens op **openen**.
1. Klik op **verbinden** om verbinding te maken met de virtuele machine. Nadat u op verbinding maken hebt geklikt, wordt SSH naar deze virtuele machine rechtstreeks in de Azure Portal geopend. Deze verbinding is meer dan HTML5 via poort 443 van de Bastion-service via het privé-IP-adres van uw virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md)
