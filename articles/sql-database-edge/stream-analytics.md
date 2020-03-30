---
title: SQL Database DAC-pakketten en Stream Analytics-taken gebruiken met Azure SQL Database Edge | Microsoft Documenten
description: Meer informatie over het gebruik van Stream Analytics-taken in SQL Database Edge
keywords: sql-databaseedge, stream analytics, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384162"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>SQL Database DAC-pakketten en Stream Analytics-taken gebruiken met SQL Database Edge

Azure SQL Database Edge Preview is een geoptimaliseerde relationele databaseengine die is afgestemd op IoT- en edge-implementaties. Het is gebouwd op de nieuwste versies van de Microsoft SQL Server Database Engine, die toonaangevende mogelijkheden voor prestaties, beveiliging en queryverwerking biedt. Naast de toonaangevende relationele databasebeheermogelijkheden van SQL Server biedt Azure SQL Database Edge ingebouwde streamingmogelijkheden voor realtime analyses en complexe gebeurtenisverwerking.

Azure SQL Database Edge biedt ook een native implementatie van SqlPackage.exe waarmee u een [SQL Database DAC-pakket](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) implementeren tijdens de implementatie van SQL Database Edge.

Azure SQL Database Edge legt twee `module twin's desired properties` optionele parameters bloot via de optie van de IoT Edge-module:

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
| SqlPackage | Uri azure blob-opslag voor het *.zip-bestand dat het SQL Database DAC-pakket bevat.
| ASAJobInfo | Azure Blob-opslagURI voor de ASA Edge-taak. Zie [Een ASA Edge-taak publiceren voor SQL Database Edge voor](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)meer informatie.

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database DAC-pakketten gebruiken met SQL Database Edge

Als u een SQL Database DAC-pakket (*.dacpac) met SQL Database Edge wilt gebruiken, neemt u de volgende stappen:

1. Een SQL Database DAC-pakket maken of extraheren. Zie [Een DAC uit een database extraheren](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) voor informatie over het genereren van een DAC-pakket voor een bestaande SQL Server-database.

2. Rits de *.dacpac dicht en upload deze naar een Azure Blob-opslagaccount. Zie [Blobs uploaden, downloaden en aanbieden met de Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md)voor meer informatie over het uploaden van bestanden naar Azure Blob-opslag.

3. Genereer een gedeelde toegangshandtekening voor het zip-bestand met behulp van de Azure-portal. Zie [Toegang tot gemachtigde met gedeelde toegangshandtekeningen (SAS) voor](../storage/common/storage-sas-overview.md)meer informatie.

4. Werk de SQL Database Edge-moduleconfiguratie bij om de gedeelde toegangs-URI voor het DAC-pakket op te nemen. Als u de SQL Database Edge-module wilt bijwerken, neemt u de volgende stappen uit:

    1. Ga in de Azure-portal naar uw IoT Hub-implementatie.

    2. Selecteer **IoT Edge** in het linkerdeelvenster.

    3. Zoek en selecteer op de pagina **IoT Edge** de IoT-rand waar de SQL Database Edge-module is geïmplementeerd.

    4. Selecteer op de apparaatpagina **van IoT Edge Device** de optie Module **instellen**.

    5. Selecteer op de pagina **Modules instellen** de optie **Configureren** ten opzichte van de SQL Database Edge-module.

    6. Selecteer in het deelvenster **Aangepaste IoT Edge-modules** de **gewenste eigenschappen van moduletwin instellen.** Werk de gewenste eigenschappen bij `SQLPackage` om de URI voor de optie op te nemen, zoals in het volgende voorbeeld wordt weergegeven.

        > [!NOTE]
        > De SAS URI in de volgende JSON is slechts een voorbeeld. Vervang de URI door de werkelijke URI van uw implementatie.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Selecteer **Opslaan**.

    8. Selecteer **Volgende**op de pagina **Modules instellen** .

    9. Selecteer op de pagina **Modules instellen** **de** optie Volgende en voer **vervolgens In.**

5. Na de module-update wordt het DAC-pakketbestand gedownload, uitgepakt en geïmplementeerd tegen de SQL Database Edge-instantie.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Streamingtaken gebruiken met SQL Database Edge

Azure SQL Database Edge heeft een native implementatie van de runtime van streamanalytics. Met deze implementatie u een Azure Stream Analytics-randtaak maken en die taak implementeren als een SQL Database Edge-streamingtaak. Voer de volgende stappen uit om een edge-taak van Stream Analytics te maken:

1. Ga naar de Azure-portal met behulp van de [preview-URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true). Met deze preview-URL u SQL Database-uitvoer configureren voor een edge-taak van Stream Analytics.

2. Maak een nieuwe **Azure Stream Analytics voor iot edge-taak.** Kies de hostingomgeving die is gericht op **Edge.**

