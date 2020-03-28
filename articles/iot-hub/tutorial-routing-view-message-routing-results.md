---
title: Resultaten van Azure IoT Hub-berichtroutering weergeven (.NET) | Microsoft Documenten
description: Nadat u alle resources hebt ingesteld met deel 1 van de zelfstudie, voegt u de mogelijkheid toe om berichten naar Azure Stream Analytics te routeren en de resultaten in PowerBI weer te geven.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bfee4e64070e5f37eaa3d63280409f00c0ed8672
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890395"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Zelfstudie: Deel 2 - Bekijk de gerouteerde berichten

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regels voor het routeren van de berichten

Dit zijn de regels voor de berichtroutering; deze werden opgezet in deel 1 van deze tutorial, en je ziet ze werken in dit tweede deel.

|Waarde |Resultaat|
|------|------|
|niveau = 'opslag' |Schrijf naar Azure Storage.|
|niveau = 'kritiek' |Schrijf naar een Service Bus-wachtrij. Een logische app haalt het bericht op uit de wachtrij en gebruikt Office 365 om het bericht via e-mail te versturen.|
|standaardinstelling |Geef deze gegevens weer met Power BI.|

Nu maakt u de bronnen waarnaar de berichten worden gerouteerd, voert u een app uit om berichten naar de hub te verzenden en ziet u de routebeschrijving in actie.

## <a name="create-a-logic-app"></a>Een logische app maken  

De Service Bus-wachtrij moet worden gebruikt voor het ontvangen van berichten die zijn aangeduid als kritiek. Installeer een logische app die wordt geactiveerd om de Service Bus-wachtrij te bewaken en een e-mailbericht te verzenden wanneer een bericht wordt toegevoegd aan de wachtrij.

