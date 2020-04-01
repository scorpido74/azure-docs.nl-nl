---
title: Een on-prem WCF REST-service blootstellen aan clients die Azure Relay gebruiken
description: In deze zelfstudie wordt beschreven hoe u een on-premises WCF REST-service aan een externe client blootstellen met Azure WCF Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 551c8e662669737d9d074a69cb03d6060ab87ad5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76513079"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Zelfstudie: Een on-premises WCF REST-service blootstellen aan externe client met Azure WCF Relay

In deze zelfstudie wordt beschreven hoe u een WCF Relay-clienttoepassing en -service maken met Azure Relay. Zie [Aan de slag gaan met wachtrijen voor servicebus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)voor een vergelijkbare zelfstudie die [servicebusberichten](../service-bus-messaging/service-bus-messaging-overview.md)gebruikt.

Door deze zelfstudie te werken, krijgt u inzicht in de stappen om een WCF Relay-client- en servicetoepassing te maken. Net als hun oorspronkelijke WCF-tegenhangers is een service een constructie die een of meer eindpunten blootlegt. Elk eindpunt legt een of meer servicebewerkingen bloot. Het eindpunt van een service bevat het adres van de service, een binding met de informatie die een client moet doorgeven aan de service en een contract waarin staat welke functionaliteit de service biedt aan de clients. Het belangrijkste verschil tussen WCF en WCF Relay is dat het eindpunt wordt blootgesteld in de cloud in plaats van lokaal op uw computer.

Nadat u de volgorde van secties in deze zelfstudie hebt doorlopen, hebt u een lopende service. U hebt ook een client die de werking van de service kan aanroepen. 

U doet de volgende taken in deze zelfstudie:

