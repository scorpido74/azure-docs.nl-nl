---
title: Toegangs beheer lijsten voor Azure endpoint beheren | Power shell | Klassiek | Microsoft Docs
description: Meer informatie over het beheren van Acl's met Power shell
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 4fe8349863c16a15886f9f1d33056f0bbfec31f2
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056756"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Toegangs beheer lijsten voor eind punten beheren met Power shell in het klassieke implementatie model
U kunt netwerk Access Control lijsten (Acl's) voor eind punten maken en beheren met Azure PowerShell of in de Beheerportal. In dit onderwerp vindt u procedures voor ACL'S-veelvoorkomende taken die u kunt uitvoeren met behulp van Power shell. Zie [Azure Management cmdlets](https://go.microsoft.com/fwlink/?LinkId=317721)(Engelstalig) voor een lijst met Azure PowerShell-cmdlets. Zie [Wat is een Network Access Control List (ACL)?](virtual-networks-acl.md)voor meer informatie over acl's. Zie [eind punten instellen voor een virtuele machine](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)als u uw acl's wilt beheren met behulp van de Beheerportal.

## <a name="manage-network-acls-by-using-azure-powershell"></a>Netwerk-Acl's beheren met behulp van Azure PowerShell
U kunt Azure PowerShell-cmdlets gebruiken om netwerk Access Control lijsten (Acl's) te maken, te verwijderen en te configureren (set). Er zijn enkele voor beelden van een aantal manieren waarop u een ACL kunt configureren met behulp van Power shell.

Als u een volledige lijst van de Power shell-cmdlets voor ACL'S wilt ophalen, kunt u een van de volgende opties gebruiken:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Een netwerk toegangs beheer lijst maken met regels die toegang tot een extern subnet toestaan
In het onderstaande voor beeld ziet u een manier om een nieuwe ACL te maken die regels bevat. Deze ACL wordt vervolgens toegepast op een eind punt van een virtuele machine. Met de ACL-regels in het onderstaande voor beeld is toegang vanaf een extern subnet toegestaan. Als u een nieuwe netwerk toegangs beheer lijst wilt maken met regels voor het toestaan van een extern subnet, opent u een Azure PowerShell ISE. Kopieer en plak het onderstaande script, Configureer het script met uw eigen waarden en voer het script uit.

1. Maak het nieuwe netwerk toegangs beheer lijst object.
   
        $acl1 = New-AzureAclConfig
2. Stel een regel in waarmee toegang vanuit een extern subnet wordt toegestaan. In het onderstaande voor beeld stelt u regel *100* (met prioriteit boven regel 200 en hoger) in om het externe subnet *10.0.0.0/8* toegang te geven tot het eind punt van de virtuele machine. Vervang de waarden door uw eigen configuratie vereisten. De naam "share point-ACL configureren" moet worden vervangen door de beschrijvende naam die u wilt aanroepen van deze regel.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Herhaal voor aanvullende regels de cmdlet en vervang de waarden door uw eigen configuratie vereisten. Zorg ervoor dat u de regel nummer volgorde wijzigt om de volg orde te bepalen waarin u de regels wilt Toep assen. Het lagere regel nummer heeft voor rang op het hogere nummer.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Vervolgens kunt u een nieuw eind punt maken (toevoegen) of de ACL instellen voor een bestaand eind punt (set). In dit voor beeld voegen we een nieuw eind punt van de virtuele machine met de naam ' Web ' toe en werken we het eind punt van de virtuele machine bij met de ACL-instellingen.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Combi neer vervolgens de cmdlets en voer het script uit. In dit voor beeld ziet de gecombineerde cmdlets er als volgt uit:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Een netwerk toegangs beheer lijst regel verwijderen waarmee toegang vanuit een extern subnet wordt toegestaan
In het onderstaande voor beeld ziet u een manier om een netwerk toegangs beheer lijst regel te verwijderen.  Als u een netwerk toegangs beheer lijst regel met regels voor toestaan voor een extern subnet wilt verwijderen, opent u een Azure PowerShell ISE. Kopieer en plak het onderstaande script, Configureer het script met uw eigen waarden en voer het script uit.

1. De eerste stap is het ophalen van het netwerk toegangs beheer lijst object voor het eind punt van de virtuele machine. Vervolgens verwijdert u de ACL-regel. In dit geval wordt het door de regel-ID verwijderd. Hiermee wordt alleen de regel-ID 0 uit de ACL verwijderd. Hiermee wordt het ACL-object niet uit het eind punt van de virtuele machine verwijderd.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Vervolgens moet u het netwerk toegangs beheer lijst object Toep assen op het eind punt van de virtuele machine en de virtuele machine bijwerken.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Een netwerk-ACL verwijderen uit een eind punt van een virtuele machine
In bepaalde scenario's wilt u mogelijk een netwerk toegangs beheer lijst object verwijderen uit het eind punt van een virtuele machine. Open hiervoor een Azure PowerShell ISE. Kopieer en plak het onderstaande script, Configureer het script met uw eigen waarden en voer het script uit.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Volgende stappen
[Wat is een netwerk Access Control lijst (ACL)?](virtual-networks-acl.md)

