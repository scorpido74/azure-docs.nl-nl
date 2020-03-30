---
title: Clusterknooppunten starten en stoppen
description: Meer informatie over het gebruik van foutinjectie om een Service Fabric-toepassing te testen door clusterknooppunten te starten en te stoppen.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 8f2eefec94ad4763a054ee089b17232c41e642dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609788"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>De API's startknooppunt en knooppunt stoppen vervangen door de API voor knooppuntovergang

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Wat doen de API's Stop Node en Start Node?

De Stop Node API (beheerd: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) stopt een Service Fabric-knooppunt.  Een Service Fabric-knooppunt is proces, geen VM of machine – de VM of machine wordt nog steeds uitgevoerd.  Voor de rest van het document betekent "knooppunt" het knooppunt Service Fabric.  Als u een knooppunt stopt, wordt het in een *gestopte* status gebracht waar het geen lid van het cluster is en geen services kan hosten, waardoor een *downnode* wordt gesimulerd.  Dit is handig voor het injecteren van fouten in het systeem om uw toepassing te testen.  De Start Node API (beheerd: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) keert de Stop Node API om, waardoor het knooppunt weer in een normale staat wordt.

## <a name="why-are-we-replacing-these"></a>Waarom vervangen we deze?

Zoals eerder beschreven, is een *gestopt* Service Fabric-knooppunt een knooppunt dat opzettelijk is gericht met behulp van de Stop Node API.  Een *down* node is een knooppunt dat om een andere reden is uitgeschakeld (bijvoorbeeld de VM of machine is uitgeschakeld).  Met de Stop Node API stelt het systeem geen informatie bloot om onderscheid te maken tussen *gestopte* knooppunten en *downnodes.*

Bovendien zijn sommige fouten die door deze API's worden geretourneerd niet zo beschrijvend als ze zouden kunnen zijn.  Als u bijvoorbeeld een beroep doet op de STOP Node API op een al *gestopt* knooppunt, wordt de fout *Ongeldig adres weergegeven.*  Deze ervaring kan worden verbeterd.

Ook is de duur waarvoor een knooppunt wordt gestopt "oneindig" totdat de API voor het startscherm wordt aangeroepen.  We hebben ontdekt dat dit problemen kan veroorzaken en kan foutgevoelig zijn.  We hebben bijvoorbeeld problemen gezien waarbij een gebruiker de Stop Node API op een knooppunt aanriep en deze vervolgens vergat.  Later was het onduidelijk of het knooppunt *naar beneden* was of *gestopt*.


## <a name="introducing-the-node-transition-apis"></a>Introductie van de Node Transition API's

We hebben deze problemen hierboven opgelost in een nieuwe set API's.  De nieuwe Node Transition API (beheerd: [StartNodeTransitionAsync()][snt]) kan worden gebruikt om een Service Fabric-knooppunt over te zetten naar een *gestopte* status of om deze over te zetten van een *gestopte* status naar een normale status.  Houd er rekening mee dat de "Start" in de naam van de API niet verwijst naar het starten van een knooppunt.  Het verwijst naar het begin van een asynchrone bewerking die het systeem uitvoert om het knooppunt over te zetten naar *gestopt* of gestarte status.

**Gebruik**

Als de API voor knooppuntovergang geen uitzondering maakt wanneer het wordt aangeroepen, heeft het systeem de asynchrone bewerking geaccepteerd en wordt deze uitgevoerd.  Een geslaagde oproep betekent niet dat de bewerking nog is voltooid.  Als u informatie wilt krijgen over de huidige status van de bewerking, roept u de Node Transition Progress API (beheerd: [GetNodeTransitionProgressAsync()][gntp]aan met de guid die wordt gebruikt bij het oproepen van node transition API voor deze bewerking.  De Node Transition Progress API retourneert een object NodeTransitionProgress.  De eigenschap Status van dit object geeft de huidige status van de bewerking aan.  Als de status 'Actief' is, wordt de bewerking uitgevoerd.  Als deze is voltooid, is de bewerking zonder fouten voltooid.  Als er een fout is gemaakt, was er een probleem met het uitvoeren van de bewerking.  De eigenschap Exception van het resultaat geeft aan wat het probleem was.  Zie https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate voor meer informatie over de eigenschap Status en de sectie 'Voorbeeldgebruik' hieronder voor codevoorbeelden.


**Differentiëren tussen een gestopt knooppunt en een down node** Als een knooppunt wordt *gestopt* met de Node Transition API, wordt de uitvoer van een knooppuntquery (beheerd: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode)][nodequeryps]weergegeven dat dit knooppunt een waarde van de eigenschap *IsStopped* van true heeft.  Let op: dit is anders dan de waarde van de eigenschap *NodeStatus,* die *omlaag*staat.  Als de eigenschap *NodeStatus* een waarde van *Down*heeft, maar *IsStopped* onjuist is, is het knooppunt niet gestopt met het gebruik van de API knooppuntovergang en is *het omlaag* vanwege een andere reden.  Als de eigenschap *IsStopped* waar is en de eigenschap *NodeStatus* *Down*is, is deze gestopt met de API voor knooppuntovergang.

