---
title: In-Memory OLTP verbetert SQL trans actie-prestaties
description: U kunt in-Memory OLTP gebruiken om de transactionele prestaties in een bestaande SQL database te verbeteren.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 82b24b51a103d31bf20bbb7a9fc304095be523d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689835"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>In-Memory OLTP gebruiken om de prestaties van uw toepassing in SQL Database te verbeteren

[In-Memory OLTP](sql-database-in-memory.md) kan worden gebruikt voor het verbeteren van de prestaties van transactie verwerking, gegevens opname en tijdelijke gegevens Scenario's in [Premium-en bedrijfskritiek-laag](sql-database-service-tiers-vcore.md) databases zonder de prijs categorie te verhogen. 

> [!NOTE] 
> Meer informatie over hoe [quorum de werk belasting van de Key Data Base verdubbelt tijdens het verlagen van DTU met 70% met SQL database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Volg deze stappen voor het aannemen van in-Memory OLTP in uw bestaande data base.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Stap 1: Zorg ervoor dat u een Premium-en Bedrijfskritiek-laag database gebruikt

OLTP in het geheugen wordt alleen ondersteund in data bases van Premium en Bedrijfskritiek. In-memory wordt ondersteund als het geretourneerde resultaat 1 (niet 0) is:

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* staat voor *extreme transactie verwerking*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Stap 2: objecten identificeren die moeten worden gemigreerd naar in-Memory OLTP
SSMS bevat een **overzichts rapport voor analyse van transactie prestaties** dat u kunt uitvoeren op een Data Base met een actieve werk belasting. Het rapport bevat tabellen en opgeslagen procedures die kandidaten zijn voor migratie naar in-Memory OLTP.

In SSMS, om het rapport te genereren:

* Klik in de **objectverkenner**met de rechter muisknop op uw database knooppunt.
* Klik op **rapporten** > **standaard rapporten** > overzicht van de **analyse van transactie prestaties**.

Zie [bepalen of een tabel of opgeslagen procedure moet worden getransporteerd naar in-Memory OLTP](https://msdn.microsoft.com/library/dn205133.aspx)voor meer informatie.

## <a name="step-3-create-a-comparable-test-database"></a>Stap 3: een vergelijk bare test database maken
Stel dat het rapport aangeeft dat uw data base een tabel heeft die kan worden geconverteerd naar een tabel die is geoptimaliseerd voor geheugen. We raden u aan eerst te testen om de vermelding te bevestigen door te testen.

U hebt een test kopie van uw productie database nodig. De test database moet zich op hetzelfde niveau van de servicelaag bezien als uw productie database.

U kunt als volgt de test database verfijnen:

1. Maak verbinding met de test database met behulp van SSMS.
2. Om te voor komen dat u de optie WITH (snap shot) in query's nodig hebt, stelt u de optie voor de data base in, zoals wordt weer gegeven in de volgende T-SQL-instructie:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Stap 4: tabellen migreren
U moet een door het geheugen geoptimaliseerde kopie maken en vullen van de tabel die u wilt testen. U kunt deze maken met behulp van:

* De handige wizard voor het optimaliseren van geheugen in SSMS.
* Hand matig T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Wizard geheugen optimalisatie in SSMS
Deze migratie optie gebruiken:

1. Maak verbinding met de test database met SSMS.
2. Klik in de **objectverkenner**met de rechter muisknop op de tabel en klik vervolgens op **geheugen optimalisatie Advisor**.
   
   * De wizard **tabel geheugen optimalisatie Advisor** wordt weer gegeven.
3. Klik in de wizard op **migratie validatie** (of op de knop **volgende** ) om te zien of de tabel niet-ondersteunde functies bevat die niet worden ondersteund in tabellen die zijn geoptimaliseerd voor geheugen. Zie voor meer informatie:
   
   * De *controle lijst voor geheugen optimalisatie* in [geheugen optimalisatie Advisor](https://msdn.microsoft.com/library/dn284308.aspx).
   * De [Transact-SQL-constructs worden niet ondersteund door de in-Memory OLTP](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migreren naar in-Memory OLTP](https://msdn.microsoft.com/library/dn247639.aspx).
4. Als de tabel geen niet-ondersteunde functies heeft, kan de Advisor het werkelijke schema en de gegevens migratie voor u uitvoeren.

#### <a name="manual-t-sql"></a>Hand matig T-SQL
Deze migratie optie gebruiken:

1. Maak verbinding met uw test database met behulp van SSMS (of een soortgelijk hulp programma).
2. Verkrijg het volledige T-SQL-script voor uw tabel en de bijbehorende indexen.
   
   * Klik in SSMS met de rechter muisknop op het knoop punt van de tabel.
   * Klik op **script tabel als** > **maken om te** > **Nieuw query venster**.
3. Voeg met (MEMORY_OPTIMIZED = aan) in het Script-venster toe aan de CREATE TABLE-instructie.
4. Als er een geclusterde index is, wijzigt u deze in niet-geclusterde.
5. Wijzig de naam van de bestaande tabel met behulp van SP_RENAME.
6. Maak de nieuwe voor het geheugen geoptimaliseerde kopie van de tabel door uw bewerkte CREATE TABLE-script uit te voeren.
7. Kopieer de gegevens naar de tabel die is geoptimaliseerd voor geheugen met behulp van INSERT... SELECTEER * IN:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Stap 5 (optioneel): opgeslagen procedures migreren
De functie in het geheugen kan ook een opgeslagen procedure wijzigen voor betere prestaties.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Overwegingen met systeem eigen, gecompileerde, opgeslagen procedures
Een opgeslagen procedure met systeem eigen compilatie moet de volgende opties hebben voor de T-SQL WITH-component:

* NATIVE_COMPILATION
* SCHEMA binding: tabellen waarvan de opgeslagen procedure de kolom definities niet kan wijzigen, worden op welke manier dan ook van invloed op de opgeslagen procedure, tenzij u de opgeslagen procedure verwijdert.

Een systeem eigen module moet een Big [atomische blok kering](https://msdn.microsoft.com/library/dn452281.aspx) voor transactie beheer gebruiken. Er is geen rol voor een expliciete BEGIN TRANSACTION of voor TERUGDRAAI transacties. Als uw code een schending van een bedrijfs regel detecteert, kan het atomische blok met een instructie [throw](https://msdn.microsoft.com/library/ee677615.aspx) worden beëindigd.

### <a name="typical-create-procedure-for-natively-compiled"></a>Typische PROCEDURE voor maken voor systeem eigen compilatie
Normaal gesp roken is de T-SQL voor het maken van een opgeslagen procedure met systeem eigen compilatie vergelijkbaar met de volgende sjabloon:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Voor de TRANSACTION_ISOLATION_LEVEL is moment opname de meest voorkomende waarde voor de systeem eigen, gecompileerde, opgeslagen procedure. Een subset van de andere waarden wordt echter ook ondersteund:
  
  * HERHAAL BARE LEES BEWERKING
  * SERIALIZABLE
* De taal waarde moet aanwezig zijn in de weer gave sys. languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Een opgeslagen procedure migreren
De migratie stappen zijn:

1. Haal het script voor het maken van de PROCEDURE op in de regel matig geïnterpreteerde opgeslagen procedure.
2. Herschrijf de koptekst zodat deze overeenkomt met de vorige sjabloon.
3. Controleer of de T-SQL-code van de opgeslagen procedure gebruikmaakt van functies die niet worden ondersteund voor systeem eigen, gecompileerde, opgeslagen procedures. Implementeer zo nodig tijdelijke oplossingen.
   
   * Zie [migratie problemen voor systeem eigen, gecompileerde, opgeslagen procedures](https://msdn.microsoft.com/library/dn296678.aspx)voor meer informatie.
4. Wijzig de naam van de oude opgeslagen procedure met behulp van SP_RENAME. U kunt deze gewoon verwijderen.
5. Voer uw bewerkte PROCEDURE T-SQL-script maken uit.

## <a name="step-6-run-your-workload-in-test"></a>Stap 6: Voer de werk belasting uit in de test
Voer een workload in uw test database uit die vergelijkbaar is met de werk belasting die wordt uitgevoerd in uw productie database. Hiermee wordt de prestatie verbetering onthuld die wordt behaald door het gebruik van de functie in het geheugen voor tabellen en opgeslagen procedures.

De belangrijkste kenmerken van de werk belasting zijn:

* Aantal gelijktijdige verbindingen.
* Ratio voor lezen/schrijven.

Als u de test-workload wilt aanpassen en uitvoeren, kunt u het handige ostress. exe-hulp programma gebruiken, dat [hier](sql-database-in-memory.md)wordt geïllustreerd.

Als u de netwerk latentie wilt minimaliseren, voert u uw test uit in dezelfde Azure-geografische regio waar de data base zich bevindt.

## <a name="step-7-post-implementation-monitoring"></a>Stap 7: bewaking na de implementatie
Overweeg de prestatie-effecten van uw in-Memory implementaties in productie te controleren:

* [Controleer de opslag in het geheugen](sql-database-in-memory-oltp-monitoring.md).
* [Bewaking van Azure SQL-database met behulp van de dynamische beheerweergave](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Verwante koppelingen
* [OLTP in het geheugen (in-Memory optimalisatie)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Inleiding tot systeem eigen, gecompileerde, opgeslagen procedures](https://msdn.microsoft.com/library/dn133184.aspx)
* [Geheugen optimalisatie Advisor](https://msdn.microsoft.com/library/dn284308.aspx)

