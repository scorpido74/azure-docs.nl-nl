---
title: 'Zelf studie: werken met Azure Storage-wacht rijen-Azure Storage'
description: Een zelf studie over het gebruik van de Azure Queue-service voor het maken van wacht rijen en het invoegen, ophalen en verwijderen van berichten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968198"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Zelf studie: werken met Azure Storage-wacht rijen

Azure Queue-opslag implementeert Cloud wachtrijen om communicatie tussen onderdelen van een gedistribueerde toepassing mogelijk te maken. Elke wachtrij houdt een lijst bij van berichten die kunnen worden toegevoegd door een onderdeel van de afzender en worden verwerkt door een onderdeel van de ontvanger. Met een wachtrij kan uw toepassing onmiddellijk worden geschaald om aan de vraag te voldoen. In dit artikel worden de basis stappen beschreven voor het werken met een Azure Storage-wachtrij.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Azure-opslagaccount maken
> - De app maken
> - Ondersteuning voor asynchrone code toevoegen
> - Een wachtrij maken
> - Berichten in een wachtrij invoegen
> - Berichten uit de wachtrij verwijderen
> - Een lege wachtrij verwijderen
> - Controleren op opdracht regel argumenten
> - De app bouwen en uitvoeren

## <a name="prerequisites"></a>Vereisten