Als u een *gestopt* knooppunt start met de API Knooppuntovergang, wordt deze weer als normaal lid van het cluster weergegeven.  De uitvoer van de node query API toont *IsStopped* als false en *NodeStatus* als iets dat niet Omlaag is (bijvoorbeeld Up).


**Beperkte duur** Wanneer u de API voor knooppuntovergang gebruikt om een knooppunt te stoppen, vertegenwoordigt een van de vereiste parameters, *stopNodeDurationInSeconds,* de hoeveelheid tijd in seconden om het knooppunt *gestopt*te houden.  Deze waarde moet zich binnen het toegestane bereik bevinden, dat een minimum van 600 heeft, en een maximum van 14400.  Nadat deze tijd is verstreken, wordt het knooppunt automatisch opnieuw gestart in de status Up.  Raadpleeg hieronder voorbeeld 1 voor een voorbeeld van gebruik.

> [!WARNING]
> Vermijd het mengen van Node Transition API's en de Stop Node en Start Node API's.  De aanbeveling is om alleen de Node Transition API te gebruiken.  > Als een knooppunt al is gestopt met het gebruik van de Stop Node API, moet deze eerst worden gestart met het gebruik van de API voor knooppunt start voordat u de API's voor > knooppuntovergang gebruikt.

> [!WARNING]
> Meerdere node transition API's-oproepen kunnen niet parallel op hetzelfde knooppunt worden uitgevoerd.  In een dergelijke situatie zal de API voor knooppuntovergang > een FabricException met een eigenschapswaarde ErrorCode van NodeTransitionInProgress gooien.  Zodra een knooppuntovergang op een specifiek knooppunt is > gestart, moet u wachten tot de bewerking een terminalstatus bereikt (Voltooid, Fout of ForceCancelled) voordat u een > nieuwe overgang op hetzelfde knooppunt start.  Parallelle knooppuntovergangsoproepen op verschillende knooppunten zijn toegestaan.


#### <a name="sample-usage"></a>Voorbeeldgebruik


**Voorbeeld 1** - In het volgende voorbeeld wordt de API knooppuntovergang gebruikt om een knooppunt te stoppen.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Voorbeeld 2** - In het volgende voorbeeld wordt een *gestopt* knooppunt gestart.  Het maakt gebruik van een aantal helper methoden uit het eerste monster.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Voorbeeld 3** - In het volgende voorbeeld wordt onjuiste gebruik weergegeven.  Dit gebruik is onjuist omdat de *stopDurationInSeconds* die het biedt groter is dan het toegestane bereik.  Aangezien StartNodeTransitionAsync() mislukt met een fatale fout, is de bewerking niet geaccepteerd en mag de voortgangs-API niet worden aangeroepen.  Dit voorbeeld maakt gebruik van een aantal helpermethoden uit het eerste monster.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Voorbeeld 4** - In het volgende voorbeeld worden de foutgegevens weergegeven die worden geretourneerd uit de Node Transition Progress API wanneer de bewerking die is gestart door de Node Transition API wordt geaccepteerd, maar later mislukt tijdens het uitvoeren.  In het geval mislukt dit omdat de API Knooppuntovergang probeert een knooppunt te starten dat niet bestaat.  Dit voorbeeld maakt gebruik van een aantal helpermethoden uit het eerste monster.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
