---
title: Bijhouden van gegevens wijzigingen in Azure SQL Edge (preview-versie)
description: Meer informatie over het bijhouden van wijzigingen en het change data capture in Azure SQL Edge (preview)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3e3a6c3e171383d1812f63b945735d1b82f70b9c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235103"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Bijhouden van gegevens wijzigingen in Azure SQL Edge (preview-versie)

Azure SQL Edge ondersteunt de twee SQL Server-functies waarmee wijzigingen in gegevens in een Data Base worden bijgehouden:[Wijzigingen bijhouden](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) en [Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Met deze functies kunnen toepassingen bepalen welke DML-wijzigingen (insert-, update-en delete-bewerkingen) zijn aangebracht in gebruikers tabellen in een Data Base. Wijzigingen voor het vastleggen van gegevens en het bijhouden van wijzigingen kunnen worden ingeschakeld voor dezelfde data base. Er zijn geen speciale overwegingen vereist.

De mogelijkheid om gegevens op te vragen die in een Data Base zijn gewijzigd, is een belang rijke vereiste voor sommige toepassingen om efficiënt te zijn. Om gegevens wijzigingen te bepalen, moeten ontwikkel aars van toepassingen meestal een aangepaste traceer methode implementeren in hun toepassingen met behulp van een combi natie van triggers, time stamp-kolommen en aanvullende tabellen. Het maken van deze toepassingen vergt meestal veel werk voor het implementeren van schema-updates en is vaak een hoge prestatie overhead. In het geval van een IoT-oplossing moet het bijhouden van wijzigingen van de rand regel matig worden verplaatst naar een Cloud of Data Center. Zo zou het gebruik van de laatste synchronisatie en het uploaden van de wijzigingen in de Cloud of het Data Center-doel snel en effectief kunnen worden uitgevoerd. Raadpleeg voor meer informatie over de voor delen van het gebruik van Wijzigingen bijhouden en Change Data Capture voor [delen van het gebruik van Change Data Capture of wijzigingen bijhouden](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Als u inzicht wilt krijgen in de functie verschillen tussen Wijzigingen bijhouden en Change Data Capture, raadpleegt u de [functie verschillen tussen het vastleggen van gegevens en het wijzigingen bijhouden](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Change Data Capture

Zie [Change Data Capture](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)voor meer informatie over de werking van Change Data Capture.

Voor informatie over het in-of uitschakelen van Change Data Capture, raadpleegt u [Change Data Capture inschakelen en uitschakelen](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)

Voor het beheren en controleren van Change Data Capture raadpleegt u [Change Data Capture beheren en controleren](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

Raadpleeg voor meer informatie over het uitvoeren van query's en het werken met de gewijzigde gegevens [werk met wijzigings gegevens](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)

## <a name="change-tracking"></a>Tracering wijzigen

Raadpleeg [Wijzigingen bijhouden](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)voor meer informatie over de werking van wijzigingen bijhouden.

Voor informatie over het in-of uitschakelen van Wijzigingen bijhouden raadpleegt u [Wijzigingen bijhouden inschakelen en uitschakelen](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

Als u Wijzigingen bijhouden wilt beheren, bewaken en beheren, raadpleegt u [Wijzigingen bijhouden beheren en controleren](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Raadpleeg voor meer informatie over het uitvoeren van query's en het werken met de gewijzigde gegevens [werk met wijzigings gegevens](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)

## <a name="temporal-tables"></a>Tijdelijke tabellen

Naast de ondersteuning van Wijzigingen bijhouden en het wijzigen van gegevens Capture-functies van SQL Server, ondersteunt Azure SQL Edge ook de functie voor tijdelijke tabellen van SQL Server. Tijdelijke tabellen (ook wel de tijdelijke systeem versie tabellen genoemd) als een database functie die ingebouwde ondersteuning biedt voor het leveren van informatie over gegevens die in de tabel zijn opgeslagen, op elk gewenst moment, in plaats van alleen de gegevens die op het huidige tijdstip in de tijd kloppen.

Een tijdelijke systeem versie tabel is een soort gebruikers tabel die is ontworpen om een volledige geschiedenis van gegevens wijzigingen te kunnen bijhouden en eenvoudige point-time-analyses mogelijk te maken. Dit type tijdelijke tabel wordt een tijdelijke systeem versie tabel genoemd, omdat de geldigheids periode voor elke rij wordt beheerd door het systeem (dat wil zeggen, data base-engine).

Elke tijdelijke tabel heeft twee expliciet gedefinieerde kolommen, elk met een DATETIME2-gegevens type. Deze kolommen worden een periode kolom genoemd. Deze periode kolommen worden uitsluitend door het systeem gebruikt om de geldigheids periode voor elke rij vast te leggen wanneer een rij wordt gewijzigd.

Naast deze periode kolommen bevat een tijdelijke tabel ook een verwijzing naar een andere tabel met een gespiegeld schema. Het systeem gebruikt deze tabel om automatisch de vorige versie van de rij op te slaan telkens wanneer een rij in de tijdelijke tabel wordt bijgewerkt of verwijderd. Deze aanvullende tabel wordt de geschiedenis tabel genoemd, terwijl de hoofd tabel waarin de huidige (werkelijke) rij versies worden opgeslagen, wordt aangeduid als de huidige tabel of net als de tijdelijke tabel. Tijdens het maken van een tijdelijke tabel kunnen gebruikers een bestaande geschiedenis tabel opgeven (moet schema compatibel zijn) of een systeem standaard geschiedenis tabel laten maken.

Raadpleeg [tijdelijke tabellen](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) voor meer informatie over tijdelijke tabellen

## <a name="see-also"></a>Zie ook

- [Streamen van gegevens in Azure SQL Edge (preview-versie)](stream-data.md)
- [Machine learning en AI met ONNX in Azure SQL Edge (preview-versie)](onnx-overview.md)
- [Replicatie naar Azure SQL Edge configureren (preview-versie)](configure-replication.md)
- [Back-up en herstel van data bases in Azure SQL Edge (preview-versie)](backup-restore.md)



