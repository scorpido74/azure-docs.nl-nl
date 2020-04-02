---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/31/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ad36547b0cf9457d7ff4fdd919a0bb1362539eb5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522720"
---
In dit artikel worden veelgestelde vragen beantwoord over Azure Managed Disks en Azure Premium SSD-schijven.

## <a name="managed-disks"></a>Beheerde schijven

**Wat zijn Azure Managed Disks?**

Managed Disks is een functie die schijfbeheer voor Azure IaaS VM's vereenvoudigt door opslagaccountbeheer voor u te beheren. Zie het overzicht [Beheerde schijven](../articles/virtual-machines/windows/managed-disks-overview.md)voor meer informatie .

**Als ik een standaard beheerde schijf van een bestaande VHD dat is 80 GB, hoeveel kost dat me?**

Een standaard beheerde schijf gemaakt van een 80-GB VHD wordt behandeld als de volgende beschikbare standaard schijfgrootte, dat is een S10-schijf. Er worden kosten in rekening gebracht volgens de S10-schijfprijzen. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/storage) voor meer informatie.

**Zijn er transactiekosten voor standaard beheerde schijven?**

Ja. Voor elke transactie worden kosten in rekening gebracht. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/storage) voor meer informatie.

**Voor een standaard beheerde schijf, wordt er kosten in rekening gebracht voor de werkelijke grootte van de gegevens op de schijf of voor de ingerichte capaciteit van de schijf?**

Er worden kosten in rekening gebracht op basis van de ingerichte capaciteit van de schijf. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/storage) voor meer informatie.

**Hoe verschilt de prijs van premium beheerde schijven van onbeheerde schijven?**

De prijzen van premium beheerde schijven is hetzelfde als onbeheerde premium schijven.

**Kan ik het type opslagaccount (Standaard of Premium) van mijn beheerde schijven wijzigen?**

Ja. U het type opslagaccount van uw beheerde schijven wijzigen met behulp van de Azure-portal, PowerShell of Azure CLI.

**Kan ik een VHD-bestand in een Azure-opslagaccount gebruiken om een beheerde schijf met een ander abonnement te maken?**

Ja.

**Kan ik een VHD-bestand in een Azure-opslagaccount gebruiken om een beheerde schijf in een andere regio te maken?**

Nee.

**Zijn er schaalbeperkingen voor klanten die beheerde schijven gebruiken?**

Beheerde schijven elimineert de limieten die zijn gekoppeld aan opslagaccounts. De maximale limiet is echter 50.000 beheerde schijven per regio en per schijftype voor een abonnement.

**Kunnen VM's in een beschikbaarheidsset bestaan uit een combinatie van beheerde en onbeheerde schijven?**

Nee. De VM's in een beschikbaarheidsset moeten alle beheerde schijven of alle niet-beheerde schijven gebruiken. Wanneer u een beschikbaarheidsset maakt, u kiezen welk type schijven u wilt gebruiken.

**Is Managed Disks de standaardoptie in de Azure-portal?**

Ja.

**Kan ik een lege beheerde schijf maken?**

Ja. U een lege schijf maken. Een beheerde schijf kan bijvoorbeeld onafhankelijk van een vm worden gemaakt zonder deze aan een vm te koppelen.

**Wat is het aantal ondersteunde foutdomeinen voor een beschikbaarheidsset die beheerde schijven gebruikt?**

Afhankelijk van het gebied waar de beschikbaarheidsset die beheerde schijven gebruikt zich bevindt, is het aantal ondersteunde foutdomeinen 2 of 3.

**Hoe wordt het standaardopslagaccount voor diagnostische gegevens ingesteld?**

U stelt een privéopslagaccount in voor VM-diagnoses.

**Wat voor functiegebaseerde toegangsbeheerondersteuning is beschikbaar voor beheerde schijven?**

Managed Disks ondersteunt drie belangrijke standaardrollen:

* Eigenaar: Kan alles beheren, inclusief toegang
* Bijdrager: kan alles beheren behalve toegang
* Lezer: kan alles bekijken, maar kan geen wijzigingen aanbrengen

**Is er een manier waarop ik een beheerde schijf kan kopiëren of exporteren naar een privé-opslagaccount?**

