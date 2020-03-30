---
title: Hyper-V VM-noodherstel met Azure Site Recovery en PowerShell
description: Automatiseer het herstel van hyperv's naar Azure met de Azure Site Recovery-service met PowerShell en Azure Resource Manager.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257990"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Disaster recovery instellen voor Azure voor Hyper V VM's met PowerShell en Azure Resource Manager

[Azure Site Recovery](site-recovery-overview.md) draagt bij aan uw BCDR-strategie (Business Continuity and Disaster Recovery) door replicatie, failover en herstel van Virtuele Azure-machines (VM's) en on-premises VM's en fysieke servers te orkestreren.

In dit artikel wordt beschreven hoe u Windows PowerShell samen met Azure Resource Manager gebruiken om Hyper-V VM's te repliceren naar Azure. In het voorbeeld dat in dit artikel wordt gebruikt, ziet u hoe u één VM die op een Hyper-V-host wordt uitgevoerd, repliceren naar Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell biedt cmdlets om Azure te beheren met Windows PowerShell. Site Recovery PowerShell-cmdlets, beschikbaar met Azure PowerShell voor Azure Resource Manager, helpen u uw servers in Azure te beschermen en te herstellen.

U hoeft geen PowerShell-expert te zijn om dit artikel te gebruiken, maar u moet wel basisconcepten, zoals modules, cmdlets en sessies, begrijpen. Zie de [PowerShell-documentatie](/powershell) en [Het gebruik van Azure PowerShell met Azure Resource Manager](../powershell-azure-resource-manager.md)voor meer informatie.

> [!NOTE]
> Microsoft-partners in het CSP-programma (Cloud Solution Provider) kunnen de beveiliging van klantservers voor hun respectieve CSP-abonnementen (tenantabonnementen) configureren en beheren.

## <a name="before-you-start"></a>Voordat u begint

Zorg ervoor dat u deze vereisten hebt:

- Een [Microsoft Azure-account.](https://azure.microsoft.com/) U kunt beginnen met een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). Daarnaast u lezen over de prijzen van [Azure Site Recovery Manager.](https://azure.microsoft.com/pricing/details/site-recovery/)
- Azure PowerShell. Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps)voor informatie over deze release en hoe u deze installeren.

Bovendien bevat het specifieke voorbeeld dat in dit artikel wordt beschreven de volgende voorwaarden:

- Een Hyper-V-host met Windows Server 2012 R2 of Microsoft Hyper-V Server 2012 R2 met een of meer VM's. Hyper-V-servers moeten rechtstreeks of via een proxy met het internet worden verbonden.
- De VM's die u wilt repliceren, moeten aan [deze vereisten](hyper-v-azure-support-matrix.md#replicated-vms)voldoen.

## <a name="step-1-sign-in-to-your-azure-account"></a>Stap 1: Aanmelden bij uw Azure-account

1. Open een PowerShell-console en voer deze opdracht uit om u aan te melden bij uw Azure-account. De cmdlet brengt een webpagina vraagt u voor `Connect-AzAccount`uw accountreferenties: .
   - U uw accountreferenties als parameter in `Connect-AzAccount` de cmdlet opnemen met behulp van de parameter **Credential.**
   - Als u een CSP-partner bent die namens een tenant werkt, geeft u de klant op als tenant door de primaire domeinnaam tenant-id of tenant te gebruiken. Bijvoorbeeld: `Connect-AzAccount -Tenant "fabrikam.com"`
1. Koppel het abonnement dat u wilt gebruiken aan het account, omdat een account meerdere abonnementen kan hebben:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Controleer of uw abonnement is geregistreerd om de Azure-providers voor Herstelservices en Siterecovery te gebruiken met behulp van de volgende opdrachten:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Controleer of in de opdrachtuitvoer de **registratiestaat** is ingesteld op **Geregistreerd,** u doorgaan naar stap 2. Als dit niet het zo is, moet u de ontbrekende provider registreren in uw abonnement door de volgende opdrachten uit te voeren:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Controleer of de providers zich hebben geregistreerd met de volgende opdrachten:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>Stap 2: De kluis instellen

1. Maak een Azure Resource Manager-brongroep waarin u de kluis maken of een bestaande brongroep gebruiken. Maak als volgt een nieuwe resourcegroep. De `$ResourceGroupName` variabele bevat de naam van de resourcegroep die u wilt maken en de $Geo variabele bevat de Azure-regio waarin de resourcegroep moet worden gemaakt (bijvoorbeeld 'Brazilië-zuid').

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Voer de `Get-AzResourceGroup` cmdlet uit om een lijst met resourcegroepen in uw abonnement te verkrijgen.
1. Maak als volgt een nieuwe kluis van Azure Recovery Services:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

U een lijst met bestaande `Get-AzRecoveryServicesVault` kluizen ophalen met de cmdlet.

## <a name="step-3-set-the-recovery-services-vault-context"></a>Stap 3: De kluiscontext van Recovery Services instellen

Stel de kluiscontext als volgt in:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>Stap 4: Een Hyper-V-site maken

1. Maak als volgt een nieuwe Hyper-V-site:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Met deze cmdlet wordt een sitehersteltaak gestart om de site te maken en wordt een taakobject Siteherstel geretourneerd. Wacht tot de taak is voltooid en controleer of de taak is voltooid.
1. Gebruik `Get-AzRecoveryServicesAsrJob` de cmdlet om het taakobject op te halen en controleer de huidige status van de taak.
1. Als volgt een registratiesleutel voor de site genereren en downloaden:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Kopieer de gedownloade sleutel naar de Hyper-V-host. Je hebt de sleutel nodig om de Hyper-V host te registreren op de site.

## <a name="step-5-install-the-provider-and-agent"></a>Stap 5: Installeer de provider en agent

1. Download het installatieprogramma voor de nieuwste versie van de provider van [Microsoft.](https://aka.ms/downloaddra)
1. Voer de installer uit op de Hyper-V host.
1. Aan het einde van de installatie verder naar de registratie stap.
1. Geef de gedownloade sleutel op wanneer u daarom wordt gevraagd en de volledige registratie van de Hyper-V-host.
1. Controleer of de Hyper-V-host als volgt op de site is geregistreerd:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Als u een Hyper-V-kernserver uitvoert, downloadt u het installatiebestand en volgt u de volgende stappen:

1. Haal de bestanden uit _AzureSiteRecoveryProvider.exe_ naar een lokale map door deze opdracht uit te voeren:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Voer de volgende opdracht uit:

   ```console
   .\setupdr.exe /i
   ```

   Resultaten worden geregistreerd op _%ProgramData%\ASRLogs\DRASetupWizard.log_.

1. Registreer de server door deze opdracht uit te voeren:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>Stap 6: Een replicatiebeleid maken

Voordat u begint, moet het opgegeven opslagaccount zich in hetzelfde Azure-gebied bevinden als de kluis en moet geo-replicatie zijn ingeschakeld.

1. Maak als volgt een replicatiebeleid:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Controleer de geretourneerde taak om ervoor te zorgen dat het maken van het replicatiebeleid slaagt.

1. Haal als volgt de beveiligingscontainer op die overeenkomt met de site:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Koppel de beveiligingscontainer als volgt aan het replicatiebeleid:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Wacht tot de koppelingstaak is voltooid.

1. Haal de toewijzing van beveiligingscontainers op.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>Stap 7: VM-beveiliging inschakelen

1. Haal het beschermbare item op dat overeenkomt met de VM die u wilt beveiligen, als volgt:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. Bescherm de VM. Als er meer dan één schijf aan de vm is gekoppeld, geeft u de schijf van het besturingssysteem op met de parameter **OSDiskName.**

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Wacht tot de VM's na de eerste replicatie een beveiligde status bereiken. Dit kan even duren, afhankelijk van factoren zoals de hoeveelheid gegevens die moet worden gerepliceerd en de beschikbare upstreambandbreedte naar Azure. Wanneer een beschermde status aanwezig is, worden de taakstatus en stateDescription als volgt bijgewerkt:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Hersteleigenschappen bijwerken (zoals de grootte van de VM-rol) en het Azure-netwerk waaraan de VM-NIC na failover kan worden gekoppeld.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Als u wilt repliceren naar beheerde schijven met CMK in Azure, gaat u de volgende stappen uitvoeren met Az PowerShell 3.3.0:
>
> 1. Failover naar beheerde schijven inschakelen door VM-eigenschappen bij te werken
> 1. De `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet gebruiken om de schijf-id voor elke schijf van het beveiligde item op te halen
> 1. Maak een woordenboekobject met `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet om de toewijzing van schijf-id naar schijfversleutelingsset te bevatten. Deze schijfversleutelingssets worden vooraf gemaakt door u in het doelgebied.
> 1. Werk de VM-eigenschappen bij met behulp van `Set-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet door het woordenboekobject door te geven in de parameter **DiskIdToDiskEncryptionSetMap.**

## <a name="step-8-run-a-test-failover"></a>Stap 8: Een testfailover uitvoeren

1. Voer een testfailover als volgt uit:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Controleer of de test-vm is gemaakt in Azure. De failovertaak van de test wordt opgeschort na het maken van de test-VM in Azure.
1. Voer het als gevolg uit om de testfailover op te ruimen en te voltooien:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](/powershell/module/az.recoveryservices) over Azure Site Recovery met Azure Resource Manager PowerShell-cmdlets.
