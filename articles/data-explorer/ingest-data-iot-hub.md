---
title: Gegevens van IoT Hub innemen in Azure Data Explorer
description: In dit artikel leert u hoe u gegevens inneemt (laden) in Azure Data Explorer van IoT Hub.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 78455c90bab694b77a5e4a56d0b40518867d8d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188365"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Gegevens van IoT Hub innemen in Azure Data Explorer 

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-sjabloon](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Data Explorer biedt inname (dataladen) van IoT Hub, een streamingplatform voor big data en IoT-opnameservice.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Maak [een testcluster en -database](create-cluster-database-portal.md) met databasenaamtestdb . *testdb*
* [Een voorbeeld-app](https://github.com/Azure-Samples/azure-iot-samples-csharp) en documentatie voor het simuleren van een apparaat.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) om de voorbeeld-app uit te voeren.

## <a name="create-an-iot-hub"></a>Een Iot-hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Een apparaat registreren bij de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Een doeltabel maken in Azure Data Explorer

Nu maakt u een tabel in Azure Data Explorer waarnaar IoT Hubs gegevens verzenden. U maakt de tabel in het cluster en de database die is ingericht in [**Voorwaarden.**](#prerequisites)

1. Ga in de Azure-portal naar het cluster en selecteer **Query**.

    ![ADX-query in portal](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Kopieer de volgende opdracht in het venster en selecteer **Uitvoeren** om de tabel (TestTable) te maken waarin de opgenomen gegevens worden geplaatst.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Maken van query uitvoeren](media/ingest-data-iot-hub/run-create-query.png)

1. Kopieer de volgende opdracht in het venster en selecteer **Uitvoeren** om de binnenkomende JSON-gegevens toe te wijzen aan de kolomnamen en gegevenstypen van de tabel (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Tabel Azure Data Explorer verbinden met IoT-hub

Nu maakt u verbinding met de IoT-hub vanuit Azure Data Explorer. Wanneer deze verbinding is voltooid, worden gegevens die naar de iot-hub stromen, naar de [doeltabel die u hebt gemaakt.](#create-a-target-table-in-azure-data-explorer)

1. Selecteer **Meldingen** op de werkbalk om te controleren of de implementatie van de IoT-hub is gelukt.

1. Selecteer onder het door u gemaakte cluster de optie **Databases** en selecteer vervolgens de database die u **testdb hebt**gemaakt.
    
    ![Testdatabase selecteren](media/ingest-data-iot-hub/select-database.png)

1. Selecteer **Gegevensopname** en **Gegevensverbinding toevoegen**. Vul het formulier in met de volgende gegevens. Selecteer **Maken** wanneer u klaar bent.

    ![IoT-hubverbinding](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Gegevensbron**:

    **Instelling** | **Veldbeschrijving**
    |---|---|
    | Naam van gegevensverbinding | De naam van de verbinding die u wilt maken in Azure Data Explorer
    | IoT Hub | Naam IoT-hub |
    | Beleid voor gedeelde toegang | De naam van het beleid voor gedeelde toegang. Moet leesmachtigingen hebben |
    | Consumentengroep |  De consumentengroep gedefinieerd in het ingebouwde eindpunt van iot-hub |
    | Eigenschappen van gebeurtenissysteem | De [eigenschappen van het IoT Hub-gebeurtenissysteem](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Wanneer u systeemeigenschappen toevoegt, maakt of [updatet u](/azure/kusto/management/alter-table-command) tabelschema's en toewijzing en [voegt u deze samen](/azure/kusto/management/mappings) om de geselecteerde eigenschappen op te nemen. [create](/azure/kusto/management/create-table-command) | | | 

    > [!NOTE]
    > In het geval van een [handmatige failover](/azure/iot-hub/iot-hub-ha-dr#manual-failover)moet u de gegevensverbinding opnieuw maken.

    **Doeltabel**:

    Er zijn twee opties voor het routeren van de ingenomen gegevens: *statisch* en *dynamisch.* 
    Voor dit artikel gebruikt u statische routering, waarbij u de tabelnaam, gegevensindeling en toewijzing opgeeft. Laat **Mijn gegevens omvatten routeringsgegevens** daarom uitgeschakeld.

     **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | Tabel | *TestTable* | De tabel die u hebt gemaakt in **testdb**. |
    | Gegevensindeling | *Json* | Ondersteunde formaten zijn Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE en TXT. |
    | Toewijzen van kolommen | *TestMapping* | De [toewijzing die](/azure/kusto/management/mappings) u hebt gemaakt in **testdb**, die inkomende JSON-gegevens inkaart aan de kolomnamen en gegevenstypen **testdb**. Vereist voor JSON, MULTILINE JSON en AVRO, en optioneel voor andere formaten.|
    | | |

    > [!NOTE]
    > * Selecteer **Mijn gegevens bevat routeringsgegevens** om dynamische routering te gebruiken, waarbij uw gegevens de benodigde routeringsinformatie bevatten zoals te zien is in de opmerkingen van de [voorbeeld-app.](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) Als zowel statische als dynamische eigenschappen zijn ingesteld, overschrijven de dynamische eigenschappen de statische eigenschappen. 
    > * Alleen gebeurtenissen die in de wachtrij staan nadat u de gegevensverbinding hebt gemaakt, worden ingenomen.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Voorbeeldgegevens genereren voor testen

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verstuurt gesimuleerde telemetrie over temperatuur en luchtvochtigheid.

1. Download het C#-voorbeeldproject van https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip en pak het ZIP-archief uit.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in C#. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device**.

1. Open het bestand **SimulatedDevice.cs** in een teksteditor van uw keuze.

    Vervang de waarde `s_connectionString` van de variabele door de tekenreeks van de apparaatverbinding van [Een apparaat registreren naar de IoT-hub](#register-a-device-to-the-iot-hub). Sla ten slotte de wijzigingen in **SimulatedDevice.cs** op.

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

Met het genereren van gegevens van de app u nu de gegevensstroom van de IoT-hub naar de tabel in uw cluster zien.

1. In de Azure-portal, onder uw IoT-hub, ziet u de piek in activiteit terwijl de app wordt uitgevoerd.

    ![IoT Hub-statistieken](media/ingest-data-iot-hub/iot-hub-metrics.png)

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
    
    ![Ingenomen gegevensresultaten weergeven](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer heeft een aggregatiebeleid (batchverwerking) voor gegevensopname, dat is ontworpen om de gegevensopname te optimaliseren. Het beleid is standaard geconfigureerd tot 5 minuten of 500 MB aan gegevens, zodat u een latentie ervaren. Zie [batchingbeleid](/azure/kusto/concepts/batchingpolicy) voor aggregatieopties. 
    > * Configureer uw tabel om streaming te ondersteunen en verwijder de vertraging in reactietijd. Zie [streamingbeleid](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent uw IoT Hub opnieuw te gebruiken, ruimt u **test-hub-rg op**om te voorkomen dat u kosten maakt.

1. Selecteer in Azure Portal **Resourcegroepen** aan de linkerkant en selecteer vervolgens de resourcegroep die u hebt gemaakt.  

    Wanneer het menu links is samengevouwen, klikt u op ![Knop Uitvouwen](media/ingest-data-event-hub/expand.png) om het menu uit te vouwen.

   ![Resourcegroep selecteren die moet worden verwijderd](media/ingest-data-event-hub/delete-resources-select.png)

1. Selecteer onder **test-resource-group** de optie **Resourcegroep verwijderen**.

1. Typ in het nieuwe venster de naam van de resourcegroep die u wilt verwijderen (*test-hub-rg*), en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [Query's uitvoeren op gegevens in Azure Data Explorer](web-query-data.md)
