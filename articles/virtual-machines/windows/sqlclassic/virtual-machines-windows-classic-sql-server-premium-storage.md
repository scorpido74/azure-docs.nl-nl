---
title: Azure Premium-opslag gebruiken met SQL Server | Microsoft Documenten
description: In dit artikel worden resources gebruikt die zijn gemaakt met het klassieke implementatiemodel en wordt richtlijnen gegeven voor het gebruik van Azure Premium Storage met SQL Server die op Azure Virtual Machines wordt uitgevoerd.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 479f9abc667e20a136da5f6231e78a1e4052f087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75965674"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Azure Premium Storage gebruiken met SQL Server op virtuele machines

## <a name="overview"></a>Overzicht

[Azure premium SSD's](../disks-types.md) is de volgende generatie opslag die lage latentie en hoge doorvoer IO biedt. Het werkt het beste voor belangrijke IO-intensieve workloads, zoals SQL Server op IaaS [Virtual Machines.](https://azure.microsoft.com/services/virtual-machines/)

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

In dit artikel vindt u planning en richtlijnen voor het migreren van een virtuele machine waarop SQL Server wordt uitgevoerd om Premium Storage te gebruiken. Dit omvat Azure-infrastructuur (netwerk-, opslag)- en windows VM-stappen voor gasten. Het voorbeeld in de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) toont een volledige uitgebreide end-to-end migratie van hoe grotere VM's te verplaatsen om te profiteren van verbeterde lokale SSD-opslag met PowerShell.

Het is belangrijk om het end-to-end proces te begrijpen van het gebruik van Azure Premium Storage met SQL Server op IAAS VM's. Dit omvat:

* Identificatie van de vereisten voor het gebruik van Premium Storage.
* Voorbeelden van het implementeren van SQL Server op IaaS naar Premium Storage voor nieuwe implementaties.
* Voorbeelden van het migreren van bestaande implementaties, zowel zelfstandige servers als implementaties met SQL Always On Availability Groups.
* Mogelijke migratiebenaderingen.
* Volledig end-to-end voorbeeld met stappen van Azure, Windows en SQL Server voor de migratie van een bestaande Always On-implementatie.

