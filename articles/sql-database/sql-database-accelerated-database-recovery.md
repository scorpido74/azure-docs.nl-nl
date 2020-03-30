---
title: Versneld databaseherstel
description: De Azure SQL Database heeft een nieuwe functie die snel en consistent databaseherstel, onmiddellijke transactierollback en agressieve logboekafvloeiing voor afzonderlijke databases en samengevoegde databases in Azure SQL Database en databases in Azure SQL Data biedt Magazijn.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238666"
---
# <a name="accelerated-database-recovery"></a>Versneld databaseherstel

**Accelerated Database Recovery (ADR)** is een SQL-databaseenginefunctie die de beschikbaarheid van database aanzienlijk verbetert, vooral in aanwezigheid van langlopende transacties, door het sql-databaseengineherstelproces opnieuw te ontwerpen. ADR is momenteel beschikbaar voor Azure SQL Database single, elastic pool en managed instance, en databases in Azure SQL Data Warehouse (momenteel in preview). De belangrijkste voordelen van ADR zijn:

- **Snel en consistent databaseherstel**

  Met ADR hebben langlopende transacties geen invloed op de totale hersteltijd, waardoor snel en consistent databaseherstel mogelijk is, ongeacht het aantal actieve transacties in het systeem of de grootte ervan.

- **Momentane transactieterugdraaiing**

  Met ADR is het terugdraaien van transacties onmiddellijk, ongeacht het tijdstip waarop de transactie actief is geweest of het aantal updates dat heeft uitgevoerd.

- **Agressieve logtruncation**

  Met ADR wordt het transactielogboek agressief afgekapt, zelfs in aanwezigheid van actieve langlopende transacties, waardoor het niet uit de hand loopt.

## <a name="the-current-database-recovery-process"></a>Het huidige databaseherstelproces

Databaseherstel in SQL Server volgt het [ARIES-herstelmodel](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) en bestaat uit drie fasen, die in het volgende diagram worden geïllustreerd en nader worden toegelicht naar aanleiding van het diagram.

