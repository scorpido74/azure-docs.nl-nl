---
title: Gegevens opnemen van IoT Hub in azure Data Explorer
description: In dit artikel leert u hoe u gegevens kunt opnemen in azure Data Explorer van IoT Hub.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: cbe9aa2ea664d97df6008de05d6cb84da9771bcc
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166548"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>Gegevens opnemen van IoT Hub in azure Data Explorer (preview-versie)

Azure Data Explorer is een snelle en zeer schaalbare service om gegevens in logboeken en telemetrie te verkennen. Azure Data Explorer biedt opname (gegevens laden) van IoT Hub, een big data streaming platform en IoT-opname service.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

* Maak [een test cluster en data base met de](create-cluster-database-portal.md) database naam *testdb*.

* [Een voor beeld-app](https://github.com/Azure-Samples/azure-iot-samples-csharp) en documentatie voor het simuleren van een apparaat.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) voor het uitvoeren van de voor beeld-app.

## <a name="create-an-iot-hub"></a>Een IOT-hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Een apparaat registreren bij de IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Een doeltabel maken in Azure Data Explorer

Nu maakt u een tabel in azure Data Explorer waarmee IoT-hubs gegevens kunnen verzenden. U maakt de tabel in het cluster en de data base die is ingericht in [**vereisten**](#prerequisites).

1. Ga in de Azure-portal naar het cluster en selecteer **Query**.

    ![ADX-query in Portal](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Kopieer de volgende opdracht in het venster en selecteer **Uitvoeren** om de tabel (TestTable) te maken waarin de opgenomen gegevens worden geplaatst.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Maken van query uitvoeren](media/ingest-data-iot-hub/run-create-query.png)

1. Kopieer de volgende opdracht in het venster en selecteer **Uitvoeren** om de binnenkomende JSON-gegevens toe te wijzen aan de kolomnamen en gegevenstypen van de tabel (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Azure Data Explorer-tabel verbinden met IoT hub

U kunt nu verbinding maken met de IoT Hub vanuit Azure Data Explorer. Wanneer deze verbinding is voltooid, worden gegevens die in de IOT hub stromen stromen naar de [doel tabel die u hebt gemaakt](#create-a-target-table-in-azure-data-explorer).

1. Selecteer **meldingen** op de werk balk om te controleren of de IOT hub-implementatie is geslaagd.

1. Selecteer **Data** bases onder het cluster dat u hebt gemaakt en selecteer vervolgens de data base die u hebt gemaakt **testdb**.
    
    ![Testdatabase selecteren](media/ingest-data-iot-hub/select-database.png)

1. Selecteer **Gegevensopname** en **Gegevensverbinding toevoegen**. Vul het formulier in met de volgende gegevens. Selecteer **maken** wanneer u klaar bent.

    ![IoT Hub verbinding](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Gegevens bron**:

    **Instelling** | **Beschrijving van veld**
    |---|---|
    | Naam van gegevensverbinding | De naam van de verbinding die u wilt maken in azure Data Explorer
    | IoT Hub | Naam van IoT-hub |
    | Beleid voor gedeelde toegang | De naam van het beleid voor gedeelde toegang. Moet Lees machtigingen hebben |
    | Consumentengroep |  De consumenten groep die is gedefinieerd in het IoT Hub ingebouwde eind punt |
    | Eigenschappen van gebeurtenis systeem | De IoT Hub gebeurtenis systeem eigenschappen |
    | | 

    > [!NOTE]
    > In het geval van een [hand matige failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover)moet u de gegevens verbinding opnieuw maken.

    **Doel tabel**:

    Er zijn twee opties voor het routeren van de opgenomen gegevens: *statisch* en *dynamisch*. 
    Voor dit artikel gebruikt u statische route ring, waar u de tabel naam, gegevens indeling en toewijzing opgeeft. Laat **Mijn gegevens omvatten routeringsgegevens** daarom uitgeschakeld.

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Tabel | *TestTable* | De tabel die u hebt gemaakt in **testdb**. |
    | Gegevensindeling | *JSON* | Ondersteunde indelingen zijn Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV en TXT. |
    | Toewijzen van kolommen | *TestMapping* | De toewijzing die u hebt gemaakt in **testdb**, waarmee inkomende JSON-gegevens worden toegewezen aan de kolom namen en gegevens typen van **testdb**. Vereist voor JSON, meerdere regels JSON en AVRO, en optioneel voor andere indelingen.|
    | | |

    > [!TIP]
    > Selecteer **mijn gegevens bevat routerings informatie** voor het gebruik van dynamische route ring, waarbij uw gegevens de benodigde routerings informatie bevatten, zoals wordt weer gegeven in de opmerkingen van de voor [beeld-app](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Als zowel statische als dynamische eigenschappen zijn ingesteld, worden de dynamische eigenschappen overschreven. 

## <a name="generate-sample-data-for-testing"></a>Voorbeeld gegevens voor testen genereren

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verstuurt gesimuleerde telemetrie over temperatuur en luchtvochtigheid.

1. Download het C#-voorbeeldproject van https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip en pak het ZIP-archief uit.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in C#. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device**.

1. Open het bestand **SimulatedDevice.cs** in een teksteditor van uw keuze.

    Vervang de waarde van de `s_connectionString` variabele door het apparaat Connection String [een apparaat te registreren bij de IOT hub](#register-a-device-to-the-iot-hub). Sla ten slotte de wijzigingen in **SimulatedDevice.cs** op.

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste pakketten te installeren voor de toepassing voor het gesimuleerde apparaat:

    ```cmd/sh
    dotnet restore
    ```

1. Voer in het lokale terminalvenster de volgende opdracht uit om de toepassing voor het gesimuleerde apparaat te compileren en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Het gesimuleerde apparaat uitvoeren](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>De gegevensstroom controleren

Met de app voor het genereren van gegevens kunt u nu de gegevens stroom van de IoT-hub bekijken in de tabel in uw cluster.

1. In de Azure Portal, onder uw IoT-hub, ziet u de Prikker in activiteit terwijl de app wordt uitgevoerd.

    ![IoT Hub metrische gegevens](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Als u wilt controleren hoeveel berichten er op dat moment de database hebben bereikt, voert u de volgende query uit in de testdatabase.

    ```Kusto
    TestTable
    | count
    ```

1. Voer de volgende query uit om de inhoud van de berichten te bekijken:

    ```Kusto
    TestTable
    ```

    De resultatenset:
    
    ![Opgenomen gegevens resultaten weer geven](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer heeft een aggregatiebeleid (batchverwerking) voor gegevensopname, dat is ontworpen om de gegevensopname te optimaliseren. Het beleid is standaard ingesteld op 5 minuten of 500 MB aan gegevens, zodat er een latentie kan optreden. Zie [batch beleid](/azure/kusto/concepts/batchingpolicy) voor aggregatie opties. 
    > * Configureer uw tabel ter ondersteuning van streaming en verwijder de vertraging in reactie tijd. Zie [streaming-beleid](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om uw IoT Hub opnieuw te gebruiken, schoont u **test-hub-RG**op om te voor komen dat er kosten in rekening worden gebracht.

1. Selecteer in Azure Portal **Resourcegroepen** aan de linkerkant en selecteer vervolgens de resourcegroep die u hebt gemaakt.  

    Als het menu links is samengevouwen, selecteert u ![Knop Uitvouwen](media/ingest-data-event-hub/expand.png) om het menu uit te vouwen.

   ![Resourcegroep selecteren die moet worden verwijderd](media/ingest-data-event-hub/delete-resources-select.png)

1. Selecteer onder **test-resource-group** de optie **Resourcegroep verwijderen**.

1. Typ in het nieuwe venster de naam van de resourcegroep die u wilt verwijderen (*test-hub-rg*), en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren op gegevens in azure Data Explorer](web-query-data.md)
