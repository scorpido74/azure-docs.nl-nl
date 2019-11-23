---
title: Aan de slag met Azure Storage met Visual Studio (webtaaks van projecten)
description: Aan de slag met Azure Table Storage in een Azure WebJobs-project in Visual Studio nadat u verbinding hebt gemaakt met een opslag account met behulp van Visual Studio Connected Services
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
ms.openlocfilehash: 042f2659d3691e8c51e092bf69473187b8615ee6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299952"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Aan de slag met Azure Storage (projecten van Azure webtaaks)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Overzicht
In dit artikel C# vindt u voor beelden van code die laten zien hoe u de Azure WebJobs SDK versie 1. x kunt gebruiken met de Azure Table Storage-service. De code voorbeelden gebruiken de [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) versie 1. x.

Met de Azure Table Storage-service kunt u grote hoeveel heden gestructureerde gegevens opslaan. De service is een NoSQL-gegevens opslag die geverifieerde aanroepen binnen en buiten de Azure-Cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens.  Zie [aan de slag met Azure Table Storage met .net](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) voor meer informatie.

Sommige code fragmenten bevatten het **tabel** kenmerk dat wordt gebruikt in functies die hand matig worden aangeroepen, dat wil zeggen, niet door een van de trigger kenmerken te gebruiken.

## <a name="how-to-add-entities-to-a-table"></a>Entiteiten toevoegen aan een tabel
Als u entiteiten wilt toevoegen aan een tabel, gebruikt u het kenmerk **Table** met een **ICollector\<t >** of **IAsyncCollector\<t >** para meter waarbij **t** het schema aangeeft van de entiteiten die u wilt toevoegen. De kenmerk-constructor heeft een teken reeks parameter waarmee de naam van de tabel wordt opgegeven.

In het volgende code voorbeeld **worden entiteits entiteiten toegevoegd aan een** tabel met *de naam*inkomend verkeer.

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

Normaal gesp roken wordt het type dat u gebruikt met **ICollector** afgeleid van **TableEntity** of **ITableEntity**geïmplementeerd, maar dit is niet nodig. Een van de volgende **persoons** klassen werkt met de code die wordt weer gegeven in de voor gaande **ingangs** methode.

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

Als u rechtstreeks met de Azure Storage API wilt werken, kunt u een **Cloud Storage account** -para meter toevoegen aan de methode handtekening.

## <a name="real-time-monitoring"></a>Realtime-bewaking
Omdat de functies voor het inkomen van gegevens vaak grote hoeveel heden gegevens verwerken, biedt het SDK-dash board van webjobs real-time bewakings gegevens. De sectie **aanroep logboek** geeft aan of de functie nog wordt uitgevoerd.

![De functie ingangs functies wordt uitgevoerd](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

Op de pagina **aanroep Details** wordt de voortgang van de functie gerapporteerd (het aantal entiteiten dat is geschreven) terwijl deze wordt uitgevoerd en krijgt u de mogelijkheid om deze af te breken.

![De functie ingangs functies wordt uitgevoerd](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Wanneer de functie is voltooid, wordt in de pagina **aanroep Details** het aantal geschreven rijen gerapporteerd.

![De functie ingress is voltooid](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Meerdere entiteiten uit een tabel lezen
Als u een tabel wilt lezen, gebruikt u het kenmerk **Table** met een **IQueryable\<t >** -para meter waarbij type **t** is afgeleid van **TableEntity** of **ITableEntity**implementeert.

In het volgende code voorbeeld worden alle rijen uit de **ingangs** tabel gelezen en geregistreerd:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Een afzonderlijke entiteit uit een tabel lezen
Er is een **Table** -kenmerk constructie met twee extra para meters waarmee u de partitie sleutel en de rij-sleutel kunt opgeven wanneer u wilt binden aan een enkele tabel entiteit.

In het volgende code voorbeeld wordt een tabelrij gelezen voor een **persoons** entiteit op basis van de waarden van de partitie sleutel en de rijwaarden die zijn ontvangen in een wachtrij bericht:  

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


De **persoons** klasse in dit voor beeld hoeft geen **ITableEntity**te implementeren.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>De .NET Storage-API rechtstreeks gebruiken om te werken met een tabel
U kunt ook het kenmerk **Table** gebruiken met een **CloudTable** -object voor meer flexibiliteit bij het werken met een tabel.

In het volgende code voorbeeld wordt een **CloudTable** -object gebruikt om één entiteit toe te voegen aan de *ingangs* tabel.

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

Zie [aan de slag met Azure Table Storage met .net](../storage/storage-dotnet-how-to-use-tables.md)voor meer informatie over het gebruik van het **CloudTable** -object.

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Verwante onderwerpen die worden behandeld in het artikel wacht rijen
Zie aan de slag [met Azure Queue Storage en Visual Studio Connected Services (project taak)](../storage/vs-storage-webjobs-getting-started-queues.md)voor meer informatie over het verwerken van tabel verwerking die wordt geactiveerd door een wachtrij bericht of voor WEBjobs SDK-scenario's die niet specifiek zijn voor tabel verwerking.

## <a name="next-steps"></a>Volgende stappen
In dit artikel vindt u code voorbeelden die laten zien hoe u veelvoorkomende scenario's kunt afhandelen voor het werken met Azure-tabellen. Zie [Azure WebJobs documentation resources](https://go.microsoft.com/fwlink/?linkid=390226)(Engelstalig) voor meer informatie over het gebruik van Azure WebJobs en de webjobs SDK.

