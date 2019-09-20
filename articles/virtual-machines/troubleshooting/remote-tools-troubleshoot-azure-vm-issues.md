---
title: Externe hulpprogram ma's gebruiken voor het oplossen van problemen met virtuele Azure-machines | Microsoft Docs
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
ms.openlocfilehash: 70d777fe2e939c1871bc318eed439214fd3e3f60
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155734"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Externe hulpprogram ma's gebruiken voor het oplossen van problemen met Azure VM

Wanneer u problemen met een virtuele Azure-machine (VM) oplost, kunt u verbinding maken met de VM met behulp van de externe hulpprogram ma's die in dit artikel worden besproken in plaats van Remote Desktop Protocol (RDP) te gebruiken.

## <a name="serial-console"></a>Seriële console

Gebruik de [seriële console van de virtuele machine](serial-console-windows.md) om opdrachten uit te voeren op de externe Azure-VM.

## <a name="remote-cmd"></a>Externe CMD

Down load [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Maak verbinding met de virtuele machine door de volgende opdracht uit te voeren:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* De opdracht moet worden uitgevoerd op een computer die zich in hetzelfde VNET bevindt.
>* Dip of hostnaam kan worden gebruikt om computer \<> te vervangen.
>* De para meter-s zorgt ervoor dat de opdracht wordt aangeroepen met behulp van systeem account (Administrator-machtiging).
>* PsExec maakt gebruik van TCP-poorten 135 en 445. Daarom moeten de twee poorten zijn geopend op de firewall.

## <a name="run-commands"></a>Uitvoeropdrachten

Zie [Power shell-scripts uitvoeren in uw Windows-VM met de opdracht uitvoeren](../windows/run-command.md) voor meer informatie over het gebruik van de functie opdrachten uitvoeren om scripts uit te voeren op de virtuele machine.

## <a name="customer-script-extension"></a>Script uitbreiding van klanten

U kunt de functie aangepaste script extensie gebruiken om een aangepast script uit te voeren op de doel-VM. Als u deze functie wilt gebruiken, moet aan de volgende voor waarden worden voldaan:

* De virtuele machine heeft connectiviteit.

* Azure agent is geïnstalleerd en werkt zoals verwacht op de VM.

* De uitbrei ding is niet eerder geïnstalleerd op de virtuele machine.
 
  Met de uitbrei ding wordt het script alleen de eerste keer dat deze wordt gebruikt, ingevoegd. Als u deze functie later gebruikt, wordt door de uitbrei ding herkend dat deze al is gebruikt en zal het nieuwe script niet worden geüpload.

U moet uw script uploaden naar een opslag account en een eigen container genereren. Voer vervolgens het volgende script uit in Azure PowerShell op een computer die verbinding met de virtuele machine heeft.

### <a name="for-v1-vms"></a>Voor v1-Vm's

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>Voor v2-Vm's

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Externe Power shell

>[!Note]
>TCP-poort 5986 (HTTPS) moet zijn geopend, zodat u deze optie kunt gebruiken.
>
>Voor ARM-Vm's moet u poort 5986 openen voor de netwerk beveiligings groep (NSG). Zie beveiligings groepen voor meer informatie. 
>
>Voor virtuele machines van RDFE moet u een eind punt hebben met een particuliere poort (5986) en een open bare poort. Vervolgens moet u die open bare poort ook openen op de NSG.

### <a name="set-up-the-client-computer"></a>De client computer instellen

Als u Power shell wilt gebruiken om op afstand verbinding te maken met de virtuele machine, moet u eerst de client computer instellen om de verbinding toe te staan. U doet dit door de virtuele machine toe te voegen aan de lijst met vertrouwde hosts van Power shell door de volgende opdracht uit te voeren, indien van toepassing.

Een VM toevoegen aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Meerdere Vm's toevoegen aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Alle computers toevoegen aan de lijst met vertrouwde hosts:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>RemotePS inschakelen op de VM

Gebruik voor klassieke Vm's aangepaste script extensie om het volgende script uit te voeren:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Voor ARM-Vm's gebruikt u de opdrachten uitvoeren vanuit de portal om het EnableRemotePS-script uit te voeren:

![Run-opdracht](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Voer de volgende opdracht uit, afhankelijk van de locatie van de client computer:

* Buiten het VNET of de implementatie

  * Voer de volgende opdracht uit voor een klassieke virtuele machine:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Voor een ARM-VM voegt u eerst een DNS-naam toe aan het open bare IP-adres. Zie [een Fully Qualified Domain name maken in de Azure portal voor een Windows-VM](../windows/portal-create-fqdn.md)voor gedetailleerde stappen. Voer daarna de volgende opdracht uit:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Voer in het VNET of de implementatie de volgende opdracht uit:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Als u de vlag SkipCaCheck instelt, wordt de vereiste voor het importeren van een certificaat naar de VM tijdens het starten van de sessie omzeild.

U kunt ook de cmdlet invoke-opdracht gebruiken om op afstand een script uit te voeren op de virtuele machine:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Extern REGI ster

>[!Note]
>TCP-poort 135 of 445 moet geopend zijn om deze optie te kunnen gebruiken.
>
>Voor ARM-Vm's moet u poort 5986 openen op de NSG. Zie beveiligings groepen voor meer informatie. 
>
>Voor virtuele machines van RDFE moet u een eind punt hebben met een particuliere poort 5986 en een open bare poort. U moet de open bare poort ook openen op de NSG.

1. Open de REGI ster-editor (Regedit. exe) van een andere VM op hetzelfde VNET.

2. Selecteer **bestand** >**Connect netwerk Registry**.

   ![Externe optie](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Zoek de doel-VM door de **hostnaam** of het **dynamische IP-adres** (voor keur) door deze in te voeren in het vak Geef de object naam op om te selecteren.

   ![Externe optie](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Voer de referenties in voor de doel-VM.

5. Breng de benodigde wijzigingen aan in het REGI ster.

## <a name="remote-services-console"></a>Externe Services-console

>[!Note]
>TCP-poorten 135 of 445 moeten zijn geopend om deze optie te kunnen gebruiken.
>
>Voor ARM-Vm's moet u poort 5986 openen op de NSG. Zie beveiligings groepen voor meer informatie. 
>
>Voor virtuele machines van RDFE moet u een eind punt hebben met een particuliere poort 5986 en een open bare poort. Vervolgens moet u die open bare poort ook openen op de NSG.

1. Open vanuit een andere VM op hetzelfde VNET een exemplaar van **Services. msc**.

2. Klik met de rechter muisknop op **Services (lokaal)** .

3. Selecteer **verbinding maken met een andere computer**.

   ![Externe service](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Voer het dynamische IP-adres van de doel-VM in.

   ![Invoer DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Breng de benodigde wijzigingen aan in de services.

## <a name="next-steps"></a>Volgende stappen

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Aangepaste script extensie voor Windows met behulp van het klassieke implementatie model](../extensions/custom-script-classic.md)

PsExec maakt deel uit van de [PSTools-Suite](https://download.sysinternals.com/files/PSTools.zip).

Zie [PSTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools)(Engelstalig) voor meer informatie over de PSTools-Suite.


