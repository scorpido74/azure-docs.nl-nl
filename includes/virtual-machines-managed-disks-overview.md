---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: fc5a974273cbc0398e9fee7b60b424a46ef5dd30
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949713"
---
## <a name="benefits-of-managed-disks"></a>Voor delen van beheerde schijven

Laten we eens profiteren van enkele voor delen van het gebruik van beheerde schijven.

### <a name="highly-durable-and-available"></a>Zeer duurzaam en hoge beschikbaarheid

Managed disks zijn ontworpen voor een Beschik baarheid van 99,999%. Managed disks behaalt dit door u drie replica's van uw gegevens te bieden, zodat u een hoge duurzaamheid krijgt. Als er zich problemen voordoen met een of zelfs twee van de replica’s, kunnen de resterende replica’s ervoor zorgen dat uw gegevens bewaard blijven en storingen weinig kwaad kunnen. Deze architectuur heeft ervoor bijgedragen dat Azure consistente duurzaamheid levert voor IaaS-schijven (Infrastructure as a Service), met een toonaangevende storing van nul% per jaar.

### <a name="simple-and-scalable-vm-deployment"></a>Eenvoudige en schaal bare VM-implementatie

Met Managed disks kunt u tot 50.000 VM- **schijven** van een type in een abonnement per regio maken, zodat u duizenden **virtuele machines** in één abonnement hoeft te maken. Deze functie verhoogt ook de schaal baarheid van [schaal sets voor virtuele machines](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , zodat u maxi maal 1.000 vm's in een schaalset voor virtuele machines kunt maken met behulp van een Marketplace-installatie kopie.

### <a name="integration-with-availability-sets"></a>Integratie met beschikbaarheids sets

Beheerde schijven zijn geïntegreerd met beschikbaarheids sets om ervoor te zorgen dat de schijven van [virtuele machines in een beschikbaarheidsset](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) voldoende van elkaar zijn geïsoleerd om een single point of failure te voor komen. Schijven worden automatisch in verschillende opslag schaal eenheden (stempels) geplaatst. Als een stempel mislukt als gevolg van een hardware-of software fout, mislukken alleen de VM-exemplaren met schijven op deze stem pels. Stel bijvoorbeeld dat u een toepassing hebt die op vijf Vm's wordt uitgevoerd en dat de virtuele machines zich in een Beschikbaarheidsset bevinden. De schijven voor deze Vm's worden niet allemaal opgeslagen in dezelfde stempel, dus als de ene stempel uitvalt, worden de andere exemplaren van de toepassing nog steeds uitgevoerd.

### <a name="integration-with-availability-zones"></a>Integratie met Beschikbaarheidszones

Managed disks biedt ondersteuning voor [Beschikbaarheidszones](../articles/availability-zones/az-overview.md). Dit is een aanbieding met hoge Beschik baarheid die uw toepassingen beschermt tegen Data Center-fouten. Beschikbaarheidszones zijn unieke fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. Met beschikbaarheidszones biedt Azure de beste uptime SLA voor VM’s van de branche, van 99,99%.

### <a name="azure-backup-support"></a>Ondersteuning voor Azure Backup

[Azure backup](../articles/backup/backup-overview.md) kunnen worden gebruikt om een back-uptaak te maken met back-ups op basis van tijd en het Bewaar beleid voor back-ups. Op die manier kunt u eenvoudig herstel bewerkingen voor de virtuele machine uitvoeren. Momenteel Azure Backup ondersteunt schijf grootten tot wel vier TiB-schijven (tebibyte).  Azure Backup ondersteunt het maken van back-ups en het herstellen van Managed disks. Meer [informatie](../articles/backup/backup-support-matrix-iaas.md) over ondersteuning voor Azure VM-back-ups.

### <a name="granular-access-control"></a>Gedetailleerd toegangs beheer

U kunt op [rollen gebaseerd toegangs beheer (RBAC) van Azure](../articles/role-based-access-control/overview.md) gebruiken voor het toewijzen van specifieke machtigingen voor een beheerde schijf aan een of meer gebruikers. Beheerde schijven bieden diverse bewerkingen, waaronder lezen, schrijven (maken/bijwerken), verwijderen en ophalen van een [SAS-URI (Shared Access Signature)](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) voor de schijf. U kunt alleen toegang verlenen aan de bewerkingen die een persoon nodig heeft om de taak uit te voeren. Als u bijvoorbeeld niet wilt dat een persoon een beheerde schijf naar een opslag account kopieert, kunt u ervoor kiezen geen toegang te verlenen tot de export actie voor die beheerde schijf. En als u niet wilt dat een gebruiker een SAS-URI gebruikt om een beheerde schijf te kopiëren, kunt u ervoor kiezen deze machtiging niet toe te kennen aan de beheerde schijf.

### <a name="upload-your-vhd"></a>Uw VHD uploaden

 Direct uploaden maakt het eenvoudig om uw VHD over te zetten naar een door Azure beheerde schijf. Voorheen moest u een recenter proces volgen dat de fase ring van uw gegevens in een opslag account bevat. Er zijn nu minder stappen. Het is eenvoudiger om on-premises Vm's naar Azure te uploaden, te uploaden naar grote beheerde schijven en het back-up-en herstel proces is vereenvoudigd. Het vermindert ook de kosten omdat u rechtstreeks gegevens naar Managed disks kunt uploaden zonder ze aan Vm's toe te voegen. U kunt direct uploaden gebruiken om vhd's met een grootte van Maxi maal 32 TiB te uploaden.

 Zie de [cli](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) -of [Power shell](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md) -artikelen voor meer informatie over het overdragen van uw VHD naar Azure.

## <a name="encryption"></a>Versleuteling

Beheerde schijven bieden twee verschillende soorten versleuteling. De eerste is Storage Service Encryption (SSE), die wordt uitgevoerd door de opslag service. De tweede is Azure Disk Encryption (ADE), die u kunt inschakelen op het besturings systeem en de gegevens schijven voor uw virtuele machines.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

[Azure Storage-service versleuteling](../articles/storage/common/storage-service-encryption.md) biedt versleuteling-at-rest en beschermt uw gegevens om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. SSE is standaard ingeschakeld voor alle beheerde schijven, moment opnamen en installatie kopieën in alle regio's waar Managed disks beschikbaar zijn. Ga naar de [pagina met Managed disks Veelgestelde vragen](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) voor meer informatie.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Met Azure Disk Encryption kunt u het besturings systeem en de gegevens schijven versleutelen die worden gebruikt door een virtuele IaaS-machine. Deze versleuteling bevat beheerde schijven. Voor Windows worden de stations versleuteld met de standaard BitLocker-versleutelings technologie. Voor Linux worden de schijven versleuteld met de DM-cryptografie technologie. Het versleutelingsproces is geïntegreerd met Azure Key Vault zodat u de schijfversleutelingssleutels kunt controleren en beheren. Zie [Azure Disk Encryption voor IaaS vm's](../articles/security/azure-security-disk-encryption-overview.md)voor meer informatie.