1. Selecteer in de [Azure-portal](https://portal.azure.com) **+ Een resource maken**. Typ **logische app** in het zoekvak en klik op Enter. Selecteer in de weergegeven zoekresultaten Logic App en selecteer **Vervolgens Maken** om door te gaan naar het deelvenster Logische **app maken.** Vul de velden in.

   **Naam**: dit veld is de naam van de logische app. In deze zelfstudie wordt gebruikgemaakt van **ContosoLogicApp**.

   **Abonnement**: selecteer uw Azure-abonnement.

   **Resourcegroep:** selecteer **Bestaande gebruiken** en selecteer uw resourcegroep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

   **Locatie**: gebruik uw locatie. In deze zelfstudie wordt gebruikgemaakt van **VS - west**.

   **Log Analytics**: deze wisselknop moet worden uitgeschakeld.

   ![Het scherm Logica-app maken](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Selecteer **Maken**. Het kan enkele minuten duren voordat de app is geïmplementeerd.

2. Ga nu naar de logische app. De eenvoudigste manier om naar de Logic App te gaan, is door **Resourcegroepen**te selecteren, uw resourcegroep te selecteren (deze zelfstudie gebruikt **ContosoResources)** en selecteert u vervolgens de Logische App in de lijst met bronnen. 

    De pagina Ontwerpfunctie voor logic apps wordt weergegeven (misschien moet u naar rechts schuiven om de volledige pagina te zien). Schuif op de pagina Logic Apps Designer omlaag totdat u de tegel met de tekst **Blank Logic App +** ziet en selecteer deze. Het standaardtabblad is 'Voor u'. Als dit deelvenster leeg is, selecteert u **Alles** om alle beschikbare connectors en triggers weer te geven.

3. Selecteer **Servicebus** in de lijst met connectoren.

   ![De lijst met connectoren](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Er wordt een lijst met triggers weergegeven. Selecteer **Wanneer een bericht wordt ontvangen in een wachtrij (automatisch aanvullen) / Servicebus**.

   ![De lijst met triggers voor de Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Vul op het volgende scherm de naam van de verbinding in. In deze zelfstudie wordt gebruikgemaakt van **ContosoConnection**.

   ![De verbinding instellen voor de wachtrij servicebus](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Selecteer de naamruimte servicebus. In deze zelfstudie wordt gebruikgemaakt van **ContosoSBNamespace**. Wanneer u de naamruimte selecteert, vraagt de portal de Service Bus-naamruimte op voor het ophalen van de sleutels. Selecteer **RootManageSharedAccessKey** en selecteer **Maken**.

   ![Het instellen van de verbinding voltooien](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Selecteer de naam van de wachtrij op het volgende scherm (in deze zelfstudie wordt gebruikgemaakt van **contososbqueue**) in de vervolgkeuzelijst. U kunt de standaardwaarden gebruiken voor de rest van de velden.

   ![De wachtrijopties](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Stel nu de actie in voor het verzenden van het e-mailbericht wanneer een bericht wordt ontvangen in de wachtrij. Selecteer in de Logische apps designer de optie **+ Nieuwe stap** om een stap toe te voegen en selecteer **Alles** om alle beschikbare opties weer te geven. Zoek en selecteer **Office 365 Outlook**in **het actievenster kiezen** . Selecteer in het scherm Acties de optie **Een e-mail verzenden / Office 365 Outlook**.  

   ![De Office365-opties](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Meld u aan bij uw Office 365-account om de verbinding in te stellen. Als dit een keer uitgaat, probeer het dan opnieuw. Geef de e-mailadressen op voor de ontvanger(s) van de e-mailberichten. Geef tevens het onderwerp op en het type berichten dat u wilt dat de ontvanger in de hoofdtekst zal zien. Vul voor het testen uw eigen e-mailadres in als ontvanger.

   Selecteer **Dynamische inhoud toevoegen** om de inhoud weer te geven uit het bericht dat u opnemen. Selecteer **Inhoud**: hiermee wordt het bericht in het e-mailbericht opgenomen.

   ![De e-mailopties voor de logische app](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Selecteer **Opslaan**. Sluit daarna de ontwerpfunctie voor de logische app af.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics installeren

Als u de gegevens in een Power BI-visualisatie wilt zien, stelt u eerst een Stream Analytics-taak in om de gegevens op te halen. Houd er rekening mee dat alleen de berichten waarin het **niveau****normaal** is, worden verzonden naar het standaardeindpunt en door de Stream Analytics-taak voor de Power BI-visualisatie worden opgehaald.

### <a name="create-the-stream-analytics-job"></a>De Stream Analytics-taak maken

1. Selecteer in de [Azure-portal](https://portal.azure.com)de taak **Een bron** > **internet of things** > **stream analytics**maken .

2. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn. In deze zelfstudie wordt gebruikgemaakt van **contosoJob**.

   **Abonnement:** het Azure-abonnement dat u voor de zelfstudie gebruikt.

   **Resourcegroep**: gebruik dezelfde resourcegroep die wordt gebruikt door uw IoT Hub. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

   **Locatie**: gebruik dezelfde locatie die u in het instellingsscript hebt gebruikt. In deze zelfstudie wordt gebruikgemaakt van **VS - west**.

   ![De taak voor streamanalyse maken](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Selecteer **Maken** om de taak te maken. Het kan enkele minuten duren voordat u deze inzet hebt.

    Als u terug wilt gaan naar de taak, selecteert u **Resourcegroepen**. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**. Selecteer de resourcegroep en selecteer vervolgens de taak Stream Analytics in de lijst met bronnen.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Selecteer **Invoer onder** **Job Topology**.

2. Selecteer **in** het deelvenster Invoer de optie **Stream-invoer toevoegen** en selecteer IoT Hub. Vul de volgende velden in op het scherm dat wordt weergegeven:

   **Invoeralias**: in deze zelfstudie wordt gebruikgemaakt **contosoinputs**.

   **Selecteer IoT Hub in uw abonnement:** Selecteer deze keuze voor een keuzerondje.

   **Abonnement:** selecteer het Azure-abonnement dat u voor deze zelfstudie gebruikt.

   **IoT-hub:** selecteer de IoT-hub. In deze zelfstudie wordt gebruikgemaakt van **ContosoTestHub**.

   **Eindpunt**: selecteer **Berichten**. (Als u Operations Monitoring selecteert, krijgt u de telemetriegegevens over de IoT Hub in plaats van de gegevens die u doorstuurt.) 

   **Naam van het beleid voor gedeelde toegang:** **Service selecteren**. In de portal wordt de sleutel van het beleid voor gedeelde toegang voor u ingevuld.

   **Consumentengroep**: Selecteer de consumentengroep die is ingesteld in deel 1 van deze zelfstudie. In deze zelfstudie wordt gebruikgemaakt van **contosoconsumers**.
   
   Accepteer de standaardwaarden voor de rest van de velden. 

   ![De ingangen voor de streamanalysetaak instellen](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer **Job Topology** **Uitvoer .**

2. Selecteer **Toevoegen**en selecteer Vervolgens Power **BI**in het deelvenster **Uitvoer** . Vul de volgende velden in op het scherm dat wordt weergegeven:

   **Uitvoeralias**: de alias die uniek is voor de uitvoer. In deze zelfstudie wordt gebruikgemaakt van **contosooutputs**. 

   **Naam van de gegevensset**: de naam van de gegevensset die moet worden gebruikt in Power BI. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. 

   **Tabelnaam**: de naam van de tabel die moet worden gebruikt in Power BI. In deze zelfstudie wordt gebruikgemaakt van **contosotable**.

   Accepteer de standaardwaarden voor de rest van de velden.

3. Selecteer **Autoriseren**en meld u aan bij uw Power BI-account. (Dit kan meer dan een keer te proberen).

   ![De uitvoer voor de taak streamanalytics instellen](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Selecteer **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Selecteer **Query** onder **Taaktopologie**.

2. Vervang `[YourInputAlias]` door de invoeralias van de taak. In deze zelfstudie wordt gebruikgemaakt van **contosoinputs**.

3. Vervang `[YourOutputAlias]` door de uitvoeralias van de taak. In deze zelfstudie wordt gebruikgemaakt van **contosooutputs**.

   ![De query instellen voor de taak voor streamanalyse](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Selecteer **Opslaan**.

5. Sluit het deelvenster Query. U keert terug naar de weergave van de resources in de resourcegroep. Selecteer de taak Stream Analytics. In deze zelfstudie heet deze taak **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Voer de Stream Analytics-taak uit

Selecteer**nu** >  **starten** > **starten**in de taak Stream Analytics . Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

Als u het Power BI-rapport instelt, hebt u gegevens nodig, dus stelt u Power BI in nadat u het apparaat hebt gemaakt en de simulatietoepassing van het apparaat hebt ingesteld.

## <a name="run-simulated-device-app"></a>Gesimuleerde apparaat-app uitvoeren

In deel 1 van deze zelfstudie stelt u een apparaat in om te simuleren met behulp van een IoT-apparaat. In deze sectie downloadt u de .NET-console-app die het apparaat simuleert dat apparaat-naar-cloudberichten verzendt naar een IoT-hub (ervan uitgaande dat u de app en bronnen in deel 1 nog niet hebt gedownload).

Deze toepassing verzendt berichten voor elk van de verschillende routeringsmethoden voor berichten. Er is ook een map in de download die de volledige Azure Resource Manager-sjabloon en parametersbestand bevat, evenals de Azure CLI- en PowerShell-scripts.

Als u de bestanden niet hebt gedownload uit de repository in deel 1 van deze zelfstudie, download ze dan nu van [IoT Device Simulation.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Als u deze koppeling selecteert, wordt een opslagplaats gedownload met verschillende toepassingen erin; de oplossing die u zoekt is iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Dubbelklik op het oplossingsbestand (IoT_SimulatedDevice.sln) om de code in Visual Studio te openen en open Program.cs. Vervang `{your hub name}` door de hostnaam van de IoT Hub. De indeling van de hostnaam van de IoT Hub is **{iot-hub-name} .azure-devices.net**. Voor deze zelfstudie is de naam van de hubhost **ContosoTestHub.azure devices.net**. Vervang vervolgens `{your device key}` door de apparaatsleutel die u eerder hebt opgeslagen bij het instellen van het gesimuleerde apparaat. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Uitvoeren en testen

Voer de consoletoepassing uit. Wacht enkele minuten. U kunt de berichten zien die op het scherm van de console van de toepassing worden verzonden.

Deze app verzendt elke seconde een nieuw apparaat-naar-cloud-bericht naar de IoT Hub. Het bericht bevat een JSON-geserialiseerd object met een apparaat-id, temperatuur, vochtigheid en berichtniveau, die als standaardwaarde `normal` heeft. Hiermee wordt een willekeurig niveau `critical` of `storage` toegewezen, zodat het bericht wordt doorgestuurd naar het opslagaccount of naar de Service Bus-wachtrij (die uw logische app aanzet tot het verzenden van een e-mailbericht). De standaardmeetwaarden (`normal`) worden weergegeven in het BI-rapport dat u daarna instelt.

Als alles juist is ingesteld, zou u op dit moment de volgende resultaten moeten zien:

1. U gaat e-mailberichten ontvangen over kritieke berichten.

   ![De resulterende e-mails](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Dit resultaat betekent dat de volgende uitspraken waar zijn. 

   * De routering naar de Service Bus-wachtrij werkt correct.
   * De logische app die het bericht uit de Service Bus-wachtrij ophaalt, werkt correct.
   * De connector van de logische app voor Outlook werkt goed. 

2. Selecteer **resourcegroepen** in de [Azure-portal](https://portal.azure.com)en selecteer uw resourcegroep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**. 

    Selecteer het opslagaccount, selecteer **Containers**en selecteer vervolgens de container. In deze zelfstudie wordt gebruikgemaakt van **contosoresults**. U zou een map moeten zien, en u kunt inzoomen via de mappen totdat u een of meer bestanden ziet. Open een van deze bestanden; ze bevatten de vermeldingen die zijn doorgestuurd naar het opslagaccount. 

   ![Het resultaat bestanden in opslag](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Dit resultaat betekent dat de volgende verklaring waar is.

   * De routering naar de Service Bus-wachtrij werkt correct.

Nu de toepassing nog steeds actief is, stelt u de Power BI-visualisatie in om de berichten te zien die via de standaardroutering binnenkomen.

## <a name="set-up-the-power-bi-visualizations"></a>De Power BI-visualisaties instellen

1. Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/)-account.

2. Ga naar **Werkruimten** en selecteer de werkruimte die u hebt ingesteld toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **Mijn werkruimte**. 

3. Selecteer **Gegevenssets**. Als u geen gegevenssets hebt, wacht u een paar minuten en controleert u het opnieuw.

   U zou de vermelde gegevensset moeten zien die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. (Het kan 5-10 minuten duren voordat de gegevensset voor de eerste keer wordt weergegeven.)

4. Selecteer **onder ACTIES**het eerste pictogram om een rapport te maken.

   ![Power BI-werkruimte met acties en rapportpictogram gemarkeerd](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Maak een lijndiagram om in realtime de temperatuur gedurende een bepaalde periode weer te geven.

   * Voeg op de pagina het maken van rapporten een lijndiagram toe door het pictogram lijndiagram te selecteren.

     ![De visualisaties en velden](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * Klap in het deelvenster **Velden** de tabel uit die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **contosotable**.

   * Sleep **EventEnqueuedUtcTime** naar **As** in het deelvenster **Visualisaties**.

   * Sleep **Temperatuur** naar **Waarden**.

   Er wordt een lijndiagram gemaakt. De x-as geeft de datum en tijd in UTC-tijdzone aan. De y-as geeft de temperatuur van de sensor aan.

6. Maak een ander lijndiagram om in realtime de vochtigheid gedurende een bepaalde periode weer te geven. Als u de tweede grafiek wilt instellen, volgt u hetzelfde proces voor de eerste grafiek, waarbij **EventEnqueuedUtcTime** op de x-as **(As**) en **vochtigheid** op de y-as **(Waarden) wordt**geplaatst.

   ![Het uiteindelijke Power BI-rapport met de twee grafieken](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Selecteer **Opslaan** om het rapport op te slaan en voert een naam in voor het rapport als daarom wordt gevraagd.

U zou gegevens moeten kunnen zien in beide diagrammen. Dit resultaat betekent dat de volgende uitspraken waar zijn:

   * De routering naar het standaardeindpunt werkt correct.
   * De Azure Stream Analytics-taak stroomt correct.
   * De Power BI-visualisatie is juist ingesteld.

U de grafieken vernieuwen om de meest recente gegevens te bekijken door de knop Vernieuwen boven aan het Power BI-venster te selecteren. 

## <a name="clean-up-resources"></a>Resources opschonen 

Als u alle Azure-resources die u via beide delen van deze zelfstudie hebt gemaakt, wilt verwijderen, verwijdert u de brongroep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT Hub, de Service Bus-naamruimte en -wachtrij, de logische app, het opslagaccount en de resourcegroep zelf verwijderd. U ook de Power BI-bronnen verwijderen en de e-mails wissen die tijdens de zelfstudie zijn verzonden.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Resources in de Power BI-visualisatie opschonen

Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/)-account. Ga naar uw werkruimte. In deze zelfstudie wordt gebruikgemaakt van **Mijn werkruimte**. Als u de Power BI-visualisatie wilt verwijderen, gaat u naar DataSets en selecteert u het pictogram prullenbak om de gegevensset te verwijderen. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. Wanneer u de gegevensset verwijdert, wordt het rapport ook verwijderd.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>De Azure CLI gebruiken om resources op te schonen

U kunt de resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup`was ingesteld op **ContosoResources** terug aan het begin van deze tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>PowerShell gebruiken om resources op te schonen

U kunt de resourcegroep verwijderen met de opdracht [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup`was ingesteld op **ContosoResources** terug aan het begin van deze tutorial.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Teste-mails opschonen

U ook de hoeveelheid e-mails in uw postvak IN verwijderen die zijn gegenereerd via de Logic App terwijl de apparaattoepassing werd uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In deze 2-delige zelfstudie hebt u geleerd hoe u berichtroutering gebruiken om IoT Hub-berichten naar verschillende bestemmingen te routeren door de volgende taken uit te voeren.  

**Deel I: Resources maken, berichtroutering instellen**
> [!div class="checklist"]
> * Maak de resources: een IoT-hub, een opslagaccount, een servicebuswachtrij en een gesimuleerd apparaat.
> * Configureer de eindpunten en berichtroutes in IoT Hub voor het opslagaccount en de wachtrij voor servicebussen.

**Deel II: Berichten verzenden naar de hub, routeresultaten bekijken**
> [!div class="checklist"]
> * Maak een logische app die wordt geactiveerd en een e-mailbericht verzendt wanneer een bericht wordt toegevoegd aan de Service Bus-wachtrij.
> * Download een app die een IoT-apparaat aanzet tot het verzenden van berichten naar de hub voor de verschillende routeringsopties en voer deze uit.
> * Maak een Power BI-visualisatie voor gegevens die naar het standaardeindpunt worden verzonden.
> * Bekijk de resultaten...
> * .. .in de Service Bus-wachtrij en e-mailberichten.
> * ...in het opslagaccount.
> * ...in de Power BI-visualisatie.

Ga door naar de volgende zelfstudie voor informatie over het beheren van de toestand van een IoT-apparaat. 

> [!div class="nextstepaction"]
> [Metrische gegevens en diagnostische gegevens instellen en gebruiken met een IoT Hub](tutorial-use-metrics-and-diags.md)
