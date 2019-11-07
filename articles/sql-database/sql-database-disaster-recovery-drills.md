---
title: SQL Database oefeningen voor herstel na nood gevallen
description: Meer informatie over de richt lijnen en aanbevolen procedures voor het gebruik van Azure SQL Database voor het uitvoeren van herstel na nood gevallen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: bf337b1312abc59048649aabe3e4f895d07a8d51
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690618"
---
# <a name="performing-disaster-recovery-drill"></a>Nood herstel analyse uitvoeren

U kunt het beste de validatie van de gereedheid van de toepassing voor de herstel werk stroom periodiek uitvoeren. Het controleren van het gedrag van de toepassing en de implicaties van gegevens verlies en/of de onderbreking van de failover is een goede technische praktijk. Het is ook een vereiste voor de meeste industrie normen als onderdeel van de certificering voor bedrijfs continuïteit.

Het uitvoeren van een nood herstel analyse bestaat uit:

* De stroom onderbreking van de gegevenslaag simuleren
* Hersteld
* Controleren op toepassings integriteit na herstel

Afhankelijk van hoe u [uw toepassing voor bedrijfs continuïteit hebt ontworpen](sql-database-business-continuity.md), kan de werk stroom voor het uitvoeren van de analyse variëren. In dit artikel worden de aanbevolen procedures beschreven voor het uitvoeren van een nood herstel analyse in de context van Azure SQL Database.

## <a name="geo-restore"></a>Geo-herstel

Als u wilt voor komen dat er gegevens verloren gaan tijdens het uitvoeren van een nood herstel analyse, voert u de analyse uit met een test omgeving door een kopie van de productie omgeving te maken en deze te gebruiken om de werk stroom van de failover van de toepassing te controleren.

### <a name="outage-simulation"></a>Simulatie van uitval

Als u de storing wilt simuleren, kunt u de naam van de bron database wijzigen. Deze naamswijziging veroorzaakt verbindings fouten van toepassingen.

### <a name="recovery"></a>Herstel

* Voer de geo-herstel bewerking van de data base uit naar een andere server, zoals [hier](sql-database-disaster-recovery.md)wordt beschreven.
* Wijzig de configuratie van de toepassing om verbinding te maken met de herstelde data base en volg de gids [een Data Base configureren na herstel](sql-database-disaster-recovery.md) om het herstel te volt ooien.

### <a name="validation"></a>Validatie

Voer de analyse uit door de controle van de toepassings integriteit te controleren (inclusief verbindings reeksen, aanmeldingen, testen van basis functies of andere validaties van signoffs procedures voor de standaard toepassing).

## <a name="failover-groups"></a>Failover-groepen

Voor een Data Base die wordt beveiligd met behulp van failover-groepen, omvat de analyse oefening een geplande failover naar de secundaire server. De geplande failover zorgt ervoor dat de primaire en secundaire data bases in de failover-groep synchroon blijven wanneer de rollen worden overgeschakeld. In tegens telling tot de niet-geplande failover resulteert deze bewerking niet in gegevens verlies, waardoor de analyse kan worden uitgevoerd in de productie omgeving.

### <a name="outage-simulation"></a>Simulatie van uitval

U kunt de storing simuleren door de webtoepassing of virtuele machine die is verbonden met de data base, uit te scha kelen. Deze uitval simulatie resulteert in de verbindings fouten voor de web-clients.

### <a name="recovery"></a>Herstel

* Zorg ervoor dat de configuratie van de toepassing in de DR-regio verwijst naar de vorige secundaire, die de volledig toegankelijke nieuwe primaire versie wordt.
* [Geplande failover](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) van de failovergroep initiëren vanaf de secundaire server.
* Volg de hand leiding [Configure Data Base after recovery](sql-database-disaster-recovery.md) om het herstel te volt ooien.

### <a name="validation"></a>Validatie

Voer de analyse uit door het na herstel van de toepassings integriteit te controleren (inclusief connectiviteit, basis functionaliteits tests of andere validaties die vereist zijn voor de analyse signoffs).

## <a name="next-steps"></a>Volgende stappen

* Zie [continuïteits scenario's](sql-database-business-continuity.md)voor meer informatie over scenario's voor bedrijfs continuïteit.
* Zie [SQL database automatische back-ups](sql-database-automated-backups.md) voor meer informatie over Azure SQL database automatische back-ups
* Zie [een Data Base herstellen vanuit de door de service geïnitieerde back-ups](sql-database-recovery-using-backups.md)voor meer informatie over het gebruik van automatische back-ups voor herstel.
* Zie [actieve geo-replicatie](sql-database-active-geo-replication.md) en [groepen voor automatische failover](sql-database-auto-failover-group.md)voor meer informatie over snellere herstel opties.
