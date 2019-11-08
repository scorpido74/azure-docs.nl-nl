---
title: Query Store uitvoeren
description: Meer informatie over het uitvoeren van de query Store in Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: fa60992c85e69143bfd65cc1a1f420ed85c8fd93
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802773"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Het query archief in Azure SQL Database gebruiken

Query Store in Azure is een volledig beheerde database functie waarmee voortdurend gedetailleerde historische informatie over alle query's wordt verzameld en gepresenteerd. U kunt nadenken over query Store net als bij een vliegtuig vlucht gegevens recorder die de prestaties van query's aanzienlijk vereenvoudigt voor zowel Cloud-als on-premises klanten. In dit artikel worden specifieke aspecten van het besturings systeem voor het uitvoeren van Query's in azure uitgelegd. Door deze vooraf verzamelde query gegevens te gebruiken, kunt u snel prestatie problemen vaststellen en oplossen en zo meer tijd best Eden aan het richten op hun bedrijf. 

Het query archief is sinds november 2015 [wereld wijd beschikbaar](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) in Azure SQL database. Query Store is de basis voor het analyseren van prestaties en het afstemmen van functies, zoals [SQL database Advisor en het dash board prestaties](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Op het moment van publicatie van dit artikel wordt de query Store uitgevoerd in meer dan 200.000 gebruikers databases in azure, en worden er zonder onderbrekingen query gegevens verzameld over enkele maanden.

> [!IMPORTANT]
> Micro soft is bezig met het activeren van het query Archief voor alle Azure SQL-data bases (bestaande en nieuwe). 

## <a name="optimal-query-store-configuration"></a>Optimale configuratie van het query archief

In deze sectie worden optimale standaard instellingen voor configuraties beschreven, die zijn ontworpen om een betrouw bare werking van de query-Store en afhankelijke functies, zoals [SQL database Advisor en het prestatie dashboard](https://azure.microsoft.com/updates/sqldatabaseadvisorga/), te garanderen. De standaard configuratie is geoptimaliseerd voor het verzamelen van doorlopende gegevens, waarbij de minimale tijd wordt besteed aan OFF-READ_ONLY statussen.

| Configuratie | Beschrijving | Standaard | Opmerking |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Hiermee geeft u de limiet op voor de gegevens ruimte die in de query opslag kan worden uitgevoerd in de klanten database |100 |Afgedwongen voor nieuwe data bases |
| INTERVAL_LENGTH_MINUTES |Definieert de grootte van het tijd venster waarin verzamelde runtime statistieken voor query plannen worden geaggregeerd en bewaard. Elk actief query plan heeft Maxi maal één rij voor een tijds periode die met deze configuratie is gedefinieerd |60 |Afgedwongen voor nieuwe data bases |
| STALE_QUERY_THRESHOLD_DAYS |Op tijd gebaseerd opschonings beleid dat de Bewaar periode van persistente runtime statistieken en inactieve query's beheert |30 |Afgedwongen voor nieuwe data bases en data bases met eerdere standaard (367) |
| SIZE_BASED_CLEANUP_MODE |Hiermee geeft u op of het automatisch opschonen van gegevens wordt uitgevoerd wanneer de gegevens opslag van de query de limiet nadert |Auto |Afgedwongen voor alle data bases |
| QUERY_CAPTURE_MODE |Hiermee wordt aangegeven of alle query's of alleen een subset van query's worden bijgehouden |Auto |Afgedwongen voor alle data bases |
| FLUSH_INTERVAL_SECONDS |Hiermee geeft u de maximum periode op waarin vastgelegde runtime statistieken in het geheugen worden bewaard voordat deze naar de schijf worden leeg gemaakt |900 |Afgedwongen voor nieuwe data bases |
|  | | | |

> [!IMPORTANT]
> Deze standaard waarden worden automatisch toegepast in de laatste fase van de activering van de query Store in alle Azure SQL-data bases (zie voorafgaande belang rijke opmerking). Na dit lampje is Azure SQL Database configuratie waarden die door klanten zijn ingesteld, niet gewijzigd, tenzij ze een negatieve invloed hebben op de primaire werk belasting of betrouw bare bewerkingen van de query Store.

Als u de aangepaste instellingen wilt blijven gebruiken, gebruikt u de [Opties Alter data base with query Store](https://msdn.microsoft.com/library/bb522682.aspx) om de configuratie terug te zetten naar de vorige status. Bekijk [de aanbevolen procedures voor het query archief](https://msdn.microsoft.com/library/mt604821.aspx) om te leren hoe beste de optimale configuratie parameters hebt gekozen.

## <a name="next-steps"></a>Volgende stappen

[Inzicht in de prestaties SQL Database](sql-database-performance.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Raadpleeg de volgende artikelen voor meer informatie:

- [Een vlucht gegevens recorder voor uw data base](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Prestaties bewaken met behulp van de query Store](https://msdn.microsoft.com/library/dn817826.aspx)
- [Query Store-gebruiksscenario's](https://msdn.microsoft.com/library/mt614796.aspx)
