---
title: Gebruik sys_schema - Azure Database voor MySQL
description: Meer informatie over het gebruik van sys_schema om prestatieproblemen te vinden en de database in Azure Database voor MySQL te onderhouden.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: 59b8753007c3b9130c397dda30c571580cbb5326
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411096"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Hoe sys_schema te gebruiken voor prestatieafstemming en databaseonderhoud in Azure Database voor MySQL

De MySQL-performance_schema, voor het eerst beschikbaar in MySQL 5.5, biedt instrumentatie voor veel vitale serverbronnen zoals geheugentoewijzing, opgeslagen programma's, metadatavergrendeling, enz. De performance_schema bevat echter meer dan 80 tabellen, en het verkrijgen van de benodigde informatie vereist vaak het samenvoegen van tabellen binnen de performance_schema, evenals tabellen van de information_schema. Voortbouwend op zowel performance_schema als information_schema biedt de sys_schema een krachtige verzameling gebruiksvriendelijke weergaven in een alleen-lezen database en is deze volledig ingeschakeld in Azure Database voor [MySQL-versie](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) 5.7.

![uitzicht op sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Er zijn 52 weergaven in de sys_schema en elke weergave heeft een van de volgende voorvoegsels:

- Host_summary of IO: I/O gerelateerde latencies.
- InnoDB: InnoDB buffer status en sloten.
- Geheugen: Geheugengebruik door de host en gebruikers.
- Schema: Schema-gerelateerde informatie, zoals automatische toename, indexen, enz.
- Verklaring: Informatie over SQL-instructies; het kan een instructie zijn die heeft geresulteerd in volledige tabelscan of lange querytijd.
- Gebruiker: resources die worden verbruikt en gegroepeerd door gebruikers. Voorbeelden zijn bestand I/O's, verbindingen en geheugen.
- Wachten: wacht gebeurtenissen gegroepeerd op host of gebruiker.

Laten we nu eens kijken naar een aantal gemeenschappelijke gebruikspatronen van de sys_schema. Om te beginnen groeperen we de gebruikspatronen in twee categorieën: **Prestatieafstemming** en **Databaseonderhoud.**

## <a name="performance-tuning"></a>Prestaties afstemmen

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO is de duurste bewerking in de database. We kunnen de gemiddelde IO-latentie achterhalen door de *sys.user_summary_by_file_io-weergave* op te vragen. Met de standaard 125 GB ingerichte opslag, mijn IO latency is ongeveer 15 seconden.

![io latentie: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Omdat Azure Database for MySQL IO schaalt met betrekking tot opslag, na het verhogen van mijn ingerichte opslag tot 1 TB, vermindert mijn IO-latentie tot 571 ms.

![io latentie: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Ondanks een zorgvuldige planning, kunnen veel query's nog steeds resulteren in volledige tabel scans. Voor meer informatie over de typen indexen en hoe u deze optimaliseren, u verwijzen naar dit artikel: [Hoe u queryprestaties oplossen.](./howto-troubleshoot-query-performance.md) Volledige tabelscans zijn resource-intensief en verslechteren de prestaties van uw database. De snelste manier om tabellen met volledige tabelscan te vinden, is door de *sys.schema_tables_with_full_table_scans-weergave* op te vragen.

![volledige tabelscans](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Om problemen met de prestaties van de database op te lossen, kan het nuttig zijn om de gebeurtenissen in uw database te identificeren en het gebruik van de *sys.user_summary_by_statement_type-weergave* kan gewoon de truc doen.

![samenvatting per verklaring](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

In dit voorbeeld heeft Azure Database voor MySQL 53 minuten besteed aan het doorspoelen van het logboekquerylogboek 44579 keer. Dat is een lange tijd en veel IOs. U deze activiteit verminderen door uw logboek voor trage query's uit te schakelen of de frequentie van de Azure-portal voor langzame query's te verlagen.

## <a name="database-maintenance"></a>Databaseonderhoud

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> Het opvragen van deze weergave kan van invloed zijn op de prestaties. Het wordt aanbevolen om deze probleemoplossing uit te voeren tijdens kantooruren buiten de spits.

De InnoDB buffer pool bevindt zich in het geheugen en is het belangrijkste cachemechanisme tussen de DBMS en de opslag. De grootte van de InnoDB-buffergroep is gekoppeld aan de prestatielaag en kan niet worden gewijzigd tenzij een ander product SKU wordt gekozen. Net als bij het geheugen in uw besturingssysteem, worden oude pagina's verwisseld om ruimte te maken voor versere gegevens. Als u wilt weten welke tabellen het grootste deel van het InnoDB-bufferpoolgeheugen verbruiken, u de *weergave sys.innodb_buffer_stats_by_table* opvragen.

![InnoDB-bufferstatus](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

In de bovenstaande afbeelding is het duidelijk dat elke tabel in de mysqldatabase033-database, die een van mijn WordPress-sites host, 16 KB of 1 pagina aan gegevens in het geheugen inbeslagneemt.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indexen zijn geweldige tools om leesprestaties te verbeteren, maar ze brengen wel extra kosten met zich mee voor wisselplaten en opslag. *Sys.schema_unused_indexes* en *sys.schema_redundant_indexes* geven inzicht in ongebruikte of dubbele indexen.

![ongebruikte indexen](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundante indexen](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Conclusie

Samengevat, de sys_schema is een geweldig hulpmiddel voor zowel performance tuning en database onderhoud. Zorg ervoor dat u gebruik maakt van deze functie in uw Azure Database voor MySQL. 

## <a name="next-steps"></a>Volgende stappen
- Ga naar [het MSDN-forum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) of [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)om antwoorden op je meest bezorgde vragen te vinden of een nieuwe vraag/antwoord te plaatsen.
