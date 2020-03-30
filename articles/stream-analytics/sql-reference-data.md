---
title: SQL-databasereferentiegegevens gebruiken in een Azure Stream Analytics-taak
description: In dit artikel wordt beschreven hoe u een SQL-database gebruikt als referentiegegevensinvoer voor een Azure Stream Analytics-taak in de Azure-portal en in Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: aebb590d93b3fb26151f15c176a2941845cdd50c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426501"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Referentiegegevens uit een SQL-database gebruiken voor een Azure Stream Analytics-taak

Azure Stream Analytics ondersteunt Azure SQL Database als invoerbron voor referentiegegevens. U SQL Database gebruiken als referentiegegevens voor uw Stream Analytics-taak in de Azure-portal en in Visual Studio met Stream Analytics-hulpprogramma's. Dit artikel laat zien hoe beide methoden te doen.

## <a name="azure-portal"></a>Azure Portal

Gebruik de volgende stappen om Azure SQL Database toe te voegen als referentiebron met behulp van de Azure-portal:

### <a name="portal-prerequisites"></a>Portal-vereisten

1. Een Stream Analytics-taak maken.

2. Maak een opslagaccount aan dat kan worden gebruikt door de streamanalytics-taak.

3. Maak uw Azure SQL Database met een gegevensset die door de taak Stream Analytics als referentiegegevens moet worden gebruikt.

### <a name="define-sql-database-reference-data-input"></a>Sql-databasereferentiegegevensinvoer definiëren

1. Selecteer in de taak Stream Analytics de optie **Inputs** onder **Job topologie**. Klik **op Referentieinvoer toevoegen** en kies **SQL-database**.

   ![Stream Analytics-taakinvoer](./media/sql-reference-data/stream-analytics-inputs.png)

