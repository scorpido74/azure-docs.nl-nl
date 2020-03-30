---
title: In-Memory OLTP verbetert SQL txn perf
description: Adopteer In-Memory OLTP om de transactionele prestaties in een bestaande SQL-database te verbeteren.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: 653ed75341d5d56ecbe06cb59f0efafa1e68aa0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067273"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>In-Memory OLTP gebruiken om de prestaties van uw toepassing in SQL Database te verbeteren

[In-Memory OLTP](sql-database-in-memory.md) kan worden gebruikt om de prestaties van transactieverwerking, gegevensopname en tijdelijke gegevensscenario's te verbeteren in [Premium- en Business Critical-laagdatabases](sql-database-service-tiers-vcore.md) zonder de prijscategorie te verhogen. 

> [!NOTE] 
> Ontdek hoe [Quorum de werkbelasting van de sleuteldatabase verdubbelt en DTU met 70% verlaagt met SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Volg deze stappen om In-Memory OLTP in uw bestaande database te nemen.

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>Stap 1: Zorg ervoor dat u een database met Premium- en Bedrijfskritieke lagen gebruikt

In-Memory OLTP wordt alleen ondersteund in Premium- en Business Critical-laagdatabases. In-Memory wordt ondersteund als het geretourneerde resultaat 1 (niet 0) is:

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* staat voor *Extreme Transaction Processing*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Stap 2: Objecten identificeren om te migreren naar In-Memory OLTP
SSMS bevat een rapport **over transactieprestatieanalyseanalyse** dat u uitvoeren tegen een database met een actieve workload. Het rapport identificeert tabellen en opgeslagen procedures die kandidaten zijn voor migratie naar In-Memory OLTP.

In SSMS, om het rapport te genereren:

* Klik in de **Object Explorer**met de rechtermuisknop op het databaseknooppunt.
* Klik **op Overzicht van** > de analyse van**de transactieprestatieanalyse****rapporten standaardrapporten** > .

Zie [Bepalen of een tabel of opgeslagen procedure moet worden geport naar in-memory OLTP](https://msdn.microsoft.com/library/dn205133.aspx)voor meer informatie.

## <a name="step-3-create-a-comparable-test-database"></a>Stap 3: Een vergelijkbare testdatabase maken
Stel dat het rapport aangeeft dat uw database een tabel heeft die baat heeft bij de omzetting naar een voor geheugen geoptimaliseerde tabel. We raden u aan om eerst te testen om de indicatie te bevestigen door te testen.

U hebt een testkopie van uw productiedatabase nodig. De testdatabase moet zich op hetzelfde serviceniveau bevinden als uw productiedatabase.

Als u het testen wilt vergemakkelijken, past u uw testdatabase als volgt aan:

1. Maak verbinding met de testdatabase met Behulp van SSMS.
2. Als u wilt voorkomen dat de optie MET (SNAPSHOT) in query's nodig is, stelt u de databaseoptie in zoals weergegeven in de volgende T-SQL-instructie:
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Stap 4: Tabellen migreren
U moet een voor geheugen geoptimaliseerde kopie maken en invullen van de tabel die u wilt testen. U het maken met behulp van:

* De handige Wizard Geheugenoptimalisatie in SSMS.
* Handmatige T-SQL.

#### <a name="memory-optimization-wizard-in-ssms"></a>Wizard Geheugenoptimalisatie in SSMS
Ga als volgt te werk om deze migratieoptie te gebruiken:

1. Maak verbinding met de testdatabase met SSMS.
2. Klik in de **Object Explorer**met de rechtermuisknop op de tabel en klik vervolgens op **Adviseur Geheugenoptimalisatie**.
   
   * De wizard **Adviseur Voor het optimaliseren van** tabelgeheugen wordt weergegeven.
3. Klik in de wizard op **Migratievalidatie** (of de knop **Volgende)** om te zien of de tabel niet-ondersteunde functies heeft die niet worden ondersteund in tabellen die zijn geoptimaliseerd voor geheugen. Zie voor meer informatie:
   
   * De *geheugen optimalisatie checklist* in Memory Optimization [Advisor](https://msdn.microsoft.com/library/dn284308.aspx).
   * [Transact-SQL Constructs Niet ondersteund door In-Memory OLTP](https://msdn.microsoft.com/library/dn246937.aspx).
   * [Migreren naar in-memory OLTP](https://msdn.microsoft.com/library/dn247639.aspx).
4. Als de tabel geen niet-ondersteunde functies heeft, kan de adviseur het werkelijke schema en de gegevensmigratie voor u uitvoeren.

#### <a name="manual-t-sql"></a>Handmatige T-SQL
Ga als volgt te werk om deze migratieoptie te gebruiken:

1. Maak verbinding met uw testdatabase met Behulp van SSMS (of een soortgelijk hulpprogramma).
2. Verkrijg het volledige T-SQL-script voor uw tabel en de bijbehorende indexen.
   
   * Klik in SSMS met de rechtermuisknop op het tafelknooppunt.
   * Klik **op Scripttabel als** > **MAKEN naar** > **nieuw queryvenster**.
3. Voeg in het scriptvenster met (MEMORY_OPTIMIZED = AAN) toe aan de instructie TABEL MAKEN.
4. Als er een geclusterde index is, wijzigt u deze in NIET-GECLUSTERD.
5. Wijzig de naam van de bestaande tabel met SP_RENAME.
6. Maak de nieuwe voor geheugen geoptimaliseerde kopie van de tabel door het bewerkte TABEL-script MAKEN uit te voeren.
7. Kopieer de gegevens naar uw geheugengeoptimaliseerde tabel met INSERT... SELECTEER * IN:

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Stap 5 (optioneel): Opgeslagen procedures migreren
De functie In-memory kan ook een opgeslagen procedure wijzigen voor betere prestaties.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Overwegingen met native gecompileerde opgeslagen procedures
Een native gecompileerde opgeslagen procedure moet de volgende opties op de T-SQL WITH-clausule hebben:

* NATIVE_COMPILATION
* SCHEMABINDING: tabellen betekenen dat de opgeslagen procedure hun kolomdefinities niet kan wijzigen op een manier die van invloed zou zijn op de opgeslagen procedure, tenzij u de opgeslagen procedure laat vallen.

Een native module moet één grote [ATOMIC-blokken](https://msdn.microsoft.com/library/dn452281.aspx) gebruiken voor transactiebeheer. Er is geen rol voor een expliciete BEGIN-transactie of voor terugdraaitransactie. Als uw code een schending van een bedrijfsregel detecteert, kan deze het atoomblok beëindigen met een [THROW-instructie.](https://msdn.microsoft.com/library/ee677615.aspx)

### <a name="typical-create-procedure-for-natively-compiled"></a>Typische CREATE-procedure voor native compiled
Meestal is de T-SQL om een native gecompileerde opgeslagen procedure te maken vergelijkbaar met de volgende sjabloon:

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

* Voor de TRANSACTION_ISOLATION_LEVEL is SNAPSHOT de meest voorkomende waarde voor de native gecompileerde opgeslagen procedure. Een subset van de andere waarden wordt echter ook ondersteund:
  
  * HERHAALBAAR GELEZEN
  * Serializable
* De TAAL-waarde moet aanwezig zijn in de weergave sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Een opgeslagen procedure migreren
De migratiestappen zijn:

1. Verkrijg het SCRIPT VAN DE MAAKPROCEDURE aan de regelmatige geïnterpreteerde opgeslagen procedure.
2. Herschrijf de koptekst naar de vorige sjabloon.
3. Controleer of de opgeslagen procedure T-SQL-code functies gebruikt die niet worden ondersteund voor native gecompileerde opgeslagen procedures. Implementeer oplossingen indien nodig.
   
   * Zie [migratieproblemen voor native gecompileerde opgeslagen procedures](https://msdn.microsoft.com/library/dn296678.aspx)voor meer informatie .
4. Wijzig de naam van de oude opgeslagen procedure met behulp van SP_RENAME. Of gewoon LATEN VALLEN.
5. Voer uw bewerkte CREATE PROCEDURE T-SQL-script uit.

## <a name="step-6-run-your-workload-in-test"></a>Stap 6: Uw werkbelasting uitvoeren in de test
Voer een werkbelasting uit in uw testdatabase die vergelijkbaar is met de werkbelasting die wordt uitgevoerd in uw productiedatabase. Dit moet de prestatiewinst onthullen die wordt bereikt door uw gebruik van de In-Memory-functie voor tabellen en opgeslagen procedures.

Belangrijke kenmerken van de werkbelasting zijn:

* Aantal gelijktijdige verbindingen.
* Lees/schrijfverhouding.

Om de testwerklast aan te passen en uit te voeren, u overwegen de handige ostress.exe-tool te gebruiken, die [hier](sql-database-in-memory.md)wordt geïllustreerd.

Voer uw test uit in dezelfde azure-geografische regio waar de database bestaat om de netwerklatentie te minimaliseren.

## <a name="step-7-post-implementation-monitoring"></a>Stap 7: Monitoring na de implementatie
Houd rekening met het monitoren van de prestatie-effecten van uw In-Memory-implementaties in productie:

* [Opslag in het geheugen bewaken](sql-database-in-memory-oltp-monitoring.md).
* [Bewaking van Azure SQL-database met behulp van de dynamische beheerweergave](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Verwante koppelingen
* [In-Memory OLTP (In-Memory Optimization)](https://msdn.microsoft.com/library/dn133186.aspx)
* [Inleiding tot native gecompileerde opgeslagen procedures](https://msdn.microsoft.com/library/dn133184.aspx)
* [Adviseur geheugenoptimalisatie](https://msdn.microsoft.com/library/dn284308.aspx)

