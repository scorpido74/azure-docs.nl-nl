---
title: Versie beheer beleid-Azure Database for MySQL-één server en flexibele server (preview)
description: Beschrijft het beleid voor de primaire en secundaire versies van MySQL in Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 0670107d84374589aa60cc18f184b9b3d3facce1
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331801"
---
# <a name="azure-database-for-mysql-versioning-policy"></a>Beleid voor Azure Database for MySQL-versie

Op deze pagina wordt het Azure Database for MySQL-versie beleid beschreven en is van toepassing op de implementatie modi Azure Database for MySQL-één server en Azure Database for MySQL-flexibele server (preview).

## <a name="supported--mysql-versions"></a>Ondersteunde MySQL-versies

Azure Database for MySQL ondersteunt de volgende database versies.

| Versie | Single Server | Flexible Server (preview) |
| ----- | :------: | :----: |
| MySQL 8 | X |  | 
| MySQL 5,7 | X | X |
| MySQL 5,6| X |  |


## <a name="major-version-support"></a>Ondersteuning voor primaire versie
Elke primaire versie van MySQL wordt ondersteund door Azure Database for MySQL vanaf de datum waarop Azure de versie ondersteunt, totdat de versie wordt ingetrokken door de MySQL-Community, zoals is opgenomen in het [versie beleid](https://en.wikipedia.org/wiki/mysql).

## <a name="minor-version-support"></a>Ondersteuning voor secundaire versie
Azure Database for MySQL voert automatisch secundaire versie-upgrades uit naar de voor Keurs-MySQL-versie van Azure als onderdeel van het periodieke onderhoud. 

## <a name="major-version-retirement-policy"></a>Beleid voor primaire versie buiten gebruik stellen
De volgende tabel bevat de details van de buiten gebruiks telling van de primaire MySQL-versie. De datums volgen het [beleid voor MySQL-versie beheer](https://www.mysql.com/support/eol-notice.html).

| Versie | Nieuwe functies | Start datum voor ondersteuning van Azure | Buitengebruikstellings datum|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/)| [Functies](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 maart 2018 | Februari 2021
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/) | [Functies](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 maart 2018 | Oktober 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Functies](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 december 2019 | April 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Buiten gebruik gestelde MySQL-Engine versies worden niet ondersteund in Azure Database for MySQL

Als u na de datum van uittreding voor elke MySQL-database versie doorgaat, moet u rekening houden met de volgende beperkingen:
- Aangezien de community geen verdere oplossingen voor problemen of beveiligingsfixes uitbrengt, zal Azure Database for MySQL de buiten gebruik gestelde data base-engine niet voor eventuele fouten of beveiliging bijwerken of anderszins beveiligings maatregelen treffen met betrekking tot de buiten gebruik gestelde data base-engine. Azure blijft echter periodiek onderhoud en patches uitvoeren voor de host, het besturings systeem, containers en andere service-gerelateerde onderdelen.
- Als er mogelijk ondersteunings problemen optreden met betrekking tot de MySQL-data base, kunnen we u mogelijk geen ondersteuning bieden voor u. In dergelijke gevallen moet u uw data base upgraden om u te helpen bij het verlenen van ondersteuning.
- U kunt geen nieuwe database servers maken voor de buiten gebruik gestelde versie. U kunt echter herstel naar een bepaald tijdstip uitvoeren en voor uw bestaande servers Lees replica's maken...
- Nieuwe service functies die zijn ontwikkeld door Azure Database for MySQL, zijn mogelijk alleen beschikbaar voor ondersteunde database server versies.
- Beschik baarheid van de uptime geldt alleen voor het Azure Database for MySQL van problemen met de service en niet voor uitval tijd die wordt veroorzaakt door fouten met betrekking tot de data base-engine.  
- In het uitzonderlijke geval van een ernstige bedreiging van de service die wordt veroorzaakt door het beveiligingslek met betrekking tot de MySQL-data base-engine die is geïdentificeerd in de buiten gebruik gestelde versie van de data base, kan Azure ervoor kiezen het reken knooppunt van uw database server te stoppen om de service eerst te beveiligen. U wordt gevraagd de server bij te werken voordat u de server online brengt. Tijdens het upgrade proces worden uw gegevens altijd beveiligd met automatische back-ups die worden uitgevoerd op de service, die kan worden gebruikt om terug te zetten naar de oudere versie. 



## <a name="next-steps"></a>Volgende stappen
- Zie Azure Database for MySQL- [ondersteunde versies](./concepts-supported-versions.md) van één server
- Zie Azure Database for MySQL-flexibele server (preview) [ondersteunde versies](flexible-server/concepts-supported-versions.md)
- Zie MySQL- [dump en herstellen](./concepts-migrate-dump-restore.md) om upgrades uit te voeren.