2. Vul de invoerconfiguraties van Stream Analytics in. Kies de databasenaam, servernaam, gebruikersnaam en wachtwoord. Als u wilt dat de invoer van referentiegegevens periodiek wordt vernieuwd, kiest u 'Aan' om de verversingsfrequentie in DD:HH:MM op te geven. Als u grote gegevenssets hebt met een korte verversingsfrequentie, u een [deltaquery](sql-reference-data.md#delta-query)gebruiken.

   ![SQL-databasereferentieconfiguratie](./media/sql-reference-data/sql-input-config.png)

3. Test de momentopnamequery in de SQL-queryeditor. Zie [De SQL-queryeditor van de Azure-portal gebruiken om verbinding te maken en querygegevens te gebruiken](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Opslagaccount opgeven in taakconfig

Navigeer naar **Instellingen voor opslagaccount** onder **Opslagaccount configureren** en selecteer **Opslagaccount toevoegen**.

   ![Instellingen voor Stream Analytics-opslagaccount](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Taak starten

Zodra u andere ingangen, uitvoer en query hebt geconfigureerd, u de taak Stream Analytics starten.

## <a name="tools-for-visual-studio"></a>Hulpprogramma's voor Visual Studio

Gebruik de volgende stappen om Azure SQL Database toe te voegen als referentiebron met Visual Studio:

### <a name="visual-studio-prerequisites"></a>Vereisten voor Visual Studio

1. [Installeer de tools Stream Analytics voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md). De volgende versies van Visual Studio worden ondersteund:

   * Visual Studio 2015
   * Visual Studio 2019

2. Maak kennis met de [Stream Analytics-tools voor Visual Studio](stream-analytics-quick-create-vs.md) snelaan.

3. Een opslagaccount maken.

### <a name="create-a-sql-database-table"></a>Een SQL-databasetabel maken

Gebruik SQL Server Management Studio om een tabel te maken om uw referentiegegevens op te slaan. Zie [Uw eerste Azure SQL-database ontwerpen met SSMS](../sql-database/sql-database-design-first-database.md) voor meer informatie.

De voorbeeldtabel die in het volgende voorbeeld wordt gebruikt, is gemaakt op de volgende instructie:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Kies uw abonnement

1. Selecteer in Visual Studio in het menu **Beeld** de optie **Server Explorer**.

2. Klik met de rechtermuisknop op **Azure,** selecteer **Verbinding maken met Microsoft Azure-abonnement**en meld u aan met uw Azure-account.

### <a name="create-a-stream-analytics-project"></a>Een Stream Analytics-project maken

1. Selecteer **Bestand > Nieuw > Project**. 

2. Selecteer **Stream Analytics**in de sjablonenlijst aan de linkerkant en selecteer vervolgens **Azure Stream Analytics Application**. 

3. Voer de naam **projectnaam**, **locatie**en **oplossing**in en selecteer **OK**.

   ![Nieuw Stream Analytics-project in Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Sql-databasereferentiegegevensinvoer definiëren

1. Maak een nieuwe invoer.

   ![Nieuwe Stream Analytics-invoer in Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Dubbelklik op **Input.json** in de **Solution Explorer**.

3. Vul de **invoerconfiguratie van Stream Analytics**in . Kies de databasenaam, servernaam, vernieuwingstype en verversingsfrequentie. Geef de verversingsfrequentie `DD:HH:MM`op in de indeling .

   ![Configuratie van Stream Analytics-invoer in Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Als u 'Slechts één keer uitvoeren' of 'Periodiek uitvoeren' kiest, wordt één SQL CodeBehind-bestand met de naam **[Input Alias].snapshot.sql** gegenereerd in het project onder het knooppunt van het **bestand Input.json.**

   ![Invoercode achter in Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Als u kiest voor 'Periodiek vernieuwen met Delta', worden twee SQL CodeBehind-bestanden gegenereerd: **[Input Alias].snapshot.sql** en **[Input Alias].delta.sql**.

   ![Code achter in solution explorer](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Open het SQL-bestand in de editor en schrijf de SQL-query.

5. Als u Visual Studio 2019 gebruikt en sql servergegevenstools hebt geïnstalleerd, u de query testen door op **Uitvoeren**te klikken. Er verschijnt een wizardvenster om u te helpen verbinding te maken met de SQL-database en het queryresultaat wordt weergegeven in het venster onderaan.

### <a name="specify-storage-account"></a>Opslagaccount opgeven

Open **JobConfig.json** om het opslagaccount op te geven voor het opslaan van SQL-referentiemomentopnamen.

   ![Functieconfiguratie van Stream Analytics in Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Lokaal testen en implementeren naar Azure

Voordat u de taak implementeert in Azure, u de querylogica lokaal testen op basis van live invoergegevens. Zie [Live-gegevens lokaal testen met Azure Stream Analytics-hulpprogramma's voor Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)voor meer informatie over deze functie. Wanneer u klaar bent met testen, klikt u op **Verzenden naar Azure**. Raadpleeg de [Analyse van een stream maken met behulp van de Azure Stream Analytics-hulpprogramma's voor Visual Studio](stream-analytics-quick-create-vs.md) snel aan de slag om te leren hoe u de taak starten.

## <a name="delta-query"></a>Deltaquery

Bij het gebruik van de deltaquery worden [tijdelijke tabellen in Azure SQL Database](../sql-database/sql-database-temporal-tables.md) aanbevolen.

1. Maak een tijdelijke tabel in Azure SQL Database.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Auteur van de momentopnamequery. 

   Gebruik ** \@** de parameter snapshotTime om de runtime van Stream Analytics te instrueren om de referentiegegevensset te verkrijgen van de temporele sql-databasetabel die geldig is op het systeemtijd. Als u deze parameter niet opgeeft, loopt u het risico een onjuiste basisreferentiegegevensset te verkrijgen als gevolg van klokscheeftrekkingen. Een voorbeeld van volledige momentopnamequery wordt hieronder weergegeven:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Auteur van de deltaquery. 
   
   Met deze query worden alle rijen in uw SQL-database opgehaald die binnen een begintijd zijn ingevoegd of verwijderd, ** \@deltaStartTime**en een eindtijd ** \@deltaEndTime**. De deltaquery moet dezelfde kolommen retourneren als de momentopnamequery en de **_kolombewerking_**. In deze kolom wordt gedefinieerd of de rij wordt ingevoegd of verwijderd tussen ** \@deltaStartTime** en ** \@deltaEndTime**. De resulterende rijen worden gemarkeerd als **1** als de records zijn ingevoegd, of **2** als ze zijn verwijderd. 

   Voor records die zijn bijgewerkt, doet de tijdelijke tabel de boekhouding door een invoeg- en verwijderingsbewerking vast te leggen. De runtime van Stream Analytics past vervolgens de resultaten van de deltaquery toe op de vorige momentopname om de referentiegegevens up-to-date te houden. Een voorbeeld van deltaquery wordt hieronder weergegeven:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Houd er rekening mee dat de runtime van Stream Analytics de momentopnamequery periodiek kan uitvoeren naast de deltaquery om controlepunten op te slaan.

## <a name="test-your-query"></a>De query testen
   Het is belangrijk om te controleren of uw query de verwachte gegevensset retourkeert die de taak Stream Analytics als referentiegegevens zal gebruiken. Als u uw query wilt testen, gaat u naar Invoer onder sectie Taaktopologie op portal. Vervolgens u Voorbeeldgegevens selecteren op uw SQL Database Reference-invoer. Nadat het voorbeeld beschikbaar is, u het bestand downloaden en controleren of de gegevens die worden geretourneerd zoals verwacht, zijn geretourneerd. Als u uw ontwikkel- en testiteraties wilt optimaliseren, is het raadzaam om de [Tools Stream Analytics voor Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)te gebruiken. U ook een ander hulpmiddel van uw voorkeur gebruiken om er eerst voor te zorgen dat de query de juiste resultaten van uw Azure SQL-database retourafgeeft en dat vervolgens gebruikt in uw Stream Analytics-taak. 

## <a name="faqs"></a>Veelgestelde vragen

**Maak ik extra kosten door SQL-referentiegegevensinvoer te gebruiken in Azure Stream Analytics?**

Er zijn geen extra [kosten per streaming-eenheid](https://azure.microsoft.com/pricing/details/stream-analytics/) in de streamanalytics-taak. De functie Stream Analytics moet echter een gekoppeld Azure-opslagaccount hebben. De taak Stream Analytics query's de SQL DB (tijdens het begin en vernieuwen interval) om de referentiegegevensset op te halen en slaat die momentopname op in het opslagaccount. Als u deze momentopnamen opslaat, worden extra kosten in rekening gebracht die worden beschreven op de [prijspagina](https://azure.microsoft.com/pricing/details/storage/) voor Azure-opslagaccount.

**Hoe weet ik of momentopname referentiegegevens wordt opgevraagd vanuit SQL DB en wordt gebruikt in de Azure Stream Analytics-taak?**

Er zijn twee statistieken gefilterd op logische naam (onder Metrics Azure Portal) die u gebruiken om de status van de SQL-databasereferentiegegevensinvoer te controleren.

   * Invoergebeurtenissen: met deze statistiek wordt het aantal records gemeten dat is geladen in de referentiegegevensset van de SQL-database.
   * InputEventBytes: met deze statistiek wordt de grootte gemeten van de momentopname van de referentiegegevens die is geladen in het geheugen van de taak Stream Analytics. 

De combinatie van beide statistieken kan worden gebruikt om af te leiden of de taak de SQL-database opvraagt om de referentiegegevensset op te halen en deze vervolgens in het geheugen te laden.

**Heb ik een speciaal type Azure SQL-database nodig?**

Azure Stream Analytics werkt met elk type Azure SQL Database. Het is echter belangrijk om te begrijpen dat de verversingsfrequentie die is ingesteld voor uw referentiegegevensinvoer van invloed kan zijn op uw querybelasting. Als u de deltaqueryoptie wilt gebruiken, wordt aanbevolen tijdelijke tabellen te gebruiken in Azure SQL Database.

**Waarom slaat Azure Stream Analytics momentopnamen op in azure storage-account?**

Stream Analytics garandeert Exactly-once-gebeurtenissenverwerking Least-once-levering van gebeurtenissen. In gevallen waarin tijdelijke problemen van invloed zijn op uw taak, is een kleine hoeveelheid herhaling nodig om de status te herstellen. Om replay in te schakelen, is het vereist dat deze momentopnamen worden opgeslagen in een Azure Storage-account. Zie [Controlepunt en replay-concepten in Azure Stream Analytics-taken](stream-analytics-concepts-checkpoint-replay.md)voor meer informatie over het opnieuw afspelen van checkpoints.

## <a name="next-steps"></a>Volgende stappen

* [Referentiegegevens gebruiken voor opzoekingen in Stream Analytics](stream-analytics-use-reference-data.md)
* [Snelstart: Een Stream Analytics-taak maken met behulp van de Azure Stream Analytics-tools voor Visual Studio](stream-analytics-quick-create-vs.md)
* [Live gegevens lokaal testen met Azure Stream Analytics-hulpprogramma's voor Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)