Zie [SQL Server in Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor meer achtergrondinformatie over SQL Server in Azure Virtual Machines.

**Auteur:** Daniel Sol **Technical Reviewers:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Voorwaarden voor Premium Storage

Er zijn verschillende vereisten voor het gebruik van Premium Storage.

### <a name="machine-size"></a>Machinegrootte

Voor het gebruik van Premium Storage moet u Virtual Machines (DS-serie VIRTUAL Machines) gebruiken. Als u machines uit de DS-serie nog niet eerder in uw cloudservice hebt gebruikt, moet u de bestaande vm verwijderen, de gekoppelde schijven behouden en vervolgens een nieuwe cloudservice maken voordat u de VM opnieuw maakt als DS*-rolgrootte. Zie [Virtuele machine- en cloudservicegroottes voor Azure voor](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie over de grootte van virtuele machines en cloudservices.

### <a name="cloud-services"></a>Cloud services

U DS* VM's alleen gebruiken met Premium Storage wanneer ze worden gemaakt in een nieuwe cloudservice. Als u SQL Server Always On in Azure gebruikt, verwijst de Always On Listener naar het IP-adres Azure Internal of External Load Balancer dat is gekoppeld aan een cloudservice. Dit artikel richt zich op het migreren met behoud van beschikbaarheid in dit scenario.

> [!NOTE]
> Een DS*-serie moet de eerste VM zijn die wordt geïmplementeerd in de nieuwe Cloud Service.
>
>

### <a name="regional-vnets"></a>Regionale VNETS

Voor DS* VM's moet u het Virtual Network (VNET) dat uw VM's host, zo mals als regionaal configureren. Dit "verbreedt" vnet moet de grotere VM's in andere clusters toestaan en communicatie tussen hen toestaan. In de volgende schermafbeelding toont de gemarkeerde locatie regionale VNET's, terwijl het eerste resultaat een "smalle" VNET toont.

![RegionalVNET (RegionalVNET)][1]

U een Microsoft-ondersteuningsticket verhogen om te migreren naar een regionaal VNET. Microsoft voert dan een wijziging door. Als u de migratie naar regionale VNET's wilt voltooien, wijzigt u de eigenschap AffinityGroup in de netwerkconfiguratie. Exporteer eerst de netwerkconfiguratie in PowerShell en vervang vervolgens de eigenschap **AffinityGroup** in het element **VirtualNetworkSite** door een eigenschap **Locatie.** Geef `Location = XXXX` `XXXX` op waar een Azure-gebied is. Importeer vervolgens de nieuwe configuratie.

Bijvoorbeeld, rekening houdend met de volgende VNET-configuratie:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Als u dit wilt verplaatsen naar een regionaal VNET in West-Europa, wijzigt u de configuratie als volgt:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Opslagaccounts

U moet een nieuw opslagaccount maken dat is geconfigureerd voor Premium Storage. Houd er rekening mee dat het gebruik van Premium Storage is ingesteld op het opslagaccount, niet op individuele VHD's, maar bij het gebruik van een VM uit de DS*-serie u VHD's van Premium- en Standard Storage-accounts koppelen. U dit overwegen als u de OS VHD niet op het Premium Storage-account wilt plaatsen.

Met de volgende opdracht **Nieuw-AzureStorageAccountPowerShell** met het **type** 'Premium_LRS' wordt een Premium-opslagaccount gemaakt:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Instellingen voor VHDs-cache

Het belangrijkste verschil tussen het maken van schijven die deel uitmaken van een Premium Storage-account is de schijfcacheinstelling. Voor SQL Server Data-volumeschijven wordt aanbevolen **'Lees caching' te**gebruiken. Voor transactielogboekvolumes moet de instelling voor schijfcache worden ingesteld op **'Geen'.** Dit is anders dan de aanbevelingen voor Standaardopslagaccounts.

Zodra de VHD's zijn aangesloten, kan de cache-instelling niet worden gewijzigd. U moet de VHD loskoppelen en opnieuw koppelen met een bijgewerkte cache-instelling.

### <a name="windows-storage-spaces"></a>Windows-opslagruimten

U [Windows-opslagruimten](https://technet.microsoft.com/library/hh831739.aspx) gebruiken zoals u deed met eerdere standaardopslag, hiermee u een VM migreren die al gebruikmaakt van opslagruimten. Het voorbeeld in [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (stap 9 en vooruit) toont de Powershell-code om een VM met meerdere gekoppelde VHD's te extraheren en te importeren.

Opslagpools werden gebruikt met standaard Azure-opslagaccount om de doorvoer te verbeteren en de latentie te verminderen. Mogelijk vindt u waarde in het testen van opslagpools met Premium Storage voor nieuwe implementaties, maar ze voegen wel extra complexiteit toe met de opslaginstelling.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Zoeken welke Azure Virtual Disks-map naar opslaggroepen wordt toegewezen

Aangezien er verschillende aanbevelingen voor cache-instellingen zijn voor gekoppelde VHD's, u besluiten de VHD's naar een Premium Storage-account te kopiëren. Wanneer u ze echter opnieuw koppelt aan de nieuwe VM uit de DS-serie, moet u mogelijk de cache-instellingen wijzigen. Het is eenvoudiger om de aanbevolen cache-instellingen voor Premium Storage toe te passen wanneer u afzonderlijke VHD's hebt voor de SQL-gegevensbestanden en logboekbestanden (in plaats van één VHD die beide bevat).

> [!NOTE]
> Als u SQL Server-gegevens en logboekbestanden op hetzelfde volume hebt, is de optie voor cache die u kiest afhankelijk van de IO-toegangspatronen voor uw databaseworkloads. Alleen testen kan aantonen welke caching-optie het beste is voor dit scenario.
>
>

Als u echter Windows-opslagruimten gebruikt die uit meerdere VHD's bestaan, moet u uw oorspronkelijke scripts bekijken om te bepalen welke gekoppelde VHD's zich in welke specifieke pool bevinden, zodat u de cache-instellingen dienovereenkomstig instellen voor elke schijf.

Als u geen origineel script beschikbaar hebt om u te laten zien welke VHD's naar de opslaggroep worden toegewezen, u de volgende stappen gebruiken om de toewijzing van de schijf/opslaggroep te bepalen.

Gebruik voor elke schijf de volgende stappen:

1. Lijst met schijven die aan VM zijn gekoppeld met de opdracht **Get-AzureVM:**

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Let op diskname en LUN.

    ![DisknameAndLUN][2]
1. Extern bureaublad in de VM. Ga vervolgens naar **Computer Management** | **Device Manager** | **Disk Drives**. Bekijk de eigenschappen van elk van de 'Microsoft Virtual Disks'

    ![VirtualDiskProperties VirtualDiskProperties][3]
1. Het LUN-nummer is hier een verwijzing naar het LUN-nummer dat u opgeeft bij het koppelen van de VHD aan de VM.
1. Ga voor de 'Virtuele schijf van Microsoft' naar het tabblad **Details** en ga vervolgens in de **lijst Eigenschappen** naar **Stuurprogrammasleutel**. Noteer in de **volgende**schermafbeelding de **verschuiving**, die 0002 is. De 0002 geeft de PhysicalDisk2 aan waarnaar de opslaggroep verwijst.

    ![VirtualDiskPropertyDetails][4]
1. Dump voor elke opslaggroep de bijbehorende schijven:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Nu u deze informatie gebruiken om gekoppelde VHD's te koppelen aan fysieke schijven in opslaggroepen.

Zodra u VHD's hebt toegewezen aan fysieke schijven in opslaggroepen, u ze vervolgens loskoppelen en kopiëren naar een Premium Storage-account en deze vervolgens koppelen met de juiste cache-instelling. Zie het voorbeeld in de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), stappen 8 tot en met 12. In deze stappen wordt uitgelegd hoe u een VM-gekoppelde VHD-schijfconfiguratie extraheren naar een CSV-bestand, de VHD's kopiëren, de cache-instellingen voor schijfconfiguratie wijzigen en de VM uiteindelijk opnieuw implementeren als vm-serie vm met alle gekoppelde schijven.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>BANDBREEDTE van VM-opslag en VHD-opslagdoorvoer

De hoeveelheid opslagprestaties is afhankelijk van de opgegeven DS* VM-grootte en de VHD-formaten. De VM's hebben verschillende vergoedingen voor het aantal VHD's dat kan worden aangesloten en de maximale bandbreedte die ze ondersteunen (MB/s). Zie [Virtuele machine- en cloudservicegroottes voor Azure voor](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)de specifieke bandbreedtenummers.

Verhoogde IOPS worden bereikt met grotere schijfformaten. U moet hier rekening mee houden wanneer u nadenkt over uw migratiepad. [Zie de tabel voor IOPS en schijftypen voor](../disks-types.md#premium-ssd)meer informatie.

Tot slot, van mening dat VM's hebben verschillende maximale schijfbandbreedtes die zij ondersteunen voor alle schijven aangesloten. Onder hoge belasting u de maximale schijfbandbreedte verzadigen die beschikbaar is voor die VM-rolgrootte. Een Standard_DS14 ondersteunt bijvoorbeeld maximaal 512 MB/s; daarom u met drie P30-schijven de schijfbandbreedte van de VM verzadigen. Maar in dit voorbeeld kan de doorvoerlimiet worden overschreden, afhankelijk van de mix van lees- en schrijf-IOs.

## <a name="new-deployments"></a>Nieuwe implementaties

In de volgende twee secties wordt uitgelegd hoe u SQL Server VM's implementeren in Premium Storage. Zoals eerder vermeld, hoeft u de OS-schijf niet per se op Premium-opslag te plaatsen. U ervoor kiezen om dit te doen als u van plan bent om intensieve IO-workloads op de OS VHD te plaatsen.

Het eerste voorbeeld toont gebruik te maken van bestaande Azure Gallery-afbeeldingen. In het tweede voorbeeld ziet u hoe u een aangepaste VM-afbeelding gebruikt die u in een bestaand standaardopslagaccount hebt.

> [!NOTE]
> In deze voorbeelden wordt ervan uitgegaan dat u al een regionale VNET hebt gemaakt.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Een nieuwe virtuele machine maken met Premium-opslag met galerijafbeelding

In het onderstaande voorbeeld ziet u hoe u de OS VHD op premium opslag plaatsen en Premium Storage VHD's koppelen. U de osschijf echter ook in een Standaardopslagaccount plaatsen en vervolgens VHD's toevoegen die zich in een Premium-opslagaccount bevinden. Beide scenario's worden gedemonstreerd.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Stap 1: Een Premium-opslagaccount maken

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Stap 2: Een nieuwe cloudservice maken

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Stap 3: Een VIP voor cloudservice reserveren (optioneel)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Stap 4: Een VM-container maken

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Stap 5: OS VHD plaatsen op standaard- of premiumopslag

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Stap 6: VM maken

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Een nieuwe virtuele machine maken om Premium-opslag te gebruiken met een aangepaste afbeelding

Dit scenario laat zien waar u bestaande aangepaste afbeeldingen hebt die zich in een standaardopslagaccount bevinden. Zoals gezegd als u de OS VHD op Premium Storage wilt plaatsen, moet u de afbeelding die in het Standaardopslagaccount bestaat kopiëren en deze overbrengen naar een Premium-opslag voordat deze kan worden gebruikt. Als u een on-premises afbeelding hebt, u deze methode ook gebruiken om deze rechtstreeks naar het Premium Storage-account te kopiëren.

#### <a name="step-1-create-storage-account"></a>Stap 1: Opslagaccount maken

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Stap 2 Cloudservice maken

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Stap 3: Bestaande afbeelding gebruiken

U een bestaande afbeelding gebruiken. U ook [een afbeelding maken van een bestaande machine.](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) Let op de machine die je afbeelding hoeft niet te zijn DS * machine. Zodra u de afbeelding hebt, wordt in de volgende stappen weergegeven hoe u deze kopiëren naar het Premium Storage-account met de **powershell-opdrachtopdracht start-AzureStorageCopy.**

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Stap 4: Blob kopiëren tussen opslagaccounts

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Stap 5: Controleer regelmatig de kopieerstatus:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Stap 6: Afbeeldingsschijf toevoegen aan Azure-schijfopslagplaats in abonnement

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> U vinden dat, hoewel de status rapporten als succes, kon je nog steeds een disk lease fout. Wacht in dit geval ongeveer 10 minuten.

#### <a name="step-7--build-the-vm"></a>Stap 7: De VM bouwen

Hier bouwt u de VM van uw afbeelding en bevestigt u twee Premium Storage VHD's:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!"


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Bestaande implementaties die geen always on beschikbaarheidsgroepen gebruiken

> [!NOTE]
> Zie eerst de sectie [Voorwaarden](#prerequisites-for-premium-storage) van dit artikel voor bestaande implementaties.

Er zijn verschillende overwegingen voor SQL Server-implementaties die geen always on beschikbaarheidsgroepen gebruiken en die welke dat wel doen. Als u Always On niet gebruikt en een bestaande zelfstandige SQL Server hebt, u upgraden naar Premium Storage met behulp van een nieuwe cloudservice en opslagaccount. Overweeg de volgende opties:

* **Maak een nieuwe SQL Server VM**. U een nieuwe SQL Server VM maken die een Premium Storage-account gebruikt, zoals gedocumenteerd in Nieuwe implementaties. Maak vervolgens een back-up en herstel uw SQL Server-configuratie en gebruikersdatabases. De toepassing moet worden bijgewerkt om naar de nieuwe SQL Server te verwijzen als deze intern of extern wordt geopend. U zou alle 'uit db'-objecten moeten kopiëren alsof u een SQL Server-migratie (Side by Side) (SxS) doet. Dit geldt ook voor objecten zoals aanmeldingen, certificaten en gekoppelde servers.
* **Een bestaande SQL Server VM migreren**. Dit vereist het offline halen van de SQL Server VM en het overbrengen naar een nieuwe cloudservice, waaronder het kopiëren van alle aangesloten VHD's naar het Premium Storage-account. Wanneer de VM online komt, verwijst de toepassing naar de naam van de serverhost als voorheen. Houd er rekening mee dat de grootte van de bestaande schijf van invloed is op de prestatiekenmerken. Een schijf van 400 GB wordt bijvoorbeeld naar boven afgerond op een P20. Als u weet dat u die schijfprestaties niet nodig hebt, u de VM opnieuw maken als vm uit de DS-serie en Premium Storage VHD's koppelen aan de gewenste grootte-/prestatiespecificatie. Dan u de SQL DB-bestanden loskoppelen en opnieuw koppelen.

> [!NOTE]
> Bij het kopiëren van de VHD-schijven moet u zich bewust zijn van de grootte, afhankelijk van de grootte betekent welk Premium Storage Disk type ze vallen in, dit bepaalt schijf prestaties specificatie. Azure wordt afgerond op de dichtstbijzijnde schijfgrootte, dus als u een schijf van 400 GB hebt, wordt dit naar boven afgerond op een P20. Afhankelijk van uw bestaande IO-vereisten van de OS VHD, hoeft u dit mogelijk niet te migreren naar een Premium Storage-account.

Als uw SQL Server extern wordt geopend, verandert de VIP van de cloudservice. U moet ook eindpunten, ACL's en DNS-instellingen bijwerken.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Bestaande implementaties die gebruikmaken van Beschikbaarheidsgroepen voor altijd op

> [!NOTE]
> Zie eerst de sectie [Voorwaarden](#prerequisites-for-premium-storage) van dit artikel voor bestaande implementaties.

In eerste instantie kijken we in deze sectie hoe Always On samenwerkt met Azure Networking. Vervolgens splitsen we migraties op in twee scenario's: migraties waarbij enige downtime kan worden getolereerd en migraties waarbij u minimale downtime moet bereiken.

On-premises SQL Server Always On Availability Groups gebruiken een listener on-premises die een virtuele DNS-naam registreert, samen met een IP-adres dat wordt gedeeld tussen een of meer SQL-servers. Wanneer clients verbinding maken, worden ze doorgestuurd via het listener-IP naar de primaire SQL Server. Dit is de server die op dat moment eigenaar is van de Always On IP-bron.

![ImplementatiesAlways Always ingeschakeld][6]

In Microsoft Azure u slechts één IP-adres toegewezen hebben aan een NIC op de VM, dus om dezelfde abstractielaag te bereiken als on-premises, maakt Azure gebruik van het IP-adres dat is toegewezen aan de Internal/External Load Balancers (ILB/ELB). De IP-bron die tussen de servers wordt gedeeld, is ingesteld op hetzelfde IP als de ILB/ELB. Dit wordt gepubliceerd in de DNS en clientverkeer wordt doorgegeven via de ILB/ELB naar de primaire SQL Server-replica. De ILB/ELB weet welke SQL Server primair is omdat het sondes gebruikt om de Always On IP-bron te onderzoeken. In het vorige voorbeeld wordt elk knooppunt gesondeerd waarop een eindpunt wordt verwezen door de ELB/ILB, afhankelijk van de primaire SQL Server.

> [!NOTE]
> De ILB en ELB zijn beide toegewezen aan een bepaalde Azure-cloudservice, dus elke cloudmigratie in Azure betekent waarschijnlijk dat het IP-adres van de Load Balancer verandert.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Altijd migreren op implementaties die enige downtime mogelijk kunnen maken

Er zijn twee strategieën om Always On-implementaties te migreren die enige downtime mogelijk maken:

1. **Meer secundaire replica's toevoegen aan een bestaand always on cluster**
2. **Migreren naar een nieuw always on-cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Meer secundaire replica's toevoegen aan een bestaand always on-cluster

Een strategie is om meer secondaries toe te voegen aan de Always On Availability Group. U moet deze toevoegen aan een nieuwe cloudservice en de listener bijwerken met het nieuwe IP-adres van de load balancer.

##### <a name="points-of-downtime"></a>Punten van downtime:

* Clustervalidatie.
* Testen Always On failovers voor nieuwe secondaries.

Als u Windows-opslaggroepen binnen de VM gebruikt voor een hogere IO-doorvoer, worden deze offline gehaald tijdens een volledige clustervalidatie. De validatietest is vereist wanneer u knooppunten aan het cluster toevoegt. De tijd die nodig is om de test uit te voeren kan variëren, dus je moet dit testen in je representatieve testomgeving om een geschatte tijd te krijgen van hoe lang dit duurt.

U moet tijd inrichten waarin u handmatige failover- en chaostests uitvoeren op de nieuw toegevoegde knooppunten om ervoor te zorgen dat always on high availability functies zoals verwacht.

![DeploymentUseAlways On2][7]

> [!NOTE]
> U moet alle exemplaren van SQL Server stoppen waarin de opslaggroepen worden gebruikt voordat de validatie wordt uitgevoerd.
>
> ##### <a name="high-level-steps"></a>Stappen op hoog niveau
>

1. Maak twee nieuwe SQL-servers in nieuwe cloudservice met bijbehorende Premium Storage.
2. Kopieer volledige back-ups en herstel met **NORECOVERY**.
3. Kopieer over 'out of user DB' afhankelijke objecten, zoals logins etc.
4. Maak een nieuwe ILB (Internal Load Balancer) of gebruik een Externe Load Balancer (ELB) en stel vervolgens Load Balanced Endpoints in op beide nieuwe knooppunten.

   > [!NOTE]
   > Controleer of alle knooppunten de juiste eindpuntconfiguratie hebben voordat u verdergaat
   >
   >
5. Gebruikers-/toepassingstoegang tot de SQL Server stoppen (bij gebruik van opslaggroepen).
6. Stop SQL Server Engine Services op alle knooppunten (als u opslaggroepen gebruikt).
7. Voeg nieuwe knooppunten toe aan het cluster en voer volledige validatie uit.
8. Zodra validatie is geslaagd, start u alle SQL Server Services.
9. Back-upvan transactielogboeken en herstel gebruikersdatabases.
10. Voeg nieuwe knooppunten toe aan de groep Altijd op beschikbaarheid en plaats replicatie in **Synchroon**.
11. Voeg de IP-adresbron van de nieuwe Cloud Service ILB/ELB toe via PowerShell for Always On op basis van het voorbeeld multi-site in de [bijlage.](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) Stel in Windows-clustering de **mogelijke eigenaren** van de **IP-adresbron** in op de oude nieuwe knooppunten. Zie de sectie 'Ip-adresbron toevoegen op hetzelfde subnet' van de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Failover naar een van de nieuwe knooppunten.
13. Maak de nieuwe knooppunten Auto Failover Partners en test failovers.
14. Verwijder originele knooppunten uit beschikbaarheidsgroep.

##### <a name="advantages"></a>Voordelen

* Nieuwe SQL-servers kunnen worden getest (SQL Server en Application) voordat ze worden toegevoegd aan Always On.
* U de VM-grootte wijzigen en de opslag aanpassen aan uw exacte vereisten. Het zou echter nuttig zijn om alle SQL-bestandspaden hetzelfde te houden.
* U bepalen wanneer de overdracht van de DB-back-ups naar de secundaire replica's wordt gestart. Dit verschilt van het gebruik van Azure **Start-AzureStorageBlobCopy** commandlet om VHD's te kopiëren, omdat dit een asynchrone kopie is.

##### <a name="disadvantages"></a>Nadelen

* Wanneer u Windows-opslaggroepen gebruikt, is er clusterdowntime tijdens de volledige clustervalidatie voor de nieuwe extra knooppunten.
* Afhankelijk van de SQL Server-versie en het bestaande aantal secundaire replica's u mogelijk geen secundaire replica's toevoegen zonder bestaande secondaries te verwijderen.
* Er kan lange SQL-gegevensoverdrachttijd zijn tijdens het instellen van de secondaries.
* Er zijn extra kosten tijdens de migratie terwijl er nieuwe machines parallel draaien.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migreren naar een nieuw always on-cluster

Een andere strategie is om een gloednieuwe Always On Cluster te maken met gloednieuwe knooppunten in nieuwe cloudservice en vervolgens de clients om te leiden om het te gebruiken.

##### <a name="points-of-downtime"></a>Punten van downtime

Er is downtime wanneer u applicaties en gebruikers overzet naar de nieuwe Always On-listener. De downtime is afhankelijk van:

* De tijd die nodig is om definitieve back-ups van transactielogboeken te herstellen naar databases op nieuwe servers.
* De tijd die nodig is om clienttoepassingen bij te werken om de nieuwe Always On-listener te gebruiken.

##### <a name="advantages"></a>Voordelen

* U de werkelijke productieomgeving, SQL Server- en OS-buildwijzigingen testen.
* U hebt de mogelijkheid om de opslag aan te passen en de grootte van VM mogelijk te verkleinen. Dit kan leiden tot kostenbesparing.
* U uw SQL Server-build of -versie tijdens dit proces bijwerken. U het besturingssysteem ook upgraden.
* Het vorige Always On Cluster kan fungeren als een solide rollback-doel.

##### <a name="disadvantages"></a>Nadelen

* U moet de DNS-naam van de listener wijzigen als u wilt dat beide Always On-clusters tegelijkertijd worden uitgevoerd. Hiermee wordt beheeroverhead toegevoegd tijdens de migratie, omdat tekenreeksen voor clienttoepassingen de nieuwe naam Listener moeten weerspiegelen.
* U moet een synchronisatiemechanisme tussen de twee omgevingen implementeren om ze zo dicht mogelijk bij elkaar te houden om de uiteindelijke synchronisatievereisten voor migratie te minimaliseren.
* Er zijn extra kosten tijdens de migratie terwijl de nieuwe omgeving wordt uitgevoerd.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Altijd aan implementaties migreren voor minimale downtime

Er zijn twee strategieën voor het migreren van Always On-implementaties voor minimale downtime:

1. **Gebruik een bestaande secundaire: single-site**
2. **Bestaande secundaire replica(s) gebruiken: multisite**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Gebruik maken van een bestaande secundaire: Single-Site

Een strategie voor minimale downtime is om een bestaande cloud secundaire en verwijderen uit de huidige cloud service. Kopieer vervolgens de VHD's naar het nieuwe Premium Storage-account en maak de VM in de nieuwe cloudservice. Werk vervolgens de listener bij in clustering en failover.

##### <a name="points-of-downtime"></a>Punten van downtime

* Er is downtime wanneer u het uiteindelijke knooppunt bijwerkt met het Laadpunt voor gebalanceerde.
* Uw clientreconnectie kan worden vertraagd, afhankelijk van uw client/DNS-configuratie.
* Er is extra downtime als u ervoor kiest om de groep Always On Cluster offline te halen om de IP-adressen uit te wisselen. U dit voorkomen door een OK-afhankelijkheid en mogelijke eigenaren te gebruiken voor de toegevoegde IP-adresbron. Zie de sectie 'Ip-adresbron toevoegen op hetzelfde subnet' van de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Wanneer u het toegevoegde knooppunt wilt deelnemen als een Always On Failover-partner, moet u een Azure-eindpunt toevoegen met een verwijzing naar de gebalanceerde set laden. Wanneer u de opdracht **Add-AzureEndpoint** uitvoert om dit te doen, blijven de huidige verbindingen open, maar kunnen nieuwe verbindingen met de listener pas worden ingesteld nadat de load balancer is bijgewerkt. Bij het testen van dit werd gezien om 90-120seconden laatste, dit moet worden getest.

##### <a name="advantages"></a>Voordelen

* Geen extra kosten gemaakt tijdens de migratie.
* Een één-op-één migratie.
* Minder complexiteit.
* Zorgt voor meer IOPS van Premium Storage SKU's. Wanneer de schijven van de VM worden losgekoppeld en naar de nieuwe cloudservice worden gekopieerd, kan een tool van derden worden gebruikt om de VHD-grootte te vergroten, wat hogere doorvoersnelheden biedt. Voor het verhogen van VHD maten, zie dit [forum discussie](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Nadelen

* Er is een tijdelijk verlies van HA en DR tijdens de migratie.
* Aangezien dit een 1:1-migratie is, moet u een minimale VM-grootte gebruiken die uw aantal VHD's ondersteunt, zodat u uw VM's mogelijk niet verkleinen.
* In dit scenario wordt de opdrachtopdracht Azure **Start-AzureStorageBlobCopy** gebruikt, die asynchroon is. Er is geen SLA bij het voltooien van de kopie. De tijd van de kopieën varieert, terwijl dit afhankelijk is van wachten in de wachtrij is het ook afhankelijk van de hoeveelheid gegevens die moet worden overgedragen. De kopieertijd neemt toe als de overdracht naar een ander Azure-datacenter gaat dat Premium Storage in een andere regio ondersteunt. Als u slechts 2 knooppunten hebt, overweeg dan een mogelijke beperking voor het geval de kopie langer duurt dan bij het testen. Dit kan de volgende ideeën omvatten.
  * Voeg een tijdelijk 3e SQL Server-knooppunt voor HA toe voordat de migratie met overeengekomen downtime wordt weergegeven.
  * Voer de migratie uit buiten het geplande onderhoud van Azure.
  * Zorg ervoor dat u uw clusterquorum correct hebt geconfigureerd.  

##### <a name="high-level-steps"></a>Stappen op hoog niveau

Dit document toont geen volledig voorbeeld van einde tot eind, maar de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) bevat details die kunnen worden gebruikt om dit uit te voeren.

![MinimalDowntime][8]

* De schijfconfiguratie verzamelen en het knooppunt verwijderen (verwijder de bijgevoegde VHD's niet).
* Een Premium Storage-account maken en VHD's kopiëren vanuit het Standaardopslagaccount
* Maak nieuwe cloudservice en implementeer de SQL2 VM opnieuw in die cloudservice. Maak de VM met behulp van het gekopieerde originele OS VHD en voeg de gekopieerde VHD's toe.
* Configureer ILB / ELB en voeg Eindpunten toe.
* Listener bijwerken door:
  * De Always On Group offline halen en de Always On Listener updaten met een nieuw ILB/ ELB IP adres.
  * Of het toevoegen van de IP-adresbron van nieuwe Cloud Service ILB/ELB via PowerShell in Windows-clustering. Stel vervolgens de mogelijke eigenaren van de IP-adresbron in op het gemigreerde knooppunt SQL2 en stel dit in als OK-afhankelijkheid in de netwerknaam. Zie de sectie 'Ip-adresbron toevoegen op hetzelfde subnet' van de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Controleer DNS-configuratie/-propagatie naar de clients.
* Migreer SQL1 VM en doorga stap 2 – 4.
* Als u stappen 5ii gebruikt, voegt u SQL1 toe als mogelijke eigenaar voor de toegevoegde IP-adresbron
* Test failovers.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Bestaande secundaire replica(s) gebruiken: Multi-Site

Als u knooppunten hebt in meer dan één Azure-datacenter (DC) of als u een hybride omgeving hebt, u een always on-configuratie in deze omgeving gebruiken om downtime te minimaliseren.

De aanpak is om de synchronisatie always on te wijzigen in Synchroon voor de on-premises of secundaire Azure DC en vervolgens over te geven aan die SQL Server. Kopieer vervolgens de VHD's naar een Premium Storage-account en implementeer de machine opnieuw in een nieuwe cloudservice. Werk de luisteraar bij en faal dan terug.

##### <a name="points-of-downtime"></a>Punten van downtime

De downtime bestaat uit de tijd om failover naar de alternatieve DC en terug. Het hangt ook af van uw client / DNS-configuratie en uw client reconnection kan worden vertraagd.
Overweeg het volgende voorbeeld van een hybride Always On-configuratie:

![MultiSite1][9]

##### <a name="advantages"></a>Voordelen

* U gebruik maken van bestaande infrastructuur.
* U hebt de optie om de Azure-opslag op de DR Azure DC eerst vooraf te upgraden.
* De DR Azure DC-opslag kan opnieuw worden geconfigureerd.
* Er zijn minimaal twee failovers tijdens de migratie, met uitzondering van testfailovers.
* U hoeft SQL Server-gegevens niet te verplaatsen met back-up en herstel.

##### <a name="disadvantages"></a>Nadelen

* Afhankelijk van de toegang van de client tot SQL Server kan de latentie toenemen wanneer SQL Server wordt uitgevoerd in een alternatieve DC-toepassing.
* De kopieertijd van VHD's naar Premium-opslag kan lang zijn. Dit kan van invloed zijn op uw beslissing om het knooppunt in de beschikbaarheidsgroep te houden. Houd hier rekening mee voor wanneer logboekintensieve werkbelastingen worden uitgevoerd tijdens de migratie, omdat het primaire knooppunt de niet-gerepliceerde transacties in het transactielogboek moet houden. Hierdoor kan dit aanzienlijk groeien.
* In dit scenario wordt de opdrachtopdracht Azure **Start-AzureStorageBlobCopy** gebruikt, die asynchroon is. Er is geen SLA bij voltooiing. De tijd van de kopieën varieert, terwijl dit afhankelijk is van wachten in de wachtrij, het hangt ook af van de hoeveelheid gegevens die moet worden overgedragen. Daarom hebt u slechts één knooppunt in uw 2e datacenter, moet u mitigatiemaatregelen nemen voor het geval de kopie langer duurt dan bij het testen. Deze mitigatiestappen omvatten de volgende ideeën:
  * Voeg een tijdelijk 2e SQL-knooppunt voor HA toe voordat de migratie met overeengekomen downtime wordt goedgekeurd.
  * Voer de migratie uit buiten het geplande onderhoud van Azure.
  * Zorg ervoor dat u uw clusterquorum correct hebt geconfigureerd.

In dit scenario wordt ervan uitgegaan dat u uw installatie hebt gedocumenteerd en weet hoe de opslag in kaart is gebracht om wijzigingen aan te brengen voor optimale instellingen voor schijfcache.

##### <a name="high-level-steps"></a>Stappen op hoog niveau

![Multisite2][10]

* Maak van de on-premises / alternatieve Azure DC de SQL Server Primary en maak er de andere Auto Failover Partner (AFP) van.
* Verzamel schijfconfiguratiegegevens uit SQL2 en verwijder het knooppunt (verwijder geen gekoppelde VHD's).
* Maak een Premium Storage-account aan en kopieer VHD's van het Standaardopslagaccount.
* Maak een nieuwe cloudservice en maak de SQL2 VM met zijn Premiums Storage-schijven eraan.
* Configureer ILB / ELB en voeg Eindpunten toe.
* Werk de Always On Listener bij met een nieuw ILB / ELB IP-adres en test failover.
* Controleer de DNS-configuratie.
* Wijzig de AFP in SQL2 en migreer SQL1 en ga door stap 2 – 5.
* Test failovers.
* De AFP terugschakelen naar SQL1 en SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Bijlage: Een multisite altijd op cluster migreren naar Premium Storage

De rest van dit artikel geeft een gedetailleerd voorbeeld van het converteren van een multi-site Always On-cluster naar Premium-opslag. Het converteert de Listener ook van het gebruik van een externe load balancer (ELB) naar een interne load balancer (ILB).

### <a name="environment"></a>Omgeving

* Windows 2k12 / SQL 2k12
* 1 DB-bestanden op SP
* 2 x opslagpools per knooppunt

![Aanhangsel1][11]

### <a name="vm"></a>Vm:

In dit voorbeeld gaan we laten zien dat we van een ELB naar ILB gaan. ELB was beschikbaar voor ILB, dus dit laat zien hoe over te schakelen naar ILB tijdens de migratie.

![Aanhangsel 2][12]

### <a name="pre-steps-connect-to-subscription"></a>Voorstappen: Verbinding maken met abonnement

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Stap 1: Nieuwe opslagaccount en cloudservice maken

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Stap 2: De toegestane fouten \<op resources optioneel> vergroten

Voor bepaalde resources die deel uitmaken van de groep Always On Availability zijn er beperkingen voor het aantal fouten dat kan optreden in een periode waarin de clusterservice probeert de brongroep opnieuw te starten. Het wordt aanbevolen om dit te verhogen terwijl u door deze procedure loopt, want als u niet handmatig failover en trigger failovers door het afsluiten van machines u dicht bij deze limiet.

Het zou verstandig zijn om de foutvergoeding te verdubbelen, om dit te doen in Failoverclusterbeheer, ga naar de eigenschappen van de groep Always On-resource:

![Aanhangsel 3][13]

Wijzig de maximale fouten in 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Stap 3: Ip-adresbron \<toevoegen voor clustergroep optioneel>

Als u slechts één IP-adres voor de clustergroep hebt en dit is uitgelijnd op het subnet van de cloud, pas op, als u per ongeluk alle clusterknooppunten in de cloud op dat netwerk offline haalt, kunnen de IP-bron cluster en de naam van het clusternetwerk niet online komen. In deze situatie voorkomt het updates van andere clusterbronnen.

#### <a name="step-4-dns-configuration"></a>Stap 4: DNS-configuratie

Het implementeren van een soepele overgang is afhankelijk van hoe DNS wordt gebruikt en bijgewerkt.
Wanneer Always On is geïnstalleerd, wordt een Windows Cluster Resource-groep gemaakt, als u Failoverclusterbeheer opent, ziet u dat het minimaal drie bronnen heeft, de twee waarnaar het document verwijst:

* Virtual Network Name (VNN) – de DNS-naam waarmee clients verbinding maken wanneer ze verbinding willen maken met SQL Servers via Always On.
* IP-adresbron – het IP-adres dat aan de VNN is gekoppeld, u er meer dan één hebben en in een multisite-configuratie hebt u een IP-adres per site/subnet.

Wanneer u verbinding maakt met SQL Server, haalt het SQL Server-clientstuurprogramma de DNS-records op die aan de listener zijn gekoppeld en probeert het verbinding te maken met elk always on-gekoppeld IP-adres. Vervolgens bespreken we een aantal factoren die dit kunnen beïnvloeden.

Het aantal gelijktijdige DNS-records dat aan de listenernaam is gekoppeld, is niet alleen afhankelijk van het aantal gekoppelde IP-adressen, maar ook van de instelling 'RegisterAllIpProviders'in Failoverclustering voor de always on VNN-bron.

Wanneer u Always On in Azure implementeert, zijn er verschillende stappen om de listener- en IP-adressen te maken, moet u de 'RegisterAllIpProviders' handmatig configureren op 1, dit is anders dan een on-premises Always On-implementatie waarbij deze al is ingesteld op 1.

Als 'RegisterAllIpProviders' 0 is, ziet u slechts één DNS-record in DNS die is gekoppeld aan de listener:

![Aanhangsel 4][14]

Als 'RegisterAllIpProviders' 1 is:

![Aanhangsel5][15]

De onderstaande code dumpt de VNN-instellingen en stelt deze voor u in. Om de wijziging van kracht te laten worden, moet u de VNN offline halen en weer online zetten. Hierdoor wordt de Listener offline gehaald waardoor de clientverbinding wordt verstoord.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

In een latere migratiestap moet u de Always On-listener bijwerken met een bijgewerkt IP-adres dat verwijst naar een load balancer, waarbij een IP-adresbron wordt verwijderd en bijtoevoeging wordt. Na de IP-update moet u ervoor zorgen dat het nieuwe IP-adres is bijgewerkt in DNS-zone en dat de clients hun lokale DNS-cache bijwerken.

Als uw clients zich in een ander netwerksegment bevinden en verwijzen naar een andere DNS-server, moet u overwegen wat er gebeurt met DNS Zone Transfer tijdens de migratie, omdat de tijd voor opnieuw verbinden van de toepassing wordt beperkt door ten minste de zoneoverdrachtstijd van een nieuw IP-adres adressen voor de luisteraar. Als u hier onder tijdsdruk bent, moet u een incrementele zoneoverdracht met uw Windows-teams bespreken en testen en ook de DNS-hostrecord op een lagere Time To Live (TTL) plaatsen, zodat de clients worden bijgewerkt. Zie [Incrementele zoneoverdrachten](https://technet.microsoft.com/library/cc958973.aspx) en [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer)voor meer informatie.

Standaard is de TTL voor DNS-record die is gekoppeld aan de listener in Always On in Azure 1200 seconden. U dit verminderen als u tijdens uw migratie tijdsdruk onder druk hebt om ervoor te zorgen dat de clients hun DNS bijwerken met het bijgewerkte IP-adres voor de luisteraar. U de configuratie zien en wijzigen door de configuratie van de VNN te dumpen:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Hoe lager de 'HostRecordTTL', hoe hoger het DNS-verkeer optreedt.

##### <a name="client-application-settings"></a>Clienttoepassingsinstellingen

Als uw SQL-clienttoepassing het .NET 4.5 SQLClient ondersteunt, u het trefwoord 'MULTISUBNETFAILOVER=TRUE' gebruiken. Dit zoekwoord moet worden toegepast, omdat het zorgt voor een snellere verbinding met SQL Always On Availability Group tijdens failover. Het somt door alle IP-adressen die zijn gekoppeld aan de Always On-listener parallel en voert een agressievere TCP-verbinding opnieuw te proberen tijdens een failover.

Zie [MultiSubnetFailover-trefwoord en bijbehorende functies](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)voor meer informatie over de vorige instellingen. Zie ook [SqlClient-ondersteuning voor hoge beschikbaarheid, disaster recovery](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Stap 5: Instellingen voor clusterquorum

Aangezien u ten minste één SQL Server tegelijk naar beneden haalt, moet u de instelling van het clusterquorum wijzigen, als u File Share Witness (FSW) met twee knooppunten gebruikt, moet u het quorum instellen om de meerderheid van het knooppunt toe te staan en dynamische stemmingen te gebruiken , waardoor één knooppunt overeind blijft.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Zie [Het quorum configureren en beheren in een Failovercluster van Windows Server 2012 voor](https://technet.microsoft.com/library/jj612870.aspx)meer informatie over het beheren en configureren van het clusterquorum.

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Stap 6: Bestaande eindpunten en ACL's extraheren

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Sla deze tekst op in een bestand.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Stap 7: Failoverpartners en replicatiemodi wijzigen

Als u meer dan twee SQL-servers hebt, moet u de failover van een andere secundaire in een andere DC of on-premises wijzigen in 'Synchroon' en er een Automatische Failover Partner (AFP) van maken, dit is zodat u HA onderhoudt terwijl u wijzigingen aanbrengt. U dit doen door middel van TSQL van wijzigen al SSMS:

![Aanhangsel6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Stap 8: Secundaire VM uit cloudservice verwijderen

U moet eerst een secundair cloudknooppunt migreren. Als dit knooppunt momenteel primair is, moet u een handmatige failover starten.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Stap 9: Instellingen voor schijfcache wijzigen in CSV-bestand en opslaan

Voor gegevensvolumes moeten deze worden ingesteld op READONLY.

Voor TLOG-volumes moeten deze worden ingesteld op GEEN.

![Aanhangsel 7][17]

#### <a name="step-10-copy-vhds"></a>Stap 10: VHDS kopiëren

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


U de kopieerstatus van de VHD's controleren op het Premium Storage-account:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Aanhangsel 8][18]

Wacht tot al deze zijn opgenomen als succes.

Voor informatie voor afzonderlijke blobs:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Stap 11: OS-schijf registreren

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Stap 12: Secundair importeren in nieuwe cloudservice

De onderstaande code maakt ook gebruik van de toegevoegde optie hier u de machine importeren en gebruik maken van de bewaarbare VIP.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Stap 13: ILB maken op nieuwe cloudsvc, load balanced endpoints en ACL's toevoegen

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>Stap 14: Altijd aan updaten

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Aanhangsel9][19]

Verwijder nu het oude IP-adres van de cloudservice.

![Aanhangsel10][20]

#### <a name="step-15-dns-update-check"></a>Stap 15: DNS-updatecontrole

U moet nu DNS-servers op uw SQL Server-clientnetwerken controleren en ervoor zorgen dat clustering de extra hostrecord voor het toegevoegde IP-adres heeft toegevoegd. Als deze DNS-servers niet zijn bijgewerkt, u overwegen een DNS-zoneoverdracht te forceren en ervoor zorgen dat de clients in het subnet in staat zijn om beide Always On IP-adressen op te lossen, zodat u niet hoeft te wachten op automatische DNS-replicatie.

#### <a name="step-16-reconfigure-always-on"></a>Stap 16: Altijd aan configureren

Op dit punt wacht u op het secundaire knooppunt dat is gemigreerd naar volledig opnieuw synchroniseren met het on-premises knooppunt en schakelt u over naar synchrone replicatieknooppunt en maakt het de AFP.  

#### <a name="step-17-migrate-second-node"></a>Stap 17: Tweede knooppunt migreren

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Stap 18: Instellingen voor schijfcache wijzigen in CSV-bestand en opslaan

Voor gegevensvolumes moeten de cache-instellingen worden ingesteld op READONLY.

Voor TLOG-volumes moeten de cache-instellingen worden ingesteld op GEEN.

![Aanhangsel11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Stap 19: Nieuw onafhankelijk opslagaccount voor secundair knooppunt maken

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>Stap 20: VHDS kopiëren

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

U de VHD-kopiestatus controleren voor alle VHD's:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Aanhangsel12][22]

Wacht tot al deze zijn opgenomen als succes.

Voor informatie voor afzonderlijke blobs:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Stap 21: OS-schijf registreren

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Stap 22: Load Balanced Endpoints en ACL's toevoegen

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>Stap 23: Test failover

Wacht tot het gemigreerde knooppunt synchroniseert met het on-premises Always On-knooppunt. Plaats het in synchrone replicatiemodus en wacht tot het is gesynchroniseerd. Dan failover van on-premises naar de eerste knooppunt gemigreerd, dat is de AFP. Zodra dat heeft gewerkt, verander de laatste gemigreerde knooppunt naar de AFP.

U moet failovers tussen alle knooppunten testen en al chaostests uitvoeren om failovers te laten werken zoals verwacht en in een tijdige landhuis.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Stap 24: Instellingen voor clusterquorum terugzetten / DNS TTL / Failover Pntrs / Synchronisatie-instellingen

##### <a name="adding-ip-address-resource-on-same-subnet"></a>IP-adresbron toevoegen op hetzelfde subnet

Als u slechts twee SQL-servers hebt en deze wilt migreren naar een nieuwe cloudservice, maar deze op hetzelfde subnet wilt houden, u voorkomen dat de listener offline wordt gehaald om het oorspronkelijke IP-adres always on te verwijderen en het nieuwe IP-adres toe te voegen. Als u de VM's migreert naar een ander subnet, hoeft u dit niet te doen omdat er een extra clusternetwerk is dat naar dat subnet verwijst.

Zodra u de gemigreerde secundaire en toegevoegd in de nieuwe IP-adresbron voor de nieuwe cloudservice hebt opgehaald voordat het bestaande primaire mislukt, moet u de volgende stappen uitvoeren in de Cluster Failover Manager:

Zie de bijlage, stap 14, als u ip-adres wilt toevoegen.

1. Wijzig voor de huidige IP-adresbron de mogelijke eigenaar in 'Bestaande primaire SQL Server', in het voorbeeld 'dansqlams4':

    ![Aanhangsel13][23]
2. Wijzig voor de nieuwe IP-adresbron de mogelijke eigenaar in 'Gemigreerde secundaire SQL Server', in het voorbeeld 'dansqlams5':

    ![Aanhangsel14][24]
3. Zodra dit is ingesteld, u failover en wanneer het laatste knooppunt is gemigreerd, moeten de mogelijke eigenaren worden bewerkt, zodat knooppunt wordt toegevoegd als mogelijke eigenaar:

    ![Aanhangsel15][25]

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Premium-opslag](../disks-types.md)
* [Virtuele machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server in Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
