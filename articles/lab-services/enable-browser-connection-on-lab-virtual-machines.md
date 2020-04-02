---
title: Browserverbinding inschakelen op virtuele azure-machines van Azure DevTest Labs | Microsoft Documenten
description: DevTest Labs integreert nu met Azure Bastion, als eigenaar van het lab u toegang tot alle virtuele laboratoriummachines via een browser inschakelen.
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
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549083"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Browserverbinding inschakelen op virtuele laboratoriummachines 

DevTest Labs integreert met [Azure Bastion,](https://docs.microsoft.com/azure/bastion/)waarmee u verbinding maken met uw virtuele machines via een browser. U moet eerst browserverbinding inschakelen op virtuele laboratoriummachines.

Als eigenaar van een lab u toegang tot alle virtuele laboratoriummachines via een browser inschakelen. U hebt geen extra client, agent of software nodig. Azure Bastion biedt veilige en naadloze RDP/SSH-connectiviteit met uw virtuele machines rechtstreeks in de Azure-portal via TLS. Wanneer u verbinding maakt via Azure Bastion, hebben uw virtuele machines geen openbaar IP-adres nodig. Zie Wat is Azure Bastion voor meer [informatie?](../bastion/bastion-overview.md)

> [!NOTE]
> Het inschakelen van browserverbinding op virtuele labmachines is in preview.

In dit artikel ziet u hoe u browserverbinding op virtuele laboratoriummachines inschakelen.

## <a name="prerequisites"></a>Vereisten 
Implementeer een Bastion-host in het virtuele netwerk **(OF)** van uw bestaande lab en verbind uw lab met een bastiongeconfigureerde VNet. 

Zie [Een Azure Bastion-host (Preview) maken](../bastion/bastion-create-host-portal.md)voor meer informatie over het implementeren van een Bastion-host in een VNet. Selecteer bij het maken van de Bastion-host het virtuele netwerk van het lab. 

Zie [Een virtueel netwerk configureren in Azure DevTest Labs](devtest-lab-configure-vnet.md)voor meer informatie over het verbinden van uw lab met een VNet met een Bastion geconfigureerd. Selecteer het VNet waarop de Bastion-host is geïmplementeerd en het **AzureBastionSubnet** erin. Hier zijn de gedetailleerde stappen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Alle services** in het linkernavigatiemenu. 
1. Selecteer **DevTest Labs** uit de lijst. 
1. Selecteer in de lijst met labs *uw lab.* 

    > [!NOTE]
    > Azure Bastion is momenteel in preview. Het is beperkt tot de volgende regio's: West-VS, Oost-VS, West-Europa, Zuid-Centraal VS, Australië Oost, en Japan East. Dus, maak een lab in een van deze regio's als je lab niet in een van hen. 
1. Selecteer **Configuratie en beleid** in de sectie **Instellingen** in het linkermenu. 
1. Selecteer **Virtuele netwerken**.
1. Selecteer **Toevoegen op** de werkbalk. 
1. Selecteer de **VNet** waarop de Bastion-host is geïmplementeerd. 
1. Selecteer het subnet: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Selecteer **Optie Gebruiken bij het maken van virtuele machines.** 
1. Selecteer **Opslaan** op de werkbalk. 
1. Als u een oude VNet voor het lab, verwijder het door het selecteren van **...*  en **verwijderen**. 

## <a name="enable-browser-connection"></a>Browserverbinding inschakelen 

Zodra u een Bastion geconfigureerd VNet in het lab, als een lab eigenaar, u browser verbinding op lab virtuele machines.

Voer de volgende stappen uit om browserverbinding op virtuele machines in het lab in te schakelen:

1. Navigeer in de Azure-portal naar *uw lab.*
1. Selecteer **Configuratie en beleid**.
1. Selecteer in **Instellingen** **Browserverbinding (Voorbeeld)**.

![Browserverbinding inschakelen](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over hoe u verbinding maken met uw VM's via een browser: [Verbinding maken met uw virtuele machines via een browser](connect-virtual-machine-through-browser.md)