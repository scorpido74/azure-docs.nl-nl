---
title: Aan de slag met Azure Queue Storage met behulp van Visual Studio (ASP.NET)
description: Aan de slag met Azure Queue Storage in een ASP.NET-project in Visual Studio nadat u verbinding hebt gemaakt met een opslag account met behulp van Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f229661ca78dc75adbc0b49073dc6f0feaf2ba22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75980758"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Aan de slag met Azure Queue Storage en Visual Studio Connected Services (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht

Azure Queue-opslag biedt Cloud berichten tussen toepassings onderdelen. Bij het ontwerpen van schaalbare toepassingen worden toepassingsonderdelen vaak ontkoppeld, zodat ze onafhankelijk van elkaar kunnen worden geschaald. Queue Storage biedt asynchrone uitwisseling van berichten voor communicatie tussen toepassingsonderdelen, of deze nu worden uitgevoerd in de cloud, op een desktopcomputer, op een on-premises server of op een mobiel apparaat. Queue Storage biedt daarnaast ondersteuning voor het beheren van asynchrone taken en het samenstellen van proceswerkstromen.

In deze zelf studie leert u hoe u ASP.NET-code kunt schrijven voor een aantal algemene scenario's die gebruikmaken van Azure queue storage-entiteiten. Deze scenario's bevatten algemene taken, zoals het maken van een Azure-wachtrij, het toevoegen, wijzigen, lezen en verwijderen van wachtrij berichten.