U een SAS-uri (read-only shared access signature) voor de beheerde schijf genereren en deze gebruiken om de inhoud te kopiëren naar een privé-opslagaccount of on-premises opslag. U de SAS URI gebruiken via de Azure-portal, Azure PowerShell, azure CLI of [AzCopy](../articles/storage/common/storage-use-azcopy.md)

**Kan ik een kopie van mijn beheerde schijf maken?**

Klanten kunnen een momentopname maken van hun beheerde schijven en vervolgens de momentopname gebruiken om een andere beheerde schijf te maken.

**Worden onbeheerde schijven nog steeds ondersteund?**

Ja, zowel onbeheerde als beheerde schijven worden ondersteund. We raden u aan beheerde schijven te gebruiken voor nieuwe workloads en uw huidige workloads te migreren naar beheerde schijven.

**Kan ik onbeheerde en beheerde schijven op dezelfde vm co-lokaliseren?**

Nee.

**Als ik een schijf van 128 GB maak en vervolgens de grootte vergroot tot 130 gibibytes (GiB), worden er dan kosten in rekening gebracht voor de volgende schijfgrootte (256 GiB)?**

Ja.

**Kan ik lokaal redundante opslag, georedundante opslag en zoneredundante opslagbeheerschijven maken?**

Azure Managed Disks ondersteunt momenteel alleen lokaal redundante opslagbeheerschijven.

**Kan ik mijn beheerde schijven verkleinen of verkleinen?**

Nee. Deze functie wordt momenteel niet ondersteund.

**Kan ik een lease-overeenkomst op mijn schijf verbreken?**

Nee. Dit wordt momenteel niet ondersteund omdat een lease aanwezig is om onbedoelde verwijdering te voorkomen wanneer de schijf wordt gebruikt.

**Kan ik de eigenschap computernaam wijzigen wanneer een gespecialiseerde (niet gemaakt met behulp van het systeemvoorbereidingsgereedschap of gegeneraliseerde) besturingssysteemschijf wordt gebruikt om een VM in te richten?**

Nee. U de eigenschap computernaam niet bijwerken. De nieuwe VM erft deze van de bovenliggende VM, die is gebruikt om de schijf van het besturingssysteem te maken. 

