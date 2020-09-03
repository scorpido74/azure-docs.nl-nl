---
title: 'Zelfstudie: Voorbeelden integratie Azure Service Bus naar Event Grid'
description: 'Zelfstudie: In dit artikel vindt u voorbeelden van de integratie van Service Bus-berichten met Event Grid.'
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 6f64c8155724a1306f5074a9164bcf97391dfbf6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000342"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Zelfstudie: Reageren op Azure Service Bus-gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Functions en Azure Logic Apps
In deze zelfstudie leert u hoe u kunt reageren op Azure Service Bus-gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Functions en Azure Logic Apps. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Service Bus-naamruimte maken
> * Een voorbeeldtoepassing voorbereiden om berichten te verzenden
> * Berichten kunt verzenden naar Service Bus-onderwerp
> * Berichten ontvangen met behulp van Logic Apps
> * Een test kunt instellen op Azure
> * Functie en naamruimte met elkaar verbinden via Event Grid
> * Berichten ontvangen met behulp van Azure Functions

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- [Visual Studio 2017 update 3 (versie 15.3, 26730.01)](https://www.visualstudio.com/vs) of hoger.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versie 2.0 of later.

## <a name="create-a-service-bus-namespace"></a>Een Service Bus-naamruimte maken
Volg de instructies in deze zelfstudie: [Snelstart: De Azure-portal gebruiken om een Service Bus-onderwerp en abonnementen te maken voor het onderwerp ](service-bus-quickstart-topics-subscriptions-portal.md)om de volgende taken uit te voeren:

- Een **premium** Service Bus-naamruimte maken. 
- De verbindingsreeks ophalen. 
- Een Service Bus-onderwerp maken.
- Twee abonnementen maken op het onderwerp. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Een voorbeeldtoepassing voorbereiden om berichten te verzenden
U kunt een willekeurige methode gebruiken om een bericht naar uw Service Bus-onderwerp te verzenden. In de voorbeeldcode aan het einde van deze procedure wordt ervan uitgegaan dat u van Visual Studio 2017 gebruikmaakt.

1. Kloon de [GitHub azure-service-bus repository](https://github.com/Azure/azure-service-bus/) (opslagplaats van GitHub-azure-service-bus).
2. Ga in Visual Studio naar de map *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* en open het bestand *SBEventGridIntegration.sln*.
3. Ga naar het project **MessageSender** en selecteer **Program.cs**.
4. Vul de naam van het Service Bus-onderwerp in en de verbindingstekenreeks die u hebt ontvangen van de vorige stap:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Werk de `numberOfMessages` waarde bij naar **5**. 
5. Bouw en voer het programma uit om testberichten te verzenden naar het Service Bus-onderwerp. 

## <a name="receive-messages-by-using-logic-apps"></a>Berichten ontvangen met behulp van Logic Apps
Verbind aan de hand van de volgende stappen een logische app met Azure Service Bus en Azure Event Grid:

1. Maak een logische app in de Azure Portal.
    1. Selecteer **+ Een resource maken**, selecteer **Integratie** en selecteer **Logische app**. 
    2. Voer op de pagina **Logische app - Maken** een **naam** in voor de logische app.
    3. Selecteer uw Azure-**abonnement**. 
    4. Selecteer **Bestaande gebruiken** voor de **Resourcegroep** en selecteer de resourcegroep die u hebt gebruikt voor andere resources (zoals Azure-functie, Service Bus-naamruimte) die u eerder hebt gemaakt. 
    5. Selecteer de **Locatie** voor de logische app. 
    6. Selecteer **Maken** om de logische app te maken. 
2. Op de pagina **Ontwerper van logische apps** selecteert u **Lege logische app** onder **Sjablonen**. 
3. Voer in de ontwerper de volgende stappen uit:
    1. Zoek naar **Event Grid**. 
    2. Selecteer **Wanneer een resourcegebeurtenis optreedt - Azure Event Grid**. 

        ![Ontwerper van logische apps: Event Grid-trigger selecteren](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecteer **Aanmelden**, voer uw Azure-referenties in en selecteer **Toegang toestaan**. 
5. Voer de volgende stappen uit op de pagina **Wanneer een resourcegebeurtenis zich voordoet**:
    1. Selecteer uw Azure-abonnement. 
    2. Voor **Resourcetype** selecteert u **Microsoft.ServiceBus.Namespaces**. 
    3. Voor **Resourcenaam** selecteert u uw Service Bus-naamruimte. 
    4. Selecteer **Nieuwe parameter toevoegen** en selecteer **Achtervoegselfilter**. 
    5. Voer voor **Achtervoegselfilter** de naam in van het tweede abonnement voor Service Bus-onderwerp. 
        ![Ontwerper van logische apps: gebeurtenis configureren](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecteer **+ Nieuwe stap** in de ontwerper en voer de volgende stappen uit:
    1. Zoek naar **Service Bus**.
    2. Selecteer **Service Bus** in de lijst. 
    3. Selecteer voor **Berichten ophalen** in de lijst **Acties**. 
    4. Selecteer **Berichten van een onderwerpabonnement ontvangen (kort weergeven)** . 

        ![Ontwerper van logische apps: de actie berichten ophalen](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Geef een **naam voor de verbinding op**. Bijvoorbeeld: **Ontvang berichten van het onderwerp-abonnement** en selecteer de Service Bus-naamruimte. 

        ![Ontwerper van logische apps: de Service Bus-naamruimte selecteren](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selecteer **RootManageSharedAccessKey** en selecteer vervolgens **Maken**.

        ![Ontwerper van logische apps: de gedeelde toegangssleutel selecteren](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Selecteer uw **onderwerp** en **abonnement**. 
    
        ![Ontwerper van logische apps: uw Service Bus-onderwerp en -abonnement selecteren](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecteer **+ Nieuwe stap** en voer de volgende stappen uit: 
    1. Selecteer **Service Bus**.
    2. Selecteer **Het bericht in een onderwerp met een abonnement voltooien** in de lijst met acties. 
    3. Selecteer uw Service Bus-**onderwerp**.
    4. Selecteer de tweede **abonnement** op het onderwerp.
    5. Selecteer voor **Vergrendelingstoken van het bericht** de optie **Vergrendelingstoken** van de **Dynamische inhoud**. 

        ![Ontwerper van logische apps: uw Service Bus-onderwerp en -abonnement selecteren](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Selecteer **Opslaan** op de werkbalk van de Ontwerper voor logische apps om de logische app op te slaan. 
9. Volg de instructies in de sectie [Berichten verzenden naar het Service Bus-onderwerp](#send-messages-to-the-service-bus-topic) om berichten naar het onderwerp te verzenden. 
10. Ga naar de pagina **Overzicht** van uw logische app. U ziet de uitvoeringen van de logische app in de **Geschiedenis van uitvoeringen** voor de verzonden berichten.

    ![Ontwerper van logische apps - uitvoeringen van logische app](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="set-up-a-test-function-on-azure"></a>Een test kunt instellen op Azure 
Voordat u het hele scenario doorwerkt, dient u in elk geval een kleine testfunctie in te stellen. Deze kunt u gebruiken om te zien welke gebeurtenissen er worden gestroomd en om de fouten erin op te sporen. Volg de instructies in het artikel [Uw eerste functie maken in de Azure Portal](../azure-functions/functions-create-first-azure-function.md) om de volgende taken uit te voeren: 

1. Een functie-app maken.
2. Een door HTTP geactiveerde functie maken. 

Voer vervolgens de volgende stappen uit: 


# <a name="azure-functions-v2"></a>[Azure Functions V2](#tab/v2)

1. Vouw **Functies** in de structuurweergave uit en selecteer de functie. Vervang de code voor de functie door de volgende code: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                log.LogInformation("Validating the subscription");            
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation($"Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }    
    ```
2. Selecteer **Opslaan** op de werkbalk om de code voor de functie op te slaan.

    ![Functiecode opslaan](./media/service-bus-to-event-grid-integration-example/save-function-code.png)
3. Selecteer op de werkbalk **Testen/Uitvoeren** en volg de volgende stappen: 
    1. Voer de volgende JSON in de **hoofdtekst** in.

        ```json
        [{
          "id": "64ba80ae-9f8e-425f-8bd7-d88d2c0ba3e3",
          "topic": "/subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/spegridsbusrg/providers/Microsoft.ServiceBus/namespaces/spegridsbusns",
          "subject": "",
          "data": {
            "validationCode": "D7D825D4-BD04-4F73-BDE3-70666B149857",
            "validationUrl": "https://rp-eastus.eventgrid.azure.net:553/eventsubscriptions/spsbusegridsubscription/validate?id=D7D825D4-BD04-4F73-BDE3-70666B149857&t=2020-06-09T18:28:51.5724615Z&apiVersion=2020-04-01-preview&[Hidden Credential]"
          },
          "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
          "eventTime": "2020-06-09T18:28:51.5724615Z",
          "metadataVersion": "1",
          "dataVersion": "2"
        }]
        ```    
    2. Klik op **Header toevoegen**en voeg een header toe met naam `aeg-event-type` en waarde `SubscriptionValidation`. 
    3. Selecteer **Uitvoeren**. 

        ![Testuitvoering](./media/service-bus-to-event-grid-integration-example/test-run-function.png)
    4. Bevestig dat u de retourstatuscode **OK** en de validatiecode in de hoofdtekst van de reactie ziet. Zie ook de informatie die door de functie is vastgelegd. 

        ![Testuitvoering - antwoord](./media/service-bus-to-event-grid-integration-example/test-function-response.png)        
3. Selecteer **Functie-URL ophalen** en noteer de URL. 

    ![Functie-URL ophalen](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Selecteer de knop **kopiëren** naast de URL-tekst.    
    ![Functie-URL kopiëren](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

# <a name="azure-functions-v1"></a>[Azure Functions V1](#tab/v1)

1. De functie voor het gebruik van **V1**-versie configureren: 
    1. Selecteer uw functie-app in de structuurweergave en selecteer **Instellingen functie-app**. 
    2. Selecteer **~1** voor **Runtime-versie**. 
2. Vouw **Functies** in de structuurweergave uit en selecteer de functie. Vervang de code voor de functie door de volgende code: 

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Selecteer **Opslaan en uitvoeren**.

    ![Uitvoer van de functie-app](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Selecteer **Functie-URL ophalen** op de werkbalk. 

    ![Functie-URL ophalen](./media/service-bus-to-event-grid-integration-example/get-function-url.png)
5. Selecteer de knop **kopiëren** naast de URL-tekst.    
    ![Functie-URL kopiëren](./media/service-bus-to-event-grid-integration-example/get-function-url-copy.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Functie en naamruimte met elkaar verbinden via Event Grid
In deze sectie koppelt u de functie en de Service Bus-naamruimte met behulp van de Azure Portal. 

Volg de volgende stappen als u een Azure Event Grid-abonnement wilt maken:

1. Ga in Azure Portal naar uw naamruimte en selecteer **Gebeurtenissen** in het linkerdeelvenster. Het venster met de naamruimte wordt geopend, waarbij twee Event Grid-abonnementen in het rechterdeelvenster worden weergegeven. 
    
    ![Service Bus - pagina gebeurtenissen](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Selecteer **+ Gebeurtenisabonnement** op de werkbalk. 
3. Voer op de pagina **Gebeurtenisabonnement maken** de volgende stappen uit:
    1. Voer een **naam** in voor het abonnement. 
    2. Voer een **naam** in voor het **systeemonderwerp**. Systeemonderwerpen zijn onderwerpen die zijn gemaakt voor Azure-resources, zoals Azure Storage-account en Azure Service Bus. Zie [Overzicht van systeemonderwerpen](../event-grid/system-topics.md) voor meer informatie over systeemonderwerpen.
    2. Selecteer **Webhook** voor het **Eindpunttype**. 

        ![Service Bus - Event Grid-abonnement](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Kies **Een eindpunt selecteren**, plak de functie-URL en selecteer vervolgens **Selectie bevestigen**. 

        ![Functie: het eindpunt selecteren](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Open het tabblad **Filters** en voer de volgende taken uit:
        1. Selecteer **Filteren van onderwerpen inschakelen**.
        2. Voer de naam in van het **eerste abonnement** op het Service Bus-onderwerp dat u eerder hebt gemaakt.
        3. Selecteer de knop **Create** (Maken). 

            ![Filter Gebeurtenisabonnement](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Ga naar het tabblad **Gebeurtenisabonnementen** van de pagina **Gebeurtenissen** en bevestig dat u het gebeurtenisabonnement in de lijst ziet.

    ![Gebeurtenisabonnement in de lijst](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Berichten kunt verzenden naar Service Bus-onderwerp
1. Voer de .NET C#-toepassing uit, waarmee berichten naar het Service Bus-onderwerp worden verzonden. 

    ![Uitvoer console-app](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Ga op de pagina voor uw Azure-functie-app naar het tabblad **Monitor** op het tabblad **Code en testen**. U zou een vermelding moeten zien voor elk bericht dat is gepost in het Microsoft Azure Service Bus-onderwerp. Als u deze niet ziet, vernieuwt u de pagina nadat u een paar minuten hebt gewacht. 

    ![Functie bewaken](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

    U kunt ook het tabblad **Logboeken** op de pagina **Monitor** gebruiken om de logboekgegevens te bekijken wanneer de berichten worden verzonden. Er kan wat vertraging optreden, dus wacht een paar minuten om de gelogde berichten te kunnen zien. 

## <a name="receive-messages-by-using-azure-functions"></a>Berichten ontvangen met behulp van Azure Functions
In de vorige sectie hebt u een eenvoudig test- en foutopsporingsscenario gezien, en gecontroleerd of de gebeurtenissen stromen. 

In deze sectie leert u hoe u berichten ontvangt en verwerkt nadat u een gebeurtenis hebt ontvangen.

### <a name="publish-a-function-from-visual-studio"></a>Een functie publiceren vanuit Visual Studio
1. In dezelfde Visual Studio-oplossing (**SBEventGridIntegration**) die u hebt geopend, selecteert u **ReceiveMessagesOnEvent.cs** in het project **SBEventGridIntegration**. 
2. Voer uw Service Bus-verbindingsreeks in de volgende code in:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Download het **publicatieprofiel** voor de functie:
    1. Selecteer uw functie-app. 
    2. Selecteer het tabblad **Overzicht** als dit nog niet is geselecteerd. 
    3. Selecteer **Publicatieprofiel ophalen** op de werkbalk. 

        ![Publicatieprofiel ophalen voor de functie](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Sla het bestand op de map van uw project. 
4. Klik vervolgens in Visual Studio met de rechtermuisknop op **SBEventGridIntegration** selecteer **Publish**. 
5. Voer in het dialoogvenster **Publiceren** de volgende stappen uit: 
    1. Selecteer **Start** op de pagina **Publiceren** 
    2. Voor het **Doel** selecteert u **Profiel importeren**. 
    3. Selecteer **Next**. 

        ![Visual Studio - knop Profiel importeren](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Selecteer het **publicatieprofielbestand** dat u eerder hebt gedownload en selecteer **Voltooien**.

    ![Het publicatieprofiel selecteren](./media/service-bus-to-event-grid-integration-example/select-publish-profile.png)
8. Selecteer **Publiceren** op de pagina **Publiceren**. 

    ![Visual Studio - Publiceren](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Controleer of u de nieuwe Azure-functie **ReceiveMessagesOnEvent** ziet. Vernieuw de pagina indien nodig. 

    ![Controleer of de nieuwe functie is gemaakt](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Haal de URL naar de nieuwe functie op en noteer deze. 

### <a name="event-grid-subscription"></a>Event Grid-abonnement

1. Het bestaande Event Grid-abonnement verwijderen:
    1. Selecteer in het linkermenu op de pagina **Service Bus-naamruimte** de optie **Gebeurtenissen**. 
    2. Schakel naar het tabblad **Gebeurtenisabonnementen**. 
    2. Selecteer het bestaande gebeurtenisabonnement. 

        ![Selecteer gebeurtenisabonnement](./media/service-bus-to-event-grid-integration-example/select-event-subscription.png)
    3. Selecteer op de pagina **Gebeurtenisabonnement** de optie **Verwijderen**. Selecteer **Ja** om het verwijderen te bevestigen. 
        ![Knop voor het verwijderen van een gebeurtenisabonnement](./media/service-bus-to-event-grid-integration-example/delete-subscription-button.png)
2. Volg de instructies in de sectie [De functie en naamruimte verbinden via Event Grid](#connect-the-function-and-namespace-via-event-grid) om een Event Grid-abonnement te maken met behulp van de nieuwe functie-URL.
3. Volg de instructies in de sectie [Berichten verzenden naar het Service Bus-onderwerp](#send-messages-to-the-service-bus-topic) om berichten naar het onderwerp te verzenden en de functie te bewaken. 


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Event Grid](../event-grid/index.yml).
* Meer informatie over [Azure Functions](../azure-functions/index.yml).
* Meer informatie over de [Logic Apps-functie van Azure App Service](../logic-apps/index.yml).
* Meer informatie over [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png