> [!div class="checklist"]
>
> * Installeer vereisten voor deze zelfstudie.
> * Maak een naamruimte voor relais.
> * Maak een WCF-servicecontract.
> * Uitvoering van het WCF-contract.
> * Host en voer de WCF-service uit om je te registreren bij de Relay-service.
> * Maak een WCF-client voor het servicecontract.
> * Configureer de WCF-client.
> * Implementeer de WCF-client.
> * Voer de toepassingen uit.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com). De voorbeelden in deze tutorial gebruiken Visual Studio 2019.
* Azure-SDK voor .NET. Installeer de SDK via de [SDK-downloadpagina](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Een naamruimte voor relais maken

De eerste stap is het maken van een naamruimte en ophalen van een SAS-sleutel ([Shared Access Signature](../service-bus-messaging/service-bus-sas.md)). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via de relayservice. Een SAS-sleutel wordt automatisch gegenereerd door het systeem wanneer een servicenaamruimte wordt gemaakt. De combinatie van servicenaamruimte en SAS-sleutel biedt Service Bus de benodigde referenties voor het verifiëren van toegang tot een toepassing.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Een WCF-servicecontract definiëren

Het servicecontract geeft aan welke bewerkingen de service ondersteunt. Bewerkingen zijn webservicemethoden of -functies. Contracten worden gemaakt door een C++-, C#- of Visual Basic-interface te definiëren. Elke methode in de interface komt overeen met een specifieke servicebewerking. Op elke interface moet het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) worden toegepast en op elke bewerking moet het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) worden toegepast. Als een methode in een interface met het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) niet het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) heeft, wordt die methode niet weergegeven. In het voorbeeld na de procedure wordt de code voor deze taken weergegeven. Zie [Services ontwerpen en implementeren](/dotnet/framework/wcf/designing-and-implementing-services)voor een grotere discussie over contracten en diensten.

### <a name="create-a-relay-contract-with-an-interface"></a>Een relaycontract maken met een interface

1. Start Microsoft Visual Studio als beheerder. Klik hiervoor met de rechtermuisknop op het programmapictogram van Visual Studio en selecteer **Uitvoeren als beheerder**.
1. Selecteer in Visual Studio De optie **Een nieuw project maken**.
1. Kies in **Een nieuw project maken**de optie Console App **(.NET Framework)** voor C# en selecteer **Volgende**.
1. Geef het project *EchoService* een naam en selecteer **Maken**.

   ![Een console-app maken][2]

1. Klik in **Solution Explorer**met de rechtermuisknop op het project en selecteer **NuGet-pakketten beheren.** **Selecteer**bladeren en kies **WindowsAzure.ServiceBus**in **NuGet Package Manager**. Selecteer **Installeren**en accepteer de gebruiksvoorwaarden.

    ![ServiceBuspakket][3]

   Dit pakket voegt automatisch verwijzingen toe naar de Service `System.ServiceModel`Bus-bibliotheken en de WCF. [System.ServiceModel](/dotnet/api/system.servicemodel) is de naamruimte die programmatisch toegang biedt tot de basisfuncties van WCF. Service Bus maakt gebruik van veel van de objecten en kenmerken van WCF om servicecontracten te definiëren.

1. Voeg de `using` volgende instructies toe aan de bovenkant van *Program.cs:*

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Verander de standaardnaam `EchoService` van de naamruimte in `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > In deze zelfstudie wordt `Microsoft.ServiceBus.Samples` de naamruimte C# gebruikt, de naamruimte van het beheerde type contract dat wordt gebruikt in het configuratiebestand in de sectie [WcF-client configureren.](#configure-the-wcf-client) U elke gewenste naamruimte opgeven wanneer u dit voorbeeld maakt. De zelfstudie werkt echter niet, tenzij u vervolgens de naamruimten van het contract en de service dienovereenkomstig wijzigt in het configuratiebestand van de toepassing. De naamruimte die is opgegeven in het *bestand App.config* moet dezelfde zijn als de naamruimte die is opgegeven in uw C#-bestanden.
   >

1. Definieer direct `Microsoft.ServiceBus.Samples` na de naamruimtedeclaratie, maar binnen `IEchoContract` de `ServiceContractAttribute` naamruimte, een nieuwe interface `https://samples.microsoft.com/ServiceModel/Relay/`met de naam en pas het kenmerk toe op de interface met een naamruimtewaarde van . Plak de volgende code na de naamruimte-vermelding:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    De naamruimtewaarde verschilt van de naamruimte die u in uw code gebruikt. In plaats daarvan wordt de naamruimtewaarde gebruikt als een unieke id voor dit contract. Door de naamruimte expliciet op te geven, wordt voorkomen dat de standaardnaamruimtewaarde wordt toegevoegd aan de naam van het contract.

   > [!NOTE]
   > De naamruimte van het servicecontract bevat meestal een schematische naam die ook versie-informatie bevat. Als u versie-informatie in de naamruimte van het servicecontract gebruikt, kunnen services grote wijzigingen isoleren door een nieuw servicecontract te definiëren met een nieuwe naamruimte en het beschikbaar te maken op een nieuw eindpunt. Op deze manier kunnen klanten het oude servicecontract blijven gebruiken zonder dat ze hoeven te worden bijgewerkt. De versie-informatie kan bestaan uit een datum of een buildnummer. Zie [Serviceversiebeheer](/dotnet/framework/wcf/service-versioning) voor meer informatie. Voor deze zelfstudie bevat het naamgevingsschema van de naamruimte voor servicecontracten geen versie-informatie.
   >

1. Declareer `IEchoContract` binnen de interface een methode `IEchoContract` voor de enkele bewerking `OperationContractAttribute` die het contract in de interface blootlegt en pas het kenmerk toe op de methode die u als onderdeel van het openbare WCF Relay-contract wilt blootstellen, als volgt:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Direct na het definiëren van de `IEchoContract`-interface declareert u een kanaal dat eigenschappen overneemt van de interfaces `IEchoContract` en `IClientChannel`. Ga als volgt te werk:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Een kanaal is het WCF-object waarmee de host en de client informatie aan elkaar doorgeven. Later schrijf je code tegen het kanaal om informatie tussen de twee toepassingen te herhalen.

1. Selecteer**Build-oplossing** **bouwen** > of selecteer Ctrl+Shift+B om de nauwkeurigheid van uw werk tot nu toe te bevestigen.

### <a name="example-of-a-wcf-contract"></a>Voorbeeld van een WCF-contract

De volgende code toont een basisinterface die een WCF Relay-contract definieert.

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

## <a name="implement-the-wcf-contract"></a>Uitvoering van het WCF-contract

Als u een Azure-relay maakt, moet u het contract eerst maken met behulp van een interface. Zie de vorige sectie voor meer informatie over het maken van de interface. De volgende procedure implementeert de interface. Deze taak omvat het `EchoService` maken van een klasse `IEchoContract` met de naam die de door de gebruiker gedefinieerde interface implementeert. Nadat u de interface hebt geïmplementeerd, configureert u de interface met behulp van een *App.config-configuratiebestand.* Het configuratiebestand bevat de nodige informatie voor de toepassing. Deze informatie omvat de naam van de service, de naam van het contract en het type protocol dat wordt gebruikt om te communiceren met de relayservice. De code die voor deze taken wordt gebruikt, wordt geleverd in het voorbeeld dat de procedure volgt. Zie [Servicecontracten](/dotnet/framework/wcf/implementing-service-contracts)implementeren voor een meer algemene discussie over het implementeren van een servicecontract.

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

1. Selecteer**Build-oplossing** **bouwen** > of selecteer Ctrl+Shift+B.

### <a name="define-the-configuration-for-the-service-host"></a>De configuratie voor de servicehost definiëren

Het configuratiebestand is vergelijkbaar met een WCF-configuratiebestand. Het bevat de servicenaam, het eindpunt en de binding. Het eindpunt is de locatie die Azure Relay blootlegt voor clients en hosts om met elkaar te communiceren. De binding is het type protocol dat wordt gebruikt om te communiceren. Het belangrijkste verschil is dat dit geconfigureerde serviceeindpunt verwijst naar een [NetTcpRelayBinding-binding,](/dotnet/api/microsoft.servicebus.nettcprelaybinding) die geen deel uitmaakt van het .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) is een van de bindingen die door de service zijn gedefinieerd.

