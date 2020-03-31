---
title: Herstel van Hyper-V (met VMM) instellen op een secundaire site met Azure Site Recovery/PowerShell
description: Beschrijft hoe u disaster recovery van Hyper-V VM's in VMM-clouds instellen op een secundaire VMM-site met Azure Site Recovery en PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: deef7bfdbc28d744cb81da59d3ffc13a1abee54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048606"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Noodherstel van Hyper V VM's instellen op een secundaire site met PowerShell (Resource Manager)

In dit artikel ziet u hoe u de stappen voor replicatie van Hyper-V VM's in Virtual Machine Manager-clouds van Het Systeemcentrum automatiseren naar een Virtual Machine Manager-cloud in een secundaire on-premises site met azure [siteherstel](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- De [architectuur en onderdelen voor dit scenario](hyper-v-vmm-architecture.md) doornemen.
- Raadpleeg de [ondersteuningsvereisten](site-recovery-support-matrix-to-sec-site.md) voor alle onderdelen.
- Zorg ervoor dat Virtual Machine Manager-servers en Hyper-V-hosts voldoen aan [de ondersteuningsvereisten.](site-recovery-support-matrix-to-sec-site.md)
- Controleer of de VM's die u wilt repliceren voldoen aan [de gerepliceerde machineondersteuning.](site-recovery-support-matrix-to-sec-site.md)

## <a name="prepare-for-network-mapping"></a>Voorbereiden op netwerktoewijzing

[Netwerktoewijzingskaarten](hyper-v-vmm-network-mapping.md) tussen on-premises VM-netwerken voor Virtual Machine Manager in bron- en doelclouds. Bij toewijzing gebeurt het volgende:

- Virtuele machines worden verbonden met de juiste VM-doelnetwerken na een failover.
- Gerepliceerde VM's worden optimaal op Hyper-V-doelhostservers geplaatst.
- Als u netwerktoewijzing niet configureert, worden replica-VM's niet meer verbonden met een VM-netwerk na een failover.

Bereid Virtual Machine Manager als volgt voor:

- Zorg ervoor dat u [logische netwerken van Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) op de bron hebt en de servers van Virtual Machine Manager target:
  - Het logische netwerk op de bronserver moet worden gekoppeld aan de broncloud waarin de Hyper-V-hosts zich bevinden.
  - Het logische netwerk op de doelserver moet worden gekoppeld aan de doelcloud.
