---
title: Een lokaal Windows-wacht woord opnieuw instellen zonder Azure-agent | Microsoft Docs
description: Het wacht woord van een lokale Windows-gebruikers account opnieuw instellen wanneer de Azure-gast agent niet is geïnstalleerd of werkt op een VM
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77921620"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Offline een lokaal Windows-wachtwoord opnieuw instellen voor een virtuele Azure-machine
U kunt het lokale Windows-wacht woord van een virtuele machine in azure opnieuw instellen met behulp van de [Azure portal of Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , op voor waarde dat de Azure-gast agent is geïnstalleerd. Deze methode is de belangrijkste manier om een wacht woord opnieuw in te stellen voor een Azure-VM. Als u problemen ondervindt met de Azure Guest agent die niet reageert of als u niet kunt installeren na het uploaden van een aangepaste installatie kopie, kunt u een Windows-wacht woord hand matig opnieuw instellen. In dit artikel wordt beschreven hoe u een wacht woord voor een lokaal account opnieuw instelt door de virtuele schijf van het bron besturingssysteem te koppelen aan een andere virtuele machine. De stappen die in dit artikel worden beschreven, zijn niet van toepassing op Windows-domein controllers. 

> [!WARNING]
> Gebruik deze methode alleen als laatste redmiddel. Probeer altijd een wacht woord opnieuw in te stellen met behulp van de [Azure portal of Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eerst.

## <a name="overview-of-the-process"></a>Overzicht van het proces
De belangrijkste stappen voor het uitvoeren van een lokale wacht woord opnieuw instellen voor een Windows-VM in azure wanneer er geen toegang tot de Azure-gast agent is:

1. Stop de betrokken VM.
1. Maak een moment opname voor de besturingssysteem schijf van de virtuele machine.
1. Maak een kopie van de besturingssysteem schijf van de moment opname.
1. Koppel en koppel de gekopieerde besturingssysteem schijf aan een andere Windows-VM en maak vervolgens enkele configuratie bestanden op de schijf. De bestanden helpen u bij het opnieuw instellen van het wacht woord.
1. Ontkoppel de gekopieerde besturingssysteem schijf van de virtuele machine voor probleem oplossing.
1. Swap de schijf van het besturings systeem voor de betrokken VM.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Gedetailleerde stappen voor de VM met Resource Manager-implementatie

> [!NOTE]
> De stappen zijn niet van toepassing op Windows-domein controllers. Het werkt alleen op een zelfstandige server of op een server die lid is van een domein.

Probeer altijd een wacht woord opnieuw in te stellen met behulp van de [Azure portal of Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voordat u de volgende stappen uitvoert. Zorg ervoor dat u een back-up van uw VM hebt voordat u begint.

1. Maak een moment opname van de besturingssysteem schijf van de betreffende virtuele machine, maakt een schijf van de moment opname en koppel de schijf vervolgens aan een virtuele machine voor probleem oplossing. Zie [problemen met een Windows-VM oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van de Azure Portal](troubleshoot-recovery-disks-portal-windows.md)voor meer informatie.
2. Maak verbinding met de virtuele machine voor probleem oplossing met behulp van Extern bureaublad.
3. Maak `gpt.ini` in `\Windows\System32\GroupPolicy` op het station van de bron-VM (als GPT. ini bestaat, wijzig de naam in GPT. ini. bak):
   
   > [!WARNING]
   > Zorg ervoor dat u niet per ongeluk de volgende bestanden in C:\Windows maakt, het station van het besturings systeem voor de virtuele machine voor probleem oplossing. Maak de volgende bestanden in het station met het besturings systeem voor de bron-VM die is gekoppeld als een gegevens schijf.
   
   * Voeg de volgende regels toe aan `gpt.ini` het bestand dat u hebt gemaakt:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![GPT maken. ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. Maken `scripts.ini` in `\Windows\System32\GroupPolicy\Machines\Scripts\`. Zorg ervoor dat verborgen mappen worden weer gegeven. Als dat nodig is, `Machine` maakt `Scripts` u de mappen of.
   
   * Voeg de volgende regels toe `scripts.ini` aan het bestand dat u hebt gemaakt:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts. ini maken](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. Maak `FixAzureVM.cmd` in `\Windows\System32` met de volgende inhoud, vervang `<username>` en `<newpassword>` door uw eigen waarden:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM. cmd maken](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    U moet voldoen aan de geconfigureerde wachtwoord complexiteits vereisten voor uw virtuele machine bij het definiëren van het nieuwe wacht woord.

6. Ontkoppel de schijf in Azure Portal van de virtuele machine voor probleem oplossing.

7. [Wijzig de besturingssysteem schijf voor de betrokken VM](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Nadat de nieuwe virtuele machine is uitgevoerd, maakt u verbinding met de virtuele machine met Extern bureaublad met het nieuwe wacht `FixAzureVM.cmd` woord dat u in het script hebt opgegeven.

9. Verwijder de volgende bestanden van uw externe sessie naar de nieuwe virtuele machine om de omgeving op te schonen:
    
    * Van%windir%\System32
      * FixAzureVM. cmd verwijderen
    * Van%windir%\System32\GroupPolicy\Machine\Scripts
      * scripts. ini verwijderen
    * Van%windir%\System32\GroupPolicy
      * Verwijder GPT. ini (als GPT. ini aanwezig was en u de naam ervan hebt gewijzigd in GPT. ini. bak, wijzig de naam van het bak-bestand weer in GPT. ini)

## <a name="detailed-steps-for-classic-vm"></a>Gedetailleerde stappen voor de klassieke VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> De stappen zijn niet van toepassing op Windows-domein controllers. Het werkt alleen op een zelfstandige server of op een server die lid is van een domein.

Probeer altijd een wacht woord opnieuw in te stellen met behulp van de [Azure portal of Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) voordat u de volgende stappen uitvoert. Zorg ervoor dat u een back-up van uw VM hebt voordat u begint. 

1. Verwijder de betrokken VM in Azure Portal. Als u de virtuele machine verwijdert, worden alleen de meta gegevens, de verwijzing van de virtuele machine in azure, verwijderd. De virtuele schijven blijven behouden wanneer de virtuele machine wordt verwijderd:
   
   * Selecteer de virtuele machine in de Azure Portal en klik vervolgens op *verwijderen*:
     
     ![Bestaande VM verwijderen](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Koppel de besturingssysteem schijf van de bron-VM aan de virtuele machine voor probleem oplossing. De virtuele machine voor probleem oplossing moet zich in dezelfde regio bevinden als de besturingssysteem schijf van de `West US`bron-VM (zoals):
   
   1. Selecteer de virtuele machine voor probleem oplossing in de Azure Portal. Klik op *schijven* | *bestaande koppelen*:
     
      ![Bestaande schijf koppelen](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Selecteer *VHD-bestand* en selecteer vervolgens het opslag account dat uw bron-VM bevat:
     
      ![Opslagaccount selecteren](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Schakel het selectie vakje *klassieke opslag accounts weer geven*in en selecteer vervolgens de bron container. De bron container is doorgaans *vhd's*:
     
      ![Opslag container selecteren](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Opslag container selecteren](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Selecteer de VHD van het besturings systeem die u wilt koppelen. Klik op *selecteren* om het proces te volt ooien:
     
      ![Virtuele bron schijf selecteren](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Klik op OK om de schijf te koppelen

      ![Bestaande schijf koppelen](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Maak verbinding met de virtuele machine voor probleem oplossing met Extern bureaublad en zorg ervoor dat de besturingssysteem schijf van de bron-VM zichtbaar is:

   1. Selecteer de virtuele machine voor probleem oplossing in de Azure Portal en klik op *verbinding maken*.

   2. Open het RDP-bestand dat wordt gedownload. Voer de gebruikers naam en het wacht woord in van de virtuele machine voor probleem oplossing.

   3. Zoek in Verkenner naar de gegevens schijf die u hebt toegevoegd. Als de VHD van de bron-VM de enige gegevens schijf is die is gekoppeld aan de virtuele machine voor probleem oplossing, moet deze het station F: zijn:
     
      ![Gekoppelde gegevens schijf weer geven](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Maken `gpt.ini` in `\Windows\System32\GroupPolicy` op het station van de bron-VM `gpt.ini` (indien aanwezig, `gpt.ini.bak`naam wijzigen in):
   
   > [!WARNING]
   > Zorg ervoor dat u niet per ongeluk de volgende bestanden maakt `C:\Windows`in, het station van het besturings systeem voor de virtuele machine voor probleem oplossing. Maak de volgende bestanden in het station met het besturings systeem voor de bron-VM die is gekoppeld als een gegevens schijf.
   
   * Voeg de volgende regels toe aan `gpt.ini` het bestand dat u hebt gemaakt:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![GPT maken. ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Maken `scripts.ini` in `\Windows\System32\GroupPolicy\Machines\Scripts\`. Zorg ervoor dat verborgen mappen worden weer gegeven. Als dat nodig is, `Machine` maakt `Scripts` u de mappen of.
   
   * Voeg de volgende regels toe `scripts.ini` aan het bestand dat u hebt gemaakt:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts. ini maken](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. Maak `FixAzureVM.cmd` in `\Windows\System32` met de volgende inhoud, vervang `<username>` en `<newpassword>` door uw eigen waarden:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM. cmd maken](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    U moet voldoen aan de geconfigureerde wachtwoord complexiteits vereisten voor uw virtuele machine bij het definiëren van het nieuwe wacht woord.

7. Ontkoppel de schijf in Azure Portal van de virtuele machine voor probleem oplossing:
   
   1. Selecteer de virtuele machine voor probleem oplossing in de Azure Portal, klik op *schijven*.
   
   2. Selecteer de gegevens schijf die is gekoppeld in stap 2, klik op **ontkoppelen**en klik vervolgens op **OK**.

     ![Schijf ontkoppelen](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Schijf ontkoppelen](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Maak een VM op basis van de besturingssysteem schijf van de bron-VM:
   
     ![Een VM maken op basis van een sjabloon](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Een VM maken op basis van een sjabloon](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Een VM maken op basis van een sjabloon](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>De ervaring voor het maken van virtuele machines volt ooien

1. Nadat de nieuwe virtuele machine is uitgevoerd, maakt u verbinding met de virtuele machine met Extern bureaublad met het nieuwe wacht `FixAzureVM.cmd` woord dat u in het script hebt opgegeven.

2. Verwijder de volgende bestanden van uw externe sessie naar de nieuwe virtuele machine om de omgeving op te schonen:
    
    * Van`%windir%\System32`
      * verwijdert`FixAzureVM.cmd`
    * Van`%windir%\System32\GroupPolicy\Machine\Scripts`
      * verwijdert`scripts.ini`
    * Van`%windir%\System32\GroupPolicy`
      * Verwijder `gpt.ini` (indien `gpt.ini` aanwezig voor, en u de naam ervan hebt gewijzigd `gpt.ini.bak`in), `.bak` Wijzig de naam `gpt.ini`van het bestand in)

## <a name="next-steps"></a>Volgende stappen
Zie de [hand leiding voor probleem oplossing van RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)als u nog steeds geen verbinding kunt maken met behulp van extern bureaublad. De [gedetailleerde hand leiding](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor het oplossen van problemen met RDP ziet u in plaats van specifieke stappen op probleemoplossings methoden. U kunt ook [een ondersteunings aanvraag voor Azure openen](https://azure.microsoft.com/support/options/) voor praktische hulp.
