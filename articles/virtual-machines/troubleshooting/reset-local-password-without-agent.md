---
title: Een lokaal Windows-wachtwoord opnieuw instellen zonder Azure-agent | Microsoft Documenten
description: Het wachtwoord van een lokaal Windows-gebruikersaccount opnieuw instellen wanneer de Azure-gastagent niet is geïnstalleerd of werkt op een vm
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: becbf88aeda164f7d916cbc1f1ace89262cc1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921620"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Offline een lokaal Windows-wachtwoord opnieuw instellen voor een virtuele Azure-machine
U het lokale Windows-wachtwoord van een VM in Azure opnieuw instellen met behulp van de [Azure-portal of Azure PowerShell,](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) mits de Azure-gastagent is geïnstalleerd. Deze methode is de primaire manier om een wachtwoord opnieuw in te stellen voor een Azure VM. Als u problemen ondervindt met de Azure-gastagent die niet reageert of niet wordt geïnstalleerd nadat u een aangepaste afbeelding hebt geüpload, u een Windows-wachtwoord handmatig opnieuw instellen. In dit artikel wordt beschreven hoe u een lokaal accountwachtwoord opnieuw instellen door de virtuele schijf van het bronbesturingssysteem aan een andere VM te koppelen. De stappen die in dit artikel worden beschreven, zijn niet van toepassing op Windows-domeincontrollers. 

