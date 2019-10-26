---
title: Een Azure Stream Analytics Cloud taak maken in Visual Studio code
description: In deze Quick start ziet u hoe u aan de slag gaat met het maken van een Stream Analytics taak, het configureren van invoer, uitvoer en het definiëren van een query met Visual Studio code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 35e2711bbb3cd6dc0662146a566014dd65d879bc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935090"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Quick Start: een Azure Stream Analytics Cloud taak maken in Visual Studio code (preview)

In deze Quick start ziet u hoe u een Stream Analytics-taak kunt maken en uitvoeren met behulp van de Azure Stream Analytics-extensie voor Visual Studio code. De voorbeeldtaak leest streaminggegevens vanaf een IoT Hub-apparaat. U definieert een taak die de gemiddelde temperatuur bij meer dan 27° berekent en de resulterende uitvoergebeurtenissen naar een nieuw bestand in blobopslag schrijft.

## <a name="before-you-begin"></a>Voordat u begint

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.

* Meld u aan bij de [Azure-portal](https://portal.azure.com/).

* Installeer [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-extension"></a>De Azure Stream Analytics-extensie installeren

1. Open Visual Studio Code.

2. Zoek **in het** linkerdeel venster naar **Stream Analytics** en selecteer **installeren** op de uitbrei ding **Azure stream Analytics** .

3. Nadat de uitbrei ding is geïnstalleerd, controleert u of **Azure stream Analytics-Hulpprogram ma's** zichtbaar zijn in de **ingeschakelde uitbrei dingen**.

   ![Azure Stream Analytics-Hulpprogram Ma's onder ingeschakelde uitbrei dingen in Visual Studio code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>De Azure Stream Analytics-extensie activeren

1. Selecteer het pictogram van **Azure** op de activiteiten balk VS-code. **Stream Analytics** is zichtbaar in de zijbalk. Onder **Stream Analytics**selecteert **u aanmelden bij Azure**. 

   ![Aanmelden bij Azure in Visual Studio code](./media/quick-create-vs-code/azure-sign-in.png)

2. Wanneer u bent aangemeld, wordt de naam van uw Azure-account weer gegeven op de status balk in de linkerbenedenhoek van het VS code-venster.

> [!NOTE]
> Azure Stream Analytics-hulpprogram ma's worden de volgende keer automatisch aangemeld als u zich niet afmeldt. Als uw account twee ledige verificatie heeft, is het raadzaam om verificatie via telefoon te gebruiken in plaats van een pincode te gebruiken.
> Als u problemen ondervindt met het aanbieden van resources, kunt u zich niet meer afmelden en opnieuw aanmelden. Voer de opdracht `Azure: Sign Out`in om u af te melden.

## <a name="prepare-the-input-data"></a>De invoergegevens voorbereiden

Voordat u de Stream Analytics-taak definieert, moet u de gegevens voorbereiden die later worden geconfigureerd als de taakinvoer. Voltooi de volgende stappen om de invoergegevens voor te bereiden die zijn vereist voor de taak:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** > **Internet of Things** > **IoT Hub**.

3. Voer in het deelvenster **IoT Hub** de volgende informatie in:
   
   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Abonnement  | \<Uw abonnement\> |  Selecteer het Azure-abonnement dat u wilt gebruiken. |
   |Resourcegroep   |   asaquickstart-resourcegroup  |   Selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep voor uw account in. |
   |Regio  |  \<Selecteer de regio die het dichtst bij uw gebruikers is gelegen\> | Selecteer een geografische locatie waar u de IoT-hub kunt hosten. Gebruik de locatie die het dichtst bij uw gebruikers is. |
   |Naam van de IoT-hub  | MyASAIoTHub  |   Selecteer een naam voor de IoT-hub.   |

   ![Een IoT Hub maken](./media/quick-create-vs-code/create-iot-hub.png)

4. Selecteer **Volgende: grootte instellen en schaal aanpassen**.

5. Kies uw **prijs- en schaalcategorie**. Selecteer voor deze quickstart de categorie **F1 - Gratis** als deze nog beschikbaar is voor uw abonnement. Als de categorie Gratis niet beschikbaar is, kiest u de laagste beschikbare categorie. Zie [Prijsinformatie IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) voor meer informatie.

   ![Grootte en schaal van de IoT-hub aanpassen](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Selecteer **Controleren + maken**. Controleer de informatie van de IoT-hub en klik op **Maken**. Het kan enkele minuten duren voordat de IoT-hub is gemaakt. U kunt de voortgang bewaken via het deelvenster **Meldingen**.

7. Klik in het IoT Hub-navigatiemenu onder **IoT-apparaten** op **Toevoegen**. Voeg een **Apparaat-id** toe en klik op **Opslaan**.

   ![Een apparaat toevoegen aan uw IoT-hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Zodra het apparaat is gemaakt, wordt het geopend vanuit de lijst **IoT-apparaten**. Kopieer de **Verbindingsreeks -- primaire sleutel** en sla deze in een kladblok op voor later gebruik.

   ![Verbindingsreeks voor IoT Hub-apparaat kopiëren](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Blob-opslag maken

1. Selecteer in de linkerbovenhoek in Azure Portal **Een resource maken** > **Storage** > **Storage-account**.

2. Voer in het deelvenster **Opslagaccount maken** een opslagaccountnaam, locatie en resourcegroep in. Kies dezelfde locatie en resourcegroep als de IoT-hub die u hebt gemaakt. Klik vervolgens op **Controleren en maken** om het account te maken.

   ![Een opslagaccount maken](./media/quick-create-vs-code/create-storage-account.png)

3. Zodra het opslagaccount is gemaakt, selecteert u in het deelvenster **Overzicht** de tegel **Blobs**.

   ![Overzicht van opslagaccounts](./media/quick-create-vs-code/blob-storage.png)

4. Selecteer op de pagina **Blob Service** de optie **Container** en geef een naam op voor de container, bijvoorbeeld *container1*. Laat **Niveau openbare toegang** staan op **Privé (geen anonieme toegang)** en selecteer **OK**.

   ![Blob-container maken](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Een Stream Analytics-project maken

1. Druk in Visual Studio code op **CTRL + SHIFT + P** om het opdracht palet te openen. Typ **ASA** en selecteer **ASA: nieuw project maken**.

   ![Nieuw project maken](./media/quick-create-vs-code/create-new-project.png)

2. Voer de naam van uw project in, zoals **myASAproj** en selecteer een map voor uw project.

    ![Project naam maken](./media/quick-create-vs-code/create-project-name.png)

3. Het nieuwe project wordt toegevoegd aan uw werk ruimte. Een ASA-project bestaat uit het query script **(*. asaql)** , een **JobConfig. json** -bestand en een **asaproj. json** -configuratie bestand.

   ![Stream Analytics project bestanden in VS code](./media/quick-create-vs-code/asa-project-files.png)

4. Het configuratie bestand **asaproj. json** bevat de invoer-, uitvoer-en taak configuratie bestand gegevens die nodig zijn voor het indienen van de stream Analytics-taak naar Azure.

   ![Stream Analytics taak configuratie bestand in VS code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Wanneer u invoer en uitvoer toevoegt vanuit het opdracht palet, worden de bijbehorende paden automatisch toegevoegd aan **asaproj. json** . Als u invoer of uitvoer rechtstreeks aan de schijf toevoegt of verwijdert, moet u deze hand matig toevoegen aan of verwijderen uit **asaproj. json**. U kunt ervoor kiezen om de invoer en uitvoer op één plek te plaatsen en hiernaar te verwijzen in verschillende taken door de paden op te geven in elke **asaproj. json**.

## <a name="define-the-transformation-query"></a>De transformatiequery definiëren

1. Open **myASAproj. asaql** uit de projectmap.

2. Voeg de volgende query toe:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```
## <a name="test-with-sample-data"></a>Testen met voorbeeldgegevens
Voordat u de query in de Cloud uitvoert, kunt u uw query testen met lokale voorbeeld gegevens om de query logica te controleren.

Volg de instructies in [test met voorbeeld gegevens](vscode-local-run.md) voor meer informatie. 

 ![Testen met voorbeeld gegevens in VS code](./media/quick-create-vs-code/vscode-localrun.gif)

## <a name="define-an-input"></a>Een invoer definiëren

1. Selecteer **CTRL + SHIFT + P** om het opdracht palet te openen en voer **asa in: invoer toevoegen**.

   ![Stream Analytics invoer in VS code toevoegen](./media/quick-create-vs-code/add-input.png)

2. Kies **IOT hub** voor het invoer type.

   ![IoT Hub selecteren als invoer optie](./media/quick-create-vs-code/iot-hub.png)

3. Kies het ASA-query script waarmee de invoer wordt gebruikt. Het bestand moet automatisch worden ingevuld met het bestandspad naar **myASAproj. asaql**.

   ![Een ASA-script in Visual Studio code selecteren](./media/quick-create-vs-code/asa-script.png)

4. Voer de naam van het invoer bestand in als **IotHub**.

5. Bewerk **IoTHub. json** met de volgende waarden. Behoud de standaard waarden voor velden die hieronder niet worden genoemd. U kunt de code lens gebruiken om een teken reeks in te voeren, een keuze lijst te selecteren of de tekst rechtstreeks in het bestand te wijzigen.

   |Instelling|Voorgestelde waarde|Beschrijving|
   |-------|---------------|-----------|
   |Naam|Invoer|Voer een unieke naam in voor de invoer van de taak.|
   |IotHubNamespace|MyASAIoTHub|Kies uw IoT-hub, of voer de naam ervan in. Namen van IoT-hubs worden automatisch gedetecteerd als ze worden gemaakt in hetzelfde abonnement.|
   |sharedAccessPolicyName|iothubowner| |

   ![Invoer in Visual Studio code configureren](./media/quick-create-vs-code/configure-input.png)



## <a name="define-an-output"></a>Een uitvoer definiëren

1. Selecteer **CTRL + SHIFT + P** om het opdracht palet te openen. Voer vervolgens **ASA: uitvoer toevoegen**in.

   ![Stream Analytics uitvoer in VS code toevoegen](./media/quick-create-vs-code/add-output.png)

2. Kies **Blob Storage** voor het sink-type.

3. Kies het ASA-query script dat deze invoer gebruikt.

4. Voer de naam van het uitvoer bestand in als **BlobStorage**.

5. Bewerk **BlobStorage** met de volgende waarden. Behoud de standaard waarden voor velden die hieronder niet worden genoemd. Gebruik de code lens om u te helpen bij het selecteren van een vervolg keuzelijst of om een teken reeks in te voeren. 

   |Instelling|Voorgestelde waarde|Beschrijving|
   |-------|---------------|-----------|
   |Naam|Uitvoer| Voer een naam in om de uitvoer van de taak te identificeren.|
   |Opslagaccount|asaquickstartstorage|Kies of typ de naam van uw opslag account. Namen van opslagaccounts worden automatisch gedetecteerd als ze worden gemaakt in hetzelfde abonnement.|
   |Container|container1|Selecteer de bestaande container die u in uw opslagaccount hebt gemaakt.|
   |Padpatroon|output|Voer de naam in van een bestandspad dat in de container moet worden gemaakt.|

 ![Uitvoer in Visual Studio code configureren](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Het script compileren

Script compilatie voert twee dingen uit: de syntaxis controleren en de Azure Resource Manager sjablonen voor de autodeployment genereren.

Er zijn twee manieren om script compilatie te activeren:

1. Selecteer het script in de werk ruimte en activeer vervolgens compileren vanuit het opdracht palet. 

   ![Het VS code-opdracht palet gebruiken om het script te compileren](./media/quick-create-vs-code/compile-script1.png)

2. Klik met de rechter muisknop op het script en selecteer **ASA: script compileren**.

    ![Klik met de rechter muisknop op het ASA-script dat u wilt compileren](./media/quick-create-vs-code/compile-script2.png)

3. Na de compilatie vindt u de twee gegenereerde Azure Resource Manager sjablonen in de map **Deploy** van uw project. Deze twee bestanden worden gebruikt voor de autodeployment.

    ![Stream Analytics implementatie sjablonen in Verkenner](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Een Stream Analytics taak naar Azure verzenden

1. Selecteer in het venster Script Editor van Visual Studio code **selecteren in uw abonnementen**.

   ![Selecteer een optie in de tekst van uw abonnementen in de Script Editor](./media/quick-create-vs-code/select-subscription.png)

2. Selecteer uw abonnement in de lijst met pop-ups.

3. Selecteer een taak * *. Kies vervolgens een nieuwe taak maken.

4. Voer de naam van de taak **myASAjob** in en volg de instructies om de resource groep en de locatie te kiezen.

5. Selecteer **verzenden naar Azure**. De logboeken vindt u in het uitvoer venster. 

6. Wanneer de taak is gemaakt, kunt u deze bekijken in de **Stream Analytics Explorer**.

![Taak weer geven in Stream Analytics Explorer](./media/quick-create-vs-code/list-job.png)


## <a name="run-the-iot-simulator"></a>De IoT-simulator uitvoeren

1. Open de [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) in een nieuw browsertabblad of -venster.

2. Vervang de tijdelijke aanduiding in regel 15 door de verbindingsreeks van het Azure IoT Hub-apparaat die u hebt opgeslagen in de vorige sectie.

3. Klik op **Run**. De uitvoer geeft de sensorgegevens en berichten weer die worden verzonden naar de IoT-hub.

   ![Raspberry Pi Azure IoT Online Simulator](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Stream Analytics-taak starten en uitvoer controleren

1. Open **Stream Analytics Explorer** in Visual Studio code en zoek de taak **myASAJob**.

2. Klik met de rechter muisknop op de naam van de taak. Selecteer vervolgens **starten** in het context menu.

   ![Stream Analytics-taak starten in VS code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Kies **nu** in het pop-upvenster om de taak te starten.

4. Houd er rekening mee dat de taak status is gewijzigd in **wordt uitgevoerd**. Klik met de rechter muisknop op de naam van de taak en kies **taak weergave openen in portal** om de metrische gegevens voor de invoer-en uitvoer gebeurtenis weer te geven. Deze actie kan enkele minuten duren.

5. Als u de resultaten wilt weer geven, opent u de Blob-opslag in de Visual Studio code extension of in de Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze snelstart zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Selecteer in het menu aan de linkerkant in Azure Portal de optie **Resourcegroepen** en selecteer vervolgens de resource die u hebt gemaakt.  

2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een eenvoudige Stream Analytics-taak geïmplementeerd met Visual Studio code. U kunt ook Stream Analytics-taken implementeren met behulp van de [Azure Portal](stream-analytics-quick-create-portal.md), [Power shell](stream-analytics-quick-create-powershell.md)en Visual Studio (stream-Analytics-Quick-Create-vs.MD). 

Ga voor meer informatie over Azure Stream Analytics-hulpprogramma's voor Visual Studio naar het volgende artikel:

> [!div class="nextstepaction"]
> [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