1. Dubbelklik in **Solution Explorer**op **App.config** om het bestand in de Visual Studio-editor te openen.
1. Vervang in het element `<appSettings>` de tijdelijke aanduidingen door de naam van uw servicenaamruimte en de SAS-sleutel die u in een eerdere stap hebt gekopieerd.
1. In de `<system.serviceModel>`-tags voegt u een `<services>`-element toe. U meerdere relaytoepassingen definiëren in één configuratiebestand. In deze zelfstudie wordt er echter maar één gedefinieerd.

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

    Het eindpunt definieert waar de client zoekt naar de hosttoepassing. Later gebruikt de zelfstudie deze stap om een URI te maken die de host volledig blootlegt via Azure Relay. De binding verklaart dat we TCP gebruiken als protocol om te communiceren met de relayservice.

1. Selecteer**Build-oplossing** **bouwen** > of selecteer Ctrl+Shift+B om de nauwkeurigheid van uw werk tot nu toe te bevestigen.

### <a name="example-of-implementation-of-a-service-contract"></a>Voorbeeld van de uitvoering van een servicecontract

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

De volgende code toont de basisindeling van het *app.config-bestand* dat is gekoppeld aan de servicehost.

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

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Host en voer de WCF-service uit om zich te registreren bij de relayservice

In deze stap wordt beschreven hoe u een Azure Relay-service uitvoert.

### <a name="create-the-relay-credentials"></a>De relayreferenties maken

1. Maak in `Main()` twee variabelen waarin de naamruimte en de SAS-sleutel uit het consolevenster worden opgeslagen.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    De SAS-sleutel wordt later gebruikt om toegang te krijgen tot uw project. De naamruimte wordt als parameter doorgegeven aan `CreateServiceUri` voor het maken van een service-URI.

1. Als u een object [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) gebruikt, verklaart u dat u een SAS-sleutel gebruikt als referentietype. Voeg de volgende code toe direct na de code die u in de vorige stap hebt toegevoegd.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Een basisadres voor de service maken

