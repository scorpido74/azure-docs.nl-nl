---
title: Azure Premium Storage gebruiken met SQL Server | Microsoft Docs
description: In dit artikel wordt gebruikgemaakt van resources die zijn gemaakt met het klassieke implementatie model en biedt richt lijnen voor het gebruik van Azure Premium Storage met SQL Server die op Azure Virtual Machines worden uitgevoerd.
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965674"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Azure Premium Storage gebruiken met SQL Server op Virtual Machines

## <a name="overview"></a>Overzicht

[Azure Premium ssd's](../disks-types.md) is de volgende generatie opslag met lage latentie en een hoge doorvoer-io. Het werkt het beste voor intensieve i/o-werk belastingen, zoals SQL Server op IaaS [virtual machines](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/management/deployment-models.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

In dit artikel vindt u plannings-en richt lijnen voor het migreren van een virtuele machine met SQL Server om Premium Storage te gebruiken. Dit omvat de stappen voor Azure-infra structuur (netwerken, opslag) en Windows VM-gast. In het voor beeld in de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) ziet u een volledig uitgebreide end-to-end migratie van hoe grotere vm's moeten worden verplaatst om te profiteren van verbeterde lokale SSD-opslag met Power shell.

Het is belang rijk om inzicht te krijgen in het end-to-end proces van het gebruik van Azure Premium Storage met SQL Server op IAAS Vm's. Dit omvat:

* Identificatie van de vereisten voor het gebruik van Premium Storage.
* Voor beelden van de implementatie van SQL Server op IaaS naar Premium Storage voor nieuwe implementaties.
* Voor beelden van het migreren van bestaande implementaties, zowel zelfstandige servers als implementaties met behulp van SQL AlwaysOn-beschikbaarheids groepen.
* Mogelijke migratie benaderingen.
* Volledig end-to-end-voor beeld met Azure, Windows en SQL Server stappen voor de migratie van een bestaande always on-implementatie.

