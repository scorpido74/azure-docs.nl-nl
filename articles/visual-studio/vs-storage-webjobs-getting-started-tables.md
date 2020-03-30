---
title: Aan de slag met Azure-opslag met Visual Studio (WebJob-projecten)
description: Azure Table-opslag gebruiken in een Azure WebJobs-project in Visual Studio nadat u verbinding hebt gemaakt met een opslagaccount met visuele studio-verbonden services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e4d8299c06bfa5b0f33bff8fa592a2fa549c695c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707605"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Aan de slag met Azure Storage (Azure WebJob-projecten)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Overzicht
In dit artikel worden C#-codevoorbeelden weergegeven die laten zien hoe u de Azure WebJobs SDK-versie 1.x gebruiken met de Azure-tabelopslagservice. De codevoorbeelden maken gebruik van de [WebJobs SDK-versie](https://github.com/Azure/azure-webjobs-sdk/wiki) 1.x.

Met de Azure Table-opslagservice u grote hoeveelheden gestructureerde gegevens opslaan. De service is een NoSQL-gegevensarchief dat geverifieerde oproepen van binnen en buiten de Azure-cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens.  Zie [Aan de slag met Azure Table-opslag met .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) voor meer informatie.

In sommige codefragmenten wordt het kenmerk **Tabel** weergegeven dat wordt gebruikt in functies die handmatig worden aangeroepen, dat wil zeggen niet met een van de triggerkenmerken.

## <a name="how-to-add-entities-to-a-table"></a>Entiteiten toevoegen aan een tabel

Als u entiteiten aan een tabel wilt toevoegen, gebruikt u het kenmerk **Tabel** met een **ICollector\<T->** of **IAsyncCollector\<T->** parameter waarin **T** het schema opgeeft van de entiteiten die u wilt toevoegen. De kenmerkconstructor neemt een tekenreeksparameter die de naam van de tabel opgeeft.

In het volgende codevoorbeeld worden **person-entiteiten** toegevoegd aan een tabel met de naam *Ingress*.

```csharp
[NoAutomaticTrigger]
public static void IngressDemo(
    [Table("Ingress")] ICollector<Person> tableBinding)
{
    for (int i = 0; i < 100000; i++)
    {
        tableBinding.Add(
            new Person() {
                PartitionKey = "Test",
                RowKey = i.ToString(),
                Name = "Name" }
            );
    }
}
```

Meestal is het type dat u met **ICollector** gebruikt afkomstig van **TableEntity** of implementeert **ITableEntity,** maar dat hoeft niet. Een van de volgende **klassen Persoon** werken met de code die wordt weergegeven in de voorgaande **Methode Ingress.**

```csharp
public class Person : TableEntity
{
    public string Name { get; set; }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Als u rechtstreeks met de Azure-opslag-API wilt werken, u een **parameter CloudStorageAccount** toevoegen aan de methodehandtekening.

## <a name="real-time-monitoring"></a>Realtimecontrole

Omdat gegevensinvallende functies vaak grote hoeveelheden gegevens verwerken, biedt het WebJobs SDK-dashboard realtime bewakingsgegevens. In de sectie **Aanroeplogboek** ziet u of de functie nog steeds wordt uitgevoerd.

![Ingress, functie running](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Op de pagina **Aanroepdetails** wordt de voortgang van de functie (aantal geschreven entiteiten) gerapporteerd terwijl deze wordt uitgevoerd en u deze kunnen afbreken.

![Ingress, functie running](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Wanneer de functie is voltooid, wordt op de pagina **Aanroepdetails** het aantal rijen gerapporteerd dat is geschreven.

![Ingress functie voltooid](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Meerdere entiteiten uit een tabel lezen

Als u een tabel wilt lezen, gebruikt u het kenmerk **Tabel** met een **\<IQueryable T-parameter>** waarbij type **T** afkomstig is van **TableEntity** of **ITableEntity**implementeert.

In het volgende codevoorbeeld worden alle rijen uit de tabel Binnenlaten gelezen en **logboeken:**

```csharp
public static void ReadTable(
    [Table("Ingress")] IQueryable<Person> tableBinding,
    TextWriter logger)
{
    var query = from p in tableBinding select p;
    foreach (Person person in query)
    {
        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
            person.PartitionKey, person.RowKey, person.Name);
    }
}
```

### <a name="how-to-read-a-single-entity-from-a-table"></a>Een enkele entiteit uit een tabel lezen

Er is **Table** een tabelkenmerkconstructor met twee extra parameters waarmee u de partitiesleutel en de rijsleutel opgeven wanneer u wilt binden aan één tabelentiteit.

In het volgende codevoorbeeld wordt een tabelrij voor een **entiteit persoon** gelezen op basis van partitiesleutel- en rijsleutelwaarden die in een wachtrijbericht zijn ontvangen:

```csharp
public static void ReadTableEntity(
    [QueueTrigger("inputqueue")] Person personInQueue,
    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
    TextWriter logger)
{
    if (personInTable == null)
    {
        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                personInQueue.PartitionKey, personInQueue.RowKey);
    }
    else
    {
        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
    }
}
```

De klasse **Persoon** in dit voorbeeld hoeft **ITableEntity**niet te implementeren .

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>De .NET Storage API direct gebruiken om met een tabel te werken

U het kenmerk **Tabel** ook gebruiken met een **CloudTable-object** voor meer flexibiliteit bij het werken met een tabel.

In het volgende codevoorbeeld wordt een **CloudTable-object** gebruikt om één entiteit toe te voegen aan de *insin-tabel.*

```csharp
public static void UseStorageAPI(
    [Table("Ingress")] CloudTable tableBinding,
    TextWriter logger)
{
    var person = new Person()
        {
            PartitionKey = "Test",
            RowKey = "100",
            Name = "Name"
        };
    TableOperation insertOperation = TableOperation.Insert(person);
    tableBinding.Execute(insertOperation);
}
```

Zie Aan de [slag met Azure Table-opslag met .NET](../storage/storage-dotnet-how-to-use-tables.md)voor meer informatie over het gebruik van het **cloudtable-object.**

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Gerelateerde onderwerpen die in de wachtrijen worden behandeld how-to-artikel

Zie [Aan de slag met Azure Queue-opslag en Visual Studio connected services (WebJob Projects)](../storage/vs-storage-webjobs-getting-started-queues.md)voor informatie over het verwerken van tabelverwerking die wordt geactiveerd door een wachtrijbericht of voor WebJobs SDK-scenario's die niet specifiek zijn voor tabelverwerking.

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden codevoorbeelden weergegeven waarin wordt uitgelegd hoe u veelvoorkomende scenario's voor het werken met Azure-tabellen verwerken. Zie [Azure WebJobs-documentatiebronnen](https://go.microsoft.com/fwlink/?linkid=390226)voor meer informatie over het gebruik van Azure WebJobs en de WebJobs SDK.
