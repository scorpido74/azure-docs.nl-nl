---
title: ReliableConcurrentQueue in Azure-servicestructuur
description: ReliableConcurrentQueue is een wachtrij met hoge doorvoer waarmee parallelle wachtrijen en wachtrijen mogelijk zijn.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: a7115db8259fde0e87e53557ecef730f8e82d2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462740"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Inleiding tot ReliableConcurrentQueue in Azure-servicestructuur
Reliable Concurrent Queue is een asynchrone, transactionele en gerepliceerde wachtrij met hoge gelijktijdigheid voor wachtrij- en wachtrijbewerkingen. Het is ontworpen om een hoge doorvoer en lage latentie te leveren door de strikte FIFO-bestelling van [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx) te ontspannen en in plaats daarvan een best-effort bestellen te bieden.

## <a name="apis"></a>API's

|Gelijktijdige wachtrij                |Betrouwbare gelijktijdige wachtrij                                         |
|--------------------------------|------------------------------------------------------------------|
| Enqueue(T-item) ongeldig maken           | TaakenqueueAsync(ITransaction tx, T-item)                       |
| bool TryDequeue(out T resultaat)  | Taak< voorwaardelijke waarde < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | lange telling()                                                     |

## <a name="comparison-with-reliable-queue"></a>Vergelijking met [betrouwbare wachtrij](https://msdn.microsoft.com/library/azure/dn971527.aspx)

