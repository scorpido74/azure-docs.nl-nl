---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 820a6a4da9f5c466e694f247d09393474d8464ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175884"
---
U kunt verbinding maken met een VM die op uw VNet is geïmplementeerd door een verbinding met extern bureaublad te maken voor uw VM. De beste manier om eerst te controleren of u verbinding met uw VM kunt maken is door verbinding te maken met behulp van het privé-IP-adres in plaats van de computernaam. Op die manier test u of u verbinding kunt maken, niet of naamomzetting correct is geconfigureerd.

1. Zoek het privé-IP-adres. U kunt op meerdere manieren het privé-IP-adres van een VM achterhalen. Hieronder ziet u de benodigde stappen voor Azure Portal en PowerShell.

   - Azure Portal: vind uw virtuele machine in Azure Portal. Bekijk de eigenschappen voor de VM. Het privé-IP-adres wordt vermeld.

   - PowerShell: gebruik het voorbeeld om een lijst met VM's en privé-IP-adressen uit uw resourcegroepen weer te geven. U hoeft het voorbeeld niet te wijzigen voordat u het gebruikt.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. Controleer of u met uw VNet bent verbonden met behulp van de VPN-verbinding.
3. Open **verbinding met extern bureaublad** door ' RDP ' of ' verbinding met extern bureaublad ' te typen in het zoekvak op de taak balk en selecteer vervolgens verbinding met extern bureaublad. U kunt Verbinding met extern bureaublad ook openen met behulp van de opdracht 'mstsc' in PowerShell. 
4. Voer het privé-IP-adres van de VM in Verbinding met extern bureaublad in. U kunt op Opties weergeven klikken om aanvullende instellingen aan te passen en vervolgens verbinding maken.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Problemen met de RDP-verbinding met een VM oplossen

Als u problemen ondervindt bij het verbinding maken met een virtuele machine via de VPN-verbinding, controleert u het volgende:

- Controleer of uw VPN-verbinding tot stand is gebracht.
- Controleer of u verbinding maakt met het privé-IP-adres voor de VM.
- Als u verbinding met de VM kunt maken met behulp van het privé-IP-adres, maar niet met de computernaam, controleert u of DNS correct is geconfigureerd. Zie [Naamomzetting voor VM's](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voor meer informatie over de werking van naamomzetting voor VM's.
- Zie [Problemen met Extern-bureaubladverbindingen met een VM oplossen](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md) voor meer informatie over Extern-bureaubladverbindingen.