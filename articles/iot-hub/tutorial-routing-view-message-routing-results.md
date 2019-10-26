---
title: Routerings resultaten voor Azure IoT Hub-berichten weer geven (.NET) | Microsoft Docs
description: Resultaten van de Routing van Azure IoT Hub-berichten weer geven
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 6d40f7d81faa77a48a32aadefdf44ed89e184efb
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900777"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Zelf studie: deel 2-de gerouteerde berichten weer geven

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regels voor het routeren van berichten

Dit zijn de regels voor de route ring van berichten. Deze zijn ingesteld in deel 1 van deze zelf studie, en u ziet dat ze in dit tweede deel werken.

|Waarde |Resultaat|
|------|------|
|niveau = 'opslag' |Schrijf naar Azure Storage.|
|niveau = 'kritiek' |Schrijf naar een Service Bus-wachtrij. Een logische app haalt het bericht op uit de wachtrij en gebruikt Office 365 om het bericht via e-mail te versturen.|
|standaardinstelling |Geef deze gegevens weer met Power BI.|

Nu maakt u de resources waarnaar de berichten worden doorgestuurd, voert u een app uit om berichten naar de hub te verzenden en bekijkt u de route ring in actie.

## <a name="create-a-logic-app"></a>Een logica-app maken  

De Service Bus-wachtrij moet worden gebruikt voor het ontvangen van berichten die zijn aangeduid als kritiek. Installeer een logische app die wordt geactiveerd om de Service Bus-wachtrij te bewaken en een e-mailbericht te verzenden wanneer een bericht wordt toegevoegd aan de wachtrij.

