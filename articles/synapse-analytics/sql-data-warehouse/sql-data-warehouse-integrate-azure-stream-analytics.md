---
title: Azure Stream Analytics gebruiken
description: Tips voor het gebruik van Azure Stream Analytics met uw datawarehouse in Azure Synapse voor het ontwikkelen van realtime oplossingen.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e281f8a1fb3959256d836134b4c59f5399deb9bd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633284"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Azure Stream Analytics gebruiken met Azure Synapse Analytics

Azure Stream Analytics is een volledig beheerde service die een laag-latentie, zeer beschikbare, schaalbare complexe gebeurtenisverwerking via streaminggegevens in de cloud biedt. U de basisbeginselen leren door [Inleiding tot Azure Stream Analytics te](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)lezen. U vervolgens leren hoe u een end-to-end oplossing maakt met Stream Analytics door de zelfstudie [van Azure Stream Analytics te](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) volgen.

In dit artikel leert u hoe u uw gegevensmagazijn gebruiken als uitvoerput voor uw Azure Stream Analytics-taken.

## <a name="prerequisites"></a>Vereisten

* Azure Stream Analytics-taak - Als u een Azure Stream Analytics-taak wilt maken, voert u de stappen uit in de zelfstudie [Van Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om:  

    1. Een gebeurtenishub-ingang maken
    2. Toepassing gebeurtenisgenerator configureren en starten
    3. Een Stream Analytics-taak inrichten
    4. Taakinvoer en query opgeven
* Azure Synapse SQL-poolgegevensmagazijn - Als u een nieuw gegevensmagazijn wilt maken, voert u de stappen in de [Quickstart om een nieuw gegevensmagazijn te maken.](create-data-warehouse-portal.md)

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Streaminguitvoer opgeven om naar uw gegevensmagazijn te wijzen

### <a name="step-1"></a>Stap 1

Ga vanuit de Azure-portal naar uw streamanalytics-taak en klik op **Uitvoer** onder het **menu Taaktopologie.**

### <a name="step-2"></a>Stap 2

Klik op de knop **Toevoegen** en kies **SQL Database** in het vervolgkeuzemenu.

![SQL-database kiezen](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Stap 3

Voer de volgende waarden in:

* *Uitvoeralias:* Voer een vriendelijke naam in voor deze taakuitvoer.
* *Abonnement*:
  * Als uw gegevensmagazijn zich in hetzelfde abonnement bevindt als de taak Stream Analytics, klikt u op ***SQL-database selecteren vanuit uw abonnementen***.
  * Als uw database een ander abonnement heeft, klikt u handmatig op SQL-database-instellingen opgeven.
* *Database:* Selecteer de doeldatabase in de vervolgkeuzelijst.
* *Gebruikersnaam:* geef de gebruikersnaam op van een account met schrijfmachtigingen voor de database.
* *Wachtwoord:* geef het wachtwoord op voor het opgegeven gebruikersaccount.
* *Tabel:* Geef de naam op van de doeltabel in de database.
* klik op de knop **Opslaan**

![Voltooid SQL-databaseformulier](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Stap 4

Voordat u een test uitvoeren, moet u de tabel in uw gegevensmagazijn maken.  Voer het volgende script voor het maken van tabels uit met SQL Server Management Studio (SSMS) of uw querytool.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Stap 5

Klik op de Azure-portal voor de taak Stream Analytics op uw taaknaam.  Klik op de knop ***Testen*** in het deelvenster ***Uitvoerdetails.***

![Testknop op Outpout-details](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) Wanneer de verbinding met de database slaagt, ziet u een melding in de portal.

### <a name="step-6"></a>Stap 6

Klik op het menu ***Query*** onder ***Taaktopologie*** en wijzig de query om gegevens in te voegen in de streamuitvoer die u hebt gemaakt.  Klik op de ***geselecteerde queryknop Testen*** om uw query te testen.  Klik op de knop ***Query opslaan*** wanneer de querytest is geslaagd.

![Query opslaan](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Stap 7

Start de azure stream analytics-taak.  Klik op de knop ***Start*** in het menu ***Overzicht.***

![Stream Analytics-taak starten](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klik op de knop ***Start*** in het taakvenster start.

![Klik op Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Andere services integreren](sql-data-warehouse-overview-integrate.md)voor een overzicht van integratie.
Zie [Ontwerpbeslissingen en coderingstechnieken voor datawarehouses voor](sql-data-warehouse-overview-develop.md)meer ontwikkelingstips.
