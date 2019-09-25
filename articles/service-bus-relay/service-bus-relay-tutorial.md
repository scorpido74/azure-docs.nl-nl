---
title: Een on-premises WCF REST-service beschikbaar maken voor externe clients met behulp van Azure WCF Relay | Microsoft Docs
description: Bouw een client-en service toepassing met behulp van WCF Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: 4707e56a09c257c9e03e6db070083c81ffde07b6
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212895"
---
# <a name="expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Een on-premises WCF REST-service beschikbaar maken voor externe clients met behulp van Azure WCF Relay

In deze zelf studie wordt beschreven hoe u een WCF Relay-client toepassing en-service maakt met behulp van Azure Relay. Zie [aan de slag met Service Bus wachtrijen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)voor een vergelijk bare zelf studie die gebruikmaakt van [Service Bus berichten](../service-bus-messaging/service-bus-messaging-overview.md).

Door deze zelf studie te gebruiken, krijgt u een goed beeld van de stappen voor het maken van een WCF Relay client en service toepassing. Net als bij hun oorspronkelijke WCF-equivalenten is een service een construct waarmee een of meer eind punten worden weer gegeven. Elk eind punt stelt een of meer service bewerkingen beschikbaar. Het eindpunt van een service bevat het adres van de service, een binding met de informatie die een client moet doorgeven aan de service en een contract waarin staat welke functionaliteit de service biedt aan de clients. Het belangrijkste verschil tussen WCF en WCF Relay is dat het eind punt in de Cloud wordt weer gegeven in plaats van lokaal op uw computer.

Nadat u de volg orde van de secties in deze zelf studie hebt door lopen, hebt u een actieve service. U hebt ook een-client die de bewerkingen van de service kan aanroepen. 

In deze zelf studie voert u de volgende taken uit:

