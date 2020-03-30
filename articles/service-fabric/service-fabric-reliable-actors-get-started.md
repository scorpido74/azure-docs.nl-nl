---
title: Een op actor gebaseerde service maken op Azure Service Fabric
description: Meer informatie over het maken, debuggen en implementeren van uw eerste op actor gebaseerde service in C# met behulp van Service Fabric Reliable Actors.
author: vturecek
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: vturecek
ms.openlocfilehash: a6e4fb48653572139463738c82de632ff7d55074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466246"
---
# <a name="getting-started-with-reliable-actors"></a>Aan de slag met betrouwbare actoren
> [!div class="op_single_selector"]
> * [C# op Windows](service-fabric-reliable-actors-get-started.md)
> * [Java op Linux](service-fabric-reliable-actors-get-started-java.md)

Dit artikel loopt door het maken en debuggen van een eenvoudige Reliable Actor applicatie in Visual Studio. Voor meer informatie over betrouwbare actoren, zie [Inleiding tot Service Fabric Betrouwbare Actoren](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u de servicefabric-ontwikkelomgeving, inclusief Visual Studio, op uw machine hebt ingesteld. Zie voor meer informatie [hoe u de ontwikkelomgeving instelt.](service-fabric-get-started.md)

## <a name="create-a-new-project-in-visual-studio"></a>Een nieuw project maken in Visual Studio

Start Visual Studio 2019 of hoger als beheerder en maak vervolgens een nieuw **project voor servicefabricetoepassingen:**

![Service Fabric tools voor Visual Studio - nieuw project][1]

Kies in het volgende dialoogvenster **Actorservice** onder **.NET Core 2.0** en voer een naam voor de service in.

![Projectsjablonen voor Service Fabric][5]

Het gemaakte project toont de volgende structuur:

![Service Fabric-projectstructuur][2]

## <a name="examine-the-solution"></a>De oplossing onderzoeken

De oplossing bestaat uit drie projecten:

* **Het toepassingsproject (MyApplication)**. Dit project verpakt alle services bij elkaar voor implementatie. Het bevat de *ApplicationManifest.xml-* en PowerShell-scripts voor het beheren van de toepassing.

* **Het interfaceproject (HelloWorld.Interfaces)**. Dit project bevat de interfacedefinitie voor de actor. Actor-interfaces kunnen in elk project met elke naam worden gedefinieerd.  De interface definieert het actorcontract dat wordt gedeeld door de implementatie van de actor en de clients die de actor aanroepen.  Omdat clientprojecten ervan afhankelijk kunnen zijn, is het meestal zinvol om het te definiëren in een vergadering die los staat van de implementatie van de actor.

* **Het project van de acteurdienst (HelloWorld)**. Dit project definieert de Service Fabric-service die de actor gaat hosten. Het bevat de uitvoering van de acteur, *HelloWorld.cs*. Een actorimplementatie is een klasse die `Actor` is afgeleid van het basistype en de interfaces implementeert die zijn gedefinieerd in het *MyActor.Interfaces-project.* Een actorklasse moet ook een constructeur `ActorService` implementeren `ActorId` die een instantie `Actor` en een instantie accepteert en deze doorgeeft aan de basisklasse.
    
    Dit project bevat ook *Program.cs*, die actorklassen registreert `ActorRuntime.RegisterActorAsync<T>()`met de runtime van de Service Fabric met behulp van . De `HelloWorld` klas is al geregistreerd. Eventuele extra actor implementaties toegevoegd aan het `Main()` project moet ook worden geregistreerd in de methode.

## <a name="customize-the-helloworld-actor"></a>De HelloWorld-actor aanpassen

De projectsjabloon definieert een `IHelloWorld` aantal methoden in `HelloWorld` de interface en implementeert deze in de implementatie van actors.  Vervang deze methoden, zodat de actor service geeft een eenvoudige "Hello World" string.

Vervang in het *project HelloWorld.Interfaces* in het *IHelloWorld.cs* bestand de interfacedefinitie als volgt:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

Vervang in het **HelloWorld-project** in **HelloWorld.cs**de volledige klassendefinitie als volgt:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Druk op **Ctrl-Shift-B** om het project te bouwen en zorg ervoor dat alles wordt gecompileerd.

## <a name="add-a-client"></a>Een client toevoegen

Maak een eenvoudige consoletoepassing om de actorservice aan te roepen.

1. Klik met de rechtermuisknop op de oplossing in Solution Explorer > Nieuw project **toevoegen...** > **New Project...**.

2. Kies **console-app (.NET Core)** onder de projecttypen **.NET Core** .  Geef het project *ActorClient een*naam .
    
    ![Dialoogvenster Nieuw project toevoegen][6]    
    
    > [!NOTE]
    > Een consoletoepassing is niet het type app dat u normaal gesproken als client in Service Fabric zou gebruiken, maar het is een handig voorbeeld voor het debuggen en testen met behulp van het lokale cluster Service Fabric.

3. De consoletoepassing moet een 64-bits toepassing zijn om compatibiliteit met het interfaceproject en andere afhankelijkheden te behouden.  Klik in Solution Explorer met de rechtermuisknop op het **ActorClient-project** en klik vervolgens op **Eigenschappen**.  Stel op het tabblad **Bouwen** **het doel van het platform** in op **x64**.
    
    ![Eigenschappen bouwen][8]

4. Het klantproject vereist het betrouwbare project NuGet van de acteurs.  Klik **op Tools** > **NuGet Package Manager** > Package**Manager Console**.  Voer in de Package Manager Console de volgende opdracht in:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Het NuGet-pakket en al zijn afhankelijkheden zijn geïnstalleerd in het ActorClient-project.

5. Het clientproject vereist ook een verwijzing naar het interfacesproject.  Klik in het ActorClient-project met de rechtermuisknop op **Afhankelijkheden** en klik vervolgens op **Referentie toevoegen...**.  Selecteer **Projecten > oplossing** (als deze nog niet is geselecteerd) en vink vervolgens het selectievakje aan naast **HelloWorld.Interfaces**.  Klik op **OK**.
    
    ![Dialoogvenster Verwijzing toevoegen][7]

6. Vervang in het ActorClient-project de volledige inhoud van *Program.cs* door de volgende code:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Uitvoeren en debuggen

Druk op **F5** om de toepassing lokaal te bouwen, implementeren en uitvoeren in het cluster Service Fabric-ontwikkeling.  Tijdens het implementatieproces u de voortgang in **het** uitvoervenster zien.

![Uitvoervenster voor foutopsporing van servicefabric][3]

Wanneer de uitvoer de tekst bevat, *is de toepassing gereed,* het is mogelijk om de service te testen met behulp van de ActorClient-toepassing.  Klik in Solution Explorer met de rechtermuisknop op het **ActorClient-project** en klik vervolgens op **Foutopsporing** > **Start nieuwe instantie**.  De opdrachtregeltoepassing moet de uitvoer van de actorservice weergeven.

![Toepassingsuitvoer][9]

> [!TIP]
> De Service Fabric Actors runtime zendt een aantal [gebeurtenissen en performance tellers met betrekking tot actor methoden](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Ze zijn nuttig bij diagnostiek en prestatiebewaking.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [hoe betrouwbare actoren het Service Fabric-platform gebruiken.](service-fabric-reliable-actors-platform.md)


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png