Nadat de code die u in `Uri` de vorige sectie hebt toegevoegd, maakt u een instantie voor het basisadres van de service. Deze URI bevat het Service Bus-schema, de naamruimte en het pad naar de service-interface.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

De waarde "sb" is een afkorting voor de Service Bus regeling. Het geeft aan dat we TCP als protocol gebruiken. Dit schema werd ook eerder aangegeven in het configuratiebestand, toen [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) als de binding werd opgegeven.

De URI voor deze zelfstudie is `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>De servicehost maken en configureren

1. Nog steeds `Main()`bezig in, stel `AutoDetect`de connectiviteit modus op .

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    De connectiviteitsmodus beschrijft het protocol dat de service gebruikt om met de relayservice te communiceren. http of TCP. Met de `AutoDetect`standaardinstelling probeert de service verbinding te maken met Azure Relay via TCP als deze beschikbaar is en HTTP als TCP niet beschikbaar is. Dit resultaat verschilt van het protocol dat de service opgeeft voor clientcommunicatie. Dat protocol wordt bepaald op basis van de gebruikte binding. Een service kan bijvoorbeeld de [binding BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) gebruiken, die aangeeft dat het eindpunt communiceert met clients via HTTP. Dezelfde service kan `ConnectivityMode.AutoDetect` opgeven zodat de service communiceert met Azure Relay via TCP.

1. Maak de servicehost met de URI die u eerder in dit gedeelte hebt gemaakt.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    De servicehost is het WCF-object dat de service start. Hier geeft u het type service door dat `EchoService` u wilt maken, een type en ook aan het adres waarop u de service wilt blootleggen.

1. Voeg boven aan het *Program.cs-bestand* verwijzingen toe naar [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) en [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Configureer vervolgens in `Main()` het eindpunt voor openbare toegang.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Deze stap informeert de relayservice dat uw aanvraag openbaar kan worden gevonden door de Atom-feed voor uw project te onderzoeken. Als u `DiscoveryType` `private`ingesteld op , een client kan nog steeds toegang tot de service. De service wordt echter niet weergegeven wanneer `Relay` deze de naamruimte doorzoekt. In plaats daarvan moet de client op voorhand al op de hoogte zijn van het eindpuntpad.

1. Pas de servicereferenties toe op de serviceeindpunten die zijn gedefinieerd in het bestand *App.config:*

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Zoals eerder vermeld, u meerdere services en eindpunten in het configuratiebestand hebben gedeclareerd. Als dit het geval is, is met de code het hele configuratiebestand doorzocht naar eindpunten waarop uw referenties kunnen worden toegepast. Voor deze zelfstudie heeft het configuratiebestand slechts één eindpunt.

### <a name="open-the-service-host"></a>De servicehost openen

1. Voeg `Main()`nog steeds in , voeg de volgende regel toe om de service te openen.

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

1. Selecteer Ctrl+Shift+B om het project te bouwen.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Voorbeeld dat een service host in een consoletoepassing

Uw ingevulde servicecode moet als volgt worden weergegeven. De code bevat het servicecontract en de implementatie van eerdere stappen in de zelfstudie en host de service in een consoletoepassing.

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

De volgende taak is het maken van een clienttoepassing en het definiëren van het servicecontract dat u later zult implementeren. Deze stappen lijken op de stappen die worden gebruikt om een service te maken: een contract definiëren, een *App.config-bestand* bewerken, referenties gebruiken om verbinding te maken met de relayservice, enzovoort. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

1. Maak een nieuw project in de huidige Visual Studio-oplossing voor de klant:

   1. Klik in **Solution Explorer**met de rechtermuisknop op de huidige oplossing (niet op het project) en selecteer Nieuw**project** **toevoegen** > .
   1. Selecteer **console-app (.NET Framework)** voor C#en selecteer **Volgende**in **Een nieuw project toevoegen**.
   1. Naam project *EchoClient* en selecteer **Maken**.

1. Dubbelklik in **Solution Explorer**in het **EchoClient-project** op **Program.cs** om het bestand in de editor te openen als het nog niet is geopend.
1. Verander de standaardnaam `EchoClient` van de naamruimte in `Microsoft.ServiceBus.Samples`.
1. Installeer het [Service Bus NuGet-pakket:](https://www.nuget.org/packages/WindowsAzure.ServiceBus)

   1. Klik in **Solution Explorer**met de rechtermuisknop op **EchoClient** en selecteer **NuGet-pakketten beheren**.
   1. Selecteer **Bladeren**en zoek naar en selecteer **WindowsAzure.ServiceBus**. Selecteer **Installeren**en accepteer de gebruiksvoorwaarden.

      ![Servicebuspakket installeren][4]

1. Voeg `using` een instructie toe voor de naamruimte [van System.ServiceModel](/dotnet/api/system.servicemodel) in het *Program.cs* bestand.

    ```csharp
    using System.ServiceModel;
    ```

1. Voeg de definitie van het servicecontract toe aan de naamruimte, zoals wordt weergegeven in het volgende voorbeeld. Deze definitie is identiek aan de definitie die in het **Service-project** wordt gebruikt. Voeg deze code toe `Microsoft.ServiceBus.Samples` boven aan de naamruimte.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Selecteer Ctrl+Shift+B om de client te bouwen.

### <a name="example-of-the-echoclient-project"></a>Voorbeeld van het EchoClient-project

De volgende code toont de huidige status van het *Program.cs* bestand in het **EchoClient-project.**

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

In deze stap maakt u een *App.config-bestand* voor een basisclienttoepassing die toegang heeft tot de service die eerder in deze zelfstudie is gemaakt. Dit *app.config-bestand* definieert het contract, de binding en de naam van het eindpunt. In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

1. Dubbelklik in **Solution Explorer**in het **EchoClient-project** op **App.config** om het bestand in de Visual Studio-editor te openen.
1. Vervang in het element `<appSettings>` de tijdelijke aanduidingen door de naam van uw servicenaamruimte en de SAS-sleutel die u in een eerdere stap hebt gekopieerd.
1. Voeg `system.serviceModel` binnen het `<client>` element een element toe.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Deze code verklaart dat u een clienttoepassing in WCF-stijl definieert.

1. Geef in het `client`-element de naam, het contract en het type binding op voor het eindpunt.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Deze code definieert de naam van het eindpunt. Het definieert ook het contract dat in de service is gedefinieerd en het feit dat de clienttoepassing TCP gebruikt om te communiceren met Azure Relay. De naam van het eindpunt wordt in de volgende stap gebruikt om deze eindpuntconfiguratie te koppelen aan de service-URI.

1. Selecteer**Alles opslaan van** **bestand** > .

### <a name="example-of-the-appconfig-file"></a>Voorbeeld van het bestand App.config

De volgende code toont het *app.config-bestand* voor de Echo-client.

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

In deze sectie implementeert u een basisclienttoepassing die toegang heeft tot de service die u eerder in deze zelfstudie hebt gemaakt. Net als bij de service doet de client veel van dezelfde bewerkingen om toegang te krijgen tot Azure Relay:

* De connectiviteitsmodus wordt ingesteld.
* De URI wordt gemaakt waarmee de hostservice wordt gezocht.
* De beveiligingsreferenties worden gedefinieerd.
* De referenties worden toegepast op de verbinding.
* De verbinding wordt geopend.
* De toepassingsspecifieke taken worden uitgevoerd.
* De verbinding wordt gesloten.

Een van de belangrijkste verschillen is echter dat de clienttoepassing een kanaal gebruikt om verbinding te maken met de relayservice. De service maakt gebruik van een oproep naar **ServiceHost.** In het voorbeeld na de procedure wordt de code weergegeven die voor deze taken wordt gebruikt.

### <a name="implement-a-client-application"></a>Een clienttoepassing implementeren

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

1. Maak de URI die de locatie van de host in uw relayproject definieert.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Maak het referentieobject voor het eindpunt van uw servicenaamruimte.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Maak de kanaalfabriek die de configuratie laadt die is beschreven in het *bestand App.config.*

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Een kanaalfactory is een WCF-object dat een kanaal maakt waardoor de service- en clienttoepassingen communiceren.

1. Pas de referenties toe.

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

    De code gebruikt de instantie van het kanaalobject als proxy voor de service.

1. Sluit het kanaal en sluit de factory.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Voorbeeldcode voor deze zelfstudie

Uw voltooide code moet als volgt worden weergegeven. Deze code laat zien hoe u een clienttoepassing maakt, hoe u de bewerkingen van de service aanroepen en hoe u de client sluiten nadat de bewerkingsoproep is voltooid.

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

1. Selecteer Ctrl+Shift+B om de oplossing te bouwen. Met deze actie wordt zowel het clientproject als het serviceproject gebouwd dat u in de vorige stappen hebt gemaakt.
1. Voordat u de clienttoepassing uitvoert, moet u ervoor zorgen dat de servicetoepassing wordt uitgevoerd. Klik in **Solution Explorer**met de rechtermuisknop op de **EchoService-oplossing** en selecteer **Eigenschappen**.
1. Kies **in Eigenschappagina's** **, Common Properties** > **Startup Project**en kies Meerdere **opstartprojecten**. Controleer of **EchoService** bovenaan staat in de lijst.
1. Stel het vak **Actie** van de projecten **EchoService** en **EchoClient** in op **Start**.

    ![Eigenschappenpagina's van project][5]

1. Selecteer **Projectafhankelijkheden**. Selecteer **EchoClient**in **Projecten**. Controleer **bij Depends**of **EchoService** is geselecteerd.

    ![Projectafhankelijkheden][6]

1. Selecteer **OK** om **Eigenschappagina's**te sluiten .
1. Selecteer F5 om beide projecten uit te voeren.
1. Beide consolevensters worden geopend en u wordt gevraagd de naam van de naamruimte op te geven. De service moet eerst worden uitgevoerd, dus voer in het **EchoService-consolevenster** de naamruimte in en selecteer Enter.
1. Vervolgens vraagt de console u om uw SAS-sleutel. Voer de SAS-toets in en selecteer Enter.

    Hier volgt een voorbeeld van de uitvoer van het consolevenster. De waarden hier zijn slechts voorbeelden.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    De servicetoepassing geeft in het consolevenster het adres weer dat wordt gebruikt, zoals in het volgende voorbeeld te zien is.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Voer in het consolevenster **EchoClient** dezelfde gegevens in als de gegevens die u eerder al hebt ingevoerd voor de servicetoepassing. Voer dezelfde servicenaamruimte en SAS-sleutelwaarden in voor de clienttoepassing.
1. Wanneer u deze waarden hebt ingevoerd, opent de client een kanaal naar de service en wordt u gevraagd om tekst in te voeren, zoals te zien is in het volgende console-uitvoervoorbeeld.

    `Enter text to echo (or [Enter] to exit):`

    Voer tekst in die u naar de servicetoepassing wilt verzenden en selecteer Enter. Deze tekst wordt naar de service verzonden via een Echo-servicebewerking en wordt in het serviceconsolevenster weergegeven, zoals in de volgende voorbeelduitvoer te zien is.

    `Echoing: My sample text`

    De clienttoepassing ontvangt de geretourneerde waarde van de `Echo`-bewerking: de oorspronkelijke tekst. Deze wordt vervolgens weergegeven in het consolevenster. De volgende tekst is voorbeelduitvoer vanuit het venster van de clientconsole.

    `Server echoed: My sample text`

1. U kunt op deze manier doorgaan met het verzenden van berichten van de client naar de service. Wanneer u klaar bent, selecteert u Enter in de vensters van de client- en serviceconsole om beide toepassingen te beëindigen.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Een on-premises WCF REST-service zichtbaar maken voor een client buiten uw netwerk](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
