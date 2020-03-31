---
title: Servers verwijderen en beveiliging uitschakelen | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u servers uit een kluis voor siteherstel uitschrijven en de beveiliging van virtuele machines en fysieke servers uitschakelen.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257626"
---
# <a name="remove-servers-and-disable-protection"></a>Servers verwijderen en beveiliging uitschakelen

In dit artikel wordt beschreven hoe u servers uiteenvalt uit een kluis van Recovery Services en hoe u de beveiliging uitschakelt voor machines die zijn beschermd door Site Recovery.


## <a name="unregister-a--configuration-server"></a>Het registreren van een configuratieserver uitschrijven

Als u VMware VM's of Windows/Linux-fysieke servers nabootst naar Azure, u een niet-verbonden configuratieserver als volgt uitschrijven uit een kluis:

1. [Schakel de bescherming van virtuele machines uit.](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)
2. [Replicatiebeleid loskoppelen of verwijderen.](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)
3. [De configuratieserver verwijderen](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Een VMM-server uitschrijven

1. Stop met het repliceren van virtuele machines in clouds op de VMM-server die u wilt verwijderen.
2. Verwijder netwerktoewijzingen die worden gebruikt door clouds op de VMM-server die u wilt verwijderen. Klik in **Site Recovery Infrastructure** > **For System Center VMM** > **Network Mapping**met de rechtermuisknop op de netwerktoewijzing > **Verwijderen.**
3. Let op de ID van de VMM-server.
4. Replicatiebeleid loskoppelen van clouds op de VMM-server die u wilt verwijderen.  Dubbelklik in **siteherstelinfrastructuur** > **voor Systeemcentrum VMM-replicatiebeleid** >  **Replication Policies**op het bijbehorende beleid. Klik met de rechtermuisknop op de cloud > **disassociate**.
5. Verwijder de VMM-server of het actieve knooppunt. Klik in **Site Recovery Infrastructure** > **For System Center VMM** > **VMM-servers**met de rechtermuisknop op de server > **Verwijderen**.
6. Als uw VMM-server de verbroken status heeft, downloadt en voert u het [opruimscript](https://aka.ms/asr-cleanup-script-vmm) uit op de VMM-server. Open PowerShell met de optie **Uitvoeren als beheerder** om het uitvoeringsbeleid voor het standaardbereik (LocalMachine) te wijzigen. Geef in het script de id op van de VMM-server die u wilt verwijderen. Het script verwijdert registratie- en cloudkoppelingsgegevens van de server.
5. Voer het opschoningsscript uit op een secundaire VMM-server.
6. Voer het opschoningsscript uit op andere passieve VMM-clusterknooppunten waarop de provider is geïnstalleerd.
7. Verwijder de provider handmatig op de VMM-server. Als u een cluster hebt, verwijdert u uit alle knooppunten.
8. Als uw virtuele machines zijn gerepliceerd naar Azure, moet u de Microsoft Recovery Services-agent verwijderen van Hyper-V-hosts in de verwijderde clouds.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Een Hyper-V-host uitschrijven op een Hyper-V-site

Hyper-V-hosts die niet worden beheerd door VMM worden verzameld in een Hyper-V-site. Verwijder een host op een Hyper-V-site als volgt:

1. Replicatie uitschakelen voor Hyper-VVm's op de host.
2. Beleid voor de Hyper-V-site loskoppelen. Dubbelklik in het replicatiebeleid > **voor siteherstelinfrastructuur voor hyper-v-sites** >  **Replication Policies**op het bijbehorende beleid. **Site Recovery Infrastructure** Klik met de rechtermuisknop op de site > **disassociate**.
3. Hyper-V-hosts verwijderen. Klik in **Site Recovery Infrastructure** > **For Hyper-V Sites** > **Hyper-V Hosts**met de rechtermuisknop op de server > **Delete**.
4. Verwijder de Hyper-V-site nadat alle hosts zijn verwijderd. Klik in **Site Recovery Infrastructure** > **For Hyper-V Sites** > **Hyper-V Sites**met de rechtermuisknop op de site > **Verwijderen**.
5. Als uw Hyper-V-host de verbroken status **heeft,** voert u het volgende script uit op elke Hyper-V-host die u hebt verwijderd. Het script ruimt instellingen op de server op en maakt deze uit de kluis.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Beveiliging uitschakelen voor een Vm of fysieke server van VMware (VMware naar Azure)

1. Klik in**gerepliceerde items**met **beveiligde items** > met de rechtermuisknop op de machine > **Replicatie uitschakelen**.
2. Selecteer **in Replicatiepagina uitschakelen** een van de volgende opties:
    - **Replicatie uitschakelen en verwijderen (aanbevolen)** - Met deze optie wordt het gerepliceerde item uit Azure Site Recovery verwijderd en wordt de replicatie voor de machine gestopt. De replicatieconfiguratie op configuration server wordt opgeschoond en de facturering van siteherstel voor deze beveiligde server is gestopt. Houd er rekening mee dat deze optie alleen kan worden gebruikt wanneer configuration server is verbonden.
    - **Verwijderen** - Deze optie wordt verondersteld alleen te worden gebruikt als de bronomgeving is verwijderd of niet toegankelijk is (niet is verbonden). Hiermee wordt het gerepliceerde item verwijderd uit Azure Site Recovery (facturering wordt gestopt). De replicatieconfiguratie op de configuratieserver **wordt niet** opgeschoond. 

> [!NOTE]
> In beide opties wordt de mobiliteitsservice niet van de beveiligde servers verwijderd, maar moet u deze handmatig verwijderen. Als u van plan bent de server opnieuw te beveiligen met dezelfde configuratieserver, u het verwijderen van de mobiliteitsservice overslaan.

> [!NOTE]
> Als u al hebt gefaald ten opzichte van een virtuele machine en deze wordt uitgevoerd in Azure, moet u er rekening mee houden dat de beveiliging uitschakelen niet wordt verwijderd/ van invloed is op het mislukte over VM.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Beveiliging uitschakelen voor een Azure VM (Azure naar Azure)

-  Klik in**gerepliceerde items**met **beveiligde items** > met de rechtermuisknop op de machine > **Replicatie uitschakelen**.
> [!NOTE]
> mobiliteitsservice wordt niet verwijderd van de beveiligde servers, u moet deze handmatig verwijderen. Als u van plan bent de server opnieuw te beschermen, u het verwijderen van de mobiliteitsservice overslaan.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Beveiliging uitschakelen voor een virtuele Hyper-V-machine (Hyper-V naar Azure)

> [!NOTE]
> Gebruik deze procedure als u Hyper-V VM's zonder VMM-server naar Azure repliceert. Als u uw virtuele machines repliceert met het **Scenario Systeemcentrum VMM naar Azure,** volgt u de instructies Beveiliging uitschakelen voor een virtuele hyper-v-machine die wordt gerepliceerd met het Scenario systeemcentrum VMM naar Azure

1. Klik in**gerepliceerde items**met **beveiligde items** > met de rechtermuisknop op de machine > **Replicatie uitschakelen**.
2. In **Replicatie uitschakelen**u de volgende opties selecteren:
   - **Replicatie uitschakelen en verwijderen (aanbevolen)** - Met deze optie wordt het gerepliceerde item uit Azure Site Recovery verwijderd en wordt de replicatie voor de machine gestopt. Replicatieconfiguratie op de on-premises virtuele machine wordt opgeschoond en de facturering van siteherstel voor deze beveiligde server wordt gestopt.
   - **Verwijderen** - Deze optie wordt verondersteld alleen te worden gebruikt als de bronomgeving is verwijderd of niet toegankelijk is (niet is verbonden). Hiermee wordt het gerepliceerde item verwijderd uit Azure Site Recovery (facturering wordt gestopt). Replicatieconfiguratie op de on-premises virtuele machine **wordt niet** opgeschoond. 

 > [!NOTE]
     > Als u de optie **Verwijderen** hebt gekozen, voert u de volgende set scripts uit om de on-premises Hyper-V-server op te schonen.

> [!NOTE]
> Als u al hebt gefaald ten opzichte van een virtuele machine en deze wordt uitgevoerd in Azure, moet u er rekening mee houden dat de beveiliging uitschakelen niet wordt verwijderd/ van invloed is op het mislukte over VM.

1. Op de bron Hyper-V-hostserver om replicatie voor de virtuele machine te verwijderen. SQLVM1 vervangen door de naam van uw virtuele machine en het script uitvoeren vanuit een administratieve PowerShell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Beveiliging uitschakelen voor een virtuele hyper-v-machine die wordt gerepliceerd naar Azure met het Scenario Van Systeemcentrum VMM naar Azure

1. Klik in**gerepliceerde items**met **beveiligde items** > met de rechtermuisknop op de machine > **Replicatie uitschakelen**.
2. Selecteer **in Replicatie uitschakelen**een van de volgende opties:

   - **Replicatie uitschakelen en verwijderen (aanbevolen)** - Met deze optie wordt het gerepliceerde item uit Azure Site Recovery verwijderd en wordt de replicatie voor de machine gestopt. Replicatieconfiguratie op de on-premises virtuele machine wordt opgeschoond en de facturering met siteherstel voor deze beveiligde server wordt gestopt.
   - **Verwijderen** - Deze optie wordt verondersteld alleen te worden gebruikt als de bronomgeving is verwijderd of niet toegankelijk is (niet is verbonden). Hiermee wordt het gerepliceerde item verwijderd uit Azure Site Recovery (facturering wordt gestopt). Replicatieconfiguratie op de on-premises virtuele machine **wordt niet** opgeschoond. 

     > [!NOTE]
     > Als u de optie **Verwijderen** hebt gekozen, u de volgende scripts tunen om de on-premises VMM-server op te schonen.
3. Voer dit script uit op de bron VMM-server met PowerShell (vereiste beheerdersbevoegdheden) vanaf de VMM-console. Vervang de tijdelijke aanduiding **SQLVM1** door de naam van uw virtuele machine.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. In de bovenstaande stappen worden de replicatie-instellingen op de VMM-server gewist. Voer dit script uit om replicatie voor de virtuele machine die op de Hyper-V-hostserver wordt uitgevoerd, te stoppen. Vervang SQLVM1 door de naam van uw virtuele machine en host01.contoso.com door de naam van de Hyper-V-hostserver.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Beveiliging uitschakelen voor een virtuele Hyper-V-machine die wordt gerepliceerd naar secundaire VMM-server met behulp van het Scenario van System Center VMM naar VMM

1. Klik in**gerepliceerde items**met **beveiligde items** > met de rechtermuisknop op de machine > **Replicatie uitschakelen**.
2. Selecteer **in Replicatie uitschakelen**een van de volgende opties:

   - **Replicatie uitschakelen en verwijderen (aanbevolen)** - Met deze optie wordt het gerepliceerde item uit Azure Site Recovery verwijderd en wordt de replicatie voor de machine gestopt. Replicatieconfiguratie op de on-premises virtuele machine wordt opgeschoond en de facturering met siteherstel voor deze beveiligde server wordt gestopt.
   - **Verwijderen** - Deze optie wordt verondersteld alleen te worden gebruikt als de bronomgeving is verwijderd of niet toegankelijk is (niet is verbonden). Hiermee wordt het gerepliceerde item verwijderd uit Azure Site Recovery (facturering wordt gestopt). Replicatieconfiguratie op de on-premises virtuele machine **wordt niet** opgeschoond. Voer de volgende set scripts uit om de on-premises virtuele machines van replicatieinstellingen op te schonen.
     > [!NOTE]
     > Als u de optie **Verwijderen** hebt gekozen, u de volgende scripts tunen om de on-premises VMM-server op te schonen.

3. Voer dit script uit op de bron VMM-server met PowerShell (vereiste beheerdersbevoegdheden) vanaf de VMM-console. Vervang de tijdelijke aanduiding **SQLVM1** door de naam van uw virtuele machine.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Voer op de secundaire VMM-server dit script uit om de instellingen voor de secundaire virtuele machine op te schonen:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Vernieuw op de secundaire VMM-server de virtuele machines op de Hyper-V-hostserver, zodat de secundaire VM opnieuw wordt gedetecteerd in de VMM-console.
6. In de bovenstaande stappen worden de replicatie-instellingen op de VMM-server gewist. Als u de replicatie voor de virtuele machine wilt stoppen, voert u het volgende script uit oh de primaire en secundaire VM's. Vervang SQLVM1 door de naam van uw virtuele machine.

        Remove-VMReplication –VMName “SQLVM1”




