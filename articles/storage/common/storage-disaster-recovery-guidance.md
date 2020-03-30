---
title: Failover van noodherstel- en opslagaccount (voorbeeld)
titleSuffix: Azure Storage
description: Azure Storage ondersteunt accountfailover (preview) voor georedundante opslagaccounts. Met accountfailover u het failoverproces voor uw opslagaccount starten als het primaire eindpunt niet beschikbaar is.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7a8f6d0d3ab3b456c41128da9b689f6b7eda0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365357"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>Herstel na noodgevallen en failover van het account (voorbeeld)

Microsoft streeft ernaar ervoor te zorgen dat Azure-services altijd beschikbaar zijn. Er kunnen echter ongeplande serviceonderbrekingen optreden. Als uw toepassing tolerantie vereist, raadt Microsoft aan om georedundante opslag te gebruiken, zodat uw gegevens naar een tweede regio worden gekopieerd. Bovendien moeten klanten beschikken over een noodherstelplan voor het afhandelen van een regionale servicestoring. Een belangrijk onderdeel van een noodherstelplan is de voorbereiding om niet over te gaan naar het secundaire eindpunt in het geval dat het primaire eindpunt niet beschikbaar is.

Azure Storage ondersteunt accountfailover (preview) voor georedundante opslagaccounts. Met accountfailover u het failoverproces voor uw opslagaccount starten als het primaire eindpunt niet beschikbaar is. De failover werkt het secundaire eindpunt bij om het primaire eindpunt voor uw opslagaccount te worden. Zodra de failover is voltooid, kunnen clients beginnen met schrijven naar het nieuwe primaire eindpunt.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

In dit artikel worden de concepten en het proces beschreven die betrokken zijn bij een failover van een account en wordt beschreven hoe u uw opslagaccount voorbereiden op herstel met de minste impact van de klant. Zie [Een accountfailover starten (voorbeeld)](storage-initiate-account-failover.md)voor meer informatie over het starten van een failovervan een account in de Azure-portal of PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>De juiste redundantieoptie kiezen

Azure Storage onderhoudt meerdere kopieën van uw opslagaccount om duurzaamheid en hoge beschikbaarheid te garanderen. Welke redundantieoptie u voor uw account kiest, is afhankelijk van de mate van veerkracht die u nodig hebt. Voor bescherming tegen regionale uitval kiest u georedundante opslag, met of zonder de optie voor leestoegang vanuit de secundaire regio:  

**Georedundante opslag (GRS) of geo-zoneredundante opslag (GZRS) (voorbeeld)** kopieert uw gegevens asynchroon in twee geografische regio's die ten minste honderden kilometers uit elkaar liggen. Als de primaire regio een storing heeft, dient de secundaire regio als een redundante bron voor uw gegevens. U een failover starten om het secundaire eindpunt om te zetten in het primaire eindpunt.

**Read-access geo-redundante opslag (RA-GRS) of read-access geo-zone-redundante opslag (RA-GZRS) (preview)** biedt georedundante opslag met het extra voordeel van leestoegang tot het secundaire eindpunt. Als er een storing optreedt in het primaire eindpunt, kunnen toepassingen die zijn geconfigureerd voor RA-GRS en die zijn ontworpen voor hoge beschikbaarheid, blijven lezen vanaf het secundaire eindpunt. Microsoft raadt RA-GRS aan voor maximale tolerantie voor uw toepassingen.

Zie [Redundantie azure storage](storage-redundancy.md)voor meer informatie over redundantie in Azure Storage.

> [!WARNING]
> Geo-redundante opslag brengt een risico op gegevensverlies met zich mee. Gegevens worden asynchroon naar het secundaire gebied gekopieerd, wat betekent dat er een vertraging is tussen wanneer gegevens die naar het primaire gebied zijn geschreven, naar het secundaire gebied worden geschreven. In het geval van een storing gaat u bewerkingen naar het primaire eindpunt schrijven die nog niet naar het secundaire eindpunt zijn gekopieerd.

## <a name="design-for-high-availability"></a>Ontwerpen voor hoge beschikbaarheid

Het is belangrijk om uw toepassing voor hoge beschikbaarheid vanaf het begin te ontwerpen. Raadpleeg deze Azure-bronnen voor richtlijnen bij het ontwerpen van uw toepassing en planning voor noodherstel:

