---
title: Aan de slag met Azure-wachtrijopslag met Visual Studio (ASP.NET)
description: Aan de slag met Azure-wachtrijopslag in een ASP.NET-project in Visual Studio nadat u verbinding hebt gemaakt met een opslagaccount met Visual Studio Connected Services
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980758"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Aan de slag met Azure queue storage en Visual Studio Connected Services (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht

Azure-wachtrijopslag biedt cloudberichten tussen toepassingsonderdelen. Bij het ontwerpen van schaalbare toepassingen worden toepassingsonderdelen vaak ontkoppeld, zodat ze onafhankelijk van elkaar kunnen worden geschaald. Queue Storage biedt asynchrone uitwisseling van berichten voor communicatie tussen toepassingsonderdelen, of deze nu worden uitgevoerd in de cloud, op een desktopcomputer, op een on-premises server of op een mobiel apparaat. Queue Storage biedt daarnaast ondersteuning voor het beheren van asynchrone taken en het samenstellen van proceswerkstromen.

In deze zelfstudie ziet u hoe u ASP.NET code voor een aantal veelvoorkomende scenario's schrijven met azure-opslagentiteiten voor wachtrijopslag. Deze scenario's omvatten veelvoorkomende taken zoals het maken van een Azure-wachtrij en het toevoegen, wijzigen, lezen en verwijderen van wachtrijberichten.

