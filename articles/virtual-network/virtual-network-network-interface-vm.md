---
title: Netwerk interfaces toevoegen aan of verwijderen uit Azure Vm's
description: Meer informatie over het toevoegen van netwerk interfaces aan of het verwijderen van netwerk interfaces van virtuele machines.
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
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: a55bf014a2da10069e4e6a5f6f4eb4b8cd9ff205
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279557"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Netwerk interfaces toevoegen aan of verwijderen van virtuele machines

Meer informatie over het toevoegen van een bestaande netwerk interface bij het maken van een virtuele Azure-machine (VM) of het toevoegen of verwijderen van netwerk interfaces van een bestaande virtuele machine in de status gestopt (toewijzing opgeheven). Met een netwerk interface kan een virtuele machine van Azure communiceren met internet-, Azure-en on-premises resources. Een virtuele machine kan een of meer netwerk interfaces hebben. 

Zie [IP-adressen van netwerk interfaces beheren](virtual-network-network-interface-addresses.md)als u IP-adressen voor een netwerk interface wilt toevoegen, wijzigen of verwijderen. Zie [netwerk interfaces beheren](virtual-network-network-interface.md)als u netwerk interfaces wilt maken, wijzigen of verwijderen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent u https://portal.azure.comen meldt u zich aan met uw Azure-account.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.26 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook `az login` uitvoeren om een verbinding te maken met Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Bestaande netwerk interfaces toevoegen aan een nieuwe virtuele machine

