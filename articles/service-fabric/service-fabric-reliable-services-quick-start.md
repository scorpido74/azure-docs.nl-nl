---
title: 'Uw eerste Service Fabric-toepassing maken in C #'
description: Inleiding tot het maken van een Microsoft Azure Service Fabric-toepassing met stateloze en stateful services.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev
ms.openlocfilehash: 15dd9bf6ac19bdac7bc8b50fc70e0b3b0a4e9a83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083753"
---
# <a name="get-started-with-reliable-services"></a>Aan de slag met Reliable Services

> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-services-quick-start.md)
> * [Java op Linux](service-fabric-reliable-services-quick-start-java.md)

Een Azure Service Fabric-toepassing bevat een of meer services waarop uw code wordt uitgevoerd. Deze handleiding laat u zien hoe u zowel stateless als stateful Service Fabric-toepassingen maken met [betrouwbare services.](service-fabric-reliable-services-introduction.md)  

## <a name="basic-concepts"></a>Basisbegrippen

Om aan de slag te gaan met Betrouwbare Services, hoeft u slechts een paar basisconcepten te begrijpen:

* **Servicetype**: Dit is uw service-implementatie. Het wordt gedefinieerd door de klasse `StatelessService` die u schrijft die zich uitbreidt en alle andere code of afhankelijkheden die daarin worden gebruikt, samen met een naam en een versienummer.
* **Benoemde serviceinstantie**: Als u uw service wilt uitvoeren, maakt u benoemde exemplaren van uw servicetype, net zoals u objectinstanties van een klassetype maakt. Een serviceinstantie heeft een naam in de vorm van een URI met de "stof:/" zoals "fabric:/MyApp/MyService".
* **Servicehost:** de benoemde service-exemplaren die u maakt, moeten worden uitgevoerd in een hostproces. De servicehost is slechts een proces waarbij exemplaren van uw service kunnen worden uitgevoerd.
* **Serviceregistratie**: Registratie brengt alles samen. Het servicetype moet zijn geregistreerd bij de runtime van Service Fabric in een servicehost, zodat Service Fabric exemplaren van de run kan maken.  

## <a name="create-a-stateless-service"></a>Een stateloze service maken

Een stateless service is een type service dat momenteel de norm is in cloudtoepassingen. Het wordt beschouwd als stateless omdat de service zelf geen gegevens bevat die betrouwbaar moeten worden opgeslagen of in hoge mate beschikbaar moeten worden gesteld. Als een instantie van een staatloze service wordt afgesloten, gaat al de interne status verloren. In dit type service moet de status worden gehandhaafd in een extern archief, zoals Azure Tables of een SQL-database, om deze zeer beschikbaar en betrouwbaar te maken.

Start Visual Studio 2017 of Visual Studio 2019 als beheerder en maak een nieuw Service Fabric-toepassingsproject met de naam *HelloWorld:*

