---
title: VM's migreren naar Azure Premium Storage | Microsoft Documenten
description: Migreer uw bestaande VM's naar Azure Premium Storage. Premium Storage biedt krachtige schijfondersteuning met lage latentie voor I/O-intensieve workloads die worden uitgevoerd op Azure Virtual Machines.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.reviewer: yuemlu
ms.subservice: common
ms.openlocfilehash: 7cb5a335af7093bc217578d57340b03b8b9c08b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748344"
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migreren naar Azure Premium Storage (onbeheerde schijven)

> [!NOTE]
> In dit artikel wordt besproken hoe u een VM migreert die onbeheerde standaardschijven gebruikt naar een vm die onbeheerde premiumschijven gebruikt. We raden u aan Azure Managed Disks te gebruiken voor nieuwe VM's en uw vorige niet-beheerde schijven om te zetten naar beheerde schijven. Beheerde schijven verwerken de onderliggende opslagaccounts, zodat u dat niet hoeft te doen. Zie voor meer informatie ons [Overzicht beheerde schijven.](../../virtual-machines/windows/managed-disks-overview.md)
>

Azure Premium Storage biedt krachtige schijfondersteuning met lage latentie voor virtuele machines met I/O-intensieve workloads. U profiteren van de snelheid en prestaties van deze schijven door de VM-schijven van uw toepassing te migreren naar Azure Premium Storage.

Het doel van deze handleiding is om nieuwe gebruikers van Azure Premium Storage te helpen zich beter voor te bereiden op een soepele overgang van hun huidige systeem naar Premium Storage. De gids behandelt drie van de belangrijkste onderdelen in dit proces:

