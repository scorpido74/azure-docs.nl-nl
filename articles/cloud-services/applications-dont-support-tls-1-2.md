---
title: Problemen oplossen die worden veroorzaakt door toepassingen die geen ondersteuning bieden voor TLS 1,2 | Microsoft Docs
description: Problemen oplossen die worden veroorzaakt door toepassingen die geen ondersteuning bieden voor TLS 1,2
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: a9d15a94421694583562f433c20413fcc84697c7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270865"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>Problemen oplossen met toepassingen die TLS 1,2 niet ondersteunen
In dit artikel wordt beschreven hoe u de oudere TLS-protocollen (TLS 1,0 en 1,1) inschakelt, en het Toep assen van verouderde coderings suites ter ondersteuning van de extra protocollen op de web-en werk rollen van de Windows Server 2019 Cloud service. 

We begrijpen dat we met de stappen voor het afnemen van TLS 1,0 en TLS 1,1 de oudere protocollen en coderings suites mogelijk moeten ondersteunen totdat ze hun afschaffing kunnen plannen.  We raden u aan om deze verouderde waarden niet opnieuw in te scha kelen. we bieden hulp bij het helpen van klanten. We raden klanten aan om het risico van regressie te evalueren voordat ze de wijzigingen implementeren die in dit artikel worden beschreven. 

> [!NOTE]
> Met releases van versie 6 van gast besturingssystemen wordt TLS 1,2 afgedwongen door 1.0/1.1-code ring uit te scha kelen. 


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>Ondersteuning voor TLS 1,0-, TLS 1,1-en oudere coderings suites verwijderen 
Ter ondersteuning van onze toezeg ging voor het gebruik van de beste versleuteling, kondigde micro soft aan om te beginnen met de migratie van TLS 1,0 en 1,1 in juni van 2017.   Sinds deze eerste aankondiging heeft micro soft ons intentie aangekondigd om Transport Layer Security (TLS) 1,0 en 1,1 standaard in ondersteunde versies van micro soft Edge en Internet Explorer 11 in de eerste helft van 2020 uit te scha kelen.  Vergelijk bare aankondigingen van Apple, Google en Mozilla geven aan in welke richting de branche zich bevindt.   

## <a name="tls-configuration"></a>TLS-configuratie  
De installatie kopie van de Windows Server 2019-Cloud Server is geconfigureerd met TLS 1,0 en TLS 1,1 uitgeschakeld op het niveau van het REGI ster. Dit betekent dat toepassingen die zijn geïmplementeerd in deze versie van Windows en het gebruik van de Windows stack voor TLS-onderhandeling geen TLS 1,0-en TLS 1,1-communicatie toestaan.   

De server wordt ook geleverd met een beperkt aantal coderings suites: 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>Stap 1: het Power shell-script maken om TLS 1,0 en TLS 1,1 in te scha kelen 

Gebruik de volgende code als voor beeld voor het maken van een script waarmee oudere protocollen en coderings suites worden ingeschakeld. Voor de doel einden van deze documentatie krijgt dit script de naam: **TLSsettings. ps1**. Sla dit script op uw lokale bureau blad op voor eenvoudige toegang in latere stappen. 


```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 
 
Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  
 
    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  
 
    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 
 
    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  
 
    $restart  
}  
 
#*************************************************************************************************************** 
 
#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 
 
function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384," 
 
# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA," 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 
 
 return $cipherorder 
} 
 

#*************************************************************************************************************** 
 
 
#********************************************** REGISTRY KEYS **************************************************** 
 
 
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 
 
#*************************************************************************************************************** 
 
$localRegistryPath = @() 
 
# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 
 
#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 
 
# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) {  
   Write-Host "Checking for existing of key: $($localRegistryPath[$i]) Severity Level: Information"
   If (!(Test-Path -Path $localRegistryPath[$i])) {
        New-Item $localRegistryPath [$i] | Out-Null
    Write-Host "Creating key: $($localRegistryPath[$i]) Severity Level: Information"
    }
}  
 
#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 
 
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) { 
    if ($localRegistryPath[$i].Contains("Client") -Or $localRegistryPath[$i].Contains("Server")) { 
      Write-Host "Enabling this key: $($localRegistryPath[$i]) Severity: Information "
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 
 
$cipherlist = @() 
 
# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  
 
if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Host "Creating key: $($CipherSuiteRegKey) Severity: Information "
}  
 
#Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  
Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherlist  
#********************************************* REBOOT ******************************************* 
 
Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4
```

## <a name="step-2-create-a-command-file"></a>Stap 2: een opdracht bestand maken 

Maak een CMD-bestand met de naam **RunTLSSettings. cmd** aan de hand van de onderstaande. Sla dit script op uw lokale bureau blad op voor eenvoudige toegang in latere stappen. 

```cmd
PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1
REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 
EXIT /B 0
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>Stap 3: de opstart taak toevoegen aan de service definitie (csdef) van de functie 

Voeg het volgende code fragment toe aan uw bestaande service definitie bestand. 


```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

Hier volgt een voor beeld waarin zowel de werk rollen als de webrol worden weer gegeven. 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Taske xecutionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-5-add-the-scripts-to-your-cloud-service"></a>Stap 5: de scripts toevoegen aan de Cloud service 

1) Klik in Visual Studio met de rechter muisknop op uw webfunctie
2) Selecteer **Toevoegen**
3) **Bestaand item** selecteren
4) Ga in Verkenner naar het bureau blad waar u de bestanden **TLSsettings. ps1** en **RunTLSSettings. cmd** hebt opgeslagen 
5) Selecteer de twee bestanden om deze aan uw Cloud Services project toe te voegen

## <a name="step-6-enable-copy-to-output-directory"></a>Stap 6: kopiëren naar uitvoermap inschakelen

Om ervoor te zorgen dat de scripts worden geüpload bij elke update die wordt gepusht vanuit Visual Studio, moet de instelling *kopiëren naar uitvoermap* worden ingesteld op *altijd kopiëren*

1) Klik onder uw webrole met de rechter muisknop op RunTLSSettings. cmd
2) **Eigenschappen** selecteren
3) Wijzig op het tabblad Eigenschappen de *Opties kopiëren naar uitvoer Directory* naar *altijd kopiëren* .
4) Herhaal de stappen voor **TLSsettings. ps1**

## <a name="step-7-publish--validate"></a>Stap 7: publiceren & valideren

Nu de bovenstaande stappen zijn voltooid, kunt u de update publiceren naar uw bestaande Cloud service. 

U kunt [SSLLabs](https://www.ssllabs.com/) gebruiken om de TLS-status van uw eind punten te valideren 

 