**Waar kan ik voorbeeld-Azure Resource Manager-sjablonen vinden om VM's met beheerde schijven te maken?**
* [Lijst met sjablonen met beheerde schijven](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Is er bij het maken van een schijf van een blob een voortdurend bestaande relatie met die bronblob?**

Nee, wanneer de nieuwe schijf wordt gemaakt is het een volledige standalone kopie van die blob op dat moment en er is geen verbinding tussen de twee. Als u wilt, kan de bronblob worden verwijderd nadat u de schijf hebt gemaakt, zonder dat dit gevolgen heeft voor de nieuw gemaakte schijf.

**Kan ik de naam van een beheerde of onbeheerde schijf wijzigen nadat deze is gemaakt?**

Voor beheerde schijven u ze niet hernoemen. U echter de naam van een niet-beheerde schijf wijzigen zolang deze momenteel niet is gekoppeld aan een VHD of VM.

**Kan ik GPT-partitionering gebruiken op een Azure Disk?**

Generatie 1-afbeeldingen kunnen alleen GPT-partitionering gebruiken op gegevensschijven, niet op OS-schijven. OS-schijven moeten de MBR-partitiestijl gebruiken.

[Generatie 2-afbeeldingen](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) kunnen GPT-partitionering gebruiken op de OS-schijf en de gegevensschijven.

**Welke schijftypen ondersteunen momentopnamen?**

Premium SSD, standaard SSD en standaard HDD ondersteunen snapshots. Voor deze drie schijftypen worden momentopnamen ondersteund voor alle schijfformaten (inclusief schijven tot 32 TiB-grootte). Ultra-schijven ondersteunen geen momentopnamen.

**Wat zijn Azure-schijfreserveringen?**
Schijfreservering is de optie om een jaar schijfopslag vooraf aan te schaffen, waardoor uw totale kosten worden verlaagd. Zie ons artikel over het onderwerp: [Hoe uw reserveringskorting wordt toegepast op Azure Disk voor](../articles/cost-management-billing/reservations/understand-disk-reservations.md)meer informatie over azure-schijfreserveringen.

**Welke opties biedt Azure-schijfreservering?**    
Azure-schijfreservering biedt de mogelijkheid om Premium-SSD's in de opgegeven SKU's te kopen van P30 (1 TiB) tot P80 (32 TiB) voor een termijn van één jaar. Er is geen beperking op het minimum aantal schijven dat nodig is om een schijfreservering aan te schaffen. Daarnaast u ervoor kiezen om te betalen met een enkele, vooruitbetaling of maandelijkse betalingen. Er worden geen extra transactiekosten toegepast voor Premium SSD Managed Disks.    

Reserveringen worden gemaakt in de vorm van schijven, niet capaciteit. Met andere woorden, wanneer u een P80 (32 TiB) schijf reserveert, krijgt u een enkele P80-schijf, u die specifieke reservering niet verdelen in twee kleinere P70 (16 TiB) schijven. U natuurlijk zoveel of zo weinig schijven reserveren als u wilt, waaronder twee afzonderlijke P70 (16 TiB) schijven.

**Hoe wordt Azure-schijfreservering toegepast?**    
Schijven reservering volgt een model vergelijkbaar met gereserveerde virtuele machine (VM) exemplaren. Het verschil is dat een schijfreservering niet kan worden toegepast op verschillende SKU's, terwijl een VM-instantie dat wel kan. Zie [Kosten besparen met Azure Reserved VM Instances](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) voor meer informatie over VM-exemplaren.     

**Kan ik mijn gegevensopslag gebruiken die is aangeschaft via azure-schijvenreservering in meerdere regio's?**    
Azure-schijvenreservering worden aangeschaft voor een specifieke regio en SKU (zoals P30 in Oost-VS 2) en kunnen daarom niet buiten deze constructies worden gebruikt. U altijd een extra Azure Disks-reservering aanschaffen voor uw schijfopslagbehoeften in andere regio's of SKU's.    

**Wat gebeurt er als de reservering van mijn Azure-schijven verloopt?**    
U ontvangt 30 dagen voor het verstrijken van de e-mailmeldingen en opnieuw op de vervaldatum. Zodra de reservering is verlopen, blijven geïmplementeerde schijven worden uitgevoerd en worden ze gefactureerd met de nieuwste [pay-as-you-go-tarieven.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="azure-shared-disks"></a>Gedeelde Azure-schijven

**Wordt de functie gedeelde schijven ondersteund voor onbeheerde schijven of paginablobs?**

Nee, het wordt alleen ondersteund voor premium SSD managed disks.

**Welke regio's ondersteunen gedeelde schijven?**

Momenteel alleen West Central US.

**Kunnen gedeelde schijven worden gebruikt als een OS-schijf?**

Nee, gedeelde schijven worden alleen ondersteund voor gegevensschijven.

**Welke schijfformaten ondersteunen gedeelde schijven?**

Alleen premium SSD's die P15 of meer ondersteuning bieden voor gedeelde schijven.

**Als ik een bestaande premium SSD heb, kan ik er gedeelde schijven op inschakelen?**

Alle beheerde schijven die zijn gemaakt met API-versie 2019-07-01 of hoger kunnen gedeelde schijven inschakelen. Om dit te doen, moet u de schijf loskoppelen van alle VM's waaraan deze is gekoppeld. Bewerk vervolgens `maxShares` de eigenschap op de schijf.

**Als ik een schijf niet meer in de gedeelde modus wil gebruiken, hoe schakel ik deze dan uit?**

De schijf loskoppelen van alle VM's waaraan deze is gekoppeld. Bewerk vervolgens de eigenschap maxShare op de schijf tot 1.

**u het formaat van een gedeelde schijf wijzigen?**

Ja.

**Kan ik schrijfversneller inschakelen op een schijf die ook gedeelde schijven heeft ingeschakeld?**

Nee.

**Kan ik hostcaching inschakelen voor een schijf waarvoor gedeelde schijf is ingeschakeld?**

De enige ondersteunde optie voor het plaatsen van hostis 'Geen'.

## <a name="ultra-disks"></a>Ultraschijven

**Waar moet ik mijn ultraschijfdoorvoer op instellen?**
Als u niet zeker weet op welke schijfdoorvoer u moet instellen, raden we u aan eerst een IO-grootte van 16 KiB aan te nemen en de prestaties vanaf daar aan te passen terwijl u uw toepassing controleert. De formule is: Doorvoer in MBps = # van IOPS * 16 / 1000.

**Ik configureerde mijn schijf tot 40000 IOPS, maar ik ben alleen het zien van 12800 IOPS, waarom zie ik niet de prestaties van de schijf?**
Naast de schijf gaspedaal, is er een IO gaspedaal dat wordt opgelegd op vm-niveau. Controleer of de VM-grootte die u gebruikt, de niveaus kan ondersteunen die op uw schijven zijn geconfigureerd. Zie [Grootte voor virtuele Windows-machines in Azure voor](../articles/virtual-machines/windows/sizes.md)meer informatie over IO-limieten die door uw VM zijn opgelegd.

**Kan ik cachingniveaus gebruiken met een ultraschijf?**
Nee, ultraschijven ondersteunen niet de verschillende cachingmethoden die worden ondersteund op andere schijftypen. Stel de schijfcache in op Geen.

**Kan ik een ultraschijf aan mijn bestaande VM koppelen?**
Misschien moet uw VM zich in een regio- en beschikbaarheidszonepaar bevinden dat Ultra-schijven ondersteunt. Zie [aan de slag met ultra schijven](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) voor meer informatie.

**Kan ik een ultraschijf gebruiken als de osschijf voor mijn VM?**
Nee, ultraschijven worden alleen ondersteund als gegevensschijven en worden alleen ondersteund als 4K-native schijven.

**Kan ik een bestaande schijf converteren naar een ultraschijf?**
Nee, maar u de gegevens migreren van een bestaande schijf naar een ultraschijf. Als u een bestaande schijf wilt migreren naar een ultraschijf, koppelt u beide schijven aan dezelfde virtuele machine en kopieert u de gegevens van de ene schijf naar de andere of maakt u gebruik van een oplossing van derden voor gegevensmigratie.

**Kan ik snapshots maken voor ultraschijven?**
Nee, snapshots zijn nog niet beschikbaar.

**Is Azure Backup beschikbaar voor ultraschijven?**
Nee, Azure Backup-ondersteuning is nog niet beschikbaar.

**Kan ik een ultraschijf koppelen aan een vm die wordt uitgevoerd in een beschikbaarheidsset?**
Nee, dit wordt nog niet ondersteund.

**Kan ik Azure Site Recovery voor VM's inschakelen met ultraschijven?**
Nee, Azure Site Recovery wordt nog niet ondersteund voor ultraschijven.

## <a name="uploading-to-a-managed-disk"></a>Uploaden naar een beheerde schijf

**Kan ik gegevens uploaden naar een bestaande beheerde schijf?**

Nee, uploaden kan alleen worden gebruikt tijdens het maken van een nieuwe lege schijf met de **status ReadyToUpload.**

**Hoe upload ik naar een beheerde schijf?**

Maak een beheerde schijf met de eigenschap [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) van [creationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) ingesteld op 'Uploaden', waarna u er gegevens naar uploaden.

**Kan ik een schijf aan een virtuele machine koppelen terwijl deze in een uploadstatus is?**

Nee.

**Kan ik een momentopname maken van een manged schijf in een uploadstatus?**

Nee.

## <a name="standard-ssd-disks"></a>Standaard SSD-schijven

**Wat zijn Azure Standard SSD-schijven?**
Standaard SSD-schijven zijn standaardschijven die worden ondersteund door solid-state media, geoptimaliseerd als kosteneffectieve opslag voor workloads die consistente prestaties op lagere IOPS-niveaus nodig hebben.

<a id="standard-ssds-azure-regions"></a>**Wat zijn de regio's die momenteel worden ondersteund voor standaard SSD-schijven?**
Alle Azure-regio's ondersteunen nu Standaard SSD-schijven.

**Is Azure Backup beschikbaar bij het gebruik van standaard-SSD's?**
Ja, Azure Backup is nu beschikbaar.

**Hoe maak ik standaard SSD-schijven?**
U standaard SSD-schijven maken met Azure Resource Manager-sjablonen, SDK, PowerShell of CLI. Hieronder staan de parameters die nodig zijn in de sjabloon Resourcemanager om standaard SSD-schijven te maken:

* *apiVersion* voor Microsoft.Compute moet `2018-04-01` worden ingesteld als (of later)
* *ManagedDisk.storageAccountType* opgeven als`StandardSSD_LRS`

In het volgende voorbeeld wordt de sectie *properties.storageProfile.osDisk* voor een VM weergegeven die standaard SSD-schijven gebruikt:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Zie [Een vm maken op basis van een Windows-afbeelding met standaard SSD-gegevensschijven](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)voor een volledig voorbeeld van het maken van een standaard SSD-schijf met een sjabloon.

**Kan ik mijn bestaande schijven converteren naar Standard SSD?**
Ja, dat is mogelijk. Raadpleeg [Azure managed disks storage converteren van standaard naar premium en vice versa](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) voor de algemene richtlijnen voor het converteren van beheerde schijven. En gebruik de volgende waarde om het schijftype bij te werken naar Standard SSD.
-AccountType StandardSSD_LRS

**Wat is het voordeel van het gebruik van standaard SSD-schijven in plaats van HDD?**
Standaard SSD-schijven bieden een betere latentie, consistentie, beschikbaarheid en betrouwbaarheid in vergelijking met HDD-schijven. Applicatie workloads draaien een stuk soepeler op Standard SSD als gevolg van dat. Premium SSD-schijven zijn de aanbevolen oplossing voor de meeste IO-intensieve productieworkloads.

**Kan ik standaard SSD's gebruiken als onbeheerde schijven?**
Nee, standaard SSD's schijven zijn alleen beschikbaar als beheerde schijven.

**Ondersteunen standaard SSD-schijven "VM SLA met één instantie"?**
Nee, standaard SSD's hebben geen VM SLA voor één instantie. Gebruik Premium SSD-schijven voor VM SLA met één exemplaar.

## <a name="migrate-to-managed-disks"></a>Migreren naar Managed Disks

**Is er een impact van migratie op de prestaties van Managed Disks?**

Migratie omvat het verplaatsen van de schijf van de ene opslaglocatie naar de andere. Dit wordt georkestreerd via een achtergrondkopie van gegevens, die enkele uren kan duren om te voltooien, meestal minder dan 24 uur, afhankelijk van de hoeveelheid gegevens in de schijven. Gedurende die tijd kan uw toepassing ervaring hoger dan de gebruikelijke leeslatentie als sommige leest kan krijgen doorgestuurd naar de oorspronkelijke locatie, en kan langer duren om te voltooien. Er is geen invloed op de schrijflatentie tijdens deze periode.  

**Welke wijzigingen zijn vereist in een reeds bestaande Azure Backup-serviceconfiguratie voorafgaand/na migratie naar Beheerde schijven?**

Er zijn geen wijzigingen vereist.

**Worden mijn VM-back-ups gemaakt via azure backup-service voordat de migratie blijft werken?**

Ja, back-ups werken naadloos.

**Welke wijzigingen zijn vereist in een reeds bestaande Azure Disks Encryption-configuratie voorafgaand/na migratie naar Beheerde schijven?**

Er zijn geen wijzigingen vereist.

**Wordt geautomatiseerde migratie van een bestaande virtuele machineschaal ingesteld van onbeheerde schijven naar beheerde schijven ondersteund?**

Nee. U een nieuwe schaalset maken met Beheerde schijven met behulp van de afbeelding van uw oude schaalset met niet-beheerde schijven.

**Kan ik een beheerde schijf maken op basis van een paginablobmomentopname die is gemaakt voordat ik naar Beheerde schijven migreer?**

Nee. U een paginablobmomentopname exporteren als een paginablob en vervolgens een beheerde schijf maken vanuit de geëxporteerde paginablob.

**Kan ik falen over mijn on-premises machines die zijn beschermd door Azure Site Recovery voor een vm met beheerde schijven?**

Ja, u ervoor kiezen om over te gaan naar een vm met beheerde schijven.

**Is er een impact van migratie op Azure VM's die zijn beschermd door Azure Site Recovery via Azure naar Azure-replicatie?**

Nee. Azure Site Recovery Azure to Azure protection for VM's with Managed Disks is available.

**Kan ik VM's migreren met niet-beheerde schijven die zich bevinden op opslagaccounts die eerder zijn of waren versleuteld naar beheerde schijven?**

Ja

## <a name="managed-disks-and-storage-service-encryption"></a>Versleuteling van beheerde schijven en opslagservice

**Is Azure Storage Service Encryption standaard ingeschakeld wanneer ik een beheerde schijf maak?**

Ja.

**Is het opstartvolume standaard versleuteld op een beheerde schijf?**

Ja. Standaard zijn alle beheerde schijven versleuteld, inclusief de OS-schijf.

**Wie beheert de encryptiesleutels?**

Microsoft beheert de versleutelingssleutels.

**Kan ik de versleuteling van de opslagservice uitschakelen voor mijn beheerde schijven?**

Nee.

**Is Storage Service Encryption alleen beschikbaar in specifieke regio's?**

Nee. Het is beschikbaar in alle regio's waar beheerde schijven beschikbaar zijn. Beheerde schijven zijn beschikbaar in alle openbare regio's en Duitsland. Het is ook beschikbaar in China, echter, alleen voor Microsoft beheerde sleutels, niet door de klant beheerde sleutels.

**Hoe kan ik erachter komen of mijn beheerde schijf is versleuteld?**

U de tijd vinden waarop een beheerde schijf is gemaakt via de Azure-portal, de Azure CLI en PowerShell. Als de tijd na 9 juni 2017 is, is uw schijf versleuteld.

**Hoe kan ik mijn bestaande schijven versleutelen die vóór 10 juni 2017 zijn gemaakt?**

Vanaf 10 juni 2017 worden nieuwe gegevens die naar bestaande beheerde schijven zijn geschreven, automatisch versleuteld. We zijn ook van plan om bestaande gegevens te versleutelen, en de encryptie zal asynchroon gebeuren op de achtergrond. Als u bestaande gegevens nu moet versleutelen, maakt u een kopie van uw schijf. Nieuwe schijven worden versleuteld.

* [Beheerde schijven kopiëren met de Azure CLI](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Beheerde schijven kopiëren met PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**Zijn beheerde snapshots en afbeeldingen versleuteld?**

Ja. Alle beheerde snapshots en afbeeldingen die na 9 juni 2017 zijn gemaakt, worden automatisch versleuteld. 

**Kan ik VM's converteren met niet-beheerde schijven die zich bevinden op opslagaccounts die eerder zijn of waren versleuteld naar beheerde schijven?**

Ja

**Wordt een geëxporteerde VHD vanaf een beheerde schijf of een momentopname ook versleuteld?**

Nee. Maar als u een VHD exporteert naar een versleutelde opslagaccount vanaf een versleutelde beheerde schijf of momentopname, dan is deze versleuteld. 

## <a name="premium-disks-managed-and-unmanaged"></a>Premium schijven: beheerd en onbeheerd

**Als een VM een grootteserie gebruikt die Premium SSD-schijven ondersteunt, zoals een DSv2, kan ik dan zowel premium- als standaardgegevensschijven toevoegen?** 

Ja.

**Kan ik zowel premium- als standaardgegevensschijven koppelen aan een serie grootte die geen Premium SSD-schijven ondersteunt, zoals D-, Dv2-, G- of F-serie?**

Nee. U alleen standaardgegevensschijven koppelen aan VM's die geen groottereeks gebruiken die Premium SSD-schijven ondersteunt.

**Als ik een premium data schijf van een bestaande VHD dat was 80 GB, hoeveel kost dat?**

Een premium dataschijf gemaakt van een 80 GB VHD wordt behandeld als de volgende beschikbare premium schijfgrootte, dat is een P10-schijf. Er worden kosten in rekening gebracht volgens de P10-schijfprijs.

**Zijn er transactiekosten voor het gebruik van Premium SSD schijven?**

Er is een vaste kosten voor elke schijfgrootte, die wordt geleverd voorzien van specifieke limieten voor IOPS en doorvoer. De overige kosten zijn uitgaande bandbreedte en momentopnamecapaciteit, indien van toepassing. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/storage) voor meer informatie.

**Wat zijn de limieten voor IOPS en doorvoer die ik kan krijgen uit de schijfcache?**

De gecombineerde limieten voor cache en lokale SSD voor een DS-serie zijn 4.000 IOPS per core en 33 MiB per seconde per core. De GS-serie biedt 5.000 IOPS per core en 50 MiB per seconde per core.

**Wordt de lokale SSD ondersteund voor een VM voor beheerde schijven?**

De lokale SSD is tijdelijke opslag die is opgenomen met een VM Managed Disks. Er zijn geen extra kosten voor deze tijdelijke opslag. We raden u aan deze lokale SSD niet te gebruiken om uw toepassingsgegevens op te slaan omdat deze niet worden gehandhaafd in Azure Blob-opslag.

**Zijn er gevolgen voor het gebruik van TRIM op premium schijven?**

Er is geen nadeel aan het gebruik van TRIM op Azure-schijven op premium of standaard schijven.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nieuwe schijfformaten: beheerd en onbeheerd

**Welke regio's ondersteunen bursting mogelijkheden voor de toepasselijke premium SSD schijfgrootte?**

De bursting-mogelijkheid wordt momenteel ondersteund in alle regio's in Azure Public Cloud, met ondersteuning voor soevereine clouds die binnenkort beschikbaar zijn. 

**In welke regio's worden 4/8/16 GiB Managed Disk-formaten (P1/P2/P3, E1/E2/E3) ondersteund?**

Deze nieuwe schijfformaten worden momenteel ondersteund in alle regio's in Azure Public Cloud, met ondersteuning voor soevereine clouds die binnenkort beschikbaar zijn. 

**Worden P1/P2/P3-schijfformaten ondersteund voor onbeheerde schijven of paginablobs?**

Nee, het wordt alleen ondersteund op premium SSD managed disks. 

**Worden E1/E2/E3-schijfformaten ondersteund voor onbeheerde schijven of paginablobs?**

Nee, standaard SSD-beheerde schijven van elke grootte kunnen niet worden gebruikt met onbeheerde schijven of paginablobs.

**Wat is de grootste beheerde schijfgrootte die wordt ondersteund voor besturingssysteem- en gegevensschijven?**

Het partitietype dat Azure ondersteunt voor een schijf van het besturingssysteem, is de master boot record (MBR). De MBR-indeling ondersteunt een schijfgrootte tot 2 TiB. Het grootste formaat dat Azure ondersteunt voor een schijf van het besturingssysteem is 2 TiB. Azure ondersteunt maximaal 32 TiB voor beheerde gegevensschijven.

**Wat is de grootste onbeheerde schijfgrootte die wordt ondersteund voor besturingssysteem- en gegevensschijven?**

Het partitietype dat Azure ondersteunt voor een schijf van het besturingssysteem, is de master boot record (MBR). De MBR-indeling ondersteunt een schijfgrootte tot 2 TiB. Het grootste formaat dat Azure ondersteunt voor een besturingssysteem Onbeheerde schijf is 2 TiB. Azure ondersteunt maximaal 4 TiB voor gegevens Onbeheerde schijven.

**Wat is de grootste paginablobgrootte die wordt ondersteund?**

De grootste paginablobgrootte die Azure ondersteunt, is 8 TiB (8.191 GiB). De maximale paginablobgrootte wanneer deze aan een VM is gekoppeld als gegevens- of besturingssysteemschijven, is 4 TiB (4.095 GiB).

**Moet ik een nieuwe versie van Azure-hulpprogramma's gebruiken om schijven van groter dan 1 TiB te maken, te koppelen, te vergroten en te uploaden?**

U hoeft uw bestaande Azure-hulpprogramma's niet te upgraden om schijven groter dan 1 TiB te maken, toe te voegen of te wijzigen. Als u uw VHD-bestand rechtstreeks naar Azure wilt uploaden als een paginablob of onbeheerde schijf, moet u de nieuwste toolsets hieronder gebruiken. We ondersteunen alleen VHD-uploads tot 8 TiB.

|Azure-hulpprogramma's      | Ondersteunde versies                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Versienummer 4.1.0: release juni 2017 of hoger|
|Azure CLI v1     | Versienummer 0.10.13: release mei 2017 of hoger|
|Azure CLI v2     | Versienummer 2.0.12: release juli 2017 of hoger|
|AzCopy              | Versienummer 6.1.0: release juni 2017 of hoger|

**Worden P4- en P6-schijfformaten ondersteund voor onbeheerde schijven of paginablobs?**

P4 -schijfformaten (32 GiB) en P6 (64 GiB) worden niet ondersteund als de standaardschijflagen voor onbeheerde schijven en paginablobs. U moet [de Blob-laag](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) expliciet instellen op P4 en P6 om uw schijf aan deze lagen in kaart te brengen. Als u een onbeheerde schijf- of paginablob implementeert met de schijfgrootte of inhoudslengte van minder dan 32 GiB of tussen 32 GiB tot 64 GiB zonder de Blob-laag in te stellen, blijft u landen op P10 met 500 IOPS en 100 MiB/s en de toegewezen prijscategorie.

**Als mijn bestaande premium beheerde schijf minder dan 64 GiB is gemaakt voordat de kleine schijf is ingeschakeld (rond 15 juni 2017), hoe wordt deze gefactureerd?**

Bestaande kleine premium schijven minder dan 64 GiB worden nog steeds gefactureerd volgens de P10-prijscategorie.

**Hoe kan ik de schijflaag van kleine premium schijven van minder dan 64 GiB van P10 naar P4 of P6?**

U een momentopname van uw kleine schijven maken en vervolgens een schijf maken om de prijslaag automatisch over te schakelen naar P4 of P6 op basis van de ingerichte grootte.

**u het formaat van bestaande beheerde schijven wijzigen van formaten van minder dan 4 tebibytes (TiB) tot nieuwe nieuw geïntroduceerde schijfformaten tot 32 TiB?**

Ja.

**Wat zijn de grootste schijfformaten die worden ondersteund door azure backup- en Azure Site Recovery-service?**

De grootste schijfgrootte die wordt ondersteund door Azure Backup is 32 TiB (4 TiB voor versleutelde schijven). De grootste schijfgrootte die wordt ondersteund door Azure Site Recovery is 8 TiB. Ondersteuning voor de grotere schijven tot 32 TiB is nog niet beschikbaar in Azure Site Recovery.

**Wat zijn de aanbevolen VM-formaten voor grotere schijfformaten (>4 TiB) voor standaard SSD- en standaard hdd-schijven om geoptimaliseerde schijf-IOPS en -bandbreedte te bereiken?**

Om de schijfdoorvoer van standaard SSD en Standard HDD grote schijfformaten (>4 TiB) te bereiken, meer dan 500 IOPS en 60 MiB/s, we raden u aan een nieuwe VM uit een van de volgende VM-formaten in te zetten om uw prestaties te optimaliseren: B-serie, DSv2-serie, Dsv3-serie, ESv3-serie, Fs-serie, Fsv2-serie, M-serie, GS-serie, NCv2-serie, NCv3-serie of Ls-serie VM's. Het koppelen van grote schijven aan bestaande VM's of VM's die de bovenstaande aanbevolen formaten niet gebruiken, kunnen lagere prestaties ervaren.

**Hoe kan ik mijn schijven upgraden (>4 TiB) die werden ingezet tijdens de grotere schijfformaten preview om de hogere IOPS & bandbreedte te krijgen bij GA?**

U de VM die de schijf is aangesloten op de schijf stoppen en starten of uw schijf weer losmaken en opnieuw bevestigen. De prestatiedoelen van grotere schijfformaten zijn verhoogd voor zowel premium SD's als standaard SSD's bij GA.

**In welke regio's worden de beheerde schijfformaten van 8 TiB, 16 TiB en 32 TiB ondersteund?**

De 8 TiB-, 16 TiB- en 32 TiB-disk SKU's worden ondersteund in alle regio's onder wereldwijde Azure, Microsoft Azure Government en Azure China 21Vianet.

**Ondersteunen we het inschakelen van Host Caching op alle schijfformaten?**

Wij ondersteunen Host Caching van ReadOnly en Read/Write op schijfformaten van minder dan 4 TiB. Voor schijfformaten van meer dan 4 TiB ondersteunen we geen andere insteloptie dan Geen. We raden u aan caching te gebruiken voor kleinere schijfformaten, waar u een betere prestatieverbetering verwachten met gegevens die in de cache zijn opgeslagen in de VM.

## <a name="what-if-my-question-isnt-answered-here"></a>Wat als mijn vraag hier niet wordt beantwoord?

Als uw vraag hier niet wordt vermeld, laat het ons weten en wij helpen u een antwoord te vinden. U een vraag aan het eind van dit artikel in de commentaren posten. Gebruik het MSDN [Azure Storage-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)om contact te leggen met het Azure Storage-team en andere communityleden over dit artikel.

Als u functies wilt aanvragen, dient u uw verzoeken en ideeën in bij het [feedbackforum azure storage.](https://feedback.azure.com/forums/217298-storage)
