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
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74970763"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Browserverbinding inschakelen op virtuele laboratoriummachines 
DevTest Labs integreert met [Azure Bastion,](https://docs.microsoft.com/azure/bastion/)waarmee u verbinding maken met uw virtuele machines via een browser. U moet eerst browserverbinding inschakelen op virtuele laboratoriummachines.

Als eigenaar van een lab u toegang tot alle virtuele laboratoriummachines via een browser inschakelen. U hebt geen extra client, agent of software nodig. Azure Bastion biedt veilige en naadloze RDP/SSH-connectiviteit met uw virtuele machines rechtstreeks in de Azure-portal via SSL. Wanneer u verbinding maakt via Azure Bastion, hebben uw virtuele machines geen openbaar IP-adres nodig. Zie Wat is Azure Bastion voor meer [informatie?](../bastion/bastion-overview.md)


In dit artikel ziet u hoe u browserverbinding op virtuele laboratoriummachines inschakelen.

## <a name="prerequisites"></a>Vereisten 
Implementeer een Bastion-host in het virtuele netwerk **(OF)** van uw bestaande lab en verbind uw lab met een door Bastion geconfigureerd virtueel netwerk. 

Zie [Een Azure Bastion-host maken](../bastion/bastion-create-host-portal.md)voor meer informatie over het implementeren van een Bastion-host in een virtueel netwerk. Selecteer bij het maken van de Bastion-host het virtuele netwerk van het lab. 

Ten eerste moet u een tweede subnet maken in het virtuele Bastion-netwerk, omdat het AzureBastionSubnet het maken van niet-Bastion-resources er niet toe staat. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Een tweede subnet maken in het virtuele Bastion-netwerk
U geen vm's voor labs maken in een Azure Bastion-subnet. Maak een ander subnet binnen het virtuele Bastion-netwerk zoals weergegeven in de volgende afbeelding:

![Tweede subnet in het virtuele Azure Bastion-netwerk](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Vm-creatie inschakelen in het subnet
Schakel nu het maken van VM's in dit subnet in door de volgende stappen te volgen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Alle services** in het linkernavigatiemenu. 
1. Selecteer **DevTest Labs** uit de lijst. 
1. Selecteer in de lijst met labs *uw lab.* 

    > [!NOTE]
    > Azure Bastion is nu algemeen beschikbaar in de volgende regio's: West-VS, Oost-VS, West-Europa, Zuid-Centraal VS, Australië-Oost en Japan East. Dus, maak een lab in een van deze regio's als je lab niet in een van hen. 
    
1. Selecteer **Configuratie en beleid** in de sectie **Instellingen** in het linkermenu. 
1. Selecteer **Virtuele netwerken**.
1. Selecteer **Toevoegen op** de werkbalk. 
1. Selecteer het **virtuele netwerk** waarop de Bastion-host is geïmplementeerd. 
1. Selecteer het subnet voor VM's, niet het **AzureBastionSubnet,** het andere subnet dat u eerder hebt gemaakt. Sluit de pagina en open deze opnieuw als u het subnet niet ziet in de lijst onderaan. 

    ![Vm-creatie inschakelen in het subnet](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Selecteer **Optie Gebruiken bij het maken van virtuele machines.** 
1. Selecteer **Opslaan** op de werkbalk. 
1. Als u een oud virtueel netwerk voor het lab hebt, verwijder het dan door **...*  en **verwijderen**. 

## <a name="enable-browser-connection"></a>Browserverbinding inschakelen 

Zodra u een Bastion geconfigureerd virtueel netwerk in het lab, als een lab eigenaar, u browser verbinding op lab virtuele machines.

Voer de volgende stappen uit om browserverbinding op virtuele machines in het lab in te schakelen:

1. Navigeer in de Azure-portal naar *uw lab.*
1. Selecteer **Configuratie en beleid**.
1. Selecteer **Browserverbinding**in **Instellingen**. Als u deze optie niet ziet, sluit u de pagina **Configuratiebeleid** en opent u deze opnieuw. 

    ![Browserverbinding inschakelen](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over hoe u verbinding maken met uw VM's via een browser: [Verbinding maken met uw virtuele machines via een browser](connect-virtual-machine-through-browser.md)
