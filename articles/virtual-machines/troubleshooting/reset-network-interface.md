---
title: Netwerkinterface opnieuw instellen voor Azure Windows VM| Microsoft Documenten
description: Toont hoe u de netwerkinterface voor Azure Windows VM opnieuw instellen
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: a8bd12d98b76d5848753987c4f7bcb76d4e2266d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250073"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Netwerkinterface opnieuw instellen voor virtuele Azure-machine voor Windows 

In dit artikel ziet u hoe u de netwerkinterface voor Azure Windows VM opnieuw instellen om problemen op te lossen wanneer u geen verbinding maken met Microsoft Azure Windows Virtual Machine (VM) nadat:

* U schakelt de standaard netwerkinterface (NIC) uit. 
* U stelt handmatig een statisch IP-adres in voor de NIC. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Netwerkinterface opnieuw instellen

### <a name="for-vms-deployed-in-resource-group-model"></a>VM's geïmplementeerd in resourcegroepmodel

1.  Ga naar de [Azure-portal.](https://ms.portal.azure.com)
2.  Selecteer de getroffen virtuele machine.
3.  Selecteer **Netwerken** en selecteer vervolgens de netwerkinterface van de vm.

    ![Locatie van de netwerkinterface](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Selecteer **IP-configuraties**.
5.  Selecteer het IP-adres. 
6.  Als de **private IP-toewijzing** niet **statisch**is, wijzigt u deze in **Statisch**.
7.  Wijzig het **IP-adres** naar een ander IP-adres dat beschikbaar is in het subnet.
8. De virtuele machine start opnieuw om de nieuwe NIC te initialiseren in het systeem.
9.  Probeer rdp naar uw machine. Als dit lukt, u het privé-IP-adres weer wijzigen naar het origineel als u dat wilt. Anders mag je het houden. 

#### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

1. Zorg ervoor dat u [de nieuwste Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) hebt geïnstalleerd
2. Open een verhoogde Azure PowerShell-sessie (Uitvoeren als beheerder). Voer de volgende opdrachten uit:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Probeer rdp naar uw machine.  Als dit lukt, u het privé-IP-adres weer wijzigen naar het origineel als u dat wilt. Anders mag je het houden.

### <a name="for-classic-vms"></a>Voor klassieke VM's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Voer de volgende stappen uit om de netwerkinterface opnieuw in te stellen:

#### <a name="use-azure-portal"></a>Azure Portal gebruiken

1.  Ga naar de [Azure-portal.]( https://ms.portal.azure.com)
2.  Selecteer **Virtuele machines (Klassiek)**.
3.  Selecteer de getroffen virtuele machine.
4.  Selecteer **IP-adressen**.
5.  Als de **private IP-toewijzing** niet **statisch**is, wijzigt u deze in **Statisch**.
6.  Wijzig het **IP-adres** naar een ander IP-adres dat beschikbaar is in het subnet.
7.  Selecteer **Opslaan**.
8.  De virtuele machine start opnieuw om de nieuwe NIC te initialiseren in het systeem.
9.  Probeer rdp naar uw machine. Als dit lukt, u ervoor kiezen om het privé-IP-adres terug te zetten naar het origineel.  

#### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

1. Zorg ervoor dat u [de nieuwste Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) hebt geïnstalleerd.
2. Open een verhoogde Azure PowerShell-sessie (Uitvoeren als beheerder). Voer de volgende opdrachten uit:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Probeer rdp naar uw machine. Als dit lukt, u het privé-IP-adres weer wijzigen naar het origineel als u dat wilt. Anders mag je het houden. 

## <a name="delete-the-unavailable-nics"></a>De niet-beschikbare NIC's verwijderen
Nadat u extern bureaublad naar de machine hebt kunnen afdrukken, moet u de oude NIC's verwijderen om het potentiële probleem te voorkomen:

1.  Open Apparaatbeheer.
2.  Selecteer Verborgen apparaten **weergeven** > **Show hidden devices**.
3.  Selecteer **Netwerkadapters**. 
4.  Controleer of de adapters met de naam "Microsoft Hyper-V Network Adapter".
5.  Mogelijk ziet u een niet-beschikbare adapter die grijs is. Klik met de rechtermuisknop op de adapter en selecteer Verwijderen.

    ![het beeld van de NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Verwijder alleen de niet-beschikbare adapters met de naam "Microsoft Hyper-V Network Adapter". Als u een van de andere verborgen adapters verwijdert, kan dit extra problemen veroorzaken.
    >
    >

6.  Nu moeten alle niet-beschikbare adapters worden gewist van uw systeem.
