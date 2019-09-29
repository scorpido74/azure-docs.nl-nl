---
title: Geografisch redundante opslag (GRS) voor meerdere regionale duurzaamheid in Azure Storage | Microsoft Docs
description: Geografisch redundante opslag (GRS) repliceert uw gegevens tussen twee regio's die honderden kilo meters uit elkaar liggen. GRS beschermt tegen hardwarestoringen in het Data Center en regionale rampen.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 16a5f214495025d16d10ee01a7b2a40b78f7a17a
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670811"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Geografisch redundante opslag (GRS): Kruis regionale replicatie voor Azure Storage
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Geografisch redundante opslag met lees toegang
Geografisch redundante opslag met lees toegang (RA-GRS) maximaliseert de beschik baarheid voor uw opslag account. RA-GRS biedt alleen-lezen toegang tot de gegevens op de secundaire locatie, naast geo-replicatie tussen twee regio's.

Wanneer u alleen-lezen toegang tot uw gegevens in de secundaire regio inschakelt, zijn uw gegevens beschikbaar op een secundair eind punt en op het primaire eind punt voor uw opslag account. Het secundaire eind punt is vergelijkbaar met het primaire eind punt, maar voegt het achtervoegsel `–secondary` toe aan de account naam. Als uw primaire eind punt voor de Blob service bijvoorbeeld `myaccount.blob.core.windows.net` is, is het secundaire eind punt `myaccount-secondary.blob.core.windows.net`. De toegangs sleutels voor uw opslag account zijn hetzelfde voor zowel de primaire als de secundaire eind punten.

Er zijn enkele overwegingen waarmee u rekening moet houden bij het gebruik van RA-GRS:

* Uw toepassing moet beheren met welk eind punt de interactie wordt gebruikt bij het gebruik van RA-GRS.
* Omdat asynchrone replicatie een vertraging vergt, kunnen wijzigingen die nog niet zijn gerepliceerd naar de secundaire regio verloren gaan als gegevens niet kunnen worden hersteld vanuit de primaire regio.
* U kunt de laatste synchronisatie tijd van uw opslag account controleren. Tijd van laatste synchronisatie is een GMT-datum/-tijd waarde. Alle primaire schrijf bewerkingen vóór de laatste synchronisatie tijd zijn naar de secundaire locatie geschreven, wat betekent dat ze kunnen worden gelezen vanaf de secundaire locatie. Primaire schrijf bewerkingen na het tijdstip van de laatste synchronisatie zijn mogelijk nog niet beschikbaar voor lees bewerkingen. U kunt deze waarde opvragen met behulp van de [Azure Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md)of van een van de Azure Storage-client bibliotheken.
* Als u een failover van een account (preview) van een GRS-of RA-GRS-account naar de secundaire regio initieert, wordt de schrijf toegang voor dat account hersteld nadat de failover is voltooid. Zie [nood herstel en failover van het opslag account (preview)](storage-disaster-recovery-guidance.md)voor meer informatie.
* RA-GRS is bedoeld voor gebruik met hoge Beschik baarheid. Raadpleeg de [controle lijst voor prestaties](storage-performance-checklist.md)voor richt lijnen voor schaal baarheid.
* Zie voor meer informatie over het ontwerpen voor hoge Beschik baarheid met RA-GRS [ontwerpen van Maxi maal beschik bare toepassingen met Ra-GRS-opslag](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Wat is de RPO en RTO met GRS?

**Beoogd herstel punt (RPO):** In GRS en RA-GRS de opslag service asynchroon geo-repliceert de gegevens van de primaire naar de secundaire locatie. In het geval dat de primaire regio niet beschikbaar is, kunt u een account-failover (preview) uitvoeren naar de secundaire regio. Wanneer u een failover initieert, kunnen recente wijzigingen die nog niet geografisch zijn gerepliceerd, verloren gaan. Het aantal minuten van potentiële gegevens dat is verloren, wordt de RPO genoemd. De RPO geeft het tijdstip aan waarop gegevens kunnen worden hersteld. Azure Storage heeft doorgaans een RPO van minder dan 15 minuten, maar er is momenteel geen SLA over hoe lang geo-replicatie in beslag neemt.

**Beoogde herstel tijd (RTO):** De RTO is een meting van hoe lang het duurt om de failover uit te voeren en het opslag account weer online te halen. De tijd voor het uitvoeren van de failover omvat de volgende acties:

   * De tijd tot de klant de failover van het opslag account van de primaire naar de secundaire regio initieert.
   * De tijd die door Azure is vereist voor het uitvoeren van de failover door de primaire DNS-vermeldingen te wijzigen zodat deze naar de secundaire locatie wijzen.

## <a name="paired-regions"></a>Gekoppelde regio's 
Wanneer u een opslag account maakt, selecteert u de primaire regio voor het account. De gekoppelde secundaire regio wordt bepaald op basis van de primaire regio en kan niet worden gewijzigd. Zie [Business continuïteit en herstel na nood gevallen (BCDR) voor actuele informatie over regio's die door Azure worden ondersteund. Gekoppelde Azure-regio's](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Zie ook
- [Azure Storage-replicatie](storage-redundancy.md)
- [Lokaal redundante opslag (LRS): Lage kosten voor gegevens redundantie voor Azure Storage](storage-redundancy-lrs.md)
- [Zone-redundante opslag (ZRS): Maxi maal beschik bare Azure Storage toepassingen](storage-redundancy-zrs.md)
