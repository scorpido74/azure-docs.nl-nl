---
title: Een virtuele machine (klassiek) of Cloud Services rolinstantie verplaatsen naar een ander subnet-Azure PowerShell | Microsoft Docs
description: Meer informatie over het verplaatsen van Vm's (klassiek) en het Cloud Services van rolinstanties naar een ander subnet met behulp van Power shell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 275d59a7bddd8b2b609169218afcd15e9a0ce913
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058377"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Een virtuele machine (klassiek) of Cloud Services rolinstantie naar een ander subnet verplaatsen met behulp van Power shell
U kunt Power shell gebruiken om uw virtuele machines (klassiek) van het ene subnet naar het andere te verplaatsen in hetzelfde virtuele netwerk (VNet). U kunt rolinstanties verplaatsen door het CSCFG-bestand te bewerken in plaats van Power shell te gebruiken.

> [!NOTE]
> In dit artikel wordt uitgelegd hoe u Vm's die worden geïmplementeerd via het klassieke implementatie model, kunt verplaatsen.
> 
> 

Waarom Vm's naar een ander subnet verplaatsen? Migratie van subnetten is handig wanneer het oudere subnet te klein is en niet kan worden uitgebreid vanwege bestaande actieve Vm's in dat subnet. In dat geval kunt u een nieuw, groter subnet maken en de virtuele machines migreren naar het nieuwe subnet. Daarna kunt u het oude lege subnet verwijderen nadat de migratie is voltooid.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Een virtuele machine verplaatsen naar een ander subnet
Als u een virtuele machine wilt verplaatsen, voert u de Power shell-cmdlet Set-AzureSubnet uit met behulp van het voor beeld hieronder als sjabloon. In het onderstaande voor beeld gaan we TestVM van het huidige subnet naar subnet 2 verplaatsen. Zorg ervoor dat u het voor beeld bewerkt om uw omgeving weer te geven. Houd er rekening mee dat wanneer u de cmdlet Update-AzureVM uitvoert als onderdeel van een procedure, de virtuele machine opnieuw wordt opgestart als onderdeel van het update proces.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Als u een statisch intern particulier IP-adres voor uw virtuele machine hebt opgegeven, moet u die instelling wissen voordat u de virtuele machine naar een nieuw subnet kunt verplaatsen. Gebruik in dat geval het volgende:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Een rolinstantie naar een ander subnet verplaatsen
Als u een rolinstantie wilt verplaatsen, bewerkt u het CSCFG-bestand. In het onderstaande voor beeld gaan we ' Role0 ' in het *VNETName* van het huidige subnet verplaatsen naar *subnet-2*. Omdat de rolinstantie al is geïmplementeerd, wijzigt u alleen de Subnetnaam = subnet-2. Zorg ervoor dat u het voor beeld bewerkt om uw omgeving weer te geven.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
