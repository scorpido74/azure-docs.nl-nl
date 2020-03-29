---
title: Een virtueel netwerk configureren in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het configureren van een bestaand virtueel netwerk en subnet en gebruiken in een VM met Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6cf3d2f82c98a3caab47ff48a600316747932b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70390031"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Een virtueel netwerk configureren in Azure DevTest Labs
Zoals uitgelegd in het artikel [Een VM toevoegen aan een lab,](devtest-lab-add-vm.md)u bij het maken van een vm in een lab een geconfigureerd virtueel netwerk opgeven. Het kan bijvoorbeeld nodig zijn om toegang te krijgen tot uw corpnet-bronnen van uw VM's via het virtuele netwerk dat is geconfigureerd met ExpressRoute of site-to-site VPN.

In dit artikel wordt uitgelegd hoe u uw bestaande virtuele netwerk toevoegt aan de instellingen voor virtueel netwerk van een lab, zodat het beschikbaar is om te kiezen bij het maken van VM's.

> [!NOTE]
> Zie [Prijzen voor Azure Virtual Network](../virtual-network/virtual-networks-overview.md#pricing)voor meer informatie over de kosten die zijn gekoppeld aan de Azure Virtual Network-service.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Een virtueel netwerk configureren voor een lab met behulp van de Azure-portal
De volgende stappen lopen u door het toevoegen van een bestaand virtueel netwerk (en subnet) aan een lab, zodat het kan worden gebruikt bij het maken van een VM in hetzelfde lab. 

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met laboratoria het gewenste lab. 
1. Selecteer **Configuratie en beleid**in het hoofdvenster van het lab.

    ![Toegang tot de configuratie en het beleid van het lab](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Selecteer **virtuele netwerken**in de sectie **EXTERNE RESOURCES** . Er wordt een lijst met virtuele netwerken weergegeven die zijn geconfigureerd voor het huidige lab en het standaard virtuele netwerk dat voor uw lab is gemaakt. 
1. Selecteer **+ Toevoegen**.
   
    ![Een bestaand virtueel netwerk toevoegen aan uw lab](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Selecteer in het **deelvenster Virtueel netwerk** de optie **[Virtueel netwerk selecteren]**.
   
    ![Een bestaand virtueel netwerk selecteren](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Selecteer **in** het deelvenster Virtueel netwerk kiezen het gewenste virtuele netwerk. Er wordt een lijst weergegeven met alle virtuele netwerken die zich in het abonnement onder dezelfde regio bevinden als het lab.
1. Nadat u een virtueel netwerk hebt geselecteerd, wordt u teruggestuurd naar het **virtuele netwerkvenster.** Selecteer het subnet in de lijst onderaan.

    ![Subnetlijst](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Het deelvenster Subnet van het Lab wordt weergegeven.

    ![Subnetvenster Lab](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Geef een **subnetnaam lab op**.
   - Als u wilt toestaan dat een subnet wordt gebruikt in het maken van lab-vm's, selecteert u **Gebruik in het maken van virtuele machines**.
   - Als u een [gedeeld openbaar IP-adres](devtest-lab-shared-ip.md)wilt inschakelen, selecteert u **Gedeeld openbaar IP inschakelen**.
   - Als u openbare IP-adressen in een subnet wilt toestaan, selecteert **u Openbare IP-creatie toestaan**.
   - Geef in het veld **Maximaal virtuele machines per gebruiker** de maximale VM's per gebruiker op voor elk subnet. Als u een onbeperkt aantal VM's wilt, laat u dit veld leeg.
1. Selecteer **OK** om het deelvenster Subnet van het lab te sluiten.
1. Selecteer **Opslaan** om het virtuele netwerkvenster te sluiten.

Nu het virtuele netwerk is geconfigureerd, kan het worden geselecteerd bij het maken van een VM. Als u wilt zien hoe u een virtuele virtuele netwerk maakt en een virtueel netwerk opgeeft, raadpleegt u het [artikel, Een vm toevoegen aan een lab](devtest-lab-add-vm.md). 

De [virtual network documentation](https://docs.microsoft.com/azure/virtual-network) van Azure biedt meer informatie over het gebruik van VNets, inclusief het instellen en beheren van een VNet en het verbinden met uw on-premises netwerk.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Zodra u het gewenste virtuele netwerk aan uw lab hebt toegevoegd, is de volgende stap het [toevoegen van een VM aan uw lab.](devtest-lab-add-vm.md)