![huidig herstelproces](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analysefase**

  De scan van het transactielogboek doorsturen vanaf het begin van het laatste succesvolle controlepunt (of de oudste vuile pagina LSN) tot het einde, om de status van elke transactie te bepalen op het moment dat SQL Server is gestopt.

- **Opnieuw doen fase**

  De scan van het transactielogboek van de oudste niet-vastgelegde transactie tot het einde doorsturen om de database naar de status te brengen die het was op het moment van de crash door alle vastgelegde bewerkingen opnieuw uit te voeren.

- **Fase ongedaan maken**

  Voor elke transactie die actief was vanaf het moment van de crash, doorloopt u het logboek achteruit en maakt u de bewerkingen ongedaan die deze transactie heeft uitgevoerd.

Op basis van dit ontwerp is de tijd die de SQL-databaseengine nodig heeft om te herstellen van een onverwachte herstart (ongeveer) evenredig met de grootte van de langste actieve transactie in het systeem op het moment van de crash. Herstel vereist een terugdraaiing van alle onvolledige transacties. De benodigde tijd is evenredig aan het werk dat de transactie heeft uitgevoerd en de tijd die de transactie heeft uitgevoerd. Daarom kan het SQL Server-herstelproces lang duren in aanwezigheid van langlopende transacties (zoals grote bulkinsertbewerkingen of indexbuildbewerkingen tegen een grote tabel).

Ook het annuleren/ terugdraaien van een grote transactie op basis van dit ontwerp kan ook lang duren omdat het dezelfde herstelfase ongedaan maken zoals hierboven beschreven, wordt gebruikt.

Bovendien kan de SQL-databaseengine het transactielogboek niet afkappen wanneer er langlopende transacties zijn, omdat de bijbehorende logboekrecords nodig zijn voor de herstel- en terugdraaiprocessen. Als gevolg van dit ontwerp van de SQL-databaseengine, sommige klanten gebruikt om het probleem dat de grootte van de transactie log groeit zeer groot en verbruikt enorme hoeveelheden schijfruimte gezicht.

## <a name="the-accelerated-database-recovery-process"></a>Het versnelde databaseherstelproces

ADR lost bovenstaande problemen op door het SQL-databaseengineherstelproces volledig te herontwerpen naar:

- Maak het constant tijd / direct door te voorkomen dat het logboek van / naar het begin van de oudste actieve transactie te scannen. Met ADR wordt het transactielogboek alleen verwerkt vanaf het laatste succesvolle controlepunt (of oudste vuile pagina Logboeknummer (LSN)). Als gevolg hiervan wordt de hersteltijd niet beïnvloed door langlopende transacties.
- Minimaliseer de vereiste transactielogboekruimte, omdat het logboek voor de hele transactie niet langer hoeft te worden verwerkt. Als gevolg hiervan kan het transactielogboek agressief worden afgekapt als controlepunten en back-ups optreden.

Op hoog niveau bereikt ADR snel databaseherstel door alle fysieke databasewijzigingen te versionen en alleen logische bewerkingen ongedaan te maken, die beperkt zijn en vrijwel direct ongedaan kunnen worden gemaakt. Elke transactie die actief was vanaf het moment van een crash, wordt gemarkeerd als afgebroken en daarom kunnen alle versies die door deze transacties worden gegenereerd, worden genegeerd door gelijktijdige gebruikersquery's.

Het ADR-herstelproces kent dezelfde drie fasen als het huidige herstelproces. Hoe deze fasen werken met ADR wordt geïllustreerd in het volgende diagram en in meer detail uitgelegd naar aanleiding van het diagram.

![ADR-herstelproces](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analysefase**

  Het proces blijft hetzelfde als voorheen met de toevoeging van het reconstrueren van sLog en het kopiëren van logboekrecords voor niet-versiebeheer.
  
- **Opnieuw** doen fase

  In twee fasen opgesplitst (P)
  - Fase 1

      Opnieuw uitvoeren van sLog (oudste niet-vastgelegde transactie tot laatste controlepunt). Redo is een snelle bewerking omdat het slechts een paar records uit de sLog hoeft te verwerken.
      
  - Fase 2

     Opnieuw uitvoeren van transactielogboek begint bij het laatste controlepunt (in plaats van de oudste niet-vastgelegde transactie)
     
- **Fase ongedaan maken**

   De ongedaan maken fase met ADR wordt vrijwel onmiddellijk voltooid met behulp van sLog om niet-versiebewerkingen ongedaan te maken en Persisted Version Store (PVS) met Logical Revert om op rijniveau gebaseerde versie ongedaan maken uit te voeren.

## <a name="adr-recovery-components"></a>ADR-terugwinningscomponenten

De vier belangrijkste onderdelen van ADR zijn:

- **Persisted Version Store (PVS)**

  Het aanhoudende versiearchief is een nieuw SQL-databaseenginemechanisme voor het voortbestaan van `tempdb` de rijversies die in de database zelf worden gegenereerd in plaats van het traditionele versiearchief. PVS maakt resourceisolatie mogelijk en verbetert de beschikbaarheid van leesbare secondaries.

- **Logische terugzetten**

  Logische terugval is het asynchrone proces dat verantwoordelijk is voor het uitvoeren van versiegebaseerde ongedaan maken op rijniveau - het bieden van directe terugdraaiing en ongedaan maken voor alle versies. Logische terugkeer wordt bereikt door:

  - Het bijhouden van alle afgebroken transacties en markeren ze onzichtbaar voor andere transacties. 
  - Het uitvoeren van terugdraaiing door PVS te gebruiken voor alle gebruikerstransacties, in plaats van het transactielogboek fysiek te scannen en wijzigingen één voor één ongedaan te maken.
  - Het vrijgeven van alle sloten onmiddellijk na het afbreken van de transactie. Aangezien afbreken gaat gewoon markeren veranderingen in het geheugen, het proces is zeer efficiënt en daarom sloten niet hoeft te worden gehouden voor een lange tijd.

- **sLog (log)**

  sLog is een secundaire logboekstream in het geheugen die logboekrecords opslaat voor bewerkingen met niet-versies (zoals ongeldigheid van metagegevenscache, vergrendelingsacquisities, enzovoort). De sLog is:

  - Laag volume en geheugen
  - Duurde voort op schijf door serialized te worden tijdens het controlepuntproces
  - Periodiek afgekapt als transacties plegen
  - Versnelt opnieuw doen en ongedaan maken door alleen de niet-versies te verwerken  
  - Maakt agressieve transactielogboektrunatie mogelijk door alleen de vereiste logboekrecords te bewaren

- **Schoner**

  De reiniger is het asynchrone proces dat periodiek wakker wordt en paginaversies reinigt die niet nodig zijn.

## <a name="accelerated-database-recovery-patterns"></a>Herstelpatronen voor versnelde database

De volgende typen workloads profiteren het meest van ADR:

- Workloads met langlopende transacties.
- Workloads die gevallen hebben gezien waarin actieve transacties ervoor zorgen dat het transactielogboek aanzienlijk toalde.  
- Workloads die lange perioden van database-onbeschikbaarheid hebben ondervonden als gevolg van sql server langlopend herstel (zoals onverwachte SQL Server opnieuw opstarten of handmatige transactierollback).

