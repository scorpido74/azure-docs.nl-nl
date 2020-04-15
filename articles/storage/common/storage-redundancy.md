---
title: De gegevensredundantie
titleSuffix: Azure Storage
description: Gegevens in uw Microsoft Azure Storage-account worden gerepliceerd voor duurzaamheid en hoge beschikbaarheid. Redundantieconfiguraties omvatten lokaal redundante opslag (LRS), zoneredundante opslag (ZRS), georedundante opslag (GRS), georedundante opslag voor leestoegang (RA-GRS), geozoneredundante opslag (GZRS) (preview) en geread-access geo-zone redundante opslag (RA-GZRS) (preview).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 1961797f37a760fe3a31dc8aa3830889965b69b5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379945"
---
# <a name="azure-storage-redundancy"></a>Redundantie azure-opslag

Azure Storage slaat altijd meerdere kopieën van uw gegevens op, zodat deze worden beschermd tegen geplande en ongeplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk- of stroomuitval en enorme natuurrampen. Redundantie zorgt ervoor dat uw opslagaccount voldoet aan de [SLA (Service-Level Agreement) voor Azure Storage,](https://azure.microsoft.com/support/legal/sla/storage/) zelfs in het licht van storingen.

Bij de beslissing welke redundantie optie is het beste voor uw scenario, rekening houden met de afwegingen tussen lagere kosten en een hogere beschikbaarheid en duurzaamheid. De factoren die helpen bepalen welke redundantieoptie u moet kiezen, zijn:  

- Hoe uw gegevens worden gerepliceerd in de primaire regio
- Of uw gegevens worden gerepliceerd naar een tweede locatie die geografisch ver van de primaire regio ligt, om te beschermen tegen regionale rampen
- Of uw toepassing leestoegang tot de gerepliceerde gegevens in het secundaire gebied vereist als de primaire regio om welke reden dan ook niet meer beschikbaar is

## <a name="redundancy-in-the-primary-region"></a>Redundantie in de primaire regio

Gegevens in een Azure Storage-account worden altijd drie keer gerepliceerd in het primaire gebied. Azure Storage biedt twee opties voor de manier waarop uw gegevens worden gerepliceerd in het primaire gebied:

- **Lrs (Local redundant storage)** kopieert uw gegevens drie keer synchroon binnen één fysieke locatie in het primaire gebied. LRS is de goedkoopste replicatieoptie, maar wordt niet aanbevolen voor toepassingen die een hoge beschikbaarheid vereisen.
- **Zrs (Zoneredundant storage)** kopieert uw gegevens synchroon over drie Azure-beschikbaarheidszones in het primaire gebied. Voor toepassingen die een hoge beschikbaarheid vereisen, raadt Microsoft aan ZRS in de primaire regio te gebruiken en ook te repliceren naar een secundaire regio.

### <a name="locally-redundant-storage"></a>Lokaal redundante opslag

Lokaal redundante opslag (LRS) repliceert uw gegevens drie keer binnen één fysieke locatie in de primaire regio. LRS biedt ten minste 99.999999999% (11 negens) duurzaamheid van objecten over een bepaald jaar.

LRS is de laagste kosten redundantie optie en biedt de minste duurzaamheid in vergelijking met andere opties. LRS beschermt uw gegevens tegen serverrack- en schijfstoringen. Als er echter een ramp zoals brand of overstroming optreedt in het datacenter, kunnen alle replica's van een opslagaccount met LRS verloren gaan of onherstelbaar zijn. Om dit risico te beperken, raadt Microsoft aan [zoneredundante opslag](#zone-redundant-storage) (ZRS), [georedundante opslag](#geo-redundant-storage) (GRS) of [geo-zoneredundante opslag (preview)](#geo-zone-redundant-storage-preview) (GZRS) te gebruiken.

Een schrijfverzoek naar een opslagaccount dat LRS gebruikt, gebeurt synchroon. De schrijfbewerking wordt pas geretourneerd nadat de gegevens naar alle drie de replica's zijn geschreven.

LRS is een goede keuze voor de volgende scenario's:

- Als uw toepassing gegevens opslaat die gemakkelijk kunnen worden gereconstrueerd als er gegevens verloren gaan, u kiezen voor LRS.
- Als uw toepassing beperkt is tot het repliceren van gegevens alleen binnen een land of regio vanwege vereisten voor gegevensbeheer, u kiezen voor LRS. In sommige gevallen kunnen de gekoppelde regio's waarde gegevens geo-gerepliceerd zijn, zich in een ander land of een andere regio bevinden. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor meer informatie over gekoppelde regio's.

### <a name="zone-redundant-storage"></a>Zone-redundante opslag

Zrs (Zone redundant storage) repliceert uw Azure Storage-gegevens synchroon in drie Azure-beschikbaarheidszones in het primaire gebied. Elke beschikbaarheidszone is een aparte fysieke locatie met onafhankelijke stroom, koeling en netwerken. ZRS biedt duurzaamheid voor Azure Storage-gegevensobjecten van ten minste 99999999999% (12 9's) over een bepaald jaar.

Met ZRS zijn uw gegevens nog steeds toegankelijk voor zowel lees- als schrijfbewerkingen, zelfs als een zone niet beschikbaar is. Als een zone niet meer beschikbaar is, voert Azure netwerkupdates uit, zoals DNS-opnieuw aanwijzen. Deze updates kunnen van invloed zijn op uw toepassing als u toegang hebt tot gegevens voordat de updates zijn voltooid. Bij het ontwerpen van toepassingen voor ZRS, volg je procedures voor tijdelijke foutafhandeling, waaronder het implementeren van opnieuw proberenbeleid met exponentiële back-off.

Een schrijfverzoek naar een opslagaccount dat ZRS gebruikt, gebeurt synchroon. De schrijfbewerking wordt pas geretourneerd nadat de gegevens naar alle replica's in de drie beschikbaarheidszones zijn geschreven.

Microsoft raadt aan ZRS in de primaire regio te gebruiken voor scenario's die consistentie, duurzaamheid en hoge beschikbaarheid vereisen. ZRS biedt uitstekende prestaties, lage latentie en tolerantie voor uw gegevens als deze tijdelijk niet beschikbaar zijn. Zrs op zichzelf kan uw gegevens echter niet beschermen tegen een regionale ramp waarbij meerdere zones permanent worden getroffen. Voor bescherming tegen regionale rampen raadt Microsoft aan om [geo-zoneredundante opslag](#geo-zone-redundant-storage-preview) (GZRS) te gebruiken, waarbij ZRS in de primaire regio wordt gebruikt en uw gegevens ook worden gegeo-gerepliceerd naar een secundaire regio.

In de volgende tabel ziet u welke typen opslagaccounts ZRS ondersteunen in welke regio's:

|    Type opslagaccount    |    Ondersteunde regio’s    |    Ondersteunde services    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Algemeen gebruik v2<sup>1</sup>    | Azië - zuidoost<br /> Australië - oost<br /> Europa - noord<br />  Europa - west<br /> Frankrijk - centraal<br /> Japan - oost<br /> Zuid-Afrika Noord<br /> Verenigd Koninkrijk Zuid<br /> US - centraal<br /> US - oost<br /> US - oost 2<br /> US - west 2    |    Blok-blobs<br /> Paginablobs<sup>2</sup><br /> Bestandsshares (standaard)<br /> Tabellen<br /> Wachtrijen<br /> |
|    BlockBlobStorage<sup>1</sup>    | Europa - west<br /> US - oost    |    Alleen blobs blokkeren    |
|    Bestandsopslag    | Europa - west<br /> US - oost    |    Alleen Azure-bestanden    |

<sup>1</sup> De archieflaag wordt momenteel niet ondersteund voor ZRS-accounts.<br />
<sup>2</sup> Opslagaccounts die door Azure beheerde schijven voor virtuele machines bevatten, maken altijd gebruik van LRS. Azure-onbeheerde schijven moeten ook LRS gebruiken. Het is mogelijk om een opslagaccount voor Azure-onbeheerde schijven te maken dat GRS gebruikt, maar dit wordt niet aanbevolen vanwege mogelijke problemen met consistentie ten opzichte van asynchrone georeplicatie. Geen beheerde of onbeheerde schijven ondersteunen ZRS of GZRS. Zie Prijzen voor door [Azure beheerde schijven voor](https://azure.microsoft.com/pricing/details/managed-disks/)meer informatie over beheerde schijven .

Zie **Services-ondersteuning per regio** in [Wat zijn Azure Availability Zones voor](../../availability-zones/az-overview.md)informatie over welke regio's ZRS ondersteunen.

## <a name="redundancy-in-a-secondary-region"></a>Redundantie in een secundaire regio

Voor toepassingen die een hoge beschikbaarheid vereisen, u ervoor kiezen om de gegevens in uw opslagaccount bovendien te kopiëren naar een secundaire regio die honderden kilometers verwijderd is van de primaire regio. Als uw opslagaccount wordt gekopieerd naar een secundaire regio, zijn uw gegevens duurzaam, zelfs in het geval van een volledige regionale storing of een ramp waarbij de primaire regio niet kan worden hersteld.

Wanneer u een opslagaccount maakt, selecteert u het primaire gebied voor het account. De gekoppelde secundaire regio wordt bepaald op basis van de primaire regio en kan niet worden gewijzigd. Zie [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)voor meer informatie over regio's die worden ondersteund door Azure.

Azure Storage biedt twee opties voor het kopiëren van uw gegevens naar een secundaire regio:

- **Georedundante opslag (GRS)** kopieert uw gegevens drie keer synchroon binnen één fysieke locatie in het primaire gebied met LRS. Vervolgens worden uw gegevens asynchroon gekopieerd naar één fysieke locatie in het secundaire gebied.
- **GzRS (preview) (Geo-zone-redundante opslag)** kopieert uw gegevens synchroon over drie Azure-beschikbaarheidszones in het primaire gebied met ZRS. Vervolgens worden uw gegevens asynchroon gekopieerd naar één fysieke locatie in het secundaire gebied.

Het belangrijkste verschil tussen GRS en GZRS is hoe gegevens worden gerepliceerd in de primaire regio. Binnen de secundaire locatie worden gegevens altijd drie keer synchroon gerepliceerd met LRS.

Bij GRS of GZRS zijn de gegevens op de secundaire locatie niet beschikbaar voor lees- of schrijftoegang, tenzij er een failover is naar de secundaire regio. Voor leestoegang tot de secundaire locatie configureert u uw opslagaccount om gebruik te maken van georedundante opslag (RA-GRS) of geo-zoneredundante opslag (RA-GZRS) met leestoegang. Zie [Toegang tot gegevens in het secundaire gebied lezen voor](#read-access-to-data-in-the-secondary-region)meer informatie .

Als het primaire gebied niet meer beschikbaar is, u ervoor kiezen om niet over te gaan naar het secundaire gebied (voorbeeld). Nadat de failover is voltooid, wordt het secundaire gebied de primaire regio en u opnieuw gegevens lezen en schrijven. Zie [Herstel na noodgevallen (preview)](storage-disaster-recovery-guidance.md)voor meer informatie over herstel na noodgevallen en om te leren hoe u mislukken naar de secundaire regio.

> [!IMPORTANT]
> Omdat gegevens asynchroon naar het secundaire gebied worden gerepliceerd, kan een fout die van invloed is op het primaire gebied leiden tot gegevensverlies als het primaire gebied niet kan worden hersteld. Het interval tussen de meest recente schrijft naar het primaire gebied en de laatste schrijven naar het secundaire gebied staat bekend als de doelstelling herstelpunt (RPO). De RPO geeft aan op welk tijdstip gegevens kunnen worden hersteld. Azure Storage heeft doorgaans een RPO van minder dan 15 minuten, hoewel er momenteel geen SLA is over hoe lang het duurt om gegevens te repliceren naar de secundaire regio.

### <a name="geo-redundant-storage"></a>Geografisch redundante opslag

Georedundante opslag (GRS) kopieert uw gegevens drie keer synchroon binnen één fysieke locatie in het primaire gebied met LRS. Vervolgens worden uw gegevens asynchroon gekopieerd naar één fysieke locatie in een secundaire regio die zich honderden kilometers verwijderd is van het primaire gebied. GRS biedt duurzaamheid voor Azure Storage-gegevensobjecten van ten minste 9999999999999999% (16 9's) over een bepaald jaar.

Een schrijfbewerking wordt eerst vastgelegd op de primaire locatie en gerepliceerd met LRS. De update wordt vervolgens asynchroon gerepliceerd naar het secundaire gebied. Wanneer gegevens naar de secundaire locatie worden geschreven, wordt deze ook gerepliceerd binnen die locatie met LRS.

### <a name="geo-zone-redundant-storage-preview"></a>Opslag die is overbodig in geozone (voorbeeld)

Geo-zoneredundante opslag (GZRS) (preview) combineert de hoge beschikbaarheid van redundantie in beschikbaarheidszones met bescherming tegen regionale storingen door geo-replicatie. Gegevens in een GZRS-opslagaccount worden gekopieerd over drie [Azure-beschikbaarheidszones](../../availability-zones/az-overview.md) in de primaire regio en worden ook gerepliceerd naar een secundaire geografische regio voor bescherming tegen regionale rampen. Microsoft raadt aan GZRS te gebruiken voor toepassingen die maximale consistentie, duurzaamheid en beschikbaarheid vereisen, uitstekende prestaties en veerkracht voor herstel na noodgevallen.

Met een GZRS-opslagaccount u gegevens blijven lezen en schrijven als een beschikbaarheidszone niet beschikbaar wordt of niet kan worden hersteld. Bovendien zijn uw gegevens ook duurzaam in het geval van een volledige regionale storing of een ramp waarbij de primaire regio niet kan worden hersteld. GZRS is ontworpen om ten minste 99.9999999999999999% (16 9's) duurzaamheid van objecten over een bepaald jaar.

Alleen producten voor v2-opslag voor algemene doeleinden ondersteunen GZRS en RA-GZRS. Zie [Overzicht van Azure-opslagaccounts](storage-account-overview.md) voor meer informatie over de typen opslagaccounts. GZRS en RA-GZRS ondersteunen blokblobs, paginablobs (behalve VHD-schijven), bestanden, tabellen en wachtrijen.

GZRS en RA-GZRS zijn momenteel beschikbaar voor preview in de volgende regio's:

- Azië - zuidoost
- Europa - noord
- Europa - west
- Japan - oost
- Verenigd Koninkrijk Zuid
- US - oost
- US - oost 2
- US - centraal
- US - west 2

Microsoft blijft GZRS en RA-GZRS inschakelen in andere Azure-regio's. Controleer regelmatig de pagina [Azure Service Updates](https://azure.microsoft.com/updates/) voor informatie over ondersteunde regio's.

Zie voor informatie over preview-prijzen de prijzen van GZRS-voorbeeld voor [Blobs](https://azure.microsoft.com/pricing/details/storage/blobs), [Bestanden](https://azure.microsoft.com/pricing/details/storage/files/), [Wachtrijen](https://azure.microsoft.com/pricing/details/storage/queues/)en [Tabellen](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft raadt aan om geen preview-functies te gebruiken voor productieworkloads.

## <a name="read-access-to-data-in-the-secondary-region"></a>Toegang tot gegevens in het secundaire gebied lezen

Georedundante opslag (met GRS of GZRS) repliceert uw gegevens naar een andere fysieke locatie in de secundaire regio om te beschermen tegen regionale storingen. Deze gegevens kunnen echter alleen worden gelezen als de klant of Microsoft een failover van de primaire naar de secundaire regio initieert. Wanneer u leestoegang tot het secundaire gebied inschakelt, zijn uw gegevens beschikbaar om te worden gelezen als de primaire regio niet beschikbaar is. Voor leestoegang tot de secundaire regio u georedundante opslag (RA-GRS) of geo-zoneredundante opslag (RA-GZRS) voor leestoegang tot de secundaire regio inschakelen.

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Ontwerp uw toepassingen voor leestoegang tot de secundaire

Als uw opslagaccount is geconfigureerd voor leestoegang tot het secundaire gebied, u uw toepassingen ontwerpen om naadloos over te schakelen naar leesgegevens uit het secundaire gebied als het primaire gebied om welke reden dan ook niet meer beschikbaar is. De secundaire regio is altijd beschikbaar voor leestoegang, zodat u uw toepassing testen om ervoor te zorgen dat deze wordt gelezen van de secundaire in het geval van een storing. Zie [Het ontwerpen van zeer beschikbare toepassingen met behulp van georedundante opslag voor leestoegang.](storage-designing-ha-apps-with-ragrs.md)

Wanneer leestoegang tot het secundaire is ingeschakeld, kunnen uw gegevens worden gelezen vanaf het secundaire eindpunt en vanaf het primaire eindpunt voor uw opslagaccount. Het secundaire eindpunt voegt het achtervoegsel toe *– secundair* aan de accountnaam. Als uw primaire eindpunt voor Blob-opslag bijvoorbeeld is, `myaccount.blob.core.windows.net` `myaccount-secondary.blob.core.windows.net`is het secundaire eindpunt . De accounttoegangssleutels voor uw opslagaccount zijn hetzelfde voor zowel de primaire als de secundaire eindpunten.

### <a name="check-the-last-sync-time-property"></a>De eigenschap Laatst gesynchroniseerd controleren

Omdat gegevens asynchroon worden gerepliceerd naar het secundaire gebied, loopt het secundaire gebied vaak achter op het primaire gebied. Als er een fout optreedt in het primaire gebied, is het waarschijnlijk dat alle schrijfbewerkingen naar de primaire nog niet zijn gerepliceerd naar de secundaire.

Als u wilt bepalen welke schrijfbewerkingen zijn gerepliceerd naar de secundaire regio, kan uw toepassing de eigenschap **Laatste synchronisatietijd** controleren voor uw opslagaccount. Alle schrijfbewerkingen die vóór de laatste synchronisatietijd naar het primaire gebied zijn geschreven, zijn met succes gerepliceerd naar het secundaire gebied, wat betekent dat ze beschikbaar zijn om te worden gelezen vanaf de secundaire. Schrijfbewerkingen die na de laatste synchronisatietijd naar het primaire gebied zijn geschreven, kunnen al dan niet zijn gerepliceerd naar het secundaire gebied, wat betekent dat ze mogelijk niet beschikbaar zijn voor leesbewerkingen.

U de waarde van de eigenschap **Last Sync Time** opvragen met Azure PowerShell, Azure CLI of een van de Azure Storage-clientbibliotheken. De eigenschap **Laatste synchronisatietijd** is een GMT-datum/-tijdwaarde. Zie [De eigenschap Laatste synchronisatietijd controleren voor een opslagaccount](last-sync-time-get.md)voor meer informatie .

## <a name="summary-of-redundancy-options"></a>Overzicht van redundantieopties

In de volgende tabel ziet u hoe duurzaam en beschikbaar uw gegevens zijn in een bepaald scenario, afhankelijk van welk type redundantie van kracht is voor uw opslagaccount:

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (preview)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Een knooppunt in een datacenter is niet beschikbaar                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Een volledig datacenter (zonal of niet-zonale) wordt niet beschikbaar                                           | Nee                              | Ja                              | Ja                                  | Ja                                  |
| Een regio-brede storing optreedt                                                                                     | Nee                              | Nee                               | Ja                                  | Ja                                  |
| Toegang tot gegevens in het secundaire gebied lezen als de primaire regio niet beschikbaar is | Nee                              | Nee                               | Ja (met RA-GRS)                                   | Ja (met RA-GZRS)                                 |
| Procent duurzaamheid van objecten over een bepaald jaar<sup>1</sup>                                          | ten minste 99.999999999% (11 9's) | ten minste 99.9999999999% (12 9's) | ten minste 999999999999999999% (16 9's) | ten minste 999999999999999999% (16 9's) |
| Ondersteunde opslagaccounttypen<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, Bestandsopslag                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Beschikbaarheid SLA voor leesaanvragen<sup>1</sup>  | Ten minste 99,9% (99% voor koele toegangslaag) | Ten minste 99,9% (99% voor koele toegangslaag) | Ten minste 99,9% (99% voor koele toegangslaag) voor GRS<br /><br />Ten minste 99,99% (99,9% voor koele toegangslaag) voor RA-GRS | Ten minste 99,9% (99% voor koele toegangslaag) voor GZRS<br /><br />Ten minste 99,99% (99,9% voor koele toegangslaag) voor RA-GZRS |
| Beschikbaarheid SLA voor schrijfaanvragen<sup>1</sup>  | Ten minste 99,9% (99% voor koele toegangslaag) | Ten minste 99,9% (99% voor koele toegangslaag) | Ten minste 99,9% (99% voor koele toegangslaag) | Ten minste 99,9% (99% voor koele toegangslaag) |

<sup>1</sup> Zie de SLA azure [storage](https://azure.microsoft.com/support/legal/sla/storage/)voor azure storage voor duurzaamheid en beschikbaarheid voor informatie over azure-opslaggaranties voor duurzaamheid en beschikbaarheid.

<sup>2</sup> Zie [Overzicht van opslagaccount](storage-account-overview.md)voor informatie over opslagaccounttypen.

Alle gegevens voor alle soorten opslagaccounts worden gekopieerd volgens de redundantieoptie voor het opslagaccount. Objecten zoals blokblobs, toevoegenblobs, paginablobs, wachtrijen, tabellen en bestanden worden gekopieerd.

Zie [Azure Storage-prijzen](https://azure.microsoft.com/pricing/details/storage/)voor prijsinformatie voor elke redundantieoptie.

> [!NOTE]
> Azure Premium Disk Storage ondersteunt momenteel alleen lokaal redundante opslag (LRS). Blokblobopslagaccounts ondersteunen lokaal redundante opslag (LRS) en zone redundante opslag (ZRS) in bepaalde regio's.

## <a name="data-integrity"></a>Gegevensintegriteit

Azure Storage controleert regelmatig de integriteit van gegevens die zijn opgeslagen met behulp van cyclische redundantiecontroles (CRC's). Als gegevensbeschadiging wordt gedetecteerd, wordt deze gerepareerd met behulp van redundante gegevens. Azure Storage berekent ook checksums op al het netwerkverkeer om beschadiging van gegevenspakketten op te sporen bij het opslaan of ophalen van gegevens.

## <a name="see-also"></a>Zie ook

- [De eigenschap Laatste synchronisatietijd controleren voor een opslagaccount](last-sync-time-get.md)
- [De redundantieoptie voor een opslagaccount wijzigen](redundancy-migration.md)
- [Ontwerpen van zeer beschikbare toepassingen met RA-GRS Storage](../storage-designing-ha-apps-with-ragrs.md)
- [Herstel na noodgevallen en failover van het account (voorbeeld)](storage-disaster-recovery-guidance.md)
