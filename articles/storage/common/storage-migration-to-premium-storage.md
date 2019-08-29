---
title: Vm's migreren naar Azure Premium Storage | Microsoft Docs
description: Migreer uw bestaande Vm's naar Azure Premium Storage. Premium Storage biedt schijf ondersteuning met hoge prestaties en lage latentie voor I/O-intensieve workloads die worden uitgevoerd op Azure Virtual Machines.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 90cd079ebc82e8231b052f65156f85d612592ad2
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114742"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migreren naar Azure Premium Storage (niet-beheerde schijven)

> [!NOTE]
> In dit artikel wordt beschreven hoe u een virtuele machine migreert die gebruikmaakt van niet-beheerde standaard schijven naar een virtuele machine die gebruikmaakt van niet-beheerde Premium-schijven. U wordt aangeraden Azure Managed Disks te gebruiken voor nieuwe Vm's en u uw vorige niet-beheerde schijven te converteren naar beheerde schijven. Managed Disks de onderliggende opslag accounts te verwerken zodat u dat niet hoeft te doen. Raadpleeg voor meer informatie onze Managed Disks- [overzicht](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage biedt schijf ondersteuning met hoge prestaties en lage latentie voor virtuele machines met I/O-intensieve workloads. U kunt profiteren van de snelheid en prestaties van deze schijven door de VM-schijven van uw toepassing te migreren naar Azure Premium Storage.

Het doel van deze hand leiding is om nieuwe gebruikers van Azure Premium Storage beter voor te bereiden om een soepele overgang van hun huidige systeem naar Premium Storage te maken. De gids heeft betrekking op drie van de belangrijkste onderdelen in dit proces:

