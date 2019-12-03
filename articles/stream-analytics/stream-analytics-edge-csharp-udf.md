---
title: 'Zelf studie: C# door gebruiker gedefinieerde functies schrijven voor Azure stream Analytics taken in Visual Studio (preview)'
description: Deze zelf studie laat zien hoe u door de gebruiker gedefinieerde c#-functies schrijft voor Stream Analytics taken in Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: d600c891f35a0719012707bdc2cb45101e14e090
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707347"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Zelf studie: een C# door de gebruiker gedefinieerde functie schrijven voor Azure stream Analytics-taak (preview-versie)

Met door de gebruiker gedefinieerde C#-functies (UDF’s) die zijn gemaakt in Visual Studio kunt u de Azure Stream Analytics-querytaal uitbreiden met uw eigen functies. U kunt bestaande code (inclusief DLL-bestanden) opnieuw gebruiken en wiskundige of complexe logica gebruiken met C#. Er zijn drie manieren om UDF’s te implementeren: CodeBehind-bestanden in een Stream Analytics-project, UDF's in een lokaal C#-project of UDF's van een bestaand pakket van een opslagaccount. In deze zelfstudie wordt de CodeBehind-methode gebruikt om een eenvoudige C#-functie te implementeren. De UDF-functie voor Stream Analytics-taken is momenteel beschikbaar als preview-versie en mag niet worden gebruikt in werk belastingen voor de productie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een door de gebruiker gedefinieerde C#-functie maken met CodeBehind.
> * Test uw Stream Analytics-taak lokaal.
> * Publiceer uw taak naar Azure.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet aan de volgende vereisten zijn voldaan:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* Installeer [Stream Analytics-hulpprogramma’s voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md) en de workload **Azure-ontwikkeling** of **Gegevensopslag en verwerking**.
* Bekijk de bestaande [Stream Analytics Edge Development Guide] als u een IoT Edge-taak bouwt (stream-Analytics-tools-for-Visual-Studio-Edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Een container in uw Azure Storage-account maken

De container die u maakt, wordt gebruikt om het gecompileerde C# pakket op te slaan. Als u een Edge-taak maakt, wordt dit opslag account ook gebruikt voor het implementeren van het pakket op uw IoT Edge-apparaat. Gebruik een specifieke container voor elke Stream Analytics-taak. Hergebruik van dezelfde container voor meerdere Stream Analytics Edge-taken wordt niet ondersteund. Als u al een opslagaccount met bestaande containers hebt, kunt u die gebruiken. Zo niet, [maak dan een nieuwe container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Een Stream Analytics-project maken in Visual Studio

1. Start Visual Studio.

2. Selecteer **Bestand > Nieuw > Project**.

3. Selecteer in de lijst sjablonen aan de linkerkant **Stream Analytics**en selecteer vervolgens **Azure stream Analytics Edge-toepassing** of **Azure stream Analytics toepassing**.

4.  Voer de **Naam**, **Locatie**en **Oplossingsnaam** van het project in en selecteer **OK**.

    ![Een Azure Stream Analytics Edge-project in Visual Studio maken](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Pad van assembly-pakket configureren

1. Open Visual Studio en ga naar de **Solution Explorer**.

2. Dubbelklik op het taakconfiguratiebestand `EdgeJobConfig.json`.

3. Vouw de sectie **Configuratie van de door de gebruiker gedefinieerde code** uit en vul de configuratie in met de volgende voorgestelde waarden:

   |**Instelling**|**Voorgestelde waarde**|
   |-------|---------------|
   |Resource voor algemene opslag instellingen|Kies gegevensbron van het huidige account|
   |Abonnement voor globale opslag instellingen| < uw abonnement >|
   |Opslag account voor algemene opslag instellingen| < uw opslag account >|
   |Resource voor aangepaste code opslag instellingen|Kies gegevensbron van het huidige account|
   |Opslag account voor aangepaste code opslag|< uw opslag account >|
   |Container voor aangepaste code opslag|< uw opslag container >|


## <a name="write-a-c-udf-with-codebehind"></a>Een C#-UDF schrijven met CodeBehind
Een CodeBehind-bestand is C# een bestand dat is gekoppeld aan één ASA-query script. De Visual Studio-hulpprogramma's zippen het CodeBehind-bestand automatisch en uploaden het naar uw Azure-opslagaccount wanneer u het verzendt. Alle klassen moeten zijn gedefinieerd als *openbaar* en alle objecten moeten zijn gedefinieerd als *statisch openbaar*.

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

1. Down load het bestand met de [voorbeeld gegevens van de temperatuur Simulator](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

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
Als u ervoor kiest om een Stream Analytics Edge-taak te maken, kan dit nu worden geïmplementeerd als een IoT Edge-module. Volg de [IoT Edge-snelstart](https://docs.microsoft.com/azure/iot-edge/quickstart) om een IoT Hub te maken, een IoT Edge-apparaat te registeren en de IoT Edge-runtime op uw apparaat te installeren en starten. Volg vervolgens de zelfstudie [de taak implementeren](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) om uw Stream Analytics-taak als een IoT Edge-module te implementeren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een eenvoudige C# door de gebruiker gedefinieerde functie gemaakt met behulp van CodeBehind, uw taak naar Azure gepubliceerd en de taak geïmplementeerd op azure of IOT edge apparaat. 

Ga naar dit artikel voor meer informatie over de C# verschillende manieren waarop u door de gebruiker gedefinieerde functies voor stream Analytics taken kunt gebruiken:

> [!div class="nextstepaction"]
> [C#-functies schrijven in Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
