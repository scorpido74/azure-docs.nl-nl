---
title: BitLocker-opstartfouten oplossen op een Azure VM | Microsoft Documenten
description: Meer informatie over het oplossen van BitLocker-opstartfouten in een Azure VM
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2019
ms.author: genli
ms.openlocfilehash: 80fd91106530c0150a85d508b24041b2263da925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250008"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>BitLocker-opstartfouten op een Azure-vm

 In dit artikel worden BitLocker-fouten beschreven die u ondervinden wanneer u een virtuele Windows-machine (VM) start in Microsoft Azure.

 

## <a name="symptom"></a>Symptoom

 Een Windows-vm wordt niet gestart. Wanneer u de schermafbeeldingen in het venster [Diagnostische gegevens opstart](../windows/boot-diagnostics.md) controleert, ziet u een van de volgende foutmeldingen:

- Sluit het USB-stuurprogramma aan met de BitLocker-toets

- Je bent buitengesloten. Voer de herstelsleutel in om weer aan de slag te gaan (Toetsenbordindeling: VS) De verkeerde aanmeldingsgegevens zijn te vaak ingevoerd, dus uw pc is vergrendeld om uw privacy te beschermen. Als u de herstelsleutel https://windows.microsoft.com/recoverykeyfaq wilt ophalen, gaat u naar een andere pc of mobiel apparaat. In het geval dat je het nodig hebt, de sleutel-ID is XXXXXXX. U uw pc ook resetten.

- Voer het wachtwoord in om dit station te ontgrendelen [ ] Druk op de invoegtoets om het wachtwoord te zien terwijl u typt.
- Voer uw herstelsleutel in Laad uw herstelsleutel vanaf een USB-apparaat.

## <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de VM het BEK-bestand (BitLocker Recovery Key) niet kan vinden om de versleutelde schijf te decoderen.

## <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, stopt en deallocateert u de VM en start u deze opnieuw op. Deze bewerking dwingt de VM om het BEK-bestand uit de Azure Key Vault op te halen en vervolgens op de versleutelde schijf te plaatsen. 

Als deze methode het probleem niet oplost, voert u de volgende stappen uit om het BEK-bestand handmatig te herstellen:

1. Maak een momentopname van de systeemschijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .
2. [Koppel de systeemschijf aan een herstelvm](troubleshoot-recovery-disks-portal-windows.md). Als u de opdracht [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) in stap 7 wilt uitvoeren, moet de functie **BitLocker-stationsversleuteling** zijn ingeschakeld in de herstel-vm.

    Wanneer u een beheerde schijf koppelt, ontvangt u mogelijk een foutbericht 'bevat versleutelingsinstellingen en kan daarom niet worden gebruikt als gegevensschijf'. Voer in deze situatie het volgende script uit om opnieuw te proberen de schijf te bevestigen:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     U een beheerde schijf niet koppelen aan een vm die is hersteld vanuit een blobafbeelding.

