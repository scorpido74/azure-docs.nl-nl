---
title: Door C# de gebruiker gedefinieerde functies schrijven voor Azure stream Analytics Edge-taken in Visual Studio (preview)
description: Informatie over het schrijven van door de gebruiker gedefinieerde C#-functies voor Stream Analytics Edge-taken in Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: cadc603a94d5d17ad2df419f8507c37f9e3272f8
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173315"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-edge-job-preview"></a>Zelfstudie: Een door de gebruiker gedefinieerde C#-functie schrijven voor een Azure Stream Analytics Edge-taak (preview)

Met door de gebruiker gedefinieerde C#-functies (UDF’s) die zijn gemaakt in Visual Studio kunt u de Azure Stream Analytics-querytaal uitbreiden met uw eigen functies. U kunt bestaande code (inclusief DLL-bestanden) opnieuw gebruiken en wiskundige of complexe logica gebruiken met C#. Er zijn drie manieren waarop UDF's kunnen worden geïmplementeerd: CodeBehind-bestanden in een Stream Analytics-project, UDF's in een lokaal C#-project of UDF's van een bestaand pakket van een opslagaccount. In deze zelfstudie wordt de CodeBehind-methode gebruikt om een eenvoudige C#-functie te implementeren. De UDF-functie voor Stream Analytics Edge-taken is momenteel in de preview-fase en moet niet worden gebruikt voor productieworkloads.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een door de gebruiker gedefinieerde C#-functie maken met CodeBehind.
> * Uw Stream Analytics Edge-taak lokaal testen.
> * Uw Edge-taak publiceren naar Azure.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet aan de volgende vereisten zijn voldaan:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* Installeer [Stream Analytics-hulpprogramma’s voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md) en de workload **Azure-ontwikkeling** of **Gegevensopslag en verwerking**.
* Bekijk de bestaande [handleiding voor het ontwikkelen van Stream Analytics Edge](stream-analytics-tools-for-visual-studio-edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Een container in uw Azure Storage-account maken

De container die u maakt, wordt gebruikt voor het opslaan van het gecompileerde C#-pakket en het implementeren van het pakket naar uw IoT Edge-apparaat. Gebruik een specifieke container voor elke Stream Analytics-taak. Hergebruik van dezelfde container voor meerdere Stream Analytics Edge-taken wordt niet ondersteund. Als u al een opslagaccount met bestaande containers hebt, kunt u die gebruiken. Zo niet, [maak dan een nieuwe container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-edge-project-in-visual-studio"></a>Een Stream Analytics-project in Visual Studio maken

1. Start Visual Studio.

2. Selecteer **Bestand > Nieuw > Project**.

3. Selecteer **Stream Analytics**in de sjablonenlijst aan de linkerkant en selecteer vervolgens **Azure Stream Analytics Edge Application**.

4.  Voer de **Naam**, **Locatie**en **Oplossingsnaam** van het project in en selecteer **OK**.

    ![Een Azure Stream Analytics Edge-project in Visual Studio maken](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Pad van assembly-pakket configureren

1. Open Visual Studio en ga naar de **Solution Explorer**.

2. Dubbelklik op het taakconfiguratiebestand `EdgeJobConfig.json`.

3. Vouw de sectie **Configuratie van de door de gebruiker gedefinieerde code** uit en vul de configuratie in met de volgende voorgestelde waarden:

    |**Instelling**  |**Voorgestelde waarde**  |
    |---------|---------|
    |Assembly-bron  |  Lokale projectverwijzing of CodeBehind   |
    |Resource  |  Kies gegevens van het huidige account   |
    |Abonnement  |  Kies uw abonnement.   |
    |Opslagaccount  |  Kies uw opslagaccount.   |
    |Container  |  Kies de container die u hebt gemaakt in uw opslagaccount.   |

    ![Azure Stream Analytics Edge-taakconfiguratie in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-edge-job-config.png)


## <a name="write-a-c-udf-with-codebehind"></a>Een C#-UDF schrijven met CodeBehind
Een CodeBehind-bestand is een C#-bestand dat is gekoppeld aan een enkel ASA Edge-queryscript. De Visual Studio-hulpprogramma's zippen het CodeBehind-bestand automatisch en uploaden het naar uw Azure-opslagaccount wanneer u het verzendt. Alle klassen moeten zijn gedefinieerd als *openbaar* en alle objecten moeten zijn gedefinieerd als *statisch openbaar*.

1. Vouw in **Solution Explorer** **Script.asql** uit om het CodeBehind-bestand **Script.asaql.cs** te zoeken.

2. Vervang de code door het volgende voorbeeld:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>De UDF implementeren

1. Open in **Solution Explorer** het bestand **Script.asaql**.

2. Vervang de bestaande query door de volgende:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Lokaal testen

1. Downloaden het Edge-[bestand met voorbeeldgegevens voor de temperatuursimulator](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. Vouw in **Solution Explorer** **Invoer** uit, klik met de rechtermuisknop op **Input.json** en selecteer **Lokale invoer toevoegen**.

   ![Lokale invoer toevoegen aan een Stream Analytics-taak in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Geef het pad van het lokale invoerbestand op voor de voorbeeldgegevens die u hebt gedownload en selecteer **Opslaan**.

    ![Lokale invoer configureren voor een Stream Analytics-taak in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Klik op **Lokaal uitvoeren** in de scripteditor. Nadat de resultaten van het uitvoeren zijn opgeslagen, drukt u op een willekeurige toets om de resultaten weer te geven in tabelindeling. 

    ![Azure Stream Analytics-taak lokaal uitvoeren met Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. U kunt ook **Map met resultaten openen** om de onbewerkte bestanden in JSON- en CSV-indeling weer te geven.

    ![Resultaten van lokale Azure Stream Analytics-taak weergeven met Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Fouten opsporen in een UDF
U kunt lokaal fouten opsporen in uw C# UDF, op dezelfde manier als dat u fouten opspoort in standaard C#-code. 

1. Onderbrekingspunten in uw C#-functie toevoegen.

    ![Onderbrekingspunten toevoegen aan een door de gebruiker gedefinieerde Stream Analytics-functie in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Druk op **F5** om de foutopsporing te starten. Het programma stopt bij de onderbrekingspunten, zoals verwacht.

    ![De foutopsporingsresultaten van de door de gebruiker gedefinieerde Stream Analytics-functie weergeven](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Uw taak publiceren naar Azure
Nadat u de query lokaal hebt getest, selecteert u **Verzenden naar Azure** in de scripteditor om de taak naar Azure te publiceren.

![Uw Stream Analytics Edge-taak verzenden naar Azure vanuit Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Implementeren naar IoT Edge-apparaten
Uw Stream Analytics-taak is nu klaar om te worden geïmplementeerd als een IoT Edge-module. Volg de [IoT Edge-snelstart](https://docs.microsoft.com/azure/iot-edge/quickstart) om een IoT Hub te maken, een IoT Edge-apparaat te registeren en de IoT Edge-runtime op uw apparaat te installeren en starten. Volg vervolgens de zelfstudie [de taak implementeren](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) om uw Stream Analytics-taak als een IoT Edge-module te implementeren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een eenvoudige door de gebruiker gedefinieerde C#-functie gemaakt met CodeBehind, uw taak gepubliceerd naar Azure en de taak geïmplementeerd op IoT Edge-apparaten met IoT Hub-portal. 

Als u meer wilt weten over de verschillende manieren waarop u door de gebruiker gedefinieerde C#-functies voor Stream Analytics Edge-taken kunt gebruiken, gaat u verder met het volgende artikel:

> [!div class="nextstepaction"]
> [C#-functies schrijven in Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
