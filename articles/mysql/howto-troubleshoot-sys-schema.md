---
title: Sys_schema gebruiken om prestaties af te stemmen en te onderhouden Azure Database for MySQL
description: Meer informatie over hoe u sys_schema kunt gebruiken om prestatie problemen op te sporen en data base in Azure Database for MySQL te onderhouden.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 7dc6b4744c74c56803127f63a8a6f29ca5a15090
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972793"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Sys_schema gebruiken voor het afstemmen van prestaties en het beheren van data bases in Azure Database for MySQL

De MySQL-performance_schema, die eerst beschikbaar is in MySQL 5,5, biedt instrumentatie voor veel cruciale Server bronnen, zoals geheugen toewijzing, opgeslagen Program ma's, meta gegevens vergren delen, enzovoort. De performance_schema bevat echter meer dan 80 tabellen en het ophalen van de benodigde gegevens vereist vaak join-tabellen in de performance_schema, evenals tabellen uit de information_schema. De sys_schema is gebaseerd op zowel performance_schema als information_schema en biedt een krachtige verzameling [gebruiks vriendelijke weer gaven](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) in een alleen-lezen data base en is volledig ingeschakeld in Azure Database for MySQL versie 5,7.

![weer gaven van sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

De sys_schema bevat 52 weer gaven en elke weer gave heeft een van de volgende voor voegsels:

- Host_summary of IO: I/O-gerelateerde latenties.
- InnoDB InnoDB buffer status en vergren delingen.
- Geheugen: Geheugen gebruik door de host en gebruikers.
- Schema Informatie die betrekking heeft op het schema, zoals automatische toename, indexen, enzovoort.
- Rekeningen Informatie over SQL-instructies; Dit kan een instructie zijn die de volledige tabel scan heeft veroorzaakt, of een lange query tijd.
- Gebruiker Resources die worden verbruikt en gegroepeerd door gebruikers. Voor beelden hiervan zijn bestands-I/O's, verbindingen en geheugen.
- Bewerking Wacht gebeurtenissen gegroepeerd op host of gebruiker.

Laten we nu eens kijken naar enkele veelvoorkomende gebruiks patronen van de sys_schema. Om te beginnen, groeperen we de gebruiks patronen in twee categorieën: **Prestaties afstemmen** en **database onderhoud**.

## <a name="performance-tuning"></a>Prestaties afstemmen

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO is de duurste bewerking in de-data base. We kunnen de gemiddelde IO-latentie vinden door de weer gave *sys. user_summary_by_file_io* te raadplegen. Met de standaard 125 GB aan ingerichte opslag ruimte, is mijn i/o-latentie ongeveer 15 seconden.

![io-latentie: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Omdat Azure Database for MySQL IO inschaalt ten opzichte van opslag, vermindert mijn i/o-wacht tijd tot 571 MS.

![io-latentie: CAPACITEIT](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Ondanks een zorgvuldige planning kunnen veel query's nog steeds leiden tot volledige tabel scans. Raadpleeg dit artikel voor meer informatie over de typen indexen en hoe u deze kunt optimaliseren: De [prestaties van query's oplossen](./howto-troubleshoot-query-performance.md). Volledige tabel scans zijn resource-intensief en verminderen de prestaties van uw data base. De snelste manier om tabellen met volledige tabel scan te vinden, is door de weer gave *sys. schema_tables_with_full_table_scans* te doorzoeken.

![volledige tabel scans](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Om problemen met de database prestaties op te lossen, kan het nuttig zijn om de gebeurtenissen in uw data base te identificeren en de weer gave *sys. user_summary_by_statement_type* mogelijk te maken.

![samen vatting per overzicht](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

In dit voor beeld Azure Database for MySQL 53 minuten besteed aan het leegmaken van het slog-query logboek 44579 keer. Dat is een lange tijd en veel IOs. U kunt deze activiteit verminderen door uw logboek voor trage query's uit te scha kelen of de frequentie van langzame query's te verlagen Azure Portal.

## <a name="database-maintenance"></a>Database onderhoud

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

De InnoDB-buffer groep bevindt zich in het geheugen en is het hoofd cache mechanisme tussen het DBMS en de opslag. De grootte van de buffer groep InnoDB is gekoppeld aan de prestatie-laag en kan alleen worden gewijzigd als er een andere product-SKU is gekozen. Net als bij geheugen in uw besturings systeem worden oude pagina's omgewisseld om ruimte te maken voor de gegevens van de nieuwer. Als u wilt weten welke tabellen het meeste geheugen van de InnoDB buffer gebruiken, kunt u de weer gave *sys. innodb_buffer_stats_by_table* opvragen.

![Status van InnoDB-buffer](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

In de bovenstaande afbeelding ziet u dat naast de systeem tabellen en weer gaven, elke tabel in de mysqldatabase033-data base, die als host fungeert voor een van mijn WordPress-sites, 16 KB of 1 pagina van de gegevens in het geheugen in beslag neemt.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indexen zijn fantastische hulp middelen voor het verbeteren van de Lees prestaties, maar er zijn extra kosten in rekening gebracht voor toevoegingen en opslag. *Sys. schema_unused_indexes* en *sys. schema_redundant_indexes* bieden inzicht in ongebruikte of dubbele indexen.

![ongebruikte indexen](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![redundante indexen](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Conclusie

In samen vatting is de sys_schema een geweldig hulp middel voor het afstemmen van prestaties en het onderhoud van de data base. Zorg ervoor dat u deze functie in uw Azure Database for MySQL kunt gebruiken. 

## <a name="next-steps"></a>Volgende stappen
- Ga naar [MSDN-forum](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) of [stack overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)om peer antwoorden te vinden op uw vragen of een nieuwe vraag/antwoord te plaatsen.