3. Nadat de schijf is aangesloten, maakt u een externe bureaubladverbinding met de herstel-vm, zodat u sommige Azure PowerShell-scripts uitvoeren. Zorg ervoor dat u de [nieuwste versie van Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op de herstel-vm hebt geïnstalleerd.

4. Open een verhoogde Azure PowerShell-sessie (Uitvoeren als beheerder). Voer de volgende opdrachten uit om u aan te melden bij een Azure-abonnement:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Voer het volgende script uit om de naam van het BEK-bestand te controleren:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Het volgende is een voorbeeld van de uitvoer. Zoek de BEK-bestandsnaam voor de bijgevoegde schijf. In dit geval gaan we ervan uit dat de schijfletter van de bijgevoegde schijf F is en het BEK-bestand EF7B2F5A-50C6-4637-9F13-7F599C12F85C. BEK, IK HEB JE NOG NIET EEN

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Als u twee dubbele volumes ziet, is het volume met de nieuwere tijdstempel het huidige BEK-bestand dat wordt gebruikt door de herstel-vm.

    Als de waarde **Inhoudstype** **IS WRAPPEd BEK**is, gaat u naar de [KEK-scenario's (Key Encryption Key).](#key-encryption-key-scenario)

    Nu u de naam van het BEK-bestand voor het station hebt, moet u de geheime bestandsnaam maken. BEK-bestand om het station te ontgrendelen.

6.  Download het BEK-bestand naar de herstelschijf. In het volgende voorbeeld wordt het BEK-bestand opgeslagen in de map C:\BEK. Controleer of `C:\BEK\` het pad bestaat voordat u de scripts uitvoert.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Voer de volgende opdracht uit om de gekoppelde schijf te ontgrendelen met het BEK-bestand.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    In dit voorbeeld is de aangesloten OS-schijf station F. Zorg ervoor dat u de juiste stationsletter gebruikt. 

8. Nadat de schijf met succes is ontgrendeld met behulp van de BEK-toets, maakt u de schijf los van de herstel-vm en maakt u de VM opnieuw met behulp van deze nieuwe OS-schijf.

    > [!NOTE]
    > Het verwisselen van OS Disk wordt niet ondersteund voor VM's met schijfversleuteling.

9. Als de nieuwe virtuele machine nog steeds niet normaal kan worden opgestart, probeert u een van de volgende stappen nadat u het station hebt ontgrendeld:

    - De beveiliging onderbreken om BitLocker TIJDELIJK uit te schakelen door het volgende uit te voeren:

                    manage-bde -protectors -disable F: -rc 0
           
    - Volledig ontsleutelen van de drive. Voer hiervoor de volgende opdracht uit:

                    manage-bde -off F:

### <a name="key-encryption-key-scenario"></a>Scenario sleutelversleutelingssleutel

Voer de volgende stappen uit voor een sleutelversleutelingssleutelscenario:

1. Zorg ervoor dat het ingelogde gebruikersaccount de machtiging 'uitgepakt' vereist heeft in het beleid voor key vault access in **de GEBRUIKER| Belangrijkste machtigingen| Cryptografische bewerkingen| Toets uitpakken**.
2. Sla het volgende script op in een . PS1-bestand:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Stel de parameters in. Het script verwerkt het KEK-geheim om de BEK-toets te maken en slaat deze vervolgens op in een lokale map op de herstelvm. Zie de sectie [Scriptprobleemoplossing](#script-troubleshooting) als u fouten ontvangt wanneer u het script uitvoert.

4. U ziet de volgende uitvoer wanneer het script begint:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Wanneer het script is voltooid, ziet u de volgende uitvoer:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Voer de volgende opdracht uit om de gekoppelde schijf te ontgrendelen met het BEK-bestand:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    In dit voorbeeld is de aangesloten OS-schijf station F. Zorg ervoor dat u de juiste stationsletter gebruikt. 

6. Nadat de schijf met succes is ontgrendeld met behulp van de BEK-toets, maakt u de schijf los van de herstel-vm en maakt u de VM opnieuw met behulp van deze nieuwe OS-schijf. 

    > [!NOTE]
    > Het verwisselen van OS Disk wordt niet ondersteund voor VM's met schijfversleuteling.

7. Als de nieuwe virtuele machine nog steeds niet normaal kan worden opgestart, probeert u een van de volgende stappen nadat u het station hebt ontgrendeld:

    - De beveiliging onderbreken om BitLocker TIJDELIJK uit te schakelen door de volgende opdracht uit te voeren:

             manage-bde -protectors -disable F: -rc 0
           
    - Volledig ontsleutelen van de drive. Voer hiervoor de volgende opdracht uit:

                    manage-bde -off F:
## <a name="script-troubleshooting"></a>Scriptprobleemoplossing

**Fout: kan bestand of verzameling niet laden**

Deze fout treedt op omdat de paden van de ADAL-assemblages verkeerd zijn. Als de AZ-module alleen voor de huidige gebruiker is geïnstalleerd, bevinden de ADAL-assemblages zich in `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>`.

U ook `Az.Accounts` zoeken naar map om het juiste pad te vinden.

**Fout: Get-AzKeyVaultSecret of Get-AzKeyVaultSecret wordt niet herkend als de naam van een cmdlet**

Als u de oude AZ PowerShell-module gebruikt, moet `Get-AzureKeyVaultSecret` `Get-AzureKeyVaultSecret`u de twee opdrachten wijzigen in en .

**Parametersmonsters**

| Parameters  | Voorbeeld van waarde  |Opmerkingen   |
|---|---|---|
|  $keyVaultName | myKeyVault212852926  | De naam van de sleutelkluis die de sleutel opslaat |
|$kekName   |mykey mykey   | De naam van de sleutel die wordt gebruikt om de VM te versleutelen|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | De naam van het geheim van de VM-toets|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK BEK |Het pad voor het schrijven van BEK-bestand.|
|$adTenant  |contoso.onmicrosoft.com   | FQDN of GUID van uw Azure Active Directory die de sleutelkluis host |
