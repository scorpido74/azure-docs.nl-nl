---
title: De groep Always On beschikbaarheid configureren op een Azure VM met PowerShell | Microsoft Documenten
description: In deze zelfstudie worden resources gebruikt die zijn gemaakt met het klassieke implementatiemodel. U gebruikt PowerShell om een groep Always On-beschikbaarheid in Azure te maken.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: ba6f1300353247ef2de99b2bd903bc82665d9a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978145"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>De groep Always On-beschikbaarheid configureren op een Azure VM met PowerShell
> [!div class="op_single_selector"]
> * [Klassiek: Gebruikersinterface](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klassiek: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Voordat u begint, moet u er rekening mee houden dat u deze taak nu voltooien in het Azure-resourcemanagermodel. We raden Azure resource manager-model aan voor nieuwe implementaties. Zie [SQL Server Always On availability groups op Azure virtuele machines](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> We raden u aan dat de meeste nieuwe implementaties het Resource Manager-model gebruiken. Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en klassiek.](../../../azure-resource-manager/management/deployment-models.md) Dit artikel gaat over het gebruik van het klassieke implementatiemodel.

Azure virtual machines (VM's) kunnen databasebeheerders helpen om de kosten van een SQL Server-systeem met hoge beschikbaarheid te verlagen. In deze zelfstudie ziet u hoe u een beschikbaarheidsgroep implementeert met SQL Server Always On end-to-end in een Azure-omgeving. Aan het einde van de zelfstudie bestaat uw SQL Server Always On-oplossing in Azure uit de volgende elementen:

* Een virtueel netwerk dat meerdere subnetten bevat, waaronder een front-end en een back-end subnet.
* Een domeincontroller met een Active Directory-domein.
* Twee SQL Server VM's die zijn geïmplementeerd op het back-end subnet en zijn verbonden met het Active Directory-domein.
* Een Windows-failovercluster met drie knooppunten met het quorummodel Knooppuntmeerderheid.
* Een beschikbaarheidsgroep met twee synchrone commit replica's van een beschikbaarheidsdatabase.

Dit scenario is een goede keuze voor de eenvoud op Azure, niet voor de kosteneffectiviteit of andere factoren. U bijvoorbeeld het aantal VM's voor een groep voor beschikbaarheid met twee replica's minimaliseren om te besparen op rekenuren in Azure door de domeincontroller te gebruiken als getuige van de quorumbestandsshare in een failovercluster met twee kopjes. Met deze methode wordt het aantal vm's met één verminderd ten opzichte van de bovenstaande configuratie.

Deze zelfstudie is bedoeld om u de stappen te laten zien die nodig zijn om de hierboven beschreven oplossing in te stellen, zonder de details van elke stap uit te werken. Daarom, in plaats van het verstrekken van de GUI-configuratie stappen, het maakt gebruik van PowerShell scripting om u snel door elke stap. In deze zelfstudie wordt het volgende aangenomen:

* U hebt al een Azure-account met het abonnement op de virtuele machine.
* U hebt de [Azure PowerShell-cmdlets](/powershell/azure/overview)geïnstalleerd.
* U hebt al een goed begrip van Always On beschikbaarheidsgroepen voor on-premises oplossingen. Zie [Beschikbaarheidsgroepen (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)voor meer informatie.

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Verbinding maken met uw Azure-abonnement en het virtuele netwerk maken
1. Importeer in een PowerShell-venster op uw lokale computer de Azure-module, download het publicatie-instellingenbestand naar uw machine en verbind uw PowerShell-sessie met uw Azure-abonnement door de gedownloade publicatie-instellingen te importeren.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    De opdracht **Get-AzurePublishSettingsFile** genereert automatisch een beheercertificaat met Azure en downloadt het naar uw machine. Een browser wordt automatisch geopend en u wordt gevraagd de Microsoft-accountreferenties voor uw Azure-abonnement in te voeren. Het gedownloade **bestand .publishsettings** bevat alle informatie die u nodig hebt om uw Azure-abonnement te beheren. Nadat u dit bestand hebt opgeslagen in een lokale map, importeert u het met de opdracht **Import-AzurePublishSettingsFile.**

   > [!NOTE]
   > Het bestand .publishsettings bevat uw referenties (ongecodeerd) die worden gebruikt om uw Azure-abonnementen en -services te beheren. De beste beveiligingstoepassing voor dit bestand is om het tijdelijk op te slaan buiten uw bronmappen (bijvoorbeeld in de map Bibliotheken\Documenten) en deze vervolgens te verwijderen nadat de import is voltooid. Een kwaadwillende gebruiker die toegang krijgt tot het bestand .publishsettings kan uw Azure-services bewerken, maken en verwijderen.

2. Definieer een reeks variabelen die u gebruikt om uw it-infrastructuur in de cloud te maken.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Let op het volgende om ervoor te zorgen dat uw opdrachten later slagen:

   * Variabelen **$storageAccountName** en **$dcServiceName** moeten uniek zijn omdat ze worden gebruikt om respectievelijk uw cloudopslagaccount en cloudserver op internet te identificeren.
   * De namen die u opgeeft voor variabelen **$affinityGroupName** en **$virtualNetworkName** zijn geconfigureerd in het virtuele netwerkconfiguratiedocument dat u later zult gebruiken.
   * **$sqlImageName** de bijgewerkte naam op van de VM-afbeelding die SQL Server 2012 Service Pack 1 Enterprise Edition bevat.
   * Voor de eenvoud is **Contoso!000** hetzelfde wachtwoord dat gedurende de hele zelfstudie wordt gebruikt.

3. Maak een affiniteitsgroep.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Maak een virtueel netwerk door een configuratiebestand te importeren.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Het configuratiebestand bevat het volgende XML-document. In het kort, het specificeert een virtueel netwerk genaamd **ContosoNET** in de affiniteit groep genaamd **ContosoAG**. Het heeft de adresruimte **10.10.0.0/16** en heeft twee subnetten, **respectievelijk 10.10.1.0/24** en **10.10.2.0/24**, die respectievelijk het subnet aan de voorzijde en het achtersubnet zijn. Het voorste subnet is de plaats waar u clienttoepassingen zoals Microsoft SharePoint plaatsen. Het achtersubnet is waar u de SQL Server VM's plaatst. Als u de **$affinityGroupName** en **$virtualNetworkName** variabelen eerder wijzigt, moet u ook de bijbehorende namen hieronder wijzigen.

        <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Maak een opslagaccount dat is gekoppeld aan de affiniteitsgroep die u hebt gemaakt en stel het in als het huidige opslagaccount in uw abonnement.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Maak de domeincontrollerserver in de nieuwe cloudservice- en beschikbaarheidsset.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Deze piped commando's doen de volgende dingen:

   * **Nieuw-AzureVMConfig** maakt een VM-configuratie.
   * **Add-AzureProvisioningConfig** geeft de configuratieparameters van een zelfstandige Windows-server.
   * **Add-AzureDataDisk** voegt de gegevensschijf toe die u gebruikt voor het opslaan van Active Directory-gegevens, waarbij de caching-optie is ingesteld op Geen.
   * **Nieuw-AzureVM** maakt een nieuwe cloudservice en maakt de nieuwe Azure VM in de nieuwe cloudservice.

7. Wacht tot de nieuwe virtuele machine volledig is ingericht en download het externe bureaubladbestand naar uw werkmap. Omdat de nieuwe Azure VM lang duurt `while` om in te richten, blijft de lus de nieuwe VM peilen totdat deze klaar is voor gebruik.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

De server met domeincontroller is nu ingericht. Vervolgens configureert u het Active Directory-domein op deze domeincontrollerserver. Laat het PowerShell-venster open op uw lokale computer. U gebruikt het later opnieuw om de twee SQL Server VM's te maken.

## <a name="configure-the-domain-controller"></a>De domeincontroller configureren
1. Maak verbinding met de server van de domeincontroller door het extern bureaublad-bestand te starten. Gebruik de gebruikersnaam AzureAdmin van de machinebeheerder en het wachtwoord **Contoso!000**, die u hebt opgegeven toen u de nieuwe vm maakte.
2. Open een PowerShell-venster in de beheerdersmodus.
3. Voer de volgende **DCPROMO uit. EXE-opdracht** om het **corp.contoso.com** domein in te stellen, met de gegevensmappen op station M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Nadat de opdracht is voltooid, wordt de VM automatisch opnieuw opgestart.

4. Maak opnieuw verbinding met de server van de domeincontroller door het externe bureaubladbestand te starten. Meld u deze keer aan als **CORP\Administrator.**
5. Open een PowerShell-venster in de beheerdersmodus en importeer de Active Directory PowerShell-module met de volgende opdracht:

        Import-Module ActiveDirectory

6. Voer de volgende opdrachten uit om drie gebruikers aan het domein toe te voegen.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** wordt gebruikt om alles te configureren dat verband houdt met de SQL Server-serviceexemplaren, het failovercluster en de beschikbaarheidsgroep. **CORP\SQLSvc1** en **CORP\SQLSvc2** worden gebruikt als SQL Server-serviceaccounts voor de twee SQL Server VM's.
7. Voer vervolgens de volgende opdrachten uit om **CORP\Installeer** de machtigingen om computerobjecten in het domein te maken.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    De hierboven opgegeven GUID is de GUID voor het type computerobject. Het **CORP\Installatieaccount** heeft de machtiging **Alle eigenschappen lezen** en **Computerobjecten maken** nodig om de Active Direct-objecten voor het failovercluster te maken. De machtiging **Alle eigenschappen lezen** is al standaard aan CORP\Installeren gegeven, dus u hoeft deze niet expliciet toe te kennen. Zie [Failovercluster stapsgewijze handleiding: Accounts configureren in Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx)voor meer informatie over machtigingen die nodig zijn om het failovercluster te maken.

    Nu u active directory en de gebruikersobjecten hebt geconfigureerd, maakt u twee SQL Server VM's en sluit u deze aan bij dit domein.

## <a name="create-the-sql-server-vms"></a>De SQL Server VM's maken
1. Blijf het PowerShell-venster gebruiken dat is geopend op uw lokale computer. Definieer de volgende aanvullende variabelen:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    Het IP-adres **10.10.0.4** wordt doorgaans toegewezen aan de eerste vm die u maakt in het subnet **10.10.0.0/16** van uw virtuele Azure-netwerk. U moet controleren of dit het adres van uw domeincontrollerserver is door **IPCONFIG**uit te voeren.
2. Voer de volgende pipeteopdrachten uit om de eerste VM in het failovercluster te maken, genaamd **ContosoQuorum:**

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Let op het volgende met betrekking tot de opdracht hierboven:

   * **Nieuw-AzureVMConfig** maakt een VM-configuratie met de gewenste naam van de beschikbaarheidsset. De volgende VM's worden gemaakt met dezelfde beschikbaarheidssetnaam, zodat ze worden gekoppeld aan dezelfde beschikbaarheidsset.
   * **Add-AzureProvisioningConfig** voegt de VM toe aan het Active Directory-domein dat u hebt gemaakt.
   * **Set-AzureSubnet** plaatst de VM in het achtersubnet.
   * **Nieuw-AzureVM** maakt een nieuwe cloudservice en maakt de nieuwe Azure VM in de nieuwe cloudservice. De parameter **DnsSettings** geeft aan dat de DNS-server voor de servers in de nieuwe cloudservice het IP-adres **10.10.0.4**heeft. Dit is het IP-adres van de domeincontrollerserver. Deze parameter is nodig om de nieuwe VM's in de cloudservice in staat te stellen zich met succes aan te sluiten bij het Active Directory-domein. Zonder deze parameter moet u handmatig de IPv4-instellingen in uw VM instellen om de domeincontrollerserver te gebruiken als de primaire DNS-server nadat de VM is ingericht, en vervolgens de VM aansluiten bij het Active Directory-domein.
3. Voer de volgende piped-opdrachten uit om de SQL Server VM's te maken, genaamd **ContosoSQL1** en **ContosoSQL2.**

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Let op het volgende met betrekking tot de bovenstaande opdrachten:

   * **Nieuw-AzureVMConfig** gebruikt dezelfde beschikbaarheidssetnaam als de server van de domeincontroller en gebruikt de SQL Server 2012 Service Pack 1 Enterprise Edition-afbeelding in de galerie met virtuele machines. Het stelt ook het besturingssysteem schijf op read-caching alleen (geen schrijven caching). We raden u aan de databasebestanden te migreren naar een afzonderlijke gegevensschijf die u aan de vm koppelt en deze te configureren zonder te lezen of te schrijven. Echter, de volgende beste ding is het verwijderen van schrijf caching op het besturingssysteem schijf, omdat je niet verwijderen lees caching op het besturingssysteem schijf.
   * **Add-AzureProvisioningConfig** voegt de VM toe aan het Active Directory-domein dat u hebt gemaakt.
   * **Set-AzureSubnet** plaatst de VM in het achtersubnet.
   * **Add-AzureEndpoint** voegt access endpoints toe zodat clienttoepassingen toegang hebben tot deze SQL Server-services-exemplaren op internet. Verschillende poorten worden gegeven aan ContosoSQL1 en ContosoSQL2.
   * **Nieuw-AzureVM** maakt de nieuwe SQL Server VM in dezelfde cloudservice als ContosoQuorum. U moet de VM's in dezelfde cloudservice plaatsen als u wilt dat ze in dezelfde beschikbaarheidsset zijn.
4. Wacht tot elke vm volledig is ingericht en dat elke virtuele machine het externe bureaubladbestand downloadt naar uw werkmap. De `for` lus cycli door de drie nieuwe VM's en voert de commando's in de top-level krullende beugels voor elk van hen.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    De SQL Server VM's zijn nu ingericht en uitgevoerd, maar ze zijn geïnstalleerd met SQL Server met standaardopties.

## <a name="initialize-the-failover-cluster-vms"></a>Initialisaliseren van de failovercluster VM's
In deze sectie moet u de drie servers wijzigen die u gebruikt in het failovercluster en de SQL Server-installatie. Met name:

* Alle servers: u moet de functie **Failoverclustering** installeren.
* Alle servers: u moet **CORP\Install** als **machinebeheerder**toevoegen.
* Alleen ContosoSQL1 en ContosoSQL2: U moet **CORP\Install** toevoegen als **sysadmin-rol** in de standaarddatabase.
* Alleen ContosoSQL1 en ContosoSQL2: U moet **NT AUTHORITY\System** toevoegen als aanmelding met de volgende machtigingen:

  * Elke beschikbaarheidsgroep wijzigen
  * SQL verbinden
  * Serverstatus weergeven
* Alleen ContosoSQL1 en ContosoSQL2: het **TCP-protocol** is al ingeschakeld op de SQL Server VM. U moet echter nog steeds de firewall openen voor externe toegang tot SQL Server.

Nu ben je klaar om te beginnen. Volg de onderstaande stappen vanaf **ContosoQuorum:**

1. Maak verbinding met **ContosoQuorum** door de externe bureaubladbestanden te starten. Gebruik de gebruikersnaam **AzureAdmin** van de machinebeheerder en het wachtwoord **Contoso!000**, die u hebt opgegeven toen u de VM's hebt gemaakt.
2. Controleer of de computers zijn samengevoegd met **corp.contoso.com**.
3. Wacht tot de SQL Server-installatie is voltooid met het uitvoeren van de geautomatiseerde initialisatietaken voordat u verdergaat.
4. Open een PowerShell-venster in de beheerdersmodus.
5. Installeer de functie Windows Failover Clustering.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. **Corp\Installeren** als lokale beheerder toevoegen.

        net localgroup administrators "CORP\Install" /Add
7. Meld u af bij ContosoQuorum. Je bent nu klaar met deze server.

        logoff.exe

Initialiseer **Vervolgens ContosoSQL1** en **ContosoSQL2**. Volg de onderstaande stappen, die identiek zijn voor beide SQL Server VM's.

1. Maak verbinding met de twee SQL Server VM's door de externe bureaubladbestanden te starten. Gebruik de gebruikersnaam **AzureAdmin** van de machinebeheerder en het wachtwoord **Contoso!000**, die u hebt opgegeven toen u de VM's hebt gemaakt.
2. Controleer of de computers zijn samengevoegd met **corp.contoso.com**.
3. Wacht tot de SQL Server-installatie is voltooid met het uitvoeren van de geautomatiseerde initialisatietaken voordat u verdergaat.
4. Open een PowerShell-venster in de beheerdersmodus.
5. Installeer de functie Windows Failover Clustering.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. **Corp\Installeren** als lokale beheerder toevoegen.

        net localgroup administrators "CORP\Install" /Add
7. Importeer de SQL Server PowerShell-provider.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. **Corp\Install** als de sysadmin-rol voor de standaardSQL Server-instantie.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. **NT AUTHORITY\System** toevoegen als aanmelding met de drie hierboven beschreven machtigingen.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Open de firewall voor externe toegang tot SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Meld u af bij beide VM's.

         logoff.exe

Ten slotte bent u klaar om de beschikbaarheidsgroep te configureren. U gebruikt de SQL Server PowerShell-provider om al het werk aan **ContosoSQL1**uit te voeren.

## <a name="configure-the-availability-group"></a>De beschikbaarheidsgroep configureren
1. Maak opnieuw verbinding met **ContosoSQL1** door de externe bureaubladbestanden te starten. In plaats van u aan te melden met het machineaccount, meldt u zich aan met **CORP\Install**.
2. Open een PowerShell-venster in de beheerdersmodus.
3. Definieer de volgende variabelen:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importeer de SQL Server PowerShell-provider.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Wijzig het SQL Server-serviceaccount voor ContosoSQL1 in CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Wijzig het SQL Server-serviceaccount voor ContosoSQL2 in CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Download **CreateAzureFailoverCluster.ps1** van [Failovercluster maken voor always on availability-groepen in Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) naar de lokale werkmap. U gebruikt dit script om u te helpen een functioneel failovercluster te maken. Zie [Hoge beschikbaarheid en noodherstel voor SQL Server in Azure Virtual Machines voor](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)belangrijke informatie over de interactie met Windows Failoverclustering met het Azure-netwerk.
8. Wijzig in uw werkmap en maak het failovercluster met het gedownloade script.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Schakel Always On-beschikbaarheidsgroepen in voor de standaard SQL Server-exemplaren in **ContosoSQL1** en **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Maak een back-upmap en geef machtigingen voor de SQL Server-serviceaccounts. U gebruikt deze map om de beschikbaarheidsdatabase op de secundaire replica voor te bereiden.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Maak een database op **ContosoSQL1** genaamd **MyDB1,** neem zowel een volledige back-up als een logboekback-up en herstel ze op **ContosoSQL2** met de **optie WITH NORECOVERY.**

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Maak de eindpunten van de beschikbaarheidsgroep op de SQL Server VM's en stel de juiste machtigingen in op de eindpunten.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Maak de beschikbaarheidsreplica's.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Maak ten slotte de beschikbaarheidsgroep en sluit u aan bij de secundaire replica voor de beschikbaarheidsgroep.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Volgende stappen
U hebt SQL Server Always On nu geïmplementeerd door een beschikbaarheidsgroep in Azure te maken. Zie [Een ILB-listener configureren voor beschikbaarheidsgroepen](../classic/ps-sql-int-listener.md)altijd in Azure als u een listener voor deze beschikbaarheidsgroep wilt configureren.

Zie [SQL Server op Azure-virtuele machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor andere informatie over het gebruik van SQL Server in Azure.
