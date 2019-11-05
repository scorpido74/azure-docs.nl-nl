---
title: SQL Database DAC-pakket en Stream Analytics-taken gebruiken met Azure SQL Database Edge | Microsoft Docs
description: Meer informatie over het gebruik van Stream Analytics-taken in SQL Database Edge
keywords: SQL data base Edge, stream Analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 1b6b3f4e4be9d056bc53fac2eb2f1f3fcd768085
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514990"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>SQL Database DAC-pakket en Stream Analytics-taak gebruiken met SQL Database Edge

Azure SQL Database Edge-Preview is een geoptimaliseerde relationele data base-engine die is afgestemd op IoT-en Edge-implementaties. Het is gebaseerd op de nieuwste versies van de Microsoft SQL Server data base-engine, waarmee toonaangevende prestaties, beveiligings-en query verwerkings mogelijkheden worden geboden. Naast de toonaangevende mogelijkheden voor het beheer van relationele data bases van SQL Server, biedt Azure SQL Database Edge ingebouwde streaming-mogelijkheden voor real-time analyse en complexe gebeurtenis verwerking.

Azure SQL Database Edge biedt ook een systeem eigen implementatie van SQLPackage. exe, waarmee gebruikers tijdens de implementatie van SQL Database Edge een [SQL database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -pakket kunnen implementeren.

Azure SQL Database Edge geeft twee optionele para meters door de *gewenste eigenschappen* van de module voor de IOT Edge-module.

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Veld | Beschrijving |
|------|-------------|
| SQLPackage | Azure Blob Storage-URI voor het *. zip-bestand met het SQL Database DAC-pakket.
| ASAJobInfo | Azure Blob Storage URI voor de ASA Edge-taak. Zie voor meer informatie over het publiceren van de ASA Edge-taak [een ASA-taak publiceren voor SQL database Edge]().

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database DAC-pakketten gebruiken met SQL Database Edge

Volg de onderstaande stappen om een SQL Database DAC-pakket (*. dacpac) met SQL Database Edge te gebruiken.

1. Een SQL Database DAC-pakket maken of extra heren. U kunt de concepten die worden vermeld in [extra DAC uit een bestaande data base](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) gebruiken om een DacPac te genereren voor een bestaande SQL database.

2. Zip-*dacpac* en upload naar een Azure Blob Storage-account. Zie [blobs uploaden, downloaden en vermelden met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor meer informatie over het uploaden van bestanden naar Azure Blob Storage.

3. Genereer een SAS-hand tekening voor het zip-bestand met behulp van Azure Portal. Zie [Toegang delegeren met Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md)voor meer informatie.

4. Werk de configuratie van de module SQL Database-rand bij om de SAS-URI voor het DAC-pakket op te slaan. De module SQL Database Edge bijwerken

    1. Blader op het Azure Portal naar de implementatie van IoT Hub.

    2. Klik in het linkerdeel venster op **IOT Edge**.

    3. Zoek en klik op de pagina **IOT Edge** op de IOT Edge waar de module SQL database Edge is geïmplementeerd.

    4. Klik op de pagina apparaat *IOT Edge* op **set**. 

    5. Klik op de pagina **modules instellen** op *configureren* voor de module SQL database Edge. 

    6. Selecteer in het deel venster **aangepaste modules IOT Edge** de *gewenste eigenschappen van modules configureren* en werk vervolgens de gewenste eigenschappen bij om de URI voor de SQLPackage-optie op te geven, zoals wordt weer gegeven in het onderstaande voor beeld. 

        > [!NOTE]
        > De SAS-URI hieronder is alleen ter illustratie. Vervang de URI door de daad werkelijke URI van uw implementatie.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Klik op **Opslaan**.

    8. Klik op de pagina **modules instellen** op *volgende*.

    9. Klik op de pagina **modules instellen** op *volgende* en klik vervolgens op **verzenden**.

5. Post de module-update, het dacpac-bestand wordt gedownload, uitgepakt en geïmplementeerd op basis van het SQL Database Edge-exemplaar.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Streaming-taken gebruiken met SQL Database Edge

Azure SQL Database Edge heeft een systeem eigen implementatie van stream Analytics runtime. Hiermee kunnen gebruikers een Azure Stream Analytics Edge-taak maken en die taak implementeren als een SQL Database Edge streaming-taak. Volg de onderstaande stappen om een stream Analytics Edge-taak te maken.

1. Blader naar het Azure Portal met behulp van de voor beeld- [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true). Met deze Preview-URL kunnen gebruikers SQL Database uitvoer configureren voor een stream Analytics Edge-taak.

2. Een nieuwe **Azure stream Analytics op IOT Edge** -taak maken en de doel **rand**van de hosting omgeving kiezen.

3. Geef de *invoer* en *uitvoer* voor de Azure stream Analytics taak op. Elke SQL-uitvoer (hieronder geconfigureerd) is gekoppeld aan één tabel in de data base. Als er gegevens naar meerdere tabellen moeten worden gestreamd, moet u meerdere SQL Database-uitvoer maken. De SQL-uitvoer kan worden geconfigureerd om naar verschillende data bases te verwijzen.

    *Invoer: Kies EdgeHub als invoer voor de Edge-taak en vul de resource gegevens in.*

    *Uitvoer: Selecteer SQL Database als uitvoer, SQL Database instellingen hand matig opgeven en geef de configuratie gegevens op voor de data base en tabel.*

    |Veld      | Beschrijving |
    |---------------|-------------|
    |Uitvoeralias | De naam van de uitvoer alias.|
    |Database | De naam van de SQL Database. Dit moet een geldige database naam zijn, die voor komt op het SQL Database Edge-exemplaar.|
    |Servernaam | Naam (of IP-adres) en Details van het poort nummer voor het SQL-exemplaar. Voor een SQL Database Edge-implementatie kunt u **TCP:., 1433** als de server naam gebruiken.|
    |Gebruikersnaam | SQL-aanmeldings account met toegang tot de gegevens lezer en gegevens schrijver tot de hierboven vermelde data base.|
    |Wachtwoord | Wacht woord voor het SQL-aanmeldings account dat hierboven wordt vermeld.|
    |Tabel | De naam van de tabel die wordt uitgevoerd voor de streaming-taak.|
    |Partities overnemen| Met deze SQL-uitvoer configuratie optie kunt u het partitie schema van de vorige query stap of-invoer overnemen. Als u deze functie inschakelt en schrijft naar een tabel op basis van een schijf en een volledig parallelle topologie voor uw taak hebt, krijgt u een betere door voer te zien.|
    |Batch grootte| Batch grootte is het maximum aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie.|

    Hieronder vindt u een voor beeld van een invoer/uitvoer-configuratie:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Zie [Azure stream Analytics output to Azure SQL database](../stream-analytics/stream-analytics-sql-output-perf.md)voor meer informatie over de SQL-uitvoer adapter voor Azure stream Analytics.

4. Definieer de ASA-taak query voor de Edge-taak. Deze query moet de gedefinieerde invoer/uitvoer aliassen gebruiken als de invoer-en uitvoer namen in de query. Zie [Stream Analytics-query taal](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)voor meer informatie.

5. Stel de instellingen voor het opslag account voor de Edge-taak in. Het opslag account wordt gebruikt als het publicatie doel voor de Edge-taak.

6. Selecteer onder configureren de optie publiceren en klik op de knop publiceren. Sla de SAS-URL op voor gebruik met de module SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>De stream Analytics Edge-taak implementeren naar de SQL Database Edge

Als u de streaming-taak wilt implementeren in de module SQL Database Edge, moet u de configuratie van de module SQL Database Edge bijwerken zodat de SAS-URI voor de streaming-taak uit de bovenstaande stap wordt vermeld. De module SQL Database Edge bijwerken

1. Blader op het Azure Portal naar de implementatie van IoT Hub.

2. Klik in het linkerdeel venster op **IOT Edge**.

3. Zoek en klik op de pagina **IOT Edge** op de IOT Edge waar de module SQL database Edge is geïmplementeerd.

4. Klik op de pagina apparaat *IOT Edge* op **set**. 

5. Klik op de pagina **modules instellen** op *configureren* voor de module SQL database Edge. 

6. Selecteer in het deel venster **aangepaste modules IOT Edge** de *gewenste eigenschappen van modules configureren* en werk vervolgens de gewenste eigenschappen bij om de URI voor de ASAJobInfo-optie op te geven, zoals wordt weer gegeven in het onderstaande voor beeld. 

    > [!NOTE]
    > De SAS-URI hieronder is alleen ter illustratie. Vervang de URI door de daad werkelijke URI van uw implementatie.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Klik op **Opslaan**.

8. Klik op de pagina **modules instellen** op *volgende*.

9. Klik op de pagina **modules instellen** op *volgende* en klik vervolgens op **verzenden**.

10. Post the module update, het Stream Analytics-taak bestand wordt gedownload, uitgepakt en geïmplementeerd op basis van het SQL Database Edge-exemplaar.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL database Edge](https://azure.microsoft.com/services/sql-database-edge/)voor prijzen en Details over de beschik baarheid.
- Aanvraag om Azure SQL Database Edge in te scha kelen voor uw abonnement.
- Ga als volgt te werk om aan de slag te gaan:
  - [SQL Database rand implementeren via Azure Portal](deploy-portal.md)