* [Plannen voor de migratie naar Premium Storage](#plan-the-migration-to-premium-storage)
* [Virtuele harde schijven (Vhd's) voorbereiden en kopiëren naar Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Virtuele Azure-machine maken met behulp van Premium Storage](#create-azure-virtual-machine-using-premium-storage)

U kunt Vm's van andere platforms migreren naar Azure Premium Storage of bestaande Azure-Vm's migreren van standaard opslag naar Premium Storage. In deze hand leiding worden de stappen voor beide scenario's besproken. Volg de stappen die zijn opgegeven in de relevante sectie, afhankelijk van uw scenario.

> [!NOTE]
> U vindt een overzicht van de functies en de prijzen voor Premium Ssd's in: [Selecteer een schijf type voor IaaS-vm's](../../virtual-machines/windows/disks-types.md#premium-ssd). We raden u aan een virtuele machine schijf te migreren die hoge IOPS vereist voor Azure Premium Storage voor de beste prestaties voor uw toepassing. Als uw schijf geen hoge IOPS vereist, kunt u de kosten beperken door deze op te slaan in de standaard opslag, waarbij de schijf gegevens van de virtuele machine worden opgeslagen op vasteschijfstations (Hdd's) in plaats van Ssd's.
>

Voor het volledig volt ooien van het migratie proces zijn mogelijk extra acties vereist voor zowel vóór als na de stappen in deze hand leiding. Voor beelden zijn onder andere het configureren van virtuele netwerken of eind punten of het aanbrengen van code wijzigingen in de toepassing zelf waarvoor mogelijk enige downtime in uw toepassing nodig is. Deze acties zijn uniek voor elke toepassing en u moet deze samen met de stappen in deze hand leiding volt ooien om de volledige overgang naar Premium Storage zo naadloos mogelijk te maken.

## <a name="plan-the-migration-to-premium-storage"></a>Plannen voor de migratie naar Premium Storage
In deze sectie wordt ervoor gezorgd dat u klaar bent om de migratie stappen in dit artikel uit te voeren en kunt u de beste beslissing nemen over VM-en schijf typen.

### <a name="prerequisites"></a>Vereisten
* U hebt een Azure-abonnement nodig. Als u er nog geen hebt, kunt u een [gratis proef](https://azure.microsoft.com/pricing/free-trial/) abonnement van één maand maken of de [prijzen van Azure](https://azure.microsoft.com/pricing/) bezoeken voor meer opties.
* Als u Power shell-cmdlets wilt uitvoeren, hebt u de module Microsoft Azure PowerShell nodig. Zie [How to install and configure Azure PowerShell](/powershell/azure/overview) (Azure PowerShell installeren en configureren) voor het installatiepunt en de installatie-instructies.
* Wanneer u van plan bent om Azure-Vm's te gebruiken die worden uitgevoerd op Premium Storage, moet u de Premium Storage capable Vm's gebruiken. U kunt zowel standaard als Premium Storage schijven gebruiken met Premium Storage geschikte Vm's. Premium-opslag schijven zullen in de toekomst beschikbaar zijn met meer VM-typen. Zie [grootten voor virtuele machines](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en [grootten voor Cloud Services](../../cloud-services/cloud-services-sizes-specs.md)voor meer informatie over alle beschik bare schijf typen en grootten voor Azure VM.

### <a name="considerations"></a>Overwegingen
Een Azure-VM biedt ondersteuning voor het koppelen van verschillende Premium Storage schijven, zodat uw toepassingen Maxi maal 256 TB opslag ruimte per VM kunnen hebben. Met Premium Storage kunnen uw toepassingen 80.000 IOPS (invoer/uitvoer-bewerkingen per seconde) per VM en 2000 MB per seconde schijf doorvoer per VM met een extreem lage latentie voor lees bewerkingen. U hebt opties in verschillende Vm's en schijven. In deze sectie kunt u een optie vinden die het beste past bij uw werk belasting.

#### <a name="vm-sizes"></a>Formaten van virtuele machines
De Azure VM-grootte specificaties worden weer gegeven in [grootten voor virtuele machines](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Bekijk de prestatie kenmerken van virtuele machines die met Premium Storage werken en kies de meest geschikte VM-grootte die het beste past bij uw werk belasting. Zorg ervoor dat er voldoende band breedte beschikbaar is op uw virtuele machine om het schijf verkeer te verzorgen.

#### <a name="disk-sizes"></a>Schijfformaten
Er zijn vijf soorten schijven die kunnen worden gebruikt met uw virtuele machine, en elk heeft specifieke IOPs-en doorvoer limieten. Houd rekening met deze beperkingen bij het kiezen van het schijf type voor uw virtuele machine op basis van de behoeften van uw toepassing in termen van capaciteit, prestaties, schaal baarheid en piek belasting.

| Type Premium-schijven  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Schijfgrootte           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOP's per schijf       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Doorvoer per schijf | 100 MB per seconde | 150 MB per seconde | 200 MB per seconde | 250 MB per seconde | 250 MB per seconde |

Bepaal, afhankelijk van uw workload, of er extra gegevens schijven voor uw virtuele machine nodig zijn. U kunt verschillende permanente gegevens schijven aan uw virtuele machine koppelen. U kunt, indien nodig, over de schijven strepen om de capaciteit en prestaties van het volume te verg Roten. (Zie wat is schijf striping [](../../virtual-machines/windows/premium-storage-performance.md#disk-striping)?) Als u Premium Storage gegevens schijven met [opslag ruimten][4]wilt verwijderen, moet u deze configureren met één kolom voor elke gebruikte schijf. Anders is het mogelijk dat de algehele prestaties van het striped volume lager zijn dan verwacht als gevolg van een ongelijke distributie van verkeer over de schijven. Voor virtuele Linux-machines kunt u het *mdadm* -hulp programma gebruiken om hetzelfde te krijgen. Zie het artikel [Software RAID op Linux configureren](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor meer informatie.

#### <a name="storage-account-scalability-targets"></a>Schaalbaarheids doelen voor het opslag account
Premium Storage accounts hebben naast de [Azure Storage schaal baarheid en prestatie doelen](storage-scalability-targets.md)de volgende schaalbaarheids doelen. Als uw toepassings vereisten de schaalbaarheids doelen van één opslag account overschrijden, bouwt u uw toepassing voor het gebruik van meerdere opslag accounts en partitioneert u uw gegevens in deze opslag accounts.

| Totale account capaciteit | Totale band breedte voor een lokaal redundante opslag account |
|:--- |:--- |
| Schijf capaciteit: 35TB<br />Momentopname capaciteit: 10 TB |Maxi maal 50 gigabits per seconde voor inkomend en uitgaand verkeer |

Raadpleeg [Azure Storage schaalbaarheids-en prestatie doelen](storage-scalability-targets.md#premium-performance-storage-account-scale-limits)voor meer informatie over Premium Storage specificaties.

#### <a name="disk-caching-policy"></a>Beleid voor schijf cache
Het cache beleid voor schijven is standaard *alleen-lezen* voor alle Premium-gegevens schijven en *lezen-schrijven* voor de Premium-besturingssysteem schijf die is gekoppeld aan de virtuele machine. Deze configuratie-instelling wordt aanbevolen om de optimale prestaties van de IOs van uw toepassing te garanderen. Voor schrijf bare of alleen-schrijven gegevens schijven (zoals SQL Server-logboek bestanden) schakelt u schijf cache, zodat u betere toepassings prestaties kunt krijgen. De cache-instellingen voor bestaande gegevens schijven kunnen worden bijgewerkt met behulp van de [Azure Portal](https://portal.azure.com) of de para meter *-HostCaching* van de cmdlet *set-AzureDataDisk* .

#### <a name="location"></a>Location
Kies een locatie waar Azure Premium Storage beschikbaar is. Zie [Azure-Services per regio](https://azure.microsoft.com/regions/#services) voor actuele informatie over beschik bare locaties. Vm's die zich in dezelfde regio bevinden als het opslag account waarin de schijven voor de virtuele machine zijn opgeslagen, bieden veel betere prestaties dan wanneer ze zich in verschillende regio's bevinden.

#### <a name="other-azure-vm-configuration-settings"></a>Andere configuratie-instellingen voor Azure VM
Wanneer u een virtuele Azure-machine maakt, wordt u gevraagd om bepaalde VM-instellingen te configureren. Houd er rekening mee dat weinig instellingen worden vastgesteld voor de levens duur van de virtuele machine, terwijl u later andere kunt wijzigen of toevoegen. Bekijk deze Azure VM-configuratie-instellingen en zorg ervoor dat deze zijn geconfigureerd om te voldoen aan de vereisten van uw werk belasting.

### <a name="optimization"></a>Optimalisatie
[Azure Premium Storage: Ontwerp voor hoge prestaties](../../virtual-machines/windows/premium-storage-performance.md) biedt richt lijnen voor het bouwen van toepassingen met hoge prestaties met behulp van Azure Premium Storage. U kunt de richt lijnen volgen in combi natie met best practices voor prestaties die van toepassing zijn op technologieën die door uw toepassing worden gebruikt.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Virtuele harde schijven (Vhd's) voorbereiden en kopiëren naar Premium Storage
De volgende sectie bevat richt lijnen voor het voorbereiden van Vhd's van uw virtuele machine en het kopiëren van Vhd's naar Azure Storage.

* [Scenario 1: "Ik ben bestaande Azure-Vm's migreren naar Azure Premium Storage."](#scenario1)
* [Scenario 2: "Ik migreer Vm's van andere platforms naar Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Vereisten
Als u de virtuele harde schijven voor migratie wilt voorbereiden, hebt u het volgende nodig:

* Een Azure-abonnement, een opslag account en een container in dat opslag account waarnaar u uw VHD kunt kopiëren. Houd er rekening mee dat het doel-opslag account een standaard-of Premium Storage account kan zijn, afhankelijk van uw vereiste.
* Een hulp programma voor het generaliseren van de VHD als u van plan bent om meerdere VM-exemplaren hiervan te maken. Bijvoorbeeld Sysprep voor Windows of virt-Sysprep voor Ubuntu.
* Een hulp programma voor het uploaden van het VHD-bestand naar het opslag account. Zie [gegevens overdragen met het opdracht regel programma AzCopy](storage-use-azcopy.md) of een [Azure Storage Explorer](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)gebruiken. In deze hand leiding wordt beschreven hoe u uw VHD kopieert met het hulp programma AzCopy.

> [!NOTE]
> Als u de optie synchroon kopiëren kiest met AzCopy, moet u voor optimale prestaties uw VHD kopiëren door een van deze hulpprogram ma's uit te voeren vanaf een Azure-VM die zich in dezelfde regio bevindt als het opslag account van de bestemming. Als u een VHD kopieert van een Azure-VM in een andere regio, kunnen de prestaties langzamer zijn.
>
> Overweeg [het gebruik van de Azure import/export-service om gegevens over te brengen naar Blob Storage](../storage-import-export-service.md), voor het kopiëren van een grote hoeveelheid gegevens over een beperkte band breedte. Zo kunt u uw gegevens overdragen door harde schijven naar een Azure-Data Center te verzenden. U kunt de Azure import/export-service alleen gebruiken om gegevens te kopiëren naar een standaard-opslag account. Zodra de gegevens zich in uw standaard opslag account bevindt, kunt u de [copy BLOB API](https://msdn.microsoft.com/library/azure/dd894037.aspx) of AzCopy gebruiken om de gegevens over te dragen naar uw Premium Storage-account.
>
> Houd er rekening mee dat Microsoft Azure alleen ondersteuning biedt voor VHD-bestanden met een vaste grootte. VHDX-bestanden of dynamische Vhd's worden niet ondersteund. Als u een dynamische VHD hebt, kunt u deze converteren naar een vaste grootte met de cmdlet [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="scenario1"></a>Scenario 1: "Ik ben bestaande Azure-Vm's migreren naar Azure Premium Storage."
Als u bestaande Azure-Vm's migreert, stopt u de virtuele machine, bereidt u Vhd's voor op basis van het gewenste type VHD en kopieert u de VHD met AzCopy of Power shell.

De virtuele machine moet volledig worden uitgeschakeld om een schone status te migreren. Er is een uitval tijd tot de migratie is voltooid.

#### <a name="step-1-prepare-vhds-for-migration"></a>Stap 1. Vhd's voorbereiden voor migratie
Als u bestaande Azure-Vm's migreert naar Premium Storage, kan uw VHD het volgende zijn:

* Een gegeneraliseerde installatie kopie van het besturings systeem
* Een unieke besturingssysteem schijf
* Een gegevens schijf

Hieronder volgen de drie scenario's voor het voorbereiden van uw VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Een gegeneraliseerde VHD van het besturings systeem gebruiken om meerdere VM-exemplaren te maken
Als u een VHD uploadt die wordt gebruikt om meerdere algemene Azure VM-exemplaren te maken, moet u eerst de VHD generaliseren met een hulp programma Sysprep. Dit is van toepassing op een VHD die zich lokaal of in de Cloud bevindt. Sysprep verwijdert specifieke informatie van de virtuele machine.

> [!IMPORTANT]
> Neem een moment opname of maak een back-up van uw virtuele machine voordat u deze generaliseert. Als Sysprep wordt uitgevoerd, wordt het VM-exemplaar gestopt en de toewijzing ongedaan maken. Volg de onderstaande stappen om een VHD van het Windows-besturings systeem te Sysprep. Houd er rekening mee dat de virtuele machine moet worden afgesloten als u de Sysprep-opdracht uitvoert. Zie voor meer informatie over Sysprep [overzicht van Sysprep](https://technet.microsoft.com/library/hh825209.aspx) of [technische documentatie over Sysprep](https://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Open een opdracht prompt venster als beheerder.
2. Voer de volgende opdracht in om Sysprep te openen:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. In het hulp programma voor systeem voorbereiding selecteert u System out-of-Box Experience (OOBE) invoeren, schakelt u het selectie vakje generalize in, selecteert u **Afsluiten**en klikt u op **OK**, zoals in de onderstaande afbeelding wordt weer gegeven. Sysprep generaliseert het besturings systeem en wordt het systeem afgesloten.

    ![][1]

Voor een Ubuntu-VM gebruikt u virt-Sysprep om hetzelfde te krijgen. Zie [virt-Sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) voor meer informatie. Zie ook enkele van de open source [Linux server](https://www.cyberciti.biz/tips/server-provisioning-software.html) -inrichtings software voor andere Linux-besturings systemen.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Een unieke VHD van het besturings systeem gebruiken om één VM-exemplaar te maken
Als u een toepassing hebt die wordt uitgevoerd op de virtuele machine waarvoor de computer gegevens moeten worden opgegeven, moet u de VHD niet generaliseren. Een niet-gegeneraliseerde VHD kan worden gebruikt om een unieke Azure VM-instantie te maken. Als u bijvoorbeeld domein controller op uw VHD hebt, wordt het uitvoeren van Sysprep als een domein controller. Bekijk de toepassingen die op uw virtuele machine worden uitgevoerd en de gevolgen van het uitvoeren van Sysprep op deze apps voordat u de VHD generaliseert.

##### <a name="register-data-disk-vhd"></a>VHD met gegevens schijf registreren
Als er gegevens schijven in Azure worden gemigreerd, moet u controleren of de virtuele machines die gebruikmaken van deze gegevens schijven, worden afgesloten.

Volg de stappen die hieronder worden beschreven om VHD naar Azure Premium Storage te kopiëren en te registreren als een ingerichte gegevens schijf.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Stap 2. De bestemming voor uw VHD maken
Maak een opslag account voor het onderhouden van uw Vhd's. Houd rekening met de volgende punten bij het plannen waar u de Vhd's opslaat:

* Het doel Premium-opslag account.
* De locatie van het opslag account moet hetzelfde zijn als Premium Storage geschikte Azure-Vm's die u in de laatste fase gaat maken. U kunt kopiëren naar een nieuw opslag account, of u kunt hetzelfde opslag account gebruiken op basis van uw behoeften.
* Kopieer en sla de sleutel van het opslag account van het doel opslag account voor de volgende fase op.

Voor gegevens schijven kunt u ervoor kiezen om sommige gegevens schijven te bewaren in een standaard-opslag account (bijvoorbeeld schijven met een koele opslag), maar we raden u ten zeerste aan alle gegevens voor de werk belasting van de productie te verplaatsen om Premium-opslag te gebruiken.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Stap 3. VHD kopiëren met AzCopy of Power shell
U moet het pad naar de container en de sleutel van het opslag account zoeken om een van deze twee opties te kunnen verwerken. De container-pad en de sleutel voor het opslag account vindt u in **Azure Portal** > -**opslag**. De container-URL is als ' https:\//myaccount.blob.core.Windows.net/mycontainer/'.

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Optie 1: Een VHD kopiëren met AzCopy (asynchroon kopiëren)
Met AzCopy kunt u eenvoudig de VHD uploaden via internet. Afhankelijk van de grootte van de Vhd's kan dit enige tijd duren. Controleer de limieten voor het inkomen/uitvallen van opslag accounts wanneer u deze optie gebruikt. Zie [Azure Storage schaal baarheid en prestatie doelen](storage-scalability-targets.md) voor meer informatie.

1. Down load en installeer AzCopy hier: [Meest recente versie van AzCopy](https://aka.ms/downloadazcopy)
2. Open Azure PowerShell en ga naar de map waarin AzCopy is geïnstalleerd.
3. Gebruik de volgende opdracht om het VHD-bestand van ' source ' naar ' Destination ' te kopiëren.

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Voorbeeld:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

   Hier volgen beschrijvingen van de para meters die worden gebruikt in de AzCopy-opdracht:

   * **/Source:** _bron:&gt; &lt;_ Locatie van de map of de opslag container-URL die de VHD bevat.
   * **/SourceKey:** _bron-account-sleutel&gt;: &lt;_ Sleutel van het opslag account van het bron-opslag account.
   * **/Dest:** _doel:&gt; &lt;_ URL van de opslag container waarnaar de VHD moet worden gekopieerd.
   * **/DestKey:** _doel account-sleutel&gt;: &lt;_ Sleutel van het opslag account van het doel-opslag account.
   * **/Pattern:** _Bestands naam:&gt; &lt;_ Geef de bestands naam op van de VHD die u wilt kopiëren.

Zie [gegevens overdragen met het opdracht regel programma AzCopy](storage-use-azcopy.md)voor meer informatie over het gebruik van het hulp programma AzCopy.

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Optie 2: Een VHD kopiëren met Power shell (gesynchroniseerde kopie)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt het VHD-bestand ook kopiëren met behulp van de Power shell-cmdlet start-AzStorageBlobCopy. Gebruik de volgende opdracht op Azure PowerShell om VHD te kopiëren. Vervang de waarden in < > door de bijbehorende waarden uit uw bron-en doel-opslag account. Als u deze opdracht wilt gebruiken, moet u een container met de naam vhd's in het doel-opslag account hebben. Als de container niet bestaat, maakt u er een voordat u de opdracht uitvoert.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Voorbeeld:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scenario 2: "Ik migreer Vm's van andere platforms naar Azure Premium Storage."
Als u VHD migreert van niet-Azure-Cloud opslag naar Azure, moet u eerst de VHD exporteren naar een lokale map. Hebben het volledige bronpad van de lokale map waar VHD op een handige manier wordt opgeslagen en vervolgens AzCopy om het te uploaden naar Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Stap 1. VHD exporteren naar een lokale map
##### <a name="copy-a-vhd-from-aws"></a>Een VHD kopiëren vanuit AWS
1. Als u AWS gebruikt, exporteert u het EC2-exemplaar naar een VHD in een Amazon S3-Bucket. Volg de stappen die worden beschreven in de Amazon-documentatie voor het exporteren van Amazon EC2 instances voor het installeren van het hulp programma Amazon EC2 Command-Line Interface (CLI) en voer de opdracht Create-instance-export-Task uit om het EC2-exemplaar te exporteren naar een VHD-bestand. Zorg ervoor dat u **VHD** gebruikt voor de&#95;variabele&#95;indeling van schijf kopie bij het uitvoeren van de opdracht **Create-instance-export-Task** . Het geëxporteerde VHD-bestand wordt opgeslagen in de Amazon S3-Bucket die u tijdens dat proces opgeeft.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Down load het VHD-bestand van de S3-Bucket. Selecteer het VHD-bestand en vervolgens de **acties** > **downloaden**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Een VHD kopiëren vanuit een andere niet-Azure-Cloud
Als u VHD migreert van niet-Azure-Cloud opslag naar Azure, moet u eerst de VHD exporteren naar een lokale map. Kopieer het volledige bronpad van de lokale map waarin VHD wordt opgeslagen.

##### <a name="copy-a-vhd-from-on-premises"></a>Een VHD kopiëren van on-premises
Als u VHD migreert vanuit een on-premises omgeving, hebt u het volledige bronpad nodig waar VHD wordt opgeslagen. Het bronpad kan een server locatie of bestands share zijn.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Stap 2. De bestemming voor uw VHD maken
Maak een opslag account voor het onderhouden van uw Vhd's. Houd rekening met de volgende punten bij het plannen waar u de Vhd's opslaat:

* Het doel-opslag account kan een standaard-of Premium-opslag zijn, afhankelijk van de vereiste van uw toepassing.
* De regio van het opslag account moet hetzelfde zijn als Premium Storage geschikte Azure-Vm's die u in de laatste fase gaat maken. U kunt kopiëren naar een nieuw opslag account, of u kunt hetzelfde opslag account gebruiken op basis van uw behoeften.
* Kopieer en sla de sleutel van het opslag account van het doel opslag account voor de volgende fase op.

We raden u ten zeerste aan alle gegevens voor de werk belasting van de productie te verplaatsen om Premium-opslag te gebruiken.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Stap 3. De VHD uploaden naar Azure Storage
Nu u uw VHD in de lokale map hebt, kunt u AzCopy of AzurePowerShell gebruiken om het. VHD-bestand te uploaden naar Azure Storage. Beide opties worden hier weer gegeven:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Optie 1: Het. VHD-bestand uploaden met Azure PowerShell add-AzureVhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Een voor \<beeld van een URI > is mogelijk **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"_** . Een voor \<beeld van een file info > is **_' C:\path\to\upload.VHD '_** .

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Optie 2: Het. VHD-bestand uploaden met behulp van AzCopy
Met AzCopy kunt u eenvoudig de VHD uploaden via internet. Afhankelijk van de grootte van de Vhd's kan dit enige tijd duren. Controleer de limieten voor het inkomen/uitvallen van opslag accounts wanneer u deze optie gebruikt. Zie [Azure Storage schaal baarheid en prestatie doelen](storage-scalability-targets.md) voor meer informatie.

1. Down load en installeer AzCopy hier: [Meest recente versie van AzCopy](https://aka.ms/downloadazcopy)
2. Open Azure PowerShell en ga naar de map waarin AzCopy is geïnstalleerd.
3. Gebruik de volgende opdracht om het VHD-bestand van ' source ' naar ' Destination ' te kopiëren.

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Voorbeeld:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

   Hier volgen beschrijvingen van de para meters die worden gebruikt in de AzCopy-opdracht:

   * **/Source:** _bron:&gt; &lt;_ Locatie van de map of de opslag container-URL die de VHD bevat.
   * **/SourceKey:** _bron-account-sleutel&gt;: &lt;_ Sleutel van het opslag account van het bron-opslag account.
   * **/Dest:** _doel:&gt; &lt;_ URL van de opslag container waarnaar de VHD moet worden gekopieerd.
   * **/DestKey:** _doel account-sleutel&gt;: &lt;_ Sleutel van het opslag account van het doel-opslag account.
   * **/BlobType: pagina:** Hiermee geeft u op dat de bestemming een pagina-blob is.
   * **/Pattern:** _Bestands naam:&gt; &lt;_ Geef de bestands naam op van de VHD die u wilt kopiëren.

Zie [gegevens overdragen met het opdracht regel programma AzCopy](storage-use-azcopy.md)voor meer informatie over het gebruik van het hulp programma AzCopy.

##### <a name="other-options-for-uploading-a-vhd"></a>Andere opties voor het uploaden van een VHD
U kunt ook een VHD uploaden naar uw opslag account met behulp van een van de volgende manieren:

* [BLOB-API Azure Storage kopiëren](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Azure Storage Explorer uploaden van blobs](https://azurestorageexplorer.codeplex.com/)
* [Naslag informatie voor Storage import/export-service REST API](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> U kunt het beste de import/export-service gebruiken als de geschatte upload tijd langer is dan 7 dagen. U kunt [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) gebruiken om de tijd te schatten van de gegevens grootte en de overdrachts eenheid.
>
> Importeren/exporteren kan worden gebruikt om naar een Standard-opslag account te kopiëren. U moet de standaard opslag kopiëren naar een Premium Storage-account met een hulp programma zoals AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Virtuele Azure-machines maken met behulp van Premium Storage
Nadat de VHD is geüpload of gekopieerd naar het gewenste opslag account, volgt u de instructies in deze sectie om de VHD als een installatie kopie van het besturings systeem te registreren, of de besturingssysteem schijf, afhankelijk van uw scenario en vervolgens een VM-exemplaar te maken. De VHD met de gegevens schijf kan worden gekoppeld aan de virtuele machine nadat deze is gemaakt.
Aan het einde van deze sectie vindt u een voor beeld van een migratie script. Dit eenvoudige script komt niet overeen met alle scenario's. Mogelijk moet u het script bijwerken zodat dit overeenkomt met uw specifieke scenario. Zie onder [een voor beeld van een migratie script](#a-sample-migration-script)om te zien of dit script van toepassing is op uw scenario.

### <a name="checklist"></a>Controlelijst
1. Wacht totdat alle VHD-schijven zijn gekopieerd.
2. Zorg ervoor dat Premium Storage beschikbaar is in de regio waar u naar migreert.
3. Bepaal welke nieuwe VM-serie u wilt gebruiken. Het moet een Premium Storage mogelijk zijn en de grootte moet afhankelijk zijn van de beschik baarheid in de regio en op basis van uw behoeften.
4. Bepaal de exacte VM-grootte die u wilt gebruiken. De VM-grootte moet groot genoeg zijn voor het ondersteunen van het aantal gegevens schijven dat u hebt. Bijvoorbeeld Als u 4 gegevens schijven hebt, moet de virtuele machine twee of meer kern geheugens hebben. Denk ook aan het verwerken van energie, geheugen en netwerk bandbreedte behoeften.
5. Maak een Premium Storage-account in de doel regio. Dit is het account dat u wilt gebruiken voor de nieuwe virtuele machine.
6. Laat de huidige VM-Details weten, met inbegrip van de lijst met schijven en de bijbehorende VHD-blobs.

Bereid uw toepassing voor op uitval tijd. Als u een schone migratie wilt uitvoeren, moet u alle bewerkingen in het huidige systeem stoppen. U kunt deze alleen weer geven in een consistente status die u kunt migreren naar het nieuwe platform. De duur van downtime is afhankelijk van de hoeveelheid gegevens op de schijven die moeten worden gemigreerd.

> [!NOTE]
> Als u een Azure Resource Manager VM maakt op basis van een speciale VHD-schijf, raadpleegt u [deze sjabloon voor het](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) implementeren van een resource manager-VM met behulp van een bestaande schijf.
>
>

### <a name="register-your-vhd"></a>Uw VHD registreren
Als u een virtuele machine wilt maken op basis van de besturingssysteem-VHD of als u een gegevens schijf aan een nieuwe virtuele machine wilt koppelen, moet u deze eerst registreren. Volg de onderstaande stappen, afhankelijk van het scenario van uw VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Algemene VHD van het besturings systeem voor het maken van meerdere Azure VM-exemplaren
Nadat de VHD met de gegeneraliseerde installatie kopie van het besturings systeem is geüpload naar het opslag account, moet u deze registreren als een **Azure VM-installatie kopie** , zodat u een of meer VM-exemplaren hiervan kunt maken. Gebruik de volgende Power shell-cmdlets om uw VHD te registreren als een installatie kopie van een Azure VM-besturings systeem. Geef de volledige container-URL op waarnaar VHD is gekopieerd.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Kopieer de naam van deze nieuwe Azure VM-installatie kopie en sla deze op. In het bovenstaande voor beeld is dit *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unieke VHD van het besturings systeem om één Azure VM-exemplaar te maken
Nadat de unieke VHD van het besturings systeem is geüpload naar het opslag account, registreert u deze als een **Azure-besturingssysteem schijf** , zodat u er een VM-instantie van kunt maken. Gebruik deze Power shell-cmdlets om uw VHD te registreren als een Azure-besturingssysteem schijf. Geef de volledige container-URL op waarnaar VHD is gekopieerd.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Kopieer de naam van deze nieuwe Azure-besturingssysteem schijf en sla deze op. In het bovenstaande voor beeld is dit *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>VHD met gegevens schijf die moet worden gekoppeld aan nieuwe Azure VM-exemplaren
Nadat de VHD met de gegevens schijf is geüpload naar het opslag account, registreert u deze als een Azure-gegevens schijf, zodat deze kan worden gekoppeld aan uw nieuwe DS-serie, DSv2-Series of GS-serie Azure VM-instantie.

Gebruik deze Power shell-cmdlets om uw VHD te registreren als een Azure-gegevens schijf. Geef de volledige container-URL op waarnaar VHD is gekopieerd.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Kopieer de naam van deze nieuwe Azure-gegevens schijf en sla deze op. In het bovenstaande voor beeld is dit *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Een virtuele machine maken die geschikt is voor Premium Storage
Nadat de installatie kopie van het besturings systeem of de besturingssysteem schijf is geregistreerd, maakt u een nieuwe virtuele machine uit de DS-serie, DSv2-serie of GS-serie. U gebruikt de installatie kopie van het besturings systeem of de naam van de besturingssysteem schijf die u hebt geregistreerd. Selecteer het VM-type in de laag Premium Storage. In het onderstaande voor beeld gebruiken we de *Standard_DS2* -VM-grootte.

> [!NOTE]
> Werk de schijf grootte bij om ervoor te zorgen dat deze overeenkomt met de capaciteits-en prestatie vereisten en de beschik bare groottes van Azure-schijven.
>
>

Volg de onderstaande stapsgewijze Power shell-cmdlets om de nieuwe virtuele machine te maken. Stel eerst de algemene para meters in:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Maak eerst een Cloud service waarin u uw nieuwe Vm's gaat hosten.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Afhankelijk van uw scenario maakt u de Azure VM-instantie van de installatie kopie van het besturings systeem of de besturingssysteem schijf die u hebt geregistreerd.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Algemene VHD van het besturings systeem voor het maken van meerdere Azure VM-exemplaren
Maak een of meer nieuwe Azure VM-exemplaren van de DS-serie met behulp van de **installatie kopie van het Azure-besturings systeem** die u hebt geregistreerd. Geef een naam op voor de installatie kopie van het besturings systeem in de VM-configuratie bij het maken van een nieuwe virtuele machine, zoals hieronder

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Unieke VHD van het besturings systeem om één Azure VM-exemplaar te maken
Maak een nieuwe Azure-VM-instantie van de DS-serie met behulp van de **Azure-besturingssysteem schijf** die u hebt geregistreerd. Geef de naam op van deze besturingssysteem schijf in de VM-configuratie bij het maken van de nieuwe VM, zoals hieronder wordt weer gegeven.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Geef andere Azure-VM-informatie op, zoals een Cloud service, regio, opslag account, beschikbaarheidsset en beleid voor caching. Het VM-exemplaar moet zich op dezelfde locatie bevinden als de onderliggende Vhd's van die schijven, zodat de geselecteerde Cloud service, de regio en het opslag account op dezelfde plaats moeten staan.

### <a name="attach-data-disk"></a>Een gegevensschijf koppelen
Ten slotte, als u virtuele harde schijven hebt geregistreerd, koppelt u deze aan de nieuwe Premium Storage geschikte Azure VM.

Gebruik de volgende Power shell-cmdlet om de gegevens schijf aan de nieuwe virtuele machine te koppelen en het cache beleid op te geven. In het onderstaande voor beeld is het beleid voor cachen ingesteld op *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Er zijn mogelijk extra stappen nodig om uw toepassing te ondersteunen die niet onder deze hand leiding valt.
>
>

### <a name="checking-and-plan-backup"></a>Back-ups controleren en plannen
Zodra de nieuwe VM actief is, opent u deze met dezelfde aanmeldings-id en hetzelfde wacht woord als de oorspronkelijke virtuele machine en controleert u of alles werkt zoals verwacht. Alle instellingen, met inbegrip van de striped volumes, worden weer gegeven in de nieuwe VM.

De laatste stap bestaat uit het plannen van de back-up-en onderhouds planning voor de nieuwe virtuele machine op basis van de behoeften van de toepassing.

### <a name="a-sample-migration-script"></a>Een voor beeld van een migratie script
Als u meerdere Vm's wilt migreren, is automatisering via Power shell-scripts handig. Hieronder volgt een voor beeld van een script waarmee de migratie van een virtuele machine wordt geautomatiseerd. Houd er rekening mee dat het onderstaande script slechts een voor beeld is en dat er enkele hypo Thesen worden gemaakt voor de huidige VM-schijven. Mogelijk moet u het script bijwerken zodat dit overeenkomt met uw specifieke scenario.

De veronderstellingen zijn:

* U maakt klassieke Azure-Vm's.
* De schijven van het bron besturingssysteem en de bron gegevens schijven bevinden zich in hetzelfde opslag account en dezelfde container. Als de schijven en gegevens schijven van uw besturings systeem zich niet op dezelfde locatie bevinden, kunt u AzCopy of Azure PowerShell gebruiken om Vhd's te kopiëren via opslag accounts en containers. Raadpleeg de vorige stap: [Kopieer de VHD met AzCopy of Power shell](#copy-vhd-with-azcopy-or-powershell). Het bewerken van dit script om te voldoen aan uw scenario is een andere keuze, maar we raden u aan om AzCopy of Power shell te gebruiken, omdat dit eenvoudiger en sneller is.

Het Automation-script wordt hieronder gegeven. Vervang tekst door uw gegevens en werk het script bij zodat dit overeenkomt met uw specifieke scenario.

> [!NOTE]
> Als u het bestaande script gebruikt, wordt de netwerk configuratie van de bron-VM niet bewaard. U moet de netwerk instellingen op uw gemigreerde Vm's opnieuw configureren.
>
>

```powershell
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    https://azure.microsoft.com/documentation/articles/powershell-install-configure/
    https://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in seconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module version" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article https://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the source vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting until the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more customization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Gedaan
Uw huidige VM-configuratie kan specifiek worden aangepast om goed te kunnen werken met standaard schijven. Bijvoorbeeld om de prestaties te verbeteren door gebruik te maken van veel schijven in een striped volume. In plaats van 4-schijven afzonderlijk op Premium Storage te gebruiken, kunt u de kosten mogelijk met één schijf optimaliseren. Optimalisaties zoals deze moeten per geval worden afgehandeld en vereisen aangepaste stappen na de migratie. Houd er ook rekening mee dat dit proces mogelijk niet goed werkt voor data bases en toepassingen die afhankelijk zijn van de schijf indeling die is gedefinieerd in de installatie.

##### <a name="preparation"></a>Voorbereiding
1. Voltooi de eenvoudige migratie zoals beschreven in de vorige sectie. Optimalisaties worden uitgevoerd op de nieuwe virtuele machine na de migratie.
2. Definieer de nieuwe schijf grootten die nodig zijn voor de geoptimaliseerde configuratie.
3. Bepaal de toewijzing van de huidige schijven/volumes aan de nieuwe schijf specificaties.

##### <a name="execution-steps"></a>Uitvoerings stappen
1. Maak de nieuwe schijven met de juiste grootte op de Premium Storage VM.
2. Meld u aan bij de virtuele machine en kopieer de gegevens van het huidige volume naar de nieuwe schijf die is gekoppeld aan dat volume. Doe dit voor alle huidige volumes die moeten worden toegewezen aan een nieuwe schijf.
3. Wijzig vervolgens de toepassings instellingen om over te scha kelen naar de nieuwe schijven en ontkoppel de oude volumes.

Raadpleeg de sectie optimalisatie van toepassings prestaties van het artikel [ontwerpen voor hoge prestaties](../../virtual-machines/windows/premium-storage-performance.md) voor het afstemmen van de toepassing voor betere prestaties van de schijf.

### <a name="application-migrations"></a>Toepassings migraties
Data bases en andere complexe toepassingen kunnen speciale stappen vereisen zoals gedefinieerd door de toepassings provider voor de migratie. Raadpleeg de documentatie van de betreffende toepassing. Bijvoorbeeld normaal gesp roken kunnen data bases worden gemigreerd met back-up en herstel.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende bronnen voor specifieke scenario's voor het migreren van virtuele machines:

* [Azure Virtual Machines migreren tussen opslag accounts](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Een Windows Server-VHD maken en uploaden naar Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Een Linux-VHD maken en uploaden naar Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Virtual Machines migreren van Amazon AWS naar Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Raadpleeg ook de volgende bronnen voor meer informatie over Azure Storage en Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Selecteer een schijf type voor IaaS Vm's](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
