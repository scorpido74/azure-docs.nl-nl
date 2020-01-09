---
title: 'Quick Start: een Azure Stream Analytics-taak maken in Visual Studio code'
description: In deze Quick start ziet u hoe u aan de slag gaat met het maken van een Stream Analytics taak, het configureren van invoer en uitvoer en het definiëren van een query met Visual Studio code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 236d29d2c50a792bc4dc11916964db47ca092807
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659590"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Snelstartgids: een Azure Stream Analytics-taak in Visual Studio code maken (preview)

In deze Quick start ziet u hoe u een Azure Stream Analytics-taak kunt maken en uitvoeren met de uitbrei ding Azure Stream Analytics tools for Visual Studio code. De voorbeeld taak leest streaming-gegevens van een Azure IoT Hub-apparaat. U definieert een taak die de gemiddelde temperatuur bij meer dan 27° berekent en de resulterende uitvoergebeurtenissen naar een nieuw bestand in blobopslag schrijft.

## <a name="before-you-begin"></a>Voordat u begint

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.

* Meld u aan bij de [Azure Portal](https://portal.azure.com/).

* Installeer [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>De uitbrei ding voor de Azure Stream Analytics-Hulpprogram Ma's installeren

1. Open Visual Studio Code.

2. Zoek **in het** linkerdeel venster naar **Stream Analytics** en selecteer **installeren** op de uitbrei ding **Azure stream Analytics hulpprogramma's** .

3. Nadat de uitbrei ding is geïnstalleerd, controleert u of **Azure stream Analytics-Hulpprogram ma's** zichtbaar zijn in **ingeschakelde uitbrei dingen**.

   ![Azure Stream Analytics-Hulpprogram Ma's onder ingeschakelde uitbrei dingen in Visual Studio code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>De uitbrei ding voor de Azure Stream Analytics-Hulpprogram Ma's activeren

1. Selecteer het pictogram van **Azure** op de activiteiten balk van Visual Studio. Onder **Stream Analytics** aan de zijkant selecteert **u aanmelden bij Azure**.

   ![Aanmelden bij Azure in Visual Studio code](./media/quick-create-vs-code/azure-sign-in.png)

2. Wanneer u bent aangemeld, wordt de naam van uw Azure-account weer gegeven op de status balk in de linkerbenedenhoek van het venster Visual Studio code.

> [!NOTE]
> Als u zich niet afmeldt, wordt u in de volgende keer automatisch aangemeld met de uitbrei ding voor de Azure Stream Analytics-Hulpprogram Ma's. Als uw account twee ledige verificatie heeft, raden wij u aan gebruik te maken van de telefoon verificatie in plaats van een pincode te gebruiken.
> Als u problemen hebt met het weer geven van resources, kunt u zich niet meer afmelden en opnieuw aanmelden. Voer de opdracht `Azure: Sign Out`in om u af te melden.

## <a name="prepare-the-input-data"></a>De invoergegevens voorbereiden

Voordat u de Stream Analytics-taak definieert, moet u de gegevens voorbereiden die later zijn geconfigureerd als de taak invoer. Voer de volgende stappen uit om de invoer gegevens voor te bereiden die de taak vereist:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** > **Internet of Things** > **IoT Hub**.

3. Voer in het deelvenster **IoT Hub** de volgende informatie in:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Abonnement  | \<Uw abonnement\> |  Selecteer het Azure-abonnement dat u wilt gebruiken. |
   |Resourcegroep   |   asaquickstart-resourcegroup  |   Selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep voor uw account in. |
   |Regio  |  \<Selecteer de regio die het dichtst bij uw gebruikers is gelegen\> | Selecteer een geografische locatie waar u uw IoT-hub kunt hosten. Gebruik de locatie die het dichtst bij uw gebruikers is. |
   |Naam van de IoT-hub  | MyASAIoTHub  |   Selecteer een naam voor uw IoT-hub.   |

   ![Een IoT Hub maken](./media/quick-create-vs-code/create-iot-hub.png)

4. Selecteer **Volgende: grootte instellen en schaal aanpassen**.

5. Maak een selectie voor de **prijs-en schaal categorie**. Selecteer voor deze quickstart de categorie **F1 - Gratis** als deze nog beschikbaar is voor uw abonnement. Als de categorie Gratis niet beschikbaar is, kiest u de laagste beschikbare categorie. Zie [prijzen van Azure IOT hub](https://azure.microsoft.com/pricing/details/iot-hub/)voor meer informatie.

   ![Uw IoT-hub verg Roten of verkleinen](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Selecteer **Controleren + maken**. Controleer de gegevens van uw IoT-hub en selecteer **maken**. Het maken van de IoT-hub kan een paar minuten duren. U kunt de voortgang controleren in het deel venster **meldingen** .

7. Selecteer onder **IOT-apparaten** **toevoegen** op het navigatie menu van uw IOT-hub. Voeg een ID toe voor **apparaat-id**en selecteer **Opslaan**.

   ![Een apparaat toevoegen aan uw IoT-hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Nadat het apparaat is gemaakt, opent u het apparaat in de lijst **IOT-apparaten** . Kopieer de teken reeks in de **verbindings reeks (primaire sleutel)** en sla deze op in een Klad blok, zodat u deze later kunt gebruiken.

   ![IoT hub-apparaat connection string kopiëren](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>De IoT-simulator uitvoeren

1. Open de [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) in een nieuw browsertabblad of -venster.

2. Vervang de tijdelijke aanduiding in regel 15 met de IoT hub-apparaat connection string die u eerder hebt opgeslagen.

3. Selecteer **Uitvoeren**. De uitvoer moet de sensor gegevens en berichten weer geven die worden verzonden naar uw IoT-hub.

   ![Raspberry Pi Azure IoT online Simulator met uitvoer](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Blob-opslag maken

1. Selecteer in de linkerbovenhoek van de Azure Portal **een resource maken** > **opslag** > **opslag account**.

2. Voer in het deelvenster **Opslagaccount maken** een opslagaccountnaam, locatie en resourcegroep in. Kies dezelfde locatie en resource groep als de IoT-hub die u hebt gemaakt. Selecteer vervolgens **controleren + maken** om het account te maken.

   ![Een opslagaccount maken](./media/quick-create-vs-code/create-storage-account.png)

3. Nadat uw opslag account is gemaakt, selecteert u de tegel **blobs** in het deel venster **overzicht** .

   ![Overzicht van opslagaccounts](./media/quick-create-vs-code/blob-storage.png)

4. Selecteer op de pagina **Blob Service** de optie **Container** en geef een naam op voor de container, bijvoorbeeld **container1**. **Open een openbaar toegangs niveau** als **privé (geen anonieme toegang)** en selecteer **OK**.

   ![Een blob-container maken](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Een Stream Analytics-project maken

1. Selecteer in Visual Studio code **CTRL + SHIFT + P** om het opdracht palet te openen. Voer vervolgens **ASA** in en selecteer **ASA: nieuw project maken**.

   ![Een nieuw project maken](./media/quick-create-vs-code/create-new-project.png)

2. Voer de naam van uw project in, bijvoorbeeld **myASAproj**, en selecteer een map voor uw project.

    ![Een project naam maken](./media/quick-create-vs-code/create-project-name.png)

3. Het nieuwe project wordt toegevoegd aan uw werk ruimte. Een Stream Analytics project bestaat uit drie mappen: **invoer**, **uitvoer**en **functies**. Het bevat ook het query script **(*. asaql)** , een **JobConfig. json** -bestand en een **asaproj. json** -configuratie bestand.

    Het configuratie bestand **asaproj. json** bevat de invoer-, uitvoer-en taak configuratie bestand gegevens die nodig zijn voor het indienen van de stream Analytics-taak naar Azure.

    ![Stream Analytics project bestanden in Visual Studio code](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> Wanneer u invoer en uitvoer toevoegt vanuit het opdracht palet, worden de bijbehorende paden automatisch toegevoegd aan **asaproj. json** . Als u invoer of uitvoer rechtstreeks aan de schijf toevoegt of verwijdert, moet u deze hand matig toevoegen aan of verwijderen uit **asaproj. json**. U kunt ervoor kiezen om de invoer en uitvoer op één plek te plaatsen en ernaar te verwijzen in verschillende taken door de paden op te geven in elk bestand **asaproj. json** .

## <a name="define-the-transformation-query"></a>De transformatiequery definiëren

1. Open **myASAproj. asaql** uit de projectmap.

2. Voeg de volgende query toe:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="test-the-query-locally-with-sample-data"></a>De query lokaal testen met voorbeeld gegevens

Voordat u de query in de Cloud uitvoert, kunt u deze lokaal testen met een lokaal bestand met voorbeeld gegevens of met gegevens die zijn vastgelegd in Live invoer om de query logica te controleren.

Volg de instructies in [Query's testen lokaal met voorbeeld gegevens](visual-studio-code-local-run.md) voor meer informatie.

 ![Testen met voorbeeld gegevens in Visual Studio code](./media/vscode-local-run/localrun-localinput.gif)

## <a name="define-a-live-input"></a>Een live-invoer definiëren

1. Klik met de rechter muisknop op de map met **invoer** gegevens in uw stream Analytics-project. Selecteer vervolgens **ASA: invoer toevoegen** in het context menu.

    ![Invoer toevoegen vanuit de map met invoer](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Of selecteer **CTRL + SHIFT + P** om het opdracht palet te openen en voer **asa in: invoer toevoegen**.

   ![Stream Analytics invoer toevoegen in Visual Studio code](./media/quick-create-vs-code/add-input.png)

2. Kies **IOT hub** voor het invoer type.

   ![IoT hub selecteren als de optie invoer](./media/quick-create-vs-code/iot-hub.png)

3. Als u de invoer van het opdracht palet hebt toegevoegd, kiest u het Stream Analytics query script waarmee de invoer wordt gebruikt. Deze moet automatisch worden ingevuld met het bestandspad naar **myASAproj. asaql**.

   ![Een Stream Analytics script selecteren in Visual Studio code](./media/quick-create-vs-code/asa-script.png)

4. Kies **selecteren in uw Azure-abonnementen** in de vervolg keuzelijst.

    ![Selecteren uit abonnementen](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Bewerk het zojuist gegenereerde bestand **IoTHub1. json** met de volgende waarden. Behoud standaard waarden voor velden die hier niet worden genoemd.

   |Instelling|Voorgestelde waarde|Beschrijving|
   |-------|---------------|-----------|
   |Name|Invoer|Voer een naam in om de invoer van de taak te identificeren.|
   |IotHubNamespace|MyASAIoTHub|Kies of typ de naam van uw IoT-hub. IoT-hub-namen worden automatisch gedetecteerd als ze worden gemaakt in hetzelfde abonnement.|
   |SharedAccessPolicyName|iothubowner| |

   U kunt de functie code lens gebruiken om een teken reeks op te geven, een selectie uit een vervolg keuzelijst te selecteren of de tekst rechtstreeks in het bestand te wijzigen. Op de volgende scherm afbeelding ziet u een voor beeld **van uw abonnementen** .

   ![Invoer in Visual Studio code configureren](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Preview-invoer

Selecteer **voorbeeld gegevens** in **IoTHub1. json** in de bovenste regel. Sommige invoer gegevens worden opgehaald van de IoT-hub en weer gegeven in het voorbeeld venster. Dit proces kan enige tijd duren.

 ![Live-invoer preview](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Uitvoer definiëren

1. Selecteer **CTRL + SHIFT + P** om het opdracht palet te openen. Voer vervolgens **ASA: uitvoer toevoegen**in.

   ![Stream Analytics uitvoer toevoegen in Visual Studio code](./media/quick-create-vs-code/add-output.png)

2. Kies **Blob Storage** voor het sink-type.

3. Kies het Stream Analytics query script waarmee deze invoer wordt gebruikt.

4. Voer de naam van het uitvoer bestand in als **BlobStorage**.

5. Bewerk **BlobStorage** door de volgende waarden te gebruiken. Behoud standaard waarden voor velden die hier niet worden genoemd. Gebruik de functie code lens om u te helpen bij het selecteren van een vervolg keuzelijst of door een teken reeks in te voeren.

   |Instelling|Voorgestelde waarde|Beschrijving|
   |-------|---------------|-----------|
   |Name|Uitvoer| Voer een naam in om de uitvoer van de taak te identificeren.|
   |Opslagaccount|asaquickstartstorage|Kies of typ de naam van uw opslag account. Namen van opslag accounts worden automatisch gedetecteerd als ze worden gemaakt in hetzelfde abonnement.|
   |Container|container1|Selecteer de bestaande container die u in uw opslagaccount hebt gemaakt.|
   |Padpatroon|output|Voer de naam in van een bestandspad dat in de container moet worden gemaakt.|

   ![Uitvoer in Visual Studio code configureren](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Het script compileren

Met de syntaxis controles voor script compilatie worden de Azure Resource Manager sjablonen voor automatische implementatie gegenereerd.

Er zijn twee manieren om script compilatie te activeren:

- Selecteer het script in de werk ruimte en compileer vervolgens vanuit het opdracht palet.

   ![Het Visual Studio code-opdracht palet gebruiken om het script te compileren](./media/quick-create-vs-code/compile-script1.png)

- Klik met de rechter muisknop op het script en selecteer **ASA: script compileren**.

    ![Klik met de rechter muisknop op het Stream Analytics script dat u wilt compileren](./media/quick-create-vs-code/compile-script2.png)

Na de compilatie vindt u de twee gegenereerde Azure Resource Manager sjablonen in de map **Deploy** van uw project. Deze twee bestanden worden gebruikt voor automatische implementatie.

![Stream Analytics implementatie sjablonen in Verkenner](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Een Stream Analytics taak naar Azure verzenden

1. Selecteer in het venster Script Editor van het query script de optie **verzenden naar Azure**.

   ![Selecteer een optie in de tekst van uw abonnementen in de Script Editor](./media/quick-create-vs-code/submit-job.png)

2. Selecteer uw abonnement in de pop-uplijst.

3. Kies **een taak selecteren**. Kies vervolgens **nieuwe taak maken**.

4. Voer de naam van uw taak in, **myASAjob**. Volg vervolgens de instructies om de resource groep en de locatie te kiezen.

5. Selecteer **verzenden naar Azure**. U kunt de logboeken vinden in het uitvoer venster. 

6. Wanneer de taak is gemaakt, kunt u deze in **Stream Analytics Verkenner**bekijken.

    ![De weer gegeven taak in Stream Analytics Explorer](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Stream Analytics-taak starten en uitvoer controleren

1. Open **Stream Analytics Explorer** in Visual Studio code en zoek de taak **myASAJob**.

2. Klik met de rechter muisknop op de naam van de taak. Selecteer vervolgens **starten** in het context menu.

   ![De Stream Analytics-taak starten in Visual Studio code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Kies **nu** in het pop-upvenster om de taak te starten.

4. Houd er rekening mee dat de taak status is gewijzigd in **wordt uitgevoerd**. Klik met de rechter muisknop op de naam van de taak en selecteer **open taak weergave in portal** om de metrische gegevens voor de invoer-en uitvoer gebeurtenis weer te geven. Deze actie kan enkele minuten duren.

5. Als u de resultaten wilt weer geven, opent u de Blob-opslag in de Visual Studio code extension of in de Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de resource groep, de streaming-taak en alle gerelateerde resources. Als u de taak verwijdert, worden facturering van de streaming-eenheden die de taak verbruikt, voor komen. 

Als u van plan bent om de taak in de toekomst te gebruiken, kunt u deze stoppen en later opnieuw opstarten. Als u deze taak niet meer wilt gebruiken, gebruikt u de volgende stappen om alle resources te verwijderen die u in deze Quick Start hebt gemaakt:

1. Selecteer in het menu links in het Azure Portal **resource groepen** en selecteer vervolgens de naam van de resource die u hebt gemaakt.  

2. Selecteer **Verwijderen** op de pagina van de resourcegroep. Voer de naam in van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een eenvoudige Stream Analytics-taak geïmplementeerd met Visual Studio code. U kunt Stream Analytics taken ook implementeren met behulp van de [Azure Portal](stream-analytics-quick-create-portal.md), [Power shell](stream-analytics-quick-create-powershell.md)en [Visual Studio](stream-analytics-quick-create-vs.md).

Ga door naar de volgende artikelen voor meer informatie over Azure Stream Analytics-Hulpprogram Ma's voor Visual Studio-code:

* [Azure Stream Analytics-taken lokaal testen op Live-invoer met Visual Studio code](visual-studio-code-local-run-live-input.md)

* [Visual Studio code gebruiken om Azure Stream Analytics taken weer te geven](visual-studio-code-explore-jobs.md)

* [CI/CD-pijp lijnen instellen met behulp van het NPM-pakket](setup-cicd-vs-code.md)
