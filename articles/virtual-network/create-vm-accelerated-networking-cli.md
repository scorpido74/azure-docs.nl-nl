---
title: Een Azure VM maken met versnelde netwerken met Azure CLI
description: Meer informatie over het maken van een Virtuele Linux-machine met Accelerated Networking ingeschakeld.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 05f8430efa31b39d49025fb8456108da229d3d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239814"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Een Virtuele Linux-machine maken met versnelde netwerken met Azure CLI

In deze zelfstudie leert u hoe u een Virtuele Linux-machine (VM) maakt met Accelerated Networking. Zie [Een Windows-vm maken met versnelde netwerken](create-vm-accelerated-networking-powershell.md)als u een Windows-vm met versnelde netwerken wilt maken. Versnelde netwerken maakt single root I/O virtualisatie (SR-IOV) naar een VM mogelijk, waardoor de netwerkprestaties aanzienlijk worden verbeterd. Dit krachtige pad omzeilt de host vanaf het gegevenspad en vermindert latentie, jitter en CPU-gebruik, voor gebruik met de meest veeleisende netwerkworkloads op ondersteunde VM-typen. De volgende foto toont communicatie tussen twee VM's met en zonder versnelde netwerken:

![Vergelijking](./media/create-vm-accelerated-networking/accelerated-networking.png)