- Zorg ervoor dat u [VM-netwerken](https://docs.microsoft.com/system-center/vmm/network-virtual) op de bron hebt en virtual machine manager-servers target. VM-netwerken moeten zijn gekoppeld aan het logische netwerk op elke locatie.
- Verbind VM's op de Hyper-V-bronhosts met het VM-bronnetwerk.

## <a name="prepare-for-powershell"></a>Voorbereiden op PowerShell

Zorg ervoor dat Azure PowerShell klaar is voor gebruik:

- Als u PowerShell al gebruikt, u upgraden naar versie 0.8.10 of hoger. [Meer informatie](/powershell/azureps-cmdlets-docs) over het instellen van PowerShell.
- Nadat u PowerShell hebt ingesteld en geconfigureerd, controleert u de [servicecmdlets](/powershell/azure/overview).
- Lees de handleiding [Aan de slag](/powershell/azure/get-started-azureps) voor meer informatie over het gebruik van parameterwaarden, ingangen en uitvoer in PowerShell.

## <a name="set-up-a-subscription"></a>Een abonnement instellen

1. Meld u vanuit PowerShell aan bij uw Azure-account.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Haal een lijst met uw abonnementen op, met de abonnements-id's. Let op de id van het abonnement waarin u de kluis Van Herstelservices wilt maken.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Stel het abonnement voor de kluis in.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

1. Maak een Azure Resource Manager-brongroep als u er geen hebt.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Maak een nieuwe vault voor Recovery Services. Sla het kluisobject op in een variabele die later moet worden gebruikt.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   U het kluisobject ophalen nadat `Get-AzRecoveryServicesVault` u het hebt gemaakt met behulp van de cmdlet.

## <a name="set-the-vault-context"></a>De context van de kluis instellen

1. Een bestaande kluis ophalen.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Stel de context van de kluis in.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>De siteherstelprovider installeren

1. Maak op de machine Virtual Machine Manager een map door de volgende opdracht uit te voeren:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Haal de bestanden uit met behulp van het gedownloade installatiebestand van de provider.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Installeer de provider en wacht tot de installatie is voltooid.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Registreer de server in de kluis.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Een replicatiebeleid maken en koppelen

1. Maak als volgt een replicatiebeleid, in dit geval voor Hyper-V 2012 R2:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > De Virtual Machine Manager-cloud kan Hyper-V-hosts bevatten die verschillende versies van Windows Server uitvoeren, maar het replicatiebeleid is voor een specifieke versie van een besturingssysteem. Als u verschillende hosts hebt die op verschillende besturingssystemen worden uitgevoerd, maakt u voor elk systeem afzonderlijke replicatiebeleidsregels. Als u bijvoorbeeld vijf hosts hebt die worden uitgevoerd op Windows Server 2012 en drie hosts die worden uitgevoerd op Windows Server 2012 R2, maakt u twee replicatiebeleidsregels. U maakt er een voor elk type besturingssysteem.

1. Haal de primaire beveiligingscontainer (primaire Virtual Machine Manager-cloud) en herstelbeveiligingscontainer op (recovery Virtual Machine Manager cloud).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Haal het replicatiebeleid op dat u hebt gemaakt met de vriendelijke naam.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Start de koppeling van de beveiligingscontainer (Virtual Machine Manager cloud) met het replicatiebeleid.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Wacht tot de taak van de beleidsvereniging is voltooid. Als u wilt controleren of de taak is voltooid, gebruikt u het volgende PowerShell-fragment:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Voer de volgende opdracht uit nadat de taak is voltooid:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Volg de stappen in [Monitor activiteit](#monitor-activity)om de voltooiing van de bewerking te controleren.

##  <a name="configure-network-mapping"></a>Netwerktoewijzing configureren

1. Gebruik deze opdracht om servers voor de huidige kluis op te halen. De opdracht slaat de siteherstelservers op in de `$Servers` arrayvariabele.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Voer deze opdracht uit om de netwerken op te halen voor de bronvirtual machinebeheerserver en de doelserver voor Virtual Machine Manager.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > De bron Virtual Machine Manager-server kan de eerste of tweede in de serverarray zijn. Controleer de namen van de Virtual Machine Manager-server en haal de netwerken op de juiste manier op.

1. Deze cmdlet maakt een mapping tussen het primaire netwerk en het herstelnetwerk. Het geeft het primaire netwerk `$PrimaryNetworks`aan als het eerste element van . Het geeft het herstelnetwerk aan `$RecoveryNetworks`als het eerste element van .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Beveiliging voor VM's inschakelen

Nadat de servers, clouds en netwerken correct zijn geconfigureerd, u de beveiliging van VM's in de cloud inschakelen.

1. Voer de volgende opdracht uit om de beveiligingscontainer op te halen om de beveiliging in te schakelen:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Download de beveiligingsentiteit (VM) als volgt:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Replicatie voor de VM inschakelen.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Als u wilt repliceren naar beheerde schijven met CMK in Azure, gaat u de volgende stappen uitvoeren met Az PowerShell 3.3.0:
>
> 1. Failover naar beheerde schijven inschakelen door VM-eigenschappen bij te werken
> 1. De `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet gebruiken om de schijf-id voor elke schijf van het beveiligde item op te halen
> 1. Maak een woordenboekobject met `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet om de toewijzing van schijf-id naar schijfversleutelingsset te bevatten. Deze schijfversleutelingssets worden vooraf gemaakt door u in het doelgebied.
> 1. Werk de VM-eigenschappen bij met behulp van `Set-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet door het woordenboekobject door te geven in de parameter **DiskIdToDiskEncryptionSetMap.**

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

Als u uw implementatie wilt testen, voert u een testfailover uit voor één virtuele machine. U ook een herstelplan maken dat meerdere VM's bevat en een testfailover voor het plan uitvoeren. Met een testfailover wordt uw failover- en herstelmechanisme in een geïsoleerd netwerk gesimuleerd.

1. Haal de VM op waarin VM's mislukken.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Voer een testfailover uit.

   Voor één virtuele machine:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Voor een herstelplan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Volg de stappen in [Monitor activiteit](#monitor-activity)om de voltooiing van de bewerking te controleren.

## <a name="run-planned-and-unplanned-failovers"></a>Geplande en ongeplande failovers uitvoeren

1. Een geplande failover uitvoeren.

   Voor één virtuele machine:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Voor een herstelplan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Een ongeplande failover uitvoeren.

   Voor één virtuele machine:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Voor een herstelplan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Activiteit controleren

Gebruik de volgende opdrachten om failoveractiviteiten te controleren. Wacht tot de verwerking is voltooid tussen taken.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](/powershell/module/az.recoveryservices) over siteherstel met PowerShell-cmdlets van Resource Manager.
