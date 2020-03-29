---
title: Logische apps aanroepen met Azure Functions
description: Azure-functies maken die logische apps aanroepen of activeren door naar Azure Service Bus te luisteren
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428708"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Logische apps aanbellen of activeren met Azure-functies en Azure Service Bus

U [Azure-functies](../azure-functions/functions-overview.md) gebruiken om een logische app te activeren wanneer u een langlopende listener of taak moet implementeren. U bijvoorbeeld een Azure-functie maken die meeluistert in een [Azure Service Bus-wachtrij](../service-bus-messaging/service-bus-messaging-overview.md) en onmiddellijk een logische app afvuurt als een trigger.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een Azure Service Bus-naamruimte. Als u geen naamruimte hebt, [maakt u eerst uw naamruimte.](../service-bus-messaging/service-bus-create-namespace-portal.md)

* Een Azure-functie-app, een container voor Azure-functies. Als u geen functie-app hebt, [maakt u eerst de functie-app](../azure-functions/functions-create-first-azure-function.md)en controleert u .NET als de runtime-stack.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Logische app maken

Voor dit scenario hebt u een functie waarop elke logische app wordt uitgevoerd die u wilt activeren. Maak eerst een logische app die begint met een HTTP-aanvraagtrigger. De functie roept dat eindpunt op wanneer een wachtrijbericht wordt ontvangen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en maak een lege logische app.

   Als u nieuw bent in logische apps, controleert u [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Typ `http request` in het zoekvak. Selecteer in de lijst triggers de **trigger Wanneer een HTTP-aanvraag is ontvangen.**

   ![Trigger selecteren](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Met de trigger Van Aanvraag u optioneel een JSON-schema invoeren dat u met het wachtrijbericht wilt gebruiken. Json-schema's helpen de Logic App Designer inzicht te krijgen in de structuur van de invoergegevens en maken de uitvoer eenvoudiger voor u om te gebruiken in uw workflow.

1. Als u een schema wilt opgeven, voert u het schema in het **json-schema van aanvraagschema** in, bijvoorbeeld:

   ![Json-schema opgeven](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Als u geen schema hebt, maar wel een monsterpayload in JSON-indeling hebt, u een schema genereren van die payload.

   1. Selecteer in de trigger Van aanvraag de optie **Voorbeeldpayload gebruiken om schema te genereren**.

   1. Voer **onder Een monster JSON-payload in of plak**deze, voer uw monsterpayload in en selecteer **Gereed**.

      ![Voer monsterpayload in](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Deze payload van het monster genereert dit schema dat in de trigger wordt weergegeven:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Voeg alle andere acties toe die u wilt uitvoeren nadat u het wachtrijbericht hebt ontvangen.

   U bijvoorbeeld een e-mail verzenden met de Office 365 Outlook-connector.

1. Sla uw logische app op, die de callback-URL voor de trigger genereert in deze logische app. Later gebruikt u deze terugroep-URL in de code voor de trigger van Azure Service Bus Queue.

   De url van de terugroepfunctie wordt weergegeven in de eigenschap **HTTP POST URL.**

   ![Gegenereerde callback-URL voor trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Azure maken, functie

Maak vervolgens de functie die fungeert als trigger en luistert naar de wachtrij.

1. Open en vouw uw functie-app uit in de Azure-portal en vouw deze uit, als deze nog niet is geopend. 

1. Vouw **functies**uit onder de naam van de functie-app . Selecteer **in** het deelvenster Functies de optie **Nieuwe functie**.

   !['Functies' uitvouwen en 'Nieuwe functie' selecteren](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Selecteer deze sjabloon op basis van de vraag of u een nieuwe functie-app hebt gemaakt waarbij u .NET als runtime-stack hebt geselecteerd of een bestaande functie-app gebruikt.

   * Selecteer deze sjabloon voor nieuwe functie-apps: **Trigger servicebuswachtrij**

     ![Sjabloon selecteren voor nieuwe functie-app](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Selecteer deze sjabloon voor een bestaande functie-app: **Trigger servicebuswachtrij - C#**

     ![Sjabloon selecteren voor bestaande functie-app](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Geef in het **triggervenster Azure Service Bus Queue** een naam voor de trigger op en stel de **ServiceBus-verbinding** in voor de wachtrij, die de Azure Service Bus SDK-listener `OnMessageReceive()` gebruikt, en selecteer **Maken**.

1. Schrijf een basisfunctie om het eerder gemaakte eindpunt van de logische app aan te roepen door het wachtrijbericht als trigger te gebruiken. Voordat u uw functie schrijft, bekijkt u de volgende overwegingen:

   * In dit `application/json` voorbeeld wordt het inhoudstype van het bericht gebruikt, maar u dit type indien nodig wijzigen.
   
   * Vanwege mogelijke gelijktijdig draaiende functies, hoge volumes of zware belastingen, moet u `using` voorkomen dat de [HTTPClient-klasse](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) met de instructie wordt geinstantt en httpclient-exemplaren per aanvraag rechtstreeks worden gemaakt. Zie [HttpClientFactory gebruiken om veerkrachtige HTTP-aanvragen te implementeren voor](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)meer informatie.
   
   * Hergebruik indien mogelijk de instantie van HTTP-clients. Zie [Verbindingen beheren in Azure-functies voor](../azure-functions/manage-connections.md)meer informatie.

   In dit [ `Task.Run` ](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) voorbeeld wordt de methode in [asynchrone](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) modus gebruikt. Zie [Asynchrone programmering met async en wacht op asynchrone programmering](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Als u de functie wilt testen, voegt u een wachtrijbericht toe met een hulpprogramma zoals de [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer)

   De logische app wordt onmiddellijk geactiveerd nadat de functie het bericht heeft ontvangen.

## <a name="next-steps"></a>Volgende stappen

* [Werkstromen aanbellen, activeren of nesten met HTTP-eindpunten](../logic-apps/logic-apps-http-endpoint.md)
