---
title: Verbinding maken met een Linux-vm met Azure Bastion
description: In dit artikel leest u hoe u verbinding maken met Virtual Machine van Linux met Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596824"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Verbinding maken met SSH met een virtuele Linux-machine met Azure Bastion

In dit artikel ziet u hoe u veilig en naadloos SSH ssh's naar uw Linux-VM's in een virtueel Azure-netwerk. U rechtstreeks vanuit de Azure-portal verbinding maken met een virtuele machine. Bij het gebruik van Azure Bastion hebben VM's geen client, agent of extra software nodig. Zie het [overzicht](bastion-overview.md)voor meer informatie over Azure Bastion.

U Azure Bastion gebruiken om verbinding te maken met een virtuele Linux-machine met Behulp van SSH. U zowel gebruikersnaam/wachtwoord als SSH-sleutels gebruiken voor verificatie. U verbinding maken met uw VM met SSH-sleutels met behulp van:

* Een privésleutel die u handmatig invoert
* Een bestand met de persoonlijke sleutelgegevens

De SSH-privésleutel moet in een `"-----BEGIN RSA PRIVATE KEY-----"` formaat `"-----END RSA PRIVATE KEY-----"`zijn dat begint met en eindigt met .

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Azure Bastion-host hebt ingesteld voor het virtuele netwerk waarin de VM zich bevindt. Zie [Een Azure Bastion-host maken voor](bastion-create-host-portal.md)meer informatie. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, u deze gebruiken om verbinding te maken met elke virtuele virtuele virtuele netwerk. 

Wanneer u Bastion gebruikt om verbinding te maken, wordt ervan uitgegaan dat u RDP gebruikt om verbinding te maken met een Windows VM en SSH om verbinding te maken met uw Linux-VM's. Zie Verbinding maken met een [VM - Windows](bastion-connect-vm-rdp.md)voor informatie over het maken van verbinding met een Windows-vm.

### <a name="required-roles"></a>Vereiste rollen

Om een verbinding te maken, zijn de volgende rollen vereist:

* De rol van de lezer op de virtuele machine
* De rol van de lezer op NIC met privé IP van de virtuele machine
* Leesrol op de Azure Bastion-bron

### <a name="ports"></a>Poorten

Om verbinding te maken met de Linux VM via SSH, moet u de volgende poorten open hebben op uw VM:

* Inkomende poort: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Verbinding maken: gebruikersnaam en wachtwoord gebruiken

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken, klik vervolgens op **Verbinding maken** en selecteer **Bastion** in de vervolgkeuzelijst.

   ![Verbinding maken](./media/bastion-connect-vm-ssh/connect.png)
1. Nadat u op Bastion hebt geklikt, verschijnt er een zijbalk met drie tabbladen : RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Zie [Bastion configureren](bastion-create-host-portal.md)als u Bastion niet hebt ingericht voor het virtuele netwerk.

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikersnaam en het wachtwoord voor SSH in op uw virtuele machine.
1. Klik op de knop **Verbinden** nadat u de sleutel hebt ingevoerd.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Verbinding maken: handmatig een privésleutel invoeren

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken, klik vervolgens op **Verbinding maken** en selecteer **Bastion** in de vervolgkeuzelijst.

   ![Verbinding maken](./media/bastion-connect-vm-ssh/connect.png)
1. Nadat u op Bastion hebt geklikt, verschijnt er een zijbalk met drie tabbladen : RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Zie [Bastion configureren](bastion-create-host-portal.md)als u Bastion niet hebt ingericht voor het virtuele netwerk.

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikersnaam in en selecteer **SSH-privésleutel**.
1. Voer uw privésleutel in in het tekstgebied **SSH Private Key** (of plak deze direct).
1. Klik op de knop **Verbinden** nadat u de sleutel hebt ingevoerd.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Verbinding maken: een privésleutelbestand gebruiken

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken, klik vervolgens op **Verbinding maken** en selecteer **Bastion** in de vervolgkeuzelijst.

   ![Verbinding maken](./media/bastion-connect-vm-ssh/connect.png)
1. Nadat u op Bastion hebt geklikt, verschijnt er een zijbalk met drie tabbladen : RDP, SSH en Bastion. Als Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Zie [Bastion configureren](bastion-create-host-portal.md)als u Bastion niet hebt ingericht voor het virtuele netwerk.

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Voer de gebruikersnaam in en selecteer **SSH-privésleutel uit lokaal bestand**.
1. Klik **op de** knop Bladeren (het mappictogram in het lokale bestand).
1. Blader naar het bestand en klik op **Openen**.
1. Klik **op Verbinding maken** om verbinding te maken met de virtuele machine. Zodra u op Verbinding klikt, wordt SSH naar deze virtuele machine rechtstreeks geopend in de Azure-portal. Deze verbinding is meer dan HTML5 met behulp van poort 443 op de Bastion-service via de privé-IP van uw virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Lees de [veelgestelde vragen over Bastion](bastion-faq.md)
