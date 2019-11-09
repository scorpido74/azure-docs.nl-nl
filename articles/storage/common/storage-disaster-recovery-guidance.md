---
title: Herstel na nood gevallen en failover van het opslag account (preview)-Azure Storage
description: Azure Storage ondersteunt de failover van een account (preview) voor geografisch redundante opslag accounts. Met account-failover kunt u het failover-proces voor uw opslag account initiëren als het primaire eind punt niet beschikbaar is.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b265ff8831275a9f4b84f7dac28b82ae75630f8b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889789"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Herstel na nood gevallen en failover van het opslag account (preview) in Azure Storage

Micro soft streeft ernaar om ervoor te zorgen dat Azure-Services altijd beschikbaar zijn. Er kunnen echter niet-geplande service storingen optreden. Als uw toepassing tolerantie vereist, raadt micro soft aan geografisch redundante opslag te gebruiken, zodat uw gegevens in een tweede regio worden gerepliceerd. Daarnaast moeten klanten een nood herstel plan hebben voor het verwerken van een regionale service storing. Een belang rijk onderdeel van een plan voor herstel na nood gevallen is bezig met het voorbereiden van een failover naar het secundaire eind punt in het geval dat het primaire eind punt niet beschikbaar is. 

Azure Storage ondersteunt de failover van een account (preview) voor geografisch redundante opslag accounts. Met account-failover kunt u het failover-proces voor uw opslag account initiëren als het primaire eind punt niet beschikbaar is. De failover werkt het secundaire eind punt bij om het primaire eind punt voor uw opslag account te worden. Zodra de failover is voltooid, kunnen clients naar het nieuwe primaire eind punt gaan schrijven.

In dit artikel worden de concepten en het proces van een failover van een account beschreven en wordt uitgelegd hoe u uw opslag account voorbereidt voor herstel met de minste gevolgen voor de klant. Zie [een account-failover initiëren (preview)](storage-initiate-account-failover.md)voor meer informatie over het initiëren van een account-failover in de Azure portal of Power shell.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>De juiste redundantie optie kiezen

Alle opslag accounts worden gerepliceerd voor redundantie. Welke redundantie optie u kiest voor uw account, is afhankelijk van de mate van tolerantie die u nodig hebt. Voor beveiliging tegen regionale storingen kiest u geografisch redundante opslag, met of zonder de optie Lees toegang vanuit de secundaire regio:  

Met **geografisch redundante opslag (GRS)** worden uw gegevens asynchroon gerepliceerd in twee geografische regio's die minstens honderden kilo meters uit elkaar liggen. Als de primaire regio een storing ondergaat, fungeert de secundaire regio als een redundante bron voor uw gegevens. U kunt een failover initiëren om het secundaire eind punt te transformeren naar het primaire eind punt.

**Geografisch redundante opslag met lees toegang (RA-GRS)** voorziet in geografisch redundante opslag met het extra voor deel van lees toegang tot het secundaire eind punt. Als er een storing optreedt in het primaire eind punt, kunnen toepassingen die zijn geconfigureerd voor RA-GRS en ontworpen voor maximale Beschik baarheid, blijven lezen van het secundaire eind punt. Micro soft raadt RA-GRS aan voor maximale tolerantie voor uw toepassingen.

Andere Azure Storage redundantie opties zijn onder meer zone-redundante opslag (ZRS), waarmee uw gegevens worden gerepliceerd in verschillende beschikbaarheids zones in één regio, en lokaal redundante opslag (LRS), waarmee uw gegevens in één Data Center in één regio worden gerepliceerd. Als uw opslag account is geconfigureerd voor ZRS of LRS, kunt u dat account converteren om GRS of RA-GRS te gebruiken. Als u uw account configureert voor geo-redundante opslag, worden extra kosten in rekening gebracht. Zie [Azure storage-replicatie](storage-redundancy.md)voor meer informatie.

> [!NOTE]
> Geo-zone-redundante opslag (GZRS) en geo-zone-redundante opslag met lees toegang (RA-GZRS) zijn momenteel beschikbaar als preview-versie, maar zijn nog niet in dezelfde regio's als de door de klant beheerde account-failover. Daarom kunnen klanten momenteel geen account-failover-gebeurtenissen beheren met GZRS-en RA-GZRS-accounts. Tijdens de preview beheert micro soft failover-gebeurtenissen die van invloed zijn op GZRS/RA-GZRS-accounts.

