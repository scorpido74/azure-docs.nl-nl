---
title: SQL Database DAC-pakketten en Stream Analytics-taken gebruiken met Azure SQL Database Edge | Microsoft Docs
description: Meer informatie over het gebruik van Stream Analytics-taken in SQL Database Edge
keywords: SQL data base Edge, stream Analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384162"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>SQL Database DAC-pakketten en Stream Analytics-taken gebruiken met SQL Database Edge

Azure SQL Database Edge-Preview is een geoptimaliseerde relationele data base-engine die is afgestemd op IoT-en Edge-implementaties. Het is gebaseerd op de nieuwste versies van de Microsoft SQL Server data base-engine, waarmee toonaangevende prestaties, beveiliging en verwerkings mogelijkheden voor query's worden geboden. Naast de toonaangevende mogelijkheden voor het beheer van relationele data bases van SQL Server, biedt Azure SQL Database Edge ingebouwde streaming-mogelijkheden voor real-time analyse en complexe gebeurtenis verwerking.

Azure SQL Database Edge biedt ook een systeem eigen implementatie van SqlPackage. exe, waarmee u een [SQL database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) -pakket kunt implementeren tijdens de implementatie van SQL database Edge.

Azure SQL Database Edge maakt twee optionele para meters via de `module twin's desired properties` optie van de module IoT Edge:

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
| SqlPackage | Azure Blob-opslag-URI voor het *. zip-bestand dat de SQL Database DAC-pakket bevat.
| ASAJobInfo | Azure Blob-opslag-URI voor de ASA Edge-taak. Zie [een ASA Edge-taak publiceren voor SQL database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)voor meer informatie.

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database DAC-pakketten gebruiken met SQL Database Edge

Voer de volgende stappen uit om een SQL Database DAC-pakket (*. dacpac) te gebruiken met SQL Database Edge:

1. Een SQL Database DAC-pakket maken of extra heren. Zie [een DAC extra heren uit een Data Base](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) voor informatie over het genereren van een DAC-pakket voor een bestaande SQL Server-Data Base.

