---
title: Noodhersteloefeningen
description: Meer informatie over richtlijnen en aanbevolen procedures voor het gebruik van Azure SQL Database om noodhersteloefeningen uit te voeren.
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
ms.openlocfilehash: 3ca00a03976ae38b7956616b8287220a7bc5998c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825856"
---
# <a name="performing-disaster-recovery-drill"></a>Uitvoeren van noodherstelboor

Het wordt aanbevolen dat de validatie van de gereedheid voor toepassingen voor herstelwerkwerkperiodiek wordt uitgevoerd. Het verifiëren van het toepassingsgedrag en de implicaties van gegevensverlies en/of de verstoring die failover met zich meebrengt, is een goede technische praktijk. Het is ook een vereiste door de meeste industrienormen als onderdeel van business continuity certificering.

Het uitvoeren van een noodhersteloefening bestaat uit:

* Storing in gegevenslagen simuleren
* Herstellen
* De integriteit van toepassingen valideren na herstel

Afhankelijk van hoe u [uw toepassing hebt ontworpen voor bedrijfscontinuïteit,](sql-database-business-continuity.md)kan de workflow om de drill uit te voeren variëren. In dit artikel worden de aanbevolen procedures beschreven voor het uitvoeren van een noodhersteloefening in de context van Azure SQL Database.

## <a name="geo-restore"></a>Geo-herstel

Als u het potentiële gegevensverlies wilt voorkomen bij het uitvoeren van een noodherstelboor, voert u de drill uit met behulp van een testomgeving door een kopie van de productieomgeving te maken en deze te gebruiken om de failoverworkflow van de toepassing te verifiëren.

### <a name="outage-simulation"></a>Storingssimulatie

Als u de storing wilt simuleren, u de naam van de brondatabase wijzigen. Deze naamswijziging veroorzaakt fouten in de verbindingsmogelijkheden van toepassingen.

### <a name="recovery"></a>Herstel

* Voer de geo-herstel van de database uit naar een andere server zoals [hier](sql-database-disaster-recovery.md)beschreven.
* Wijzig de toepassingsconfiguratie om verbinding te maken met de herstelde database en volg de [handleiding Een database configureren na herstelom](sql-database-disaster-recovery.md) het herstel te voltooien.

### <a name="validation"></a>Validatie

Voltooi de drill door de verificatie van de toepassingsintegriteit na herstel (inclusief verbindingstekenreeksen, aanmeldingen, basisfunctionaliteitstests of andere validaties onderdeel van standaardprocedures voor toepassingsafsluitingen) te verifiëren.

## <a name="failover-groups"></a>Failover-groepen

Voor een database die is beveiligd met failovergroepen, omvat de oefening geplande failover naar de secundaire server. De geplande failover zorgt ervoor dat de primaire en de secundaire databases in de failovergroep synchroon blijven wanneer de rollen worden geschakeld. In tegenstelling tot de ongeplande failover leidt deze bewerking niet tot gegevensverlies, zodat de boor kan worden uitgevoerd in de productieomgeving.

### <a name="outage-simulation"></a>Storingssimulatie

Als u de storing wilt simuleren, u de webtoepassing of virtuele machine uitschakelen die is aangesloten op de database. Deze storingssimulatie resulteert in de verbindingsfouten voor de webclients.

### <a name="recovery"></a>Herstel

* Zorg ervoor dat de toepassingsconfiguratie in het DR-gebied wijst op de voormalige secundaire, die de volledig toegankelijke nieuwe primaire wordt.
* Geplande [failover](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) van de failovergroep vanaf de secundaire server starten.
* Volg de [handleiding Een database configureren na herstelom](sql-database-disaster-recovery.md) het herstel te voltooien.

### <a name="validation"></a>Validatie

Voltooi de drill door de verificatie van de toepassingsintegriteit na herstel (inclusief connectiviteit, basisfunctionaliteitstests of andere validaties die nodig zijn voor de handtekeningborden) te verifiëren.

## <a name="next-steps"></a>Volgende stappen

* Zie [Continuïteitsscenario's](sql-database-business-continuity.md)voor meer informatie over scenario's voor bedrijfscontinuïteit.
* Zie [geautomatiseerde back-ups van SQL Database](sql-database-automated-backups.md) voor meer informatie over geautomatiseerde back-ups van Azure SQL Database
* Zie [een database herstellen van de door de service gestarte back-ups](sql-database-recovery-using-backups.md)voor meer informatie over het gebruik van geautomatiseerde back-ups voor herstel.
* Zie [Actieve georeplicatie-](sql-database-active-geo-replication.md) en [autofailovergroepen voor](sql-database-auto-failover-group.md)meer informatie over snellere herstelopties.
