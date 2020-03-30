---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72b6c1f86b18df172994827ec78eb109fe82454e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75913769"
---
## <a name="benefits-of-managed-disks"></a>Voordelen van beheerde schijven

Laten we eens kijken over een aantal van de voordelen die u krijgt met behulp van beheerde schijven.

### <a name="highly-durable-and-available"></a>Zeer duurzaam en beschikbaar

Beheerde schijven zijn ontworpen voor 99,999% beschikbaarheid. Beheerde schijven bereiken dit door u te voorzien van drie replica's van uw gegevens, waardoor een hoge duurzaamheid mogelijk is. Als een of zelfs twee replica's problemen ondervinden, zorgen de resterende replica's voor persistentie van uw gegevens en een hoge tolerantie tegen storingen. Deze architectuur heeft Azure geholpen om consistent enterprise-grade duurzaamheid te leveren voor iaas-schijven (infrastructure as a service), met een toonaangevend zero% per jaar uitvalpercentage.

### <a name="simple-and-scalable-vm-deployment"></a>Eenvoudige en schaalbare VM-implementatie

Met beheerde schijven u tot 50.000 **VM-schijven** van een type per regio maken, zodat u duizenden **VM's** in één abonnement maken. Deze functie verhoogt ook de schaalbaarheid van [virtuele machineschaalsets](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) doordat u tot 1.000 VM's maken in een virtuele machineschaalset met behulp van een Marketplace-afbeelding.

### <a name="integration-with-availability-sets"></a>Integratie met beschikbaarheidssets

Beheerde schijven zijn geïntegreerd met beschikbaarheidssets om ervoor te zorgen dat de schijven van [VM's in een beschikbaarheidsset](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) voldoende van elkaar zijn geïsoleerd om één storingspunt te voorkomen. Schijven worden automatisch in verschillende opslagschaaleenheden (stempels) geplaatst. Als een stempel uitvalt als gevolg van hardware- of softwarefouten, mislukken alleen de VM-exemplaren met schijven op die stempels. Stel dat u een toepassing hebt die op vijf VM's wordt uitgevoerd en dat de VM's zich in een beschikbaarheidsset bevinden. De schijven voor die VM's worden niet allemaal in dezelfde stempel opgeslagen, dus als één stempel naar beneden gaat, blijven de andere exemplaren van de toepassing worden uitgevoerd.

### <a name="integration-with-availability-zones"></a>Integratie met beschikbaarheidszones

Beheerde schijven ondersteunen [beschikbaarheidszones](../articles/availability-zones/az-overview.md), een aanbod met hoge beschikbaarheid dat uw toepassingen beschermt tegen datacenterfouten. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. Met beschikbaarheidszones biedt Azure de beste uptime SLA voor VM’s van de branche, van 99,99%.

### <a name="azure-backup-support"></a>Azure Backup-ondersteuning

Om te beschermen tegen regionale rampen, kan [Azure Backup](../articles/backup/backup-overview.md) worden gebruikt om een back-uptaak te maken met op tijd gebaseerde back-ups en beleid voor het bewaren van back-ups. Hierdoor u naar eigen zeggen eenvoudige VM-restauraties uitvoeren. Momenteel ondersteunt Azure Backup schijfformaten tot vier Tebibyte -schijven (TiB).  Azure Backup ondersteunt back-up sup- en herstel van beheerde schijven. [Meer informatie](../articles/backup/backup-support-matrix-iaas.md) over Azure VM-back-upondersteuning.

### <a name="granular-access-control"></a>Gedetailleerd toegangscontrole

U [RBAC (Azure role-based access control)](../articles/role-based-access-control/overview.md) gebruiken om specifieke machtigingen voor een beheerde schijf toe te wijzen aan een of meer gebruikers. Beheerde schijven leggen verschillende bewerkingen bloot, waaronder lezen, schrijven (maken/bijwerken), verwijderen en ophalen van een [SAS-uri (Shared Access Signature)](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) voor de schijf. U alleen toegang verlenen tot de bewerkingen die een persoon nodig heeft om zijn taak uit te voeren. Als u bijvoorbeeld niet wilt dat een persoon een beheerde schijf naar een opslagaccount kopieert, u ervoor kiezen geen toegang te verlenen tot de exportactie voor die beheerde schijf. Als u ook niet wilt dat een persoon een SAS URI gebruikt om een beheerde schijf te kopiëren, u ervoor kiezen die toestemming niet aan de beheerde schijf te verlenen.

### <a name="upload-your-vhd"></a>Upload je vhd

 Directe upload maakt het eenvoudig om uw vhd over te zetten naar een door Azure beheerde schijf. Voorheen moest u een meer betrokken proces volgen dat het organiseren van uw gegevens in een opslagaccount omvatte. Nu zijn er minder stappen. Het is eenvoudiger om on-premises VM's naar Azure te uploaden, te uploaden naar grote beheerde schijven en het back-up- en herstelproces wordt vereenvoudigd. Het vermindert ook de kosten doordat u gegevens rechtstreeks naar beheerde schijven uploaden zonder ze aan VM's te koppelen. U direct uploaden gebruiken om vhds tot 32 TiB groot te uploaden.

 Zie de [ARTIKELEN CLI](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) of [PowerShell](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md) voor meer informatie over het overbrengen van uw vhd naar Azure.

## <a name="encryption"></a>Versleuteling

Beheerde schijven bieden twee verschillende soorten encryptie. De eerste is Server Side Encryption (SSE), die wordt uitgevoerd door de opslagservice. De tweede is Azure Disk Encryption (ADE), die u inschakelen op het besturingssysteem en gegevensschijven voor uw VM's.

### <a name="server-side-encryption"></a>Versleuteling aan de serverzijde

[Azure Server-side Encryption](../articles/virtual-machines/windows/disk-encryption.md) biedt versleuteling in rust en beschermt uw gegevens om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Server-side encryptie is standaard ingeschakeld voor alle beheerde schijven, snapshots en afbeeldingen in alle regio's waar beheerde schijven beschikbaar zijn. U Azure toestaan uw sleutels voor u te beheren, dit zijn door het platform beheerde sleutels, of u de sleutels zelf beheren, dit zijn door de klant beheerde sleutels. Ga naar de [veelgestelde vragen over beheerde schijven](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) voor meer informatie.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption stelt u in staat om de besturingssysteem- en gegevensschijven te versleutelen die worden gebruikt door een Virtuele IaaS-machine. Deze versleuteling omvat beheerde schijven. Voor Windows worden de schijven versleuteld met behulp van industriestandaard BitLocker-versleutelingstechnologie. Voor Linux worden de schijven versleuteld met behulp van de DM-Crypt-technologie. Het versleutelingsproces is geïntegreerd met Azure Key Vault zodat u de schijfversleutelingssleutels kunt controleren en beheren. Zie [Azure Disk Encryption for IaaS VM's voor](../articles/security/azure-security-disk-encryption-overview.md)meer informatie.

