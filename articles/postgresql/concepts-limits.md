---
title: Limieten - Azure Database voor PostgreSQL - Single Server
description: In dit artikel worden limieten beschreven in Azure Database voor PostgreSQL - Single Server, zoals het aantal verbindings- en opslagengineopties.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836453"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Limieten in Azure-database voor PostgreSQL - Enkele server
In de volgende secties worden capaciteits- en functionele limieten in de databaseservice beschreven. Zie het artikel over [resourcelagen](concepts-pricing-tiers.md) (compute, memory, storage) als u meer wilt weten over resourcelagen (compute, memory, storage).


## <a name="maximum-connections"></a>Maximum aantal verbindingen
Het maximum aantal verbindingen per prijscategorie en vCores wordt hieronder weergegeven. Het Azure-systeem vereist vijf verbindingen om de Azure Database voor PostgreSQL-server te controleren. 

|**Prijsniveau**| **vCore(s)**| **Maximum aantal verbindingen** | **Maximale gebruikersverbindingen** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Algemeen gebruik| 2| 150| 145|
|Algemeen gebruik| 4| 250| 245|
|Algemeen gebruik| 8| 480| 475|
|Algemeen gebruik| 16| 950| 945|
|Algemeen gebruik| 32| 1500| 1495|
|Algemeen gebruik| 64| 1900| 1895|
|Geoptimaliseerd geheugen| 2| 300| 295|
|Geoptimaliseerd geheugen| 4| 500| 495|
|Geoptimaliseerd geheugen| 8| 960| 955|
|Geoptimaliseerd geheugen| 16| 1900| 1895|
|Geoptimaliseerd geheugen| 32| 1987| 1982|

Wanneer verbindingen de limiet overschrijden, ontvangt u mogelijk de volgende fout:
> FATAAL: sorry, al te veel klanten

> [!IMPORTANT]
> Voor de beste ervaring raden we u aan een verbindingspooler zoals pgBouncer te gebruiken om verbindingen efficiënt te beheren.

Een PostgreSQL-verbinding, zelfs idle, kan ongeveer 10 MB geheugen in beslag nemen. Ook het maken van nieuwe verbindingen kost tijd. De meeste toepassingen vragen om veel kortstondige verbindingen, die deze situatie verbindingen. Het resultaat is minder resources beschikbaar voor uw werkelijke werklast, wat leidt tot verminderde prestaties. Een verbindingspooler die niet-actieve verbindingen vermindert en bestaande verbindingen opnieuw gebruikt, helpt dit te voorkomen. Voor meer informatie, bezoek onze [blogpost](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Functionele beperkingen
### <a name="scale-operations"></a>Schaalbewerkingen
- Dynamische schaling van en naar de basisprijsniveaus wordt momenteel niet ondersteund.
- De kleinerende serveropslagwordt momenteel niet ondersteund.

### <a name="server-version-upgrades"></a>Serverversie-upgrades
- Geautomatiseerde migratie tussen belangrijke database-engineversies wordt momenteel niet ondersteund. Als u wilt upgraden naar de volgende grote versie, neem een [dump en het te herstellen](./howto-migrate-using-dump-and-restore.md) naar een server die is gemaakt met de nieuwe engine versie.

> Merk op dat voorafgaand aan PostgreSQL versie 10, de [PostgreSQL versiebeleid](https://www.postgresql.org/support/versioning/) beschouwd als een _belangrijke versie_ upgrade als een toename van het eerste _of_ tweede nummer (bijvoorbeeld, 9,5 tot 9.6 werd beschouwd als een _belangrijke_ versie upgrade).
> Vanaf versie 10 wordt alleen een wijziging in het eerste getal beschouwd als een belangrijke versie-upgrade (bijvoorbeeld 10,0 tot 10,1 is een _kleine_ versie-upgrade en 10 tot 11 is een _belangrijke_ versie-upgrade).

### <a name="vnet-service-endpoints"></a>VNet-service-eindpunten
- Ondersteuning voor VNet-serviceeindpunten is alleen voor servers met algemeen gebruik en geheugengeoptimaliseerd.

### <a name="restoring-a-server"></a>Een server herstellen
- Bij het gebruik van de PITR-functie wordt de nieuwe server gemaakt met dezelfde configuraties op het prijsniveau als de server waarop deze is gebaseerd.
- De nieuwe server die tijdens een herstel is gemaakt, heeft niet de firewallregels die op de oorspronkelijke server bestonden. Firewallregels moeten afzonderlijk worden ingesteld voor deze nieuwe server.
- Het herstellen van een verwijderde server wordt niet ondersteund.

### <a name="utf-8-characters-on-windows"></a>UTF-8-tekens in Windows
- In sommige scenario's worden UTF-8-tekens niet volledig ondersteund in open source PostgreSQL op Windows, wat van invloed is op Azure Database voor PostgreSQL. Zie de thread op [Bug #15476 in het postgresql-archief](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Begrijpen [wat er beschikbaar is in elke prijscategorie](concepts-pricing-tiers.md)
- Meer informatie over [ondersteunde PostgreSQL-databaseversies](concepts-supported-versions.md)
- Controleren [hoe u een back-up maken en een server in Azure Database voor PostgreSQL herstellen met behulp van de Azure-portal](howto-restore-server-portal.md)