- Ontvang een gratis kopie van de multi platform [Visual Studio code](https://code.visualstudio.com/download) -editor.
- Down load en installeer de [.net core SDK](https://dotnet.microsoft.com/download).
- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

Maak eerst een Azure Storage-account. Zie de Snelstartgids [een opslag account maken](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) voor een stapsgewijze hand leiding voor het maken van een opslag account.

## <a name="create-the-app"></a>De app maken

Maak een .NET core-toepassing met de naam **QueueApp**. Voor eenvoud kan deze app berichten verzenden en ontvangen via de wachtrij.

1. In een console venster (zoals CMD, Power shell of Azure CLI) gebruikt u de opdracht `dotnet new` om een nieuwe console-app te maken met de naam **QueueApp**. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Ga naar de zojuist gemaakte map **QueueApp** en bouw de app om te controleren of alles goed is.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   De resultaten moeten er ongeveer als volgt uitzien:

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

Omdat de app gebruikmaakt van cloud resources, wordt de code asynchroon uitgevoerd. C#Het is echter wel **async** en **wacht** op ongeldige tref woorden in **hoofd** methoden tot C# 7,1. U kunt eenvoudig overschakelen naar die compiler via een vlag in het **csproj** -bestand.

1. Ga naar de opdracht regel in de projectmap en typ `code .` om Visual Studio code te openen in de huidige map. Houd het opdracht regel venster geopend. Er zijn meer opdrachten om later uit te voeren. Als u wordt gevraagd om assets C# toe te voegen die vereist zijn voor het maken en opsporen van fouten, klikt u op de knop **Ja** .

2. Open het bestand **QueueApp.csproj** in de editor.

3. Voeg `<LangVersion>7.1</LangVersion>` toe aan de eerste **PropertyGroup** in het build-bestand. Zorg ervoor dat u alleen de **LangVersion** -tag toevoegt als uw **TargetFramework** kan afwijken, afhankelijk van welke versie van .net u hebt geïnstalleerd.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Sla het bestand **QueueApp. csproj** op.

5. Open het bron bestand **Program.cs** en werk de methode **Main** bij om asynchroon te worden uitgevoerd. Vervangen **door** een **async-taak** retour waarde.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Sla bestand **Program.cs** op.

## <a name="create-a-queue"></a>Een wachtrij maken

1. Installeer de pakketten **micro soft. Azure. storage. common** en **micro soft. Azure. storage. Queue** in het project met de opdracht `dotnet add package`. Voer de volgende DotNet-opdrachten uit vanuit de projectmap in het console venster.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Voeg boven aan het **Program.cs** -bestand de volgende naam ruimten toe, direct na de `using System;`-instructie. Deze app gebruikt typen uit deze naam ruimten om verbinding te maken met Azure Storage en te werken met wacht rijen.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Sla bestand **Program.cs** op.

### <a name="get-your-connection-string"></a>Verbindingsreeks ophalen

De client bibliotheek gebruikt een connection string om uw verbinding tot stand te brengen. Uw connection string is beschikbaar in het gedeelte **instellingen** van uw opslag account in de Azure Portal.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/)in uw webbrowser.

2. Ga in Azure Portal naar uw opslagaccount.

3. Selecteer **toegangs sleutels**.

4. Klik op de knop **kopiëren** rechts van het veld **verbindings reeks** .

![Verbindingsreeks](media/storage-tutorial-queues/get-connection-string.png)

De verbindingsreeks heeft de volgende indeling:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>De connection string toevoegen aan de app

Voeg de connection string toe aan de app, zodat deze toegang heeft tot het opslag account.

1. Ga terug naar Visual Studio code.

2. Voeg in de klasse **Program** een `private const string connectionString =` lid toe om de Connection String op te slaan.

3. Plak na het gelijkteken de teken reeks waarde die u eerder in uw Azure Portal hebt gekopieerd. De **Connections Tring** -waarde is uniek voor uw account.

4. Verwijder de code ' Hallo wereld ' uit het **hoofd**. De code moet er ongeveer als volgt uitzien, maar met uw unieke connection string waarde.

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

5. Update **Main** om een **CloudQueue** -object te maken, dat later wordt door gegeven aan de methoden voor verzenden en ontvangen.

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

Maak een nieuwe methode om een bericht naar de wachtrij te verzenden. Voeg de volgende methode toe aan de **programma** klasse. Met deze methode wordt een wachtrij verwijzing opgehaald en vervolgens een nieuwe wachtrij gemaakt als deze nog niet bestaat door het aanroepen van [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Vervolgens wordt het bericht aan de wachtrij toegevoegd door het aanroepen van [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Voeg de volgende **SendMessageAsync** -methode toe aan de **programma** klasse.

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

Een bericht moet in een formaat zijn dat kan worden opgenomen in een XML-aanvraag met UTF-8-codering, en mag maximaal 64 KB groot zijn. Als een bericht binaire gegevens bevat, is het raadzaam om het bericht met base64 te coderen.

Standaard is de maximale time-to-live voor een bericht ingesteld op 7 dagen. U kunt elk wille keurig positief getal voor de time-to-Live-bericht opgeven. Als u een bericht wilt toevoegen dat niet verloopt, gebruikt u `Timespan.FromSeconds(-1)` in de aanroep van **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Berichten uit de wachtrij verwijderen

Maak een nieuwe methode met de naam **ReceiveMessageAsync**. Met deze methode wordt een bericht uit de wachtrij ontvangen door het aanroepen van [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Zodra het bericht is ontvangen, is het belang rijk dat het uit de wachtrij wordt verwijderd zodat het niet meer dan één keer wordt verwerkt. Nadat het bericht is ontvangen, verwijdert u het uit de wachtrij door [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync)aan te roepen.

1. Voeg de volgende **ReceiveMessageAsync** -methode toe aan de **programma** klasse.

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

Het is een best practice aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief blijven, kunnen u geld kosten. Als de wachtrij bestaat, maar leeg is, vraagt u de gebruiker of ze deze wil verwijderen.

1. Vouw de methode **ReceiveMessageAsync** uit om een prompt op te geven voor het verwijderen van de lege wachtrij.

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

## <a name="check-for-command-line-arguments"></a>Controleren op opdracht regel argumenten

Als er opdracht regel argumenten worden door gegeven aan de app, wordt ervan uitgegaan dat er een bericht wordt toegevoegd aan de wachtrij. Voeg de argumenten samen om een teken reeks te maken. Voeg deze teken reeks toe aan de berichten wachtrij door de **SendMessageAsync** -methode aan te roepen die u eerder hebt toegevoegd.

Als er geen opdracht regel argumenten zijn, voert u een ophalen-bewerking uit. Roep de methode **ReceiveMessageAsync** aan om het eerste bericht in de wachtrij op te halen.

Wacht ten slotte op invoer van de gebruiker voordat u afsluit door **console. readline**aan te roepen.

1. Vouw de methode **Main** uit om te controleren op opdracht regel argumenten en te wachten op invoer van de gebruiker.

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

Dit is de volledige code lijst voor dit project.

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

1. Voer vanaf de opdracht regel in de projectmap de volgende DotNet-opdracht uit om het project te bouwen.

   ```console
   dotnet build
   ```

2. Nadat het project is gebouwd, voert u de volgende opdracht uit om het eerste bericht toe te voegen aan de wachtrij.

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

3. Voer de app uit zonder opdracht regel argumenten voor het ontvangen en verwijderen van het eerste bericht in de wachtrij.

   ```console
   dotnet run
   ```

4. Ga door met het uitvoeren van de app totdat alle berichten zijn verwijderd. Als u het nog een keer uitvoert, ontvangt u een bericht dat de wachtrij leeg is en wordt u gevraagd om de wachtrij te verwijderen.

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
2. Berichten toevoegen aan en verwijderen uit een wachtrij
3. Een Azure Storage-wachtrij verwijderen

Bekijk de Snelstartgids voor Azure queues voor meer informatie.

> [!div class="nextstepaction"]
> [Snelstartgids voor wacht rijen](storage-quickstart-queues-portal.md)
