---
title: Browser verbinding op Azure DevTest Labs virtuele machines inschakelen | Microsoft Docs
description: DevTest Labs kan nu worden geïntegreerd met Azure Bastion. als eigenaar van het lab kunt u toegang tot alle virtuele lab-machines via een browser inschakelen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 080dd91b2ab6792debfae3a3ccc97b0927015de4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580135"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Browser verbinding inschakelen op virtuele lab-machines 

DevTest Labs kan worden geïntegreerd met [Azure Bastion](https://docs.microsoft.com/azure/bastion/), waarmee u via een browser verbinding kunt maken met uw virtuele machines. U moet eerst de browser verbinding inschakelen op de virtuele lab-machines.

Als eigenaar van een lab kunt u de toegang tot alle virtuele lab-machines inschakelen via een browser. U hebt geen aanvullende client, agent of software nodig. Azure Bastion biedt beveiligde en naadloze RDP-/SSH-connectiviteit in uw virtuele machine, rechtstreeks in Azure Portal via SSL. Wanneer u verbinding maakt via Azure Bastion, hebt u geen openbaar IP-adres nodig voor uw virtuele machines. Zie [Wat is Azure Bastion?](../bastion/bastion-overview.md) voor meer informatie.


In dit artikel wordt beschreven hoe u browser verbinding kunt inschakelen voor virtuele lab-machines.

## <a name="prerequisites"></a>Vereisten 
Implementeer een bastion-host in uw bestaande virtuele netwerk van uw Lab **(of)** Verbind uw Lab met een bastion geconfigureerd VNet. 

Zie [een Azure bastion-host maken (preview)](../bastion/bastion-create-host-portal.md)voor meer informatie over het implementeren van een bastion-host in een VNet. Wanneer u de bastion-host maakt, selecteert u het virtuele netwerk van de test omgeving. 

Zie [een virtueel netwerk configureren in azure DevTest Labs](devtest-lab-configure-vnet.md)voor meer informatie over het verbinden van uw Lab met een bastion geconfigureerde VNet. Selecteer het VNet waarop de bastion-host is geïmplementeerd en de **AzureBastionSubnet** . Hier volgen de gedetailleerde stappen: 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **alle services** in het navigatie menu links. 
1. Selecteer **DevTest Labs** uit de lijst. 
1. Selecteer in de lijst met Labs *uw Lab*. 

    > [!NOTE]
    > Azure Bastion is nu algemeen verkrijgbaar in de volgende regio's: VS-West, VS-Oost, Europa-west, Zuid-Centraal VS, Australië-oost en Japan-Oost. Maak in een van deze regio's een Lab als uw Lab zich niet bevindt. 
    
1. Selecteer **configuratie en beleid** in de sectie **instellingen** in het menu links. 
1. Selecteer **virtuele netwerken**.
1. Selecteer **toevoegen** op de werk balk. 
1. Selecteer het **VNet** waarop de bastion-host is geïmplementeerd. 
1. Selecteer het subnet: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Selecteer **gebruiken in de optie voor het maken van virtuele machines** . 
1. Selecteer **Opslaan** op de werkbalk. 
1. Als u een oud VNet voor het Lab hebt, verwijdert u dit door * *...* te selecteren.  en **verwijderen**. 

## <a name="enable-browser-connection"></a>Browser verbinding inschakelen 

Zodra u in het lab een bastion-geconfigureerde VNet hebt, kunt u browser verbinding maken inschakelen voor virtuele lab-machines.

Voer de volgende stappen uit om browser verbinding te maken op virtuele lab-machines:

1. Ga in het Azure Portal naar *uw Lab*.
1. Selecteer **configuratie en beleid**.
1. Selecteer in **instellingen**de optie **browser verbinding maken (preview)** .

    ![Browser verbinding inschakelen](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over het maken van verbinding met uw Vm's met behulp van een browser: [verbinding maken met uw virtuele machines via een browser](connect-virtual-machine-through-browser.md)
