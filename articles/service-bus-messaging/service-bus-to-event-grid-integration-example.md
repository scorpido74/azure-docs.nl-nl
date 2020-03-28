---
title: 'Zelfstudie: voorbeelden van azure servicebus naar gebeurtenisrasterintegratie'
description: 'Zelfstudie: In dit artikel vindt u voorbeelden van servicebusberichten en integratie van eventgrid.'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: fef325b67c38eda09a05dac9d74bd5b97df164cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067772"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Zelfstudie: Reageren op Azure Service Bus-gebeurtenissen die zijn ontvangen via Azure Event Grid met Azure-functies en Azure Logic Apps
In deze zelfstudie leert u hoe u reageren op Azure Service Bus-gebeurtenissen die via Azure Event Grid worden ontvangen met Azure-functies en Azure Logic Apps. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Service Bus-naamruimte maken
> * Een voorbeeldtoepassing voorbereiden om berichten te verzenden
> * Een testfunctie instellen op Azure
> * Functie en naamruimte met elkaar verbinden via Event Grid
> * Berichten verzenden naar het onderwerp ServiceBus
> * Berichten ontvangen met behulp van Azure Functions
> * Berichten ontvangen met behulp van Logic Apps

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- [Visual Studio 2017 update 3 (versie 15.3, 26730.01)](https://www.visualstudio.com/vs) of later.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versie 2.0 of later.

## <a name="create-a-service-bus-namespace"></a>Een Service Bus-naamruimte maken
Volg instructies in deze zelfstudie: [Snel beginnen: gebruik de Azure-portal om een ServiceBus-onderwerp en abonnementen op het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om de volgende taken uit te voeren:

- Maak een **premium** servicebusnaamruimte. 
- Haal de verbindingstekenreeks. 
- Een servicebusonderwerp maken.
- Maak twee abonnementen op het onderwerp. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Een voorbeeldtoepassing voorbereiden om berichten te verzenden
U kunt een willekeurige methode gebruiken om een bericht naar uw Service Bus-onderwerp te verzenden. De voorbeeldcode aan het einde van deze procedure gaat ervan uit dat u Visual Studio 2017 gebruikt.

1. Kloon de [GitHub azure-service-bus repository](https://github.com/Azure/azure-service-bus/) (opslagplaats van GitHub-azure-service-bus).
2. Ga in Visual Studio naar de map *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* en open het bestand *SBEventGridIntegration.sln*.
3. Ga naar het project **MessageSender** en selecteer **Program.cs**.
4. Vul de naam van het ServiceBus-onderwerp en de verbindingstekenreeks in die u uit de vorige stap hebt gekregen:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Bouw en voer het programma uit om testberichten naar het onderwerp ServiceBus te sturen. 

## <a name="set-up-a-test-function-on-azure"></a>Een testfunctie instellen op Azure 
Voordat u het hele scenario doorloopt, stelt u ten minste een kleine testfunctie in, die u gebruiken om de gebeurtenissen die stromen te debuggen en te observeren. Volg instructies in de [eerste functie maken in het azure-portalartikel](../azure-functions/functions-create-first-azure-function.md) om de volgende taken uit te voeren: 

1. Maak een functie-app.
2. Maak een HTTP-geactiveerde functie. 

Voer vervolgens de volgende stappen uit: 


# <a name="azure-functions-v2"></a>[Azure-functies V2](#tab/v2)

1. Vouw **Functies** uit in de structuurweergave en selecteer de functie. Vervang de code voor de functie door de volgende code: 

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
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
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
2. Selecteer **Opslaan en uitvoeren**.

    ![Functie-app-uitvoer](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Selecteer **Functie-URL ophalen** en noteer de URL. 

    ![Functie-URL ophalen](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1"></a>[Azure-functies V1](#tab/v1)

1. Configureer de functie om **v1-versie** te gebruiken: 
    1. Selecteer de functie-app in de structuurweergave en selecteer **Instellingen van de functie-app**. 

        ![Instellingen voor functie-app]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Selecteer **~1** voor **Runtime-versie**. 
2. Vouw **Functies** uit in de structuurweergave en selecteer de functie. Vervang de code voor de functie door de volgende code: 

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

    ![Functie-app-uitvoer](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Selecteer **Functie-URL ophalen** en noteer de URL. 

    ![Functie-URL ophalen](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Functie en naamruimte met elkaar verbinden via Event Grid
In deze sectie bindt u de functie en de naamruimte van de Servicebus aan elkaar met behulp van de Azure-portal. 

Voer de volgende stappen uit om een Azure Event Grid-abonnement te maken:

1. Ga in de Azure-portal naar uw naamruimte en selecteer vervolgens in het linkerdeelvenster **Gebeurtenissen**. Het venster met de naamruimte wordt geopend, waarbij twee Event Grid-abonnementen in het rechterdeelvenster worden weergegeven. 
    
    ![Servicebus - evenementenpagina](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Selecteer **+ Gebeurtenisabonnement** op de werkbalk. 
3. Ga op de pagina **Gebeurtenisabonnement maken** de volgende stappen uit:
    1. Voer een **naam** in voor het abonnement. 
    2. Selecteer **Webhaak** voor **eindpunttype**. 

        ![Servicebus - Abonnement op eventgrid](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Kies **Een eindpunt selecteren,** de functie-URL plakken en selecteer **Selectie bevestigen**. 

        ![Functie - selecteer het eindpunt](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Ga naar het tabblad **Filters,** voer de naam in van het **eerste abonnement op** het onderwerp Servicebus dat u eerder hebt gemaakt en selecteer vervolgens de knop **Maken.** 

        ![Filter gebeurtenisabonnement](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Controleer of u het gebeurtenisabonnement in de lijst ziet.

    ![Gebeurtenisabonnement in de lijst](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Berichten verzenden naar het onderwerp ServiceBus
1. Voer de toepassing .NET C# uit, waarmee berichten worden verzonden naar het onderwerp Servicebus. 

    ![Uitvoer van console-apps](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Vouw op de pagina voor uw Azure-functie-app **Functies**uit, vouw uw **functie**uit en selecteer **Monitor**. 

    ![Monitor, functie](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Berichten ontvangen met behulp van Azure Functions
In de vorige sectie hebt u een eenvoudig test- en foutopsporingsscenario gezien, en gecontroleerd of de gebeurtenissen stromen. 

In deze sectie leert u hoe u berichten ontvangt en verwerkt nadat u een gebeurtenis hebt ontvangen.

### <a name="publish-a-function-from-visual-studio"></a>Een functie publiceren vanuit Visual Studio
1. Selecteer **ReceiveMessagesOnEvent.cs** in het **SBEventGridIntegration-project** in dezelfde Visual Studio-oplossing **(SBEventGridIntegration).** 
2. Voer de verbindingstekenreeks servicebus in de volgende code in:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Download het **publicatieprofiel** voor de functie:
    1. Selecteer uw functie-app. 
    2. Selecteer het tabblad **Overzicht** als deze nog niet is geselecteerd. 
    3. Selecteer **Publicatieprofiel op** de werkbalk. 

        ![Publicatieprofiel voor de functie opvragen](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Sla het bestand op in de map van uw project. 
4. Klik vervolgens in Visual Studio met de rechtermuisknop op **SBEventGridIntegration** selecteer **Publish**. 
5. Selecteer **Start** op de **pagina Publiceren.** 
6. Selecteer op de **pagina Een publicatiedoel** kiezen de volgende stappen de optie **Profiel importeren**. 

    ![Visual Studio - knop Profiel importeren](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Selecteer het **eerder gedownloade publicatieprofielbestand.** 
8. Selecteer **Publiceren** op de **pagina Publiceren.** 

    ![Visual Studio - Publiceren](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Controleer of u de nieuwe Azure-functie **ReceiveMessagesOnEvent**ziet. Vernieuw de pagina indien nodig. 

    ![Controleren of de nieuwe functie is gemaakt](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Download de URL naar de nieuwe functie en noteer deze. 

### <a name="event-grid-subscription"></a>Abonnement op gebeurtenisraster

1. Verwijder het bestaande abonnement op het gebeurtenisraster:
    1. Selecteer **Gebeurtenissen** op de pagina **Naamruimte servicebus** in het linkermenu. 
    2. Selecteer het bestaande gebeurtenisabonnement. 
    3. Selecteer **Verwijderen**op de pagina **Abonnement voor gebeurtenissen** .
2. Volg instructies in de [sectie De functie en de naamruimte](#connect-the-function-and-namespace-via-event-grid) verbinden via de sectie Gebeurtenisraster om een eventgrid-abonnement te maken met behulp van de nieuwe functie-URL.
3. Volg de instructie in de sectie [Berichten verzenden naar de sectie Servicebus](#send-messages-to-the-service-bus-topic) om berichten naar het onderwerp te verzenden en de functie te controleren. 

## <a name="receive-messages-by-using-logic-apps"></a>Berichten ontvangen met behulp van Logic Apps
Verbind een logische app met Azure Service Bus en Azure Event Grid door de volgende stappen te volgen:

1. Maak een logische app in de Azure-portal.
    1. Selecteer **+ Een resource maken,** selecteer **Integratie**en selecteer vervolgens **Logic App**. 
    2. Voer op de **pagina Logica-app - Maken** een **naam** in voor de logische app.
    3. Selecteer uw **Azure-abonnement**. 
    4. Selecteer **Bestaand gebruiken** voor de **resourcegroep**en selecteer de resourcegroep die u hebt gebruikt voor andere resources (zoals azure-functie, naamruimte van servicebus) die u eerder hebt gemaakt. 
    5. Selecteer de **locatie** voor de logische app. 
    6. Selecteer **Maken** om de logische app te maken. 
2. Selecteer op de pagina **Logic Apps Designer** de optie Blank Logic **App** onder **Sjablonen**. 
3. Ga op de ontwerper de volgende stappen uit:
    1. Zoeken naar **gebeurtenisraster**. 
    2. Selecteer **Wanneer een resourcegebeurtenis plaatsvindt (voorbeeld) - Azure Event Grid**. 

        ![Logic Apps Designer - selecteer Gebeurtenisrastertrigger](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecteer **Aanmelden,** voer uw Azure-referenties in en selecteer **Toegang toestaan**. 
5. Ga op de pagina **Wanneer een resourcegebeurtenis plaatsvindt,** de volgende stappen uit:
    1. Selecteer uw Azure-abonnement. 
    2. Selecteer **Microsoft.ServiceBus.Namespaces**voor **Resourcetype**. 
    3. Selecteer **voor Resourcenaam**de naamruimte van de Servicebus. 
    4. Selecteer **Nieuwe parameter toevoegen**en selecteer **Achtervoegselfilter**. 
    5. Voer **voor achtervoegselfilter**de naam in van uw tweede abonnement op het onderwerp Service Bus. 
        ![Logic Apps Designer - gebeurtenis configureren](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecteer **+ Nieuwe stap** in de ontwerper en volg de volgende stappen:
    1. Zoeken naar **servicebus**.
    2. Selecteer **Servicebus** in de lijst. 
    3. Selecteer voor **Berichten ontvangen** in de lijst **Acties.** 
    4. Selecteer **Berichten ophalen via een onderwerpabonnement (peek-lock)**. 

        ![Logic Apps Designer - berichtenactie ontvangen](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Voer een **naam in voor de verbinding**. Bijvoorbeeld: **Ontvang berichten uit het onderwerpabonnement**en selecteer de naamruimte servicebus. 

        ![Logic Apps Designer - selecteer de naamruimte servicebus](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. selecteer **RootManageSharedAccessKey**.

        ![Logic Apps Designer - selecteer de gedeelde toegangssleutel](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Selecteer **Maken**. 
    8. Selecteer uw onderwerp en abonnement. 
    
        ![Logic Apps Designer - selecteer uw servicebusonderwerp en -abonnement](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecteer **+ Nieuwe stap**en doe de volgende stappen: 
    1. Selecteer **Servicebus**.
    2. Selecteer **Het bericht voltooien in een onderwerpabonnement** in de lijst met acties. 
    3. Selecteer het **onderwerp**ServiceBus .
    4. Selecteer het tweede **abonnement op** het onderwerp.
    5. Selecteer **Token vergrendelen** in de dynamische **inhoud**voor Het token vergrendelen van **het bericht**. 

        ![Logic Apps Designer - selecteer uw servicebusonderwerp en -abonnement](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Selecteer **Opslaan** op de werkbalk in de Logic Apps Designer om de logische app op te slaan. 
9. Volg de instructie in de sectie [Berichten verzenden naar de sectie Servicebus](#send-messages-to-the-service-bus-topic) om berichten naar het onderwerp te verzenden. 
10. Ga naar de **overzichtspagina** van uw logica-app. U ziet de logische app wordt uitgevoerd in de **geschiedenis Uitvoeringen** voor de verzonden berichten.

    ![Logic Apps Designer - logische app wordt uitgevoerd](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Meer informatie over [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Meer informatie over de [Logic Apps-functie van Azure App Service](https://docs.microsoft.com/azure/logic-apps/).
* Meer informatie over [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).


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