> [!WARNING]
> Gebruik deze methode alleen als laatste redmiddel. Probeer altijd eerst een wachtwoord opnieuw in te stellen via de [Azure-portal of Azure PowerShell.](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="overview-of-the-process"></a>Overzicht van het proces
De belangrijkste stappen voor het uitvoeren van een lokale wachtwoordreset voor een Windows-vm in Azure wanneer er geen toegang is tot de Azure-gastagent, zijn als volgt:

1. Stop de betreffende VM.
1. Maak een momentopname voor de OS-schijf van de VM.
1. Maak een kopie van de OS-schijf van de momentopname.
1. Voeg de gekopieerde osschijf vast aan een andere Windows-vm en maak vervolgens een aantal config-bestanden op de schijf. De bestanden helpen u om het wachtwoord opnieuw in te stellen.
1. Maak de gekopieerde osschijf los en los van de VM voor het oplossen van problemen.
1. Wissel de OS-schijf in voor de betreffende VM.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Gedetailleerde stappen voor de VM met implementatie van Resource Manager

> [!NOTE]
> De stappen zijn niet van toepassing op Windows-domeincontrollers. Het werkt alleen op standalone server of een server die lid is van een domein.

Probeer altijd een wachtwoord opnieuw in te stellen via de [Azure-portal of Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voordat u de volgende stappen probeert. Zorg ervoor dat u een back-up van uw VM hebt voordat u begint.

1. Maak een momentopname voor de OS-schijf van de betreffende VM, maak een schijf van de momentopname en voeg de schijf toe aan een probleemoplossingsvm. Zie [Problemen met een Windows-vm oplossen door de OS-schijf aan een herstel-vm te koppelen met behulp van de Azure-portal](troubleshoot-recovery-disks-portal-windows.md)voor meer informatie.
2. Maak verbinding met de VM voor het oplossen van problemen met Extern bureaublad.
3. Maak `gpt.ini` `\Windows\System32\GroupPolicy` in op de bron VM's drive (als gpt.ini bestaat, hernoemen naar gpt.ini.bak):
   
   > [!WARNING]
   > Zorg ervoor dat u niet per ongeluk de volgende bestanden maakt in C:\Windows, het station voor het besturingssysteem voor de vm voor het oplossen van problemen. Maak de volgende bestanden in het station van het besturingssysteem voor uw bron-vm die is gekoppeld als een gegevensschijf.
   
   * Voeg de volgende `gpt.ini` regels toe aan het bestand dat u hebt gemaakt:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Maak gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. Maak `scripts.ini` `\Windows\System32\GroupPolicy\Machines\Scripts\`in . Zorg ervoor dat verborgen mappen worden weergegeven. Maak indien nodig `Machine` `Scripts` de of mappen.
   
   * Voeg de volgende `scripts.ini` regels toe aan het bestand dat u hebt gemaakt:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts.ini maken](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. Maak `FixAzureVM.cmd` `\Windows\System32` in met de volgende `<username>` `<newpassword>` inhoud, vervangen en met uw eigen waarden:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd maken](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    U moet voldoen aan de vereiste vereisten voor de complexiteit van het wachtwoord voor uw vm bij het definiëren van het nieuwe wachtwoord.

6. Maak in Azure-portal de schijf los van de VM voor het oplossen van problemen.

7. [Wijzig de osschijf voor de betreffende VM](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Nadat de nieuwe virtuele machine is uitgevoerd, maakt u verbinding met `FixAzureVM.cmd` de vm met Extern bureaublad met het nieuwe wachtwoord dat u in het script hebt opgegeven.

9. Verwijder de volgende bestanden om de omgeving op te schonen, van uw externe sessie tot de nieuwe virtuele machine:
    
    * Van %windir%\System32
      * FixAzureVM.cmd verwijderen
    * Van %windir%\System32\GroupPolicy\Machine\Scripts
      * scripts verwijderen.ini
    * Van %windir%\System32\GroupPolicy
      * verwijderen gpt.ini (als gpt.ini bestond vóór, en u omgedoopt tot gpt.ini.bak, hernoemen van de .bak bestand terug naar gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Gedetailleerde stappen voor Klassieke VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> De stappen zijn niet van toepassing op Windows-domeincontrollers. Het werkt alleen op standalone server of een server die lid is van een domein.

Probeer altijd een wachtwoord opnieuw in te stellen via de [Azure-portal of Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) voordat u de volgende stappen probeert. Zorg ervoor dat u een back-up van uw VM hebt voordat u begint. 

1. Verwijder de betreffende VM in Azure-portal. Als u de VM verwijdert, worden alleen de metagegevens, de verwijzing van de VM binnen Azure, verwijderd. De virtuele schijven blijven behouden wanneer de VM wordt verwijderd:
   
   * Selecteer de VM in de Azure-portal en klik op *Verwijderen:*
     
     ![Bestaande VM verwijderen](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Voeg de besturingssysteemschijf van de bron-VM toe aan de VM voor het oplossen van problemen. De VM voor het oplossen van problemen moet zich in `West US`dezelfde regio bevinden als de besturingssysteemschijf van de bron-VM (zoals):
   
   1. Selecteer de VM voor probleemoplossing in de Azure-portal. Klik op Bestaande*koppelen aan* *schijven:* | 
     
      ![Bestaande schijf koppelen](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Selecteer *VHD-bestand* en selecteer vervolgens het opslagaccount met uw bron-VM:
     
      ![Opslagaccount selecteren](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Schakel het selectievakje gemarkeerd met *klassieke opslagaccounts weergeven*in en selecteer vervolgens de broncontainer. De broncontainer is meestal *vhds:*
     
      ![Opslagcontainer selecteren](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Opslagcontainer selecteren](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Selecteer de vhd-besturingssysteem om toe te voegen. Klik *op Selecteren* om het proces te voltooien:
     
      ![Virtuele bronschijf selecteren](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Klik op Ok om de schijf toe te voegen

      ![Bestaande schijf koppelen](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Maak verbinding met de VM voor het oplossen van problemen met Extern bureaublad en zorg ervoor dat de be-schijf van de bron-vm zichtbaar is:

   1. Selecteer de VM voor probleemoplossing in de Azure-portal en klik op *Verbinden*.

   2. Open het RDP-bestand dat wordt gedownload. Voer de gebruikersnaam en het wachtwoord van de VM voor het oplossen van problemen in.

   3. Zoek in Verkenner naar de gegevensschijf die u hebt gekoppeld. Als de VHD van de bron-VM de enige gegevensschijf is die is gekoppeld aan de VM voor het oplossen van problemen, moet het f: station zijn:
     
      ![Gekoppelde gegevensschijf weergeven](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Maak `gpt.ini` `\Windows\System32\GroupPolicy` in op het station van `gpt.ini` de bron-VM (indien aanwezig, hernoem de naam in): `gpt.ini.bak`
   
   > [!WARNING]
   > Zorg ervoor dat u niet per `C:\Windows`ongeluk de volgende bestanden maakt in het station van het besturingssysteem voor de vm voor het oplossen van problemen. Maak de volgende bestanden in het station van het besturingssysteem voor uw bron-vm die is gekoppeld als een gegevensschijf.
   
   * Voeg de volgende `gpt.ini` regels toe aan het bestand dat u hebt gemaakt:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Maak gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Maak `scripts.ini` `\Windows\System32\GroupPolicy\Machines\Scripts\`in . Zorg ervoor dat verborgen mappen worden weergegeven. Maak indien nodig `Machine` `Scripts` de of mappen.
   
   * Voeg de volgende `scripts.ini` regels toe aan het bestand dat u hebt gemaakt:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts.ini maken](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. Maak `FixAzureVM.cmd` `\Windows\System32` in met de volgende `<username>` `<newpassword>` inhoud, vervangen en met uw eigen waarden:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd maken](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    U moet voldoen aan de vereiste vereisten voor de complexiteit van het wachtwoord voor uw vm bij het definiëren van het nieuwe wachtwoord.

7. Maak in Azure-portal de schijf los van de VM voor het oplossen van problemen:
   
   1. Selecteer de VM voor probleemoplossing in de Azure-portal en klik op *Schijven*.
   
   2. Selecteer de gegevensschijf die in stap 2 is gekoppeld, klik op **Los maken**en klik vervolgens op **OK**.

     ![Schijf loskoppelen](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Schijf loskoppelen](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Maak een VM op de osschijf van de bron-VM:
   
     ![Een VM maken op basis van sjabloon](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Een VM maken op basis van sjabloon](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Een VM maken op basis van sjabloon](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Voltooi de virtuele machine-ervaring maken

1. Nadat de nieuwe virtuele machine is uitgevoerd, maakt u verbinding met `FixAzureVM.cmd` de vm met Extern bureaublad met het nieuwe wachtwoord dat u in het script hebt opgegeven.

2. Verwijder de volgende bestanden om de omgeving op te schonen, van uw externe sessie tot de nieuwe virtuele machine:
    
    * Van`%windir%\System32`
      * Verwijderen`FixAzureVM.cmd`
    * Van`%windir%\System32\GroupPolicy\Machine\Scripts`
      * Verwijderen`scripts.ini`
    * Van`%windir%\System32\GroupPolicy`
      * verwijderen `gpt.ini` (indien `gpt.ini` eerder bestond, en `gpt.ini.bak`u hebt `.bak` het hernoemd naar , wijzigt u de naam van het bestand terug naar `gpt.ini`)

## <a name="next-steps"></a>Volgende stappen
Zie de [handleiding voor probleemoplossing](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor extern bureaublad als u nog steeds geen verbinding maken via Extern bureaublad. In [de gedetailleerde handleiding voor probleemoplossing](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor rdp wordt gekeken naar probleemoplossingsmethoden in plaats van naar specifieke stappen. U ook [een Azure-ondersteuningsaanvraag](https://azure.microsoft.com/support/options/) voor praktische ondersteuning openen.
