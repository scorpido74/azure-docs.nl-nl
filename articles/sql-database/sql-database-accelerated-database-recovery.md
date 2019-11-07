---
title: Versneld database herstel-Azure SQL Database
description: De Azure SQL Database heeft een nieuwe functie waarmee u snel en consistent database herstel, momentane trans actie terugdraaien en een agressieve afkap ping van Logboeken voor afzonderlijke data bases en gepoolde data bases in Azure SQL Database en data bases in Azure SQL-gegevens kunt maken. Uitslag.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: e66b3e6563d796cc7b59e82233bd1b22bc906c6e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691351"
---
# <a name="accelerated-database-recovery"></a>Versneld database herstel

**Versneld database herstel (ADR)** is een nieuwe functie van SQL database engine waarmee de beschik baarheid van de data base aanzienlijk wordt verbeterd, met name als er langlopende trans acties worden uitgevoerd, door het herstel proces van de SQL database-engine opnieuw te ontwerpen. ADR is momenteel beschikbaar voor afzonderlijke data bases en gepoolde data bases in Azure SQL Database, en in de data bases in Azure SQL Data Warehouse (momenteel in open bare preview). De belangrijkste voor delen van ADR zijn:

- **Snel en consistent herstel van de data base**

  Met ADR hebben langlopende trans acties geen invloed op de algehele herstel tijd, waardoor snel en consistent herstel van de data base mogelijk is, ongeacht het aantal actieve trans acties in het systeem of de grootte ervan.

- **Momentane trans actie terugdraaien**

  Met ADR is het terugdraaien van trans acties onmiddellijk, ongeacht het tijdstip waarop de trans actie actief is of het aantal updates dat is uitgevoerd.

- **Afkap ping van agressief logboek**

  Met ADR wordt het transactie logboek agressief afgekapt, zelfs in de aanwezigheid van actieve langlopende trans acties, waardoor het niet mogelijk is om de controle uit te voeren.

## <a name="the-current-database-recovery-process"></a>Het huidige herstel proces van de data base

Het herstel van de data base in SQL Server volgt het [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) -herstel model en bestaat uit drie fasen, die in het volgende diagram worden geïllustreerd en uitvoeriger worden beschreven in het diagram.

