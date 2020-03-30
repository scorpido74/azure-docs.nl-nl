---
title: Avro-gegevens opvragen met Azure Data Lake Analytics | Microsoft Documenten
description: Gebruik de eigenschappen van de berichttekst om telemetrie van het apparaat te routeren naar blob-opslag en de Avro-indelingsgegevens op te vragen die naar Blob-opslag zijn geschreven.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 92fc5bb88ff5efd8fe1a8cd61be833b3984b673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605624"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Avro-gegevens opvragen met Azure Data Lake Analytics

In dit artikel wordt besproken hoe u Avro-gegevens opvragen om berichten efficiënt van Azure IoT Hub naar Azure-services te routeren. [Met Berichtroutering](iot-hub-devguide-messages-d2c.md) u gegevens filteren met uitgebreide query's op basis van berichteigenschappen, berichttekst, dubbele tags op het apparaat en dubbele eigenschappen van het apparaat. Zie het artikel over de syntaxis van de [routeringsquery](iot-hub-devguide-routing-query-syntax.md)voor berichten voor meer informatie over de querymogelijkheden voor berichten .

De uitdaging is dat wanneer Azure IoT Hub berichten naar Azure Blob-opslag leidt, standaard IoT Hub de inhoud in Avro-indeling schrijft, die zowel een eigenschap van de berichtbody als een berichteigenschap heeft. Het Avro-formaat wordt niet gebruikt voor andere eindpunten. Hoewel het Avro-formaat ideaal is voor het bewaren van gegevens en berichten, is het een uitdaging om het te gebruiken om gegevens op te vragen. In vergelijking, JSON of CSV formaat is veel gemakkelijker voor het opvragen van gegevens. IoT Hub ondersteunt nu het schrijven van gegevens naar Blob-opslag in JSON en AVRO.

Zie [Azure Storage gebruiken als eindpunt voor routering voor](iot-hub-devguide-messages-d2c.md#azure-storage)meer informatie.

Om tegemoet te komen aan niet-relationele big-data behoeften en formaten en deze uitdaging te overwinnen, u veel van de big-data patronen gebruiken voor zowel het transformeren en schalen van gegevens. Een van de patronen, "pay per query", is Azure Data Lake Analytics, dat is de focus van dit artikel. Hoewel u de query eenvoudig uitvoeren in Hadoop of andere oplossingen, is Data Lake Analytics vaak beter geschikt voor deze pay per query-benadering.

Er is een "extractor" voor Avro in U-SQL. Zie [u-SQL Avro-voorbeeld voor](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)meer informatie .

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro-gegevens opvragen en exporteren naar een CSV-bestand

In deze sectie bevraagt u Avro-gegevens en exporteert u deze naar een CSV-bestand in Azure Blob-opslag, hoewel u de gegevens eenvoudig in andere opslagplaatsen of gegevensopslag plaatsen.

1. Stel Azure IoT Hub in om gegevens naar een eindpunt van Azure Blob-opslag te routeren met een eigenschap in de berichttekst om berichten te selecteren.

   ![De sectie 'Aangepaste eindpunten'](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![De routeringsregels](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Zie [Berichtroutering voor een IoT-hub voor](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub)meer informatie over het instellen van routes en aangepaste eindpunten.

2. Zorg ervoor dat uw apparaat de coderings-, inhoudstypen en benodigde gegevens heeft in de eigenschappen of de berichttekst, zoals in de productdocumentatie wordt verwezen. Wanneer u deze kenmerken bekijkt in Device Explorer, zoals hier wordt weergegeven, u controleren of ze correct zijn ingesteld.

   ![Het deelvenster Gegevens van gebeurtenishub](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Stel een Azure Data Lake Store-exemplaar en een Instantie Data Lake Analytics in. Azure IoT Hub gaat niet naar een Instantie Data Lake Store, maar een Instantie Data Lake Analytics vereist er een.

   ![Data Lake Store en Data Lake Analytics-exemplaren](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. Configureer in Data Lake Analytics Azure Blob-opslag als een extra winkel, dezelfde Blob-opslag waar naar Azure IoT Hub gegevens worden gesmeerd.

   ![Het deelvenster Gegevensbronnen](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Zoals besproken in het [U-SQL Avro-voorbeeld,](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)hebt u vier DLL-bestanden nodig. Upload deze bestanden naar een locatie in uw Instantie Data Lake Store.

   ![Vier geüploade DLL-bestanden](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. Maak in Visual Studio een U-SQL-project.

   ! Een U-SQL-project maken](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Plak de inhoud van het volgende script in het nieuw gemaakte bestand. Wijzig de drie gemarkeerde secties: uw Data Lake Analytics-account, de bijbehorende DLL-bestandspaden en het juiste pad voor uw opslagaccount.

   ![De drie te wijzigen secties](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Het werkelijke U-SQL-script voor eenvoudige uitvoer naar een CSV-bestand:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Het kostte Data Lake Analytics vijf minuten om het volgende script uit te voeren, dat beperkt was tot 10 analytische eenheden en 177 bestanden verwerkte. Het resultaat wordt weergegeven in de csv-bestandsuitvoer die wordt weergegeven in de volgende afbeelding:

    ![Resultaten van de uitvoer naar CSV-bestand](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Uitvoer geconverteerd naar CSV-bestand](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Om de JSON te ontleden, ga je verder met stap 8.

8. De meeste IoT-berichten zijn in JSON-bestandsindeling. Door de volgende regels toe te voegen, u het bericht in een JSON-bestand ontleden, waarmee u de WHERE-clausules toevoegen en alleen de benodigde gegevens uitvoeren.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    In de uitvoer wordt een `SELECT` kolom weergegeven voor elk item in de opdracht.

    ![Uitvoer met een kolom voor elk item](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Avro-gegevens opvragen om berichten efficiënt van Azure IoT Hub naar Azure-services te routeren.

Zie de Documentatie Azure [IoT Solution Accelerators](/azure/iot-accelerators)voor voorbeelden van complete end-to-end-oplossingen die IoT Hub gebruiken.

Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IoT Hub.

Zie Berichten verzenden en ontvangen met [IoT Hub](iot-hub-devguide-messaging.md)voor meer informatie over berichtroutering in IoT Hub.