- [Het ontwerpen van veerkrachtige toepassingen voor Azure:](/azure/architecture/checklist/resiliency-per-service)een overzicht van de belangrijkste concepten voor het ontwerpen van zeer beschikbare toepassingen in Azure.
- [Beschikbaarheidschecklist](/azure/architecture/checklist/resiliency-per-service): Een checklist om te controleren of uw toepassing de beste ontwerpprocedures voor hoge beschikbaarheid implementeert.
- [Het ontwerpen van zeer beschikbare applicaties met RA-GRS](storage-designing-ha-apps-with-ragrs.md): Ontwerpbegeleiding voor het bouwen van applicaties om te profiteren van RA-GRS.
- [Zelfstudie: Bouw een zeer beschikbare toepassing met Blob-opslag:](../blobs/storage-create-geo-redundant-storage.md)een zelfstudie die laat zien hoe u een zeer beschikbare toepassing bouwen die automatisch schakelt tussen eindpunten als fouten en herstelwordt gesimuleerd. 

Houd bovendien rekening met deze aanbevolen procedures voor het behouden van hoge beschikbaarheid voor uw Azure Storage-gegevens:

- **Schijven:** Gebruik [Azure Backup](https://azure.microsoft.com/services/backup/) om een back-up te maken van de VM-schijven die worden gebruikt door uw virtuele Azure-machines. Overweeg ook [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) te gebruiken om uw VM's te beschermen in het geval van een regionale ramp.
- **Blobs blokkeren:** Schakel [soft delete](../blobs/storage-blob-soft-delete.md) in om te beschermen tegen verwijderingen en overschrijft op objectniveau, of kopieer blokblobs naar een ander opslagaccount in een andere regio met [AzCopy,](storage-use-azcopy.md) [Azure PowerShell](storage-powershell-guide-full.md)of de [Azure Data Movement-bibliotheek](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
- **Bestanden:** Gebruik [AzCopy](storage-use-azcopy.md) of [Azure PowerShell](storage-powershell-guide-full.md) om uw bestanden te kopiëren naar een ander opslagaccount in een andere regio.
- **Tabellen:** gebruik [AzCopy](storage-use-azcopy.md) om tabelgegevens te exporteren naar een ander opslagaccount in een andere regio.

## <a name="track-outages"></a>Uitval bijhouden

Klanten kunnen zich abonneren op het [Azure Service Health Dashboard](https://azure.microsoft.com/status/) om de status en status van Azure Storage en andere Azure-services bij te houden.

Microsoft raadt u ook aan uw toepassing te ontwerpen om u voor te bereiden op de mogelijkheid van schrijffouten. Uw toepassing moet schrijffouten blootleggen op een manier die u waarschuwt voor de mogelijkheid van een storing in de primaire regio.

## <a name="understand-the-account-failover-process"></a>Inzicht in het failoverproces van uw account

Met een door de klant beheerde accountfailover (preview) u uw volledige opslagaccount naar het secundaire gebied niet opnemen als de primaire om welke reden dan ook niet meer beschikbaar is. Wanneer u een failover naar het secundaire gebied forceert, kunnen clients beginnen met het schrijven van gegevens naar het secundaire eindpunt nadat de failover is voltooid. De failover duurt meestal ongeveer een uur.

### <a name="how-an-account-failover-works"></a>Hoe een accountfailover werkt

Onder normale omstandigheden schrijft een client gegevens naar een Azure Storage-account in het primaire gebied en worden die gegevens asynchroon gekopieerd naar het secundaire gebied. In de volgende afbeelding ziet u het scenario waarin het primaire gebied beschikbaar is:

![Clients schrijven gegevens naar het opslagaccount in de primaire regio](media/storage-disaster-recovery-guidance/primary-available.png)

Als het primaire eindpunt om welke reden dan ook niet meer beschikbaar is, kan de client niet meer naar het opslagaccount schrijven. In de volgende afbeelding ziet u het scenario waarin de primaire niet meer beschikbaar is, maar er nog geen herstel is gebeurd:

![De primaire is niet beschikbaar, zodat clients geen gegevens kunnen schrijven](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

De klant initieert de account failover naar het secundaire eindpunt. Het failoverproces werkt de DNS-invoer van Azure Storage bij, zodat het secundaire eindpunt het nieuwe primaire eindpunt voor uw opslagaccount wordt, zoals in de volgende afbeelding wordt weergegeven:

![Klant initieert accountfailover naar secundair eindpunt](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Schrijftoegang wordt hersteld voor GRS- en RA-GRS-accounts zodra de DNS-vermelding is bijgewerkt en aanvragen naar het nieuwe primaire eindpunt worden doorverwezen. Bestaande eindpunten voor opslagservice voor blobs, tabellen, wachtrijen en bestanden blijven hetzelfde na de failover.

> [!IMPORTANT]
> Nadat de failover is voltooid, is het opslagaccount geconfigureerd om lokaal redundant te zijn in het nieuwe primaire eindpunt. Als u de replicatie naar het nieuwe secundaire wilt hervatten, configureert u het account om georedundante opslag opnieuw te gebruiken (RA-GRS of GRS).
>
> Houd er rekening mee dat het omzetten van een LRS-account naar RA-GRS of GRS kosten met zich meebrengt. Deze kosten zijn van toepassing op het bijwerken van het opslagaccount in de nieuwe primaire regio om RA-GRS of GRS te gebruiken na een failover.  

### <a name="anticipate-data-loss"></a>Anticiperen op gegevensverlies

> [!CAUTION]
> Een account failover gaat meestal om wat verlies van gegevens. Het is belangrijk om de implicaties van het initiëren van een account failover te begrijpen.  

Omdat gegevens asynchroon worden geschreven van het primaire gebied naar het secundaire gebied, is er altijd een vertraging voordat een schrijven naar het primaire gebied wordt gekopieerd naar het secundaire gebied. Als het primaire gebied niet meer beschikbaar is, zijn de meest recente schrijft mogelijk nog niet gekopieerd naar het secundaire gebied.

Wanneer u een failover forceert, gaan alle gegevens in het primaire gebied verloren als het secundaire gebied het nieuwe primaire gebied wordt en het opslagaccount is geconfigureerd als lokaal redundant. Alle gegevens die al naar het secundaire worden gekopieerd, worden bijgehouden wanneer de failover plaatsvindt. Alle gegevens die naar de primaire gegevens zijn geschreven en die niet ook naar het secundaire zijn gekopieerd, gaan echter permanent verloren.

De eigenschap **Last Sync Time** geeft de meest recente tijd aan dat gegevens uit het primaire gebied gegarandeerd naar het secundaire gebied zijn geschreven. Alle gegevens die vóór de laatste synchronisatietijd zijn geschreven, zijn beschikbaar op de secundaire, terwijl gegevens die na de laatste synchronisatietijd zijn geschreven, mogelijk niet naar de secundaire zijn geschreven en mogelijk verloren zijn gegaan. Gebruik deze eigenschap in het geval van een storing om de hoeveelheid gegevensverlies te schatten die u oplopen door het initiëren van een failover van een account.

Als een aanbevolen praktijk, het ontwerpen van uw toepassing, zodat u de laatste synchronisatietijd gebruiken om het verwachte verlies van gegevens te evalueren. Als u bijvoorbeeld alle schrijfbewerkingen registreert, u de tijd van uw laatste schrijfbewerkingvergelijken met de laatste synchronisatietijd om te bepalen welke schrijfbewerkingen niet zijn gesynchroniseerd met het secundaire.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Wees voorzichtig wanneer u niet terugkeert naar de oorspronkelijke primaire

Nadat u niet meer overdeint van de primaire naar het secundaire gebied, is uw opslagaccount geconfigureerd als lokaal overbodig in het nieuwe primaire gebied. U het account opnieuw configureren voor georedundantie door het bij te werken om GRS of RA-GRS te gebruiken. Wanneer het account opnieuw is geconfigureerd voor georedundantie na een failover, begint het nieuwe primaire gebied onmiddellijk met het kopiëren van gegevens naar het nieuwe secundaire gebied, dat de primaire was vóór de oorspronkelijke failover. Het kan echter enige tijd duren voordat bestaande gegevens in het primaire volledig worden gekopieerd naar het nieuwe secundaire.

Nadat het opslagaccount opnieuw is geconfigureerd voor georedundantie, is het mogelijk om een nieuwe failover te starten van de nieuwe primaire terug naar het nieuwe secundaire. In dit geval wordt het oorspronkelijke primaire gebied voorafgaand aan de failover opnieuw de primaire regio en is het geconfigureerd om lokaal overbodig te zijn. Alle gegevens in het primaire gebied na failover (het oorspronkelijke secundaire) gaan vervolgens verloren. Als de meeste gegevens in het opslagaccount niet zijn gekopieerd naar het nieuwe secundaire voordat u niet teruggaat, u een groot gegevensverlies lijden.

Als u een groot gegevensverlies wilt voorkomen, controleert u de waarde van de eigenschap **Last Sync Time** voordat u niet meer teruggaat. Vergelijk de laatste synchronisatietijd met de laatste keren dat gegevens naar het nieuwe primaire worden geschreven om het verwachte gegevensverlies te evalueren. 

## <a name="initiate-an-account-failover"></a>Een failover van account initiëren

U een failovervoor voor een account starten vanuit de Azure-portal, PowerShell, Azure CLI of de API voor Azure Storage-bronnen. Zie [Een accountfailover starten (voorbeeld)](storage-initiate-account-failover.md)voor meer informatie over het starten van een failover.

## <a name="about-the-preview"></a>Informatie over de preview

Accountfailover is beschikbaar in preview voor alle klanten die GRS of RA-GRS gebruiken met Azure Resource Manager-implementaties. Typen v1, V2 voor algemeen gebruik en Blob-opslagaccountworden ondersteund. Accountfailover is momenteel beschikbaar in alle openbare regio's. Account failover is niet beschikbaar in soevereine / nationale wolken op dit moment.

De preview is alleen bedoeld voor niet-productiegebruik. Productieserviceovereenkomsten (SLA's) zijn momenteel niet beschikbaar.

### <a name="additional-considerations"></a>Aanvullende overwegingen

Bekijk de aanvullende overwegingen die in deze sectie worden beschreven om te begrijpen hoe uw toepassingen en services kunnen worden beïnvloed wanneer u een failover forceert tijdens de previewperiode.

#### <a name="storage-account-containing-archived-blobs"></a>Opslagaccount met gearchiveerde blobs

Opslagaccounts met gearchiveerde blobs ondersteunen accountfailover. Zodra failover is voltooid, om het account terug te zetten naar GRS of RA-GRS alle gearchiveerde blobs moeten worden gerehydrateerd naar een online laag eerst.

#### <a name="storage-resource-provider"></a>Opslagresourceprovider

Nadat een failover is voltooid, kunnen clients opnieuw Azure Storage-gegevens lezen en schrijven in het nieuwe primaire gebied. De Azure Storage-bronprovider mislukt echter niet, dus resourcebeheerbewerkingen moeten nog steeds plaatsvinden in de primaire regio. Als de primaire regio niet beschikbaar is, u geen beheerbewerkingen uitvoeren op het opslagaccount.

Omdat de Azure Storage-bronprovider niet mislukt, retourneert de eigenschap [Locatie](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) de oorspronkelijke primaire locatie nadat de failover is voltooid.

#### <a name="azure-virtual-machines"></a>Virtuele machines van Azure

Azure virtual machines (VM's) mislukken niet als onderdeel van een failover account. Als het primaire gebied niet meer beschikbaar is en u niet naar het secundaire gebied gaat, moet u na de failover alle VM's opnieuw maken. Ook is er een potentieel gegevensverlies in verband met de failover van het account. Microsoft raadt de volgende richtlijnen voor [hoge beschikbaarheid](../../virtual-machines/windows/manage-availability.md) en [noodherstel](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md) aan die specifiek zijn voor virtuele machines in Azure.

#### <a name="azure-unmanaged-disks"></a>Azure-onbeheerde schijven

Als aanbevolen aanbevolen aanbevolen toepassing van Microsoft om onbeheerde schijven om te zetten naar beheerde schijven. Als u echter moet falen voor een account dat niet-beheerde schijven bevat die zijn gekoppeld aan Azure VM's, moet u de VM afsluiten voordat u de failover start.

Onbeheerde schijven worden opgeslagen als paginablobs in Azure Storage. Wanneer een vm wordt uitgevoerd in Azure, worden alle niet-beheerde schijven die aan de VM zijn gekoppeld, geleased. Een failoveraccount kan niet doorgaan wanneer er een lease op een blob is. Voer de volgende stappen uit om de failover uit te voeren:

1. Noteer voordat u begint de namen van onbeheerde schijven, hun logische eenheidsnummers (LUN) en de VM waaraan ze zijn gekoppeld. Hierdoor wordt het gemakkelijker om de schijven opnieuw te bevestigen na de failover.
2. Schakel de VM uit.
3. Verwijder de VM, maar bewaar de VHD-bestanden voor de niet-beheerde schijven. Let op het tijdstip waarop u de VM hebt verwijderd.
4. Wacht tot de **laatste synchronisatietijd** is bijgewerkt en is later dan het tijdstip waarop u de VM hebt verwijderd. Deze stap is belangrijk, want als het secundaire eindpunt niet volledig is bijgewerkt met de VHD-bestanden wanneer de failover optreedt, werkt de VM mogelijk niet goed in het nieuwe primaire gebied.
5. Start de failover van de account.
6. Wacht tot de failover van het account is voltooid en de secundaire regio de nieuwe primaire regio is geworden.
7. Maak een VM in het nieuwe primaire gebied en bevestig de VHD's opnieuw.
8. Start de nieuwe VM.

Houd er rekening mee dat alle gegevens die in een tijdelijke schijf zijn opgeslagen, verloren gaan wanneer de vm wordt afgesloten.

### <a name="unsupported-features-and-services"></a>Niet-ondersteunde functies en services

De volgende functies en services worden niet ondersteund voor accountfailover voor de preview-release:

- Azure File Sync biedt geen ondersteuning voor failover voor opslagaccount. Er mag geen failover-overschakeling worden uitgevoerd voor opslagaccounts met Azure-bestandsshares die worden gebruikt als cloudeindpunten in Azure File Sync. Als u dat wel doet, werkt de synchronisatie niet meer en kan dit leiden tot onverwacht gegevensverlies van bestanden in cloudlagen.
- ADLS Gen2-opslagaccounts (accounts met hiërarchische naamruimte ingeschakeld) worden op dit moment niet ondersteund.
- Een opslagaccount met premium blokblobs kan niet worden mislukt. Opslagaccounts die premium block blobs ondersteunen, ondersteunen momenteel geen georedundantie.
- Een opslagaccount met een [worm-onveranderlijkheidsbeleid](../blobs/storage-blob-immutable-storage.md) dat is ingeschakeld, kan niet worden mislukt. Ontgrendelde/vergrendelde op tijd gebaseerde retentie- of juridische wachtbeleidsregels voorkomen failover om naleving te behouden.
- Nadat de failover is voltooid, werken de volgende functies mogelijk niet meer als deze oorspronkelijk zijn ingeschakeld: [gebeurtenisabonnementen,](../blobs/storage-blob-event-overview.md) [Feed wijzigen,](../blobs/storage-blob-change-feed.md) [Levenscyclusbeleid](../blobs/storage-lifecycle-management-concepts.md)en [Logboekregistratie van opslaganalyses](storage-analytics-logging.md).

## <a name="copying-data-as-an-alternative-to-failover"></a>Gegevens kopiëren als alternatief voor failover

Als uw opslagaccount is geconfigureerd voor RA-GRS, hebt u de toegang tot uw gegevens gelezen met behulp van het secundaire eindpunt. Als u liever niet uitvalt in het geval van een storing in het primaire gebied, u hulpprogramma's zoals [AzCopy,](storage-use-azcopy.md) [Azure PowerShell](storage-powershell-guide-full.md)of de [Azure Data Movement-bibliotheek](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) gebruiken om gegevens van uw opslagaccount in het secundaire gebied te kopiëren naar een ander opslagaccount in een onaangetast gebied. U uw toepassingen vervolgens naar dat opslagaccount wijzen voor zowel lees- als schrijfbeschikbaarheid.

> [!CAUTION]
> Een failoveraccount mag niet worden gebruikt als onderdeel van uw gegevensmigratiestrategie.


## <a name="microsoft-managed-failover"></a>Door Microsoft beheerde failover

In extreme omstandigheden waarin een regio verloren gaat als gevolg van een aanzienlijke ramp, kan Microsoft een regionale failover initiëren. In dit geval is er geen actie van uw kant vereist. Totdat de door Microsoft beheerde failover is voltooid, hebt u geen schrijftoegang tot uw opslagaccount. Uw toepassingen kunnen uit de secundaire regio lezen of uw opslagaccount is geconfigureerd voor RA-GRS. 

## <a name="see-also"></a>Zie ook

- [Een accountfailover starten (voorbeeld)](storage-initiate-account-failover.md)
- [Maximaal beschikbare toepassingen met RA-GRS ontwerpen](storage-designing-ha-apps-with-ragrs.md)
- [Zelfstudie: Een zeer beschikbare toepassing bouwen met Blob-opslag](../blobs/storage-create-geo-redundant-storage.md) 
