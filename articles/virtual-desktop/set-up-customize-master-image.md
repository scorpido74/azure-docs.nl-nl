---
title: Een master VHD-afbeelding voorbereiden en aanpassen - Azure
description: Een windows virtual desktop-hoofdafbeelding voorbereiden, aanpassen en uploaden naar Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127721"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Een VHD-hoofdinstallatiekopie voorbereiden en aanpassen

In dit artikel wordt uitgelegd hoe u een vhd-afbeelding (master virtual hard disk) voorbereiden voor uploaden naar Azure, inclusief hoe u virtuele machines (VM's) maakt en er software op installeert. Deze instructies zijn bedoeld voor een Windows Virtual Desktop-specifieke configuratie die kan worden gebruikt met de bestaande processen van uw organisatie.

## <a name="create-a-vm"></a>Een virtuele machine maken

Windows 10 Enterprise-sessies zijn beschikbaar in de Azure Image Gallery. Er zijn twee opties voor het aanpassen van deze afbeelding.

De eerste optie is het inrichten van een virtuele machine (VM) in Azure door de instructies in [Een VM maken vanuit een beheerde afbeelding](../virtual-machines/windows/create-vm-generalized-managed.md)te volgen en vervolgens verder te gaan naar [softwarevoorbereiding en -installatie.](set-up-customize-master-image.md#software-preparation-and-installation)

De tweede optie is om de afbeelding lokaal te maken door de afbeelding te downloaden, een Hyper-V VM in te richten en aan te passen aan uw behoeften, die we in de volgende sectie behandelen.

### <a name="local-image-creation"></a>Lokale beeldcreatie

Zodra u de afbeelding naar een lokale locatie hebt gedownload, opent u **Hyper-V Manager** om een VM te maken met de VHD die u hebt gekopieerd. De volgende instructies zijn een eenvoudige versie, maar u meer gedetailleerde instructies vinden in [Een virtuele machine maken in Hyper-V.](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/)

Ga als lid van het nieuws om een vm te maken met de gekopieerde VHD:

1. Open de **wizard Nieuwe virtuele machine**.

2. Selecteer **generatie 1**op de pagina Generatie opgeven .

    ![Een schermafbeelding van de pagina Generatie opgeven. De optie "Generatie 1" is geselecteerd.](media/a41174fd41302a181e46385e1e701975.png)

3. Schakel onder Controlepunttype controlepunten uit door het selectievakje uit te schakelen.

    ![Een schermafbeelding van het gedeelte Controlepunttype van de pagina Controlepunten.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

U ook de volgende cmdlet in PowerShell uitvoeren om controlepunten uit te schakelen.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Vaste schijf

Als u een VM maakt op basis van een bestaande VHD, wordt standaard een dynamische schijf gemaakt. Deze kan worden gewijzigd in een vaste schijf door **Schijf bewerken te selecteren...** zoals in de volgende afbeelding wordt weergegeven. Zie Een Windows [VHD of VHDX voorbereiden om te uploaden naar Azure voor](../virtual-machines/windows/prepare-for-upload-vhd-image.md)meer gedetailleerde instructies.

![Een schermafbeelding van de optie Schijf bewerken.](media/35772414b5a0f81f06f54065561d1414.png)

U ook de volgende PowerShell-cmdlet uitvoeren om de schijf op een vaste schijf te wijzigen.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Softwarevoorbereiding en -installatie

In dit gedeelte wordt uitgelegd hoe u FSLogix en Windows Defender voorbereiden en installeren, evenals enkele basisconfiguratieopties voor apps en het register van uw afbeelding. 

Als u Office 365 ProPlus en OneDrive op uw vm installeert, gaat u naar [Office installeren op een hoofdVHD-afbeelding](install-office-on-wvd-master-image.md) en volgt u de instructies om de apps te installeren. Nadat je klaar bent, ga je terug naar dit artikel.

Als uw gebruikers toegang nodig hebben tot bepaalde LOB-toepassingen, raden we u aan ze te installeren nadat u de instructies van deze sectie hebt voltooid.

### <a name="set-up-user-profile-container-fslogix"></a>Container voor gebruikersprofielen instellen (FSLogix)

Als u de FSLogix-container als onderdeel van de afbeelding wilt opnemen, volgt u de instructies in [Een profielcontainer maken voor een hostgroep met behulp van een bestandsshare.](create-host-pools-user-profile.md#configure-the-fslogix-profile-container) U de functionaliteit van de FSLogix container testen met [deze quickstart.](/fslogix/configure-cloud-cache-tutorial/)

### <a name="configure-windows-defender"></a>Windows Defender configureren

Als Windows Defender is geconfigureerd in de VM, controleert u of deze is geconfigureerd om de volledige inhoud van VHD- en VHDX-bestanden tijdens de bijlage niet te scannen.

Deze configuratie verwijdert alleen het scannen van VHD- en VHDX-bestanden tijdens de bijlage, maar heeft geen invloed op het scannen in realtime.

Zie Uitsluitingen van [Windows Defender-antivirus configureren op Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)voor meer gedetailleerde instructies voor het configureren van Windows Defender Antivirus.

Zie Uitsluitingen configureren en valideren op basis van [bestandsextensie en maplocatie](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)voor meer informatie over het configureren van Windows Defender om bepaalde bestanden uit te sluiten van scannen.

### <a name="disable-automatic-updates"></a>Automatische updates uitschakelen

Automatische updates uitschakelen via lokaal groepsbeleid:

1. Open **de beheersjablonen\\\\van\\de lokale groepsbeleidseditor Windows-onderdelen Windows Update**.
2. Klik met de rechtermuisknop op **Automatische update configureren** en stel deze in op **Uitgeschakeld**.

U ook de volgende opdracht uitvoeren op een opdrachtprompt om Automatische updates uit te schakelen.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Startindeling opgeven voor Windows 10-pc's (optioneel)

Voer deze opdracht uit om een startindeling voor Windows 10-pc's op te geven.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Omleiding tijdzone instellen

Omleiding van tijdzones kan worden afgedwongen op groepsbeleidsniveau, omdat alle VM's in een hostgroep deel uitmaken van dezelfde beveiligingsgroep.

Ga als u tijdzones omleiden:

1. Open de **console Groepsbeleidsbeheer**op de Active Directory-server .
2. Vouw uw domein- en groepsbeleidsobjecten uit.
3. Klik met de rechtermuisknop op het **groepsbeleidsobject** dat u hebt gemaakt voor de groepsbeleidsinstellingen en selecteer **Bewerken**.
4. Navigeer in de **groepsbeleidsbeheereditor**naar**Beheersjablonen** > **Windows Components** > **Remote Desktop Services** >  **computerconfiguratiebeleid** > **Policies** > Windows Components Remote Desktop Services**Extern bureaublad-sessiehostapparaat** > **en Resourceomleiding**.
5. Schakel de **omleidingsinstelling tijdzone toestaan in.**

U deze opdracht ook uitvoeren op de hoofdafbeelding om tijdzones om te leiden:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Opslaggevoel uitschakelen

Voor Windows Virtual Desktop-sessiehost die windows 10 Enterprise of Windows 10 Enterprise-sessie gebruikt, raden we u aan Storage Sense uit te schakelen. U Storage Sense uitschakelen in het menu Instellingen onder **Opslag**, zoals in de volgende schermafbeelding wordt weergegeven:

![Een schermafbeelding van het menu Opslag onder Instellingen. De optie 'Opslaggevoel' is uitgeschakeld.](media/storagesense.png)

U de instelling ook wijzigen met het register door de volgende opdracht uit te voeren:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Extra taalondersteuning opnemen

In dit artikel wordt niet ingaan op het configureren van taal- en regionale ondersteuning. Raadpleeg voor meer informatie de volgende artikelen:

- [Talen toevoegen aan Windows-afbeeldingen](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Functies op aanvraag](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Taal- en regiofuncties on demand (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Andere toepassingen en registerconfiguratie

Deze sectie behandelt de configuratie van toepassingen en besturingssystemen. Alle configuratie in deze sectie wordt gedaan door middel van registervermeldingen die kunnen worden uitgevoerd door opdrachtregel- en regedit-hulpprogramma's.

>[!NOTE]
>U aanbevolen procedures in configuratie implementeren met groepsbeleidsobjecten (GPO's) of registerinvoer. De beheerder kan een van beide opties kiezen op basis van de vereisten van hun organisatie.

Voer de opdracht uit voor het verzamelen van telemetriegegevens op Windows 10 Enterprise- multisessie:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Voer de volgende opdracht uit om Watson-crashes op te lossen:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Voer de volgende opdrachten in de registereditor in om ondersteuning voor 5k-resolutie op te lossen. U moet de opdrachten uitvoeren voordat u de stack naast elkaar inschakelen.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>De afbeelding voorbereiden voor uploaden naar Azure

Nadat u de configuratie hebt voltooid en alle toepassingen hebt geïnstalleerd, volgt u de instructies in [Een Windows VHD of VHDX voorbereiden om te uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) om de afbeelding voor te bereiden.

Nadat u de afbeelding hebt voorbereid om te uploaden, moet u ervoor zorgen dat de vm in de uit- of deallocatie blijft.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Hoofdafbeelding uploaden naar een opslagaccount in Azure

Deze sectie is alleen van toepassing wanneer de hoofdafbeelding lokaal is gemaakt.

In de volgende instructies ziet u hoe u uw hoofdafbeelding uploadt naar een Azure-opslagaccount. Als u nog geen Azure-opslagaccount hebt, volgt u de instructies in [dit artikel](/azure/javascript/tutorial-vscode-static-website-node-03) om er een te maken.

1. Converteer de VM-afbeelding (VHD) naar Vast als u dat nog niet hebt gedaan. Als u de afbeelding niet converteert naar Vast, u de afbeelding niet maken.

2. Upload de VHD naar een blobcontainer in uw opslagaccount. U snel uploaden met het [hulpprogramma Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/) Zie [dit artikel](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)voor meer informatie over het gereedschap Storage Explorer.

    ![Een schermafbeelding van het zoekvenster van het Microsoft Azure Storage Explorer Tool. Het selectievakje 'Vhd- of vhdx-bestanden uploaden als paginablobs (aanbevolen)' is ingeschakeld.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Ga vervolgens naar de Azure-portal in uw browser en zoek naar 'Afbeeldingen'. Uw zoekopdracht moet u naar de pagina **Afbeelding maken** leiden, zoals in de volgende schermafbeelding wordt weergegeven:

    ![Een schermafbeelding van de afbeeldingspagina maken van de Azure-portal, gevuld met voorbeeldwaarden voor de afbeelding.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Zodra u de afbeelding hebt gemaakt, ziet u een melding zoals die in de volgende schermafbeelding:

    ![Een screenshot van de melding 'met succes gemaakte afbeelding'.](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Volgende stappen

Nu u een afbeelding hebt, u hostpools maken of bijwerken. Zie de volgende artikelen voor meer informatie over het maken en bijwerken van hostpools:

- [Een hostpool maken met een Azure Resource Manager-sjabloon](create-host-pools-arm-template.md)
- [Zelfstudie: Een hostgroep maken met Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Een hostpool maken met PowerShell](create-host-pools-powershell.md)
- [Een profielcontainer maken voor een hostpool met behulp van een bestandsshare](create-host-pools-user-profile.md)
- [De taakverdelingsmethode voor Windows Virtual Desktop configureren](configure-host-pool-load-balancing.md)