Betrouwbare gelijktijdige wachtrij wordt aangeboden als alternatief voor [betrouwbare wachtrij.](https://msdn.microsoft.com/library/azure/dn971527.aspx) Het moet worden gebruikt in gevallen waarin strikte FIFO-bestellingen niet vereist zijn, omdat het garanderen van FIFO een afweging met gelijktijdigheid vereist.  [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx) gebruikt vergrendelingen om FIFO-bestellingen af te dwingen, waarbij maximaal één transactie in de wachtrij mag staan en hoogstens één transactie tegelijk kan worden verwijderd. Ter vergelijking: Reliable Concurrent Queue versoepelt de bestelbeperking en stelt elk aantal gelijktijdige transacties in staat om hun wachtrij- en wachtrijactiviteiten te interleave. Best-effort bestellen is voorzien, maar de relatieve volgorde van twee waarden in een betrouwbare gelijktijdige wachtrij kan nooit worden gegarandeerd.

Betrouwbare gelijktijdige wachtrij biedt een hogere doorvoer en lagere latentie dan [betrouwbare wachtrij](https://msdn.microsoft.com/library/azure/dn971527.aspx) wanneer er meerdere gelijktijdige transacties zijn die wachtrijen en/of wachtrijen uitvoeren.

Een voorbeeldgebruiksaanvraag voor de ReliableConcurrentQueue is het scenario van de [berichtenwachtrij.](https://en.wikipedia.org/wiki/Message_queue) In dit scenario maken en toevoegen een of meer berichtenproducenten items aan de wachtrij en trekken een of meer berichtenconsumenten berichten uit de wachtrij en verwerken ze. Meerdere producenten en consumenten kunnen zelfstandig werken en gelijktijdige transacties gebruiken om de wachtrij te verwerken.

## <a name="usage-guidelines"></a>Gebruiksrichtlijnen
* De wachtrij verwacht dat de items in de wachtrij een lage bewaarperiode hebben. Dat wil zeggen, de items zou niet in de wachtrij voor een lange tijd verblijven.
* De wachtrij garandeert geen strikte FIFO-bestelling.
* De wachtrij leest zijn eigen schrijft niet. Als een item in een wachtrij staat voor een transactie, is het niet zichtbaar voor een dequeuer binnen dezelfde transactie.
* Wachtrijen zijn niet geïsoleerd van elkaar. Als post *A* in de wachtrij staat in *transactietxnA*, ook al is *txnA* niet vastgelegd, is post *A* niet zichtbaar voor een gelijktijdige *transactietxnB*.  Als *txnA* afbreekt, wordt *A* onmiddellijk zichtbaar voor *txnB.*
* *TryPeekAsync-gedrag* kan worden geïmplementeerd door een *TryDequeueAsync te* gebruiken en de transactie vervolgens af te breken. Een voorbeeld van dit gedrag is te vinden in de sectie Programmeerpatronen.
* Count is niet-transactioneel. Het kan worden gebruikt om een idee te krijgen van het aantal elementen in de wachtrij, maar vertegenwoordigt een point-in-time en kan niet worden ingeroepen.
* Dure verwerking op de in de wachtrij geplaatste items mag niet worden uitgevoerd terwijl de transactie actief is, om langlopende transacties te voorkomen die een prestatie-impact op het systeem kunnen hebben.

## <a name="code-snippets"></a>Codefragmenten
Laten we eens kijken naar een paar code fragmenten en hun verwachte uitgangen. De afhandeling van uitzonderingen wordt in deze sectie genegeerd.

### <a name="instantiation"></a>Instantiëring
Het maken van een instantie van een betrouwbare concurrent wachtrij is vergelijkbaar met elke andere betrouwbare collectie.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Hier zijn een paar codefragmenten voor het gebruik van EnqueueAsync, gevolgd door de verwachte uitvoer.

- *Case 1: Taak voor één wachtrij*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Ga ervan uit dat de taak is voltooid en dat er geen gelijktijdige transacties zijn gewijzigd die de wachtrij wijzigen. De gebruiker kan verwachten dat de wachtrij de items in een van de volgende orders bevat:

> 10, 20
> 
> 20, 10


- *Case 2: Parallel enqueue Taak*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Ga ervan uit dat de taken zijn voltooid, dat de taken parallel zijn uitgevoerd en dat er geen andere gelijktijdige transacties zijn gewijzigd die de wachtrij hebben gewijzigd. Er kan geen gevolgtrekking worden gemaakt over de volgorde van artikelen in de wachtrij. Voor dit codefragment kunnen de items worden weergegeven in een van de 4! mogelijke bestellingen.  De wachtrij probeert de items in de oorspronkelijke (in de wachtrij) volgorde te houden, maar kan worden gedwongen om ze opnieuw te ordenen vanwege gelijktijdige bewerkingen of fouten.


### <a name="dequeueasync"></a>DequeueAsync
Hier volgen een paar codefragmenten voor het gebruik van TryDequeueAsync, gevolgd door de verwachte uitvoer. Ga ervan uit dat de wachtrij al is gevuld met de volgende items in de wachtrij:
> 10, 20, 30, 40, 50, 60

- *Case 1: Single Dequeue Taak*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Ga ervan uit dat de taak is voltooid en dat er geen gelijktijdige transacties zijn gewijzigd die de wachtrij wijzigen. Aangezien er geen gevolgtrekking kan worden gemaakt over de volgorde van de items in de wachtrij, kunnen drie van de items in elke volgorde in de wachtrij worden geplaatst. De wachtrij probeert de items in de oorspronkelijke (in de wachtrij) volgorde te houden, maar kan worden gedwongen om ze opnieuw te ordenen vanwege gelijktijdige bewerkingen of fouten.  

- *Case 2: Parallel Dequeue Taak*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Ga ervan uit dat de taken zijn voltooid, dat de taken parallel zijn uitgevoerd en dat er geen andere gelijktijdige transacties zijn gewijzigd die de wachtrij hebben gewijzigd. Aangezien er geen gevolgtrekking kan worden gemaakt over de volgorde van de items in de wachtrij, zullen de lijsten *dequeue1* en *dequeue2* elk twee items bevatten, in elke volgorde.

Hetzelfde item wordt *niet* in beide lijsten weergegeven. Vandaar, als dequeue1 *heeft 10*, *30*, dan dequeue2 zou hebben *20*, *40*.

- *Case 3: Bestellen met transactie afbreken met de wachtrij*

Als u een transactie met wachtrijen in de vlucht afbreekt, worden de artikelen weer op het hoofd van de wachtrij geplaatst. De volgorde waarin de artikelen weer op de kop van de wachtrij worden gezet, is niet gegarandeerd. Laten we eens kijken naar de volgende code:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Ga ervan uit dat de items in de volgende volgorde in de wachtrij zijn geplaatst:
> 10, 20

Wanneer we de transactie afbreken, worden de artikelen weer toegevoegd aan het hoofd van de wachtrij in een van de volgende orders:
> 10, 20
> 
> 20, 10

Hetzelfde geldt voor alle gevallen waarin de transactie niet is *vastgelegd*.

## <a name="programming-patterns"></a>Programmeerpatronen
Laten we in deze sectie een smaken naar een paar programmeerpatronen die nuttig kunnen zijn bij het gebruik van ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Batchdewachtrijen
Een aanbevolen programmeerpatroon is dat de taak van de consument de wachtrijen batcht in plaats van één wachtrij tegelijk uit te voeren. De gebruiker kan ervoor kiezen om vertragingen tussen elke batch of de batchgrootte te beperken. In het volgende codefragment wordt dit programmeermodel weergegeven. Houd er rekening mee dat in dit voorbeeld de verwerking wordt uitgevoerd nadat de transactie is vastgelegd, dus als er een fout optreedt tijdens de verwerking, gaan de onverwerkte artikelen verloren zonder dat deze zijn verwerkt.  Als alternatief kan de verwerking worden uitgevoerd binnen het bereik van de transactie, maar het kan een negatieve invloed hebben op de prestaties en vereist de behandeling van de reeds verwerkte items.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Verwerking op basis van kennisgevingen op basis van de beste inspanning
Een ander interessant programmeerpatroon maakt gebruik van de Count API. Hier kunnen we de verwerking op basis van meldingen op basis van de beste inspanning implementeren voor de wachtrij. Het aantal wachtrijen kan worden gebruikt om een wachtrij- of wachtrijtaak te beperken.  Houd er rekening mee dat, net als in het vorige voorbeeld, omdat de verwerking buiten de transactie plaatsvindt, onverwerkte artikelen verloren kunnen gaan als er een fout optreedt tijdens de verwerking.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Best-Effort Drain
Een afvoer van de wachtrij kan niet worden gegarandeerd vanwege het gelijktijdige karakter van de gegevensstructuur.  Het is mogelijk dat, zelfs als er geen gebruikersbewerkingen in de wachtrij zijn in de vlucht, een bepaalde oproep naar TryDequeueAsync een item dat eerder in de wachtrij en vastgelegd is, niet kan retourneren.  Het in de wachtrij staande artikel zal *uiteindelijk* gegarandeerd zichtbaar worden voor de wachtrij, maar zonder een out-of-band communicatiemechanisme kan een onafhankelijke consument niet weten dat de wachtrij een stabiele status heeft bereikt, zelfs als alle producenten zijn gestopt en er geen nieuwe wachtrijbewerkingen zijn toegestaan. Dus, de afvoer operatie is best-inspanning zoals hieronder uitgevoerd.

De gebruiker moet alle verdere taken van producenten en consumenten stoppen en wachten tot transacties aan boord worden gepleegd of afgebroken, voordat de wachtrij wordt uitgeput.  Als de gebruiker het verwachte aantal items in de wachtrij kent, kan hij een melding instellen die aangeeft dat alle items in de wachtrij zijn geplaatst.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Piek
ReliableConcurrentQueue biedt de *TryPeekAsync* api niet. Gebruikers kunnen de peek semantische krijgen door een *TryDequeueAsync te* gebruiken en vervolgens de transactie af te breken. In dit voorbeeld worden wachtrijen alleen verwerkt als de waarde van het artikel groter is dan *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Moet lezen
* [Betrouwbare services snel van start](service-fabric-reliable-services-quick-start.md)
* [Werken met betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Meldingen van betrouwbare services](service-fabric-reliable-services-notifications.md)
* [Betrouwbare services back-up en herstel (herstel na noodgevallen)](service-fabric-reliable-services-backup-restore.md)
* [Betrouwbare configuratie van State Manager](service-fabric-reliable-services-configuration.md)
* [Aan de slag met Service Fabric Web API Services](service-fabric-reliable-services-communication-webapi.md)
* [Geavanceerd gebruik van het programmeermodel betrouwbare services](service-fabric-reliable-services-advanced-usage.md)
* [Referentie voor ontwikkelaars voor betrouwbare verzamelingen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
