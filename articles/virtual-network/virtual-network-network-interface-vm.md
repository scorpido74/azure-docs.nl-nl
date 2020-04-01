---
title: Netwerkinterfaces toevoegen aan of verwijderen uit Azure VM's
description: Meer informatie over het toevoegen van netwerkinterfaces aan of verwijderen van netwerkinterfaces van virtuele machines.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060325"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Netwerkinterfaces toevoegen aan of verwijderen van netwerkinterfaces van virtuele machines

Meer informatie over het toevoegen van een bestaande netwerkinterface wanneer u een virtuele Azure-machine (VM) maakt. Leer ook netwerkinterfaces toevoegen of verwijderen van een bestaande VM in de gestopte (deallocated) status. Met een netwerkinterface kan een Azure VM communiceren met internet-, Azure- en on-premises bronnen. Een VM heeft een of meer netwerkinterfaces. 

Zie IP-adressen voor [netwerkinterface beheren](virtual-network-network-interface-addresses.md)als u IP-adressen voor een netwerkinterface wilt toevoegen, wijzigen of verwijderen. Zie [Netwerkinterfaces beheren](virtual-network-network-interface.md)als u netwerkinterfaces wilt maken, wijzigen of verwijderen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u er geen hebt, stelt u een Azure-account in met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voltooi een van deze taken voordat u aan de rest van dit artikel begint:

- **Portalgebruikers:** Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-account.

- **PowerShell-gebruikers**: Voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of voer PowerShell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek op het tabblad Azure Cloud Shell de vervolgkeuzelijst **Voor de omgeving selecteren** en kies **PowerShell** als deze nog niet is geselecteerd.

    Als u PowerShell lokaal gebruikt, gebruikt u Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

- **CLI-gebruikers (Azure Command-line interface)** uitvoeren: voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI-versie 2.0.26 of hoger als u de Azure CLI lokaal uitvoert. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Voer `az login` uit om een verbinding op te zetten met Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Bestaande netwerkinterfaces toevoegen aan een nieuwe virtuele machine

