---
title: Extern bureaublad-services of het beheerderswachtwoord opnieuw instellen in een Windows-vm | Microsoft Documenten
description: Meer informatie over het opnieuw instellen van een accountwachtwoord of Extern bureaublad-services op een Windows-vm met behulp van de Azure-portal of Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 580ec443dc087f270e30856c336a5699bbf1ae71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058446"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Extern bureaublad-services of het beheerderswachtwoord ervan opnieuw instellen in een Windows-vm
Als u geen verbinding maken met een virtuele Windows-machine (VM), u het wachtwoord van uw lokale beheerder opnieuw instellen of de configuratie van Extern bureaublad-services opnieuw instellen (niet ondersteund op Windows-domeincontrollers). Voor het opnieuw instellen van het wachtwoord gebruikt u de Azure-portal of de VM-toegangsextensie in Azure PowerShell. Wanneer u zich bij de VM hebt aangemeld, stelt u het wachtwoord van die lokale beheerder opnieuw in.  
Als u PowerShell gebruikt, controleert u of u de [nieuwste PowerShell-module](/powershell/azure/overview) hebt geïnstalleerd en geconfigureerd en bent aangemeld bij uw Azure-abonnement. U kunt [deze stappen ook uitvoeren voor virtuele machines die zijn gemaakt met het klassieke implementatiemodel](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

U kunt Extern bureaublad-services en de referenties op de volgende manieren opnieuw instellen:

- [Opnieuw instellen met de Azure-portal](#reset-by-using-the-azure-portal)

- [Opnieuw instellen met de VMAccess-extensie en PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Opnieuw instellen met de Azure-portal

Meld u eerst aan bij de [Azure-portal](https://portal.azure.com) en selecteer **virtuele machines** in het linkermenu. 

### <a name="reset-the-local-administrator-account-password"></a>**Het wachtwoord van het lokale beheerdersaccount opnieuw instellen**

1. Selecteer uw Windows-vm en selecteer **vervolgens Wachtwoord opnieuw instellen** onder Ondersteuning + **Probleemoplossing**. Het **venster Wachtwoord opnieuw instellen** wordt weergegeven.

2. Selecteer **Wachtwoord opnieuw instellen,** voer een gebruikersnaam en een wachtwoord in en selecteer **Bijwerken**. 

3. Probeer opnieuw verbinding te maken met uw virtuele machine.

### <a name="reset-the-remote-desktop-services-configuration"></a>**De configuratie van Extern bureaublad-services opnieuw instellen**

Met dit proces schakelt u de Extern bureaublad-service in de VM in en maakt u een firewallregel voor de standaard RDP-poort 3389.

1. Selecteer uw Windows-vm en selecteer **vervolgens Wachtwoord opnieuw instellen** onder Ondersteuning + **Probleemoplossing**. Het **venster Wachtwoord opnieuw instellen** wordt weergegeven. 

2. Selecteer **Alleen configuratie opnieuw instellen** en selecteer **Bijwerken**. 

3. Probeer opnieuw verbinding te maken met uw virtuele machine.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Opnieuw instellen met de VMAccess-extensie en PowerShell

Zorg er eerst voor dat u de [nieuwste PowerShell-module hebt geïnstalleerd en geconfigureerd](/powershell/azure/overview) en bent aangemeld bij uw Azure-abonnement met behulp van de cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)

### <a name="reset-the-local-administrator-account-password"></a>**Het wachtwoord van het lokale beheerdersaccount opnieuw instellen**

- Stel het beheerderswachtwoord of de gebruikersnaam opnieuw in met de PowerDlet [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell. De `typeHandlerVersion` instelling moet 2.0 of hoger zijn, omdat versie 1 is afgeschaft. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Als u een andere naam invoert dan het huidige lokale beheerdersaccount op uw vm, voegt de VMAccess-extensie een lokaal beheerdersaccount met die naam toe en wijst u uw opgegeven wachtwoord toe aan dat account. Als het lokale beheerdersaccount op uw VM bestaat, wordt het wachtwoord opnieuw ingesteld door de VMAccess-extensie. Als het account is uitgeschakeld, wordt het account ingeschakeld door de VMAccess-extensie.

### <a name="reset-the-remote-desktop-services-configuration"></a>**De configuratie van Extern bureaublad-services opnieuw instellen**

1. Stel externe toegang tot uw VM opnieuw in met de [PowerShell-cmdlet set-AzVMAccessExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) In het volgende voorbeeld wordt `myVMAccess` de toegangsextensie met de naam van de VM met de naam `myVM` in de `myResourceGroup` resourcegroep opnieuw ingesteld:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > Op elk moment kan een VM slechts één VM-toegangsagent hebben. Als u de eigenschappen van `-ForceRerun` de VM-agent wilt instellen, gebruikt u de optie. Wanneer u `-ForceRerun`deze gebruikt, moet u ervoor zorgen dat u dezelfde naam gebruikt voor de VM-toegangsagent die u mogelijk hebt gebruikt in eerdere opdrachten.

1. Zie [Problemen met Verbindingen met extern bureaublad met een windows-gebaseerde virtuele machine op](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)afstand. Als u de verbinding met de Windows-domeincontroller verliest, moet u deze herstellen vanuit een back-up van een domeincontroller.

## <a name="next-steps"></a>Volgende stappen

- Als de Azure VM-toegangsextensie niet reageert en u het wachtwoord niet opnieuw instellen, u [het lokale Windows-wachtwoord offline opnieuw instellen.](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Deze methode is geavanceerder en vereist dat u de virtuele harde schijf van de problematische VM aansluit op een andere VM. Volg eerst de stappen die in dit artikel zijn gedocumenteerd en probeer de methode voor het opnieuw instellen van offline wachtwoorden alleen als deze stappen niet werken.

- [Meer informatie over Azure VM-extensies en -functies](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Maak verbinding met een virtuele Azure-machine met RDP of SSH.](https://msdn.microsoft.com/library/azure/dn535788.aspx)

- [Problemen met Extern bureaublad-verbindingen met een windows-gebaseerde virtuele azure-machine](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)oplossen .