Zie [SQL Server in azure virtual machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor meer achtergrond informatie over SQL Server in azure virtual machines.

**Auteur:** Een van de **volgende technische revisoren:** Luis Carlos Vargas haring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Vereisten voor Premium Storage

Er zijn verschillende vereisten voor het gebruik van Premium Storage.

### <a name="machine-size"></a>Computer grootte

Voor het gebruik van Premium Storage moet u de DS-serie Virtual Machines (VM) gebruiken. Als u nog geen DS Series-computers in uw Cloud service hebt gebruikt, moet u de bestaande virtuele machine verwijderen, de gekoppelde schijven blijven gebruiken en vervolgens een nieuwe Cloud service maken voordat u de VM opnieuw maakt als DS * Role-grootte. Zie [Virtual machine and Cloud service sizes for Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Engelstalig) voor meer informatie over de grootte van virtuele machines.

### <a name="cloud-services"></a>Cloudservices

U kunt DS * Vm's alleen gebruiken met Premium Storage wanneer ze worden gemaakt in een nieuwe Cloud service. Als u SQL Server altijd in azure gebruikt, verwijst de always on-listener naar het interne of externe Load Balancer IP-adres van Azure dat is gekoppeld aan een Cloud service. In dit artikel wordt uitgelegd hoe u migreert tijdens het onderhoud van de beschik baarheid in dit scenario.

> [!NOTE]
> Een DS *-serie moet de eerste VM zijn die wordt geïmplementeerd in de nieuwe Cloud service.
>
>

### <a name="regional-vnets"></a>Regionale VNETS

Voor DS *-Vm's moet u de Virtual Network (VNET) configureren die uw Vm's hosten als regionaal. Dit "breder" is het VNET om de grotere Vm's in andere clusters in te richten en communicatie tussen de virtuele machines mogelijk te maken. In de volgende scherm afbeelding toont de gemarkeerde locatie regionale VNETs, terwijl het eerste resultaat een ' smalle ' VNET bevat.

![RegionalVNET][1]

U kunt een micro soft-ondersteunings ticket genereren om te migreren naar een regionaal VNET. Micro soft brengt vervolgens een wijziging aan. Wijzig de eigenschap AffinityGroup in de netwerk configuratie om de migratie naar regionale VNETs te volt ooien. Exporteer eerst de netwerk configuratie in Power shell en vervang vervolgens de eigenschap **AffinityGroup** in het element **VirtualNetworkSite** door een **locatie** -eigenschap. Geef `Location = XXXX` op waarbij `XXXX` een Azure-regio is. Importeer vervolgens de nieuwe configuratie.

Bijvoorbeeld overwegende over de volgende VNET-configuratie:

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

Als u dit wilt verplaatsen naar een regionaal VNET in Europa-west, wijzigt u de configuratie in het volgende:
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

U moet een nieuw opslag account maken dat is geconfigureerd voor Premium Storage. Houd er rekening mee dat het gebruik van Premium Storage is ingesteld op het opslag account, niet op afzonderlijke Vhd's, maar wanneer u een virtuele machine van de DS *-serie gebruikt, kunt u de VHD koppelen vanuit Premium-en Standard-opslag accounts. U kunt dit overwegen als u de VHD met het besturings systeem niet wilt plaatsen op het Premium Storage-account.

Met de volgende opdracht **New-AzureStorageAccountPowerShell** met het **type** ' Premium_LRS ' maakt u een Premium Storage-account:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Cache-instellingen voor Vhd's

Het belangrijkste verschil tussen het maken van schijven die onderdeel zijn van een Premium Storage account is de schijf cache-instelling. Voor SQL Server gegevens volume schijven wordt aanbevolen om '**Lees cache**' te gebruiken. Voor transactie logboek volumes moet de cache-instelling van de schijf worden ingesteld op**geen**. Dit wijkt af van de aanbevelingen voor standaard opslag accounts.

Zodra de virtuele harde schijven zijn gekoppeld, kan de cache-instelling niet worden gewijzigd. U moet de VHD ontkoppelen en opnieuw koppelen met een bijgewerkte cache-instelling.

### <a name="windows-storage-spaces"></a>Windows-opslag ruimten

U kunt [Windows-opslag ruimten](https://technet.microsoft.com/library/hh831739.aspx) gebruiken zoals u dat eerder hebt gedaan, zodat u een VM kunt migreren die al gebruikmaakt van opslag ruimten. In het voor beeld in [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (stap 9 en volgende) ziet u de Power shell-code voor het uitpakken en importeren van een virtuele machine met meerdere gekoppelde vhd's.

Opslag groepen zijn gebruikt met een standaard Azure-opslag account om de door voer te verbeteren en de latentie te verlagen. U kunt de waarde in opslag groepen testen met Premium Storage voor nieuwe implementaties, maar ze voegen extra complexiteit toe met opslag installatie.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Vaststellen welke virtuele Azure-schijven zijn toegewezen aan opslag groepen

Omdat er verschillende cache-instellingen worden aanbevolen voor gekoppelde Vhd's, kunt u besluiten om de Vhd's te kopiëren naar een Premium Storage-account. Als u ze echter opnieuw koppelt aan de nieuwe VM van de DS-serie, moet u de cache-instellingen mogelijk wijzigen. Het is eenvoudiger om de Premium Storage aanbevolen cache-instellingen toe te passen wanneer u afzonderlijke Vhd's hebt voor de SQL-gegevens bestanden en-logboek bestanden (in plaats van een enkele VHD die beide bevat).

> [!NOTE]
> Als u gegevens en logboek bestanden op hetzelfde volume hebt SQL Server, is de optie die u kiest afhankelijk van de i/o-toegangs patronen voor de werk belasting van uw data base. Alleen testen kunnen laten zien welke cache optie het meest geschikt is voor dit scenario.
>
>

Als u echter Windows-opslag ruimten gebruikt die bestaan uit meerdere Vhd's, moet u de oorspronkelijke scripts bekijken om te bepalen welke gekoppelde Vhd's zich in welke specifieke groep bevinden, zodat u de cache-instellingen voor elke schijf vervolgens kunt instellen.

Als u het oorspronkelijke script niet hebt voor het weer geven van de virtuele harde schijven die aan de opslag groep zijn toegewezen, kunt u de volgende stappen gebruiken om de toewijzing van de schijf/opslag groep te bepalen.

Gebruik de volgende stappen voor elke schijf:

1. Een lijst met schijven die zijn gekoppeld aan de VM ophalen met de opdracht **Get-AzureVM** :

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Noteer de diskette en LUN.

    ![DisknameAndLUN][2]
1. Extern bureau blad in de VM. Ga vervolgens naar **computer beheer** | **Apparaatbeheer** | **schijf stations**. Bekijk de eigenschappen van elk van de ' virtuele micro soft-schijven '

    ![VirtualDiskProperties][3]
1. Het LUN-nummer hier is een verwijzing naar het LUN-nummer dat u opgeeft wanneer u de VHD koppelt aan de virtuele machine.
1. Ga naar het tabblad **Details** van micro soft Virtual Disk en ga in de lijst met **Eigenschappen** naar de **stuur programma-sleutel**. In de **waarde**ziet u de **verschuiving**, die 0002 in de volgende scherm afbeelding. De 0002 geeft de PhysicalDisk2 aan waarnaar de opslag groep verwijst.

    ![VirtualDiskPropertyDetails][4]
1. Voor elke opslag groep dumpen de gekoppelde schijven:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Nu kunt u deze informatie gebruiken om gekoppelde Vhd's aan fysieke schijven in opslag groepen te koppelen.

Zodra u Vhd's aan fysieke schijven in opslag groepen hebt toegewezen, kunt u deze loskoppelen en kopiëren naar een Premium Storage-account en deze vervolgens koppelen aan de juiste cache-instelling. Zie het voor beeld in de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), stappen 8 tot en met 12. In deze stappen ziet u hoe u een aan een virtuele machine gekoppelde VHD-schijf configuratie uitpakt naar een CSV-bestand, de Vhd's kopieert, de cache-instellingen van de schijf configuratie wijzigt en ten slotte de virtuele machine opnieuw implementeert als een DS-serie-VM met alle gekoppelde schijven.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM-opslag bandbreedte en door Voer van VHD-opslag

De hoeveelheid opslag prestaties is afhankelijk van de DS * VM-grootte die is opgegeven en de grootte van de VHD. De Vm's hebben verschillende limieten voor het aantal Vhd's dat kan worden bijgevoegd en de maximale band breedte die ze ondersteunen (MB/s). Zie [Virtual machine and Cloud service sizes for Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Engelstalig) voor de specifieke bandbreedte nummers.

Verhoogde IOPS worden behaald met grotere schijf grootten. Houd rekening met het volgende wanneer u van plan bent om uw migratie traject te bekijken. [Zie de tabel voor IOPS en schijf typen](../disks-types.md#premium-ssd)voor meer informatie.

Ten slotte moet u overwegen dat Vm's verschillende maximale schijf bandbreedten hebben die worden ondersteund voor alle gekoppelde schijven. Onder hoge belasting kunt u de maximale schijf bandbreedte die beschikbaar is voor de grootte van de VM-rol intensiteit. Een Standard_DS14 ondersteunt bijvoorbeeld Maxi maal 512 MB/s; Daarom kunt u met drie P30-schijven de schijf bandbreedte van de virtuele machine verzadigen. Maar in dit voor beeld kan de doorvoer limiet echter worden overschreden, afhankelijk van de combi natie van lezen en schrijven IOs.

## <a name="new-deployments"></a>Nieuwe implementaties

In de volgende twee secties ziet u hoe u SQL Server Vm's kunt implementeren op Premium Storage. Zoals eerder vermeld, hoeft u de besturingssysteem schijf niet noodzakelijkerwijs op Premium Storage te plaatsen. U kunt ervoor kiezen dit te doen als u een intensieve IO-werk belasting wilt plaatsen op de VHD van het besturings systeem.

In het eerste voor beeld ziet u hoe u bestaande Azure Gallery-installatie kopieën gebruikt. In het tweede voor beeld ziet u hoe u een aangepaste VM-installatie kopie gebruikt die u in een bestaand standaard opslag account hebt.

> [!NOTE]
> In deze voor beelden wordt ervan uitgegaan dat u al een regionaal VNET hebt gemaakt.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Een nieuwe virtuele machine maken met Premium Storage met een galerie-afbeelding

In het onderstaande voor beeld ziet u hoe u de VHD van het besturings systeem in Premium Storage plaatst en Premium Storage Vhd's koppelt. U kunt de besturingssysteem schijf echter ook in een Standard-opslag account plaatsen en vervolgens Vhd's koppelen die zich in een Premium Storage-account bevinden. Beide scenario's worden gedemonstreerd.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Stap 1: een Premium Storage-account maken

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Stap 2: een nieuwe Cloud service maken

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Stap 3: een Cloud service-VIP reserveren (optioneel)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Stap 4: een VM-container maken

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Stap 5: VHD met besturings systeem op Standard of Premium Storage plaatsen

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Stap 6: een VM maken

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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Een nieuwe virtuele machine maken om Premium Storage te gebruiken met een aangepaste installatie kopie

Dit scenario laat zien hoe u bestaande aangepaste installatie kopieën hebt die zich bevinden in een Standard-opslag account. Zoals vermeld als u de VHD met het besturings systeem op Premium Storage wilt plaatsen, moet u de installatie kopie die in het standaard opslag account bestaat, kopiëren naar een Premium Storage voordat u deze kunt gebruiken. Als u een installatie kopie on-premises hebt, kunt u deze methode ook gebruiken om die rechtstreeks naar het Premium Storage-account te kopiëren.

#### <a name="step-1-create-storage-account"></a>Stap 1: een opslag account maken

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Stap 2 Cloud service maken

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Stap 3: een bestaande installatie kopie gebruiken

U kunt een bestaande installatie kopie gebruiken. U kunt ook [een installatie kopie van een bestaande machine maken](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Let op dat de computer die u hebt geïnstalleerd, geen DS *-machine hoeft te zijn. Zodra u de installatie kopie hebt, ziet u in de volgende stappen hoe u deze kunt kopiëren naar het Premium Storage-account met de Power shell **-Commandlet start-AzureStorageBlobCopy** .

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Stap 4: BLOB kopiëren tussen opslag accounts

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Stap 5: de Kopieer status regel matig controleren:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Stap 6: afbeeldings schijf toevoegen aan Azure Disk-opslag plaats in abonnement

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Hoewel de status rapporteert als geslaagd, kunt u nog steeds een fout in de schijf lease ophalen. Wacht in dit geval ongeveer 10 minuten.

#### <a name="step-7--build-the-vm"></a>Stap 7: de VM bouwen

Hier bouwt u de virtuele machine op basis van uw installatie kopie en voegt u twee Premium Storage Vhd's toe:

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

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Bestaande implementaties die geen gebruik maken van AlwaysOn-beschikbaarheids groepen

> [!NOTE]
> Voor bestaande implementaties raadpleegt u eerst de sectie [vereisten](#prerequisites-for-premium-storage) van dit artikel.

Er zijn verschillende overwegingen voor SQL Server-implementaties die geen gebruik maken van AlwaysOn-beschikbaarheids groepen en die dat doen. Als u geen gebruik maakt van always on en een bestaande zelfstandige SQL Server hebt, kunt u een upgrade uitvoeren naar Premium Storage met behulp van een nieuw Cloud service-en opslag account. Houd rekening met de volgende opties:

* **Maak een nieuwe SQL Server VM**. U kunt een nieuwe SQL Server VM maken die gebruikmaakt van een Premium Storage-account, zoals beschreven in nieuwe implementaties. Maak vervolgens een back-up en herstel de SQL Server configuratie en gebruikers databases. De toepassing moet worden bijgewerkt om te verwijzen naar de nieuwe SQL Server als deze intern of extern wordt geopend. U moet alle objecten van de data base kopiëren alsof u een gelijktijdige (gelijktijdige) SQL Server migratie uitvoert. Dit omvat objecten als aanmeldingen, certificaten en gekoppelde servers.
* **Migreer een bestaande SQL Server-VM**. Hiervoor moet de SQL Server-VM offline worden gezet en vervolgens worden overgebracht naar een nieuwe Cloud service, zoals het kopiëren van alle gekoppelde Vhd's naar het Premium Storage-account. Wanneer de virtuele machine online is, verwijst de toepassing naar de hostnaam van de server. Houd er rekening mee dat de grootte van de bestaande schijf invloed heeft op de prestatie kenmerken. Een schijf van 400 GB wordt bijvoorbeeld afgerond naar een P20. Als u weet dat u geen schijf prestaties nodig hebt, kunt u de virtuele machine opnieuw maken als een DS-serie-VM en Premium Storage Vhd's koppelen van de gewenste grootte/prestatie specificatie. Vervolgens kunt u de SQL DB-bestanden ontkoppelen en opnieuw koppelen.

> [!NOTE]
> Bij het kopiëren van de schijven van de VHD moet u rekening houden met de grootte, afhankelijk van de grootte betekent wat Premium Storage schijf type in die waar ze zijn, bepaalt dit de schijf prestatie specificatie. Azure wordt naar boven afgerond op de dichtstbijzijnde schijf, dus als u een schijf van 400 GB hebt, wordt deze naar boven afgerond naar een P20. Afhankelijk van uw bestaande IO-vereisten van de VHD van het besturings systeem hoeft u dit niet te migreren naar een Premium Storage-account.

Als uw SQL Server extern wordt geopend, wordt de Cloud service-VIP gewijzigd. U moet ook de eind punten, Acl's en DNS-instellingen bijwerken.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Bestaande implementaties die gebruikmaken van AlwaysOn-beschikbaarheids groepen

> [!NOTE]
> Voor bestaande implementaties raadpleegt u eerst de sectie [vereisten](#prerequisites-for-premium-storage) van dit artikel.

In het begin van deze sectie kijken we naar hoe altijd aan de interactie met Azure-netwerken moet worden voldaan. Vervolgens worden er migraties onderverdeeld in twee scenario's: migraties waarbij enige downtime kan worden toegestaan en migraties waarbij u minimale downtime moet bezorgen.

On-premises SQL Server AlwaysOn-beschikbaarheids groepen gebruiken een listener on-premises die een virtuele DNS-naam registreert samen met een IP-adres dat wordt gedeeld tussen een of meer SQL-servers. Wanneer clients verbinding maken, worden ze via het IP-adres van de listener gerouteerd naar de primaire SQL Server. Dit is de server die eigenaar is van de always-op-IP-resource op dat moment.

![DeploymentsUseAlways op][6]

In Microsoft Azure kunt u slechts één IP-adres toewijzen aan een NIC op de virtuele machine, zodat Azure gebruikmaakt van het IP-adres dat is toegewezen aan de interne/externe load balancers (ILB/ELB) om dezelfde laag van de abstractie te krijgen als on-premises. De IP-bron die wordt gedeeld tussen de servers, wordt ingesteld op hetzelfde IP-adres als de ILB/ELB. Dit wordt gepubliceerd in de DNS en client verkeer wordt door gegeven via de ILB-ELB naar de primaire SQL Server replica. De ILB/ELB weet welke SQL Server primair is, omdat er tests worden gebruikt om de always on-IP-bron te testen. In het vorige voor beeld wordt elk knoop punt onderzocht met een eind punt waarnaar wordt verwezen door de ELB/ILB, afhankelijk van wat reageert op de primaire SQL Server.

> [!NOTE]
> De ILB en ELB zijn beide toegewezen aan een bepaalde Azure-Cloud service. Daarom betekent elke Cloud migratie in azure waarschijnlijk dat het Load Balancer IP-adres wordt gewijzigd.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Altijd migreren op implementaties die enige downtime kunnen toestaan

Er zijn twee strategieën voor het migreren van AlwaysOn-implementaties die enige downtime toestaan:

1. **Meer secundaire replica's toevoegen aan een bestaand, altijd op cluster**
2. **Migreren naar een nieuw cluster voor altijd**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Voeg meer secundaire Replica's toe aan een bestaand, altijd op het cluster

Een strategie bestaat uit het toevoegen van meer secundaire zones aan de AlwaysOn-beschikbaarheids groep. U moet deze toevoegen aan een nieuwe Cloud service en de listener bijwerken met het nieuwe load balancer IP-adres.

##### <a name="points-of-downtime"></a>Punten van downtime:

* Cluster validatie.
* Testen altijd op failovers voor nieuwe secundaire zones.

Als u Windows-opslag groepen in de virtuele machine gebruikt voor een hogere IO-door Voer, worden deze offline gehaald tijdens een volledige cluster validatie. De validatie test is vereist wanneer u knoop punten aan het cluster toevoegt. De tijd die nodig is om de test uit te voeren, kan variëren, dus u moet dit in uw representatieve test omgeving testen om een benadering te krijgen van hoe lang dit duurt.

U dient tijd in te richten waar u hand matige failover en chaos-tests kunt uitvoeren op de zojuist toegevoegde knoop punten om ervoor te zorgen dat de functies voor hoge Beschik baarheid altijd zoals verwacht worden uitgevoerd.

![DeploymentUseAlways On2][7]

> [!NOTE]
> U moet alle exemplaren van SQL Server stoppen waarbij de opslag groepen worden gebruikt voordat de validatie wordt uitgevoerd.
>
> ##### <a name="high-level-steps"></a>Stappen op hoog niveau
>

1. Maak twee nieuwe SQL-servers in een nieuwe Cloud service met gekoppelde Premium Storage.
2. Kopieer de volledige back-ups en herstel met **norecovery**.
3. Kopiëren van de afhankelijke objecten van de gebruikers database, zoals aanmeldingen, enzovoort.
4. Maak een nieuwe interne Load Balancer (ILB) of gebruik een externe Load Balancer (ELB) en stel vervolgens eind punten met gelijke taak verdeling in op beide nieuwe knoop punten.

   > [!NOTE]
   > Controleer of alle knoop punten de juiste eindpunt configuratie hebben voordat u doorgaat
   >
   >
5. Stop de toegang van gebruikers/toepassingen tot de SQL Server (bij gebruik van opslag groepen).
6. Stop SQL Server Engine Services op alle knoop punten (bij gebruik van opslag groepen).
7. Nieuwe knoop punten toevoegen aan het cluster en volledige validatie uitvoeren.
8. Nadat de validatie is voltooid, start u alle SQL Server services.
9. Back-uptransactie logboeken en herstel van gebruikers databases.
10. Voeg nieuwe knoop punten toe aan de AlwaysOn-beschikbaarheids groep en plaats replicatie **synchroon**.
11. Voeg de IP-adres resource van de nieuwe Cloud service ILB/ELB via Power shell toe voor Always on op basis van het voor beeld van meerdere locaties in de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). Stel in Windows Clustering de **mogelijke eigen aars** van de **IP-adres** bron in op de nieuwe knoop punten oud. Zie de sectie IP-adres resource op hetzelfde subnet toevoegen van de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Failover naar een van de nieuwe knoop punten.
13. Maak de nieuwe knoop punten automatische failover-partners en testfailover.
14. Verwijder de oorspronkelijke knoop punten uit de beschikbaarheids groep.

##### <a name="advantages"></a>Voordelen

* Nieuwe SQL-servers kunnen worden getest (SQL Server en toepassing) voordat ze worden toegevoegd aan always on.
* U kunt de grootte van de virtuele machine wijzigen en de opslag aanpassen aan uw exacte vereisten. Het is echter nuttig om alle SQL-bestands paden hetzelfde te laten.
* U kunt bepalen wanneer de overdracht van de back-ups van de Data Base naar de secundaire Replica's wordt gestart. Dit wijkt af van het gebruik van de **AzureStorageBlobCopy-** Commandlet van Azure voor het kopiëren van vhd's, omdat dat een asynchrone kopie is.

##### <a name="disadvantages"></a>Nadelen

* Wanneer u Windows-opslag groepen gebruikt, wordt de uitval van het cluster tijdens de volledige cluster validatie voor de nieuwe extra knoop punten.
* Afhankelijk van de SQL Server-versie en het bestaande aantal secundaire replica's, kunt u mogelijk niet meer secundaire replica's toevoegen zonder bestaande zones te verwijderen.
* Tijdens het instellen van de secundaire zones kan er sprake zijn van langdurige tijd voor SQL-gegevens overdracht.
* Tijdens de migratie worden extra kosten in rekening gebracht terwijl er parallelle computers worden uitgevoerd.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. migreren naar een nieuw cluster voor altijd

Een andere strategie is het maken van een gloed nieuw, altijd on-cluster met gloed nieuwe knoop punten in een nieuwe Cloud service en vervolgens de clients omleiden om deze te gebruiken.

##### <a name="points-of-downtime"></a>Punten van uitval tijd

Er is een uitval tijd wanneer u toepassingen en gebruikers overbrengt naar de nieuwe, altijd op listener. De downtime is afhankelijk van:

* De tijd die nodig is voor het herstellen van definitieve back-ups van transactie logboeken naar data bases op nieuwe servers.
* De tijd die nodig is om client toepassingen bij te werken voor het gebruik van de nieuwe always on-listener.

##### <a name="advantages"></a>Voordelen

* U kunt de werkelijke productie omgeving, het SQL Server en de build van het besturings systeem testen.
* U hebt de mogelijkheid om de opslag aan te passen en om de grootte van de virtuele machine mogelijk te beperken. Dit kan leiden tot een onkosten verlaging.
* U kunt uw SQL Server build of versie bijwerken tijdens dit proces. U kunt ook een upgrade van het besturings systeem uitvoeren.
* Het vorige always-cluster kan optreden als een vast terugdraai doel.

##### <a name="disadvantages"></a>Nadelen

* U moet de DNS-naam van de listener wijzigen als u beide altijd op clusters tegelijk wilt uitvoeren. Dit voegt beheer overhead toe tijdens de migratie, omdat client toepassings teken reeksen moeten overeenkomen met de nieuwe naam van de listener.
* U moet een synchronisatie mechanisme tussen de twee omgevingen implementeren om ze zo dicht mogelijk te houden om de definitieve synchronisatie vereisten vóór de migratie tot een minimum te beperken.
* Er zijn kosten toegevoegd tijdens de migratie terwijl u de nieuwe omgeving uitvoert.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Altijd migreren op implementaties voor minimale downtime

Er zijn twee strategieën voor het migreren van AlwaysOn-implementaties voor minimale downtime:

1. **Een bestaande secundaire: Eén site gebruiken**
2. **Bestaande secundaire replica (s) gebruiken: meerdere locaties**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. gebruik een bestaande secundaire: één site

Een strategie voor minimale downtime is om een bestaande Cloud secundair te maken en deze te verwijderen uit de huidige Cloud service. Kopieer vervolgens de Vhd's naar het nieuwe Premium Storage-account en maak de virtuele machine in de nieuwe Cloud service. Werk vervolgens de listener bij in clustering en failover.

##### <a name="points-of-downtime"></a>Punten van uitval tijd

* Er is een downtime wanneer u het laatste knoop punt bijwerkt met het eind punt met gelijke taak verdeling.
* De client opnieuw verbinding kan worden vertraagd, afhankelijk van uw client-en DNS-configuratie.
* Er is extra uitval tijd als u ervoor kiest om de cluster groep altijd offline te halen om de IP-adressen te wisselen. U kunt dit voor komen door een-of-afhankelijkheid en mogelijke eigen aars voor de toegevoegde IP-adres bron te gebruiken. Zie de sectie IP-adres resource op hetzelfde subnet toevoegen van de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Wanneer u wilt dat het toegevoegde knoop punt wordt Partake als een always on-failover-partner, moet u een Azure-eindpunt toevoegen met een verwijzing naar de set met gelijke taak verdeling. Wanneer u de opdracht **add-AzureEndpoint** uitvoert om dit te doen, blijven huidige verbindingen geopend, maar kunnen er geen nieuwe verbindingen met de listener tot stand worden gebracht totdat de Load Balancer is bijgewerkt. In deze test is de laatste 90-120seconds gedetecteerd. dit moet worden getest.

##### <a name="advantages"></a>Voordelen

* Geen extra kosten gemaakt tijdens de migratie.
* Een een-op-een-migratie.
* Minder complexiteit.
* Hiermee kunnen gestegen IOPS van Premium Storage Sku's worden uitgebreid. Wanneer de schijven zijn losgekoppeld van de virtuele machine en naar de nieuwe Cloud service worden gekopieerd, kan een hulp programma van derden worden gebruikt om de VHD-grootte te verg Roten. Dit biedt hogere door voer. Zie deze [Forum discussie](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)voor meer informatie over de grootte van virtuele harde schijven.

##### <a name="disadvantages"></a>Nadelen

* Er is een tijdelijk verlies van HA en DR tijdens de migratie.
* Aangezien dit een 1:1-migratie is, moet u een minimale VM-grootte gebruiken die uw aantal Vhd's ondersteunt, zodat u uw Vm's mogelijk niet kunt krimpen.
* In dit scenario wordt de Azure **Start-AzureStorageBlobCopy** -commandlet gebruikt die asynchroon is. Er is geen SLA voor het volt ooien van de kopie. De tijd van de kopieën varieert, maar dit is afhankelijk van wacht tijden in de wachtrij, maar is ook afhankelijk van de hoeveelheid gegevens die u wilt overdragen. De Kopieer tijd wordt verhoogd als de overdracht naar een ander Azure-data centrum gaat dat Premium Storage in een andere regio ondersteunt. Als u slechts twee knoop punten hebt, moet u rekening houden met een mogelijke oplossing voor het geval de kopie langer duurt dan bij het testen. Dit kan de volgende ideeën bevatten.
  * Voeg een tijdelijk derde SQL Server knoop punt voor HA toe vóór de migratie met de overeengekomen downtime.
  * Voer de migratie buiten het geplande onderhoud van Azure uit.
  * Zorg ervoor dat u het cluster quorum correct hebt geconfigureerd.  

##### <a name="high-level-steps"></a>Stappen op hoog niveau

In dit document wordt niet het volledige end-to-end-voor beeld gedemonstreerd, maar de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) bevat details die kunnen worden gebruikt om dit uit te voeren.

![MinimalDowntime][8]

* Verzamel de schijf configuratie en verwijder het knoop punt (geen gekoppelde Vhd's verwijderen).
* Een Premium Storage account maken en Vhd's kopiëren vanuit het standaard opslag account
* Maak een nieuwe Cloud service en implementeer de SQL2-VM opnieuw in die Cloud service. Maak de virtuele machine met de gekopieerde oorspronkelijke besturingssysteem-VHD en koppelt de gekopieerde Vhd's.
* Configureer ILB/ELB en voeg eind punten toe.
* Listener bijwerken door een van de volgende opties:
  * De always on-groep offline halen en de always on-listener bijwerken met het nieuwe ILB/ELB IP-adres.
  * Of Voeg de IP-adres resource van de nieuwe Cloud service ILB/ELB toe via Power shell in Windows-Clustering. Stel vervolgens de mogelijke eigen aars van de IP-adres bron in op het gemigreerde knoop punt, SQL2 en stel dit in als of afhankelijkheid in de netwerk naam. Zie de sectie IP-adres resource op hetzelfde subnet toevoegen van de [bijlage](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Controleer de DNS-configuratie/doorgifte naar de clients.
* Migreer SQL1-VM en ga door met stap 2 tot en met 4.
* Als u de stappen 5II gebruikt, voegt u SQL1 als mogelijke eigenaar toe voor de toegevoegde IP-adres resource
* Testfailover.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. gebruik bestaande secundaire replica (s): meerdere locaties

Als u knoop punten in meer dan één Azure-Data Center (DC) hebt of als u een hybride omgeving hebt, kunt u in deze omgeving een permanente configuratie gebruiken om de downtime te minimaliseren.

De benadering is het wijzigen van de synchronisatie altijd op synchrone voor de on-premises of secundaire Azure-domein controller en vervolgens een failover naar die SQL Server. Kopieer vervolgens de Vhd's naar een Premium Storage-account en implementeer de machine opnieuw in een nieuwe Cloud service. Werk de listener bij en klik vervolgens op failback.

##### <a name="points-of-downtime"></a>Punten van uitval tijd

De downtime bestaat uit de tijd om een failover naar de alternatieve DC en terug te maken. Het is ook afhankelijk van uw client-en DNS-configuratie en de client opnieuw verbinding kan worden vertraagd.
Bekijk het volgende voor beeld van een hybride altijd on-configuratie:

![MultiSite1][9]

##### <a name="advantages"></a>Voordelen

* U kunt gebruikmaken van de bestaande infra structuur.
* U hebt de mogelijkheid om eerst de Azure-opslag op de DR Azure-DC te upgraden.
* De DR-opslag van Azure DC kan opnieuw worden geconfigureerd.
* Er zijn tijdens de migratie mini maal twee failovers, met uitzonde ring van test-failovers.
* U hoeft SQL Server gegevens niet te verplaatsen met back-up en herstel.

##### <a name="disadvantages"></a>Nadelen

* Afhankelijk van de client toegang tot SQL Server, is er mogelijk een grotere latentie wanneer SQL Server wordt uitgevoerd in een andere domein controller voor de toepassing.
* De Kopieer tijd van Vhd's naar Premium Storage kan lang zijn. Dit kan van invloed zijn op uw beslissing of het knoop punt in de beschikbaarheids groep moet worden bewaard. Houd er rekening mee dat wanneer tijdens de migratie logboek intensieve werk belasting wordt uitgevoerd, omdat het primaire knoop punt de niet-gerepliceerde trans acties in het transactie logboek moet houden. Dit kan daarom aanzienlijk toenemen.
* In dit scenario wordt de Azure **Start-AzureStorageBlobCopy** -commandlet gebruikt die asynchroon is. Er is geen SLA na voltooiing. De tijd van de kopieën varieert, maar dit is afhankelijk van wacht tijden in de wachtrij, maar is ook afhankelijk van de hoeveelheid gegevens die u wilt overdragen. Daarom hebt u slechts één knoop punt in het tweede Data Center, maar u moet de stappen voor het oplossen van problemen met de kopie langer duren dan bij het testen. Deze beperkings stappen zijn onder andere de volgende ideeën:
  * Voeg een tijdelijk 2e tweede SQL-knoop punt voor HA toe vóór de migratie met de overeengekomen downtime.
  * Voer de migratie buiten het geplande onderhoud van Azure uit.
  * Zorg ervoor dat u het cluster quorum correct hebt geconfigureerd.

In dit scenario wordt ervan uitgegaan dat u uw installatie hebt gedocumenteerd en weet u hoe de opslag is toegewezen, zodat u wijzigingen kunt aanbrengen in de instellingen voor optimale schijf cache.

##### <a name="high-level-steps"></a>Stappen op hoog niveau

![Multisite2][10]

* Zorg ervoor dat de on-premises/alternatieve Azure-DC de SQL Server primaire is en zorg ervoor dat de andere automatische failover-partner (AFP) is.
* Informatie over de schijf configuratie verzamelen van SQL2 en het knoop punt verwijderen (gekoppelde Vhd's niet verwijderen).
* Maak een Premium Storage-account en kopieer Vhd's uit het standaard opslag account.
* Maak een nieuwe Cloud service en maak de SQL2-VM met de Premium-opslag schijven die zijn gekoppeld.
* Configureer ILB/ELB en voeg eind punten toe.
* Werk de always on-listener bij met het nieuwe ILB/ELB IP-adres en de testfailover.
* Controleer de DNS-configuratie.
* Wijzig de AFP in SQL2 en migreer SQL1 en ga door met stap 2 tot en met 5.
* Testfailover.
* Schakel de AFP terug naar SQL1 en SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Bijlage: een multi-site altijd op een cluster migreren naar Premium Storage

In de rest van dit artikel vindt u een gedetailleerd voor beeld van het converteren van een multi-site-cluster naar Premium-opslag. Ook wordt de listener geconverteerd van het gebruik van een externe load balancer (ELB) naar een interne load balancer (ILB).

### <a name="environment"></a>Omgeving

* Windows 2k12 / SQL 2k12
* 1 DB-bestanden in SP
* 2 x opslag groepen per knoop punt

![Appendix1][11]

### <a name="vm"></a>VM

In dit voor beeld laten we zien hoe u overstapt van een ELB naar ILB. ELB is beschikbaar vóór ILB, dus hier ziet u hoe u kunt overschakelen naar ILB tijdens de migratie.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Voorbereidende stappen: verbinding maken met abonnement

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Stap 1: nieuw opslag account en Cloud service maken

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Stap 2: Verhoog de toegestane fouten voor resources \<optioneel >

Voor bepaalde resources die deel uitmaken van uw beschikbaarheids groep altijd, gelden er limieten voor het aantal storingen dat in een bepaalde periode kan optreden, waarbij de Cluster service de resource groep probeert te starten. U kunt dit het beste verhogen, omdat u deze procedure doorloopt, omdat als u niet hand matig failovers doorvoert en failovers doorschakelt door machines af te sluiten, kan deze limiet bijna worden bereikt.

Het is verstandig om de fout limiet te verdubbelen. Als u dit wilt doen in Failoverclusterbeheer, gaat u naar de eigenschappen van de always on-resource groep:

![Appendix3][13]

Wijzig het maximum aantal fouten in 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Stap 3: extra IP-adres bron voor cluster groep \<optioneel >

Als u slechts één IP-adres voor de cluster groep hebt en dit is afgestemd op het Cloud subnet, moet u er rekening mee houden als u per ongeluk alle cluster knooppunten in de Cloud op dat netwerk offline neemt, de cluster-IP-bron en de cluster netwerk naam niet online kunnen worden gebracht. In deze situatie voor komt u dat er updates voor andere cluster bronnen worden bijgewerkt.

#### <a name="step-4-dns-configuration"></a>Stap 4: DNS-configuratie

Het implementeren van een soepele overgang is afhankelijk van hoe DNS wordt gebruikt en wordt bijgewerkt.
Als er altijd op is geïnstalleerd, wordt er een Windows-cluster resource groep gemaakt. Als u Failoverclusterbeheer opent, ziet u dat er mini maal drie resources zijn, de twee waarnaar het document verwijst:

* Virtual Network naam (VNN): de DNS-naam waarmee clients verbinding maken wanneer ze verbinding willen maken met SQL-servers via always on.
* IP-adres bron: het IP-adres dat aan de VNN is gekoppeld, u kunt meer dan één, en in een configuratie voor meerdere locaties hebt u een IP-adres per site/subnet.

Wanneer u verbinding maakt met SQL Server, worden de DNS-records die zijn gekoppeld aan de listener opgehaald door het stuur programma van SQL Server-client en wordt geprobeerd verbinding te maken met elk altijd op het bijbehorende IP-adres. Vervolgens bespreken we een aantal factoren waarmee dit kan worden beïnvloed.

Het aantal gelijktijdige DNS-records dat is gekoppeld aan de naam van de listener is niet alleen afhankelijk van het aantal toegewezen IP-adressen, maar de ' RegisterAllIpProviders'setting in Failoverclustering voor de always ON VNN-resource.

Wanneer u AlwaysOn in azure implementeert, zijn er verschillende stappen voor het maken van de listener-en IP-adressen. u moet de ' RegisterAllIpProviders ' hand matig configureren op 1. Dit is anders dan bij een on-premises AlwaysOn-implementatie waar deze al is ingesteld op 1.

Als ' RegisterAllIpProviders ' 0 is, ziet u slechts één DNS-record in DNS die is gekoppeld aan de listener:

![Appendix4][14]

Als ' RegisterAllIpProviders ' is 1:

![Appendix5][15]

Met de onderstaande code worden de VNN-instellingen en wordt deze voor u ingesteld. Als u de wijziging wilt door voeren, moet u de VNN offline halen en weer online zetten. Dit zorgt ervoor dat de listener offline zorgt voor onderbreking van de client verbinding.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

In een latere stap van de migratie moet u de always on-listener bijwerken met een bijgewerkt IP-adres dat verwijst naar een load balancer. Dit houdt in dat een IP-adres bron wordt verwijderd en toegevoegd. Nadat de IP-update is uitgevoerd, moet u ervoor zorgen dat het nieuwe IP-adres is bijgewerkt in DNS-zone en dat de clients hun lokale DNS-cache bijwerken.

Als uw clients zich in een ander netwerk segment bevinden en verwijzen naar een andere DNS-server, moet u overwegen wat er gebeurt met DNS-zone overdracht tijdens de migratie, omdat de tijd voor het opnieuw verbinden van de toepassing wordt beperkt door ten minste de zone overdrachts tijd van een nieuw IP-adres adressen voor de listener. Als u hier een beperking voor de tijd hebt, moet u een incrementele zone overdracht met uw Windows-teams bespreken en testen, en de DNS-hostrecord ook in een trage TTL (time-to-Live) plaatsen, zodat de clients worden bijgewerkt. Zie [incrementele zone transfers](https://technet.microsoft.com/library/cc958973.aspx) en [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer)voor meer informatie.

Standaard is de TTL voor DNS-record die is gekoppeld aan de listener in de always on in azure 1200 seconden. U kunt dit beperken als u tijdens de migratie een tijds beperking ondervindt om ervoor te zorgen dat de clients hun DNS bijwerken met het bijgewerkte IP-adres voor de listener. U kunt de configuratie bekijken en wijzigen door de configuratie van de VNN te dumpen:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> De lagere ' HostRecordTTL ' is een hogere hoeveelheid DNS-verkeer.

##### <a name="client-application-settings"></a>Instellingen voor client toepassing

Als uw SQL-client toepassing .NET 4,5 SQLClient ondersteunt, kunt u het tref woord ' MULTISUBNETFAILOVER = TRUE ' gebruiken. Dit sleutel woord moet worden toegepast, omdat het een snellere verbinding met de beschikbaarheids groep SQL always tijdens de failover mogelijk maakt. Het inventariseren van alle IP-adressen die zijn gekoppeld aan de always on-listener parallel, en voert een agressieve snelheid van de TCP-verbinding tijdens een failover uit.

Zie [MultiSubnetFailover-tref woord en bijbehorende functies](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)voor meer informatie over de vorige instellingen. Zie ook [SqlClient-ondersteuning voor hoge Beschik baarheid, herstel na nood gevallen](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Stap 5: cluster quorum instellingen

Wanneer u ten minste één SQL Server tegelijk gaat uitnemen, moet u de quorum instelling van het cluster aanpassen als u bestands share-Witness (FSW) met twee knoop punten gebruikt, moet u het quorum zo instellen dat knooppunt meerderheid wordt toegestaan en dynamische stemmen worden gebruikt , waardoor een enkel knoop punt stand kan blijven.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Zie [het quorum configureren en beheren in een Windows Server 2012-failovercluster](https://technet.microsoft.com/library/jj612870.aspx)voor meer informatie over het beheren en configureren van het cluster quorum.

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Stap 6: bestaande eind punten en Acl's extra heren

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Sla deze tekst op in een bestand.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Stap 7: failover-partners en replicatie modi wijzigen

Als u meer dan twee SQL-servers hebt, moet u de failover van een ander secundair in een andere domein controller of on-premises wijzigen in ' synchroon ' en het maken van een automatische failover-partner (AFP), zodat u HA kunt onderhouden terwijl u wijzigingen aanbrengt. U kunt dit doen via TSQL van Modify, maar SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Stap 8: secundaire VM verwijderen uit Cloud service

U moet eerst plannen dat u een secundair Cloud knooppunt wilt migreren. Als dit knoop punt momenteel primair is, moet u een hand matige failover initiëren.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Stap 9: instellingen voor schijf cache wijzigen in CSV-bestand en opslaan

Voor gegevens volumes moet deze worden ingesteld op READONLY.

Voor TLOG-volumes moet u deze instellen op geen.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Stap 10: VHD'S kopiëren

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


U kunt de Kopieer status van de Vhd's controleren naar het Premium Storage-account:

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

![Appendix8][18]

Wacht tot alle deze zijn geregistreerd als geslaagd.

Voor informatie over afzonderlijke blobs:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Stap 11: de besturingssysteem schijf registreren

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Stap 12: secundaire importeren in een nieuwe Cloud service

In de onderstaande code wordt ook de optie extra gebruikt. u kunt de machine importeren en het te behouden VIP gebruiken.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Stap 13: ILB maken op nieuwe Cloud-SVC, eind punten en Acl's voor taak verdeling toevoegen

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

#### <a name="step-14-update-always-on"></a>Stap 14: update always on

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

![Appendix9][19]

Verwijder nu het oude IP-adres van de Cloud service.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Stap 15: controle van DNS-updates

Controleer nu de DNS-servers op uw SQL Server-client netwerken en zorg ervoor dat clustering de extra hostrecord voor het toegevoegde IP-adres heeft toegevoegd. Als deze DNS-servers niet zijn bijgewerkt, kunt u overwegen om een DNS-zone overdracht af te dwingen en ervoor te zorgen dat de clients in dat subnet kunnen omzetten naar beide altijd op IP-adressen. Dit betekent dat u niet hoeft te wachten op automatische DNS-replicatie.

#### <a name="step-16-reconfigure-always-on"></a>Stap 16: altijd opnieuw configureren

Op dit moment wacht u op het secundaire knoop punt dat is gemigreerd om volledig opnieuw te synchroniseren met het on-premises knoop punt en over te scha kelen naar het knoop punt synchrone replicatie en de AFP te maken.  

#### <a name="step-17-migrate-second-node"></a>Stap 17: tweede knoop punt migreren

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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Stap 18: instellingen voor het opslaan van schijven in CSV-bestand wijzigen en opslaan

Voor gegevens volumes moeten de cache-instellingen worden ingesteld op READONLY.

De cache-instellingen voor TLOG-volumes moeten worden ingesteld op geen.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Stap 19: nieuw onafhankelijk opslag account maken voor secundair knoop punt

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

#### <a name="step-20-copy-vhds"></a>Stap 20: VHD'S kopiëren

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

U kunt de status van de VHD-kopie controleren voor alle Vhd's:

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

![Appendix12][22]

Wacht tot alle deze zijn geregistreerd als geslaagd.

Voor informatie over afzonderlijke blobs:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Stap 21: de besturingssysteem schijf registreren

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Stap 22: eind punten en Acl's voor taak verdeling toevoegen

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

#### <a name="step-23-test-failover"></a>Stap 23: testfailover

Wacht tot het gemigreerde knoop punt is gesynchroniseerd met het on-premises altijd op het knoop punt. Plaats deze in de synchrone replicatie modus en wacht totdat deze is gesynchroniseerd. Vervolgens voert u een failover uit van on premises naar het eerste knoop punt dat is gemigreerd. Dit is de AFP. Nadat het is uitgevoerd, wijzigt u het laatste gemigreerde knoop punt naar de AFP.

U moet failovers tussen alle knoop punten testen en chaos-tests uitvoeren om ervoor te zorgen dat failovers werken zoals verwacht en in een tijdige Manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Stap 24: de instellingen van het cluster quorum herstellen/instellingen voor DNS TTL/failover Pntrs/Sync

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Een IP-adres bron op hetzelfde subnet toevoegen

Als u slechts twee SQL-servers hebt en u deze wilt migreren naar een nieuwe Cloud service, maar deze wilt blijven gebruiken op hetzelfde subnet, kunt u voor komen dat de listener offline wordt gezet om het oorspronkelijke altijd-IP-adres te verwijderen en het nieuwe IP-adres toe te voegen. Als u de virtuele machines naar een ander subnet migreert, hoeft u dit niet te doen omdat er een extra cluster netwerk is dat verwijst naar dat subnet.

Zodra u de gemigreerde secundaire en de nieuwe Cloud service hebt toegevoegd aan de nieuwe IP-adres resource voor de failover, moet u deze stappen uitvoeren binnen het cluster Failover Manager:

Als u een IP-adres wilt toevoegen, raadpleegt u de bijlage, stap 14.

1. Voor de huidige IP-adres bron wijzigt u de mogelijke eigenaar in ' bestaande primaire SQL Server ', in het voor beeld ' dansqlams4 ':

    ![Appendix13][23]
2. Wijzig voor de nieuwe IP-adres bron de mogelijke eigenaar in ' gemigreerde secundaire SQL Server ', in het voor beeld ' dansqlams5 ':

    ![Appendix14][24]
3. Zodra deze is ingesteld, kunt u een failover uitvoeren en wanneer het laatste knoop punt wordt gemigreerd, moeten de mogelijke eigen aren worden bewerkt zodat het knoop punt als mogelijke eigenaar wordt toegevoegd:

    ![Appendix15][25]

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Premium Storage](../disks-types.md)
* [Virtuele machines](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server in azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