> [!WARNING]
> Geografisch redundante opslag heeft een risico op gegevens verlies. Gegevens worden asynchroon gerepliceerd naar de secundaire regio, wat betekent dat er een vertraging is tussen het moment dat de gegevens die naar de primaire regio worden geschreven naar de secundaire regio worden geschreven. In het geval van een storing, gaan schrijf bewerkingen naar het primaire eind punt die nog niet zijn gerepliceerd naar het secundaire eind punt, verloren.

## <a name="design-for-high-availability"></a>Ontwerpen voor hoge beschikbaarheid

Het is belang rijk om uw toepassing te ontwerpen voor hoge Beschik baarheid vanaf het begin. Raadpleeg deze Azure-bronnen voor hulp bij het ontwerpen van uw toepassing en het plannen van nood herstel:

* [Flexibele toepassingen ontwerpen voor Azure](/azure/architecture/checklist/resiliency-per-service): een overzicht van de belangrijkste concepten voor het ontwikkelen van Maxi maal beschik bare toepassingen in Azure.
* [Beschikbaarheids controlelijst](/azure/architecture/checklist/resiliency-per-service): een controle lijst voor het controleren of uw toepassing de aanbevolen ontwerp procedures voor hoge Beschik baarheid implementeert.
* [Ontwerpen van Maxi maal beschik bare toepassingen met Ra-GRS](storage-designing-ha-apps-with-ragrs.md): ontwerp richt lijnen voor het ontwikkelen van toepassingen om te profiteren van Ra-GRS.
* [Zelf studie: een Maxi maal beschik bare toepassing bouwen met Blob-opslag](../blobs/storage-create-geo-redundant-storage.md): een zelf studie waarin wordt uitgelegd hoe u een Maxi maal beschik bare toepassing bouwt die automatisch schakelt tussen eind punten als storingen en herstel bewerkingen worden gesimuleerd. 

Houd bovendien de volgende aanbevolen procedures voor het onderhouden van hoge Beschik baarheid voor uw Azure Storage gegevens:

