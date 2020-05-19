---
title: Azure Stream Analytics Edge-taken gebruiken met Azure SQL Edge (preview)
description: Meer informatie over het gebruik van Stream Analytics-taken in Azure SQL Edge (preview)
keywords: SQL-rand, stream Analytics,
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b57cb8cae80381a6c2cd88358dd9284ba56c919
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597315"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Azure Stream Analytics-taken gebruiken met SQL Edge

Azure SQL Edge (preview) is een geoptimaliseerde relationele data base-engine die is afgestemd op IoT-en Edge-implementaties. Het is gebaseerd op de nieuwste versies van de Microsoft SQL Server data base-engine, waarmee toonaangevende prestaties, beveiliging en verwerkings mogelijkheden voor query's worden geboden. Naast de toonaangevende mogelijkheden voor het beheer van relationele data bases van SQL Server biedt Azure SQL Edge ingebouwde streaming-mogelijkheden voor realtime analyse en complexe gebeurtenis verwerking.

Azure SQL Edge heeft een systeem eigen implementatie van de stream Analytics-runtime. Met deze implementatie kunt u een Azure Stream Analytics Edge-taak maken en die taak implementeren als een taak voor het streamen van SQL-edges. Azure Stream Analytics-taken kunnen worden geïmplementeerd naar SQL Edge met behulp van de para meter ASAJobInfo die wordt weer gegeven via de `module twin's desired properties` optie van de SQL Edge-module:

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
| ASAJobInfo | Azure Blob-opslag-URI voor de ASA Edge-taak.

## <a name="create-an-azure-stream-analytics-edge-job"></a>Een Azure Stream Analytics Edge-taak maken

1. Ga naar Azure Portal.

2. Een nieuwe **Azure stream Analytics op IOT Edge** -taak maken. Kies de hosting omgeving die de **rand**van het doel heeft.

3. Definieer een invoer en uitvoer voor de Azure Stream Analytics taak. Elke SQL-uitvoer, die u hier instelt, is gekoppeld aan één tabel in de data base. Als u gegevens naar meerdere tabellen wilt streamen, moet u meerdere SQL Database-uitvoer maken. U kunt de SQL-uitvoer zodanig configureren dat deze naar verschillende data bases verwijst.

    **Invoer**. Kies EdgeHub als invoer voor de Edge-taak en geef de resource gegevens op.

    **Uitvoer**. Selecteer SQL Database de als uitvoer. Selecteer **SQL database-instellingen hand matig opgeven**. Geef de configuratie gegevens op voor de data base en tabel.

    |Veld      | Beschrijving |
    |---------------|-------------|
    |Uitvoeralias | De naam van de uitvoer alias.|
    |Database | De naam van de SQL database. Dit moet een geldige naam zijn van een Data Base die zich op het SQL Edge-exemplaar bevindt.|
    |Servernaam | Naam (of IP-adres) en Details van het poort nummer voor het SQL-exemplaar. Voor een implementatie van een SQL-Edge kunt u **TCP:., 1433** gebruiken voor de server naam.|
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

6. Selecteer onder **configureren**de optie **publiceren**en selecteer vervolgens de knop **publiceren** . Sla de SAS-URI op voor gebruik met de SQL Edge-module.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Azure Stream Analytics Edge-taak implementeren naar SQL-rand

Als u de streaming-taak wilt implementeren in de SQL Edge-module, moet u de configuratie van de SQL-Edge-module bijwerken zodat de SAS-URI voor de streaming-taak uit de vorige stap wordt vermeld. De SQL Edge-module bijwerken:

1. Ga in het Azure Portal naar de implementatie van IoT Hub.

2. Selecteer **IoT Edge** in het linkerdeelvenster.

3. Zoek en selecteer op de pagina **IOT Edge** de IOT Edge waar de SQL Edge-module is geïmplementeerd.

4. Selecteer op de pagina **apparaat IOT edge apparaten** de optie **module instellen**.

5. Selecteer op de pagina **modules instellen** de optie **configureren** voor de SQL-rand module.

6. Selecteer in het deel venster **aangepaste Modules IOT Edge** de **gewenste eigenschappen van module configureren**. Werk de gewenste eigenschappen bij om de URI voor de `ASAJobInfo` optie op te geven, zoals wordt weer gegeven in het volgende voor beeld.

    > [!NOTE]
    > De SAS-URI in de volgende JSON is slechts een voor beeld. Vervang de URI door de daad werkelijke URI van uw implementatie.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Selecteer **Opslaan**.

8. Selecteer op de pagina **modules instellen** de optie **volgende**.

9. Selecteer op de pagina **modules instellen** de optie **volgende** en vervolgens **verzenden**.

10. Na het bijwerken van de module wordt het Stream Analytics-taak bestand gedownload, uitgepakt en geïmplementeerd op basis van het SQL Edge-exemplaar.

## <a name="next-steps"></a>Volgende stappen

- [Implementeer SQL Edge via Azure Portal](deploy-portal.md).

- [Gegevens streamen](stream-data.md)

- [Machine learning en AI met ONNX in SQL Edge (preview-versie)](onnx-overview.md)