## <a name="disk-roles"></a>Schijfrollen

Er zijn drie hoofdschijfrollen in Azure: de gegevensschijf, de osschijf en de tijdelijke schijf. Deze rollen worden toegewezen aan schijven die zijn gekoppeld aan uw virtuele machine.

![Schijfrollen in actie](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Gegevensschijf

Een gegevensschijf is een beheerde schijf die is gekoppeld aan een virtuele machine om toepassingsgegevens op te slaan of andere gegevens die u moet bewaren. Gegevensschijven worden geregistreerd als SCSI-stations en worden gelabeld met een door u gekozen letter. Elke dataschijf heeft een maximale capaciteit van 32.767 gibibytes (GiB). De grootte van de virtuele machine bepaalt hoeveel gegevensschijven u eraan koppelen en het type opslag dat u gebruiken om de schijven te hosten.

### <a name="os-disk"></a>Besturingssysteemschijf

Elke virtuele machine heeft een aangesloten besturingssysteem schijf. Die OS-schijf heeft een vooraf geïnstalleerd besturingssysteem, dat is geselecteerd toen de VM werd gemaakt. Deze schijf bevat het opstartvolume.

Deze schijf heeft een maximale capaciteit van 2.048 GiB.

### <a name="temporary-disk"></a>Tijdelijke schijf


Elke VM bevat een tijdelijke schijf, die geen beheerde schijf is. De tijdelijke schijf biedt opslag op korte termijn voor toepassingen en processen en is bedoeld om alleen gegevens op te slaan, zoals pagina- of swapbestanden. Gegevens op de tijdelijke schijf kunnen verloren gaan tijdens een [onderhoudsgebeurtenis](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) of wanneer u [een VM opnieuw implementeert.](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) Op Azure Linux VM's is de tijdelijke schijf standaard /dev/sdb en op Windows VM's is de tijdelijke schijf D: standaard. Tijdens een succesvolle standaard reboot van de VM blijven de gegevens op de tijdelijke schijf bestaan.

## <a name="managed-disk-snapshots"></a>Beheerde schijfmomentopnamen

Een beheerde schijfmomentopname is een alleen-lezen crashconsistente volledige kopie van een beheerde schijf die standaard als standaard beheerde schijf is opgeslagen. Met momentopnamen u op elk gewenst moment een back-up maken van uw beheerde schijven. Deze momentopnamen bestaan onafhankelijk van de bronschijf en kunnen worden gebruikt om nieuwe beheerde schijven te maken. 

Momentopnamen worden gefactureerd op basis van de gebruikte grootte. Als u bijvoorbeeld een momentopname maakt van een beheerde schijf met een ingerichte capaciteit van 64 GiB en de werkelijke gebruikte gegevensgrootte van 10 GiB, wordt die momentopname alleen gefactureerd voor de gebruikte gegevensgrootte van 10 GiB. U de gebruikte grootte van uw momentopnamen bekijken door het [Azure-gebruiksrapport te](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)bekijken. Als de gebruikte gegevensgrootte van een momentopname bijvoorbeeld 10 GiB is, wordt in het **dagelijkse** gebruiksrapport 10 GiB/(31-dagen) = 0,3226 weergegeven als de verbruikte hoeveelheid.

Zie de volgende bronnen voor meer informatie over het maken van momentopnamen voor beheerde schijven:

* [Een momentopname van een beheerde schijf maken in Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Een momentopname van een beheerde schijf maken in Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Installatiekopieën

Beheerde schijven ondersteunen ook het maken van een beheerde aangepaste afbeelding. U een afbeelding maken van uw aangepaste VHD in een opslagaccount of rechtstreeks vanaf een gegeneraliseerde (sysprepped) VM. Dit proces legt één beeld vast. Deze afbeelding bevat alle beheerde schijven die zijn gekoppeld aan een vm, inclusief zowel het besturingssysteem als de gegevensschijven. Met deze beheerde aangepaste afbeelding u honderden VM's maken met uw aangepaste afbeelding zonder dat u opslagaccounts hoeft te kopiëren of beheren.

Zie de volgende artikelen voor informatie over het maken van afbeeldingen:

* [Een beheerde afbeelding van een gegeneraliseerde VM vastleggen in Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Een virtuele Linux-machine generaliseren en vastleggen met de Azure CLI](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Afbeeldingen versus momentopnamen

Het is belangrijk om het verschil tussen afbeeldingen en snapshots te begrijpen. Met beheerde schijven u een afbeelding maken van een gegeneraliseerde VM die is toegewezen. Deze afbeelding bevat alle schijven die aan de VM zijn gekoppeld. U deze afbeelding gebruiken om een vm te maken en alle schijven.

Een momentopname is een kopie van een schijf op het moment dat de momentopname wordt gemaakt. Het is slechts op één schijf van toepassing. Als u een vm hebt met één schijf (de osschijf), u er een momentopname of een afbeelding van maken en een VM maken op basis van de momentopname of de afbeelding.

Een momentopname heeft geen bewustzijn van een schijf, behalve degene die het bevat. Dit maakt het problematisch om te gebruiken in scenario's die de coördinatie van meerdere schijven vereisen, zoals striping. Momentopnamen moeten met elkaar kunnen coördineren en dit wordt momenteel niet ondersteund.

## <a name="disk-allocation-and-performance"></a>Toewijzing en prestaties van schijven

In het volgende diagram wordt real-time toewijzing van bandbreedte en IOPS voor schijven weergegeven, met behulp van een inrichtingssysteem op drie niveaus:

![Drie niveau inrichtingsysteem met bandbreedte en IOPS-toewijzing](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

In de inrichting op het eerste niveau wordt de IOPS per schijf en de bandbreedtetoewijzing ingesteld.  Op het tweede niveau implementeert compute server host SSD provisioning, waarbij deze alleen wordt toegepast op gegevens die zijn opgeslagen op de SSD van de server, waaronder schijven met caching (ReadWrite en ReadOnly) en lokale en tijdelijke schijven. Ten slotte vindt VM-netwerkinrichting plaats op het derde niveau voor elke I/O die de compute host naar de back-end van Azure Storage verzendt. Met dit schema zijn de prestaties van een VM afhankelijk van verschillende factoren, van de manier waarop de VM de lokale SSD gebruikt, tot het aantal schijven dat is aangesloten, evenals het prestatie- en cachingtype van de schijven die zijn aangesloten.

Als voorbeeld van deze beperkingen kan een Standard_DS1v1 VM het 5.000 IOPS-potentieel van een P30-schijf niet bereiken, of deze nu in de cache wordt opgeslagen of niet, vanwege limieten op SSD- en netwerkniveau:

![Standard_DS1v1 voorbeeldtoewijzing](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure gebruikt geprioriteerd netwerkkanaal voor schijfverkeer, dat de prioriteit krijgt boven andere lage prioriteit van netwerkverkeer. Dit helpt schijven hun verwachte prestaties te behouden in het geval van netwerkconflicten. Op dezelfde manier verwerkt Azure Storage resourcestellingen en andere problemen op de achtergrond met automatische taakverdeling. Azure Storage wijst vereiste resources toe wanneer u een schijf maakt en past proactieve en reactieve afweging van resources toe om het verkeersniveau te verwerken. Dit zorgt er verder voor dat schijven hun verwachte IOPS- en doorvoerdoelen kunnen ondersteunen. U de statistieken op VM-niveau en schijfniveau gebruiken om de prestatie- en installatiewaarschuwingen zo nodig bij te houden.

Raadpleeg ons [ontwerp voor een artikel met hoge prestaties,](../articles/virtual-machines/windows/premium-storage-performance.md) om de aanbevolen procedures voor het optimaliseren van VM + Schijfconfiguraties te leren, zodat u de gewenste prestaties bereiken

## <a name="next-steps"></a>Volgende stappen

Als u een video wilt die meer in detail op beheerde schijven wordt behandeld, bekijk dan: [Beter Azure VM-tolerantie met beheerde schijven](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Lees meer over de afzonderlijke schijftypen die Azure biedt, welk type geschikt is voor uw behoeften en lees meer over hun prestatiedoelen in ons artikel over schijftypen.