Zonder versnelde netwerken moet al het netwerkverkeer in en uit de VM de host en de virtuele switch doorkruisen. De virtuele switch biedt alle beleidshandhaving, zoals netwerkbeveiligingsgroepen, toegangscontrolelijsten, isolatie en andere gevirtualiseerde netwerkservices voor netwerkverkeer. Lees voor meer informatie over virtuele switches het [hyper-V-netwerkvirtualisatie en het](https://technet.microsoft.com/library/jj945275.aspx) virtuele switch-artikel.

Bij versnelde netwerken komt netwerkverkeer aan op de netwerkinterface (NIC) van de virtuele machine en wordt het vervolgens doorgestuurd naar de VM. Alle netwerkbeleidsregels die de virtuele switch toepast, worden nu ontladen en toegepast in hardware. Door beleid in hardware toe te passen, kan de NIC netwerkverkeer rechtstreeks doorsturen naar de VM, waarbij de host en de virtuele switch worden omzeild, met behoud van al het beleid dat in de host is toegepast.

De voordelen van versnelde netwerken zijn alleen van toepassing op de VM waarop deze is ingeschakeld. Voor de beste resultaten is het ideaal om deze functie in te schakelen op ten minste twee VM's die zijn aangesloten op hetzelfde Virtual Azure-netwerk (VNet). Wanneer u communiceert via VNets of on-premises verbinding maakt, heeft deze functie een minimale impact op de algehele latentie.

## <a name="benefits"></a>Voordelen
* **Lagere latentie / Hogere pakketten per seconde (pps):** Als u de virtuele switch van het gegevenspad verwijdert, worden de tijdpakketten in de host verwijderd voor beleidsverwerking en wordt het aantal pakketten dat binnen de VM kan worden verwerkt, verhoogd.
* **Verminderde jitter:** Verwerking van virtuele schakelaars is afhankelijk van de hoeveelheid beleid die moet worden toegepast en de werkbelasting van de CPU die de verwerking doet. Als u de beleidshandhaving naar de hardware haalt, wordt die variabiliteit verwijderd door pakketten rechtstreeks aan de VM te leveren, de host naar VM-communicatie te verwijderen en alle software-interrupts en contextswitches.
* **Verminderd CPU-gebruik:** Het omzeilen van de virtuele schakelaar in de host leidt tot minder CPU-gebruik voor het verwerken van netwerkverkeer.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
De volgende distributies worden uit het vak ondersteund vanuit de Azure Gallery: 
* **Ubuntu 14.04 met de linux-azure kernel**
* **Ubuntu 16.04 of hoger** 
* **SLES12 SP3 of hoger** 
* **RHEL 7.4 of hoger**
* **CentOS 7.4 of hoger**
* **CoreOS Linux**
* **Debian "Stretch" met backports kernel**
* **Oracle Linux 7.4 en hoger met Red Hat Compatible Kernel (RHCK)**
* **Oracle Linux 7.5 en hoger met UEK versie 5**
* **FreeBSD 10.4, 11.1 & 12.0**

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren
Accelerated Networking wordt ondersteund op de meeste algemene en voor compute geoptimaliseerde instantieformaten met 2 of meer vCPU's.  Deze ondersteunde series zijn: D/DSv2 en F/Fs

Op gevallen die hyperthreading ondersteunen, wordt Accelerated Networking ondersteund op VM-exemplaren met 4 of meer vCPU's. Ondersteunde series zijn: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms en Ms/Mmsv2.

Zie Linux VM-formaten voor meer informatie over [VM-exemplaren.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="custom-images"></a>Aangepaste installatiekopieën
Als u een aangepaste afbeelding gebruikt en uw afbeelding versnelde netwerken ondersteunt, moet u ervoor zorgen dat u over de vereiste stuurprogramma's beschikt om te werken met Mellanox ConnectX-3 en ConnectX-4 Lx NIC's op Azure.

### <a name="regions"></a>Regio's
Beschikbaar in alle openbare Azure-regio's en Azure Government Clouds.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Versneld netwerken inschakelen op een draaiende VM
Een ondersteunde VM-grootte zonder versnelde netwerk ingeschakeld kan alleen de functie ingeschakeld wanneer deze is gestopt en deallocated.  
### <a name="deployment-through-azure-resource-manager"></a>Implementatie via Azure Resource Manager
Virtuele machines (klassiek) kunnen niet worden geïmplementeerd met Accelerated Networking.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Een Linux-vm maken met Azure Accelerated Networking
## <a name="portal-creation"></a>Portalcreatie
Hoewel dit artikel stappen bevat om een virtuele machine met versnelde netwerken te maken met behulp van de Azure CLI, u ook [een virtuele machine maken met versnelde netwerken met behulp van de Azure-portal.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Kies bij het maken van een virtuele machine in de portal in **het blad Een virtueel machineblad maken** het tabblad **Netwerken.**  In dit tabblad is er een optie voor **Versnelde netwerken.**  Als u een [ondersteund besturingssysteem](#supported-operating-systems) en [vm-grootte](#supported-vm-instances)hebt gekozen, wordt deze optie automatisch ingevuld tot 'Aan'.  Zo niet, dan zal het de optie "Uit" voor Versnelde netwerken vullen en de gebruiker een reden geven waarom deze niet is ingeschakeld.   

* *Let op:* Alleen ondersteunde besturingssystemen kunnen via de portal worden ingeschakeld.  Als u een aangepaste afbeelding gebruikt en uw afbeelding versnelde netwerken ondersteunt, maakt u uw VM met CLI of PowerShell. 

Nadat de virtuele machine is gemaakt, u bevestigen dat Versnelde netwerken is ingeschakeld door de instructies in het [bevestigen dat versnelde netwerken is ingeschakeld.](#confirm-that-accelerated-networking-is-enabled)

## <a name="cli-creation"></a>CLI-creatie
### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Installeer de nieuwste [Azure CLI](/cli/azure/install-azure-cli) en meld u aan bij een Azure-account met [az-aanmelding.](/cli/azure/reference-index) Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeelden van parameternamen waren *myResourceGroup,* *myNic*en *myVm*.

Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *centralus-locatie* ge:

```azurecli
az group create --name myResourceGroup --location centralus
```

Selecteer een ondersteunde Linux-regio die wordt vermeld in [Linux versnelde netwerken.](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVnet* met één subnet gemaakt:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken
Maak een netwerkbeveiligingsgroep met [het AZ-netwerk nsg maken](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNetworkSecurityGroup* gemaakt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

De netwerkbeveiligingsgroep bevat verschillende standaardregels, waarvan er één alle binnenkomende toegang vanaf het internet uitschakelt. Open een poort om SSH toegang te geven tot de virtuele machine met [de NSG-regel van het AZ-netwerk:](/cli/azure/network/nsg/rule)

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Een netwerkinterface maken met versnelde netwerken

Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). Een openbaar IP-adres is niet vereist als u niet van plan bent om toegang te krijgen tot de virtuele machine vanaf het internet, maar om de stappen in dit artikel te voltooien, is dit vereist.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Maak een netwerkinterface met [az-netwerknic create](/cli/azure/network/nic) met versnelde netwerkingeschakeld. In het volgende voorbeeld wordt een netwerkinterface met de naam *myNic* in het *mySubnet-subnet* van het virtuele *myVnet-netwerk* geopperd en wordt de netwerkbeveiligingsgroep *myNetworkSecurityGroup* gekoppeld aan de netwerkinterface:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Een VM maken en de NIC koppelen
Wanneer u de vm maakt, geeft `--nics`u de NIC op waarmee u hebt gemaakt . Selecteer een grootte en distributie die wordt vermeld in [Linux versnelde netwerken.](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) 

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een VM met de naam *myVM gemaakt* met de UbuntuLTS-afbeelding en een grootte die accelerated networking ondersteunt *(Standard_DS4_v2):*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Zie [Linux VM-formaten](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een lijst met alle VM-formaten en -kenmerken.

Zodra de VM is gemaakt, wordt de uitvoer die vergelijkbaar is met de volgende voorbeelduitvoer geretourneerd. Let op het **openbare IP-adres**. Dit adres wordt gebruikt om toegang te krijgen tot de VM in volgende stappen.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Bevestigen dat versnelde netwerken is ingeschakeld

Gebruik de volgende opdracht om voor de VM een SSH-sessie te maken. Vervang `<your-public-ip-address>` het openbare IP-adres dat is toegewezen aan de virtuele machine die `--admin-username` u hebt gemaakt en vervang *azureuser* als u een andere waarde hebt gebruikt voor het maken van de vm.

```bash
ssh azureuser@<your-public-ip-address>
```

Voer vanuit de `uname -r` Bash-shell in en bevestig dat de kernelversie een van de volgende versies is, of meer:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Controleer of het Mellanox VF-apparaat `lspci` met de opdracht aan de VM wordt blootgesteld. De geretourneerde uitvoer is vergelijkbaar met de volgende uitvoer:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Controleer met de `ethtool -S eth0 | grep vf_` opdracht op activiteit op de VF (virtuele functie). Als u uitvoer ontvangt die vergelijkbaar is met de volgende voorbeelduitvoer, is versnelde netwerken ingeschakeld en werkt het.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Versnelde netwerken is nu ingeschakeld voor uw VM.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Omgaan met dynamische binding en intrekking van virtuele functie 
Toepassingen moeten over de synthetische NIC lopen die in VM wordt weergegeven. Als de toepassing rechtstreeks over de VF NIC loopt, ontvangt deze niet **alle** pakketten die zijn bestemd voor de VM, omdat sommige pakketten via de synthetische interface worden weergegeven.
Als u een toepassing via de synthetische NIC uitvoert, garandeert dit dat de toepassing **alle** pakketten ontvangt die voor het land zijn bestemd. Het zorgt er ook voor dat de toepassing blijft draaien, zelfs als de VF wordt ingetrokken wanneer de host wordt onderhouden. Toepassingen die bindend zijn voor de synthetische NIC is een **verplichte** vereiste voor alle toepassingen die gebruik maken van **Accelerated Networking.**

## <a name="enable-accelerated-networking-on-existing-vms"></a>Versneld netwerken inschakelen op bestaande VM's
Als u een VM zonder versnelde netwerken hebt gemaakt, is het mogelijk om deze functie in te schakelen op een bestaande virtuele machine.  De VM moet Versnelde netwerken ondersteunen door te voldoen aan de volgende vereisten die hierboven zijn beschreven:

* De VM moet een ondersteunde grootte hebben voor Accelerated Networking
* De VM moet een ondersteunde Azure Gallery-afbeelding zijn (en kernelversie voor Linux)
* Alle VM's in een beschikbaarheidsset of VMSS moeten worden gestopt/deallocated voordat accelerated networking op een NIC wordt ingeschakeld

### <a name="individual-vms--vms-in-an-availability-set"></a>Afzonderlijke VM's & VM's in een beschikbaarheidsset
Eerste stop/deallocate de VM of, als een beschikbaarheidsset, alle VM's in de set:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Belangrijk, let op: als uw VM afzonderlijk is gemaakt, zonder beschikbaarheidsset, hoeft u alleen de afzonderlijke VM te stoppen/detoewijzen om versneld netwerken in te schakelen.  Als uw VM is gemaakt met een beschikbaarheidsset, moeten alle VM's in de beschikbaarheidsset worden gestopt/deallocated voordat versnelde netwerken op een van de NIC's worden ingeschakeld. 

Als u eenmaal gestopt bent, schakelt u Versneld netwerken in op de nic van uw vm:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Start uw VM opnieuw of, als u in een beschikbaarheidsset alle VM's in de set hebt en bevestig dat Versnelde netwerken is ingeschakeld: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS is iets anders, maar volgt dezelfde workflow.  Stop eerst de VM's:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Zodra de VM's zijn gestopt, werkt u de eigenschap Accelerated Networking bij onder de netwerkinterface:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Houd er rekening mee dat een VMSS VM-upgrades heeft die updates toepassen met behulp van drie verschillende instellingen, automatisch, rollen d.m.v. handmatig.  In deze instructies is het beleid ingesteld op automatisch, zodat de VMSS de wijzigingen onmiddellijk na het opnieuw opstarten oppakt.  Om het automatisch in te stellen, zodat de wijzigingen onmiddellijk worden opgepikt: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Start ten slotte het VMSS opnieuw:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Zodra u opnieuw start, wacht u tot de upgrades zijn voltooid, maar zodra deze is voltooid, wordt de VF in de VM weergegeven.  (Zorg ervoor dat u een ondersteund besturingssysteem en vm-formaat gebruikt.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Het formaat van bestaande VM's wijzigen met accelerated networking

VM's met Accelerated Networking ingeschakeld kunnen alleen worden aangepast aan VM's die Accelerated Networking ondersteunen.  

Een VM met accelerated networking ingeschakeld kan niet worden aangepast aan een VM-exemplaar dat geen ondersteuning biedt voor Versnelde netwerken met behulp van de bewerking Formaat wijzigen.  In plaats daarvan u het formaat van een van deze VM's wijzigen: 

* Stop/deallocate de VM of als in een beschikbaarheidsset/VMSS alle VM's in de set/VMSS stoppen/detoewijzen.
* Versnelde netwerken moeten worden uitgeschakeld op de NIC van de VM of als in een beschikbaarheidsset/VMSS alle VM's in de set/VMSS worden ingeschakeld.
* Zodra Accelerated Networking is uitgeschakeld, kan de VM/availability set/VMSS worden verplaatst naar een nieuwe grootte die geen ondersteuning biedt voor Accelerated Networking en opnieuw wordt gestart.  