* [Plannen voor de migratie naar premium opslag](#plan-the-migration-to-premium-storage)
* [Virtuele harde schijven (VHD's) voorbereiden en kopiëren naar Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Azure Virtual Machine maken met Premium Storage](#create-azure-virtual-machine-using-premium-storage)

U VM's migreren van andere platforms naar Azure Premium Storage of bestaande Azure VM's migreren van standaardopslag naar Premium-opslag. Deze handleiding bevat stappen voor beide scenario's. Volg de stappen die in de desbetreffende sectie zijn opgegeven, afhankelijk van uw scenario.

> [!NOTE]
> U vindt een functieoverzicht en prijzen van premium SSD's in: [Selecteer een schijftype voor IaaS VM's.](../../virtual-machines/windows/disks-types.md#premium-ssd) We raden u aan een virtuele machineschijf te migreren waarvoor een hoge IOPS nodig is naar Azure Premium Storage voor de beste prestaties voor uw toepassing. Als uw schijf geen hoge IOPS vereist, u de kosten beperken door deze te onderhouden in Standard Storage, dat schijfgegevens van virtuele machines op harde schijven (HDD's) opslaat in plaats van SSD's.
>

Het voltooien van het migratieproces in zijn geheel kan aanvullende acties vereisen, zowel voor als na de stappen in deze handleiding. Voorbeelden hiervan zijn het configureren van virtuele netwerken of eindpunten of het aanbrengen van codewijzigingen binnen de toepassing zelf, wat enige downtime in uw toepassing kan vereisen. Deze acties zijn uniek voor elke toepassing en u moet ze voltooien samen met de stappen in deze handleiding om de volledige overgang naar Premium Storage zo naadloos mogelijk te maken.

## <a name="plan-for-the-migration-to-premium-storage"></a><a name="plan-the-migration-to-premium-storage"></a>Plannen voor de migratie naar Premium Storage
In deze sectie u ervoor zorgen dat u klaar bent om de migratiestappen in dit artikel te volgen en u de beste beslissing nemen over VM- en schijftypen.

### <a name="prerequisites"></a>Vereisten
* U hebt een Azure-abonnement nodig. Als u er geen hebt, u een [gratis proefabonnement van](https://azure.microsoft.com/pricing/free-trial/) één maand maken of Azure [Pricing](https://azure.microsoft.com/pricing/) bezoeken voor meer opties.
* Als u PowerShell-cmdlets wilt uitvoeren, hebt u de Microsoft Azure PowerShell-module nodig. Zie [How to install and configure Azure PowerShell](/powershell/azure/overview) (Azure PowerShell installeren en configureren) voor het installatiepunt en de installatie-instructies.
* Wanneer u Azure VM's wilt gebruiken die op Premium Storage worden uitgevoerd, moet u de VM's met premiumopslag gebruiken. U zowel standaard- als Premium-opslagschijven gebruiken met VM's met standaardopslag met standaardopslag. Premium-opslagschijven zullen in de toekomst beschikbaar zijn met meer VM-typen. Zie [Groottes voor virtuele machines](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en formaten [voor cloudservices voor](../../cloud-services/cloud-services-sizes-specs.md)meer informatie over alle beschikbare Azure VM-schijftypen en -formaten.

### <a name="considerations"></a>Overwegingen
Een Azure VM ondersteunt het koppelen van meerdere Premium Storage-schijven, zodat uw toepassingen maximaal 256 TB opslagruimte per vm kunnen hebben. Met Premium Storage kunnen uw toepassingen 80.000 IOPS (input/output operations per seconde) per VM en 2000 MB per seconde schijfdoorvoer per VM bereiken met extreem lage latencies voor leesbewerkingen. U hebt opties in verschillende VM's en schijven. Deze sectie is om u te helpen een optie te vinden die het beste bij uw werkbelasting past.

#### <a name="vm-sizes"></a>Formaten van virtuele machines
De azure VM-groottespecificaties worden vermeld in [Grootte voor virtuele machines.](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Bekijk de prestatiekenmerken van virtuele machines die werken met Premium Storage en kies de meest geschikte VM-grootte die het beste bij uw werkbelasting past. Zorg ervoor dat er voldoende bandbreedte beschikbaar is op uw VM om het schijfverkeer te stimuleren.

#### <a name="disk-sizes"></a>Schijfformaten
Er zijn vijf soorten schijven die kunnen worden gebruikt met uw VM en elk heeft specifieke IPP's en doorvoerlimieten. Houd rekening met deze limieten bij het kiezen van het schijftype voor uw vm op basis van de behoeften van uw toepassing op het gebied van capaciteit, prestaties, schaalbaarheid en piekbelastingen.

| Type Premium-schijven  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Schijfgrootte           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOP's per schijf       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Doorvoer per schijf | 100 MB per seconde | 150 MB per seconde | 200 MB per seconde | 250 MB per seconde | 250 MB per seconde |

Bepaal afhankelijk van uw werkbelasting of er extra gegevensschijven nodig zijn voor uw virtuele machine. U verschillende permanente gegevensschijven aan uw VM koppelen. Indien nodig u over de schijven strepen om de capaciteit en prestaties van het volume te verhogen. (Zie wat disk striping [hier](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Als u premium opslaggegevensschijven streept met [opslagruimten,][4]moet u deze configureren met één kolom voor elke schijf die wordt gebruikt. Anders kunnen de algehele prestaties van het gestreepte volume lager zijn dan verwacht als gevolg van een ongelijke verdeling van het verkeer over de schijven. Voor Linux VM's u het *mdadm-hulpprogramma* gebruiken om hetzelfde te bereiken. Zie artikel [Software RAID op Linux configureren](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor meer informatie.

#### <a name="storage-account-scalability-targets"></a>Schaalbaarheidsdoelen voor opslagaccount

Premium Storage-accounts hebben de volgende schaalbaarheidsdoelen. Als uw toepassingsvereisten de schaalbaarheidsdoelstellingen van één opslagaccount overschrijden, bouwt u uw toepassing om meerdere opslagaccounts te gebruiken en partitieert u uw gegevens over die opslagaccounts.

| Totale accountcapaciteit | Totale bandbreedte voor een lokaal redundant opslagaccount |
|:--- |:--- |
| Schijfcapaciteit: 35 TB<br />Momentopnamecapaciteit: 10 TB |Tot 50 gigabit per seconde voor Inbound + Outbound |

Zie [Schaalbaarheidsdoelen voor premium paginablob-opslagaccounts voor](../blobs/scalability-targets-premium-page-blobs.md)meer informatie over premium opslagspecificaties.

#### <a name="disk-caching-policy"></a>Beleid voor het plaatsen van schijven
Standaard is schijfcachebeleid *Alleen-lezen* voor alle Premium-gegevensschijven en *Lees-schrijfvoor* de schijf van het Premium-besturingssysteem die aan de VM is gekoppeld. Deze configuratie-instelling wordt aanbevolen om de optimale prestaties voor de IOs van uw toepassing te bereiken. Schakel schijfcaches uit voor alleen schrijfzware of write-only gegevensschijven (zoals SQL Server-logboekbestanden), en schakel schijfcache uit, zodat u betere toepassingsprestaties bereiken. De cache-instellingen voor bestaande gegevensschijven kunnen worden bijgewerkt met behulp van de [Azure-portal](https://portal.azure.com) of de parameter *-HostCaching* van de cmdlet *Set-AzureDataDisk.*

#### <a name="location"></a>Locatie
Kies een locatie waar Azure Premium Storage beschikbaar is. Zie [Azure Services by Region](https://azure.microsoft.com/regions/#services) voor actuele informatie over beschikbare locaties. VM's in dezelfde regio als het opslagaccount dat de schijven voor de VM opslaat, bieden veel betere prestaties dan wanneer ze zich in afzonderlijke regio's bevinden.

#### <a name="other-azure-vm-configuration-settings"></a>Andere Azure VM-configuratie-instellingen
Bij het maken van een Azure VM wordt u gevraagd om bepaalde VM-instellingen te configureren. Vergeet niet dat er weinig instellingen zijn vastgesteld voor de levensduur van de VM, terwijl u later anderen wijzigen of toevoegen. Controleer deze Azure VM-configuratie-instellingen en controleer of deze op de juiste manier zijn geconfigureerd om aan uw werkbelastingvereisten te voldoen.

### <a name="optimization"></a>Optimalisatie
[Azure Premium Storage: Design for High Performance](../../virtual-machines/windows/premium-storage-performance.md) biedt richtlijnen voor het bouwen van krachtige toepassingen met Azure Premium Storage. U de richtlijnen volgen in combinatie met best practices voor prestaties die van toepassing zijn op technologieën die door uw toepassing worden gebruikt.

## <a name="prepare-and-copy-virtual-hard-disks-vhds-to-premium-storage"></a><a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Virtuele harde schijven (VHD's) voorbereiden en kopiëren naar Premium Storage
In de volgende sectie vindt u richtlijnen voor het voorbereiden van VHD's van uw VM en het kopiëren van VHD's naar Azure Storage.

* [Scenario 1: "Ik migreer bestaande Azure VM's naar Azure Premium Storage."](#scenario1)
* [Scenario 2: "Ik migreer VM's van andere platforms naar Azure Premium Storage."](#scenario2)

### <a name="prerequisites"></a>Vereisten
Als u de VHD's wilt voorbereiden op migratie, hebt u het:

* Een Azure-abonnement, een opslagaccount en een container in dat opslagaccount waarnaar u uw VHD kopiëren. Houd er rekening mee dat het bestemmingsopslagaccount een standaard- of Premium-opslagaccount kan zijn, afhankelijk van uw behoefte.
* Een tool om de VHD te generaliseren als u van plan bent om er meerdere VM-exemplaren van te maken. Bijvoorbeeld sysprep voor Windows of virt-sysprep voor Ubuntu.
* Een tool om het VHD-bestand te uploaden naar het Opslagaccount. Zie [Gegevens overbrengen met het AzCopy-opdrachtregelhulpprogramma](storage-use-azcopy.md) of gebruik een [Azure-opslagverkenner](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). In deze handleiding wordt het kopiëren van uw VHD beschreven met behulp van het AzCopy-gereedschap.

> [!NOTE]
> Als u de optie Synchrone kopiëren met AzCopy kiest, kopieert u uw VHD voor optimale prestaties door een van deze hulpprogramma's uit te voeren vanuit een Azure VM die zich in dezelfde regio bevindt als het doelopslagaccount. Als u een VHD kopieert vanuit een Azure VM in een andere regio, kunnen uw prestaties trager zijn.
>
> Voor het kopiëren van een grote hoeveelheid gegevens over een beperkte bandbreedte u overwegen [de azure import/export-service te gebruiken om gegevens over te dragen naar Blob Storage;](../storage-import-export-service.md) Hiermee u uw gegevens overbrengen door harde schijven naar een Azure-datacenter te verzenden. U de Azure Import/Export-service gebruiken om alleen gegevens naar een standaardopslagaccount te kopiëren. Zodra de gegevens in uw standaardopslagaccount staan, u de [Copy Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) of AzCopy gebruiken om de gegevens over te zetten naar uw premium opslagaccount.
>
> Houd er rekening mee dat Microsoft Azure alleen VHD-bestanden met een vaste grootte ondersteunt. VHDX-bestanden of dynamische VHD's worden niet ondersteund. Als u een dynamische VHD hebt, u deze converteren naar een vaste grootte met behulp van de [Convert-VHD-cmdlet.](https://technet.microsoft.com/library/hh848454.aspx)
>
>

### <a name="scenario-1-i-am-migrating-existing-azure-vms-to-azure-premium-storage"></a><a name="scenario1"></a>Scenario 1: "Ik migreer bestaande Azure VM's naar Azure Premium Storage."
Als u bestaande Azure VM's migreert, stopt u de VM, bereidt u VHD's voor op het gewenste type VHD en kopieert u de VHD met AzCopy of PowerShell.

De VM moet volledig naar beneden om een schone status te migreren. Er zal een downtime zijn totdat de migratie is voltooid.

#### <a name="step-1-prepare-vhds-for-migration"></a>Step 1. VHD's voorbereiden op migratie
Als u bestaande Azure VM's migreert naar Premium Storage, kan uw VHD:

* Een algemene afbeelding van het besturingssysteem
* Een unieke schijf van het besturingssysteem
* Een gegevensschijf

Hieronder doorlopen we deze 3 scenario's voor de voorbereiding van uw VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Een gegeneraliseerd besturingssysteem VHD gebruiken om meerdere VM-exemplaren te maken
Als u een VHD uploadt die wordt gebruikt om meerdere generieke Azure VM-exemplaren te maken, moet u VHD eerst generaliseren met behulp van een sysprep-hulpprogramma. Dit geldt voor een VHD die on-premises of in de cloud is. Sysprep verwijdert alle machine-specifieke informatie uit de VHD.

> [!IMPORTANT]
> Maak een momentopname of maak een back-up van uw vm voordat u deze generaliseert. Als u sysprep uitvoert, wordt de VM-instantie gestopt en verwijderd. Volg de stappen hieronder om een Windows OS VHD te sysprepen. Houd er rekening mee dat u met de opdracht Sysprep de virtuele machine moet uitschakelen. Zie [Sysprep Overview](https://technet.microsoft.com/library/hh825209.aspx) of [Sysprep Technical Reference](https://technet.microsoft.com/library/cc766049.aspx)voor meer informatie over Sysprep.
>
>

1. Open een opdrachtpromptvenster als beheerder.
2. Voer de volgende opdracht in om Sysprep te openen:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Schakel in het systeemvoorbereidingshulpmiddel Systeemgebruik In Systeem out-of-box Experience (OOBE) in, schakel het selectievakje Generaliseren in, schakel **Afsluiten**in en klik vervolgens op **OK,** zoals in de afbeelding hieronder wordt weergegeven. Sysprep generaliseert het besturingssysteem en sluit het systeem af.

    ![][1]

Gebruik virt-sysprep voor een Ubuntu-VM om hetzelfde te bereiken. Zie [virt-sysprep](https://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) voor meer details. Zie ook enkele van de open source [Linux Server Provisioning software](https://www.cyberciti.biz/tips/server-provisioning-software.html) voor andere Linux besturingssystemen.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Gebruik een uniek Besturingssysteem VHD om één VM-exemplaar te maken
Als u een toepassing hebt die op de VM wordt uitgevoerd waarvoor de specifieke gegevens van de machine nodig zijn, generaliseer t.a.vhd. Een niet-gegeneraliseerde VHD kan worden gebruikt om een unieke Azure VM-instantie te maken. Als u bijvoorbeeld domeincontroller op uw VHD hebt, wordt het uitvoeren van sysprep ineffectief als domeincontroller. Bekijk de toepassingen die op uw VM worden uitgevoerd en de impact van het uitvoeren van sysprep op deze apparaten voordat u de VHD generaliseert.

##### <a name="register-data-disk-vhd"></a>Gegevensschijf VHD registreren
Als u gegevensschijven in Azure moet migreren, moet u ervoor zorgen dat de VM's die deze gegevensschijven gebruiken, worden afgesloten.

Volg de onderstaande stappen om VHD naar Azure Premium Storage te kopiëren en registreer deze als een ingerichte gegevensschijf.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Stap 2. De bestemming voor uw VHD maken
Maak een opslagaccount aan voor het onderhouden van uw VHD's. Houd rekening met de volgende punten bij het plannen van waar u uw VHD's wilt opslaan:

* Het doelPremium-opslagaccount.
* De locatie van het opslagaccount moet hetzelfde zijn als Azure VM's met Azure-vm's met een Premium-opslag die u in de laatste fase maakt. U kopiëren naar een nieuw opslagaccount of van plan zijn hetzelfde opslagaccount te gebruiken op basis van uw behoeften.
* Kopieer en sla de opslagaccountsleutel van het doelopslagaccount voor de volgende fase op.

Voor gegevensschijven u ervoor kiezen om sommige gegevensschijven in een standaardopslagaccount te bewaren (bijvoorbeeld schijven met koelere opslag), maar we raden u ten zeerste aan om alle gegevens te verplaatsen voor productiewerkbelasting om premium opslag te gebruiken.

#### <a name="step-3-copy-vhd-with-azcopy-or-powershell"></a><a name="copy-vhd-with-azcopy-or-powershell"></a>Stap 3. VHD kopiëren met AzCopy of PowerShell
U moet uw containerpad en opslagaccountsleutel vinden om een van deze twee opties te verwerken. Containerpad en opslagaccountsleutel zijn te vinden in **Azure Portal** > **Storage.** De url van de container\/zal zijn als "https: /myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Optie 1: Een VHD kopiëren met AzCopy (Asynchrone kopie)

Met AzCopy u de VHD eenvoudig uploaden via internet. Afhankelijk van de grootte van de VHD's kan dit tijd kosten. Vergeet niet om de opslagaccount ingress/uitgangslimieten te controleren wanneer u deze optie gebruikt. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](scalability-targets-standard-account.md) voor details.

1. AzCopy downloaden en installeren vanaf hier: [Nieuwste versie van AzCopy](https://aka.ms/downloadazcopy)
2. Open Azure PowerShell en ga naar de map waar AzCopy is geïnstalleerd.
3. Gebruik de volgende opdracht om het VHD-bestand te kopiëren van "Bron" naar "Bestemming".

   ```azcopy
   AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

    Voorbeeld:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```
 
   Hier volgen beschrijvingen van de parameters die worden gebruikt in de opdracht AzCopy:

   * **/Bron:** _ &lt;&gt;bron:_ Locatie van de map of opslagcontainer-URL die de VHD bevat.
   * **/SourceKey:** _ &lt;&gt;source-account-key :_ Storage account key of the source storage account account.
   * **/Dest:** _ &lt;&gt;bestemming :_ URL van opslagcontainer om de VHD naar te kopiëren.
   * **/DestKey:** _ &lt;dest-account-key&gt;:_ Opslagaccountsleutel van het bestemmingsopslagaccount.
   * **/Patroon:** _ &lt;bestandsnaam&gt;:_ Geef de bestandsnaam op van de VHD die u wilt kopiëren.

Zie [Gegevens overbrengen met het AzCopy-opdrachtregelhulpprogramma](storage-use-azcopy.md)voor meer informatie over het gebruik van azcopy.

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Optie 2: Een VHD kopiëren met PowerShell (gesynchroniseerde kopie)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U het VHD-bestand ook kopiëren met de PowerShell-cmdlet Start-AzStorageBlobCopy. Gebruik de volgende opdracht op Azure PowerShell om VHD te kopiëren. Vervang de waarden in <> door overeenkomstige waarden uit uw bron- en bestemmingsopslagaccount. Als u deze opdracht wilt gebruiken, moet u een container met de naam vhds in uw bestemmingsopslagaccount hebben. Als de container niet bestaat, maakt u er een voordat u de opdracht uitvoert.

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

### <a name="scenario-2-i-am-migrating-vms-from-other-platforms-to-azure-premium-storage"></a><a name="scenario2"></a>Scenario 2: "Ik migreer VM's van andere platforms naar Azure Premium Storage."
Als u VHD migreert van niet-Azure Cloud Storage naar Azure, moet u eerst de VHD exporteren naar een lokale map. Zorg ervoor dat het volledige bronpad van de lokale map waar VHD is opgeslagen bij de hand is en gebruik vervolgens AzCopy om het te uploaden naar Azure Storage.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Step 1. VHD exporteren naar een lokale map
##### <a name="copy-a-vhd-from-aws"></a>Een VHD kopiëren van AWS
1. Als u AWS gebruikt, exporteert u de EC2-instantie naar een VHD in een Amazon S3-bucket. Volg de stappen die zijn beschreven in de Amazon-documentatie voor het exporteren van Amazon EC2 Instances om het Amazon EC2-command-line-interface (CLI)-hulpprogramma te installeren en de opdracht create-instance-export-task uit te voeren om de EC2-instantie naar een VHD-bestand te exporteren. Zorg ervoor dat u **VHD** gebruikt voor de&#95;IMAGE&#95;FORMAT-variabele bij het uitvoeren van de opdracht **create-instance-export-task.** Het geëxporteerde VHD-bestand wordt opgeslagen in de Amazon S3-bucket die u tijdens dat proces aanwijst.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Download het VHD-bestand uit de S3-bucket. Selecteer het VHD-bestand en download **vervolgens** > **Acties**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Een VHD kopiëren vanuit andere niet-Azure-cloud
Als u VHD migreert van niet-Azure Cloud Storage naar Azure, moet u eerst de VHD exporteren naar een lokale map. Kopieer het volledige bronpad van de lokale map waar VHD is opgeslagen.

##### <a name="copy-a-vhd-from-on-premises"></a>Een VHD kopiëren van on-premises
Als u VHD migreert vanuit een on-premises omgeving, hebt u het volledige bronpad nodig waar VHD is opgeslagen. Het bronpad kan een serverlocatie of bestandsshare zijn.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Stap 2. De bestemming voor uw VHD maken
Maak een opslagaccount aan voor het onderhouden van uw VHD's. Houd rekening met de volgende punten bij het plannen van waar u uw VHD's wilt opslaan:

* Het doelopslagaccount kan standaard- of premiumopslag zijn, afhankelijk van uw toepassingsvereisten.
* Het opslagaccountgebied moet hetzelfde zijn als Azure VM's met Azure-vm's met premiumopslag die u in de laatste fase maakt. U kopiëren naar een nieuw opslagaccount of van plan zijn hetzelfde opslagaccount te gebruiken op basis van uw behoeften.
* Kopieer en sla de opslagaccountsleutel van het doelopslagaccount voor de volgende fase op.

We raden u ten zeerste aan alle gegevens te verplaatsen voor productiewerkbelasting naar premium opslag.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Stap 3. De VHD uploaden naar Azure Storage
Nu u uw VHD in de lokale map hebt, u AzCopy of AzurePowerShell gebruiken om het VHD-bestand te uploaden naar Azure Storage. Beide opties zijn hier beschikbaar:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Optie 1: Azure PowerShell Add-AzureVhd gebruiken om het .vhd-bestand te uploaden

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Een \<voorbeeld dat Uri> zou kunnen zijn **_"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd._** Een \<voorbeeld van FileInfo-> kan **_zijn :C:\path\to\upload.vhd.'_**.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Optie 2: AzCopy gebruiken om het .vhd-bestand te uploaden

Met AzCopy u de VHD eenvoudig uploaden via internet. Afhankelijk van de grootte van de VHD's kan dit tijd kosten. Vergeet niet om de opslagaccount ingress/uitgangslimieten te controleren wanneer u deze optie gebruikt. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts](scalability-targets-standard-account.md) voor details.

1. AzCopy downloaden en installeren vanaf hier: [Nieuwste versie van AzCopy](https://aka.ms/downloadazcopy)
2. Open Azure PowerShell en ga naar de map waar AzCopy is geïnstalleerd.
3. Gebruik de volgende opdracht om het VHD-bestand te kopiëren van "Bron" naar "Bestemming".

   ```azcopy
      AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
   ```

   Voorbeeld:

   ```azcopy
      AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
   ```

   Hier volgen beschrijvingen van de parameters die worden gebruikt in de opdracht AzCopy:

   * **/Bron:** _ &lt;&gt;bron:_ Locatie van de map of opslagcontainer-URL die de VHD bevat.
   * **/SourceKey:** _ &lt;&gt;source-account-key :_ Storage account key of the source storage account account.
   * **/Dest:** _ &lt;&gt;bestemming :_ URL van opslagcontainer om de VHD naar te kopiëren.
   * **/DestKey:** _ &lt;dest-account-key&gt;:_ Opslagaccountsleutel van het bestemmingsopslagaccount.
   * **/BlobType: pagina:** Hiermee geeft u op dat de bestemming een paginablob is.
   * **/Patroon:** _ &lt;bestandsnaam&gt;:_ Geef de bestandsnaam op van de VHD die u wilt kopiëren.

Zie [Gegevens overbrengen met het AzCopy-opdrachtregelhulpprogramma](storage-use-azcopy.md)voor meer informatie over het gebruik van azcopy.

##### <a name="other-options-for-uploading-a-vhd"></a>Andere opties voor het uploaden van een VHD
U ook een VHD uploaden naar uw opslagaccount met een van de volgende middelen:

* [Blob-API voor kopiëren van Azure-opslag](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Blobs uploaden van Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
* [Naslaginformatie over opslagimport/exportserviceREST API](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> We raden u aan de import/exportservice te gebruiken als de geschatte uploadtijd langer is dan 7 dagen. U [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) gebruiken om de tijd van de gegevensgrootte en overdrachtseenheid in te schatten.
>
> Import/Export kan worden gebruikt om te kopiëren naar een standaard opslagaccount. U moet kopiëren van standaard opslag naar premium opslag account met behulp van een tool als AzCopy.
>
>

## <a name="create-azure-vms-using-premium-storage"></a><a name="create-azure-virtual-machine-using-premium-storage"></a>Azure VM's maken met Premium Storage
Nadat de VHD is geüpload of gekopieerd naar het gewenste opslagaccount, volgt u de instructies in deze sectie om de VHD te registreren als een OS-afbeelding of osschijf, afhankelijk van uw scenario en maakt u er vervolgens een VM-exemplaar van. De gegevensschijf VHD kan aan de VM worden gekoppeld zodra deze is gemaakt.
Aan het einde van deze sectie wordt een voorbeeldmigratiescript verstrekt. Dit eenvoudige script komt niet overeen met alle scenario's. Mogelijk moet u het script bijwerken om aan uw specifieke scenario te voldoen. Zie hieronder [Een voorbeeldmigratiescript](#a-sample-migration-script)om te zien of dit script van toepassing is op uw scenario.

### <a name="checklist"></a>Controlelijst
1. Wacht tot alle VHD-schijven zijn gekopieerd.
2. Controleer of Premium Storage beschikbaar is in de regio waarnaar u migreert.
3. Bepaal de nieuwe VM-serie die u gaat gebruiken. Het moet een Premium Storage staat, en de grootte moet afhankelijk zijn van de beschikbaarheid in de regio en op basis van uw behoeften.
4. Bepaal de exacte VM-grootte die u gebruikt. Vm-grootte moet groot genoeg zijn om het aantal gegevensschijven dat u hebt te ondersteunen. Bijvoorbeeld als u 4 gegevensschijven hebt, moet de VM 2 of meer kernen hebben. Overweeg ook verwerkingskracht, geheugen en netwerkbandbreedtebehoeften.
5. Maak een Premium Storage-account in de doelregio. Dit is het account dat u gebruikt voor de nieuwe VM.
6. Heb de huidige VM-details bij de hand, inclusief de lijst met schijven en bijbehorende VHD-blobs.

Bereid uw toepassing voor op downtime. Om een schone migratie uit te voeren, moet u alle verwerking in het huidige systeem stoppen. Alleen dan u het naar consistente staat krijgen die u migreren naar het nieuwe platform. De downtimeduur is afhankelijk van de hoeveelheid gegevens in de schijven die moeten worden gemigreerd.

> [!NOTE]
> Als u een Azure Resource Manager VM maakt vanuit een gespecialiseerde VHD-schijf, raadpleegt u [deze sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) voor het implementeren van Resource Manager VM met behulp van bestaande schijf.
>
>

### <a name="register-your-vhd"></a>Registreer uw VHD
Als u een VM wilt maken vanaf OS VHD of een gegevensschijf wilt koppelen aan een nieuwe virtuele machine, moet u deze eerst registreren. Volg onderstaande stappen, afhankelijk van het scenario van uw VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Gegeneraliseerde Besturingssysteem VHD om meerdere Azure VM-exemplaren te maken
Nadat gegeneraliseerde OS-afbeelding VHD is geüpload naar het opslagaccount, registreert u deze als een **Azure VM-afbeelding,** zodat u er een of meer VM-exemplaren van maken. Gebruik de volgende PowerShell-cmdlets om uw VHD te registreren als een Azure VM OS-afbeelding. Geef de volledige container-URL op waarnaar VHD is gekopieerd.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Kopieer en sla de naam van deze nieuwe Azure VM Image op. In het bovenstaande voorbeeld is dit *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Uniek besturingssysteem VHD om één Azure VM-exemplaar te maken
Nadat het unieke OS VHD is geüpload naar het opslagaccount, registreert u het als een **Azure OS-schijf,** zodat u er een VM-exemplaar van maken. Gebruik deze PowerShell-cmdlets om uw VHD te registreren als Azure OS-schijf. Geef de volledige container-URL op waarnaar VHD is gekopieerd.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Kopieer en sla de naam van deze nieuwe Azure OS Disk op. In het bovenstaande voorbeeld is het *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Gegevensschijf VHD die moet worden gekoppeld aan nieuwe Azure VM-instantie(s)
Nadat de gegevensschijf VHD is geüpload naar een opslagaccount, registreert u deze als een Azure-gegevensschijf, zodat deze kan worden gekoppeld aan uw nieuwe Azure VM-exemplaar uit de DS-serie, DSv2-serie of GS-serie.

Gebruik deze PowerShell-cmdlets om uw VHD te registreren als azure-gegevensschijf. Geef de volledige container-URL op waarnaar VHD is gekopieerd.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Kopieer en sla de naam van deze nieuwe Azure Data Disk op. In het bovenstaande voorbeeld is dit *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Een VM met premiumopslag maken
Zodra de OS-afbeelding of OS-schijf is geregistreerd, maakt u een nieuwe DS-serie, DSv2-serie of GS-serie VM. U gebruikt de afbeelding van het besturingssysteem of de schijfnaam van het besturingssysteem die u hebt geregistreerd. Selecteer het VM-type in de laag Premium-opslag. In onderstaandvoorbeeld gebruiken we de *Standard_DS2* VM-grootte.

> [!NOTE]
> Werk de schijfgrootte bij om te zien of deze overeenkomt met uw capaciteits- en prestatievereisten en de beschikbare Azure-schijfformaten.
>
>

Volg de stap voor stap PowerShell-cmdlets hieronder om de nieuwe VM te maken. Stel eerst de algemene parameters in:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Maak eerst een cloudservice waarin u uw nieuwe VM's host.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Maak vervolgens, afhankelijk van uw scenario, de Azure VM-instantie op basis van de OS Image of OS Disk die u hebt geregistreerd.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Gegeneraliseerde Besturingssysteem VHD om meerdere Azure VM-exemplaren te maken
Maak de een of meer nieuwe Azure VM-exemplaren uit de DS-reeks met de **Azure OS Image** die u hebt geregistreerd. Geef deze naam van deze OS-afbeelding op in de VM-configuratie bij het maken van nieuwe vm zoals hieronder weergegeven.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Uniek besturingssysteem VHD om één Azure VM-exemplaar te maken
Maak een nieuwe Azure VM-exemplaar uit de DS-serie met de **Azure OS Disk** die u hebt geregistreerd. Geef deze naam van de OS-schijf op in de VM-configuratie bij het maken van de nieuwe vm zoals hieronder weergegeven.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Geef andere Azure VM-gegevens op, zoals een cloudservice, regio, opslagaccount, beschikbaarheidsset en cachingbeleid. Houd er rekening mee dat de VM-instantie moet worden geplaatst met gekoppelde besturingssysteem- of gegevensschijven, zodat de geselecteerde cloudservice, regio en opslagaccount zich allemaal op dezelfde locatie moeten bevinden als de onderliggende VHD's van die schijven.

### <a name="attach-data-disk"></a>Een gegevensschijf koppelen
Als u VHD's met geregistreerde gegevensschijf hebt geregistreerd, koppelt u deze tot slot aan de nieuwe Azure VM met Premium Storage-ondersteuning.

Gebruik de volgende PowerShell-cmdlet om gegevensschijf aan de nieuwe virtuele machine te koppelen en het cachingbeleid op te geven. In voorbeeld onder het cachingbeleid is ingesteld op *Alleen lezen*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Er kunnen aanvullende stappen nodig zijn om uw toepassing te ondersteunen die niet onder deze handleiding valt.
>
>

### <a name="checking-and-plan-backup"></a>Back-up controleren en plannen
Zodra de nieuwe VM operationeel is, krijgt u toegang tot deze met dezelfde inlog-id en hetzelfde wachtwoord als de oorspronkelijke VM en controleert u of alles werkt zoals verwacht. Alle instellingen, inclusief de gestreepte volumes, zouden aanwezig zijn in de nieuwe VM.

De laatste stap is het plannen van back-up- en onderhoudsschema's voor de nieuwe VM op basis van de behoeften van de toepassing.

### <a name="a-sample-migration-script"></a><a name="a-sample-migration-script"></a>Een voorbeeldmigratiescript
Als u meerdere VM's hebt om te migreren, is automatisering via PowerShell-scripts handig. Hieronder volgt een voorbeeldscript dat de migratie van een VM automatiseert. Houd er rekening mee dat onderstaand script slechts een voorbeeld is en er zijn weinig aannames gemaakt over de huidige VM-schijven. Mogelijk moet u het script bijwerken om aan uw specifieke scenario te voldoen.

De veronderstellingen zijn:

* U maakt klassieke Azure VM's.
* Uw bron-besturingssysteemschijven en brongegevensschijven bevinden zich in hetzelfde opslagaccount en dezelfde container. Als uw besturingssysteemschijven en gegevensschijven zich niet op dezelfde plaats bevinden, u AzCopy of Azure PowerShell gebruiken om VHD's te kopiëren via opslagaccounts en containers. Raadpleeg de vorige stap: [Kopieer VHD met AzCopy of PowerShell](#copy-vhd-with-azcopy-or-powershell). Het bewerken van dit script om aan uw scenario te voldoen is een andere keuze, maar we raden u aan AzCopy of PowerShell te gebruiken, omdat het gemakkelijker en sneller is.

Het automatiseringsscript vindt u hieronder. Vervang tekst door uw gegevens en werk het script bij om aan uw specifieke scenario te voldoen.

> [!NOTE]
> Als u het bestaande script gebruikt, wordt de netwerkconfiguratie van uw bron-vm niet behouden. U moet de netwerkinstellingen op uw gemigreerde VM's opnieuw config.
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

#### <a name="optimization"></a><a name="optimization"></a>Optimalisatie
Uw huidige VM-configuratie kan specifiek worden aangepast om goed te werken met standaardschijven. Bijvoorbeeld om de prestaties te verhogen door veel schijven in een gestreept volume te gebruiken. In plaats van bijvoorbeeld 4 schijven afzonderlijk te gebruiken op Premium Storage, u de kosten mogelijk optimaliseren door één schijf te hebben. Optimalisaties zoals deze moeten per geval worden afgehandeld en vereisen aangepaste stappen na de migratie. Houd er ook rekening mee dat dit proces mogelijk niet goed werkt voor databases en toepassingen die afhankelijk zijn van de schijfindeling die in de installatie is gedefinieerd.

##### <a name="preparation"></a>Voorbereiding
1. Voltooi de eenvoudige migratie zoals beschreven in de eerdere sectie. Optimalisaties worden uitgevoerd op de nieuwe VM na de migratie.
2. Definieer de nieuwe schijfformaten die nodig zijn voor de geoptimaliseerde configuratie.
3. Bepaal de toewijzing van de huidige schijven/volumes aan de nieuwe schijfspecificaties.

##### <a name="execution-steps"></a>Uitvoeringsstappen
1. Maak de nieuwe schijven met de juiste formaten op de PREMIUM Storage VM.
2. Log in bij de VM en kopieer de gegevens van het huidige volume naar de nieuwe schijf die naar dat volume wordt toegewezen. Doe dit voor alle huidige volumes die moeten worden toegewezen aan een nieuwe schijf.
3. Wijzig vervolgens de toepassingsinstellingen om over te schakelen naar de nieuwe schijven en maak de oude volumes los.

Voor het afstemmen van de toepassing voor betere schijfprestaties, verwijzen wij u naar het gedeelte voor het optimaliseren van de toepassingsprestaties van [ons ontwerp voor een artikel met hoge prestaties.](../../virtual-machines/windows/premium-storage-performance.md)

### <a name="application-migrations"></a>Toepassingsmigraties
Databases en andere complexe toepassingen vereisen mogelijk speciale stappen zoals gedefinieerd door de toepassingsprovider voor de migratie. Raadpleeg de desbetreffende aanvraagdocumentatie. Bijvoorbeeld doorgaans kunnen databases worden gemigreerd via back-up en herstel.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor specifieke scenario's voor het migreren van virtuele machines:

* [Azure Virtual Machines migreren tussen opslagaccounts](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Een Windows Server VHD maken en uploaden naar Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Een Linux VHD maken en uploaden naar Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Virtuele machines migreren van Amazon AWS naar Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zie ook de volgende bronnen voor meer informatie over Azure Storage en Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtuele machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Een schijftype selecteren voor IaaS-VM's](../../virtual-machines/windows/disks-types.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: https://technet.microsoft.com/library/hh831739.aspx