2. Zip-dacpac en upload het naar een Azure Blob Storage-account. Zie [blobs uploaden, downloaden en weer geven met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor meer informatie over het uploaden van bestanden naar Azure Blob-opslag.

3. Genereer een Shared Access Signature voor het zip-bestand met behulp van de Azure Portal. Zie [Toegang delegeren met Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md)voor meer informatie.

4. Werk de configuratie van de module SQL Database-rand bij om de gedeelde toegangs-URI voor het DAC-pakket op te geven. Voer de volgende stappen uit om de module SQL Database Edge bij te werken:

    1. Ga in het Azure Portal naar de implementatie van IoT Hub.

    2. Selecteer **IoT Edge** in het linkerdeelvenster.

    3. Zoek en selecteer op de pagina **IOT Edge** de IOT-rand waar de module SQL database Edge is geïmplementeerd.

    4. Selecteer op de pagina **apparaat IOT edge apparaten** de optie **module instellen**.

    5. Selecteer op de pagina **modules instellen** de optie **configureren** voor de module SQL database Edge.

    6. Selecteer in het deel venster **aangepaste Modules IOT Edge** de **gewenste eigenschappen van module configureren**. Werk de gewenste eigenschappen bij om de URI voor de `SQLPackage` optie op te geven, zoals wordt weer gegeven in het volgende voor beeld.

        > [!NOTE]
        > De SAS-URI in de volgende JSON is slechts een voor beeld. Vervang de URI door de daad werkelijke URI van uw implementatie.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Selecteer **Opslaan**.

    8. Selecteer op de pagina **modules instellen** de optie **volgende**.

    9. Selecteer op de pagina **modules instellen** de optie **volgende** en vervolgens **verzenden**.

5. Na het bijwerken van de module wordt het DAC-pakket bestand gedownload, uitgepakt en geïmplementeerd op basis van het SQL Database Edge-exemplaar.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Streaming-taken gebruiken met SQL Database Edge

Azure SQL Database Edge heeft een systeem eigen implementatie van de stream Analytics-runtime. Met deze implementatie kunt u een Azure Stream Analytics Edge-taak maken en die taak implementeren als een SQL Database Edge streaming-taak. Voer de volgende stappen uit om een Stream Analytics Edge-taak te maken:

1. Ga naar de Azure Portal met behulp van de voor beeld- [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true). Met deze Preview-URL kunt u SQL Database uitvoer voor een Stream Analytics Edge-taak configureren.

2. Een nieuwe **Azure stream Analytics op IOT Edge** -taak maken. Kies de hosting omgeving die de **rand**van het doel heeft.

3. Definieer een invoer en uitvoer voor de Azure Stream Analytics taak. Elke SQL-uitvoer, die u hier instelt, is gekoppeld aan één tabel in de data base. Als u gegevens naar meerdere tabellen wilt streamen, moet u meerdere SQL Database-uitvoer maken. U kunt de SQL-uitvoer zodanig configureren dat deze naar verschillende data bases verwijst.

    **Invoer**. Kies EdgeHub als invoer voor de Edge-taak en geef de resource gegevens op.

    **Uitvoer**. Selecteer SQL Database de als uitvoer. Selecteer **SQL database-instellingen hand matig opgeven**. Geef de configuratie gegevens op voor de data base en tabel.

    |Veld      | Beschrijving |
    |---------------|-------------|
    |Uitvoeralias | De naam van de uitvoer alias.|
    |Database | De naam van de SQL database. Dit moet een geldige naam zijn van een Data Base die zich op het SQL Database Edge-exemplaar bevindt.|
    |Servernaam | Naam (of IP-adres) en Details van het poort nummer voor het SQL-exemplaar. Voor een SQL Database Edge-implementatie kunt u **TCP:., 1433** gebruiken voor de server naam.|
    |Gebruikersnaam | SQL-aanmeldings account met gegevens lezer en gegevens schrijver toegang tot de data base die u eerder hebt opgegeven.|
    |Wachtwoord | Wacht woord voor het SQL-aanmeldings account dat u eerder hebt opgegeven.|
    |Tabel | De naam van de tabel die wordt uitgevoerd voor de streaming-taak.|
    |Partities overnemen| Hiermee wordt het schema voor het partitioneren van de vorige query stap of-invoer ingeschakeld. Wanneer deze optie is ingeschakeld, kunt u een betere door Voer verwachten wanneer u naar een op schijf gebaseerde tabel schrijft en een volledig parallelle topologie voor uw taak hebt.|
    |Batch grootte| Het maximum aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie.|

    Hier volgt een voor beeld van een invoer/uitvoer-configuratie:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
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

6. Selecteer onder **configureren**de optie **publiceren**en selecteer vervolgens de knop **publiceren** . Sla de SAS-URI op voor gebruik met de module SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>De Stream Analytics Edge-taak implementeren op SQL Database Edge

Als u de streaming-taak wilt implementeren in de module SQL Database Edge, moet u de configuratie van de module SQL Database Edge bijwerken met de SAS-URI voor de streaming-taak uit de vorige stap. De module SQL Database Edge bijwerken:

1. Ga in het Azure Portal naar de implementatie van IoT Hub.

2. Selecteer **IoT Edge** in het linkerdeelvenster.

3. Zoek en selecteer op de pagina **IOT Edge** de IOT-rand waar de module SQL database Edge is geïmplementeerd.

4. Selecteer op de pagina **apparaat IOT edge apparaten** de optie **module instellen**.

5. Selecteer op de pagina **modules instellen** de optie **configureren** voor de module SQL database Edge.

6. Selecteer in het deel venster **aangepaste Modules IOT Edge** de **gewenste eigenschappen van module configureren**. Werk de gewenste eigenschappen bij om de URI voor de `ASAJobInfo` optie op te geven, zoals wordt weer gegeven in het volgende voor beeld.

    > [!NOTE]
    > De SAS-URI in de volgende JSON is slechts een voor beeld. Vervang de URI door de daad werkelijke URI van uw implementatie.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Selecteer **Opslaan**.

8. Selecteer op de pagina **modules instellen** de optie **volgende**.

9. Selecteer op de pagina **modules instellen** de optie **volgende** en vervolgens **verzenden**.

10. Na het bijwerken van de module wordt het Stream Analytics-taak bestand gedownload, uitgepakt en geïmplementeerd op basis van het SQL Database Edge-exemplaar.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL database Edge](https://azure.microsoft.com/services/sql-database-edge/)voor prijzen en Details van de beschik baarheid.
- Aanvraag voor het inschakelen van Azure SQL Database Edge voor uw abonnement.
- Als u aan de slag wilt gaan, raadpleegt u [SQL database Edge implementeren via Azure Portal](deploy-portal.md).
