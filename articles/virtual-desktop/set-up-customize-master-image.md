---
title: Een VHD-model installatie kopie voorbereiden en aanpassen-Azure
description: Een installatie kopie van een virtuele Windows Desktop-Preview Master voorbereiden, aanpassen en uploaden naar Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: 8127c0a42ec42a661af31cc489964cc40cb4937d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981080"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Een VHD-hoofdinstallatiekopie voorbereiden en aanpassen

In dit artikel leest u hoe u een installatie kopie van een virtuele harde schijf (VHD) voor het uploaden naar Azure voorbereidt, inclusief het maken van virtuele machines (Vm's) en het installeren van software. Deze instructies gelden voor een Windows-voor beeld-specifieke configuratie voor virtuele Bureau bladen die kan worden gebruikt met de bestaande processen van uw organisatie.

## <a name="create-a-vm"></a>Een virtuele machine maken

Windows 10 Enter prise multi-session is beschikbaar in de galerie met installatie kopieën van Azure. Er zijn twee opties voor het aanpassen van deze installatie kopie.

De eerste optie is het inrichten van een virtuele machine (VM) in azure door de instructies te volgen in [een VM maken op basis van een beheerde installatie kopie](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)en vervolgens door te gaan naar de voor [bereiding en installatie van software](set-up-customize-master-image.md#software-preparation-and-installation).

De tweede optie is om de installatie kopie lokaal te maken door de installatie kopie te downloaden, een Hyper-V-VM in te richten en deze aan te passen aan uw behoeften, die in de volgende sectie worden behandeld.

### <a name="local-image-creation"></a>Lokale installatie kopie maken

Zodra u de installatie kopie naar een lokale locatie hebt gedownload, opent u **Hyper-V-beheer** om een virtuele machine te maken met de VHD die u hebt gekopieerd. De volgende instructies zijn een eenvoudige versie, maar u vindt meer gedetailleerde instructies voor het [maken van een virtuele machine in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Een virtuele machine maken met de gekopieerde VHD:

1. Open de **wizard Nieuwe virtuele machine**.

2. Selecteer op de pagina generatie opgeven de optie **generatie 1**.

    ![Een scherm afbeelding van de pagina voor het genereren van opgeven. De optie ' generatie 1 ' is geselecteerd.](media/a41174fd41302a181e46385e1e701975.png)

3. Schakel onder controlepunt type de controle punten uit door het selectie vakje uit te scha kelen.

    ![Een scherm afbeelding van de sectie type controle punt van de pagina met controle punten.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

U kunt ook de volgende cmdlet uitvoeren in Power shell om controle punten uit te scha kelen.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Vaste schijf

Als u een virtuele machine maakt op basis van een bestaande VHD, wordt standaard een dynamische schijf gemaakt. U kunt het wijzigen naar een vaste schijf door **schijf bewerken** te selecteren, zoals wordt weer gegeven in de volgende afbeelding. Zie [een Windows VHD of VHDX voorbereiden voor het uploaden naar Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)voor meer gedetailleerde instructies.

![Een scherm afbeelding van de optie voor het bewerken van de schijf.](media/35772414b5a0f81f06f54065561d1414.png)

U kunt ook de volgende Power shell-cmdlet uitvoeren om de schijf naar een vaste schijf te wijzigen.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Software voorbereiding en-installatie

In deze sectie wordt beschreven hoe u FSLogix, Windows Defender en andere algemene toepassingen voorbereidt en installeert. 

Als u Office 365 ProPlus en OneDrive op uw VM installeert, raadpleegt u [Office installeren op een Master-VHD-installatie kopie](install-office-on-wvd-master-image.md). Volg de koppeling in de volgende stappen van dit artikel om terug te gaan naar dit artikel en het hoofd-VHD-proces te volt ooien.

Als uw gebruikers toegang moeten hebben tot bepaalde LOB-toepassingen, raden we u aan deze te installeren nadat u de instructies van deze sectie hebt voltooid.

### <a name="disable-automatic-updates"></a>Automatische updates uitschakelen

Automatische updates via lokale groepsbeleid uitschakelen:

1. Open **lokale Groepsbeleidsobjecteditor\\\\Beheersjablonen\\Windows-onderdelen Windows Update**.
2. Klik met de rechter muisknop op **Automatische updates configureren** en stel deze in op **uitgeschakeld**.

U kunt ook de volgende opdracht uitvoeren op een opdracht prompt om Automatische updates uit te scha kelen.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Start indeling voor Windows 10-Pc's opgeven (optioneel)

Voer deze opdracht uit om een start indeling op te geven voor Windows 10-Pc's.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-user-profile-container-fslogix"></a>Gebruikers profiel container instellen (FSLogix)

Als u de container FSLogix wilt opnemen als onderdeel van de installatie kopie, volgt u de instructies in [een profiel container maken voor een hostgroep met een bestands share](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). U kunt de functionaliteit van de FSLogix-container testen met [deze Snelstartgids](https://docs.microsoft.com/en-us/fslogix/configure-cloud-cache-tutorial).

### <a name="configure-windows-defender"></a>Windows Defender configureren

Als Windows Defender is geconfigureerd in de virtuele machine, moet u ervoor zorgen dat de volledige inhoud van VHD-en VHDX-bestanden tijdens een bijlage niet wordt gescand.

Deze configuratie verwijdert alleen het scannen van VHD-en VHDX-bestanden tijdens een bijlage, maar heeft geen invloed op real-time scans.

Zie [Windows Defender anti virus-uitsluitingen configureren op Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus)voor meer gedetailleerde instructies voor het configureren van Windows Defender op Windows Server.

Zie uitsluitingen [configureren en valideren op basis van bestands extensie en maplocatie](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)voor meer informatie over het configureren van Windows Defender om bepaalde bestanden uit te sluiten.

### <a name="configure-session-timeout-policies"></a>Sessietime-outbeleid configureren

Externe-sessie beleidsregels kunnen worden afgedwongen op groepsbeleid niveau, omdat alle virtuele machines in een hostgroep deel uitmaken van dezelfde beveiligings groep.

Beleid voor externe sessies configureren:

1. Navigeer naar **Beheersjablonen** > **Windows** > -onderdelenexternbureaublad-services > de limieten voor**sessies**van**extern bureaublad sessie**- > sessiehost.
2. Selecteer in het deel venster aan de rechter kant het beleid **tijds limiet instellen voor actieve maar niet-actieve Extern bureaublad-services sessies** .
3. Nadat het modale venster wordt weer gegeven, wijzigt u de beleids optie van **niet geconfigureerd** in **ingeschakeld** om het beleid te activeren.
4. Stel de hoeveelheid tijd in het vervolg keuzemenu onder de beleids optie in op **3 uur**.

U kunt beleid voor externe sessies ook hand matig configureren door de volgende opdrachten uit te voeren:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 10800000 /f
```

### <a name="set-up-time-zone-redirection"></a>Tijd zone-omleiding instellen

Tijdzone omleiding kan worden afgedwongen op groepsbeleid niveau, omdat alle virtuele machines in een hostgroep deel uitmaken van dezelfde beveiligings groep.

Tijd zones omleiden:

1. Open de **console Groepsbeleidbeheer**op de Active Directory-server.
2. Breid uw domein en groepsbeleid objecten uit.
3. Klik met de rechter muisknop op het **Groepsbeleid-object** dat u hebt gemaakt voor de instellingen voor groeps beleid en selecteer **bewerken**.
4. Ga in het **Groepsbeleidsbeheer-editor**naar **computer configuratie** > **beleid** > **Beheersjablonen** > **Windows-onderdelen**  >   **Extern bureaublad-services** > **extern bureaublad Session**host > -**apparaat en-bron omleiding**.
5. Schakel de instelling **tijd zone omleiding toestaan** in.

U kunt deze opdracht ook uitvoeren op de hoofd installatie kopie om tijd zones om te leiden:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Opslag Sense uitschakelen

Voor Windows Virtual Desktop Session Host die gebruikmaakt van Windows 10 Enter prise of Windows 10 Enter prise multi-session, raden we u aan om opslag Sense uit te scha kelen. U kunt opslag Sense uitschakelen in het menu instellingen onder **opslag**, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Een scherm opname van het menu opslag onder instellingen. De optie ' opslag Sense ' is uitgeschakeld.](media/storagesense.png)

U kunt de instelling ook wijzigen met het REGI ster door de volgende opdracht uit te voeren:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Aanvullende taal ondersteuning toevoegen

Dit artikel heeft geen betrekking op het configureren van taal-en regionale ondersteuning. Raadpleeg voor meer informatie de volgende artikelen:

- [Talen toevoegen aan Windows-installatie kopieën](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Onderdelen op aanvraag](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Taal-en regio functies op aanvraag (DOM)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Andere toepassingen en register configuratie

In deze sectie wordt de configuratie van toepassingen en besturings systemen besproken. Alle configuratie in deze sectie wordt uitgevoerd via Register vermeldingen die kunnen worden uitgevoerd met behulp van de opdracht regel-en Regedit-hulpprogram ma's.

>[!NOTE]
>U kunt best practices in de configuratie implementeren met behulp van groepsbeleid objecten (Gpo's) of register invoer. De beheerder kan een van beide opties kiezen op basis van de vereisten van de organisatie.

Voer de volgende opdracht uit voor het verzamelen van telemetrie-gegevens over Windows 10 Enter prise met een feedback hub:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Voer de volgende opdracht uit om Watson-crashes op te lossen:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Voer de volgende opdrachten in de REGI ster-editor in om ondersteuning voor 5k-oplossingen te herstellen. U moet de opdrachten uitvoeren voordat u de side-by-side-stack kunt inschakelen.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>De installatie kopie voorbereiden voor uploaden naar Azure

Nadat u de configuratie hebt voltooid en alle toepassingen hebt geïnstalleerd, volgt u de instructies in [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) om de installatie kopie voor te bereiden.

Nadat de installatie kopie is voor bereid voor het uploaden, moet u ervoor zorgen dat de virtuele machine de status uitgeschakeld of niet-toegewezen heeft.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Master installatie kopie uploaden naar een opslag account in azure

Deze sectie is alleen van toepassing wanneer de master installatie kopie lokaal is gemaakt.

In de volgende instructies wordt uitgelegd hoe u uw master installatie kopie uploadt naar een Azure-opslag account. Als u nog geen Azure Storage-account hebt, volgt u de instructies in [dit artikel](https://code.visualstudio.com/tutorials/static-website/create-storage) om er een te maken.

1. Converteer de VM-installatie kopie (VHD) naar vast als u dat nog niet hebt gedaan. Als u de afbeelding niet naar vast hebt geconverteerd, is het niet mogelijk om de installatie kopie te maken.

2. Upload de VHD naar een BLOB-container in uw opslag account. U kunt snel uploaden met het [Storage Explorer-hulp programma](https://azure.microsoft.com/features/storage-explorer/). Zie [dit artikel](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)voor meer informatie over het Storage Explorer-hulp programma.

    ![Een scherm opname van het zoek venster van het Microsoft Azure Storage Explorer-hulp programma. Het selectie vakje upload. VHD-of vhdx-bestanden als pagina-blobs (aanbevolen) is ingeschakeld.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Ga vervolgens naar de Azure Portal in uw browser en zoek naar ' installatie kopieën '. Uw zoek opdracht leidt u naar de pagina **installatie kopie maken** , zoals wordt weer gegeven in de volgende scherm afbeelding:

    ![Een scherm opname van de pagina afbeelding maken van het Azure Portal, gevuld met voorbeeld waarden voor de afbeelding.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Wanneer u de installatie kopie hebt gemaakt, ziet u een melding zoals de afbeelding in de volgende scherm afbeelding:

    ![Een scherm opname van de melding ' installatie kopie is gemaakt '.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een installatie kopie hebt, kunt u hostgroepen maken of bijwerken. Raadpleeg de volgende artikelen voor meer informatie over het maken en bijwerken van hostgroepen:

- [Een hostgroep met een Azure Resource Manager-sjabloon maken](create-host-pools-arm-template.md)
- [Zelfstudie: Een hostgroep maken met Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Een hostgroep maken met Power shell](create-host-pools-powershell.md)
- [Een profiel container maken voor een hostgroep met een bestands share](create-host-pools-user-profile.md)
- [De taakverdelings methode voor virtuele Bureau bladen van Windows configureren](configure-host-pool-load-balancing.md)