![huidig herstel proces](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analyse fase**

  Voorwaartse scan van het transactie logboek vanaf het begin van het laatste geslaagde controle punt (of het oudste wegge schreven pagina-LSN) tot aan het eind, om de status van elke trans actie op het moment te bepalen SQL Server gestopt.

- **Fase opnieuw uitvoeren**

  Voorwaartse scan van het transactie logboek van de oudste niet-doorgevoerde trans actie tot het eind, om de data base te verplaatsen naar de status die het was op het moment van de crash door alle doorgevoerde bewerkingen opnieuw uit te voeren.

- **Fase ongedaan maken**

  Voor elke trans actie die actief was op het moment van de crash, doorloopt het logboek achterwaarts, waardoor de bewerkingen die deze trans actie hebben uitgevoerd, worden ongedaan gemaakt.

Op basis van dit ontwerp is de tijd die het SQL database-programma nodig heeft om te herstellen na een onverwachte herstart (ruwweg) evenredig met de grootte van de langste actieve trans actie in het systeem op het moment van de crash. Voor het herstel moeten alle onvolledige trans acties worden teruggedraaid. De vereiste hoeveelheid tijd is evenredig met het werk dat de trans actie heeft uitgevoerd en de tijd die het is actief. Het SQL Server herstel proces kan daarom veel tijd in beslag nemen in de aanwezigheid van langlopende trans acties (zoals grote bewerkingen voor bulksgewijs invoegen of het samen stellen van index voor een grote tabel).

Het annuleren/terugdraaien van een grote trans actie op basis van dit ontwerp kan ook lang duren omdat deze dezelfde fase voor het ongedaan maken van de herstel bewerking gebruikt, zoals hierboven wordt beschreven.

Daarnaast kan de SQL database-engine het transactie logboek niet afkappen wanneer er langlopende trans acties zijn, omdat de bijbehorende logboek records nodig zijn voor de herstel-en terugdraai processen. Als gevolg van dit ontwerp van de SQL database-engine, hebben sommige klanten het probleem geruimen dat de omvang van het transactie logboek erg groot wordt en veel schijf ruimte verbruikt.

## <a name="the-accelerated-database-recovery-process"></a>Het herstel proces voor versneld data base

ADR behandelt de bovenstaande problemen door het herstel proces van de SQL database-engine volledig opnieuw te ontwerpen voor:

- Maak deze constant tijd/direct door te voor komen dat u het logboek van/naar het begin van de oudste actieve trans actie moet scannen. Met ADR wordt het transactie logboek alleen verwerkt vanaf het laatste geslaagde controle punt (of het oudste LSN (Dirty page log Sequence Number)). Als gevolg hiervan worden de herstel tijd niet beïnvloed door langlopende trans acties.
- Minimaliseer de vereiste transactie logboek ruimte, omdat het logboek voor de hele trans actie niet meer hoeft te worden verwerkt. Als gevolg hiervan kan het transactie logboek agressief worden afgekapt als controle punten en back-ups worden uitgevoerd.

Op hoog niveau verkrijgt ADR snelle herstel van de data base door versie beheer van alle fysieke database wijzigingen en alleen ongedaan maken van logische bewerkingen, die beperkt zijn en bijna onmiddellijk ongedaan kunnen worden gemaakt. Alle trans acties die actief waren op het moment van een crash, worden gemarkeerd als afgebroken en daarom kunnen alle door deze trans acties gegenereerde versies worden genegeerd door gelijktijdige gebruikers query's.

Het proces voor het herstellen van ADR heeft dezelfde drie fasen als het huidige herstel proces. Hoe deze fasen met ADR worden gebruikt, worden in het volgende diagram geïllustreerd en uitvoerig beschreven in het diagram.

![ADR-herstel proces](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analyse fase**

  Het proces blijft hetzelfde als vandaag, met toevoeging van het reconstrueren van sLog en het kopiëren van logboek records voor niet-versie bewerkingen.
  
- Fase **opnieuw uitvoeren**

  Onderverdeeld in twee fasen (P)
  - Fase 1

      Voer de bewerking opnieuw uit vanaf sLog (oudste niet-doorgevoerde trans actie tot het laatste controle punt). Opnieuw is een snelle bewerking, omdat alleen enkele records uit de sLog moeten worden verwerkt.
      
  - Fase 2

     Opnieuw uitvoeren vanuit het transactie logboek vanaf het laatste controle punt (in plaats van de oudste niet-doorgevoerde trans actie)
     
- **Fase ongedaan maken**

   De fase voor het ongedaan maken met ADR wordt bijna onmiddellijk voltooid met behulp van sLog om bewerkingen zonder versie ongedaan te maken en de persistente versie van PVS

## <a name="adr-recovery-components"></a>ADR-herstel onderdelen

De vier belangrijkste onderdelen van ADR zijn:

- **Permanente versie opslag (PVS)**

  De permanente versie opslag is een nieuw SQL database engine-mechanisme voor het persistent maken van de rijdefinities die in de data base zelf zijn gegenereerd in plaats van de traditionele `tempdb` versie opslag. PVS maakt het isoleren van bronnen mogelijk en verbetert de beschik baarheid van Lees bare secundaire zones.

- **Logische terugzet actie**

  Logische herverteren is het asynchrone proces dat verantwoordelijk is voor het uitvoeren van ongedaan maken op basis van een op rijniveau gebaseerde, direct terugdraaiende trans actie en ongedaan maken voor alle bewerkingen met een versie.

  - Houdt alle afgebroken trans acties bij
  - Voert terugdraaien uit met PVS voor alle gebruikers transacties
  - Hiermee worden alle vergren delingen direct na de trans actie afgebroken

- **sLog**

  sLog is een secundaire logboek stroom in het geheugen waarin logboek records worden opgeslagen voor niet-verwerkte bewerkingen (zoals meta gegevens cache, vergren delen, enzovoort). De sLog is:

  - Laag volume en in-Memory
  - Bewaard op schijf door geserialiseerd tijdens het controlepunt proces
  - Periodiek afgekapt tijdens het door voeren van trans acties
  - Versnelt opnieuw en ongedaan maken door alleen de niet-geversiete bewerkingen te verwerken  
  - Maakt agressieve afkap ping van transactie logboeken mogelijk door alleen de vereiste logboek records te behouden

- **Schone**

  De Removal is het asynchrone proces dat periodiek wordt geactiveerd en dat er geen pagina versies worden opgeschoond die niet nodig zijn.

## <a name="who-should-consider-accelerated-database-recovery"></a>Wie moet versneld database herstel overwegen

De volgende typen klanten moeten overwegen om ADR in te scha kelen:

- Klanten die werk belastingen met langlopende trans acties hebben.
- Klanten die gevallen hebben gezien dat het transactie logboek aanzienlijk groeit door actieve trans acties.  
- Klanten die lange Peri Oden niet beschik baarheid hebben ondervonden omdat SQL Server langdurige herstel bewerking wordt uitgevoerd (zoals onverwacht SQL Server opnieuw opstarten of het terugdraaien van hand matige trans acties).