1. Selecteer in het [Azure Portal](https://portal.azure.com) **+ een resource maken**. Typ **logische app** in het zoekvak en klik op Enter. Selecteer in de weer gegeven Zoek resultaten Logic app en selecteer vervolgens **maken** om door te gaan naar het deel venster **logische app maken** . Vul de velden in.

   **Naam**: dit veld is de naam van de logische app. In deze zelfstudie wordt gebruikgemaakt van **ContosoLogicApp**.

   **Abonnement**: selecteer uw Azure-abonnement.

   **Resource groep**: Selecteer **bestaande gebruiken** en selecteer uw resource groep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

   **Locatie**: gebruik uw locatie. In deze zelfstudie wordt gebruikgemaakt van **US - west**.

   **Log Analytics**: deze wisselknop moet worden uitgeschakeld.

   ![Het scherm logische app maken](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Selecteer **Maken**. Het kan enkele minuten duren voordat de app is geïmplementeerd.

2. Ga nu naar de logische app. De eenvoudigste manier om naar de logische app te gaan, is door **resource groepen**te selecteren, de resource groep te selecteren (deze zelf studie maakt gebruik van **ContosoResources**) en selecteert vervolgens de logische app in de lijst met resources. 

    De pagina Ontwerpfunctie voor logic apps wordt weergegeven (misschien moet u naar rechts schuiven om de volledige pagina te zien). Schuif op de pagina Logic Apps Designer omlaag totdat u de tegel ziet met de tekst **lege logische app +** en selecteer deze. Het standaard tabblad is ' voor u '. Als dit deel venster leeg is, selecteert u **Alles** om alle beschik bare connectors en triggers weer te geven.

3. Selecteer **Service Bus** in de lijst met connectors.

   ![De lijst met connectors](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Er wordt een lijst met triggers weergegeven. Selecteren **Wanneer een bericht wordt ontvangen in een wachtrij (automatisch volt ooien)/service bus**.

   ![De lijst met triggers voor de Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Vul op het volgende scherm de naam van de verbinding in. In deze zelfstudie wordt gebruikgemaakt van **ContosoConnection**.

   ![Instellen van de verbinding voor de Service Bus wachtrij](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Selecteer de Service Bus naam ruimte. In deze zelfstudie wordt gebruikgemaakt van **ContosoSBNamespace**. Wanneer u de naamruimte selecteert, vraagt de portal de Service Bus-naamruimte op voor het ophalen van de sleutels. Selecteer **RootManageSharedAccessKey** en selecteer **maken**.

   ![Instellen van de verbinding volt ooien](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Selecteer de naam van de wachtrij op het volgende scherm (in deze zelfstudie wordt gebruikgemaakt van **contososbqueue**) in de vervolgkeuzelijst. U kunt de standaardwaarden gebruiken voor de rest van de velden.

   ![De wachtrij opties](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Stel nu de actie in voor het verzenden van het e-mailbericht wanneer een bericht wordt ontvangen in de wachtrij. Selecteer in de Logic Apps Designer **+ nieuwe stap** om een stap toe te voegen en selecteer vervolgens **Alles** om alle beschik bare opties weer te geven. Zoek in het deel venster **een actie kiezen** en selecteer **Office 365 Outlook**. Selecteer in het scherm acties de optie **een E-mail verzenden/Office 365 Outlook**.  

   ![De Office365-opties](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Meld u aan bij uw Office 365-account om de verbinding in te stellen. Als er een time-out optreedt, probeert u het opnieuw. Geef de e-mailadressen op voor de ontvanger(s) van de e-mailberichten. Geef tevens het onderwerp op en het type berichten dat u wilt dat de ontvanger in de hoofdtekst zal zien. Vul voor het testen uw eigen e-mailadres in als ontvanger.

   Selecteer **dynamische inhoud toevoegen** om de inhoud van het bericht dat u kunt bevatten weer te geven. Selecteer **Inhoud**: hiermee wordt het bericht in het e-mailbericht opgenomen.

   ![De e-mail opties voor de logische app](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Selecteer **Opslaan**. Sluit daarna de ontwerpfunctie voor de logische app af.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics instellen

Als u de gegevens in een Power BI-visualisatie wilt zien, stelt u eerst een Stream Analytics-taak in om de gegevens op te halen. Houd er rekening mee dat alleen de berichten waarin het **niveau** **normaal** is, worden verzonden naar het standaardeindpunt en door de Stream Analytics-taak voor de Power BI-visualisatie worden opgehaald.

### <a name="create-the-stream-analytics-job"></a>De Stream Analytics-taak maken

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een resource maken** > **Internet of Things** > **Stream Analytics taak**.

2. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn. In deze zelfstudie wordt gebruikgemaakt van **contosoJob**.

   **Abonnement**: het Azure-abonnement dat u voor de zelf studie gebruikt.

   **Resourcegroep**: gebruik dezelfde resourcegroep die wordt gebruikt door uw IoT Hub. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

   **Locatie**: gebruik dezelfde locatie die u in het instellingsscript hebt gebruikt. In deze zelfstudie wordt gebruikgemaakt van **US - west**.

   ![De stream Analytics-taak maken](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Selecteer **maken** om de taak te maken. Het kan een paar minuten duren voordat de implementatie is uitgevoerd.

    Selecteer **resource groepen**om terug te gaan naar de taak. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**. Selecteer de resource groep en selecteer vervolgens de taak Stream Analytics in de lijst met resources.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Selecteer **invoer**onder **taak topologie**.

2. Selecteer in het deel venster **invoer** de optie **stroom invoer toevoegen** en selecteer IOT hub. Vul de volgende velden in op het scherm dat wordt weergegeven:

   **Invoeralias**: in deze zelfstudie wordt gebruikgemaakt **contosoinputs**.

   **Selecteer IOT hub uit uw abonnement**: Selecteer deze keuze rondje.

   **Abonnement**: Selecteer het Azure-abonnement dat u voor deze zelf studie gebruikt.

   **IOT hub**: Selecteer de IOT-hub. In deze zelfstudie wordt gebruikgemaakt van **ContosoTestHub**.

   **Eindpunt**: selecteer **Berichten**. (Als u Operations Monitoring selecteert, krijgt u de telemetriegegevens over de IoT Hub in plaats van de gegevens die u doorstuurt.) 

   **Naam van beleid voor gedeelde toegang**: Selecteer **service**. In de portal wordt de sleutel van het beleid voor gedeelde toegang voor u ingevuld.

   **Consumenten groep**: Selecteer de consumenten groep die is ingesteld in deel 1 van deze zelf studie. In deze zelfstudie wordt gebruikgemaakt van **contosoconsumers**.
   
   Accepteer de standaardwaarden voor de rest van de velden. 

   ![De invoer voor de stream Analytics-taak instellen](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer **uitvoer**onder **taak topologie**.

2. Selecteer in het deel venster **outputs** de optie **toevoegen**en selecteer vervolgens **Power bi**. Vul de volgende velden in op het scherm dat wordt weergegeven:

   **Uitvoeralias**: de alias die uniek is voor de uitvoer. In deze zelfstudie wordt gebruikgemaakt van **contosooutputs**. 

   **Naam van de gegevensset**: de naam van de gegevensset die moet worden gebruikt in Power BI. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. 

   **Tabelnaam**: de naam van de tabel die moet worden gebruikt in Power BI. In deze zelfstudie wordt gebruikgemaakt van **contosotable**.

   Accepteer de standaardwaarden voor de rest van de velden.

3. Selecteer **autoriseren**en meld u aan bij uw Power bi-account. (Dit kan meer dan één poging doen).

   ![De uitvoer voor de stream Analytics-taak instellen](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Selecteer **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Selecteer **Query** onder **Taaktopologie**.

2. Vervang `[YourInputAlias]` door de invoeralias van de taak. In deze zelfstudie wordt gebruikgemaakt van **contosoinputs**.

3. Vervang `[YourOutputAlias]` door de uitvoeralias van de taak. In deze zelfstudie wordt gebruikgemaakt van **contosooutputs**.

   ![De query voor de stream Analytics-taak instellen](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Selecteer **Opslaan**.

5. Sluit het deelvenster Query. U keert terug naar de weer gave van de resources in de resource groep. Selecteer de Stream Analytics taak. In deze zelfstudie heet deze taak **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

Selecteer in de taak Stream Analytics **start** > **nu** > **starten**. Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

Als u het Power BI-rapport instelt, hebt u gegevens nodig, dus stelt u Power BI in nadat u het apparaat hebt gemaakt en de simulatietoepassing van het apparaat hebt ingesteld.

## <a name="run-simulated-device-app"></a>Gesimuleerde apparaat-app uitvoeren

In deel 1 van deze zelf studie stelt u een apparaat in om te simuleren met behulp van een IoT-apparaat. In deze sectie downloadt u de .NET-console-app die het apparaat simuleert dat apparaat-naar-Cloud-berichten verzendt naar een IoT-hub (ervan uitgaande dat u de app en resources in deel 1 niet al hebt gedownload).

Met deze toepassing worden berichten verzonden voor elk van de verschillende routerings methoden voor berichten. Er is ook een map in de down load die de volledige Azure Resource Manager sjabloon en het parameter bestand bevat, evenals de Azure CLI-en Power shell-scripts.

Als u de bestanden niet hebt gedownload uit de opslag plaats in deel 1 van deze zelf studie, kunt u deze nu downloaden vanuit [IOT Device simulatie](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Als u deze koppeling selecteert, wordt een opslag plaats met meerdere toepassingen gedownload. de oplossing die u zoekt, is IOT-hub/zelf studies/Routing/IoT_SimulatedDevice. SLN. 

Dubbel klik op het oplossings bestand (IoT_SimulatedDevice. SLN) om de code te openen in Visual Studio en open vervolgens Program.cs. Vervang `{your hub name}` door de hostnaam van de IoT Hub. De indeling van de hostnaam van de IoT Hub is **{iot-hub-name} .azure-devices.net**. Voor deze zelfstudie is de naam van de hubhost **ContosoTestHub.azure devices.net**. Vervang vervolgens `{your device key}` door de apparaatsleutel die u eerder hebt opgeslagen bij het instellen van het gesimuleerde apparaat. 

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

   Dit resulteert in de volgende-instructies: True. 

   * De routering naar de Service Bus-wachtrij werkt correct.
   * De logische app die het bericht uit de Service Bus-wachtrij ophaalt, werkt correct.
   * De connector van de logische app voor Outlook werkt goed. 

2. In de [Azure Portal](https://portal.azure.com)selecteert u **resource groepen** en selecteert u de resource groep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**. 

    Selecteer het opslag account, selecteer **containers**en selecteer vervolgens de container. In deze zelfstudie wordt gebruikgemaakt van **contosoresults**. U zou een map moeten zien, en u kunt inzoomen via de mappen totdat u een of meer bestanden ziet. Open een van deze bestanden; ze bevatten de vermeldingen die zijn doorgestuurd naar het opslagaccount. 

   ![De resultaat bestanden in de opslag](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Dit resulteert in de volgende instructie: True.

   * De routering naar de Service Bus-wachtrij werkt correct.

Nu de toepassing nog steeds wordt uitgevoerd, stelt u de Power BI visualisatie in om de berichten te zien die via de standaard routering worden verzonden.

## <a name="set-up-the-power-bi-visualizations"></a>De Power BI-visualisaties instellen

1. Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/)-account.

2. Ga naar **Werkruimten** en selecteer de werkruimte die u hebt ingesteld toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **Mijn werkruimte**. 

3. Selecteer **gegevens sets**. Als u geen gegevens sets hebt, wacht u een paar minuten en controleert u het opnieuw.

   U zou de vermelde gegevensset moeten zien die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. (Het kan 5-10 minuten duren voordat de gegevensset voor de eerste keer wordt weergegeven.)

4. Onder **acties**, selecteert u het eerste pictogram om een rapport te maken.

   ![Power BI werk ruimte met gemarkeerde acties en rapport pictogrammen](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Maak een lijndiagram om in realtime de temperatuur gedurende een bepaalde periode weer te geven.

   * Voeg op de pagina rapport maken een lijn diagram toe door het pictogram lijn diagram te selecteren.

     ![De visualisaties en velden](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * Klap in het deelvenster **Velden** de tabel uit die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt. In deze zelfstudie wordt gebruikgemaakt van **contosotable**.

   * Sleep **EventEnqueuedUtcTime** naar **As** in het deelvenster **Visualisaties**.

   * Sleep **Temperatuur** naar **Waarden**.

   Er wordt een lijndiagram gemaakt. De x-as geeft de datum en tijd in UTC-tijdzone aan. De y-as geeft de temperatuur van de sensor aan.

6. Maak een ander lijndiagram om in realtime de vochtigheid gedurende een bepaalde periode weer te geven. Als u de tweede grafiek wilt instellen, volgt u hetzelfde proces voor de eerste grafiek en plaatst u **EventEnqueuedUtcTime** op de x-as (**as**) en **vochtigheid** op de y-as (**waarden**).

   ![Het uiteindelijke Power BI rapport met de twee grafieken](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Selecteer **Opslaan** om het rapport op te slaan en voer een naam in voor het rapport als u hierom wordt gevraagd.

U zou gegevens moeten kunnen zien in beide diagrammen. Dit resulteert in de volgende-instructies:

   * De routering naar het standaardeindpunt werkt correct.
   * De Azure Stream Analytics-taak stroomt correct.
   * De Power BI-visualisatie is juist ingesteld.

U kunt de grafieken vernieuwen om de meest recente gegevens te zien door de knop Vernieuwen boven aan het Power BI venster te selecteren. 

## <a name="clean-up-resources"></a>Resources opschonen 

Als u alle Azure-resources die u hebt gemaakt, wilt verwijderen uit beide delen van deze zelf studie, verwijdert u de resource groep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT Hub, de Service Bus-naamruimte en -wachtrij, de logische app, het opslagaccount en de resourcegroep zelf verwijderd. U kunt ook de Power BI resources verwijderen en de e-mail berichten wissen die tijdens de zelf studie zijn verzonden.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Resources in de Power BI-visualisatie opschonen

Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/)-account. Ga naar uw werkruimte. In deze zelfstudie wordt gebruikgemaakt van **Mijn werkruimte**. Als u de Power BI visualisatie wilt verwijderen, gaat u naar gegevens sets en selecteert u het prullenbak pictogram om de gegevensset te verwijderen. In deze zelfstudie wordt gebruikgemaakt van **contosodataset**. Wanneer u de gegevensset verwijdert, wordt het rapport ook verwijderd.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>De Azure CLI gebruiken om resources op te schonen

U kunt de resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` is ingesteld op **ContosoResources** terug aan het begin van deze zelf studie.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Power shell gebruiken om resources op te schonen

U kunt de resourcegroep verwijderen met de opdracht [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` is ingesteld op **ContosoResources** terug aan het begin van deze zelf studie.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Test berichten opschonen

Het is ook mogelijk dat u het aantal e-mails in uw postvak in dat is gegenereerd via de logische app wilt verwijderen terwijl de Device-toepassing werd uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie met twee deel woorden hebt u geleerd hoe u bericht routering kunt gebruiken om IoT Hub berichten naar verschillende bestemmingen te sturen door de volgende taken uit te voeren.  

**Deel I: resources maken, bericht routering instellen**
> [!div class="checklist"]
> * Maak de resources--een IoT-hub, een opslag account, een Service Bus wachtrij en een gesimuleerd apparaat.
> * Configureer de eind punten en bericht routes in IoT Hub voor het opslag account en de Service Bus wachtrij.

**Deel II: berichten verzenden naar de hub, gerouteerde resultaten weer geven**
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
