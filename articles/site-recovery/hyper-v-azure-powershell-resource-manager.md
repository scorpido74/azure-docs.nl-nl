---
title: Herstel na nood geval voor Hyper-V-VM'S met Azure Site Recovery en Power shell
description: Automatiseer herstel na nood gevallen van virtuele Hyper-V-machines naar Azure met de Azure Site Recovery-service met behulp van Power shell en Azure Resource Manager.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 548fa8181c4841d8f57de485c0a4e714b5e9321a
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863907"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Herstel na nood geval instellen op Azure voor Hyper-V-Vm's met behulp van Power shell en Azure Resource Manager

[Azure site Recovery](site-recovery-overview.md) draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR) door de replicatie, failover en het herstel van virtuele machines van Azure (vm's) en on-premises vm's en fysieke servers te organiseren.

In dit artikel wordt beschreven hoe u Windows Power shell gebruikt, samen met Azure Resource Manager, om virtuele Hyper-V-machines te repliceren naar Azure. In het voor beeld in dit artikel wordt beschreven hoe u één VM die op een Hyper-V-host wordt uitgevoerd, naar Azure repliceert.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell biedt cmdlets voor het beheren van Azure met behulp van Windows Power shell. Site Recovery Power shell-cmdlets, die beschikbaar zijn in Azure PowerShell voor Azure Resource Manager, helpen u bij het beveiligen en herstellen van uw servers in Azure.

U hoeft geen Power shell-expert te zijn om dit artikel te kunnen gebruiken, maar u moet wel inzicht krijgen in basis concepten, zoals modules, cmdlets en sessies. Lees aan de [slag met Windows Power shell](https://technet.microsoft.com/library/hh857337.aspx)en [gebruik Azure PowerShell met Azure Resource Manager](../powershell-azure-resource-manager.md).

> [!NOTE]
> Micro soft-partners in het programma Cloud Solution Provider (CSP) kunnen de beveiliging van klanten servers configureren en beheren voor hun respectieve CSP-abonnementen (Tenant abonnementen).
>
>

## <a name="before-you-start"></a>Voordat u begint
Zorg ervoor dat deze vereisten aanwezig zijn in de volgende plaats:

* Een [Microsoft Azure](https://azure.microsoft.com/) -account. U kunt beginnen met een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). Daarnaast kunt u meer lezen over de [prijzen van Azure site Recovery Manager](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell. Zie [install Azure PowerShell](/powershell/azure/install-az-ps)voor meer informatie over deze versie en hoe u deze installeert.

Daarnaast bevat het specifieke voor beeld dat in dit artikel wordt beschreven, de volgende vereisten:

* Een Hyper-V-host met Windows Server 2012 R2 of Microsoft Hyper-V Server 2012 R2 met een of meer virtuele machines. Hyper-V-servers moeten zijn verbonden met internet, hetzij rechtstreeks, hetzij via een proxy.
* De Vm's die u wilt repliceren, moeten voldoen aan [deze vereisten](hyper-v-azure-support-matrix.md#replicated-vms).

## <a name="step-1-sign-in-to-your-azure-account"></a>Stap 1: Meld u aan bij uw Azure-account

1. Open een Power shell-console en voer deze opdracht uit om u aan te melden bij uw Azure-account. Met de cmdlet wordt een webpagina weer gegeven waarin u wordt gevraagd om uw account referenties: **Connect-AzAccount**.
    - U kunt ook uw account referenties als een para meter in de cmdlet **Connect-AzAccount** toevoegen met behulp van de para meter **-Credential** .
    - Als u een CSP-partner werkt namens een Tenant, geeft u de klant op als Tenant met behulp van hun tenantID of Tenant primaire domein naam. Bijvoorbeeld: **Connect-AzAccount-Tenant "fabrikam.com"**
2. Koppel het abonnement dat u wilt gebruiken met het account, omdat een account meerdere abonnementen kan hebben:

    `Select-AzSubscription -SubscriptionName $SubscriptionName`

3. Controleer of uw abonnement is geregistreerd voor het gebruik van de Azure-providers voor Recovery Services en Site Recovery met behulp van de volgende opdrachten:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

4. Controleer of in de uitvoer van de opdracht de **RegistrationState** is ingesteld op **geregistreerd**, u kunt door gaan met stap 2. Als dat niet het geval is, moet u de ontbrekende provider in uw abonnement registreren door de volgende opdrachten uit te voeren:

    `Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Controleer of de providers zijn geregistreerd met behulp van de volgende opdrachten:

    `Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`

## <a name="step-2-set-up-the-vault"></a>Stap 2: de kluis instellen

1. Maak een Azure Resource Manager resource groep waarin u de kluis wilt maken of gebruik een bestaande resource groep. Maak als volgt een nieuwe resource groep. De variabele $ResourceGroupName bevat de naam van de resource groep die u wilt maken en de $Geo variabele bevat de Azure-regio waarin de resource groep moet worden gemaakt (bijvoorbeeld ' Brazilië-zuid ').

    `New-AzResourceGroup -Name $ResourceGroupName -Location $Geo`

2. Als u een lijst met resource groepen in uw abonnement wilt ophalen, voert u de cmdlet **Get-AzResourceGroup** uit.
2. Maak als volgt een nieuwe Azure Recovery Services-kluis:

        $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    U kunt een lijst met bestaande kluizen ophalen met de cmdlet **Get-AzRecoveryServicesVault** .


## <a name="step-3-set-the-recovery-services-vault-context"></a>Stap 3: de Recovery Services kluis context instellen

Stel de context van de kluis als volgt in:

`Set-AsrVaultSettings -Vault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>Stap 4: een Hyper-V-site maken

1. Maak als volgt een nieuwe Hyper-V-site:

        $sitename = "MySite"                #Specify site friendly name
        New-AsrFabric -Type HyperVSite -Name $sitename

2. Met deze cmdlet wordt een Site Recovery taak gestart om de site te maken en wordt een Site Recovery taak object geretourneerd. Wacht tot de taak is voltooid en controleer of de taak is voltooid.
3. Gebruik de **cmdlet Get-AsrJob**om het taak object op te halen en de huidige status van de taak te controleren.
4. Genereer en down load een registratie sleutel voor de site, als volgt:

    ```
    $SiteIdentifier = Get-AsrFabric -Name $sitename | Select -ExpandProperty SiteIdentifier
    $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
    ```

5. Kopieer de gedownloade sleutel naar de Hyper-V-host. U hebt de sleutel nodig om de Hyper-V-host te registreren bij de-site.

## <a name="step-5-install-the-provider-and-agent"></a>Stap 5: de provider en de agent installeren

1. Down load het installatie programma voor de nieuwste versie van de provider van [micro soft](https://aka.ms/downloaddra).
2. Voer het installatie programma uit op de Hyper-V-host.
3. Aan het einde van de installatie gaat u verder met de registratie stap.
4. Wanneer u hierom wordt gevraagd, geeft u de gedownloade sleutel op en voltooit u de registratie van de Hyper-V-host.
5. Controleer als volgt of de Hyper-V-host is geregistreerd bij de site:

        $server =  Get-AsrFabric -Name $siteName | Get-AsrServicesProvider -FriendlyName $server-friendlyname

Als u een Hyper-V-kern server gebruikt, downloadt u het installatie bestand en voert u de volgende stappen uit:
1. Pak de bestanden uit AzureSiteRecoveryProvider. exe uit naar een lokale map door de volgende opdracht uit te voeren: ```AzureSiteRecoveryProvider.exe /x:. /q```
2. Run ```.\setupdr.exe /i``` resultaten worden geregistreerd in%Programdata%\ASRLogs\DRASetupWizard.log.

3. Registreer de server door de volgende opdracht uit te voeren:

    ```cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"```


## <a name="step-6-create-a-replication-policy"></a>Stap 6: een replicatie beleid maken

Houd er rekening mee dat het opgegeven opslag account zich in dezelfde Azure-regio als de kluis moet bevinden en dat geo-replicatie moet zijn ingeschakeld.

1. Maak als volgt een replicatie beleid:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Controleer de geretourneerde taak om te controleren of het maken van het replicatie beleid slaagt.

3. Haal als volgt de beveiligings container op die overeenkomt met de site:

        $protectionContainer = Get-AsrProtectionContainer
3. Koppel de beveiligings container als volgt aan het replicatie beleid:

        $Policy = Get-AsrPolicy -FriendlyName $PolicyName
        $associationJob  = New-AsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
4. Wacht totdat de koppelings taak is voltooid.

5. Haal de toewijzing van de beveiligings container op.

        $ProtectionContainerMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $protectionContainer

## <a name="step-7-enable-vm-protection"></a>Stap 7: VM-beveiliging inschakelen

1. Haal als volgt het Beveilig bare item op dat overeenkomt met de virtuele machine die u wilt beveiligen:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $ProtectableItem = Get-AsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Beveilig de virtuele machine. Als er meer dan één schijf aan de virtuele machine is gekoppeld, geeft u de besturingssysteem schijf op met behulp van de para meter *OSDiskName* .

        $OSType = "Windows"                                 # "Windows" or "Linux"
        $DRjob = New-AsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID

3. Wacht totdat de virtuele machines een beveiligde status hebben bereikt na de initiële replicatie. Dit kan enige tijd duren, afhankelijk van factoren zoals de hoeveelheid gegevens die moeten worden gerepliceerd en de beschik bare upstream-band breedte naar Azure. Wanneer een beveiligde status is ingesteld, worden de taak status en StateDescription als volgt bijgewerkt:

        PS C:\> $DRjob = Get-AsrJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Update herstel-eigenschappen (zoals de grootte van de VM-rol,) en het Azure-netwerk waaraan de VM-NIC moet worden gekoppeld na een failover.

        PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AsrReplicationProtectedItem --InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AsrJob -Job $UpdateJob

        PS C:\> $UpdateJob| select -ExpandProperty state
        Get-AsrJob -Job $job | select -ExpandProperty state

        Succeeded

> [!NOTE]
> Als u wilt repliceren naar CMK ingeschakelde beheerde schijven in azure, voert u de volgende stappen uit met AZ Power Shell 3.3.0:
>
> 1. Failover naar beheerde schijven inschakelen door de VM-eigenschappen bij te werken
> 2. Gebruik de cmdlet Get-AsrReplicationProtectedItem om de schijf-ID op te halen voor elke schijf van het beveiligde item
> 3. Maak een Dictionary-object met behulp van de cmdlet New-object System. Collections. generic. dictionary ' 2 [System. String, System. String] ' om de toewijzing van schijf-id aan schijf versleutelings te bevatten. Deze schijf versleutelings sets moeten vooraf door u worden gemaakt in de doel regio.
> 4. Werk de VM-eigenschappen bij met de cmdlet Set-AsrReplicationProtectedItem door het object Dictionary in de DiskIdToDiskEncryptionSetMap-para meter door te geven.

## <a name="step-8-run-a-test-failover"></a>Stap 8: een testfailover uitvoeren
1. Voer een testfailover als volgt uit:

        $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $rpi = Get-AsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        $TFjob =Start-AsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Controleer of de test-VM is gemaakt in Azure. De taak testfailover is onderbroken na het maken van de test-VM in Azure.
3. Voer de volgende handelingen uit om de testfailover op te schonen en te volt ooien:

        $TFjob = Start-AsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"

## <a name="next-steps"></a>Volgende stappen
Meer [informatie](https://docs.microsoft.com/powershell/module/az.recoveryservices) over Azure Site Recovery met Azure Resource Manager Power shell-cmdlets.