## <a name="prerequisites"></a>Vereisten

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure-opslagaccount](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Een MVC-controller maken 

1. Klik in de **Oplossingsverkenner**met de rechtermuisknop op **Controllers**en selecteer in het contextmenu **>Controller toevoegen**.

    ![Een controller toevoegen aan een ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Selecteer **mvc 5-controller toevoegen in**het dialoogvenster **Steiger toevoegen** - Leeg en selecteer **Toevoegen**.

    ![MVC-controllertype opgeven](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Geef in het dialoogvenster **Controller toevoegen** de naam van de controller *QueuesController*en selecteer **Toevoegen**.

    ![De MVC-controller een naam geven](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Voeg de *using* volgende richtlijnen `QueuesController.cs` toe aan het bestand:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Een wachtrij maken

In de volgende stappen wordt uitgelegd hoe u een wachtrij maakt:

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid [Het instellen van de ontwikkelomgeving](#set-up-the-development-environment). 

1. Open het `QueuesController.cs`-bestand. 

1. Voeg een methode met de naam **CreateQueue** toe die een **ActionResult**retourneert.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Download binnen de methode **CreateQueue** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudQueueClient-object** ophalen vertegenwoordigt een wachtrijserviceclient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Download een **CloudQueue-object** dat een verwijzing naar de gewenste wachtrijnaam vertegenwoordigt. Met de methode **CloudQueueClient.GetQueueReference** wordt geen aanvraag ingediend tegen wachtrijopslag. De verwijzing wordt geretourneerd of de wachtrij al dan niet bestaat. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Bel de methode **CloudQueue.CreateIfNotExists** om de wachtrij te maken als deze nog niet bestaat. De methode **CloudQueue.CreateIfNotExist retourneert** **true** als de wachtrij niet bestaat en is gemaakt. Anders wordt **false** geretourneerd.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Werk de **ViewBag** bij met de naam van de wachtrij.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Wachtrijen**en selecteer in het contextmenu **>weergave toevoegen**.

1. Typ **CreateQueue** voor de weergavenaam in het dialoogvenster **Weergave toevoegen** en selecteer **Toevoegen**.

1. Open `CreateQueue.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **Wachtrij maken** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Wachtrij maken](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Zoals eerder vermeld, retourneert de methode **CloudQueue.CreateIfNotExists** **alleen true** wanneer de wachtrij niet bestaat en wordt gemaakt. Als u de app uitvoert wanneer de wachtrij bestaat, retourneert de methode **dus false**. Als u de app meerdere keren wilt uitvoeren, moet u de wachtrij verwijderen voordat de app opnieuw wordt uitgevoerd. Het verwijderen van de wachtrij kan via de methode **CloudQueue.Delete.** U de wachtrij ook verwijderen via de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) of de [Microsoft Azure Storage Explorer.](../vs-azure-tools-storage-manage-with-storage-explorer.md)  

## <a name="add-a-message-to-a-queue"></a>Een bericht toevoegen aan een wachtrij

Nadat u een wachtrij hebt [gemaakt,](#create-a-queue)u berichten toevoegen aan die wachtrij. In deze sectie u een bericht toevoegen aan een *wachtrijtestwachtrij.* 

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid [Het instellen van de ontwikkelomgeving](#set-up-the-development-environment). 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode met de naam **AddMessage** toe die een **ActionResult**retourneert .

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Download binnen de **methode AddMessage** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient-object** ophalen vertegenwoordigt een wachtrijserviceclient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Download een **CloudQueueContainer-object** dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Maak het object **CloudQueueMessage** dat het bericht vertegenwoordigt dat u aan de wachtrij wilt toevoegen. Een **CloudQueueMessage-object** kan worden gemaakt op basis van een tekenreeks (in UTF-8-indeling) of een bytearray.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Bel de methode **CloudQueue.AddMessage** om het bericht toe te voegen aan de wachtrij.

    ```csharp
    queue.AddMessage(message);
    ```

1. Maak en stel een paar **ViewBag-eigenschappen** in voor weergave in de weergave.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Wachtrijen**en selecteer in het contextmenu **>weergave toevoegen**.

1. Voer in het dialoogvenster **Weergave toevoegen** **Invoegingsboodschap** in voor de weergavenaam en selecteer **Toevoegen**.

1. Open `AddMessage.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **Bericht toevoegen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Bericht toevoegen](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

De twee secties - [Lees een bericht uit een wachtrij zonder het te verwijderen](#read-a-message-from-a-queue-without-removing-it) en Lees en verwijder een bericht uit een [wachtrij](#read-and-remove-a-message-from-a-queue) - illustreren hoe u berichten uit een wachtrij lezen.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Een bericht uit een wachtrij lezen zonder het te verwijderen

In deze sectie ziet u hoe u een bericht in de wachtrij bekijkt (lees het eerste bericht zonder het te verwijderen).  

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid [Het instellen van de ontwikkelomgeving](#set-up-the-development-environment). 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode met de naam **PeekMessage** toe die een **ActionResult**retourneert .

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Download binnen de **methode PeekMessage** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient-object** ophalen vertegenwoordigt een wachtrijserviceclient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Download een **CloudQueueContainer-object** dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Bel de methode **CloudQueue.PeekMessage** om het eerste bericht in de wachtrij te lezen zonder het uit de wachtrij te verwijderen. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Werk de **ViewBag** bij met twee waarden: de naam van de wachtrij en het bericht dat is gelezen. Het object **CloudQueueMessage** onthult twee eigenschappen voor het verkrijgen van de waarde van het object: **CloudQueueMessage.AsBytes** en **CloudQueueMessage.AsString**. **AsString** (gebruikt in dit voorbeeld) retourneert een tekenreeks, terwijl **AsBytes** een bytearray retourneert.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Wachtrijen**en selecteer in het contextmenu **>weergave toevoegen**.

1. Typ **PeekMessage** voor de weergavenaam in het dialoogvenster **Weergave toevoegen** en selecteer **Toevoegen**.

1. Open `PeekMessage.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

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

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **Peek-bericht** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Peek-bericht](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Een bericht uit een wachtrij lezen en verwijderen

In deze sectie leert u hoe u een bericht uit een wachtrij lezen en verwijderen.   

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid [Het instellen van de ontwikkelomgeving](#set-up-the-development-environment). 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode met de naam **ReadMessage** toe die een **ActionResult retourneert**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Download binnen de **methode ReadMessage** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient-object** ophalen vertegenwoordigt een wachtrijserviceclient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Download een **CloudQueueContainer-object** dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Bel de methode **CloudQueue.GetMessage** om het eerste bericht in de wachtrij te lezen. De **methode CloudQueue.GetMessage** maakt het bericht gedurende 30 seconden (standaard) onzichtbaar voor andere codeleesberichten, zodat geen enkele andere code het bericht kan wijzigen of verwijderen terwijl u het verwerkt. Als u de tijd wilt wijzigen waarop het bericht onzichtbaar is, wijzigt u de parameter **visibilityTimeout** die wordt doorgegeven aan de methode **CloudQueue.GetMessage.**

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Roep de methode **CloudQueueMessage.Delete** aan om het bericht uit de wachtrij te verwijderen.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Werk de **ViewBag** bij met het verwijderde bericht en de naam van de wachtrij.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Wachtrijen**en selecteer in het contextmenu **>weergave toevoegen**.

1. Typ In het dialoogvenster **Weergave toevoegen** **Leesboodschap** voor de weergavenaam en selecteer **Toevoegen**.

1. Open `ReadMessage.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

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

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **Het bericht Lezen/verwijderen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Bericht lezen en verwijderen](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

In deze sectie ziet u hoe u de wachtrijlengte (aantal berichten) krijgen. 

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid [Het instellen van de ontwikkelomgeving](#set-up-the-development-environment). 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode met de naam **GetQueueLength** toe die een **ActionResult retourneert.**

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Download binnen de **methode ReadMessage** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient-object** ophalen vertegenwoordigt een wachtrijserviceclient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Download een **CloudQueueContainer-object** dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Roep de methode **CloudQueue.FetchAttributes** aan om de kenmerken van de wachtrij (inclusief de lengte ervan) op te halen. 

    ```csharp
    queue.FetchAttributes();
    ```

1. Toegang tot de eigenschap **CloudQueue.ApproximateMessageCount** om de lengte van de wachtrij te krijgen.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Werk de **ViewBag** bij met de naam van de wachtrij en de lengte ervan.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Wachtrijen**en selecteer in het contextmenu **>weergave toevoegen**.

1. Typ In het dialoogvenster **Weergave toevoegen** **GetQueuelength** voor de weergavenaam en selecteer **Toevoegen**.

1. Open `GetQueueLengthMessage.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **Wachtrijlengte opdoen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Wachtrijlengte opdoen](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Een wachtrij verwijderen
In deze sectie ziet u hoe u een wachtrij verwijdert. 

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid [Het instellen van de ontwikkelomgeving](#set-up-the-development-environment). 

1. Open het `QueuesController.cs`-bestand.

1. Voeg een methode met de naam **DeleteQueue** toe die een **ActionResult retourneert.**

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Download binnen de methode **DeleteQueue** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Een **CloudQueueClient-object** ophalen vertegenwoordigt een wachtrijserviceclient.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Download een **CloudQueueContainer-object** dat een verwijzing naar de wachtrij vertegenwoordigt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Roep de methode **CloudQueue.Delete** aan om de wachtrij te verwijderen die wordt weergegeven door het object **CloudQueue.**

    ```csharp
    queue.Delete();
    ```

1. Werk de **ViewBag** bij met de naam van de wachtrij en de lengte ervan.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Wachtrijen**en selecteer in het contextmenu **>weergave toevoegen**.

1. Typ **DeleteQueue** voor de weergavenaam in het dialoogvenster **Weergave toevoegen** en selecteer **Toevoegen**.

1. Open `DeleteQueue.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Voer de toepassing uit en selecteer **Wachtrijlengte opdoen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Wachtrij verwijderen](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Volgende stappen
Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.

  * [Aan de slag met Azure blob storage en Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Aan de slag met Azure-tabelopslag en Visual Studio Connected Services (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
