---
title: Gegevens redundantie in Azure Storage | Microsoft Docs
description: Gegevens in uw Microsoft Azure Storage-account worden gerepliceerd voor duurzaamheid en maximale Beschik baarheid. Redundantie opties zijn lokaal redundante opslag (LRS), zone-redundante opslag (ZRS), geografisch redundante opslag (GRS), geografisch redundante opslag met lees toegang (RA-GRS), geo-zone-redundante opslag (GZRS) (preview) en geografische zone met lees toegang-redundante opslag (RA-GZRS) (preview).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 7517c4d9b3f9b58d9cf745f5001078837e1fbfea
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748174"
---
# <a name="azure-storage-redundancy"></a>Azure Storage redundantie

De gegevens in uw Microsoft Azure Storage-account worden altijd gerepliceerd om duurzaamheid en hoge beschikbaarheid te garanderen. Azure Storage kopieert uw gegevens, zodat deze worden beveiligd tegen geplande en niet-geplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk-of energie storingen en enorme natuur rampen. U kunt ervoor kiezen om uw gegevens te repliceren binnen hetzelfde Data Center, op zonegebonden-data centrums binnen dezelfde regio of in geografisch gescheiden regio's.

Replicatie zorgt ervoor dat uw opslagaccount voldoet aan de [Service-Level Agreement (SLA) voor opslag](https://azure.microsoft.com/support/legal/sla/storage/), zelfs wanneer er fouten optreden. Raadpleeg de SLA voor informatie over de garanties van Azure Storage voor duurzaamheid en beschikbaarheid.

Azure Storage controleert regel matig de integriteit van gegevens die zijn opgeslagen met behulp van cyclische redundantie controles (CRCs). Als beschadigde gegevens worden gedetecteerd, wordt deze hersteld met behulp van redundante gegevens. Azure Storage berekent ook de controle sommen op al het netwerk verkeer om beschadiging van gegevens pakketten te detecteren bij het opslaan of ophalen van gegevens.

## <a name="choosing-a-redundancy-option"></a>Een optie voor redundantie kiezen

Wanneer u een opslag account maakt, kunt u een van de volgende redundantie opties selecteren:

[!INCLUDE [azure-storage-redundancy](../../../includes/azure-storage-redundancy.md)]

De volgende tabel geeft een beknopt overzicht van het bereik van duurzaamheid en beschik baarheid dat elke replicatie strategie biedt voor een bepaald type gebeurtenis (of gebeurtenis van vergelijk bare gevolgen).

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (preview-versie)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Niet-beschik baarheid van knoop punt binnen een Data Center                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Een volledig Data Center (zonegebonden of niet-zonegebonden) is niet meer beschikbaar                                           | Nee                              | Ja                              | Ja                                  | Ja                                  |
| Een onderbreking in de gehele regio                                                                                     | Nee                              | Nee                               | Ja                                  | Ja                                  |
| Lees toegang tot uw gegevens (in een externe, geo-gerepliceerde regio) in het geval van regionale niet-beschik baarheid | Nee                              | Nee                               | Ja (met RA-GRS)                                   | Ja (met RA-GZRS)                                 |
| Ontworpen om \_\_ duurzaamheid van objecten over een bepaald jaar te bieden<sup>1</sup>                                          | ten minste 99,999999999% (11 9) | ten minste 99,9999999999% (12 9) | ten minste 99.99999999999999% (16 9) | ten minste 99.99999999999999% (16 9) |
| Ondersteunde typen opslag accounts<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| SLA voor Beschik baarheid voor lees aanvragen<sup>1</sup>  | Ten minste 99,9% (99% voor de laag van de cool-toegang) | Ten minste 99,9% (99% voor de laag van de cool-toegang) | Ten minste 99,9% (99% voor de laag met coole toegang) voor GRS<br /><br />Ten minste 99,99% (99,9% voor de laag voor cool-toegang) voor RA-GRS | Ten minste 99,9% (99% voor de laag met coole toegang) voor GZRS<br /><br />Ten minste 99,99% (99,9% voor de laag voor cool-toegang) voor RA-GZRS |
| SLA voor Beschik baarheid voor schrijf aanvragen<sup>1</sup>  | Ten minste 99,9% (99% voor de laag van de cool-toegang) | Ten minste 99,9% (99% voor de laag van de cool-toegang) | Ten minste 99,9% (99% voor de laag van de cool-toegang) | Ten minste 99,9% (99% voor de laag van de cool-toegang) |

<sup>1</sup> zie de [Azure Storage Sla](https://azure.microsoft.com/support/legal/sla/storage/)voor informatie over Azure Storage garanties voor duurzaamheid en beschik baarheid.   

<sup>2</sup> Zie [overzicht van opslag accounts](storage-account-overview.md)voor meer informatie over typen opslag accounts.

Alle gegevens voor alle typen opslag accounts worden gerepliceerd, inclusief blok-blobs, toevoeg-blobs, pagina-blobs, wacht rijen, tabellen en bestanden.

Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor prijs informatie voor elke optie voor redundantie.

> [!NOTE]
> Azure Premium Disk Storage ondersteunt momenteel alleen lokaal redundante opslag (LRS). Azure Premium Block Blob Storage ondersteunt lokaal redundante opslag (LRS) en zone redundante opslag (ZRS) in bepaalde regio's.

## <a name="changing-replication-strategy"></a>Replicatie strategie wijzigen

U kunt de replicatie strategie van uw opslag account wijzigen met behulp van de [Azure Portal](https://portal.azure.com/), [Azure Power shell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)of een van de [Azure Storage-client bibliotheken](https://docs.microsoft.com/azure/index#pivot=sdkstools). Het wijzigen van het replicatie type van uw opslag account resulteert niet in de tijd.

> [!NOTE]
> Op dit moment kunt u de Azure Portal of de Azure Storage-client bibliotheken niet gebruiken om uw account om te zetten in ZRS, GZRS of RA-GZRS. Als u uw account wilt migreren naar ZRS, raadpleegt u [zone-redundante opslag (ZRS) voor het bouwen van Maxi maal beschik bare Azure Storage toepassingen](storage-redundancy-zrs.md) voor meer informatie. Zie [geo-zone-redundante opslag voor hoge Beschik baarheid en maximale duurzaamheid (preview)](storage-redundancy-zrs.md) voor meer informatie over het migreren van GZRS of Ra-GZRS.

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Zijn er kosten verbonden aan het wijzigen van de replicatie strategie van mijn account?

Dit is afhankelijk van het pad van de conversie. Bestel van minst naar het duurste, Azure Storage redundantie aanbod LRS, ZRS, GRS, RA-GRS, GZRS en RA-GZRS. Als u bijvoorbeeld *van* LRS naar een ander type replicatie gaat, worden er extra kosten in rekening gebracht, omdat u overstapt naar een geavanceerd redundantie niveau. Als u migreert *naar* GRS of Ra-GRS, worden er kosten in rekening gebracht voor de uitgaande band breedte, omdat uw gegevens (in de primaire regio) worden gerepliceerd naar uw externe secundaire regio. Deze kosten zijn tijdens de eerste Setup eenmalige kosten. Nadat de gegevens zijn gekopieerd, worden er geen verdere migratie kosten in rekening gebracht. Er worden alleen kosten in rekening gebracht voor het repliceren van nieuwe of bijgewerkte bestaande gegevens. Zie [Azure Storage-pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de bandbreedte kosten.

Als u uw opslag account migreert van GRS naar LRS, zijn er geen extra kosten, maar worden uw gerepliceerde gegevens verwijderd van de secundaire locatie.

Als u uw opslag account migreert van RA-GRS naar GRS of LRS, wordt dat account gefactureerd als RA-GRS voor een extra periode van 30 dagen na de datum waarop deze is geconverteerd.

## <a name="see-also"></a>Zie ook

- [Overzicht van opslag account](storage-account-overview.md)
- [Lokaal redundante opslag (LRS): lage kosten voor gegevens redundantie voor Azure Storage](storage-redundancy-lrs.md)
- [Zone-redundante opslag (ZRS): Maxi maal beschik bare Azure Storage toepassingen](storage-redundancy-zrs.md)
- [Geografisch redundante opslag (GRS): cross-regionale replicatie voor Azure Storage](storage-redundancy-grs.md)
- [Geo-zone-redundante opslag (GZRS) voor hoge Beschik baarheid en maximale duurzaamheid (preview-versie)](storage-redundancy-gzrs.md)
- [Schaalbaarheids-en prestatie doelen voor standaard opslag accounts](scalability-targets-standard-account.md)
- [Maxi maal beschik bare toepassingen ontwerpen met behulp van RA-GRS-opslag](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundantie opties en geografisch redundante opslag met lees toegang](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper-Azure Storage: een Maxi maal beschik bare Cloud opslag service met sterke consistentie](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
