---
title: Externe hulpprogramma's gebruiken om Azure VM-problemen op te lossen | Microsoft Documenten
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: b86b1a2d8a49554cc3df99e0a32a2c0ccaacb560
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920005"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Externe hulpprogramma's gebruiken om Azure VM-problemen op te lossen

Wanneer u problemen op een virtuele Azure-machine (VM) oplost, u verbinding maken met de VM met behulp van de externe hulpprogramma's die in dit artikel worden besproken in plaats van het Extern bureaublad-protocol (RDP) te gebruiken.

## <a name="serial-console"></a>Seriële console

Gebruik een [seriële console voor Azure Virtual Machines](serial-console-windows.md) om opdrachten uit te voeren op de externe Azure VM.

## <a name="remote-cmd"></a>Externe CMD

[PsExec downloaden](https://docs.microsoft.com/sysinternals/downloads/psexec). Maak verbinding met de VM door de volgende opdracht uit te voeren:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* De opdracht moet worden uitgevoerd op een computer die zich in hetzelfde virtuele netwerk bevindt.
>* DIP of HostName kan \<worden gebruikt om computer> te vervangen.
>* De parameter -s zorgt ervoor dat de opdracht wordt aangeroepen met behulp van Systeemaccount (beheerdersmachtiging).
>* PsExec maakt gebruik van TCP-poorten 135 en 445. Als gevolg hiervan moeten de twee poorten open zijn op de firewall.

## <a name="run-command"></a>Voer de opdracht  uit

Zie [PowerShell-scripts uitvoeren in uw Windows VM met opdracht](../windows/run-command.md)uitvoeren voor meer informatie over het gebruik van de opdracht Opdracht Uitvoeren om scripts op de vm uit te voeren.

## <a name="custom-script-extension"></a>Aangepaste scriptextensie

U de functie Aangepaste scriptextensie gebruiken om een aangepast script uit te voeren op de doel-VM. Om deze functie te kunnen gebruiken, moet aan de volgende voorwaarden worden voldaan:

* De VM heeft connectiviteit.
* Azure Virtual Machine Agent is geïnstalleerd en werkt zoals verwacht op de VM.
* De extensie is niet eerder op de VM geïnstalleerd.
 
  De extensie injecteert het script alleen de eerste keer dat het wordt gebruikt. Als u deze functie later gebruikt, herkent de extensie dat deze al is gebruikt en wordt het nieuwe script niet geüpload.

Upload uw script naar een opslagaccount en genereer een eigen container. Voer vervolgens het volgende script uit in Azure PowerShell op een computer met connectiviteit met de VM.

### <a name="for-classic-deployment-model-vms"></a>Vm's voor klassiek implementatiemodel

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>VMs van Azure Resource Manager

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Externe PowerShell

>[!NOTE]
>TCP Port 5986 (HTTPS) moet open zijn, zodat u deze optie gebruiken.
>
>Voor Azure Resource Manager VM's moet u poort 5986 openen in de netwerkbeveiligingsgroep (NSG). Zie Beveiligingsgroepen voor meer informatie. 
>
>Voor RDFE VM's moet u een eindpunt hebben met een privéhaven (5986) en een openbare haven. Dan moet je ook die openbare poort op de NSG openen.

### <a name="set-up-the-client-computer"></a>De clientcomputer instellen

Als u PowerShell wilt gebruiken om op afstand verbinding te maken met de VIRTUELE V.D. Voeg hiervoor de VM toe aan de lijst vertrouwde hosts van PowerShell door de volgende opdracht uit te voeren, naar gelang van het geval.

Eén vm toevoegen aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Ga als u meerdere VM's toevoegt aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Ga als u alle computers toevoegen aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>RemotePS inschakelen op de VM

Voor VM's die zijn gemaakt met behulp van het klassieke implementatiemodel, gebruikt u de aangepaste scriptextensie om het volgende script uit te voeren:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Gebruik voor Azure Resource Manager VM's voer opdrachten uit vanuit de portal om het EnableRemotePS-script uit te voeren:

![Voer de opdracht  uit](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Voer de volgende opdracht uit op basis van de locatie van de clientcomputer:

* Buiten het virtuele netwerk of de implementatie

  * Voer de volgende opdracht uit voor een vm die is gemaakt met behulp van het klassieke implementatiemodel:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Voeg voor een VM azure resource manager eerst een DNS-naam toe aan het openbare IP-adres. Zie [Een volledig gekwalificeerde domeinnaam maken in de Azure-portal voor een Windows-vm voor](../windows/portal-create-fqdn.md)gedetailleerde stappen. Voer daarna de volgende opdracht uit:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Voer de volgende opdracht uit in het virtuele netwerk of de volgende implementatie:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Als u de SkipCaCheck-vlag instelt, wordt de vereiste om een certificaat naar de VM te importeren bij het starten van de sessie omzeild.

U de cmdlet Aanroepen om een script op afstand op de VM uit te voeren.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Extern register

>[!NOTE]
>TCP-poort 135 of 445 moet open zijn om deze optie te kunnen gebruiken.
>
>Voor Azure Resource Manager VM's moet u poort 5986 openen op de NSG. Zie Beveiligingsgroepen voor meer informatie. 
>
>Voor RDFE VM's moet u een eindpunt hebben met een privéhaven 5986 en een openbare haven. Je moet ook die openbare poort op de NSG openen.

1. Open de registereditor (regedit.exe) vanuit een andere virtuele virtuele netwerk.

2. Selecteer **Netwerkregister Bestandsverbinding** > **selecteren**.

   ![Registereditor](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Zoek de doel-VM op **hostnaam** of **dynamisch IP** (bij voorkeur) door deze in te voeren in het vak **De objectnaam invoeren om te selecteren.**

   ![Voer de objectnaam in om het vak te selecteren](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Voer de referenties voor de doel-VM in.

5. Breng de nodige registerwijzigingen aan.

## <a name="remote-services-console"></a>Console voor externe services

>[!NOTE]
>TCP-poorten 135 of 445 moeten open zijn om deze optie te kunnen gebruiken.
>
>Voor Azure Resource Manager VM's moet u poort 5986 openen op de NSG. Zie Beveiligingsgroepen voor meer informatie. 
>
>Voor RDFE VM's moet u een eindpunt hebben met een privéhaven 5986 en een openbare haven. Je moet ook die openbare poort op de NSG openen.

1. Open vanaf een andere VM op hetzelfde virtuele netwerk een exemplaar van **Services.msc**.

2. Klik met de rechtermuisknop op **Services (Lokaal).**

3. Selecteer **Verbinding maken met een andere computer**.

   ![Externe service](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Voer het dynamische IP-adres van de doel-vm in.

   ![Dynamische IP-invoer](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Breng de nodige wijzigingen aan in de services.

## <a name="next-steps"></a>Volgende stappen

- Zie Enter-PSSession voor meer informatie over de cmdlet [Enter-PSSession.](https://technet.microsoft.com/library/hh849707.aspx)
- Zie [Aangepaste scriptextensie voor Windows voor](../extensions/custom-script-classic.md)meer informatie over de aangepaste scriptextensie voor Windows.
- PsExec is onderdeel van de [PSTools Suite.](https://download.sysinternals.com/files/PSTools.zip)
- Zie [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)voor meer informatie over de PSTools Suite.


