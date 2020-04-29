---
title: 'Zelf studie: Azure Service Bus voor voor beelden van Event Grid-integratie'
description: 'Zelf studie: in dit artikel vindt u voor beelden van de integratie van Service Bus Messa ging en Event Grid.'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067772"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Zelf studie: reageren op Azure Service Bus gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Functions en Azure Logic Apps
In deze zelf studie leert u hoe u kunt reageren op Azure Service Bus gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Functions en Azure Logic Apps. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Service Bus-naamruimte maken
> * Een voorbeeld toepassing voorbereiden om berichten te verzenden
> * Een test functie instellen op Azure
> * Functie en naamruimte met elkaar verbinden via Event Grid
> * Berichten verzenden naar het Service Bus onderwerp
> * Berichten ontvangen met behulp van Azure Functions
> * Berichten ontvangen met behulp van Logic Apps

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- [Visual Studio 2017 update 3 (versie 15.3, 26730.01)](https://www.visualstudio.com/vs) of later.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versie 2.0 of later.

## <a name="create-a-service-bus-namespace"></a>Een Service Bus-naamruimte maken
Volg de instructies in deze zelf studie: [Quick Start: gebruik de Azure Portal om een service bus onderwerp en abonnementen aan het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om de volgende taken uit te voeren:

- Maak een **Premium** -service bus naam ruimte. 
- Haal de connection string op. 
- Maak een Service Bus onderwerp.
- Maak twee abonnementen op het onderwerp. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Een voorbeeld toepassing voorbereiden om berichten te verzenden
U kunt een willekeurige methode gebruiken om een bericht naar uw Service Bus-onderwerp te verzenden. In de voorbeeld code aan het einde van deze procedure wordt ervan uitgegaan dat u Visual Studio 2017 gebruikt.

1. Kloon de [GitHub azure-service-bus repository](https://github.com/Azure/azure-service-bus/) (opslagplaats van GitHub-azure-service-bus).
2. Ga in Visual Studio naar de map *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* en open het bestand *SBEventGridIntegration.sln*.
3. Ga naar het project **MessageSender** en selecteer **Program.cs**.
4. Vul de naam van het Service Bus onderwerp in en de connection string die u hebt ontvangen van de vorige stap:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Bouw en voer het programma uit om test berichten te verzenden naar het Service Bus onderwerp. 

## <a name="set-up-a-test-function-on-azure"></a>Een test functie instellen op Azure 
Voordat u het volledige scenario doorloopt, stelt u ten minste een kleine test functie in, die u kunt gebruiken om fouten op te sporen en de gebeurtenissen te observeren die stromen. Volg de instructies in de [eerste functie maken in het Azure Portal](../azure-functions/functions-create-first-azure-function.md) artikel om de volgende taken uit te voeren: 

1. Een functie-app maken.
2. Een door HTTP geactiveerde functie maken. 

Voer vervolgens de volgende stappen uit: 


# <a name="azure-functions-v2"></a>[Azure Functions v2](#tab/v2)

1. Vouw **functies** in de structuur weergave uit en selecteer de functie. Vervang de code voor de functie door de volgende code: 

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

    ![Uitvoer functie-app](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Selecteer **functie-URL ophalen** en noteer de URL. 

    ![Functie-URL ophalen](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1"></a>[Azure Functions v1](#tab/v1)

1. De functie voor het gebruik van **v1** -versie configureren: 
    1. Selecteer de functie-app in de structuur weergave en selecteer **functie-app-instellingen**. 

        ![Instellingen voor functie-app]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Selecteer **~ 1** voor **runtime versie**. 
2. Vouw **functies** in de structuur weergave uit en selecteer de functie. Vervang de code voor de functie door de volgende code: 

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

    ![Uitvoer functie-app](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Selecteer **functie-URL ophalen** en noteer de URL. 

    ![Functie-URL ophalen](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Functie en naamruimte met elkaar verbinden via Event Grid
In deze sectie kunt u de functie en de naam ruimte Service Bus koppelen met behulp van de Azure Portal. 

Voer de volgende stappen uit om een Azure Event Grid-abonnement te maken:

1. Ga in het Azure Portal naar uw naam ruimte en selecteer vervolgens in het linkerdeel venster **gebeurtenissen**. Het venster met de naamruimte wordt geopend, waarbij twee Event Grid-abonnementen in het rechterdeelvenster worden weergegeven. 
    
    ![Service Bus-gebeurtenissen pagina](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Selecteer **+ gebeurtenis abonnement** op de werk balk. 
3. Voer op de pagina **gebeurtenis abonnement maken** de volgende stappen uit:
    1. Voer een **naam** in voor het abonnement. 
    2. Selecteer een **webhook** voor het **type eind punt**. 

        ![Service Bus-Event Grid-abonnement](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Kies **een eind punt selecteren**, plak de functie-URL en selecteer vervolgens **selectie bevestigen**. 

        ![Functie: Selecteer het eind punt](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Ga naar het tabblad **filters** , voer de naam in van het **eerste abonnement** op het service bus onderwerp dat u eerder hebt gemaakt, en selecteer vervolgens de knop **maken** . 

        ![Filter voor gebeurtenis abonnementen](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Controleer of het gebeurtenis abonnement in de lijst wordt weer geven.

    ![Gebeurtenis abonnement in de lijst](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Berichten verzenden naar het Service Bus onderwerp
1. Voer de .NET C#-toepassing uit, waarmee berichten naar het onderwerp Service Bus worden verzonden. 

    ![Console-app-uitvoer](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Vouw op de pagina voor uw Azure function-app **functies**uit, vouw uw **functie**uit en selecteer **monitor**. 

    ![Functie bewaken](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Berichten ontvangen met behulp van Azure Functions
In de vorige sectie hebt u een eenvoudig test- en foutopsporingsscenario gezien, en gecontroleerd of de gebeurtenissen stromen. 

In deze sectie leert u hoe u berichten ontvangt en verwerkt nadat u een gebeurtenis hebt ontvangen.

### <a name="publish-a-function-from-visual-studio"></a>Een functie publiceren vanuit Visual Studio
1. In dezelfde Visual Studio-oplossing (**SBEventGridIntegration**) die u hebt geopend, selecteert u **ReceiveMessagesOnEvent.cs** in het **SBEventGridIntegration** -project. 
2. Voer uw Service Bus in connection string in de volgende code:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Down load het **publicatie profiel** voor de functie:
    1. Selecteer uw functie-app. 
    2. Selecteer het tabblad **overzicht** als dit nog niet is geselecteerd. 
    3. Selecteer **publicatie profiel ophalen** op de werk balk. 

        ![Publicatie profiel ophalen voor de functie](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Sla het bestand op in de map van uw project. 
4. Klik vervolgens in Visual Studio met de rechtermuisknop op **SBEventGridIntegration** selecteer **Publish**. 
5. Selecteer **Start** op de pagina **publiceren** . 
6. Voer op de pagina **een publicatie doel** selecteren de volgende stappen uit, selecteer **profiel importeren**. 

    ![Knop Visual Studio-profiel importeren](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Selecteer het **publicatie profiel bestand** dat u eerder hebt gedownload. 
8. Selecteer **publiceren** op de pagina **publiceren** . 

    ![Visual Studio-publiceren](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Bevestig dat u de nieuwe Azure Function **ReceiveMessagesOnEvent**ziet. Vernieuw de pagina als dat nodig is. 

    ![Controleer of de nieuwe functie is gemaakt](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Haal de URL naar de nieuwe functie op en noteer deze. 

### <a name="event-grid-subscription"></a>Event Grid abonnement

1. Het bestaande Event Grid-abonnement verwijderen:
    1. Selecteer op de pagina **Service Bus naam ruimte** de optie **gebeurtenissen** in het menu links. 
    2. Selecteer het bestaande gebeurtenis abonnement. 
    3. Selecteer op de pagina **gebeurtenis abonnement** de optie **verwijderen**.
2. Volg de instructies in de sectie [verbinding maken met de functie en de naam ruimte via event grid](#connect-the-function-and-namespace-via-event-grid) om een event grid-abonnement te maken met behulp van de nieuwe functie-URL.
3. Volg de instructies in de sectie [berichten verzenden naar het onderwerp service bus](#send-messages-to-the-service-bus-topic) om berichten naar het onderwerp te verzenden en de functie te controleren. 

## <a name="receive-messages-by-using-logic-apps"></a>Berichten ontvangen met behulp van Logic Apps
Verbind een logische app met Azure Service Bus en Azure Event Grid door de volgende stappen uit te voeren:

1. Maak een logische app in de Azure Portal.
    1. Selecteer **+ een resource maken**, selecteer **integratie**en selecteer vervolgens **logische app**. 
    2. Voer op de pagina **logische app-maken** een **naam** in voor de logische app.
    3. Selecteer uw Azure- **abonnement**. 
    4. Selecteer **bestaande gebruiken** voor de **resource groep**en selecteer de resource groep die u hebt gebruikt voor andere resources (zoals Azure function, service bus naam ruimte) die u eerder hebt gemaakt. 
    5. Selecteer de **locatie** voor de logische app. 
    6. Selecteer **maken** om de logische app te maken. 
2. Selecteer op de pagina **Logic apps Designer** de optie **lege logische app** onder **sjablonen**. 
3. Voer de volgende stappen uit in de ontwerp functie:
    1. Zoeken naar **Event grid**. 
    2. Selecteren **Wanneer een bron gebeurtenis plaatsvindt (preview)-Azure Event grid**. 

        ![Logic Apps Designer: Selecteer Event Grid trigger](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecteer **Aanmelden**, voer uw Azure-referenties in en selecteer **toegang toestaan**. 
5. Voer de volgende stappen uit op de pagina **Wanneer een bron gebeurtenis zich voordoet** :
    1. Selecteer uw Azure-abonnement. 
    2. Selecteer voor **resource type**de optie **micro soft. ServiceBus. namespaces**. 
    3. Selecteer bij **resource naam**uw service bus naam ruimte. 
    4. Selecteer **nieuwe para meter toevoegen**en selecteer **achtervoegsel filter**. 
    5. Voer bij **achtervoegsel filter**de naam in van het tweede service bus-onderwerp-abonnement. 
        ![Logic Apps Designer: gebeurtenis configureren](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecteer **+ nieuwe stap** in de ontwerp functie en voer de volgende stappen uit:
    1. Zoeken naar **Service Bus**.
    2. Selecteer **Service Bus** in de lijst. 
    3. Selecteer voor **Get-berichten** in de lijst met **acties** . 
    4. Selecteer **berichten ophalen uit een onderwerp-abonnement (Peek-vergren delen)**. 

        ![Logic Apps Designer: de actie berichten ophalen](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Voer een **naam in voor de verbinding**. Bijvoorbeeld: **berichten van het onderwerp-abonnement ophalen**en de naam ruimte service bus selecteren. 

        ![Logic Apps Designer: Selecteer de Service Bus naam ruimte](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. selecteer **RootManageSharedAccessKey**.

        ![Logic Apps Designer: Selecteer de gedeelde toegangs sleutel](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Selecteer **Maken**. 
    8. Selecteer uw onderwerp en abonnement. 
    
        ![Logic Apps Designer: Selecteer uw Service Bus onderwerp en abonnement](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecteer **+ nieuwe stap**en voer de volgende stappen uit: 
    1. Selecteer **Service Bus**.
    2. Selecteer in de lijst met acties **het bericht in een onderwerp-abonnement volt ooien** . 
    3. Selecteer uw Service Bus **onderwerp**.
    4. Selecteer het tweede **abonnement** op het onderwerp.
    5. Selecteer **token** vergren delen van de **dynamische inhoud**voor **het lock-token van het bericht**. 

        ![Logic Apps Designer: Selecteer uw Service Bus onderwerp en abonnement](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Selecteer **Opslaan** in de werk balk van de Logic apps Designer om de logische app op te slaan. 
9. Volg de instructies in de sectie [berichten verzenden naar het onderwerp service bus](#send-messages-to-the-service-bus-topic) om berichten naar het onderwerp te verzenden. 
10. Ga naar de **overzichts** pagina van uw logische app. U ziet dat de logische app wordt uitgevoerd in de geschiedenis van de **uitvoering** van de verzonden berichten.

    ![Logic Apps Designer-Logic app-uitvoeringen](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
