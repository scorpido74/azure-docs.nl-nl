---
title: Bouw Maxi maal beschik bare Azure Storage toepassingen met geo-zone-redundante opslag (GZRS) (preview) | Microsoft Docs
description: Geo-zone-redundante opslag (GZRS) trouwt de hoge Beschik baarheid van zone-redundante opslag (ZRS) met beveiliging van regionale storingen zoals bepaald door Geo-redundante opslag (GRS). Gegevens in een GZRS-opslag account worden gerepliceerd tussen Azure-beschikbaarheids zones in de primaire regio en worden ook gerepliceerd naar een secundaire geografische regio voor beveiliging tegen regionale rampen.
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: c102941920d2b8746a49be47ef4c5613fa0bc281
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719072"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>Bouw Maxi maal beschik bare Azure Storage toepassingen met geo-zone-redundante opslag (GZRS) (preview)

Geo-zone-redundante opslag (GZRS) (preview) trouwt de hoge Beschik baarheid van [zone-redundante opslag (ZRS)](storage-redundancy-zrs.md) met beveiliging van regionale storingen zoals bepaald door [geo-redundante opslag (GRS)](storage-redundancy-grs.md). Gegevens in een GZRS-opslag account worden gerepliceerd over drie [Azure-beschikbaarheids zones](../../availability-zones/az-overview.md) in de primaire regio en worden ook gerepliceerd naar een secundaire geografische regio voor beveiliging tegen regionale rampen. Elke Azure-regio is gekoppeld aan een andere regio binnen dezelfde geografie, waarbij een regionale koppeling wordt gemaakt. Raadpleeg de [documentatie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie en uitzonde ringen.

Met een GZRS-opslag account kunt u door gaan met het lezen en schrijven van gegevens als een beschikbaarheids zone niet meer beschikbaar is of niet kan worden hersteld. Daarnaast zijn uw gegevens ook duurzaam in het geval van een volledige regionale onderbreking of een nood situatie waarin de primaire regio niet kan worden hersteld. GZRS is ontworpen om ten minste 99.99999999999999% (16 9) duurzaamheid van objecten in een bepaald jaar te bieden. GZRS biedt ook dezelfde schaalbaarheids doelen als LRS, ZRS, GRS of RA-GRS. U kunt optioneel Lees toegang inschakelen voor gegevens in de secundaire regio met een geografisch redundante opslag met lees toegang (RA-GZRS) als uw toepassingen gegevens moeten kunnen lezen in het geval van een ramp in de primaire regio.

Micro soft raadt aan om GZRS te gebruiken voor toepassingen die consistentie, duurzaamheid, hoge Beschik baarheid, uitstekende prestaties en flexibiliteit voor herstel na nood gevallen vereisen. Voor de extra beveiliging van lees toegang tot de secundaire regio in het geval van een regionale ramp, schakelt u RA-GZRS in voor uw opslag account.

## <a name="about-the-preview"></a>Over de preview-versie

Alleen voor algemeen gebruik v2-opslag accounts bieden ondersteuning voor GZRS en RA-GZRS. Zie [Overzicht van Azure-opslagaccounts](storage-account-overview.md) voor meer informatie over de typen opslagaccounts. GZRS en RA-GZRS ondersteunen blok-blobs, pagina-blobs (die geen VHD-schijven zijn), bestanden, tabellen en wacht rijen.

GZRS en RA-GZRS zijn momenteel beschikbaar als preview-versie in de volgende regio's:

- Azië (zuidoost)
- Europa (noord)
- Europa (west)
- Japan - Oost
- VK - zuid
- US - oost
- US - oost 2
- US - centraal

Micro soft blijft GZRS en RA-GZRS inschakelen in extra Azure-regio's. Controleer regel matig de pagina met [updates voor Azure-Services](https://azure.microsoft.com/updates/) voor informatie over ondersteunde regio's.

Raadpleeg voor meer informatie over de preview-prijzen GZRS preview-prijzen voor [blobs](https://azure.microsoft.com/pricing/details/storage/blobs), [bestanden](https://azure.microsoft.com/pricing/details/storage/files/), [wacht rijen](https://azure.microsoft.com/pricing/details/storage/queues/)en [tabellen](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Micro soft raadt aan om Preview-functies voor productie werkbelastingen te gebruiken.

## <a name="how-gzrs-and-ra-gzrs-work"></a>Hoe GZRS en RA-GZRS werken

Wanneer gegevens worden geschreven naar een opslag account waarvoor GZRS of RA-GZRS is ingeschakeld, worden die gegevens eerst synchroon gerepliceerd in de primaire regio over drie beschikbaarheids zones. De gegevens worden vervolgens asynchroon gerepliceerd naar een tweede regio die honderden kilo meters is. Wanneer de gegevens naar de secundaire regio worden geschreven, wordt deze synchroon drie keer achter elkaar gerepliceerd met behulp van [lokaal redundante opslag (LRS)](storage-redundancy-lrs.md).

> [!IMPORTANT]
> Asynchrone replicatie vergt een vertraging tussen de tijd dat gegevens naar de primaire regio worden geschreven en wanneer deze naar de secundaire regio worden gerepliceerd. In het geval van een regionale ramp kunnen wijzigingen die nog niet zijn gerepliceerd naar de secundaire regio, verloren gaan als de gegevens niet kunnen worden hersteld vanuit de primaire regio.

Wanneer u een opslag account maakt, geeft u op hoe gegevens in dat account moeten worden gerepliceerd en geeft u ook de primaire regio voor dat account op. De gekoppelde secundaire regio voor een geo-gerepliceerd account wordt bepaald op basis van de primaire regio en kan niet worden gewijzigd. Zie [bedrijfs continuïteit en herstel na nood gevallen (BCDR): gekoppelde Azure-regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor actuele informatie over regio's die door Azure worden ondersteund. Zie [een opslag account maken](storage-account-create.md)voor meer informatie over het maken van een opslag account met behulp van GZRS of Ra-GZRS.

### <a name="use-ra-gzrs-for-high-availability"></a>RA-GZRS gebruiken voor hoge Beschik baarheid

Wanneer u RA-GZRS inschakelt voor uw opslag account, kunnen uw gegevens worden gelezen van het secundaire eind punt en van het primaire eind punt voor uw opslag account. Het secundaire eind punt voegt het achtervoegsel *(secundair* ) toe aan de account naam. Als uw primaire eind punt voor de Blob service is `myaccount.blob.core.windows.net`, is het secundaire eind punt bijvoorbeeld `myaccount-secondary.blob.core.windows.net`. De toegangs sleutels voor uw opslag account zijn hetzelfde voor zowel de primaire als de secundaire eind punten.

Als u gebruik wilt maken van RA-GZRS in het geval van een regionale storing, moet u uw toepassing vooraf ontwerpen om dit scenario af te handelen. Uw toepassing moet lezen van en schrijven naar het primaire eind punt, maar overschakelen naar het gebruik van het secundaire eind punt in het geval dat de primaire regio niet beschikbaar is. Zie voor hulp bij het ontwerpen voor hoge Beschik baarheid met RA-GZRS [ontwerpen van Maxi maal beschik bare toepassingen met Ra-GZRS of Ra-GRS](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs).

Omdat gegevens asynchroon naar de secundaire regio worden gerepliceerd, is de secundaire regio vaak achter de primaire regio. Om te bepalen welke schrijf bewerkingen zijn gerepliceerd naar de secundaire regio, controleert uw toepassing de laatste synchronisatie tijd voor uw opslag account. Alle schrijf bewerkingen die zijn geschreven naar de primaire regio vóór de laatste synchronisatie tijd zijn gerepliceerd naar de secundaire regio, wat betekent dat ze kunnen worden gelezen van de secundaire. Schrijf bewerkingen die worden geschreven naar de primaire regio na de laatste synchronisatie tijd, kunnen al dan niet zijn gerepliceerd naar de secundaire regio, wat inhoudt dat ze mogelijk niet beschikbaar zijn voor lees bewerkingen.

U kunt een query uitvoeren op de waarde van de eigenschap **laatste synchronisatie tijd** met behulp van Azure PowerShell, Azure CLI of een van de Azure Storage-client bibliotheken. De eigenschap **laatste synchronisatie tijd** is een GMT-datum/-tijd waarde.

Zie de [Microsoft Azure Storage controle lijst voor prestaties en schaal baarheid](storage-performance-checklist.md)voor meer informatie over prestaties en schaal BAARHEID met Ra-GZRS.

### <a name="availability-zone-outages"></a>Uitval van de beschikbaarheids zone

In het geval van een storing die van invloed is op een beschikbaarheids zone in de primaire regio, kunnen uw toepassingen probleemloos blijven lezen uit en schrijven naar uw opslag account met behulp van de andere beschikbaarheids zones voor die regio. Micro soft raadt u aan om de procedures voor tijdelijke fout afhandeling voort te zetten bij het gebruik van GZRS of ZRS. Deze procedures omvatten het implementeren van beleid voor opnieuw proberen met exponentiële back-ups.

Wanneer een beschikbaarheids zone niet meer beschikbaar is, neemt Azure netwerk updates in beslag, zoals het opnieuw aanwijzen van DNS. Deze updates kunnen van invloed zijn op uw toepassing als u gegevens opent voordat de updates zijn voltooid.

### <a name="regional-outages"></a>Regionale storingen

Als een storing van invloed is op de hele primaire regio, probeert micro soft eerst de primaire regio te herstellen. Als herstel niet mogelijk is, zal micro soft een failover uitvoeren naar de secundaire regio, zodat de secundaire regio de nieuwe primaire regio wordt. Als voor het opslag account RA-GZRS is ingeschakeld, kunnen toepassingen die voor dit scenario zijn ontworpen, lezen van de secundaire regio tijdens het wachten op failover. Als RA-GZRS niet is ingeschakeld voor het opslag account, kunnen toepassingen niet meer lezen van de secundaire tot de failover is voltooid.

> [!NOTE]
> GZRS en RA-GZRS zijn momenteel als preview-versie beschikbaar in de regio VS Oost. De door de klant beheerde account-failover (preview) is nog niet beschikbaar in VS Oost 2, zodat klanten momenteel geen account-failover-gebeurtenissen kunnen beheren met GZRS-en RA-GZRS-accounts. Tijdens de preview beheert micro soft failover-gebeurtenissen die van invloed zijn op GZRS-en RA-GZRS-accounts.

Omdat gegevens asynchroon naar de secundaire regio worden gerepliceerd, kan een storing die van invloed is op de primaire regio, leiden tot gegevens verlies als de primaire regio niet kan worden hersteld. Het interval tussen de meest recente schrijf bewerkingen naar de primaire regio en de laatste schrijf bewerking naar de secundaire regio wordt de Recovery Point Objective (RPO) genoemd. De RPO geeft het tijdstip aan waarop gegevens kunnen worden hersteld. Azure Storage heeft doorgaans een RPO van minder dan 15 minuten, maar er is momenteel geen SLA over hoe lang het duurt om gegevens naar de secundaire regio te repliceren.

De beoogde herstel tijd (RTO) is een meting van hoe lang het duurt om de failover uit te voeren en het opslag account weer online te halen. Deze meting geeft de tijd aan die door Azure wordt vereist voor het uitvoeren van de failover door de primaire DNS-vermeldingen te wijzigen zodat deze naar de secundaire locatie verwijzen.

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>Een opslag account migreren naar GZRS of RA-GZRS

U kunt elk bestaand opslag account migreren naar GZRS of RA-GZRS. Migratie van een bestaand ZRS-account naar GZRS of RA-GZRS is eenvoudig, terwijl de migratie vanuit een LRS-, GRS-of RA-GRS-account meer betrokken is. In de volgende secties wordt beschreven hoe u in beide gevallen migreert.

**Bekende beperkingen**

- De Archive-laag wordt momenteel niet ondersteund voor GZRS-accounts (RA-). Zie [Azure Blob-opslag: dynamische, koele en archief toegangs lagen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) voor meer informatie.
- Managed disks ondersteunen geen RA-GZRS. U kunt moment opnamen en installatie kopieën voor Standard-SSD-Managed Disks op Standard-HDD opslag opslaan en [kiezen tussen opties voor LRS en ZRS](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="migrating-from-a-zrs-account"></a>Migreren vanuit een ZRS-account

Als u een bestaand ZRS-account wilt converteren naar RA-GZRS, gebruikt u de cmdlet [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) om de SKU voor het account te wijzigen. Vergeet niet om de waarden van de tijdelijke aanduidingen te vervangen door uw eigen waarden:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>Migreren vanuit een LRS-, GRS-of RA-GRS-account

Er zijn twee opties voor het migreren naar GZRS of RA-GZRS van een LRS-, GRS-of RA-GRS-account:

- U kunt gegevens hand matig kopiëren of verplaatsen naar een nieuw GZRS-of RA-GZRS-account vanuit een bestaand account.
- U kunt een Livemigratie aanvragen.

#### <a name="perform-a-manual-migration"></a>Een hand matige migratie uitvoeren

Als u de migratie op een bepaalde datum wilt volt ooien, kunt u een hand matige migratie uitvoeren. Een handmatige migratie biedt meer flexibiliteit dan een livemigratie. Met een handmatige migratie hebt u de controle over de timing.

Als u gegevens van een bestaand account hand matig naar een GZRS-of RA-GZRS-account wilt migreren, gebruikt u een hulp programma waarmee gegevens efficiënt kunnen worden gekopieerd. Voorbeelden zijn:

- Gebruik een hulp programma zoals AzCopy of een betrouwbaar hulp programma van derden. Zie [aan de slag met AzCopy](storage-use-azcopy-v10.md)voor meer informatie over AzCopy.
- Als u bekend bent met Hadoop of HDInsight, koppelt u de bron-en doel opslag accounts aan uw cluster. Parallelliseren vervolgens het proces voor het kopiëren van gegevens met een hulp programma zoals DistCp.
- Bouw uw eigen hulp programma met behulp van een van de Azure Storage-client bibliotheken.

#### <a name="perform-a-live-migration"></a>Een Livemigratie uitvoeren

Een hand matige migratie kan leiden tot uitval tijd van toepassingen. Als voor uw toepassing hoge beschikbaarheid is vereist, biedt Microsoft u de mogelijkheid tot het uitvoeren van een livemigratie. Een livemigratie is een in-place migratie zonder uitvaltijd.

Tijdens een Livemigratie kunt u uw opslag account gebruiken terwijl uw gegevens worden gemigreerd tussen de bron-en doel opslag accounts. Tijdens het Live migratie proces blijft uw account voldoen aan de SLA voor duurzaamheid en beschik baarheid. Er wordt geen downtime of gegevens verlies veroorzaakt door de Livemigratie.

Alleen voor algemeen gebruik v2-accounts ondersteunen GZRS/RA-GZRS, dus voordat u een aanvraag voor een Livemigratie naar GZRS/RA-GZRS indient, moet u uw account upgraden naar de versie van het algemene doel v2. Zie [overzicht van Azure Storage-accounts](https://docs.microsoft.com/azure/storage/common/storage-account-overview) en [een upgrade uitvoeren naar een v2-opslag account voor algemeen gebruik](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)voor meer informatie.

Zodra de migratie is voltooid, wordt de replicatie-instelling van het opslag account bijgewerkt naar **geo-zone-redundante opslag (GZRS)** of **geo-zone-redundante opslag met lees toegang (RA-GZRS)** . Service-eind punten, toegangs sleutels, SA'S (Shared Access signatures) en andere account configuratie opties blijven ongewijzigd en intact.

Houd u aan de volgende beperkingen voor Livemigratie:

- Microsoft verwerkt uw aanvraag voor livemigratie onmiddellijk, maar er is geen garantie wanneer een livemigratie wordt voltooid. Als u wilt dat uw gegevens worden gemigreerd naar GZRS of RA-GZRS op een bepaalde datum, raadt micro soft u aan om in plaats daarvan een hand matige migratie uit te voeren. In het algemeen geldt dat hoe meer gegevens u voor uw account hebt, hoe langer het duurt om die gegevens te migreren.
- Livemigratie wordt alleen ondersteund voor opslag accounts die gebruikmaken van GRS of RA-GRS-replicatie. Als uw account gebruikmaakt van LRS, moet u eerst het replicatie type van uw account wijzigen in GRS of RA-GRS voordat u doorgaat. In deze tussenliggende stap wordt het secundaire eind punt toegevoegd dat is verschaft door GRS/RA-GRS.
- Uw account moet gegevens bevatten.
- U kunt alleen gegevens binnen dezelfde regio migreren.
- Alleen de standaard opslag typen bieden ondersteuning voor Livemigratie. Premium Storage-accounts moeten hand matig worden gemigreerd.
- Livemigratie van een GZRS-of RA-GZRS-account naar een LRS-, GRS-of RA-GRS-account wordt niet ondersteund. U moet de gegevens handmatig verplaatsen naar een nieuw opslagaccount of een bestaand opslagaccount.
- U kunt een Live migratie van RA-GRS aanvragen bij RA-GZRS. Migratie van RA-GRS naar GZRS wordt echter niet ondersteund. In dit geval moet u een Livemigratie aanvragen bij RA-GZRS en het opslag account vervolgens hand matig converteren om GZRS te gebruiken.
- Beheerde schijven bieden alleen ondersteuning voor LRS en kunnen niet worden gemigreerd naar GZRS of RA-GZRS. Zie [Introduction to Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)(Engelstalig) voor integratie met beschikbaarheids sets.
- U kunt moment opnamen en installatie kopieën voor Standard-SSD-Managed Disks op Standard-HDD opslag opslaan en [kiezen tussen LRS, ZRS, GZRS en RA-GZRS opties](https://azure.microsoft.com/pricing/details/managed-disks/).
- Accounts met grote bestands shares worden niet ondersteund voor GZRS.

Als u een Live migratie wilt aanvragen, gebruikt u de [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Selecteer in de portal het opslag account dat u wilt migreren naar GZRS of RA-GZRS en volg deze instructies:

1. Selecteer een **nieuwe ondersteunings aanvraag**.
2. Voltooi de basis **beginselen** op basis van uw account gegevens. Selecteer in de sectie **service** **opslag account beheer** en geef het account op dat moet worden gemigreerd.
3. Selecteer **Next**.
4. Geef de volgende waarden op voor het **probleem** gedeelte:
    - **Ernst**: behoud de standaard waarde in.
    - **Probleem type**: **gegevens migratie**selecteren.
    - **Categorie**: Selecteer **migreren naar (RA) GZRS binnen een regio**.
    - **Titel**: Typ een beschrijvende titel, bijvoorbeeld **(RA-) GZRS-account migratie**.
    - **Details**: Typ aanvullende details in het vak **Details** , bijvoorbeeld "Ik wil migreren naar GZRS van [LRS, GRS] in de regio \_\_." of "Ik wil graag migreren naar RA-GZRS van [LRS, RA-GRS] in de regio \_\_."
5. Selecteer **Next**.
6. Controleer of de contact gegevens juist zijn op de Blade **contact gegevens** .
7. Selecteer **Maken**.

Een ondersteunings medewerker neemt contact met u op om hulp te bieden.

## <a name="see-also"></a>Zie ook

- [Azure Storage-replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [Lokaal redundante opslag (LRS): lage kosten voor gegevens redundantie voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [Zone-redundante opslag (ZRS): Maxi maal beschik bare Azure Storage toepassingen](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
- [Schaalbaarheids-en prestatie doelen voor standaard opslag accounts](scalability-targets-standard-account.md)