![Het dialoogvenster Nieuw project gebruiken om een nieuwe Service Fabric-toepassing te maken](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Maak vervolgens een stateless serviceproject met **.NET Core 2.0** genaamd *HelloWorldStateless:*

![Maak in het tweede dialoogvenster een stateless serviceproject](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Uw oplossing bevat nu twee projecten:

* *HelloWorld*. Dit is het *toepassingsproject* dat uw *services*bevat. Het bevat ook het toepassingsmanifest dat de toepassing beschrijft, evenals een aantal PowerShell-scripts die u helpen om uw toepassing te implementeren.
* *HelloWorldStateless*. Dit is het serviceproject. Het bevat de stateless service implementatie.

## <a name="implement-the-service"></a>De service implementeren

Open het **HelloWorldStateless.cs-bestand** in het serviceproject. In Service Fabric kan een service elke bedrijfslogica uitvoeren. De service-API biedt twee toegangspunten voor uw code:

* Een open-end entry point-methode, *runasync*genaamd, waar u beginnen met het uitvoeren van workloads, inclusief langlopende compute-workloads.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* Een communicatietoegangspunt waar u uw communicatiestack naar keuze aansluiten, zoals ASP.NET Core. Hier u aanvragen ontvangen van gebruikers en andere services.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

In deze zelfstudie zullen `RunAsync()` we ons richten op de instapmethode. Hier u meteen beginnen met het uitvoeren van uw code.
De projectsjabloon bevat een `RunAsync()` voorbeeldimplementatie van die stappen een rollend aantal.

> [!NOTE]
> Zie [Servicecommunicatie met ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) voor meer informatie over het werken met een communicatiestack

### <a name="runasync"></a>RunAsync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Het platform roept deze methode aan wanneer een instantie van een service is geplaatst en klaar is om uit te voeren. Voor een staatloze service betekent dit gewoon wanneer de service-instantie wordt geopend. Er wordt een annuleringstoken verstrekt om te coördineren wanneer uw service-instantie moet worden gesloten. In Service Fabric kan deze open/sluitcyclus van een service-instantie gedurende de levensduur van de service als geheel vele malen voorkomen. Dit kan om verschillende redenen gebeuren, waaronder:

* Het systeem verplaatst uw service-exemplaren voor resourcebalancing.
* Er treden fouten op in uw code.
* De toepassing of het systeem wordt geüpgraded.
* De onderliggende hardware ondervindt een storing.

Deze orkestratie wordt beheerd door het systeem om uw service zeer beschikbaar en goed in balans te houden.

`RunAsync()`mag niet synchroon blokkeren. Uw implementatie van RunAsync moet een taak retourneren of wachten op langlopende of blokkeringsbewerkingen om de runtime door te laten gaan. Opmerking in `while(true)` de lus in het vorige `await Task.Delay()` voorbeeld, wordt een taak-retourneren gebruikt. Als uw werkbelasting synchroon moet worden geblokkeerd, `Task.Run()` moet `RunAsync` u een nieuwe taak plannen met in uw implementatie.

Het annuleren van uw werklast is een coöperatieve inspanning die wordt georkestreerd door het opgegeven annuleringstoken. Het systeem wacht tot uw taak is beëindigd (door succesvolle voltooiing, annulering of fout) voordat deze wordt uitgevoerd. Het is belangrijk om het annuleringstoken te `RunAsync()` eren, alle werkzaamheden af te ronden en zo snel mogelijk af te sluiten wanneer het systeem annulering aanvraagt.

In dit stateless servicevoorbeeld wordt het aantal opgeslagen in een lokale variabele. Maar omdat dit een statusloze service is, bestaat de waarde die is opgeslagen alleen voor de huidige levenscyclus van de serviceinstantie. Wanneer de service wordt verplaatst of opnieuw wordt opgestart, gaat de waarde verloren.

## <a name="create-a-stateful-service"></a>Een stateful service maken

Service Fabric introduceert een nieuw soort service die stateful is. Een stateful service kan de status betrouwbaar behouden binnen de service zelf, naast de code die deze gebruikt. Staat wordt zeer beschikbaar gesteld door Service Fabric zonder de noodzaak om te blijven staan staat naar een externe winkel.

Als u een tegenwaarde wilt converteren van stateloos naar zeer beschikbaar en persistent, zelfs wanneer de service wordt verplaatst of opnieuw wordt opgestart, hebt u een stateful service nodig.

In dezelfde *HelloWorld-toepassing* u een nieuwe service toevoegen door met de rechtermuisknop op de servicesverwijzingen in het toepassingsproject te klikken en **Add-> New Service Fabric Service**te selecteren.

![Een service toevoegen aan uw Service Fabric-toepassing](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Selecteer **.NET Core 2.0 -> Stateful Service** en noem deze *HelloWorldStateful*. Klik op **OK**.

![Het dialoogvenster Nieuw project gebruiken om een nieuwe servicefabric-service te maken](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Uw applicatie moet nu twee diensten: de stateless dienst *HelloWorldStateless* en de stateful dienst *HelloWorldStateful*.

Een stateful service heeft dezelfde toegangspunten als een staatloze service. Het belangrijkste verschil is de beschikbaarheid van een *statusprovider* die de status betrouwbaar kan opslaan. Service Fabric wordt geleverd met een implementatie van de overheidsprovider genaamd [Betrouwbare verzamelingen,](service-fabric-reliable-services-reliable-collections.md)waarmee u gerepliceerde gegevensstructuren maken via de Reliable State Manager. Een stateful Reliable Service maakt standaard gebruik van deze statusprovider.

Open **HelloWorldStateful.cs** in *HelloWorldStateful*, dat de volgende RunAsync-methode bevat:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync

`RunAsync()`werkt op dezelfde manier in stateful en stateless diensten. In een stateful service voert het platform echter extra werk `RunAsync()`voor u uit voordat het wordt uitgevoerd. Dit werk kan onder meer ervoor zorgen dat de Reliable State Manager en Betrouwbare Collecties klaar zijn voor gebruik.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Betrouwbare collecties en de betrouwbare staatsmanager

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) is een woordenboekimplementatie die u gebruiken om de status in de service betrouwbaar op te slaan. Met Service Fabric en Betrouwbare Collecties u gegevens rechtstreeks in uw service opslaan zonder dat u een externe permanente winkel nodig hebt. Betrouwbare collecties maken uw gegevens zeer beschikbaar. Service Fabric bereikt dit door meerdere *replica's* van uw service voor u te maken en te beheren. Het biedt ook een API die de complexiteit van het beheer van deze replica's en hun statusovergangen abstraheert.

Betrouwbare verzamelingen kunnen elk .NET-type, inclusief uw aangepaste typen, opslaan met een paar kanttekeningen:

* Service Fabric maakt uw status zeer beschikbaar door de status over knooppunten *te repliceren* en betrouwbare verzamelingen slaan uw gegevens op lokale schijf op elke replica op. Dit betekent dat alles wat is opgeslagen in betrouwbare collecties *moet worden serializable*. Standaard gebruiken betrouwbare verzamelingen [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) voor serialisatie, dus het is belangrijk om ervoor te zorgen dat uw typen [worden ondersteund door de Data Contract Serializer](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) wanneer u de standaard serialisator gebruikt.
* Objecten worden gerepliceerd voor hoge beschikbaarheid wanneer u transacties uitvoert op betrouwbare verzamelingen. Objecten die zijn opgeslagen in betrouwbare verzamelingen worden bewaard in het lokale geheugen in uw service. Dit betekent dat u een lokale verwijzing naar het object hebt.
  
   Het is belangrijk dat u lokale exemplaren van die objecten niet muteert zonder een updatebewerking uit te voeren op de betrouwbare verzameling in een transactie. Dit komt omdat wijzigingen in lokale exemplaren van objecten niet automatisch worden gerepliceerd. U moet het object opnieuw in het woordenboek invoegen of een van de *updatemethoden* in het woordenboek gebruiken.

De Reliable State Manager beheert betrouwbare collecties voor u. U de Reliable State Manager op elk gewenst moment en op elke plaats in uw dienst om een betrouwbare collectie op naam vragen. De Reliable State Manager zorgt ervoor dat u een referentie terugkrijgt. We raden u niet aan verwijzingen op te slaan naar betrouwbare verzamelingsinstanties in klasse-lidvariabelen of -eigenschappen. Er moet speciale zorg worden getroffen om ervoor te zorgen dat de verwijzing te allen tijde in de levenscyclus van de service naar een instantie wordt ingesteld. De Reliable State Manager verzorgt dit werk voor u en is geoptimaliseerd voor herhaalde bezoeken.

### <a name="transactional-and-asynchronous-operations"></a>Transactionele en asynchrone bewerkingen

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Betrouwbare verzamelingen hebben veel van `System.Collections.Generic` `System.Collections.Concurrent` dezelfde bewerkingen als hun en tegenhangers, met uitzondering van Language Integrated Query (LINQ). Bewerkingen op betrouwbare verzamelingen zijn asynchroon. Dit komt omdat schrijfbewerkingen met betrouwbare verzamelingen I/O-bewerkingen uitvoeren om gegevens naar schijf te repliceren en te blijven bestaan.

Betrouwbare incassobewerkingen zijn *transactioneel,* zodat u de status consistent houden voor meerdere betrouwbare verzamelingen en bewerkingen. U bijvoorbeeld een werkitem uit een betrouwbare wachtrij de wachtrij en een bewerking uitvoeren en het resultaat opslaan in een betrouwbaar woordenboek, allemaal binnen één transactie. Dit wordt behandeld als een atoomoperatie, en het garandeert dat ofwel de hele operatie zal slagen of de hele operatie zal terugdraaien. Als er een fout optreedt nadat u het item in de wachtrij hebt geplaatst, maar voordat u het resultaat hebt opgeslagen, wordt de hele transactie teruggedraaid en blijft het item in de wachtrij voor verwerking.

## <a name="run-the-application"></a>De toepassing uitvoeren
We keren nu terug naar de *HelloWorld* applicatie. U nu uw services bouwen en implementeren. Wanneer u op **F5**drukt, wordt uw toepassing gebouwd en geïmplementeerd in uw lokale cluster.

Nadat de services zijn gestart, u de gegenereerde Gebeurtenistracering voor Windows (ETW)-gebeurtenissen weergeven in een venster **Diagnostische gebeurtenissen.** Houd er rekening mee dat de weergegeven gebeurtenissen afkomstig zijn van zowel de stateless service als de stateful service in de toepassing. U de stream onderbreken door op de knop **Onderbreken** te klikken. U vervolgens de details van een bericht onderzoeken door dat bericht uit te breiden.

> [!NOTE]
> Voordat u de toepassing uitvoert, moet u ervoor zorgen dat er een lokaal ontwikkelingscluster wordt uitgevoerd. Bekijk de [handleiding voor](service-fabric-get-started.md) het starten van uw lokale omgeving.
> 
> 

![Diagnostische gebeurtenissen weergeven in Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Volgende stappen
[Foutopsporing van uw Service Fabric-toepassing in Visual Studio](service-fabric-debugging-your-application.md)

[Aan de slag: Service Fabric Web API-services met OWIN self-hosting](service-fabric-reliable-services-communication-webapi.md)

[Meer informatie over betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md)

[Een app implementeren](service-fabric-deploy-remove-applications.md)

[Toepassingsupgrade](service-fabric-application-upgrade.md)

[Ontwikkelaarsreferentie voor betrouwbare services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

