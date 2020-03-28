---
title: Zelfstudie - Werken met Azure-opslagwachtrijen - Azure Storage
description: Een zelfstudie over het gebruik van de Azure Queue-service om wachtrijen te maken en berichten in te voegen, op te halen en te verwijderen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75968198"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Zelfstudie: Werken met Azure-opslagwachtrijen

Azure Queue-opslag implementeert wachtrijen op basis van de cloud om communicatie tussen onderdelen van een gedistribueerde toepassing mogelijk te maken. Elke wachtrij houdt een lijst bij met berichten die kunnen worden toegevoegd door een afzendercomponent en verwerkt door een ontvangercomponent. Met een wachtrij kan uw toepassing direct schalen om aan de vraag te voldoen. In dit artikel worden de basisstappen weergegeven voor het werken met een Azure-opslagwachtrij.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Azure-opslagaccount maken
> - De app maken
> - Ondersteuning voor asynchrone code toevoegen
> - Een wachtrij maken
> - Berichten invoegen in een wachtrij
> - Wachtrijberichten dewachtrij
> - Een lege wachtrij verwijderen
> - Controleren op opdrachtregelargumenten
> - De app bouwen en uitvoeren

## <a name="prerequisites"></a>Vereisten

- Ontvang uw gratis exemplaar van het cross-platform [Visual Studio Code](https://code.visualstudio.com/download) editor.
- Download en installeer de [.NET Core SDK](https://dotnet.microsoft.com/download).
- Als u geen huidig Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

Maak eerst een Azure-opslagaccount aan. Zie [snel](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) aan de slag voor een stapsgewijze handleiding voor het maken van een opslagaccount.

## <a name="create-the-app"></a>De app maken

Maak een .NET Core-toepassing met de naam **QueueApp**. Voor de eenvoud, deze app zal zowel verzenden en ontvangen berichten via de wachtrij.

1. Gebruik de `dotnet new` opdracht om in een consolevenster (zoals CMD, PowerShell of Azure CLI) een nieuwe console-app te maken met de naam **QueueApp.** Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Schakel over naar de nieuw gemaakte **QueueApp-map** en bouw de app om te controleren of alles in orde is.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   U ziet resultaten die vergelijkbaar zijn met het volgende:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Ondersteuning voor asynchrone code toevoegen

Omdat de app cloudbronnen gebruikt, wordt de code asynchroon uitgevoerd. Echter, C # **async** en **wachten** waren niet geldig zoekwoorden in **de belangrijkste** methoden tot C # 7.1. U eenvoudig overschakelen naar die compiler via een vlag in het **csproj-bestand.**

1. Typ `code .` visual studiocode in de huidige map in de opdrachtregel in de opdrachtregel. Houd het opdrachtregelvenster open. Er zullen later meer opdrachten worden uitgevoerd. Als u wordt gevraagd C#-elementen toe te voegen die nodig zijn om te bouwen en te debuggen, klikt u op de knop **Ja.**

2. Open het bestand **QueueApp.csproj** in de editor.

3. Toevoegen `<LangVersion>7.1</LangVersion>` aan de eerste **PropertyGroup** in het buildbestand. Zorg ervoor dat u alleen de **LangVersion-tag** toevoegt, omdat uw **TargetFramework** mogelijk anders is, afhankelijk van welke versie van .NET u hebt geïnstalleerd.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Sla het bestand **QueueApp.csproj** op.

5. Open het **Program.cs** bronbestand en werk de **hoofdmethode** bij om asynchroon uit te voeren. Vervang **de legewaarde** door een **retourwaarde voor async-taken.**

   ```csharp
   static async Task Main(string[] args)
   ```

6. Sla het **Program.cs** bestand op.

## <a name="create-a-queue"></a>Een wachtrij maken

1. Installeer de pakketten **Microsoft.Azure.Storage.Common** en **Microsoft.Azure.Storage.Queue** met de `dotnet add package` opdracht voor het project. Voer de volgende dotnetopdrachten uit vanuit de projectmap in het consolevenster.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Voeg boven aan het **Program.cs** bestand direct na `using System;` de instructie de volgende naamruimten toe. Deze app gebruikt typen uit deze naamruimten om verbinding te maken met Azure Storage en te werken met wachtrijen.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Sla het **Program.cs** bestand op.

### <a name="get-your-connection-string"></a>Verbindingsreeks ophalen

De clientbibliotheek maakt gebruik van een verbindingsreeks om uw verbinding te maken. Uw verbindingstekenreeks is beschikbaar in het gedeelte **Instellingen** van uw opslagaccount in de Azure-portal.

1. Meld u in uw webbrowser aan bij de [Azure-portal.](https://portal.azure.com/)

2. Ga in Azure Portal naar uw opslagaccount.

3. Selecteer **Toegangstoetsen**.

4. Klik **op** de knop Kopiëren rechts van het veld **Verbindingstekenreeks.**

![Verbindingsreeks](media/storage-tutorial-queues/get-connection-string.png)

De verbindingsreeks heeft de volgende indeling:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>De verbindingstekenreeks toevoegen aan de app

Voeg de verbindingstekenreeks toe aan de app zodat deze toegang heeft tot het opslagaccount.

1. Schakel terug naar Visual Studio Code.

2. Voeg **Program** in de klasse `private const string connectionString =` Programma een lid toe om de verbindingstekenreeks vast te houden.

3. Plak na het gelijkteken de tekenreekswaarde die u eerder in uw Azure-portal hebt gekopieerd. De **waarde van de verbindingString** is uniek voor uw account.

4. Verwijder de code "Hello World" van **Main**. Uw code moet er hetzelfde uitzien als de volgende, maar met uw unieke verbindingstekenreekswaarde.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. **Hoofd bijwerken** om een **CloudQueue-object** te maken, dat later wordt doorgegeven aan de methoden verzenden en ontvangen.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Sla het bestand op.

## <a name="insert-messages-into-the-queue"></a>Berichten invoegen in de wachtrij

Maak een nieuwe methode om een bericht in de wachtrij te verzenden. Voeg de volgende methode toe aan de klasse **Programma.** Met deze methode wordt een wachtrijverwijzing gemaakt en wordt een nieuwe wachtrij gemaakt als deze nog niet bestaat door [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync)aan te roepen. Vervolgens wordt het bericht toegevoegd aan de wachtrij door [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync)te bellen.

1. Voeg de volgende **Methode SendMessageAsync** toe aan de klasse **Programma.**

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Sla het bestand op.

Een bericht moet in een formaat zijn dat kan worden opgenomen in een XML-aanvraag met UTF-8-codering, en mag maximaal 64 KB groot zijn. Als een bericht binaire gegevens bevat, raden we u aan het bericht te coderen.

Standaard is de maximale time-to-live voor een bericht ingesteld op 7 dagen. U een positief nummer opgeven voor het bericht time-to-live. Als u een bericht wilt `Timespan.FromSeconds(-1)` toevoegen dat niet verloopt, gebruikt u in uw gesprek **aan AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Wachtrijberichten dewachtrij

Maak een nieuwe methode genaamd **ReceiveMessageAsync**. Met deze methode ontvangt u een bericht uit de wachtrij door [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync)te bellen. Zodra het bericht is ontvangen, is het belangrijk om het uit de wachtrij te verwijderen, zodat het niet meer dan één keer wordt verwerkt. Nadat het bericht is ontvangen, verwijdert u het uit de wachtrij door [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync)aan te roepen.

1. Voeg de volgende **ReceiveMessageAsync-methode** toe aan de klasse **Programma.**

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Sla het bestand op.

## <a name="delete-an-empty-queue"></a>Een lege wachtrij verwijderen

Het is een aanbevolen praktijk aan het einde van een project om te bepalen of u nog de resources nodig hebt die u hebt gemaakt. Resources die actief blijven, kunnen u geld kosten. Als de wachtrij bestaat maar leeg is, vraagt u de gebruiker of hij deze wil verwijderen.

1. Vouw de **methode ReceiveMessageAsync** uit met een prompt om de lege wachtrij te verwijderen.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Sla het bestand op.

## <a name="check-for-command-line-arguments"></a>Controleren op opdrachtregelargumenten

Als er argumenten voor opdrachtregels zijn doorgegeven aan de app, gaat u ervan uit dat dit een bericht is dat aan de wachtrij moet worden toegevoegd. Sluit de argumenten samen om een tekenreeks te maken. Voeg deze tekenreeks toe aan de berichtenwachtrij door de methode **SendMessageAsync** aan te roepen die we eerder hebben toegevoegd.

Als er geen opdrachtregelargumenten zijn, voert u een bewerking voor ophalen uit. Roep de methode **ReceiveMessageAsync** op om het eerste bericht in de wachtrij op te halen.

Wacht tot slot op gebruikersinvoer voordat u deze afsluit door **Console.ReadLine**te bellen.

1. Vouw de **hoofdmethode** uit om te controleren op argumenten voor de opdrachtregel en wacht op gebruikersinvoer.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Sla het bestand op.

## <a name="complete-code"></a>Volledige code

Hier is de volledige code vermelding voor dit project.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Voer in de opdrachtregel in de projectmap de volgende dotnetopdracht uit om het project te bouwen.

   ```console
   dotnet build
   ```

2. Nadat het project is voltooid, voert u de volgende opdracht uit om het eerste bericht aan de wachtrij toe te voegen.

   ```console
   dotnet run First queue message
   ```

Deze uitvoer ziet er ongeveer als volgt uit:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Voer de app uit zonder argumenten voor opdrachtregelom het eerste bericht in de wachtrij te ontvangen en te verwijderen.

   ```console
   dotnet run
   ```

4. Blijf de app uitvoeren totdat alle berichten zijn verwijderd. Als u de wachtrij nog een keer uitvoert, ontvangt u een bericht dat de wachtrij leeg is en een prompt om de wachtrij te verwijderen.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

1. Een wachtrij maken
2. Berichten uit een wachtrij toevoegen en verwijderen
3. Een Azure-opslagwachtrij verwijderen

Bekijk de Azure-wachtrijen snelstart voor meer informatie.

> [!div class="nextstepaction"]
> [Wachtrijen snelaan de dag](storage-quickstart-queues-portal.md)
