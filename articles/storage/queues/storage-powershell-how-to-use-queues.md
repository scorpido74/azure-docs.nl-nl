---
title: Azure Queue-opslagacties uitvoeren in PowerShell
description: Bewerkingen uitvoeren op Azure Queue-opslag met PowerShell
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: bd2f372bdcb949b64f748d186a9b060bb9cbec4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087072"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Azure Queue Storage-bewerkingen uitvoeren met Azure PowerShell

Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Zie [Inleiding tot Azure-wachtrijen](storage-queues-introduction.md)voor meer informatie. Dit artikel over de algemene wachtrijopslag heeft betrekking op algemene wachtrijopslagbewerkingen. Procedures voor:

> [!div class="checklist"]
>
> * Een wachtrij maken
> * Een wachtrij ophalen
> * Een bericht toevoegen
> * Een bericht lezen
> * Een bericht verwijderen
> * Een wachtrij verwijderen

Voor deze how-to is de Azure PowerShell-module Az-versie 0.7 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

Er zijn geen PowerShell-cmdlets voor het gegevensvlak voor wachtrijen. Als u gegevensvlakbewerkingen wilt uitvoeren, zoals een bericht toevoegen, een bericht lezen en een bericht verwijderen, moet u de .NET-opslagclientbibliotheek gebruiken wanneer het wordt weergegeven in PowerShell. U maakt een berichtobject en vervolgens u opdrachten zoals AddMessage gebruiken om bewerkingen in dat bericht uit te voeren. Dit artikel laat zien hoe je dat doet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Lijst met locaties ophalen

Als u niet weet welke locatie u kunt gebruiken, kunt u een lijst met de beschikbare locaties weergeven. Selecteer de gewenste locatie in de lijst. Deze oefening zal gebruik maken **van eastus**. Sla dit op de variabele **locatie** op voor toekomstig gebruik.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Sla de naam van de resourcegroep op in een variabele voor toekomstig gebruik. In dit voorbeeld wordt een resourcegroep met de naam *howtoqueuesrg* gemaakt in de *eastusregio.*

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Een opslagaccount maken

Maak een standaard opslagaccount voor algemene doeleinden met lokaal redundante opslag (LRS) met behulp van [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Download de context van het opslagaccount waarin het opslagaccount wordt gedefinieerd dat moet worden gebruikt. Als u werkt met een opslagaccount, verwijst u naar de context in plaats van herhaaldelijk de referenties op te geven.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Een wachtrij maken

In het volgende voorbeeld wordt eerst een verbinding met Azure Storage gemaakt met behulp van de context van het opslagaccount, die de naam van het opslagaccount en de toegangssleutel bevat. Vervolgens wordt [New-AzStorageQueue-cmdlet](/powershell/module/az.storage/New-AzStorageQueue) aangeroepen om een wachtrij met de naam 'howtoqueue' te maken.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Zie [Wachtrijen en metagegevens voor](https://msdn.microsoft.com/library/azure/dd179349.aspx)naamgevingsconventies voor Azure Queue Service.

## <a name="retrieve-a-queue"></a>Een wachtrij ophalen

U een specifieke wachtrij of een lijst met alle wachtrijen in een opslagaccount opvragen en ophalen. In de volgende voorbeelden wordt uitgelegd hoe u alle wachtrijen in het opslagaccount en een specifieke wachtrij ophalen. beide opdrachten maken gebruik van de [cmdlet Get-AzStorageQueue.](/powershell/module/az.storage/Get-AzStorageQueue)

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Een bericht toevoegen aan een wachtrij

Bewerkingen die van invloed zijn op de werkelijke berichten in de wachtrij, gebruiken de .NET-opslagclientbibliotheek zoals weergegeven in PowerShell. Als u een bericht aan een wachtrij wilt toevoegen, maakt u een nieuw exemplaar van het berichtobject, de klasse [Microsoft.Azure.Storage.Queue.CloudQueueMessage.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue_message) Daarna roept u de methode [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue._cloud_queue.addmessage) aan. Een CloudQueueMessage kan worden gemaakt vanuit een tekenreeks (in UTF-8-indeling) of een bytematrix.

In het volgende voorbeeld wordt uitgelegd hoe u een bericht aan uw wachtrij toevoegt.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Als u de [Azure Storage Explorer](https://storageexplorer.com)gebruikt, u verbinding maken met uw Azure-account en de wachtrijen in het opslagaccount bekijken en inzoomen op een wachtrij om de berichten in de wachtrij weer te geven.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Een bericht uit de wachtrij lezen en vervolgens verwijderen

Berichten worden gelezen in de best-try first-in-first-out volgorde. Dit is niet gegarandeerd. Wanneer u het bericht uit de wachtrij leest, wordt het onzichtbaar voor alle andere processen die naar de wachtrij kijken. Dit zorgt ervoor dat als uw code het bericht niet verwerkt als gevolg van hardware- of softwarefouten, een ander exemplaar van uw code hetzelfde bericht kan krijgen en het opnieuw kan proberen.  

Deze **onzichtbaarheidstime-out** bepaalt hoe lang het bericht onzichtbaar blijft voordat het weer beschikbaar is voor verwerking. De standaardwaarde is 30 seconden.

Uw code leest een bericht uit de wachtrij in twee stappen. Wanneer u de methode [Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) aanroept, krijgt u het volgende bericht in de wachtrij. Een bericht dat wordt geretourneerd door **GetMessage**, wordt onzichtbaar voor andere codes die berichten lezen uit deze wachtrij. Als u het bericht uit de wachtrij wilt verwijderen, belt u de methode [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage.](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage)

In het volgende voorbeeld leest u de drie wachtrijberichten door en wacht u vervolgens 10 seconden (de onzichtbaarheidstime-out). Vervolgens leest u de drie berichten opnieuw, waarbij u de berichten verwijdert nadat u ze hebt gelezen door **DeleteMessage te**bellen. Als u de wachtrij probeert te lezen nadat de berichten zijn verwijderd, worden $queueMessage geretourneerd als NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

Als u een wachtrij en alle berichten in de wachtrij wilt verwijderen, roept u de cmdlet Remove-AzStorageQueue aan. In het volgende voorbeeld ziet u hoe u de specifieke wachtrij verwijdert die in deze oefening wordt gebruikt met de cmdlet Remove-AzStorageQueue.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle elementen wilt verwijderen die u in deze oefening hebt gemaakt, verwijdert u de brongroep. Hiermee verwijdert u ook alle resources binnen de groep. In dit geval worden het opslagaccount verwijderd dat is gemaakt en de brongroep zelf.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over basisopslagbeheer voor wachtrijen met PowerShell, waaronder hoe u:

> [!div class="checklist"]
>
> * Een wachtrij maken
> * Een wachtrij ophalen
> * Een bericht toevoegen
> * Lees het volgende bericht
> * Een bericht verwijderen
> * Een wachtrij verwijderen

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage-cmdlets

* [PowerShell Storage-cmdlets](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