* **Schijven:** Gebruik [Azure backup](https://azure.microsoft.com/services/backup/) om een back-up te maken van de VM-schijven die worden gebruikt door uw virtuele Azure-machines. U kunt ook [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) gebruiken om uw vm's te beschermen in het geval van een regionale nood situatie.
* **Blok-blobs:** Schakel [zacht verwijderen](../blobs/storage-blob-soft-delete.md) in om te beschermen tegen verwijderingen op object niveau en overschrijvingen, of kopieer blok-blobs naar een ander opslag account in een andere regio met behulp van [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)of de [Azure data verplaatsings bibliotheek](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Bestanden:** Gebruik [AzCopy](storage-use-azcopy.md) of [Azure PowerShell](storage-powershell-guide-full.md) om uw bestanden te kopiëren naar een ander opslag account in een andere regio.
* **Tabellen:** gebruik [AzCopy](storage-use-azcopy.md) om tabel gegevens te exporteren naar een ander opslag account in een andere regio.

## <a name="track-outages"></a>Uitval bijhouden

Klanten kunnen zich abonneren op het [Azure service Health-dash board](https://azure.microsoft.com/status/) om de status en status van Azure Storage en andere Azure-Services bij te houden.

Micro soft raadt u ook aan uw toepassing te ontwerpen om de mogelijkheid van schrijf fouten voor te bereiden. Uw toepassing moet schrijf fouten op een manier weer geven waarmee u wordt gewaarschuwd als er een storing optreedt in de primaire regio.

## <a name="understand-the-account-failover-process"></a>Meer informatie over het proces van failover van accounts

Met een door de klant beheerde account-failover (preview) kunt u uw hele opslag account laten mislukken tot de secundaire regio als de primaire om een of andere reden niet beschikbaar is. Wanneer u een failover naar de secundaire regio afdwingt, kunnen clients beginnen met het schrijven van gegevens naar het secundaire eind punt nadat de failover is voltooid. De failover duurt doorgaans ongeveer een uur.

### <a name="how-an-account-failover-works"></a>Hoe een account-failover werkt

Onder normale omstandigheden schrijft een client gegevens naar een Azure Storage account in de primaire regio en worden gegevens asynchroon gerepliceerd naar de secundaire regio. In de volgende afbeelding ziet u het scenario wanneer de primaire regio beschikbaar is:

![Clients schrijven gegevens naar het opslag account in de primaire regio](media/storage-disaster-recovery-guidance/primary-available.png)

Als het primaire eind punt om de een of andere reden niet beschikbaar is, kan de client niet meer schrijven naar het opslag account. De volgende afbeelding toont het scenario waarin de primaire onbeschikbaar is geworden, maar er is nog geen herstel plaatsgevonden:

![De primaire is niet beschikbaar, zodat clients geen gegevens kunnen schrijven](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

De klant initieert de account-failover naar het secundaire eind punt. Met het failoverproces wordt de DNS-vermelding die is opgegeven door Azure Storage, zodanig bijgewerkt dat het secundaire eind punt het nieuwe primaire eind punt wordt voor uw opslag account, zoals wordt weer gegeven in de volgende afbeelding:

![Klant initieert een failover van een account naar een secundair eind punt](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Schrijf toegang wordt hersteld voor GRS-en RA-GRS-accounts nadat de DNS-vermelding is bijgewerkt en er aanvragen worden doorgestuurd naar het nieuwe primaire eind punt. Bestaande opslag service-eind punten voor blobs, tabellen, wacht rijen en bestanden blijven hetzelfde na de failover.

> [!IMPORTANT]
> Nadat de failover is voltooid, is het opslag account geconfigureerd om lokaal redundant te zijn in het nieuwe primaire eind punt. Als u de replicatie naar de nieuwe secundaire wilt hervatten, configureert u het account voor het opnieuw gebruiken van geo-redundante opslag (RA-GRS of GRS).
>
> Houd er rekening mee dat het converteren van een LRS-account naar RA-GRS of GRS een kosten kost. Deze kosten zijn van toepassing op het bijwerken van het opslag account in de nieuwe primaire regio voor het gebruik van RA-GRS of GRS na een failover.  

### <a name="anticipate-data-loss"></a>Verwachte gegevens verlies

> [!CAUTION]
> Voor een failover van een account is doorgaans een verlies van gegevens vereist. Het is belang rijk om inzicht te krijgen in de gevolgen van het initiëren van een account-failover.  

Omdat gegevens asynchroon van de primaire regio naar de secundaire regio worden geschreven, is er altijd een vertraging voordat een schrijf bewerking naar de primaire regio wordt gerepliceerd naar de secundaire regio. Als de primaire regio niet beschikbaar is, zijn de meest recente schrijf bewerkingen mogelijk nog niet gerepliceerd naar de secundaire regio.

Wanneer u een failover afdwingt, gaan alle gegevens in de primaire regio verloren, omdat de secundaire regio de nieuwe primaire regio wordt en het opslag account is geconfigureerd om lokaal redundant te zijn. Alle gegevens die al naar het secundaire repliceren, worden behouden wanneer de failover plaatsvindt. Alle gegevens die naar de primaire zijn geschreven en die niet ook zijn gerepliceerd naar de secundaire, gaan echter permanent verloren. 

De eigenschap **laatste synchronisatie tijd** geeft de meest recente tijd aan dat de gegevens van de primaire regio gegarandeerd naar de secundaire regio zijn geschreven. Alle gegevens die vóór de laatste synchronisatie tijd zijn geschreven, zijn beschikbaar op de secundaire, terwijl gegevens die zijn geschreven na de laatste synchronisatie tijd mogelijk niet naar het secundaire apparaat zijn geschreven en mogelijk verloren gaan. Gebruik deze eigenschap in het geval van een storing om een schatting te maken van de hoeveelheid gegevens verlies die kan ontstaan door het initiëren van een account-failover. 

Ontwerp uw toepassing als best practice, zodat u de laatste synchronisatie tijd kunt gebruiken om het verwachte verlies van gegevens te evalueren. Als u bijvoorbeeld alle schrijf bewerkingen registreert, kunt u de tijd van uw laatste schrijf bewerkingen vergelijken met de tijd van de laatste synchronisatie om te bepalen welke geschreven gegevens niet zijn gesynchroniseerd met de secundaire.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Wees voorzichtig bij het uitvoeren van een failback naar de oorspronkelijke primaire

Nadat u een failover van de primaire naar de secundaire regio hebt uitgevoerd, is uw opslag account geconfigureerd om lokaal te worden redundant in de nieuwe primaire regio. U kunt het account voor geo-redundantie opnieuw configureren door het bij te werken voor gebruik van GRS of RA-GRS. Wanneer het account na een failover eenmaal is geconfigureerd voor geo-redundantie, begint de nieuwe primaire regio onmiddellijk met het repliceren van gegevens naar de nieuwe secundaire regio, wat het belangrijkste was vóór de oorspronkelijke failover. Het kan echter een periode duren voordat de bestaande gegevens in de primaire replica volledig zijn gerepliceerd naar de nieuwe secundaire.

Nadat het opslag account voor geo-redundantie opnieuw is geconfigureerd, is het mogelijk om een andere failover van de nieuwe primaire back-up naar de nieuwe secundaire te initiëren. In dit geval wordt de oorspronkelijke primaire regio voorafgaand aan de failover opnieuw de primaire regio en is deze geconfigureerd om lokaal redundant te zijn. Alle gegevens in de primaire regio na de failover (de oorspronkelijke secundaire) zijn dan verloren gegaan. Als het meren deel van de gegevens in het opslag account niet naar de nieuwe secundaire is gerepliceerd voordat u een failback hebt uitgevoerd, kan dit een groot verlies van gegevens opleveren. 

Als u een groot gegevens verlies wilt voor komen, controleert u de waarde van de eigenschap **laatste synchronisatie tijd** voordat u een failback hebt uitgevoerd. Vergelijk de tijd van de laatste synchronisatie met de laatste keer dat gegevens naar de nieuwe primaire zijn geschreven om het verwachte verlies van gegevens te evalueren. 

## <a name="initiate-an-account-failover"></a>Een failover van account initiëren

U kunt een failover van een account initiëren vanuit de Azure Portal, Power shell, Azure CLI of de Azure Storage Resource provider-API. Zie [een account-failover initiëren (preview)](storage-initiate-account-failover.md)voor meer informatie over het initiëren van een failover.

## <a name="about-the-preview"></a>Over de preview-versie

Account-failover is beschikbaar als preview-versie voor alle klanten die gebruikmaken van GRS of RA-GRS met Azure Resource Manager-implementaties. Algemeen gebruik v1, algemeen v2 en Blob Storage-account typen worden ondersteund. de failover van het account is momenteel beschikbaar in deze regio's:

- Azië - oost
- Azië - zuidoost
- Australië - oost
- Australië - zuidoost
- US - centraal
- US - oost 2
- US - west-centraal
- US - west 2

Het voor beeld is alleen bedoeld voor niet-productie gebruik. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.

### <a name="register-for-the-preview"></a>Registreren voor de preview-versie

Voer de volgende opdrachten uit in Power shell om u te registreren voor de preview-versie. Zorg ervoor dat u de tijdelijke aanduiding tussen vier Kante haken vervangt door uw eigen abonnements-ID:

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Het kan 1-2 dagen duren voordat goed keuring voor de preview wordt ontvangen. Voer de volgende opdracht uit om te controleren of uw registratie is goedgekeurd:

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Aanvullende overwegingen 

Bekijk de aanvullende overwegingen die in deze sectie worden beschreven om te begrijpen hoe uw toepassingen en services kunnen worden beïnvloed wanneer u een failover tijdens de preview-periode afdwingt.

#### <a name="azure-virtual-machines"></a>Virtuele machines van Azure

Virtuele Azure-machines (Vm's) mislukken niet als onderdeel van een account-failover. Als de primaire regio niet beschikbaar is en u een failover naar de secundaire regio wilt uitvoeren, moet u de virtuele machines na de failover opnieuw maken. 

#### <a name="azure-unmanaged-disks"></a>Niet-beheerde Azure-schijven

Als best practice, raadt micro soft aan om niet-beheerde schijven te converteren naar beheerde schijven. Als u echter een failover wilt uitvoeren voor een account met niet-beheerde schijven die zijn gekoppeld aan virtuele Azure-machines, moet u de virtuele machine afsluiten voordat de failover wordt gestart.

Niet-beheerde schijven worden opgeslagen als pagina-blobs in Azure Storage. Wanneer een virtuele machine wordt uitgevoerd in azure, worden alle niet-beheerde schijven die aan de virtuele machine zijn gekoppeld, geleasd. Een account-failover kan niet door gaan als er een lease is op een blob. Voer de volgende stappen uit om de failover uit te voeren:

1. Voordat u begint, moet u rekening houden met de namen van alle onbeheerde schijven, hun logische eenheids nummers (LUN) en de virtuele machine waaraan deze zijn gekoppeld. Als u dit doet, is het eenvoudiger om de schijven na de failover opnieuw te koppelen. 
2. Sluit de virtuele machine af.
3. Verwijder de virtuele machine, maar behoud de VHD-bestanden voor de niet-beheerde schijven. Let op het tijdstip waarop u de virtuele machine hebt verwijderd.
4. Wacht tot de **laatste synchronisatie tijd** is bijgewerkt en later is dan het tijdstip waarop u de virtuele machine hebt verwijderd. Deze stap is belang rijk, omdat als het secundaire eind punt niet volledig is bijgewerkt met de VHD-bestanden wanneer de failover plaatsvindt, de virtuele machine mogelijk niet goed werkt in de nieuwe primaire regio.
5. Start de account-failover.
6. Wacht tot de account-failover is voltooid en de secundaire regio de nieuwe primaire regio geworden.
7. Maak een virtuele machine in de nieuwe primaire regio en koppel de Vhd's opnieuw.
8. Start de nieuwe VM.

Houd er wel bij dat alle gegevens die zijn opgeslagen op een tijdelijke schijf verloren gaan wanneer de virtuele machine wordt afgesloten.

### <a name="unsupported-features-or-services"></a>Niet-ondersteunde functies of services
De volgende functies of services worden niet ondersteund voor account-failover voor de preview-versie:

- Azure File Sync biedt geen ondersteuning voor de failover van het opslag account. Er mag geen failover worden uitgevoerd voor opslag accounts met Azure-bestands shares die worden gebruikt als Cloud-eind punten in Azure File Sync. Als u dat wel doet, werkt de synchronisatie niet meer en kan dit leiden tot onverwachte gegevens verlies in het geval van nieuwe gelaagde bestanden.  
- Er kan geen failover worden uitgevoerd voor een opslag account met gearchiveerde blobs. Bewaar gearchiveerde blobs in een afzonderlijk opslag account waarvan u niet van plan bent een failover uit te voeren.
- Er kan geen failover worden uitgevoerd voor een opslag account met Premium-blok-blobs. Opslag accounts die ondersteuning bieden voor Premium-blok-blobs ondersteunen momenteel geen geo-redundantie.
- Nadat de failover is voltooid, zullen de volgende functies niet meer werken als deze is ingeschakeld: [gebeurtenis abonnementen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), [levenscyclus beleid](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) [Opslaganalyse logboek registratie](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Gegevens kopiëren als alternatief voor failover

Als uw opslag account is geconfigureerd voor RA-GRS, hebt u lees toegang tot uw gegevens via het secundaire eind punt. Als u de voor keur geeft aan een failover in het geval van een storing in de primaire regio, kunt u hulpprogram ma's zoals [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)of de [Azure-bibliotheek voor gegevens verplaatsing](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) gebruiken om gegevens van uw opslag account in de secundaire regio te kopiëren naar een ander opslag account in een andere regio. U kunt vervolgens uw toepassingen naar dat opslag account laten wijzen voor zowel lees-als schrijf Beschik baarheid.

## <a name="microsoft-managed-failover"></a>Door micro soft beheerde failover

In uitzonderlijke omstandigheden waarbij een regio door een belang rijke nood geval verloren gaat, kan micro soft een regionale failover initiëren. In dit geval is er geen actie voor uw onderdeel vereist. Totdat de door micro soft beheerde failover is voltooid, hebt u geen schrijf toegang tot uw opslag account. Uw toepassingen kunnen lezen uit de secundaire regio als uw opslag account is geconfigureerd voor RA-GRS. 

## <a name="see-also"></a>Zie ook

* [Failover van een account initiëren (preview-versie)](storage-initiate-account-failover.md)
* [Maximaal beschikbare toepassingen met RA-GRS ontwerpen](storage-designing-ha-apps-with-ragrs.md)
* [Zelf studie: een Maxi maal beschik bare toepassing bouwen met Blob Storage](../blobs/storage-create-geo-redundant-storage.md) 
