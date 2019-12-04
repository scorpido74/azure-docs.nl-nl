---
title: Zelf studie-basis Active Directory on-premises en Azure AD-omgeving.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356a05d4d92f17ceb66ff0208153ec3eac736757
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793896"
---
# <a name="tutorial-basic-active-directory-environment"></a>Zelf studie: Basic Active Directory Environment

In deze zelf studie wordt u begeleid bij het maken van een basis Active Directory-omgeving. 

![Create](media/tutorial-single-forest/diagram1.png)

U kunt de omgeving die u in de zelf studie maakt, gebruiken om verschillende aspecten van hybride identiteits scenario's te testen en is een vereiste voor sommige zelf studies.  Als u al een bestaande Active Directory omgeving hebt, kunt u deze gebruiken als vervanging.  Deze informatie wordt verstrekt voor personen die vanaf niets beginnen.

Deze zelf studie bestaat uit
## <a name="prerequisites"></a>Vereisten
Dit zijn de vereisten voor het voltooien van deze zelfstudie
- Een computer waarop [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) is geïnstalleerd.  Het wordt aangeraden dit te doen op een computer met [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) of [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- Een [externe netwerkadapter](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) waarmee de virtuele machine kan communiceren met internet.
- Een [Azure-abonnement](https://azure.microsoft.com/free)
- Een exemplaar van Windows Server 2016
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> In deze zelfstudie worden PowerShell-scripts gebruikt, zodat u de omgeving voor de zelfstudie zo snel mogelijk kunt inrichten.  Elk van de scripts maakt gebruik van variabelen die worden gedeclareerd aan het begin van de scripts.  U kunt en moet de variabelen aanpassen aan uw omgeving.
>
>De scripts die worden gebruikt, maken een algemene Active Directory omgeving voordat u de Azure AD Connect Cloud inrichtings Agent installeert.  De scripts zijn relevant voor alle zelfstudies.
>
> Kopieën van de PowerShell-scripts die worden gebruikt in deze zelfstudie zijn [hier](https://github.com/billmath/tutorial-phs) beschikbaar op GitHub.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Het eerste wat u moet doen om ervoor te zorgen dat onze Hybrid Identity Environment actief is, is het maken van een virtuele machine die wordt gebruikt als onze on-premises Active Directory server.  Ga als volgt te werk:

1. Open de PowerShell ISE als Administrator.
2. Voer het volgende script uit:

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>Implementatie van het besturingssysteem voltooien
Om het bouwen van de virtuele machine te voltooien, moet u de installatie van het besturingssysteem afronden.

1. Ga naar Hyper-V Manager en dubbelklik op de virtuele machine
2. Klik op de knop Start.
3. U wordt gevraagd om op een willekeurige toets te drukken om op te starten vanaf cd of dvd. Druk op een willekeurige toets.
4. Selecteer uw taal in het opstartscherm van Windows Server en klik op **Volgende**.
5. Klik op **Nu installeren**.
6. Voer uw licentiecode in en klik op **Volgende**.
7. Geef aan dat u akkoord gaat met de licentievoorwaarden en klik op **Volgende**.
8. Selecteer **aangepast: alleen Windows installeren (Geavanceerd)**
9. Klik op **Volgende**
10. Als de installatie is voltooid, start u de virtuele machine opnieuw op, meldt u zich aan en installeert u de beschikbare Windows-updates om er zeker van te zijn dat de VM up-to-date is.  Installeer de laatste updates.

## <a name="install-active-directory-prerequisites"></a>Vereisten voor de installatie van Active Directory Domain Services
Nu u een virtuele machine hebt, moet u enkele dingen doen voordat u Active Directory installeert.  Dat wil zeggen, u moet de naam van de virtuele machine wijzigen, een statisch IP-adres en DNS-informatie instellen en de Remote Server Administration Tools installeren.   Ga als volgt te werk:

1. Open de PowerShell ISE als Administrator.
2. Voer het volgende script uit:

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Een Windows Server AD-omgeving inrichten
Nu u de virtuele machine hebt gemaakt en de naam ervan hebt gewijzigd en een statisch IP-adres hebt, kunt u door gaan en Active Directory Domain Services installeren en configureren.  Ga als volgt te werk:

1. Open de PowerShell ISE als Administrator.
2. Voer het volgende script uit:

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Een Windows Server AD-gebruiker maken
Nu u onze Active Directory omgeving hebt, moet u een test account hebben.  Dit account wordt gemaakt in onze on-premises AD-omgeving en vervolgens gesynchroniseerd met Azure Active Directory.  Ga als volgt te werk:

1. Open de PowerShell ISE als Administrator.
2. Voer het volgende script uit:

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Een Azure Active Directory-tenant maken
U moet nu een Azure AD-Tenant maken zodat u onze gebruikers kunt synchroniseren met de Cloud.  Ga als volgt te werk om een nieuwe Azure Active Directory-tenant te maken.

1. Meld u bij de [Azure Portal](https://portal.azure.com) aan met een account waaraan een Azure-abonnement is gekoppeld.
2. Selecteer het **plusteken (+)** en zoek naar **Azure Active Directory**.
3. Selecteer **Azure Active Directory** in de zoekresultaten.
4. Selecteer **Maken**.</br>
![Maken](media/tutorial-single-forest/create1.png)</br>
5. Geef een **naam op voor de organisatie**, evenals een **oorspronkelijke domeinnaam**. Selecteer vervolgens **Maken**. Er wordt nu een map aangemaakt.
6. Als die klaar is, klikt u op de koppeling **hier** om de adreslijst te beheren.

## <a name="create-a-global-administrator-in-azure-ad"></a>Een globale beheerder maken in Azure Active Directory
Nu u een Azure AD-Tenant hebt, maakt u een algemeen beheerders account.  Ga als volgt te werk om het globale beheerdersaccount te maken.

1.  Onder **Beheren**, selecteer **Gebruikers**.</br>
![Maken](media/tutorial-single-forest/administrator1.png)</br>
2.  Selecteer **Alle gebruikers** en selecteer vervolgens **+ Nieuwe gebruiker**.
3.  Geef een naam en gebruikersnaam op voor deze gebruiker. Dit is de globale beheerder voor de tenant. U moet ook de **Maprol** wijzigen naar **Globale beheerder.** U kunt ook het tijdelijke wachtwoord weergeven. Als u klaar bent, selecteert u **Maken**.</br>
![Maken](media/tutorial-single-forest/administrator2.png)</br>
4. Als de bewerkingen zijn voltooid, opent u een nieuwe webbrowser en meldt u zich met het nieuwe globale beheerdersaccount en het tijdelijke wachtwoord aan bij myapps.microsoft.com.
5. Wijzig het wachtwoord voor de globale beheerder in iets dat u makkelijk kunt onthouden.

## <a name="optional--additional-server-and-forest"></a>Optioneel: extra server en forest
Hier volgt een optionele sectie met stappen voor het maken van een extra server en of forest.  Dit kan worden gebruikt in een aantal geavanceerdere zelf studies zoals [pilot voor Azure AD Connect naar Cloud inrichting](tutorial-pilot-aadc-aadccp.md).

Als u alleen een extra server nodig hebt, kunt u stoppen na de stap- **de virtuele machine maken** en de server toevoegen aan het bestaande domein dat hierboven is gemaakt.  

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Open de PowerShell ISE als Administrator.
2. Voer het volgende script uit:

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>Implementatie van het besturingssysteem voltooien
Om het bouwen van de virtuele machine te voltooien, moet u de installatie van het besturingssysteem afronden.

1. Ga naar Hyper-V Manager en dubbelklik op de virtuele machine
2. Klik op de knop Start.
3. U wordt gevraagd om op een willekeurige toets te drukken om op te starten vanaf cd of dvd. Druk op een willekeurige toets.
4. Selecteer uw taal in het opstartscherm van Windows Server en klik op **Volgende**.
5. Klik op **Nu installeren**.
6. Voer uw licentiecode in en klik op **Volgende**.
7. Geef aan dat u akkoord gaat met de licentievoorwaarden en klik op **Volgende**.
8. Selecteer **aangepast: alleen Windows installeren (Geavanceerd)**
9. Klik op **Volgende**
10. Als de installatie is voltooid, start u de virtuele machine opnieuw op, meldt u zich aan en installeert u de beschikbare Windows-updates om er zeker van te zijn dat de VM up-to-date is.  Installeer de laatste updates.

### <a name="install-active-directory-prerequisites"></a>Vereisten voor de installatie van Active Directory Domain Services
Nu u een virtuele machine hebt, moet u enkele dingen doen voordat u Active Directory installeert.  Dat wil zeggen, u moet de naam van de virtuele machine wijzigen, een statisch IP-adres en DNS-informatie instellen en de Remote Server Administration Tools installeren.   Ga als volgt te werk:

1. Open de PowerShell ISE als Administrator.
2. Voer het volgende script uit:

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Een Windows Server AD-omgeving inrichten
Nu u de virtuele machine hebt gemaakt en de naam ervan hebt gewijzigd en een statisch IP-adres hebt, kunt u door gaan en Active Directory Domain Services installeren en configureren.  Ga als volgt te werk:

1. Open de PowerShell ISE als Administrator.
2. Voer het volgende script uit:

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD envrionment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Een Windows Server AD-gebruiker maken
Nu u onze Active Directory omgeving hebt, moet u een test account hebben.  Dit account wordt gemaakt in onze on-premises AD-omgeving en vervolgens gesynchroniseerd met Azure Active Directory.  Ga als volgt te werk:

1. Open de PowerShell ISE als Administrator.
2. Voer het volgende script uit:

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Conclusie
U hebt nu een omgeving die kan worden gebruikt voor bestaande zelf studies en om aanvullende functies te testen die in de cloud worden ingericht.

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)
