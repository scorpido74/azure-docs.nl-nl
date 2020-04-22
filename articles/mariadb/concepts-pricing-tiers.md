---
title: Prijscategorieën - Azure Database for MariaDB
description: Meer informatie over de verschillende prijsniveaus voor Azure Database voor MariaDB, waaronder rekengeneraties, opslagtypen, opslaggrootte, vCores, geheugen en bewaartermijnen voor back-ups.
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9c057bebf94362b3b9e42db9d311e99f1e35c651
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770133"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database voor MariaDB-prijslagen

U een Azure-database voor MariaDB-server maken in een van de drie verschillende prijsniveaus: Basic, General Purpose en Memory Optimized. De prijsniveaus worden gedifferentieerd naar de hoeveelheid compute in vCores die kunnen worden ingericht, geheugen per vCore en de opslagtechnologie die wordt gebruikt om de gegevens op te slaan. Alle resources zijn ingericht op MariaDB-serverniveau. Een server kan een of meerdere databases hebben.

|    | **Basic** | **Algemeen doel** | **Geheugen geoptimaliseerd** |
|:---|:----------|:--------------------|:---------------------|
| Compute generatie | Gen 5 |Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Geheugen per vCore | 2 GB | 5 GB | 10 GB |
| Opslaggrootte | 5 GB tot 1 TB | 5 GB tot 4 TB | 5 GB tot 4 TB |
| Bewaarperiode voor databaseback-ups | 7 tot 35 dagen | 7 tot 35 dagen | 7 tot 35 dagen |

Als u een prijscategorie wilt kiezen, gebruikt u de volgende tabel als uitgangspunt.

| Prijscategorie | Beoogde workloads |
|:-------------|:-----------------|
| Basic | Workloads die lichte rekenkracht en I/O-prestaties vereisen. Voorbeelden hiervan zijn servers die worden gebruikt voor ontwikkeling of testen of kleinschalige niet-gebruikte toepassingen. |
| Algemeen gebruik | De meeste zakelijke workloads die een evenwichtige rekenkracht en geheugen vereisen met schaalbare I/O-doorvoer. Voorbeelden hiervan zijn servers voor het hosten van web- en mobiele apps en andere bedrijfstoepassingen.|
| Geoptimaliseerd geheugen | Krachtige databaseworkloads die in-memory prestaties vereisen voor snellere transactieverwerking en hogere gelijktijdigheid. Voorbeelden hiervan zijn servers voor het verwerken van realtime gegevens en krachtige transactionele of analytische apps.|