Wanneer u via de portal een virtuele machine maakt, maakt de portal een netwerk interface met de standaard instellingen en koppelt deze aan de VM voor u. U kunt geen bestaande netwerk interfaces toevoegen aan een nieuwe virtuele machine of een virtuele machine met meerdere netwerk interfaces maken met behulp van de Azure Portal. U kunt beide doen met behulp van de CLI of Power shell, maar u moet wel vertrouwd zijn met de [beperkingen](#constraints). Als u een virtuele machine met meerdere netwerk interfaces maakt, moet u ook het besturings systeem configureren om ze goed te gebruiken nadat u de virtuele machine hebt gemaakt. Meer informatie over het configureren van [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) voor meerdere netwerk interfaces.

### <a name="commands"></a>Opdrachten

Voordat u de virtuele machine maakt, maakt u een netwerk interface met behulp van de stappen in [een netwerk interface maken](virtual-network-network-interface.md#create-a-network-interface).

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Een netwerk interface toevoegen aan een bestaande virtuele machine

1. Meld u aan bij Azure Portal.
2. Typ in het zoekvak boven aan de Portal de naam van de virtuele machine waaraan u de netwerk interface wilt toevoegen, of blader naar de virtuele machine door **alle services**en vervolgens **virtuele machines**te selecteren. Nadat u de virtuele machine hebt gevonden, selecteert u deze. De virtuele machine moet ondersteuning bieden voor het aantal netwerk interfaces dat u wilt toevoegen. Zie [grootten voor virtuele Linux-machines in azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [grootten voor virtuele Windows-machines in azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor informatie over het aantal netwerk interfaces dat elke VM-grootte ondersteunt.  
3. Selecteer **overzicht**onder **instellingen**. Selecteer **stoppen**en wacht totdat de **status** van de VM is gewijzigd in **gestopt (toewijzing opgeheven)** .
4. Selecteer **netwerken**, onder **instellingen**.
5. Selecteer **netwerk interface koppelen**. Selecteer in de lijst met netwerk interfaces die momenteel niet aan een andere virtuele machine zijn gekoppeld, het account dat u wilt koppelen.

   >[!NOTE]
   >Voor de netwerk interface die u selecteert, kan geen versneld netwerk zijn ingeschakeld, er kan geen IPv6-adres aan worden toegewezen en het moet zich in hetzelfde virtuele netwerk bevinden als het, dat de netwerk interface bevat die momenteel aan de virtuele machine is gekoppeld.

   Als u geen bestaande netwerk interface hebt, moet u er eerst een maken. Hiertoe selecteert u **netwerk interface maken**. Zie [een netwerk interface maken](virtual-network-network-interface.md#create-a-network-interface)voor meer informatie over het maken van een netwerk interface. Zie voor meer informatie over aanvullende beperkingen bij het toevoegen van netwerk interfaces aan virtuele machines de [beperkingen](#constraints).

6. Selecteer **OK**.
7. Selecteer **overzicht**onder **instellingen**en **Start** de virtuele machine.
8. Configureer het VM-besturings systeem voor het op de juiste manier gebruiken van meerdere netwerk interfaces. Meer informatie over het configureren van [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) voor meerdere netwerk interfaces.

### <a name="commands"></a>Opdrachten
|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ VM NIC add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (verwijzing) of [gedetailleerde stappen](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (verwijzing) of [gedetailleerde stappen](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Netwerk interfaces voor een virtuele machine weer geven

U kunt de netwerk interfaces die momenteel zijn gekoppeld aan een virtuele machine weer geven voor meer informatie over de configuratie van elke netwerk interface en de IP-adressen die zijn toegewezen aan elke netwerk interface. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een account dat is toegewezen aan de rol eigenaar, bijdrager of netwerk Inzender voor uw abonnement. Zie [ingebouwde rollen voor op rollen gebaseerd toegangs beheer voor Azure voor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)meer informatie over het toewijzen van rollen aan accounts.
2. In het vak met de tekst **zoeken resources** boven aan de Azure Portal, typt u **virtuele machines**. Wanneer **virtuele machines** worden weer gegeven in de zoek resultaten, selecteert u deze.
3. Selecteer de naam van de virtuele machine waarvoor u de netwerk interfaces wilt weer geven.
4. Selecteer in de sectie **instellingen** voor de virtuele machine die u hebt geselecteerd, **netwerken**. Zie [netwerk interfaces beheren](virtual-network-network-interface.md)voor meer informatie over de instellingen van de netwerk interface en hoe u deze kunt wijzigen. Zie [IP-adressen van netwerk interfaces beheren](virtual-network-network-interface-addresses.md)voor meer informatie over het toevoegen, wijzigen of verwijderen van IP-adressen die zijn toegewezen aan een netwerk interface.

### <a name="commands"></a>Opdrachten

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Een netwerk interface verwijderen van een virtuele machine

1. Meld u aan bij Azure Portal.
2. Zoek in het zoekvak boven aan de portal naar de naam van de virtuele machine die u wilt verwijderen (ontkoppelen) van de netwerk interface of blader naar de virtuele machine door **alle services**en vervolgens **virtuele machines**te selecteren. Nadat u de virtuele machine hebt gevonden, selecteert u deze.
3. Selecteer **overzicht**, onder **instellingen**en klik vervolgens op **stoppen**. Wacht totdat de **status** van de VM is gewijzigd in **gestopt (toewijzing opgeheven)** .
4. Selecteer **netwerken**, onder **instellingen**.
5. Selecteer **netwerk interface ontkoppelen**. Selecteer in de lijst met netwerk interfaces die momenteel zijn gekoppeld aan de virtuele machine de netwerk interface die u wilt loskoppelen.

   >[!NOTE]
   >Als er slechts één netwerk interface wordt weer gegeven, kunt u deze niet loskoppelen omdat er altijd ten minste één netwerk interface aan een virtuele machine is gekoppeld.
6. Selecteer **OK**.

### <a name="commands"></a>Opdrachten

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (naslag) of [gedetailleerde stappen](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (verwijzing) of [gedetailleerde stappen](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Beperkingen

- Aan een virtuele machine moet ten minste één netwerk interface zijn gekoppeld.
- Een VM kan slechts net zoveel netwerk interfaces als de VM-grootte worden gekoppeld. Zie [grootten voor virtuele Linux-machines in azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [grootten voor virtuele Windows-machines in azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het aantal netwerk interfaces dat elke VM-grootte ondersteunt. Alle grootten ondersteunen ten minste twee netwerk interfaces.
- De netwerk interfaces die u aan een virtuele machine toevoegt, kunnen momenteel niet aan een andere virtuele machine worden gekoppeld. Zie [een netwerk interface maken](virtual-network-network-interface.md#create-a-network-interface)voor meer informatie over het maken van netwerk interfaces.
- In het verleden konden netwerk interfaces alleen worden toegevoegd aan Vm's die ondersteuning voor meerdere netwerk interfaces hebben en zijn gemaakt met ten minste twee netwerk interfaces. U kunt geen netwerk interface toevoegen aan een virtuele machine die is gemaakt met één netwerk interface, zelfs als de VM-grootte meerdere netwerk interfaces ondersteunt. U kunt echter alleen netwerk interfaces van een virtuele machine met ten minste drie netwerk interfaces verwijderen, omdat Vm's die zijn gemaakt met ten minste twee netwerk interfaces, altijd ten minste twee netwerk interfaces hadden. Geen van deze beperkingen zijn van toepassing. U kunt nu een virtuele machine maken met een wille keurig aantal netwerk interfaces (Maxi maal het aantal dat wordt ondersteund door de VM-grootte).
- Standaard wordt de eerste netwerk interface die is gekoppeld aan een virtuele machine gedefinieerd als de *primaire* netwerk interface. Alle andere netwerk interfaces in de VM zijn *secundaire* netwerk interfaces.
- Hoewel u kunt bepalen met welke netwerk interface u uitgaand verkeer hebt verzonden, wordt standaard al het uitgaande verkeer van de virtuele machine verzonden naar het IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerk interface.
- In het verleden moesten alle virtuele machines in dezelfde beschikbaarheidsset een enkele of meerdere netwerk interfaces hebben. Vm's met een wille keurig aantal netwerk interfaces kunnen nu in dezelfde beschikbaarheidsset bestaan, tot het aantal dat wordt ondersteund door de VM-grootte. U kunt alleen een virtuele machine toevoegen aan een beschikbaarheidsset wanneer deze wordt gemaakt. Zie [de beschik baarheid van vm's in azure beheren](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)voor meer informatie over beschikbaarheids sets.
- Hoewel netwerk interfaces in dezelfde VM kunnen worden verbonden met verschillende subnetten binnen een virtueel netwerk, moeten de netwerk interfaces allemaal zijn verbonden met hetzelfde virtuele netwerk.
- U kunt elk IP-adres voor elke IP-configuratie van een primaire of secundaire netwerk interface toevoegen aan een Azure Load Balancer back-end-pool. In het verleden kon alleen het primaire IP-adres voor de primaire netwerk interface worden toegevoegd aan een back-end-pool. Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)voor meer informatie over IP-adressen en configuraties.
- Als u een virtuele machine verwijdert, worden de gekoppelde netwerk interfaces niet verwijderd. Wanneer u een virtuele machine verwijdert, worden de netwerk interfaces losgekoppeld van de virtuele machine. U kunt de netwerk interfaces toevoegen aan verschillende Vm's of verwijderen.
- Net als bij IPv6 kunt u een netwerk interface met versnelde netwerken niet koppelen aan een virtuele machine nadat u deze hebt gemaakt. Als u verder wilt profiteren van versneld netwerken, moet u ook de stappen binnen het VM-besturings systeem volt ooien. Meer informatie over versneld netwerken en andere beperkingen bij het gebruik van het netwerk voor virtuele [Windows](create-vm-accelerated-networking-powershell.md) -of [Linux](create-vm-accelerated-networking-cli.md) -machines.

## <a name="next-steps"></a>Volgende stappen
Als u een virtuele machine met meerdere netwerk interfaces of IP-adressen wilt maken, raadpleegt u de volgende artikelen:

|Taak|Hulpprogramma|
|---|---|
|Een VM met meerdere NIC's maken|[Cli](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Power shell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Eén NIC-VM met meerdere IPv4-adressen maken|[Cli](virtual-network-multiple-ip-addresses-cli.md), [Power shell](virtual-network-multiple-ip-addresses-powershell.md)|
|Een enkele NIC-VM maken met een privé-IPv6-adres (achter een Azure Load Balancer)|[Cli](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [power shell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sjabloon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
