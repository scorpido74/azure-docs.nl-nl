---
title: De AlwaysOn-beschikbaarheids groep configureren op een virtuele Azure-machine met behulp van Power shell | Microsoft Docs
description: In deze zelf studie worden resources gebruikt die zijn gemaakt met het klassieke implementatie model. U kunt Power shell gebruiken om een AlwaysOn-beschikbaarheids groep te maken in Azure.
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
ms.openlocfilehash: 7f20d79ea353830b41290c7b91d8d1de2b1b3abe
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014856"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>De AlwaysOn-beschikbaarheids groep configureren op een virtuele Azure-machine met Power shell
> [!div class="op_single_selector"]
> * [Klassiek: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klassiek: Power shell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Bedenk voordat u begint dat u deze taak nu kunt volt ooien in het Azure Resource Manager-model. We raden het Azure Resource Manager-model aan voor nieuwe implementaties. Zie SQL Server AlwaysOn- [beschikbaarheids groepen op virtuele machines van Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).

> [!IMPORTANT]
> Het is raadzaam om de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/management/deployment-models.md). Dit artikel gaat over het gebruik van het klassieke implementatiemodel.

Azure virtual machines (Vm's) kunnen database beheerders helpen bij het verlagen van de kosten van een SQL Server systeem met hoge Beschik baarheid. In deze zelf studie leert u hoe u een beschikbaarheids groep implementeert met behulp van SQL Server altijd op end-to-end in een Azure-omgeving. Aan het einde van de zelf studie bestaan uw SQL Server always on solution in azure uit de volgende elementen:

* Een virtueel netwerk met meerdere subnetten, met inbegrip van een front-end en een back-end-subnet.
* Een domein controller met een Active Directory domein.
* Twee SQL Server Vm's die worden geïmplementeerd naar het back-end-subnet en zijn gekoppeld aan het Active Directory domein.
* Een Windows-failovercluster met drie knoop punten met het quorum model knooppunt meerderheid.
* Een beschikbaarheids groep met twee replica's met synchrone door Voer van een beschikbaarheids database.

Dit scenario is een goede keuze voor de eenvoud op Azure, niet voor de kosten effectiviteit of andere factoren. U kunt bijvoorbeeld het aantal virtuele machines voor een beschikbaarheids groep met twee replica's minimaliseren om de reken uren in azure op te slaan met behulp van de domein controller als de bestandssharewitness voor quorum bestands shares in een failovercluster met twee knoop punten. Met deze methode wordt het aantal VM'S met één van de bovenstaande configuratie verminderd.

Deze zelf studie is bedoeld om u te laten zien welke stappen u moet uitvoeren om de hierboven beschreven oplossing in te stellen, zonder dat u de details van elke stap hoeft op te lossen. Daarom kunt u in plaats van de GUI-configuratie stappen een Power shell-script gebruiken om snel door elke stap te gaan. In deze zelf studie wordt ervan uitgegaan dat het volgende:

* U hebt al een Azure-account met het abonnement van de virtuele machine.
* U hebt de [Azure PowerShell-cmdlets](/powershell/azure/overview)geïnstalleerd.
* U hebt al een solide memorandum van AlwaysOn-beschikbaarheids groepen voor on-premises oplossingen. Zie AlwaysOn [Availability groups (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)voor meer informatie.

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Maak verbinding met uw Azure-abonnement en maak het virtuele netwerk
1. Importeer in een Power shell-venster op uw lokale computer de Azure-module, down load het bestand met publicatie-instellingen naar uw computer en verbind uw Power shell-sessie met uw Azure-abonnement door de gedownloade publicatie-instellingen te importeren.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Met de opdracht **Get-AzurePublishSettingsFile** wordt automatisch een beheer certificaat met Azure gegenereerd en gedownload naar uw computer. Er wordt automatisch een browser geopend en u wordt gevraagd om de Microsoft-account referenties voor uw Azure-abonnement in te voeren. Het gedownloade **. publishsettings** -bestand bevat alle informatie die u nodig hebt om uw Azure-abonnement te beheren. Nadat u dit bestand hebt opgeslagen in een lokale map, importeert u dit met de opdracht **import-AzurePublishSettingsFile** .

   > [!NOTE]
   > Het. publishsettings-bestand bevat uw referenties (niet-versleuteld) die worden gebruikt voor het beheren van uw Azure-abonnementen en-services. De beveiligings best practice voor dit bestand is het opslaan van de sjabloon tijdelijk buiten uw bron mappen (bijvoorbeeld in de map Libraries\Documents) en deze vervolgens verwijderen nadat het importeren is voltooid. Een kwaadwillende gebruiker die toegang tot het. publishsettings-bestand krijgt, kan uw Azure-Services bewerken, maken en verwijderen.

2. Definieer een reeks variabelen die u gaat gebruiken om uw Cloud IT-infra structuur te maken.

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

    Let op het volgende om ervoor te zorgen dat uw opdrachten later worden uitgevoerd:

   * Variabelen **$storageAccountName** en **$dcServiceName** moeten uniek zijn omdat ze worden gebruikt om respectievelijk uw Cloud Storage-account en de Cloud Server op internet te identificeren.
   * De namen die u opgeeft voor variabelen **$affinityGroupName** en **$virtualNetworkName** worden geconfigureerd in het configuratie document van het virtuele netwerk dat u later gaat gebruiken.
   * **$sqlImageName** geeft de bijgewerkte naam van de VM-installatie kopie op die SQL Server 2012 Service Pack 1 ENTER prise Edition bevat.
   * Voor het gemak is **Contoso! 000** hetzelfde als het wacht woord dat wordt gebruikt in de volledige zelf studie.

3. Maak een affiniteits groep.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Een virtueel netwerk maken door een configuratie bestand te importeren.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Het configuratie bestand bevat het volgende XML-document. In korte informatie geeft u een virtueel netwerk met de naam **ContosoNET** op in de affiniteits groep met de naam **ContosoAG**. Het heeft de adres ruimte **10.10.0.0/16** en heeft twee subnetten: **10.10.1.0/24** en **10.10.2.0/24**, die respectievelijk het front-subnet en het back-subnet zijn. Het front-subnet is waar u client toepassingen zoals micro soft share point kunt plaatsen. In het back-subnet plaatst u de SQL Server Vm's. Als u de **$affinityGroupName** en **$virtualNetworkName** variabelen eerder wijzigt, moet u ook de bijbehorende namen wijzigen.

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

5. Maak een opslag account dat is gekoppeld aan de affiniteits groep die u hebt gemaakt en stel deze in als het huidige opslag account in uw abonnement.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Maak de domein controller server in de nieuwe Cloud service en beschikbaarheidsset.

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

    Deze pipede opdrachten doen het volgende:

   * **New-AzureVMConfig** maakt een VM-configuratie.
   * **Add-AzureProvisioningConfig** geeft de configuratie parameters van een zelfstandige Windows-Server.
   * **Add-AzureDataDisk** voegt de gegevens schijf toe die u gebruikt voor het opslaan van Active Directory gegevens, waarbij de optie cache is ingesteld op geen.
   * **New-AzureVM** maakt een nieuwe Cloud service en maakt de nieuwe virtuele machine van Azure in de nieuwe Cloud service.

7. Wacht tot de nieuwe virtuele machine volledig is ingericht en down load het extern bureau blad-bestand naar uw werkmap. Omdat de nieuwe virtuele machine van Azure veel tijd in beslag neemt, `while` blijft de lus de nieuwe VM pollen tot deze klaar is voor gebruik.

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

De domein controller Server is nu ingericht. Vervolgens configureert u het domein Active Directory op deze domein controller Server. Sluit het Power shell-venster geopend op de lokale computer. U gebruikt deze later opnieuw om de twee SQL Server Vm's te maken.

## <a name="configure-the-domain-controller"></a>De domein controller configureren
1. Maak verbinding met de domein controller Server door het extern bureau blad-bestand te starten. Gebruik de gebruikers naam AzureAdmin en het wacht woord **Contoso! 000**dat u hebt opgegeven tijdens het maken van de nieuwe virtuele machine.
2. Open een Power shell-venster in de beheerders modus.
3. Voer de volgende **dcpromo uit. EXE** -opdracht voor het instellen van het domein **Corp.contoso.com** , met de gegevens Directory's op station M.

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

4. Maak opnieuw verbinding met de domein controller Server door het extern bureau blad-bestand te starten. Nu meldt u zich aan als **CORP\Administrator**.
5. Open een Power shell-venster in de beheerders modus en importeer de Active Directory Power shell-module met de volgende opdracht:

        Import-Module ActiveDirectory

6. Voer de volgende opdrachten uit om drie gebruikers toe te voegen aan het domein.

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

    **CORP\Install** wordt gebruikt voor het configureren van alles dat is gerelateerd aan de SQL Server service-exemplaren, het failovercluster en de beschikbaarheids groep. **CORP\SQLSvc1** en **CORP\SQLSvc2** worden gebruikt als de SQL Server-service accounts voor de twee SQL Server vm's.
7. Voer vervolgens de volgende opdrachten uit om **CORP\Install** de machtigingen te geven voor het maken van computer objecten in het domein.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    De hierboven opgegeven GUID is de GUID voor het type computer object. Het **CORP\Install** -account heeft de machtiging **alle eigenschappen lezen** en **computer objecten maken** nodig om de Active direct-objecten voor het failovercluster te maken. De machtiging **alle eigenschappen lezen** wordt standaard al aan CORP\Install gegeven, dus u hoeft deze niet expliciet toe te kennen. Zie [Stapsgewijze hand leiding voor failover-clusters](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx)voor meer informatie over de machtigingen die nodig zijn voor het maken van het failovercluster: accounts configureren in Active Directory.

    Nu u klaar bent met het configureren van Active Directory en de gebruikers objecten, maakt u twee SQL Server Vm's en voegt u deze toe aan dit domein.

## <a name="create-the-sql-server-vms"></a>De SQL Server Vm's maken
1. Ga verder met het Power shell-venster dat is geopend op de lokale computer. Definieer de volgende aanvullende variabelen:

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

    Het IP-adres **10.10.0.4** wordt doorgaans toegewezen aan de eerste virtuele machine die u maakt in het **10.10.0.0/16** -subnet van uw virtuele Azure-netwerk. U moet controleren of dit het adres van uw domein controller Server is door het uitvoeren van **ipconfig**.
2. Voer de volgende opdrachten in de pipes uit om de eerste VM in het failovercluster te maken, met de naam **ContosoQuorum**:

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

    Let op het volgende met betrekking tot de bovenstaande opdracht:

   * **New-AzureVMConfig** maakt een VM-configuratie met de gewenste naam van de beschikbaarheidsset. De volgende Vm's worden gemaakt met dezelfde naam voor de beschikbaarheidsset, zodat ze zijn gekoppeld aan dezelfde beschikbaarheidsset.
   * **Add-AzureProvisioningConfig** voegt de virtuele machine toe aan het Active Directory domein dat u hebt gemaakt.
   * **Set-AzureSubnet** plaatst de virtuele machine in het back-subnet.
   * **New-AzureVM** maakt een nieuwe Cloud service en maakt de nieuwe virtuele machine van Azure in de nieuwe Cloud service. De para meter **DnsSettings** geeft aan dat de DNS-server voor de servers in de nieuwe Cloud service het IP-adres **10.10.0.4**heeft. Dit is het IP-adres van de domein controller Server. Deze para meter is vereist om de nieuwe virtuele machines in de Cloud service in te scha kelen om lid te worden van het Active Directory domein. Als u deze para meter niet opgeeft, moet u de IPv4-instellingen in uw virtuele machine hand matig instellen op het gebruik van de domein controller Server als de primaire DNS-server nadat de virtuele machine is ingericht, en vervolgens de virtuele machine toevoegen aan het Active Directory domein.
3. Voer de volgende opdrachten uit om de SQL Server Vm's met de naam **ContosoSQL1** en **ContosoSQL2**te maken.

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

   * **New-AzureVMConfig** gebruikt dezelfde naam voor de beschikbaarheidsset als de domein controller Server en maakt gebruik van de SQL Server 2012 Service Pack 1 ENTER prise Edition-installatie kopie in de galerie met virtuele machines. Ook wordt de schijf van het besturings systeem ingesteld op alleen-lezen-caching (geen schrijf cache). U wordt aangeraden om de database bestanden te migreren naar een afzonderlijke gegevens schijf die u aan de virtuele machine koppelt en deze te configureren zonder lees-of schrijf cache. De volgende keer is echter het verwijderen van de schrijf cache op de schijf met het besturings systeem, omdat u de Lees cache niet kunt verwijderen op de besturingssysteem schijf.
   * **Add-AzureProvisioningConfig** voegt de virtuele machine toe aan het Active Directory domein dat u hebt gemaakt.
   * **Set-AzureSubnet** plaatst de virtuele machine in het back-subnet.
   * **Add-AzureEndpoint** voegt toegangs eindpunten toe zodat client toepassingen toegang hebben tot deze SQL Server services-instanties op internet. Er worden verschillende poorten gegeven aan ContosoSQL1 en ContosoSQL2.
   * **New-AzureVM** maakt de nieuwe SQL Server virtuele machine in dezelfde Cloud service als ContosoQuorum. U moet de virtuele machines in dezelfde Cloud service plaatsen als u wilt dat ze zich in dezelfde beschikbaarheidsset bevinden.
4. Wacht totdat elke virtuele machine volledig is ingericht en voor elke virtuele machine het externe bureau blad-bestand naar uw werkmap downloadt. De `for` lus doorloopt de drie nieuwe virtuele machines en voert de opdrachten binnen de accolades van het hoogste niveau voor elk van deze cycli uit.

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

    De SQL Server Vm's worden nu ingericht en uitgevoerd, maar ze worden met SQL Server geïnstalleerd met de standaard opties.

## <a name="initialize-the-failover-cluster-vms"></a>Virtuele failover-cluster-Vm's initialiseren
In deze sectie moet u de drie servers wijzigen die u gaat gebruiken in het failovercluster en de SQL Server-installatie. Met name:

* Alle servers: u moet het onderdeel **failover clustering** installeren.
* Alle servers: u moet **CORP\Install** toevoegen als machine **beheerder**.
* Alleen ContosoSQL1 en ContosoSQL2: u moet **CORP\Install** toevoegen als **sysadmin** -rol in de standaard database.
* Alleen ContosoSQL1 en ContosoSQL2: u moet **NT AUTHORITY\SYSTEM** toevoegen als aanmelding met de volgende machtigingen:

  * Elke beschikbaarheids groep wijzigen
  * Verbinding maken met SQL
  * Server status weer geven
* Alleen ContosoSQL1 en ContosoSQL2: het **TCP** -protocol is al ingeschakeld op de SQL Server VM. U moet echter wel de firewall openen voor externe toegang van SQL Server.

U bent nu klaar om te beginnen. Volg de onderstaande stappen vanaf **ContosoQuorum**:

1. Maak verbinding met **ContosoQuorum** door de bestanden van extern bureau blad te starten. Gebruik de gebruikers naam **AzureAdmin** en het wacht woord **Contoso! 000**dat u hebt opgegeven tijdens het maken van de vm's.
2. Controleer of de computers zijn toegevoegd aan **Corp.contoso.com**.
3. Wacht totdat de SQL Server installatie is voltooid met het uitvoeren van de geautomatiseerde initialisatie taken voordat u doorgaat.
4. Open een Power shell-venster in de beheerders modus.
5. Installeer de functie Windows Failover Clustering.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Voeg **CORP\Install** toe als een lokale beheerder.

        net localgroup administrators "CORP\Install" /Add
7. Meld u af bij ContosoQuorum. U bent nu klaar met deze server.

        logoff.exe

Initialiseer vervolgens **ContosoSQL1** en **ContosoSQL2**. Volg de onderstaande stappen, die identiek zijn voor beide SQL Server Vm's.

1. Maak verbinding met de twee SQL Server Vm's door de bestanden van extern bureau blad te starten. Gebruik de gebruikers naam **AzureAdmin** en het wacht woord **Contoso! 000**dat u hebt opgegeven tijdens het maken van de vm's.
2. Controleer of de computers zijn toegevoegd aan **Corp.contoso.com**.
3. Wacht totdat de SQL Server installatie is voltooid met het uitvoeren van de geautomatiseerde initialisatie taken voordat u doorgaat.
4. Open een Power shell-venster in de beheerders modus.
5. Installeer de functie Windows Failover Clustering.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Voeg **CORP\Install** toe als een lokale beheerder.

        net localgroup administrators "CORP\Install" /Add
7. Importeer de SQL Server Power shell-provider.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Voeg **CORP\Install** toe als de rol sysadmin voor het standaard SQL Server exemplaar.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Voeg **NT AUTHORITY\SYSTEM** toe als een aanmelding met de drie machtigingen die hierboven worden beschreven.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Open de firewall voor externe toegang van SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Meld u af bij beide Vm's.

         logoff.exe

Ten slotte bent u klaar om de beschikbaarheids groep te configureren. U gebruikt de SQL Server Power shell-provider om alle werkzaamheden op **ContosoSQL1**uit te voeren.

## <a name="configure-the-availability-group"></a>De beschikbaarheids groep configureren
1. Maak opnieuw verbinding met **ContosoSQL1** door de bestanden van extern bureau blad te starten. Meld u aan met behulp van **CORP\Install**in plaats van u aan te melden met behulp van het computer account.
2. Open een Power shell-venster in de beheerders modus.
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
4. Importeer de SQL Server Power shell-provider.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Wijzig het SQL Server-service account voor ContosoSQL1 in CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Wijzig het SQL Server-service account voor ContosoSQL2 in CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Down load **CreateAzureFailoverCluster. ps1** van het maken van een failovercluster voor AlwaysOn- [beschikbaarheids groepen in azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) naar de lokale werkmap. U gebruikt dit script om u te helpen bij het maken van een functioneel failovercluster. Zie [hoge Beschik baarheid en herstel na nood gevallen voor SQL Server in azure virtual machines](../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)voor belang rijke informatie over de interactie van Windows Failover Clustering met het Azure-netwerk.
8. Ga naar uw werkmap en maak het failovercluster met het gedownloade script.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Schakel AlwaysOn-beschikbaarheids groepen in voor de standaard SQL Server instanties in **ContosoSQL1** en **ContosoSQL2**.

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
10. Maak een back-upmap en verleen machtigingen voor de SQL Server-service accounts. U gebruikt deze map om de beschikbaarheids database op de secundaire replica voor te bereiden.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Maak een Data Base op **ContosoSQL1** met de naam **MyDB1**, neem een volledige back-up en een logboek back-up en herstel ze op **CONTOSOSQL2** met de optie **with norecovery** .

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Maak de eind punten van de beschikbaarheids groep op de SQL Server Vm's en stel de juiste machtigingen in voor de eind punten.

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
13. Maak de beschikbaarheids replica's.

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
14. Maak ten slotte de beschikbaarheids groep en voeg de secundaire replica toe aan de beschikbaarheids groep.

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
U hebt nu de implementatie SQL Server altijd voltooid door een beschikbaarheids groep te maken in Azure. Zie een ILB-listener configureren voor AlwaysOn- [beschikbaarheids groepen in azure](../classic/ps-sql-int-listener.md)voor informatie over het configureren van een listener voor deze beschikbaarheids groep.

Zie [SQL Server op virtuele machines van Azure](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)voor meer informatie over het gebruik van SQL Server in Azure.