Nadat u een server hebt gemaakt, kunnen het aantal vCores en de prijscategorie (behalve van en naar Basic) binnen enkele seconden omhoog of omlaag worden gewijzigd. U ook zelfstandig de hoeveelheid opslagruimte en de back-upbewaarperiode omhoog of omlaag aanpassen zonder downtime van de toepassing. U het type back-upopslag niet wijzigen nadat een server is gemaakt. Zie de sectie [Schaalbronnen](#scale-resources) voor meer informatie.

## <a name="compute-generations-and-vcores"></a>Rekengeneraties en vCores

Compute resources worden geleverd als vCores, die de logische CPU van de onderliggende hardware vertegenwoordigen. Gen 5 logische CPU's zijn gebaseerd op Intel E5-2673 v4 (Broadwell) 2,3-GHz processors.

## <a name="storage"></a>Storage

De opslag die u indient, is de hoeveelheid opslagcapaciteit die beschikbaar is voor uw Azure Database voor MariaDB-server. De opslag wordt gebruikt voor de databasebestanden, tijdelijke bestanden, transactielogboeken en de MariaDB-serverlogboeken. De totale hoeveelheid opslagruimte die u indient, definieert ook de I/O-capaciteit die beschikbaar is voor uw server.

|    | **Basic** | **Algemeen doel** | **Geheugen geoptimaliseerd** |
|:---|:----------|:--------------------|:---------------------|
| Opslagtype | Basisopslag | Opslag voor algemeen gebruik | Opslag voor algemeen gebruik |
| Opslaggrootte | 5 GB tot 1 TB | 5 GB tot 4 TB | 5 GB tot 4 TB |
| Omvang opslagverhoging | 1 GB | 1 GB | 1 GB |
| IOPS | Variabele |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS |

U extra opslagcapaciteit toevoegen tijdens en na het maken van de server en het systeem toestaan om de opslag automatisch te laten groeien op basis van het opslagverbruik van uw werkbelasting.

>[!NOTE]
> Opslag kan alleen worden opgeschaald, niet naar beneden.

De Basic-laag biedt geen IOPS-garantie. In de prijzenlagen Voor algemeen gebruik en geheugen geoptimaliseerd, wordt de IOPS-schaal uitgevoerd met de ingerichte opslaggrootte in een verhouding van 3:1.

U uw I/O-verbruik controleren in de Azure-portal of met Azure CLI-opdrachten. De relevante statistieken die moeten worden gecontroleerd zijn [opslaglimiet, opslagpercentage, gebruikte opslag en IO-percentage.](concepts-monitoring.md)

### <a name="reaching-the-storage-limit"></a>Het bereiken van de opslaglimiet

Servers met minder dan 100 GB ingerichte opslag zijn alleen-lezen gemarkeerd als de gratis opslag minder dan 5% van de ingerichte opslaggrootte bedraagt. Servers met meer dan 100 GB ingerichte opslag worden als alleen-lezen gemarkeerd als de vrije opslag minder is dan 5 GB.

Als u bijvoorbeeld 110 GB opslagruimte hebt ingericht en het werkelijke gebruik meer dan 105 GB bedraagt, wordt de server alleen-lezen gemarkeerd. Als u 5 GB opslagruimte hebt ingericht, is de server alleen als alleen-lezen gemarkeerd wanneer de gratis opslag minder dan 256 MB bedraagt.

Terwijl de service probeert om de server alleen-lezen te maken, worden alle nieuwe transactieaanvragen voor schrijven geblokkeerd en worden bestaande actieve transacties verder uitgevoerd. Indien de server op alleen-lezen is ingesteld, zullen alle daaropvolgende schrijfbewerkingen en transactiedoorvoeringen mislukken. Leesquery’s blijven gewoon werken. Pas nadat u de ingerichte opslagruimte hebt vergroot, is de server weer klaar om nieuwe schrijftransacties te accepteren.

We raden u aan de opslag automatisch te laten groeien in of een waarschuwing in te stellen om u op de hoogte te stellen wanneer uw serveropslag de drempel nadert, zodat u niet in de alleen-lezenstatus komen. Zie voor meer informatie de documentatie over [het instellen van een waarschuwing](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Opslag automatisch groeien

Opslagauto-grow voorkomt dat uw server geen opslagruimte meer heeft en alleen-lezen wordt. Als de opslagautomatisch wordt ingeschakeld, wordt de opslag automatisch groter zonder dat dit gevolgen heeft voor de werkbelasting. Voor servers met minder dan 100 GB ingerichte opslag wordt de ingerichte opslaggrootte met 5 GB vergroot wanneer de gratis opslag lager is dan 10% van de ingerichte opslag. Voor servers met meer dan 100 GB ingerichte opslag wordt de ingerichte opslaggrootte met 5% vergroot wanneer de vrije opslagruimte lager is dan 10 GB van de ingerichte opslaggrootte. De hierboven vermelde maximale opslaglimieten zijn van toepassing.

Als u bijvoorbeeld 1000 GB opslagruimte hebt ingericht en het werkelijke gebruik meer dan 990 GB bedraagt, wordt de opslaggrootte van de server verhoogd tot 1050 GB. Als u 10 GB opslagruimte hebt ingericht, wordt de opslaggrootte verhoogd tot 15 GB wanneer minder dan 1 GB opslagruimte gratis is.

Vergeet niet dat opslag alleen kan worden opgeschaald, niet naar beneden.

## <a name="backup"></a>Backup

De service maakt automatisch back-ups van uw server. U een bewaartermijn selecteren uit een bereik van 7 tot 35 dagen. Servers met algemene toepassing en geheugen geoptimaliseerde servers kunnen ervoor kiezen om geo-redundante opslag voor back-ups te hebben. Meer informatie over back-ups in het [conceptartikel](concepts-backup.md).

## <a name="scale-resources"></a>Resources omhoog/omlaag schalen

Nadat u uw server hebt gemaakt, u zelfstandig de vCores, de prijscategorie (behalve basic), de hoeveelheid opslagruimte en de back-upbewaarperiode wijzigen. U het type back-upopslag niet wijzigen nadat een server is gemaakt. Het aantal vCores kan worden opgeschaald of omlaag. De back-upbewaarperiode kan worden opgeschaald of omlaag van 7 naar 35 dagen. De opslaggrootte kan alleen maar worden vergroot. Schalen van de resources kan via de portal of Azure CLI. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Wanneer u het aantal vCores of de prijscategorie wijzigt, wordt een kopie van de oorspronkelijke server gemaakt met de nieuwe rekentoewijzing. Wanneer de nieuwe server actief is, worden de verbindingen hiernaartoe overgeschakeld. Op het moment dat het systeem overschakelt naar de nieuwe server, kunnen er geen nieuwe verbindingen worden vastgelegd en worden alle niet-doorgevoerde transacties teruggedraaid. Dit tijdvenster is variabel, maar is in de meeste gevallen minder dan een minuut.

Het schalen van opslag en het wijzigen van de back-upbewaarperiode zijn echte onlinebewerkingen. Er is geen downtime en uw toepassing wordt niet beïnvloed. Als IOPS-schaal met de grootte van de ingerichte opslag, u het IOPS dat beschikbaar is voor uw server vergroten door de opslag op te schalen.

## <a name="pricing"></a>Prijzen

Zie de pagina serviceprijzen voor de meest actuele [prijsinformatie.](https://azure.microsoft.com/pricing/details/mariadb/) Als u de gewenste kosten wilt zien voor de gewenste configuratie, [worden](https://portal.azure.com/#create/Microsoft.MariaDBServer) de maandelijkse kosten weergegeven op het tabblad **Prijslaag** op basis van de opties die u selecteert. Als u geen Azure-abonnement hebt, u de azure-prijscalculator gebruiken om een geschatte prijs te krijgen. Selecteer op de website van de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) de optie **Items toevoegen,** vouw de categorie **Databases** uit en kies **Azure Database voor MariaDB** om de opties aan te passen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [servicebeperkingen](concepts-limits.md).
- Meer informatie over het [maken van een MariaDB-server in de Azure-portal.](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