> [!div class="checklist"]
>
> * Installeer de vereisten voor deze zelf studie.
> * Een relay-naam ruimte maken.
> * Een WCF-service contract maken.
> * Implementeer het WCF-contract.
> * Host en voer de WCF-service uit om u te registreren bij de Relay-service.
> * Maak een WCF-client voor het service contract.
> * Configureer de WCF-client.
> * Implementeer de WCF-client.
> * Voer de toepassingen uit.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com). In de voor beelden in deze zelf studie wordt gebruikgemaakt van Visual Studio 2019.
* Azure-SDK voor .NET. Installeer de SDK via de [SDK-downloadpagina](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Een relay-naam ruimte maken

De eerste stap is het maken van een naamruimte en ophalen van een SAS-sleutel ([Shared Access Signature](../service-bus-messaging/service-bus-sas.md)). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via de relayservice. Een SAS-sleutel wordt automatisch door het systeem gegenereerd wanneer een service naam ruimte wordt gemaakt. De combinatie van servicenaamruimte en SAS-sleutel biedt Service Bus de benodigde referenties voor het verifiëren van toegang tot een toepassing.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Een WCF-service contract definiëren

In het service contract wordt aangegeven welke bewerkingen door de service worden ondersteund. Bewerkingen zijn webservice-methoden of-functies. Contracten worden gemaakt door een C++-, C#- of Visual Basic-interface te definiëren. Elke methode in de interface komt overeen met een specifieke servicebewerking. Op elke interface moet het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) worden toegepast en op elke bewerking moet het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) worden toegepast. Als een methode in een interface met het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) niet beschikt over het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) , wordt die methode niet weer gegeven. In het voorbeeld na de procedure wordt de code voor deze taken weergegeven. Zie [Services ontwerpen en implementeren](/dotnet/framework/wcf/designing-and-implementing-services)voor een grotere bespreking van contracten en services.

### <a name="create-a-relay-contract-with-an-interface"></a>Een relay-contract met een interface maken

1. Start micro soft Visual Studio als beheerder. Als u dit wilt doen, klikt u met de rechter muisknop op het pictogram van het Visual Studio-programma en selecteert u **als administrator uitvoeren**.
1. Selecteer in Visual Studio **een nieuw project maken**.
1. Kies in **een nieuw project maken de**optie **console-app (.NET Framework)** voor C# en selecteer **volgende**.
1. Geef het project de naam *echo service* en selecteer **maken**.

   ![Een console-app maken][2]

1. Klik in **Solution Explorer**met de rechter muisknop op het project en selecteer **NuGet-pakketten beheren**. Selecteer in de **NuGet-pakket manager** **Bladeren**, zoek naar en kies **WindowsAzure. ServiceBus**. Selecteer **installeren**en ga akkoord met de gebruiks voorwaarden.

    ![Service Bus-pakket][3]

   Met dit pakket worden automatisch verwijzingen naar de Service Bus-bibliotheken en `System.ServiceModel`de WCF toegevoegd. [System.ServiceModel](/dotnet/api/system.servicemodel) is de naamruimte die programmatisch toegang biedt tot de basisfuncties van WCF. Service Bus maakt gebruik van veel van de objecten en kenmerken van WCF om servicecontracten te definiëren.

1. Voeg de volgende `using` instructies toe boven aan *Program.cs*:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Verander de standaardnaam `EchoService` van de naamruimte in `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > Deze zelf studie maakt C# gebruik `Microsoft.ServiceBus.Samples` van de naam ruimte van het op contracten gebaseerd beheerde type dat wordt gebruikt in het configuratie bestand in het gedeelte [de WCF-client configureren](#configure-the-wcf-client) . U kunt elke gewenste naam ruimte opgeven wanneer u dit voor beeld bouwt. De zelf studie werkt echter alleen als u de naam ruimten van het contract en de service dienovereenkomstig wijzigt in het configuratie bestand van de toepassing. De naam ruimte die is opgegeven in het bestand *app. config* moet hetzelfde zijn als de naam ruimte C# die is opgegeven in uw bestanden.
   >

1. Direct na de `Microsoft.ServiceBus.Samples` naam ruimte declaratie, maar binnen de naam ruimte, definieert u een `IEchoContract` nieuwe interface met `ServiceContractAttribute` de naam en past u het kenmerk toe op `https://samples.microsoft.com/ServiceModel/Relay/`de interface met een naam ruimte waarde van. Plak de volgende code achter de naam ruimte declaratie:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    De naamruimtewaarde verschilt van de naamruimte die u in uw code gebruikt. In plaats daarvan wordt de naamruimtewaarde gebruikt als een unieke id voor dit contract. Door de naamruimte expliciet op te geven, wordt voorkomen dat de standaardnaamruimtewaarde wordt toegevoegd aan de naam van het contract.

   > [!NOTE]
   > De naamruimte van het servicecontract bevat meestal een schematische naam die ook versie-informatie bevat. Als u versie-informatie in de naamruimte van het servicecontract gebruikt, kunnen services grote wijzigingen isoleren door een nieuw servicecontract te definiëren met een nieuwe naamruimte en het beschikbaar te maken op een nieuw eindpunt. Op deze manier kunnen clients het oude service contract blijven gebruiken zonder dat ze moeten worden bijgewerkt. De versie-informatie kan bestaan uit een datum of een buildnummer. Zie [Serviceversiebeheer](/dotnet/framework/wcf/service-versioning) voor meer informatie. Voor deze zelf studie bevat het naamgevings schema van de naam ruimte van het service contract geen versie-informatie.
   >

1. In de `IEchoContract` interface declareert u een methode voor de enkelvoudige `IEchoContract` bewerking die het contract in de interface weergeeft en `OperationContractAttribute` past u het kenmerk toe op de methode die u als onderdeel van het open bare WCF relay contract wilt weer geven. dit doet u als volgt:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Direct na het definiëren van de `IEchoContract`-interface declareert u een kanaal dat eigenschappen overneemt van de interfaces `IEchoContract` en `IClientChannel`. Ga als volgt te werk:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Een kanaal is het WCF-object waarmee de host en de client informatie aan elkaar doorgeven. Later schrijft u code voor het kanaal om informatie over de twee toepassingen te echo.

1. Selecteer Build**Build Solution** of selecteer CTRL + SHIFT + B om de nauw keurigheid van uw werk tot nu toe te bevestigen. > 

### <a name="example-of-a-wcf-contract"></a>Voor beeld van een WCF-contract

De volgende code toont een basis interface die een WCF Relay contract definieert.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nu de interface is gemaakt, kunt u de interface implementeren.

## <a name="implement-the-wcf-contract"></a>Het WCF-contract implementeren

Als u een Azure relay wilt maken, moet u eerst het contract maken met behulp van een interface. Zie de vorige sectie voor meer informatie over het maken van de interface. In de volgende procedure wordt de interface geïmplementeerd. Deze taak omvat het maken van een `EchoService` klasse met de naam die de door `IEchoContract` de gebruiker gedefinieerde interface implementeert. Nadat u de interface hebt geïmplementeerd, configureert u de interface met behulp van een *app. config* -configuratie bestand. Het configuratie bestand bevat de benodigde informatie voor de toepassing. Deze informatie bevat de naam van de service, de naam van het contract en het type protocol dat wordt gebruikt om te communiceren met de Relay-service. De code die wordt gebruikt voor deze taken vindt u in het voor beeld dat volgt op de procedure. Zie [service contracten implementeren](/dotnet/framework/wcf/implementing-service-contracts)voor een meer algemene discussie over het implementeren van een service contract.

1. Maak een nieuwe klasse met de naam `EchoService` direct nadat de `IEchoContract`-interface is gedefinieerd. Met de klasse `EchoService` wordt de `IEchoContract`-interface geïmplementeerd.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Net als bij andere interface-implementaties kunt u de definitie implementeren in een ander bestand. In deze zelfstudie staat de implementatie echter in hetzelfde bestand als de interfacedefinitie en de `Main()`-methode.

1. Pas het kenmerk [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) toe op de `IEchoContract`-interface. Aan het kenmerk ziet u wat de servicenaam en naamruimte zijn. Wanneer u dit hebt gedaan, wordt de klasse `EchoService` als volgt weergegeven:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementeer de `Echo`-methode die is gedefinieerd in de interface `IEchoContract` in de klasse `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Selecteer Build**Build Solution** of selecteer CTRL + SHIFT + B. > 

### <a name="define-the-configuration-for-the-service-host"></a>De configuratie voor de servicehost definiëren

Het configuratie bestand is vergelijkbaar met een WCF-configuratie bestand. Het bevat de service naam, het eind punt en de binding. Het eind punt is de locatie Azure Relay wordt weer gegeven voor clients en hosts om met elkaar te communiceren. De binding is het type protocol dat wordt gebruikt om te communiceren. Het belangrijkste verschil is dat dit geconfigureerde service-eind punt verwijst naar een [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) -binding die geen deel uitmaakt van de .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) is een van de bindingen die zijn gedefinieerd door de service.

1. Dubbel klik in **Solution Explorer**op **app. config** om het bestand te openen in de Visual Studio-editor.
1. Vervang in het element `<appSettings>` de tijdelijke aanduidingen door de naam van uw servicenaamruimte en de SAS-sleutel die u in een eerdere stap hebt gekopieerd.
1. In de `<system.serviceModel>`-tags voegt u een `<services>`-element toe. U kunt meerdere relay-toepassingen definiëren in een enkel configuratie bestand. In deze zelfstudie wordt er echter maar één gedefinieerd.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Voeg in het `<services>`-element een `<service>`-element toe om de naam van de service te definiëren.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Geef in het `<service>`-element de locatie op van het eindpuntcontract en het type binding voor het eindpunt.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Het eindpunt definieert waar de client zoekt naar de hosttoepassing. Later gebruikt de zelf studie deze stap om een URI te maken waarmee de host volledig wordt weer gegeven via Azure Relay. De binding declareert dat we TCP als protocol gebruiken om te communiceren met de Relay-service.

1. Selecteer Build**Build Solution** of selecteer CTRL + SHIFT + B om de nauw keurigheid van uw werk tot nu toe te bevestigen. > 

### <a name="example-of-implementation-of-a-service-contract"></a>Voor beeld van implementatie van een service contract

In de volgende code staat de implementatie van het servicecontract.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

De volgende code toont de basis indeling van het bestand *app. config* dat is gekoppeld aan de servicehost.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Host en voer de WCF-service uit om u te registreren bij de Relay-service

In deze stap wordt beschreven hoe u een Azure Relay-service uitvoert.

### <a name="create-the-relay-credentials"></a>De relay-referenties maken

1. Maak in `Main()` twee variabelen waarin de naamruimte en de SAS-sleutel uit het consolevenster worden opgeslagen.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    De SAS-sleutel wordt later gebruikt voor toegang tot uw project. De naamruimte wordt als parameter doorgegeven aan `CreateServiceUri` voor het maken van een service-URI.

1. Als u een [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) -object gebruikt, declareert u dat u een SAS-sleutel gebruikt als het referentie type. Voeg de volgende code toe direct na de code die u in de vorige stap hebt toegevoegd.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Een basis adres voor de service maken

Na de code die u in de vorige sectie hebt toegevoegd, `Uri` maakt u een exemplaar voor het basis adres van de service. Deze URI bevat het Service Bus-schema, de naamruimte en het pad naar de service-interface.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

De waarde ' SB ' is een afkorting voor het Service Bus schema. Dit geeft aan dat TCP als protocol wordt gebruikt. Dit schema is ook eerder in het configuratie bestand aangegeven toen [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) werd opgegeven als de binding.

De URI voor deze zelfstudie is `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>De servicehost maken en configureren

1. Als u nog `Main()`steeds aan de slag wilt, `AutoDetect`stelt u de connectiviteits modus in op.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    De connectiviteits modus beschrijft het protocol dat door de service wordt gebruikt om te communiceren met de Relay-service. HTTP of TCP. Met de standaard instelling `AutoDetect`probeert de service verbinding te maken met Azure relay via TCP als deze beschikbaar is, en http als TCP niet beschikbaar is. Dit resultaat wijkt af van het protocol dat door de service is opgegeven voor client communicatie. Dat protocol wordt bepaald op basis van de gebruikte binding. Een service kan bijvoorbeeld de [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) -binding gebruiken, waarmee wordt aangegeven dat het eind punt communiceert met clients via http. Deze service zou kunnen opgeven `ConnectivityMode.AutoDetect` , zodat de service communiceert met Azure relay over TCP.

1. Maak de servicehost met de URI die u eerder in dit gedeelte hebt gemaakt.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    De servicehost is het WCF-object dat de service start. Hier geeft u het type service dat u wilt maken, een `EchoService` type en ook aan het adres waarop u de service beschikbaar wilt stellen.

1. Voeg boven aan het *Program.cs* -bestand verwijzingen toe aan [System. service model. Description](/dotnet/api/system.servicemodel.description) en [micro soft. ServiceBus. Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Configureer vervolgens in `Main()` het eindpunt voor openbare toegang.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Met deze stap wordt de Relay-service geïnformeerd dat uw toepassing openbaar kan worden gevonden door de Atom-feed voor uw project te controleren. Als u deze `DiscoveryType` instelt `private`op, kan de client nog steeds toegang krijgen tot de service. De service wordt echter niet weer gegeven wanneer de `Relay` naam ruimte wordt doorzocht. In plaats daarvan moet de client op voorhand al op de hoogte zijn van het eindpuntpad.

1. Pas de service referenties toe op de service-eind punten die zijn gedefinieerd in het bestand *app. config* :

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Zoals eerder is aangegeven, kon u meerdere services en eind punten hebben gedeclareerd in het configuratie bestand. Als dit het geval is, is met de code het hele configuratiebestand doorzocht naar eindpunten waarop uw referenties kunnen worden toegepast. Voor deze zelf studie heeft het configuratie bestand slechts één eind punt.

### <a name="open-the-service-host"></a>Open de servicehost

1. `Main()`Voeg nog steeds de volgende regel toe om de service te openen.

    ```csharp
    host.Open();
    ```

1. Stel de gebruiker ervan op de hoogte dat de service wordt uitgevoerd en leg uit hoe de service kan worden afgesloten.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Sluit de servicehost wanneer u klaar bent.

    ```csharp
    host.Close();
    ```

1. Selecteer CTRL + SHIFT + B om het project te bouwen.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Voor beeld dat een service in een console toepassing host

Uw voltooide service code moet er als volgt uitzien. De code bevat het service contract en de implementatie uit de vorige stappen in de zelf studie en fungeert als host voor de service in een console toepassing.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Een WCF-client maken voor het servicecontract

De volgende taak is het maken van een client toepassing en het definiëren van het service contract dat u later implementeert. Deze stappen lijken op de stappen voor het maken van een service: het definiëren van een contract, het bewerken van een *app. config* -bestand, het gebruiken van referenties om verbinding te maken met de Relay-service, enzovoort. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

1. Een nieuw project maken in de huidige Visual Studio-oplossing voor de client:

   1. Klik in **Solution Explorer**met de rechter muisknop op de huidige oplossing (niet op het project) en selecteer**Nieuw project** **toevoegen** > .
   1. Selecteer in **een nieuw project toevoegen de**optie **console-app (.NET Framework)** voor C#en selecteer **volgende**.
   1. Name project *EchoClient* en selecteer **Create**.

1. Dubbel klik in **Solution Explorer**in het project **EchoClient** op **Program.cs** om het bestand in de editor te openen, als dit nog niet is geopend.
1. Verander de standaardnaam `EchoClient` van de naamruimte in `Microsoft.ServiceBus.Samples`.
1. Installeer het [Service Bus NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. Klik in **Solution Explorer**met de rechter muisknop op **EchoClient** en selecteer vervolgens **NuGet-pakketten beheren**.
   1. Selecteer **Bladeren**, zoek naar en selecteer **WindowsAzure. ServiceBus**. Selecteer **installeren**en ga akkoord met de gebruiks voorwaarden.

      ![Service Bus-pakket installeren][4]

1. Voeg een `using` instructie voor [System. service model](/dotnet/api/system.servicemodel) naam ruimte toe aan het *Program.cs* -bestand.

    ```csharp
    using System.ServiceModel;
    ```

1. Voeg de definitie van het servicecontract toe aan de naamruimte, zoals wordt weergegeven in het volgende voorbeeld. Deze definitie is identiek aan de definitie die in het **service** project wordt gebruikt. Voeg deze code toe boven aan de `Microsoft.ServiceBus.Samples` naam ruimte.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Selecteer CTRL + SHIFT + B om de client te bouwen.

### <a name="example-of-the-echoclient-project"></a>Voor beeld van het project EchoClient

De volgende code toont de huidige status van het *Program.cs* -bestand in het **EchoClient** -project.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>De WCF-client configureren

In deze stap maakt u een *app. config* -bestand voor een eenvoudige client toepassing die toegang heeft tot de service die eerder in deze zelf studie is gemaakt. Dit *app. config* -bestand definieert het contract, de binding en de naam van het eind punt. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

1. In **Solution Explorer**, in het project **EchoClient** dubbelklikt u op **app. config** om het bestand te openen in de Visual Studio-editor.
1. Vervang in het element `<appSettings>` de tijdelijke aanduidingen door de naam van uw servicenaamruimte en de SAS-sleutel die u in een eerdere stap hebt gekopieerd.
1. In het `system.serviceModel` element voegt u een `<client>` -element toe.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Deze code declareert dat u een client toepassing in WCF-stijl definieert.

1. Geef in het `client`-element de naam, het contract en het type binding op voor het eindpunt.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Deze code definieert de naam van het eind punt. Het definieert ook het contract dat is gedefinieerd in de service en het feit dat de client toepassing TCP gebruikt om te communiceren met Azure Relay. De naam van het eindpunt wordt in de volgende stap gebruikt om deze eindpuntconfiguratie te koppelen aan de service-URI.

1. Selecteer **bestand** > **Alles opslaan**.

### <a name="example-of-the-appconfig-file"></a>Voor beeld van het bestand app. config

Met de volgende code wordt het bestand *app. config* voor de echo-client weer gegeven.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>De WCF-client implementeren

In deze sectie implementeert u een eenvoudige client toepassing die toegang heeft tot de service die u eerder in deze zelf studie hebt gemaakt. Net als bij de service heeft de client veel van dezelfde bewerkingen voor toegang tot Azure Relay:

* De connectiviteitsmodus wordt ingesteld.
* De URI wordt gemaakt waarmee de hostservice wordt gezocht.
* De beveiligingsreferenties worden gedefinieerd.
* De referenties worden toegepast op de verbinding.
* De verbinding wordt geopend.
* De toepassingsspecifieke taken worden uitgevoerd.
* De verbinding wordt gesloten.

Een van de belangrijkste verschillen is echter dat de client toepassing een kanaal gebruikt om verbinding te maken met de Relay-service. De service maakt gebruik van een aanroep van **ServiceHost**. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

### <a name="implement-a-client-application"></a>Een client toepassing implementeren

1. Stel de connectiviteitsmodus in op `AutoDetect`. Voeg de volgende code toe in de `Main()`-methode van de **EchoClient**-toepassing.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Geef variabelen op voor de waarden van de naamruimte van de service. Geef ook de SAS-sleutel op die in de console wordt weergegeven.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Maak de URI die de locatie van de host in uw relay-project definieert.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Maak het referentieobject voor het eindpunt van uw servicenaamruimte.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Maak de kanaalfactory die de configuratie laadt die wordt beschreven in het bestand *app. config* .

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Een kanaalfactory is een WCF-object dat een kanaal maakt waardoor de service- en clienttoepassingen communiceren.

1. De referenties Toep assen.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Maak en open het kanaal naar de service.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Schrijf de algemene gebruikersinterface en de functionaliteit voor de echo.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    De code gebruikt het exemplaar van het kanaal object als een proxy voor de service.

1. Sluit het kanaal en sluit de factory.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Voorbeeld code voor deze zelf studie

De voltooide code moet er als volgt uitzien. Deze code laat zien hoe u een client toepassing maakt, hoe u de bewerkingen van de service aanroept en hoe u de client sluit nadat de bewerkings aanroep is voltooid.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>De toepassingen uitvoeren

1. Selecteer CTRL + SHIFT + B om de oplossing te bouwen. Deze actie bouwt zowel het client project als het service project dat u in de vorige stappen hebt gemaakt.
1. Voordat u de clienttoepassing uitvoert, moet u ervoor zorgen dat de servicetoepassing wordt uitgevoerd. Klik in **Solution Explorer**met de rechter muisknop op de oplossing **echo service** en selecteer vervolgens **Eigenschappen**.
1. Kies **in eigenschappen pagina's**het**opstart project** **algemene eigenschappen** > en vervolgens **meerdere opstart projecten**. Controleer of **EchoService** bovenaan staat in de lijst.
1. Stel het vak **Actie** van de projecten **EchoService** en **EchoClient** in op **Start**.

    ![Eigenschappen pagina's van project][5]

1. Selecteer **Project afhankelijkheden**. Selecteer in **projecten** **EchoClient**. Voor **is afhankelijk van**, moet u ervoor zorgen dat **echo service** is geselecteerd.

    ![Projectafhankelijkheden][6]

1. Selecteer **OK** om **Eigenschappen Vensters**te sluiten.
1. Selecteer F5 om beide projecten uit te voeren.
1. Beide consolevensters worden geopend en u wordt gevraagd de naam van de naamruimte op te geven. De service moet eerst worden uitgevoerd, dus voer in het venster **echo service** -console de naam ruimte in en selecteer vervolgens ENTER.
1. Vervolgens wordt u gevraagd om uw SAS-sleutel. Voer de SAS-sleutel in en selecteer ENTER.

    Hier volgt een voorbeeld van de uitvoer van het consolevenster. Deze waarden zijn slechts voor beelden.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    De servicetoepassing geeft in het consolevenster het adres weer dat wordt gebruikt, zoals in het volgende voorbeeld te zien is.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Voer in het consolevenster **EchoClient** dezelfde gegevens in als de gegevens die u eerder al hebt ingevoerd voor de servicetoepassing. Voer dezelfde service naam ruimte en SAS-sleutel waarden in voor de client toepassing.
1. Wanneer u deze waarden hebt ingevoerd, opent de client een kanaal naar de service en wordt u gevraagd om tekst in te voeren, zoals te zien is in het volgende console-uitvoervoorbeeld.

    `Enter text to echo (or [Enter] to exit):`

    Voer tekst in om naar de service toepassing te verzenden en selecteer ENTER. Deze tekst wordt naar de service verzonden via een Echo-servicebewerking en wordt in het serviceconsolevenster weergegeven, zoals in de volgende voorbeelduitvoer te zien is.

    `Echoing: My sample text`

    De clienttoepassing ontvangt de geretourneerde waarde van de `Echo`-bewerking: de oorspronkelijke tekst. Deze wordt vervolgens weergegeven in het consolevenster. De volgende tekst is een voor beeld van een uitvoer van het client console venster.

    `Server echoed: My sample text`

1. U kunt op deze manier doorgaan met het verzenden van berichten van de client naar de service. Wanneer u klaar bent, selecteert u Enter in de console van de client en service om beide toepassingen te beëindigen.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Een on-premises WCF REST-service beschikbaar maken voor een client buiten uw netwerk](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
