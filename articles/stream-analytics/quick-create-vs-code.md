---
title: Snelstart - Een Azure Stream Analytics-taak maken in Visual Studio Code
description: Met deze quickstart u aan de slag gaan door een Stream Analytics-taak te maken, invoer en uitvoer te configureren en een query te definiëren met Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: fe666359b9ed2773f615ff496f4032bda5e74dc2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767764"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Snelstart: een Azure Stream Analytics-taak maken in Visual Studio Code (voorbeeld)

Met deze quickstart ziet u hoe u een Azure Stream Analytics-taak maakt en uitvoert met behulp van de azure stream analytics-extensie voor Visual Studio Code. In de voorbeeldtaak worden streaminggegevens van een Azure IoT Hub-apparaat gelezen. U definieert een taak die de gemiddelde temperatuur bij meer dan 27° berekent en de resulterende uitvoergebeurtenissen naar een nieuw bestand in blobopslag schrijft.

> [!NOTE]
> De hulpprogramma's Visual Studio en Visual Studio Code ondersteunen geen vacatures in de regio's China East, China North, Germany Central en Germany NorthEast.

## <a name="before-you-begin"></a>Voordat u begint

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/)aan.

* Meld u aan bij de [Azure-portal](https://portal.azure.com/).

* Installeer [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>De extensie Azure Stream Analytics Tools installeren

1. Open Visual Studio Code.

2. Zoek vanuit **extensies** in het linkerdeelvenster naar **Stream Analytics** en selecteer **Installeren** in de azure **stream analytics-extensie.**

3. Nadat de extensie is geïnstalleerd, controleert u of **Azure Stream Analytics-hulpprogramma's** zichtbaar zijn in **Ingeschakelde extensies**.

   ![Azure Stream Analytics-hulpprogramma's onder ingeschakelde extensies in Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>De extensie Azure Stream Analytics Tools activeren

1. Selecteer het **Azure-pictogram** op de activiteitsbalk van visual studiocode. Selecteer Onder **Stream Analytics** op de zijbalk de optie Aanmelden **bij Azure**.

   ![Aanmelden bij Azure in Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Wanneer u bent aangemeld, wordt uw Azure-accountnaam weergegeven op de statusbalk in de linkerbenedenhoek van het venster Visual Studio Code.

> [!NOTE]
> De azure stream analytics-extensie meldt u de volgende keer automatisch als u zich niet afmeldt. Als uw account tweestapsverificatie heeft, raden we u aan telefoonverificatie te gebruiken in plaats van een pincode te gebruiken.
> Als u problemen hebt met aanbiedingsbronnen, helpt het afmelden en opnieuw aanmelden. Als u zich wilt `Azure: Sign Out`afmelden, voert u de opdracht in .

## <a name="prepare-the-input-data"></a>De invoergegevens voorbereiden

Voordat u de taak Stream Analytics definieert, moet u de gegevens voorbereiden die later zijn geconfigureerd als de taakinvoer. Voer de volgende stappen uit om de invoergegevens voor te bereiden die de taak vereist:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Een bron** > **Internet of Things** > **IoT Hub maken**.

3. Voer in het deelvenster **IoT Hub** de volgende informatie in:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Abonnement  | \<Uw abonnement\> |  Selecteer het Azure-abonnement dat u wilt gebruiken. |
   |Resourcegroep   |   asaquickstart-resourcegroup  |   Selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep voor uw account in. |
   |Regio  |  \<Selecteer de regio die het dichtst bij uw gebruikers is gelegen\> | Selecteer een geografische locatie waar u uw IoT-hub hosten. Gebruik de locatie die het dichtst bij uw gebruikers is. |
   |Naam van de IoT-hub  | MyASAIoTHub  |   Selecteer een naam voor uw IoT-hub.   |

   ![Een IoT Hub maken](./media/quick-create-vs-code/create-iot-hub.png)

4. Selecteer **Volgende: grootte instellen en schaal aanpassen**.

5. Maak een selectie voor **prijzen en schaalniveaus**. Selecteer voor deze quickstart de categorie **F1 - Gratis** als deze nog beschikbaar is voor uw abonnement. Als de categorie Gratis niet beschikbaar is, kiest u de laagste beschikbare categorie. Zie [Azure IoT Hub-prijzen](https://azure.microsoft.com/pricing/details/iot-hub/)voor meer informatie.

   ![Uw IoT-hub vergroten en schalen](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Selecteer **Controleren + maken**. Bekijk de informatie over uw IoT-hub en selecteer **Maken**. Het maken van de IoT-hub kan een paar minuten duren. U de voortgang in het deelvenster **Meldingen** controleren.

7. Selecteer in het navigatiemenu van uw IoT-hub de optie **Toevoegen** onder **IoT-apparaten**. Voeg een ID toe voor **apparaat-id**en selecteer **Opslaan**.

   ![Een apparaat toevoegen aan uw IoT-hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Nadat het apparaat is gemaakt, opent u het apparaat in de lijst **met IoT-apparaten.** Kopieer de tekenreeks in **verbindingstekenreeks (primaire toets)** en sla deze op in een notitieblok om later te gebruiken.

   ![Verbindingstekenreeks voor IoT-hub-apparaat kopiëren](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>De IoT-simulator uitvoeren

1. Open de [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) in een nieuw browsertabblad of -venster.

2. Vervang de tijdelijke aanduiding in lijn 15 door de verbindingstekenreeks voor het IoT-hub-apparaat die u eerder hebt opgeslagen.

3. Selecteer **Uitvoeren**. De uitvoer moet de sensorgegevens en -berichten weergeven die naar uw IoT-hub worden verzonden.

   ![Raspberry Pi Azure IoT Online Simulator met uitvoer](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Blob-opslag maken

1. Selecteer linksboven in de Azure-portal de optie > Een > **bronopslagopslagaccount** **maken**.**Storage**

2. Voer in het deelvenster **Opslagaccount maken** een opslagaccountnaam, locatie en resourcegroep in. Kies dezelfde locatie- en resourcegroep als de IoT-hub die u hebt gemaakt. Selecteer vervolgens **Controleren + maken** om het account te maken.

   ![Een opslagaccount maken](./media/quick-create-vs-code/create-storage-account.png)

3. Nadat uw opslagaccount is gemaakt, selecteert u de tegel **Klodders** in het deelvenster **Overzicht.**

   ![Overzicht van opslagaccounts](./media/quick-create-vs-code/blob-storage.png)

4. Selecteer op de pagina **Blob Service** de optie **Container** en geef een naam op voor de container, bijvoorbeeld **container1**. Laat **het toegangsniveau van openbaar als** privé **(geen anonieme toegang)** en selecteer **OK**.

   ![Een blob-container maken](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Een Stream Analytics-project maken

1. Selecteer in Visual Studio Code **Ctrl+Shift+P** om het opdrachtpalet te openen. Voer vervolgens **ASA** in en selecteer **ASA: Nieuw project maken**.

   ![Een nieuw project maken](./media/quick-create-vs-code/create-new-project.png)

2. Voer uw projectnaam in, zoals **myASAproj,** en selecteer een map voor uw project.

    ![Een projectnaam maken](./media/quick-create-vs-code/create-project-name.png)

3. Het nieuwe project wordt toegevoegd aan uw werkruimte. Een Stream Analytics-project bestaat uit drie mappen: **Inputs,** **Outputs**en **Functions**. Het heeft ook het queryscript **(*.asaql),** een **JobConfig.json-bestand** en een **asaproj.json-configuratiebestand.**

    Het **configuratiebestand asaproj.json** bevat de gegevens over de invoer, uitvoer en taakconfiguratie die nodig zijn voor het indienen van de taak Stream Analytics naar Azure.

    ![Stream Analytics-projectbestanden in Visual Studio Code](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> Wanneer u ingangen en uitgangen uit het opdrachtpalet toevoegt, worden de bijbehorende paden automatisch toegevoegd aan **asaproj.json.** Als u direct ingangen of uitgangen op de schijf toevoegt of verwijdert, moet u deze handmatig toevoegen of verwijderen van **asaproj.json**. U ervoor kiezen om de ingangen en uitgangen op één plaats te plaatsen en er vervolgens naar te verwijzen in verschillende taken door de paden in elk **asaproj.json-bestand** op te geven.

## <a name="define-the-transformation-query"></a>De transformatiequery definiëren

1. Open **myASAproj.asaql** vanuit uw projectmap.

2. Voeg de volgende query toe:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="define-a-live-input"></a>Een live-invoer definiëren

1. Klik met de rechtermuisknop op de map **Invoer** in uw Stream Analytics-project. Selecteer vervolgens **ASA: Invoer toevoegen** in het contextmenu.

    ![Invoer toevoegen vanuit de map Invoer](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Of selecteer **Ctrl+Shift+P** om het opdrachtpalet te openen en voer **ASA in: Invoer toevoegen**.

   ![Stream Analytics-invoer toevoegen in Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Kies **IoT Hub** voor het invoertype.

   ![IoT-hub selecteren als invoeroptie](./media/quick-create-vs-code/iot-hub.png)

3. Als u de invoer uit het opdrachtpalet hebt toegevoegd, kiest u het queryscript Stream Analytics dat de invoer gebruikt. Het moet automatisch worden gevuld met het bestandspad naar **myASAproj.asaql**.

   ![Een Stream Analytics-script selecteren in Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Kies **Selecteren in uw Azure-abonnementen** in het vervolgkeuzemenu.

    ![Kiezen uit abonnementen](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Bewerk het nieuw gegenereerde **IoTHub1.json-bestand** met de volgende waarden. Houd standaardwaarden bij voor velden die hier niet worden vermeld.

   |Instelling|Voorgestelde waarde|Beschrijving|
   |-------|---------------|-----------|
   |Naam|Invoer|Voer een naam in om de invoer van de taak te identificeren.|
   |IotHubNamespace|MyASAIoTHub|Kies of voer de naam van uw IoT-hub in. IoT-hubnamen worden automatisch gedetecteerd als ze in hetzelfde abonnement zijn gemaakt.|
   |Naam van SharedAccessPolicyName|iothubowner| |

   U de functie CodeLens gebruiken om u te helpen een tekenreeks in te voeren, uit een vervolgkeuzelijst te kiezen of de tekst rechtstreeks in het bestand te wijzigen. In de volgende schermafbeelding wordt **Selecteren uit uw abonnementen** als voorbeeld weergegeven. De referenties worden automatisch weergegeven en opgeslagen in lokale referentiebeheer.

   ![Invoer configureren in Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

   ![Invoerwaarde configureren in Visual Studio Code](./media/quick-create-vs-code/configure-input-value.png)

## <a name="preview-input"></a>Voorvertoning van invoer

Selecteer **Voorbeeldgegevens** in **IoTHub1.json** vanaf de bovenste regel. Sommige invoergegevens worden opgehaald uit de IoT-hub en weergegeven in het voorbeeldvenster. Dit proces kan een tijdje duren.

 ![Voorbeeld van live-invoer bekijken](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Een uitvoer definiëren

1. Selecteer **Ctrl+Shift+P** om het opdrachtpalet te openen. Voer vervolgens **ASA in: Uitvoer toevoegen**.

   ![Stream Analytics-uitvoer toevoegen in Visual Studio-code](./media/quick-create-vs-code/add-output.png)

2. Kies **Blob-opslag** voor het gootsteentype.

3. Kies het queryscript Stream Analytics dat deze invoer gebruikt.

4. Voer de naam van het uitvoerbestand in als **BlobStorage**.

5. Bewerk **BlobStorage** met de volgende waarden. Houd standaardwaarden bij voor velden die hier niet worden vermeld. Gebruik de functie CodeLens om u te helpen kiezen uit een vervolgkeuzelijst of een tekenreeks in te voeren.

   |Instelling|Voorgestelde waarde|Beschrijving|
   |-------|---------------|-----------|
   |Naam|Uitvoer| Voer een naam in om de uitvoer van de taak te identificeren.|
   |Opslagaccount|asaquickstartstorage|Kies of voer de naam van uw opslagaccount in. Opslagaccountnamen worden automatisch gedetecteerd als ze in hetzelfde abonnement zijn gemaakt.|
   |Container|container1|Selecteer de bestaande container die u in uw opslagaccount hebt gemaakt.|
   |Padpatroon|output|Voer de naam in van een bestandspad dat in de container moet worden gemaakt.|

   ![Uitvoer configureren in Visual Studio-code](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Het script compileren

Scriptcompilatie controleert de syntaxis en genereert de Azure Resource Manager-sjablonen voor automatische implementatie.

Er zijn twee manieren om scriptcompilatie te activeren:

- Selecteer het script in de werkruimte en compileer vervolgens uit het opdrachtpalet.

   ![Het opdrachtpalet Visual Studio Code gebruiken om het script samen te stellen](./media/quick-create-vs-code/compile-script1.png)

- Klik met de rechtermuisknop op het script en selecteer **ASA: Script compileren**.

    ![Klik met de rechtermuisknop op het Script van Stream Analytics om te compileren](./media/quick-create-vs-code/compile-script2.png)

Na het maken u de twee gegenereerde Azure Resource Manager-sjablonen vinden in de map **Implementeren** van uw project. Deze twee bestanden worden gebruikt voor automatische implementatie.

![Implementatiesjablonen voor Stream Analytics in Verkenner](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Een Stream Analytics-taak verzenden naar Azure

1. Selecteer **Verzenden naar Azure**in het venster scripteditor van uw queryscript .

   ![Tekst uit uw abonnementen selecteren in de scripteditor](./media/quick-create-vs-code/submit-job.png)

2. Selecteer uw abonnement in de pop-uplijst.

3. Kies **Een taak selecteren**. Kies vervolgens **Nieuwe taak maken**.

4. Voer je vacaturenaam in, **myASAjob.** Volg vervolgens de instructies om de resourcegroep en -locatie te kiezen.

5. Selecteer **Verzenden naar Azure**. U de logboeken vinden in het uitvoervenster. 

6. Wanneer uw taak is gemaakt, u deze zien in **Stream Analytics Explorer.**

    ![Aangeboden taak in Stream Analytics Explorer](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Stream Analytics-taak starten en uitvoer controleren

1. Open **Stream Analytics Explorer** in Visual Studio Code en vind je baan, **myASAJob.**

2. Klik met de rechtermuisknop op de naam van de taak. Selecteer vervolgens **Start** in het contextmenu.

   ![De functie Stream Analytics starten in Visual Studio Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Kies **Nu** in het pop-upvenster om de taak te starten.

4. Houd er rekening mee dat de taakstatus is gewijzigd in **Actief .** Klik met de rechtermuisknop op de taaknaam en selecteer **Taakweergave openen in Portal** om de statistieken van de invoer- en uitvoergebeurtenis weer te geven. Deze actie kan enkele minuten duren.

5. Als u de resultaten wilt bekijken, opent u de blob-opslag in de visual studiocode-extensie of in de Azure-portal.

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de brongroep, de streamingtaak en alle gerelateerde bronnen. Als u de taak uitdeed, wordt voorkomen dat de streaming-eenheden die de taak verbruikt, worden facturering. 

Als u van plan bent om de taak in de toekomst te gebruiken, u deze stoppen en later opnieuw starten. Als u deze taak niet opnieuw gaat gebruiken, gebruikt u de volgende stappen om alle resources die u in deze snelstart hebt gemaakt, te verwijderen:

1. Selecteer **resourcegroepen** in het linkermenu in de Azure-portal en selecteer vervolgens de naam van de bron die u hebt gemaakt.  

2. Selecteer **Verwijderen** op de pagina van de resourcegroep. Voer de naam in van de resource die u wilt verwijderen in het tekstvak en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een eenvoudige Stream Analytics-taak geïmplementeerd met Behulp van Visual Studio Code. U streamanalytics-taken ook implementeren via de [Azure-portal,](stream-analytics-quick-create-portal.md) [PowerShell](stream-analytics-quick-create-powershell.md)en [Visual Studio.](stream-analytics-quick-create-vs.md)

Ga voor meer informatie over Azure Stream Analytics Tools for Visual Studio Code verder naar de volgende artikelen:

* [Query's van TestStream Analytics lokaal met voorbeeldgegevens met Behulp van Visual Studio Code](visual-studio-code-local-run.md)

* [Azure Stream Analytics-taken lokaal testen tegen live-invoer met Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Visual Studio Code gebruiken om Azure Stream Analytics-taken weer te geven](visual-studio-code-explore-jobs.md)

* [CI/CD-pijplijnen instellen met het npm-pakket](setup-cicd-vs-code.md)