Wanneer u via de portal een virtuele machine maakt, maakt de portal een netwerkinterface met standaardinstellingen en koppelt de netwerkinterface aan de VM voor u. U de portal niet gebruiken om bestaande netwerkinterfaces toe te voegen aan een nieuwe virtuele machine of om een VM met meerdere netwerkinterfaces te maken. U beide doen met behulp van de CLI of PowerShell. Zorg ervoor dat u vertrouwd te raken met de [beperkingen](#constraints). Als u een vm met meerdere netwerkinterfaces maakt, moet u het besturingssysteem ook configureren om deze correct te gebruiken nadat u de VM hebt gemaakt. Meer informatie over het configureren van [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) voor meerdere netwerkinterfaces.

### <a name="commands"></a>Opdrachten

Voordat u de VM maakt, [maakt u een netwerkinterface](virtual-network-network-interface.md#create-a-network-interface).

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Een netwerkinterface toevoegen aan een bestaande virtuele machine

Ga als lid van het nieuwe bedrijf naar een netwerkinterface toe aan uw virtuele machine:

1. Ga naar de [Azure-portal](https://portal.azure.com) om een bestaande virtuele machine te vinden. Zoeken naar virtuele machines en selecteer **deze**.

2. Selecteer de naam van uw vm. De VM moet het aantal netwerkinterfaces ondersteunen dat u wilt toevoegen. Zie de groottes in Azure voor [Linux VM's](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows VM's](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het aantal netwerkinterfaces dat elke VM-grootte ondersteunt.

3. Selecteer **stoppen**en **vervolgens OK** in het bevestigingsdialoogvenster in de vm-opdrachtbalk. Wacht vervolgens tot de **status** van de VM wordt gewijzigd **in Gestopt (deallocated).**

4. Kies**netwerkinterface netwerkinterface netwerken koppelen**in de menubalk **van** > de VM . Kies vervolgens in **Bestaande netwerkinterface koppelen**de netwerkinterface die u wilt koppelen en selecteer **OK**.

    >[!NOTE]
    >De netwerkinterface die u selecteert, kan geen versnelde netwerkingeschakeld hebben, kan geen IPv6-adres hebben toegewezen en moet in hetzelfde virtuele netwerk bestaan met de netwerkinterface die momenteel aan de VM is gekoppeld.

    Als u geen bestaande netwerkinterface hebt, moet u er eerst een maken. Selecteer hiervoor **netwerkinterface maken**. Zie [Een netwerkinterface maken](virtual-network-network-interface.md#create-a-network-interface)voor meer informatie over het maken van een netwerkinterface. Zie [Beperkingen](#constraints)voor meer informatie over extra beperkingen bij het toevoegen van netwerkinterfaces aan virtuele machines.

5. Kies **overzicht start** > **in** de menubalk van de VM om de virtuele machine opnieuw op te starten.

Nu u het VM-besturingssysteem configureren om meerdere netwerkinterfaces correct te gebruiken. Meer informatie over het configureren van [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) voor meerdere netwerkinterfaces.

### <a name="commands"></a>Opdrachten

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az vm nic toevoegen](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (referentie); [gedetailleerde stappen](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referentie); [gedetailleerde stappen](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Netwerkinterfaces voor een virtuele machine weergeven

U de netwerkinterfaces bekijken die momenteel aan een VM zijn gekoppeld om meer te weten te komen over de configuratie van elke netwerkinterface en de IP-adressen die aan elke netwerkinterface zijn toegewezen. 

1. Ga naar de [Azure-portal](https://portal.azure.com) om een bestaande virtuele machine te vinden. Zoeken naar virtuele machines en selecteer **deze**.

    >[!NOTE]
    >Meld u aan met een account waarvoor de rol Eigenaar, Inzender of Netwerkbijdrager voor uw abonnement is toegewezen. Zie [Ingebouwde rollen voor Azure-rolegebaseerde toegangscontrole voor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)meer informatie over het toewijzen van rollen aan accounts.

2. Selecteer de naam van de VM waarvoor u gekoppelde netwerkinterfaces wilt weergeven.

3. Selecteer **netwerken**in de menubalk van de VM .

Zie [Netwerkinterfaces beheren](virtual-network-network-interface.md)voor meer informatie over de instellingen van de netwerkinterface en hoe u deze wijzigen. Zie [IP-adressen](virtual-network-network-interface-addresses.md)van de netwerkinterface beheren voor meer informatie over het toevoegen, wijzigen of verwijderen van IP-adressen die zijn toegewezen aan een netwerkinterface.

### <a name="commands"></a>Opdrachten

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az vm nic lijst](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Een netwerkinterface verwijderen uit een virtuele machine

1. Ga naar de [Azure-portal](https://portal.azure.com) om een bestaande virtuele machine te vinden. Zoeken naar virtuele machines en selecteer **deze**.

2. Selecteer de naam van de VM waarvoor u gekoppelde netwerkinterfaces wilt weergeven.

3. Kies op de werkbalk VM de keuze **Stoppen**.

4. Wacht tot de **status** van de virtuele machine wordt gewijzigd **in Gestopt (deallocated).**

5. Kies**netwerkinterface Netwerkontkoppel**in de menubalk **van** > de VM.

6. Selecteer in het dialoogvenster **Netwerkinterface loskoppelen** de netwerkinterface die u wilt loskoppelen. Selecteer vervolgens **OK**.

    >[!NOTE]
    >Als er slechts één netwerkinterface wordt vermeld, u deze niet loskoppelen, omdat een virtuele machine altijd ten minste één netwerkinterface moet hebben.

### <a name="commands"></a>Opdrachten

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az vm nic verwijderen](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (referentie); [gedetailleerde stappen](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referentie); [gedetailleerde stappen](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Beperkingen

- Een VM moet ten minste één netwerkinterface hebben.

- Een VM kan er slechts zoveel netwerkinterfaces aan koppelen als de VM-grootte ondersteunt. Zie de groottes in Azure voor [Linux VM's](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of Windows [VM's](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het aantal netwerkinterfaces dat elke VM-grootte ondersteunt. Alle formaten ondersteunen ten minste twee netwerkinterfaces.

- De netwerkinterfaces die u aan een vm toevoegt, kunnen momenteel niet aan een andere virtuele machine worden gekoppeld. Zie [Een netwerkinterface maken](virtual-network-network-interface.md#create-a-network-interface)voor meer informatie over het maken van netwerkinterfaces.

- In het verleden kon u alleen netwerkinterfaces toevoegen aan VM's die meerdere netwerkinterfaces ondersteunden en zijn gemaakt met ten minste twee netwerkinterfaces. U geen netwerkinterface toevoegen aan een VM die is gemaakt met één netwerkinterface, zelfs niet als de VM-grootte meer dan één netwerkinterface ondersteunde. Omgekeerd kon u alleen netwerkinterfaces van een VM verwijderen met ten minste drie netwerkinterfaces, omdat VM's die met ten minste twee netwerkinterfaces zijn gemaakt, altijd ten minste twee netwerkinterfaces moesten hebben. Deze beperkingen zijn niet langer van toepassing. U nu een VM maken met een willekeurig aantal netwerkinterfaces (tot het aantal dat wordt ondersteund door de VM-grootte).

- Standaard is de eerste netwerkinterface die aan een VM is gekoppeld de *primaire* netwerkinterface. Alle andere netwerkinterfaces in de VM zijn *secundaire* netwerkinterfaces.

- U bepalen naar welke netwerkinterface u uitgaand verkeer verzendt. Een VM stuurt echter standaard al het uitgaande verkeer naar het IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerkinterface.

- In het verleden waren alle VM's binnen dezelfde beschikbaarheidsset vereist om één of meerdere netwerkinterfaces te hebben. VM's met een willekeurig aantal netwerkinterfaces kunnen nu in dezelfde beschikbaarheidsset bestaan, tot het aantal dat wordt ondersteund door de VM-grootte. U een vm alleen toevoegen aan een beschikbaarheidsset wanneer deze wordt gemaakt. Zie [De beschikbaarheid van VM's in Azure beheren](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)voor meer informatie over beschikbaarheidssets.

- U netwerkinterfaces in dezelfde VM verbinden met verschillende subnetten binnen een virtueel netwerk. De netwerkinterfaces moeten echter allemaal zijn aangesloten op hetzelfde virtuele netwerk.

- U elk IP-adres voor elke IP-configuratie van een primaire of secundaire netwerkinterface toevoegen aan een back-endpool van Azure Load Balancer. In het verleden kon alleen het primaire IP-adres voor de primaire netwerkinterface worden toegevoegd aan een back-endpool. Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)voor meer informatie over IP-adressen en configuraties.

- Als u een VM verwijdert, worden de netwerkinterfaces die eraan zijn gekoppeld, niet verwijderd. Wanneer u een vm verwijdert, worden de netwerkinterfaces losgekoppeld van de VM. U deze netwerkinterfaces toevoegen aan verschillende VM's of deze verwijderen.

- Net als bij IPv6 u een netwerkinterface met versnelde netwerken die zijn ingeschakeld, niet aan een VM koppelen nadat u deze hebt gemaakt. Om te profiteren van versnelde netwerken, moet u ook stappen uitvoeren binnen het VM-besturingssysteem. Meer informatie over versnelde netwerken en andere beperkingen bij het gebruik ervan voor virtuele [Windows-](create-vm-accelerated-networking-powershell.md) of [Linux-machines.](create-vm-accelerated-networking-cli.md)

## <a name="next-steps"></a>Volgende stappen

Zie:

|Taak|Hulpprogramma|
|---|---|
|Een virtuele machine met meerdere NIC's maken|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Eén NIC-vm maken met meerdere IPv4-adressen|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Eén NIC-vm maken met een privé-IPv6-adres (achter een Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sjabloon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