## <a name="disk-roles"></a>Schijf rollen

Er zijn drie belang rijke schijf rollen in Azure: de gegevens schijf, de besturingssysteem schijf en de tijdelijke schijf. Deze rollen worden toegewezen aan schijven die zijn gekoppeld aan de virtuele machine.

![Schijf rollen in actie](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Gegevensschijf

Een gegevens schijf is een beheerde schijf die is gekoppeld aan een virtuele machine om toepassings gegevens op te slaan, of andere gegevens die u moet bewaren. Gegevens schijven worden geregistreerd als SCSI-stations en zijn voorzien van een door u gekozen letter. Elke gegevens schijf heeft een maximale capaciteit van 32.767 gibibytes (GiB). De grootte van de virtuele machine bepaalt hoeveel gegevens schijven u kunt koppelen en welk type opslag u kunt gebruiken om de schijven te hosten.

### <a name="os-disk"></a>Besturingssysteemschijf

Elke virtuele machine heeft een gekoppelde besturingssysteem schijf. De besturingssysteem schijf heeft een vooraf geïnstalleerd besturings systeem dat is geselecteerd toen de virtuele machine werd gemaakt. Deze schijf bevat het opstart volume.

Deze schijf heeft een maximale capaciteit van 2.048 GiB.

### <a name="temporary-disk"></a>Tijdelijke schijf

Elke VM bevat een tijdelijke schijf, die geen beheerde schijf is. De tijdelijke schijf biedt langdurige opslag voor toepassingen en processen en is bedoeld om alleen gegevens op te slaan, zoals pagina-of Wissel bestanden. Gegevens op de tijdelijke schijf kunnen verloren gaan tijdens een [onderhouds gebeurtenis](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) of wanneer u [een virtuele machine opnieuw implementeert](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Op virtuele machines van Azure Linux wordt de tijdelijke schijf standaard/dev/sdb en op Windows-Vm's de tijdelijke schijf is D: standaard. Tijdens een geslaagde standaard herstart van de virtuele machine blijven de gegevens op de tijdelijke schijf behouden.

## <a name="managed-disk-snapshots"></a>Moment opnamen van beheerde schijven

Een moment opname van een beheerde schijf is een alleen-lezen, crash consistente volledige kopie van een beheerde schijf die standaard wordt opgeslagen als standaard beheerde schijf. Met moment opnamen kunt u op elk moment een back-up maken van uw beheerde schijven. Deze moment opnamen bestaan onafhankelijk van de bron schijf en kunnen worden gebruikt om nieuwe beheerde schijven te maken. 

Moment opnamen worden gefactureerd op basis van de gebruikte grootte. Als u bijvoorbeeld een moment opname maakt van een beheerde schijf met een ingerichte capaciteit van 64 GiB en de daad werkelijke gebruikte gegevens grootte van 10 GiB, wordt die moment opname alleen gefactureerd voor de gebruikte gegevens grootte van 10 GiB. U kunt de gebruikte grootte van uw moment opnamen zien door het Azure- [gebruiks rapport](https://docs.microsoft.com/en-us/azure/billing/billing-understand-your-bill)te bekijken. Als de gebruikte gegevens grootte van een moment opname bijvoorbeeld 10 GiB is, wordt in het gebruiks rapport 10 GiB/(31 dagen x 24 uur) = 0,013441 GiB weer gegeven als de verbruikte hoeveelheid.

Raadpleeg de volgende bronnen voor meer informatie over het maken van moment opnamen met Managed disks:

* [Een kopie maken van een VHD die is opgeslagen als beheerde schijf met behulp van moment opnamen in Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Een kopie maken van een VHD die is opgeslagen als beheerde schijf met behulp van momentopnamen in Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Installatiekopieën

Managed disks biedt ook ondersteuning voor het maken van een beheerde aangepaste installatie kopie. U kunt een installatie kopie maken van uw aangepaste VHD in een opslag account of rechtstreeks vanuit een gegeneraliseerde (Sysprep) VM. Dit proces legt één installatie kopie vast. Deze installatie kopie bevat alle beheerde schijven die zijn gekoppeld aan een virtuele machine, inclusief het besturings systeem en de gegevens schijven. Met deze beheerde aangepaste installatie kopie kunt u honderden Vm's maken met behulp van uw aangepaste installatie kopie zonder dat u opslag accounts hoeft te kopiëren of te beheren.

Raadpleeg de volgende artikelen voor meer informatie over het maken van installatie kopieën:

* [Een beheerde installatie kopie van een gegeneraliseerde VM in azure vastleggen](../articles/virtual-machines/windows/capture-image-resource.md)
* [Een virtuele Linux-machine generaliseren en vastleggen met de Azure CLI](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Afbeeldingen versus moment opnamen

Het is belang rijk dat u begrijpt wat het verschil is tussen afbeeldingen en moment opnamen. Met Managed disks kunt u een installatie kopie maken van een gegeneraliseerde virtuele machine waarvan de toewijzing ongedaan is gemaakt. Deze installatie kopie bevat alle schijven die aan de virtuele machine zijn gekoppeld. U kunt deze installatie kopie gebruiken om een virtuele machine te maken en bevat alle schijven.

Een moment opname is een kopie van een schijf op het moment dat de moment opname wordt gemaakt. De eigenschap is alleen van toepassing op één schijf. Als u een virtuele machine hebt die één schijf (de besturingssysteem schijf) heeft, kunt u een moment opname of een installatie kopie maken en een virtuele machine van de moment opname of de installatie kopie.

Een moment opname heeft geen inzicht in de aanwezigheid van schijven, behalve de schijf die deze bevat. Dit maakt het problematisch om te gebruiken in scenario's die de coördinatie van meerdere schijven, zoals het verwijderen van een schijf, hebben. Moment opnamen moeten met elkaar kunnen worden gecoördineerd. dit wordt momenteel niet ondersteund.

## <a name="disk-allocation-and-performance"></a>Schijf toewijzing en prestaties

In het volgende diagram ziet u de realtime toewijzing van band breedte en IOPS voor schijven met behulp van een inrichtings systeem met drie niveaus:

![Inrichtings systeem met drie niveaus met band breedte en IOPS-toewijzing](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

De inrichting van het eerste niveau stelt het aantal IOPS per schijf en de bandbreedte toewijzing in.  Op het tweede niveau implementeert de Compute Server-host SSD-inrichting en past deze alleen toe op gegevens die zijn opgeslagen op de SSD van de server, inclusief schijven met caching (ReadWrite en ReadOnly), evenals lokale en tijdelijke schijven. Ten slotte vindt er een VM-netwerk inrichting plaats op het derde niveau voor elke I/O die de compute host verzendt naar de back-end van de Azure Storage. Met dit schema is de prestaties van een virtuele machine afhankelijk van verschillende factoren, van de manier waarop de virtuele machine gebruikmaakt van de lokale SSD, het aantal gekoppelde schijven, evenals de prestaties en het cache type van de schijven die zijn gekoppeld.

Als voor beeld van deze beperkingen kan een Standard_DS1v1-VM ertoe leiden dat het 5.000 IOPS-potentieel van een P30-schijf, ongeacht of deze in de cache is opgeslagen, door limieten op de SSD-en netwerk niveaus:

![Toewijzing van Standard_DS1v1-voor beeld](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Azure gebruikt netwerk kanaal met prioriteit voor schijf verkeer, dat de prioriteit krijgt boven een andere lage prioriteit van netwerk verkeer. Op die manier kunnen schijven hun verwachte prestaties behouden in het geval van netwerk conflicten. Op dezelfde manier worden resource-conflicten en andere problemen op de achtergrond met automatische taak verdeling door Azure Storage verwerkt. Azure Storage wijst de vereiste bronnen toe wanneer u een schijf maakt en past proactieve en reactieve verdeling van resources toe om het verkeers niveau af te handelen. Zo zorgt u ervoor dat schijven hun verwachte IOPS-en doorvoer doelen kunnen ondervangen. U kunt de metrische gegevens op het niveau van de virtuele machine en op schijf niveau gebruiken om de prestatie-en configuratie-meldingen naar behoefte bij te houden.

Raadpleeg ons [ontwerp voor high performance](../articles/virtual-machines/windows/premium-storage-performance.md) -artikel voor meer informatie over de aanbevolen procedures voor het optimaliseren van VM + schijf configuraties zodat u de gewenste prestaties kunt verzorgen

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de afzonderlijke schijf typen van Azure biedt, welk type geschikt is voor uw behoeften, en meer informatie over hun prestatie doelen in ons artikel op schijf typen.
