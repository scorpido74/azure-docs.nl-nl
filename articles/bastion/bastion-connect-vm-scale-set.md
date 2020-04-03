---
title: Verbinding maken met een Windows-set voor virtuele machines met Azure Bastion | Microsoft Documenten
description: In dit artikel leest u hoe u verbinding maken met een Azure-set voor virtuele machines met Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 290a20fcd827841c24983f3bdd54b6db8e154462
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619344"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Verbinding maken met een virtuele machineschaalset met Azure Bastion

In dit artikel ziet u hoe u veilig en naadloos RDP gebruiken voor uw Windows-installatie setset voor virtuele machines in een Azure-virtueel netwerk met Azure Bastion. U rechtstreeks vanuit de Azure-portal verbinding maken met een exemplaar met een virtuele machineschaalset. Bij het gebruik van Azure Bastion hebben VM's geen client, agent of extra software nodig. Zie het [overzicht](bastion-overview.md)voor meer informatie over Azure Bastion.

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u een Azure Bastion-host hebt ingesteld voor het virtuele netwerk waarin de virtuele machineschaalset zich bevindt. Zie [Een Azure Bastion-host maken voor](bastion-create-host-portal.md)meer informatie. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, u deze gebruiken om verbinding te maken met een instantie met virtuele machineschaalinstel in dit virtuele netwerk. Bastion gaat ervan uit dat u RDP gebruikt om verbinding te maken met een Windows-set voor virtuele machineschaal en SSH om verbinding te maken met uw Linux-virtuele machineschaalset. Zie Verbinding maken met een [VM - Linux](bastion-connect-vm-ssh.md)voor informatie over verbinding met een Linux-vm.

## <a name="connect-using-rdp"></a><a name="rdp"></a>Verbinding maken via RDP

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machineschaalset waarmee u verbinding wilt maken.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Navigeer naar de instantie met de virtuele machineschaalset waarmee u verbinding wilt maken en selecteer **Verbinding maken**. Bij gebruik van een RDP-verbinding moet de virtuele machineschaalset een Windows-schaalset voor virtuele machines zijn.

   ![virtuele machineschaalset](./media/bastion-connect-vm-scale-set/2.png)
3. Nadat u **Connect hebt**geselecteerd, verschijnt er een zijbalk met drie tabbladen : RDP, SSH en Bastion. Selecteer het tabblad **Bastion** op de zijbalk. Als u Bastion niet hebt ingericht voor het virtuele netwerk, u de koppeling selecteren om Bastion te configureren. Zie [Bastion configureren voor](bastion-create-host-portal.md)configuratie-instructies.

   ![Tabblad Bastion](./media/bastion-connect-vm-scale-set/3.png)
4. Voer op het tabblad Bastion de gebruikersnaam en het wachtwoord in voor de grootte van uw virtuele machine en selecteer **Verbinding maken**.

   ![verbinding maken](./media/bastion-connect-vm-scale-set/4.png)
5. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure-portal (via HTML5) met poort 443 en de Bastion-service.

## <a name="next-steps"></a>Volgende stappen

Lees de [Bastion FAQ](bastion-faq.md).