## <a name="prerequisites"></a>Vereisten

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure-opslag account](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Een MVC-controller maken 

1. In de **Solution Explorer**, klikt u met de rechter muisknop op **controllers**en selecteert u in het context menu de optie **add->controller**.

    ![Een controller toevoegen aan een ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Selecteer in het dialoog venster **steigers toevoegen** de optie **MVC 5 controller-empty**en selecteer **toevoegen**.

    ![MVC-controller type opgeven](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Geef in het dialoog venster **controller toevoegen** de naam van de controller *QueuesController*en selecteer **toevoegen**.

    ![De MVC-controller een naam](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Voeg het volgende toe *met behulp van* de instructies in het `QueuesController.cs` bestand:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Een wachtrij maken

De volgende stappen laten zien hoe u een wachtrij maakt:

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen. 

1. Open het `QueuesController.cs`-bestand. 

1. Voeg een methode toe met de naam **CreateQueue** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. In de methode **CreateQueue** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag Connection String en het opslag account op te halen uit de configuratie * &lt;* van de Azure-service: (Wijzig de naam van het opslag account>in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudQueueClient** -object ophalen vertegenwoordigt een Queue service-client.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Haal een **CloudQueue** -object op dat een verwijzing naar de gewenste wachtrij naam vertegenwoordigt. De methode **CloudQueueClient. GetQueueReference** maakt geen aanvraag voor de wachtrij opslag. De verwijzing wordt geretourneerd, ongeacht of de wachtrij bestaat. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Roep de methode **CloudQueue. CreateIfNotExists** aan om de wachtrij te maken als deze nog niet bestaat. De methode **CloudQueue. CreateIfNotExists** retourneert **True** als de wachtrij niet bestaat en is gemaakt. Anders wordt **False** geretourneerd.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Werk de **ViewBag** bij met de naam van de wachtrij.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. In de **Solution Explorer**vouwt u de map **views** uit, klikt u met de rechter muisknop op **wacht rijen**en selecteert u in het context menu de optie **toevoegen >weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **CreateQueue** in als weergave naam en selecteer **toevoegen**.

1. Open `CreateQueue.cshtml`en wijzig de waarde zodat deze eruitziet als in het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Vouw in de **Solution Explorer**de **weer gaven->gedeelde** map uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **wachtrij maken** om de resultaten te bekijken die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Wachtrij maken](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Zoals eerder vermeld, retourneert de methode **CloudQueue. CreateIfNotExists** alleen **True** als de wachtrij niet bestaat en is gemaakt. Als u de app uitvoert wanneer de wachtrij bestaat, retourneert de methode daarom **False**. Als u de app meerdere keren wilt uitvoeren, moet u de wachtrij verwijderen voordat u de app opnieuw uitvoert. Het verwijderen van de wachtrij kan worden uitgevoerd via de methode **CloudQueue. Delete** . U kunt de wachtrij ook verwijderen met behulp van de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) of de [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Een bericht aan een wachtrij toevoegen

Wanneer u [een wachtrij hebt gemaakt](#create-a-queue), kunt u berichten toevoegen aan die wachtrij. In deze sectie wordt uitgelegd hoe u een bericht aan een *test wachtrij*voor een wachtrij toevoegt. 

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen. 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode toe met de naam **AddMessage** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. In de methode **AddMessage** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag Connection String en het opslag account op te halen uit de configuratie * &lt;* van de Azure-service: (Wijzig de naam van het opslag account>in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient** -object ophalen vertegenwoordigt een Queue service-client.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Een **CloudQueueContainer** -object ophalen dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Maak het **CloudQueueMessage** -object dat het bericht vertegenwoordigt dat u wilt toevoegen aan de wachtrij. U kunt een **CloudQueueMessage** -object maken op basis van een teken reeks (in UTF-8-indeling) of een byte matrix.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Roep de methode **CloudQueue. AddMessage** aan om het bericht aan de wachtrij toe te voegen.

    ```csharp
    queue.AddMessage(message);
    ```

1. Maak en stel een aantal **ViewBag** -eigenschappen in voor weer gave in de weer gave.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. In de **Solution Explorer**vouwt u de map **views** uit, klikt u met de rechter muisknop op **wacht rijen**en selecteert u in het context menu de optie **toevoegen >weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **AddMessage** in als weergave naam en selecteer **toevoegen**.

1. Open `AddMessage.cshtml`en wijzig de waarde zodat deze eruitziet als in het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Vouw in de **Solution Explorer**de **weer gaven->gedeelde** map uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **bericht toevoegen** om resultaten te zien die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Bericht toevoegen](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

De twee secties: [een bericht uit een wachtrij lezen zonder dit te verwijderen](#read-a-message-from-a-queue-without-removing-it) en [een bericht uit een wachtrij te lezen en verwijderen](#read-and-remove-a-message-from-a-queue) : Hiermee wordt uitgelegd hoe u berichten uit een wachtrij kunt lezen.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Een bericht uit een wachtrij lezen zonder het te verwijderen

In deze sectie wordt beschreven hoe u een bericht in de wachtrij bekijkt (Lees het eerste bericht zonder het te verwijderen).  

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen. 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode toe met de naam **PeekMessage** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. In de methode **PeekMessage** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag Connection String en het opslag account op te halen uit de configuratie * &lt;* van de Azure-service: (Wijzig de naam van het opslag account>in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient** -object ophalen vertegenwoordigt een Queue service-client.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Een **CloudQueueContainer** -object ophalen dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Roep de methode **CloudQueue. PeekMessage** aan om het eerste bericht in de wachtrij te lezen zonder het uit de wachtrij te verwijderen. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Werk de **ViewBag** bij met twee waarden: de wachtrij naam en het bericht dat is gelezen. Het object **CloudQueueMessage** beschrijft twee eigenschappen voor het ophalen van de waarde van het object: **CloudQueueMessage. AsBytes** en **CloudQueueMessage. AsString**. **AsString** (gebruikt in dit voor beeld) retourneert een teken reeks, terwijl **AsBytes** een byte matrix retourneert.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. In de **Solution Explorer**vouwt u de map **views** uit, klikt u met de rechter muisknop op **wacht rijen**en selecteert u in het context menu de optie **toevoegen >weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **PeekMessage** in als weergave naam en selecteer **toevoegen**.

1. Open `PeekMessage.cshtml`en wijzig de waarde zodat deze eruitziet als in het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. Vouw in de **Solution Explorer**de **weer gaven->gedeelde** map uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **bericht bekijken** om de resultaten te bekijken die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Bericht bekijken](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Een bericht uit een wachtrij lezen en verwijderen

In deze sectie leert u hoe u een bericht uit een wachtrij leest en verwijdert.   

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen. 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode toe met de naam **ReadMessage** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. In de methode **ReadMessage** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag Connection String en het opslag account op te halen uit de configuratie * &lt;* van de Azure-service: (Wijzig de naam van het opslag account>in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient** -object ophalen vertegenwoordigt een Queue service-client.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Een **CloudQueueContainer** -object ophalen dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Roep de methode **CloudQueue. GetMessage** aan om het eerste bericht in de wachtrij te lezen. De methode **CloudQueue. GetMessage** maakt het bericht 30 seconden (standaard) onzichtbaar voor andere code die berichten leest, zodat geen enkele andere code het bericht kan wijzigen of verwijderen tijdens de verwerking. Als u de hoeveelheid tijd wilt wijzigen die het bericht onzichtbaar is, wijzigt u de **visibilityTimeout** -para meter die wordt door gegeven aan de methode **CloudQueue. GetMessage** .

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Roep de methode **CloudQueueMessage. Delete** aan om het bericht uit de wachtrij te verwijderen.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Werk de **ViewBag** bij met het bericht dat is verwijderd en de naam van de wachtrij.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. In de **Solution Explorer**vouwt u de map **views** uit, klikt u met de rechter muisknop op **wacht rijen**en selecteert u in het context menu de optie **toevoegen >weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **ReadMessage** in als weergave naam en selecteer **toevoegen**.

1. Open `ReadMessage.cshtml`en wijzig de waarde zodat deze eruitziet als in het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. Vouw in de **Solution Explorer**de **weer gaven->gedeelde** map uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **bericht lezen/verwijderen** om de resultaten te bekijken die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Bericht lezen en verwijderen](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

In deze sectie ziet u hoe u de lengte van de wachtrij kunt ophalen (aantal berichten). 

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen. 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode toe met de naam **GetQueueLength** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. In de methode **ReadMessage** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag Connection String en het opslag account op te halen uit de configuratie * &lt;* van de Azure-service: (Wijzig de naam van het opslag account>in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient** -object ophalen vertegenwoordigt een Queue service-client.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Een **CloudQueueContainer** -object ophalen dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Roep de methode **CloudQueue. FetchAttributes** aan om de kenmerken van de wachtrij op te halen (inclusief de lengte). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Open de eigenschap **CloudQueue. ApproximateMessageCount** om de lengte van de wachtrij op te halen.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Werk de **ViewBag** bij met de naam van de wachtrij en de lengte.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. In de **Solution Explorer**vouwt u de map **views** uit, klikt u met de rechter muisknop op **wacht rijen**en selecteert u in het context menu de optie **toevoegen >weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **GetQueueLength** in als weergave naam en selecteer **toevoegen**.

1. Open `GetQueueLengthMessage.cshtml`en wijzig de waarde zodat deze eruitziet als in het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Vouw in de **Solution Explorer**de **weer gaven->gedeelde** map uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **wachtrij lengte ophalen** om resultaten te zien die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Wachtrij lengte ophalen](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Een wachtrij verwijderen
In deze sectie wordt beschreven hoe u een wachtrij verwijdert. 

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen. 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode toe met de naam **Delete Queue** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. In de methode **Delete Queue** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag Connection String en het opslag account op te halen uit de configuratie * &lt;* van de Azure-service: (Wijzig de naam van het opslag account>in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient** -object ophalen vertegenwoordigt een Queue service-client.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Een **CloudQueueContainer** -object ophalen dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Roep de **CloudQueue. Delete** -methode aan om de wachtrij te verwijderen die door het **CloudQueue** -object wordt vertegenwoordigd.

    ```csharp
    queue.Delete();
    ```

1. Werk de **ViewBag** bij met de naam van de wachtrij en de lengte.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. In de **Solution Explorer**vouwt u de map **views** uit, klikt u met de rechter muisknop op **wacht rijen**en selecteert u in het context menu de optie **toevoegen >weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **Delete Queue** in als weergave naam en selecteer **toevoegen**.

1. Open `DeleteQueue.cshtml`en wijzig de waarde zodat deze eruitziet als in het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Vouw in de **Solution Explorer**de **weer gaven->gedeelde** map uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **wachtrij lengte ophalen** om resultaten te zien die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Wachtrij verwijderen](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Volgende stappen
Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.

  * [Aan de slag met Azure Blob Storage en Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Aan de slag met Azure Table Storage en Visual Studio Connected Services (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