3. Definieer een invoer en uitvoer voor de Azure Stream Analytics-taak. Elke SQL-uitvoer, die u hier instelt, is gekoppeld aan één tabel in de database. Als u gegevens naar meerdere tabellen moet streamen, moet u meerdere SQL-database-uitvoermaken. U de SQL-uitvoer configureren om naar verschillende databases te wijzen.

    **Invoer**. Kies EdgeHub als invoer voor de randtaak en geef de brongegevens op.

    **Output**. Selecteer SQL Database als uitvoer. Selecteer **SQL-database-instellingen handmatig opgeven**. Geef de configuratiegegevens voor de database en tabel op.

    |Veld      | Beschrijving |
    |---------------|-------------|
    |Uitvoeralias | Naam van de uitvoeralias.|
    |Database | Naam van de SQL-database. Het moet een geldige naam zijn van een database die bestaat op de SQL Database Edge-instantie.|
    |Servernaam | Naam (of IP-adres) en poortnummergegevens voor de SQL-instantie. Voor een SQL Database Edge-implementatie u **tcp:.,1433** gebruiken voor de servernaam.|
    |Gebruikersnaam | SQL-aanmeldingsaccount met toegang tot gegevenslezer en gegevensschrijver tot de database die u eerder hebt opgegeven.|
    |Wachtwoord | Wachtwoord voor het SQL-aanmeldingsaccount dat u eerder hebt opgegeven.|
    |Tabel | Naam van de tabel die wordt uitgevoerd voor de streamingtaak.|
    |Partitionering overnemen| Hiermee u het partitieschema van uw vorige querystap of -invoer overnemen. Wanneer deze optie is ingeschakeld, u een betere doorvoer verwachten wanneer u naar een op schijven gebaseerde tabel schrijft en een volledig parallelle topologie voor uw taak hebt.|
    |Batchgrootte| Het maximum aantal records dat wordt verzonden bij elke bulkwisseltransactie.|

    Hier is een voorbeeldinvoer-/uitvoerconfiguratie:

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
    > Zie [Azure Stream Analytics-uitvoer naar Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md)voor meer informatie over de SQL-uitvoeradapter voor Azure Stream Analytics.

4. Definieer de ASA-taakquery voor de randtaak. Deze query moet de gedefinieerde invoer-/uitvoeraliassen gebruiken als de invoer- en uitvoernamen in de query. Zie [Naslaganalyse querytaalverwijzing](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)voor meer informatie.

5. Stel de instellingen voor het opslagaccount in voor de randtaak. Het opslagaccount wordt gebruikt als publicatiedoel voor de randtaak.

6. Selecteer **onder Configureren**de optie **Publiceren**en selecteer de knop **Publiceren.** Sla de SAS URI op voor gebruik met de SQL Database Edge-module.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>De edge-taak van Stream Analytics implementeren in SQL Database Edge

Als u de streamingtaak wilt implementeren in de SQL Database Edge-module, werkt u de SQL Database Edge-moduleconfiguratie bij om de SAS URI voor de streamingtaak van de eerdere stap op te nemen. Ga als een te werk om de SQL Database Edge-module bij te werken:

1. Ga in de Azure-portal naar uw IoT Hub-implementatie.

2. Selecteer **IoT Edge** in het linkerdeelvenster.

3. Zoek en selecteer op de pagina **IoT Edge** de IoT-rand waar de SQL Database Edge-module is geïmplementeerd.

4. Selecteer op de apparaatpagina **van IoT Edge Device** de optie Module **instellen**.

5. Selecteer op de pagina **Modules instellen** de optie **Configureren** ten opzichte van de SQL Database Edge-module.

6. Selecteer in het deelvenster **Aangepaste IoT Edge-modules** de **gewenste eigenschappen van moduletwin instellen.** Werk de gewenste eigenschappen bij `ASAJobInfo` om de URI voor de optie op te nemen, zoals in het volgende voorbeeld wordt weergegeven.

    > [!NOTE]
    > De SAS URI in de volgende JSON is slechts een voorbeeld. Vervang de URI door de werkelijke URI van uw implementatie.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Selecteer **Opslaan**.

8. Selecteer **Volgende**op de pagina **Modules instellen** .

9. Selecteer op de pagina **Modules instellen** **de** optie Volgende en voer **vervolgens In.**

10. Na de module-update wordt het taakbestand streamanalytics gedownload, uitgepakt en geïmplementeerd tegen de SQL Database Edge-instantie.

## <a name="next-steps"></a>Volgende stappen

- Zie Azure SQL Database [Edge](https://azure.microsoft.com/services/sql-database-edge/)voor prijs- en beschikbaarheidsgegevens.
- Azure SQL Database Edge inschakelen aanvragen voor uw abonnement.
- Zie [SQL Database Edge implementeren via Azure-portal](deploy-portal.md)om aan de slag te gaan.
