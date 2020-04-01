---
title: 'Zelfstudie: ZELFstudie REST met Azure Relay'
description: 'Zelfstudie: Maak een Azure Service Bus Relay-hosttoepassing die een op RUST gebaseerde interface blootlegt.'
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 229ed2b00582f2c73ce68c47406d68325abda736
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718828"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Zelfstudie: Zelfstudie Azure WCF Relay REST

In deze zelfstudie wordt beschreven hoe u een Azure Relay-hosttoepassing maken die een op RUST gebaseerde interface blootlegt. Met REST kan een webclient, zoals een webbrowser, toegang krijgen tot de Service Bus-API's via HTTP-aanvragen.

De zelfstudie maakt gebruik van het rest-programmeermodel (Windows Communication Foundation) om een REST-service op Azure Relay te bouwen. Zie [WCF REST Programming Model](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) and [Designing and Implementing Services](/dotnet/framework/wcf/designing-and-implementing-services)voor meer informatie.

U doet de volgende taken in deze zelfstudie:

> [!div class="checklist"]
>
> * Installeer vereisten voor deze zelfstudie.
> * Maak een naamruimte voor relais.
> * Definieer een WCF-servicecontract op basis van rest.
> * Implementeer het WCF-contract op basis van REST.
> * Host en voer de WCF-service uit op basis van REST.
> * Voer de service uit en test deze.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com). De voorbeelden in deze tutorial gebruiken Visual Studio 2019.
* Azure-SDK voor .NET. Installeer de SDK via de [SDK-downloadpagina](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Een naamruimte voor relais maken

Als u de relayfuncties in Azure wilt gebruiken, moet u eerst een servicenaamruimte maken. Een naamruimte biedt een scoping container voor het verwerken van Azure-resources in uw toepassing. Volg [deze instructies](relay-create-namespace-portal.md) om een Relay-naamruimte te maken.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Een WCF-servicecontract op basis van rest definiëren voor gebruik met Azure Relay

Wanneer u een WCF REST-achtige service maakt, moet u het contract definiëren. Het contract geeft aan welke bewerkingen door de host worden ondersteund. Een servicebewerking lijkt op een webservicemethode. Definieer een contract met een C++-, C#- of Visual Basic-interface. Elke methode in de interface komt overeen met een specifieke servicebewerking. Pas het kenmerk [ServiceContractAttribuut](/dotnet/api/system.servicemodel.servicecontractattribute) toe op elke interface en pas het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) toe op elke bewerking. 

> [!TIP]
> Als een methode in een interface met [servicecontractattribuut](/dotnet/api/system.servicemodel.servicecontractattribute) niet het [OperationContractAttribuut](/dotnet/api/system.servicemodel.operationcontractattribute)heeft, wordt die methode niet weergegeven. De code die voor deze taken wordt gebruikt, wordt weergegeven in het voorbeeld volgens de procedure.

Het belangrijkste verschil tussen een WCF-contract en een REST-stijl contract is de toevoeging van een eigenschap aan de [OperationContractAttribute:](/dotnet/api/system.servicemodel.operationcontractattribute) [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Met deze eigenschap kunt u een methode in uw interface toewijzen aan een methode aan de andere kant van de interface. In dit voorbeeld wordt het kenmerk [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) gebruikt om een methode te koppelen aan `HTTP GET`. Met deze aanpak kan Service Bus opdrachten die naar de interface worden verzonden nauwkeurig ophalen en interpreteren.

### <a name="to-create-a-contract-with-an-interface"></a>Een contract maken met een interface

1. Start Microsoft Visual Studio als beheerder. Klik hiervoor met de rechtermuisknop op het programmapictogram van Visual Studio en selecteer **Uitvoeren als beheerder**.
1. Selecteer in Visual Studio De optie **Een nieuw project maken**.
1. Kies in **Een nieuw project maken**de optie Console App **(.NET Framework)** voor C# en selecteer **Volgende**.
1. Geef het project *ImageListener een*naam . Gebruik de **standaardlocatie**en selecteer **Vervolgens Maken**.

   Voor een C#-project maakt Visual Studio een *Program.cs* bestand. Deze klasse bevat een lege `Main()`-methode, vereist voor de juiste opbouw van een consoletoepassingsproject.

1. Klik in **Solution Explorer**met de rechtermuisknop op het project **ImageListener** en selecteer **NuGet-pakketten beheren**.
1. Selecteer **Bladeren**en zoek naar en kies **WindowsAzure.ServiceBus**. Selecteer **Installeren**en accepteer de gebruiksvoorwaarden.

    Deze stap voegt verwijzingen toe naar Service Bus en *System.ServiceModel.dll*. Dit pakket voegt automatisch verwijzingen toe naar de Service `System.ServiceModel`Bus-bibliotheken en de WCF.

1. Voeg expliciet een `System.ServiceModel.Web.dll` verwijzing toe naar het project. Klik in **Solution Explorer**met de rechtermuisknop **op Verwijzingen** onder de projectmap en selecteer **Naslagwaarde toevoegen**.
1. Selecteer **Framework** **toevoegen**en voer *System.ServiceModel.Web* in **Search**in. Schakel het selectievakje **System.ServiceModel.Web** in en klik vervolgens op **OK**.

Breng vervolgens de volgende codewijzigingen aan in het project:

1. Voeg de `using` volgende instructies toe boven aan het *Program.cs* bestand.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) is de naamruimte die programmatisch toegang biedt tot de basisfuncties van WCF. WCF Relay gebruikt veel van de objecten en kenmerken van WCF om servicecontracten te definiëren. U gebruikt deze naamruimte in de meeste van uw relay-toepassingen.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) helpt bij het definiëren van het kanaal, het object waarmee u communiceert met Azure Relay en de webbrowser client.
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) bevat de typen waarmee u webtoepassingen maken.

1. Wijzig de `ImageListener` naam `Microsoft.ServiceBus.Samples`van de naamruimte in .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Direct na de opening krullend brace van de `IImageContract` naamruimte `ServiceContractAttribute` verklaring, definieer een nieuwe `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`interface met de naam en het kenmerk toe te passen op de interface met een waarde van . 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    De naamruimtewaarde verschilt van de naamruimte die u in uw code gebruikt. De waarde voor de naamruimte is een unieke id voor dit contract en moet versie-informatie bevatten. Zie [Serviceversiebeheer](/dotnet/framework/wcf/service-versioning) voor meer informatie. Door de naamruimte expliciet op te geven, wordt voorkomen dat de standaardnaamruimtewaarde wordt toegevoegd aan de naam van het contract.

1. Declareer `IImageContract` binnen de interface een methode `IImageContract` voor de afzonderlijke bewerking die `OperationContract` het contract in de interface blootlegt en pas het kenmerk toe op de methode die u wilt blootstellen als onderdeel van het openbare Service Bus-contract.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. Voeg `OperationContract` in het `WebGet` kenmerk de waarde toe.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Door `WebGet` de waarde toe te voegen, `GetImage`kan de relayservice HTTP `GetImage` GET-aanvragen routeren naar en de retourwaarden vertalen naar een `HTTP GETRESPONSE` antwoord. Later in de zelfstudie gebruikt u een webbrowser om toegang te krijgen tot deze methode en om de afbeelding weer te geven in de browser.

1. Declareer direct na de `IImageContract`-definitie een kanaal dat de eigenschappen overneemt van de `IImageContract`- en `IClientChannel`-interface.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Een kanaal is het WCF-object waarmee de service en de client informatie aan elkaar doorgeven. Later maak je het kanaal in je hosttoepassing. Azure Relay gebruikt dit kanaal vervolgens om de HTTP `GetImage` GET-aanvragen van de browser door te geven aan uw implementatie. Het relais maakt ook gebruik `GetImage` van het kanaal `HTTP GETRESPONSE` om de retourwaarde te nemen en te vertalen naar een voor de client browser.

1. Selecteer **Build** > **Build Solution** om de nauwkeurigheid van uw werk tot nu toe te bevestigen.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Voorbeeld dat een WCF Relay-contract definieert

De volgende code toont een basisinterface die een WCF Relay-contract definieert.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementeren van het WCF-servicecontract op basis van rest

Als u een WCF Relay-service in REST-stijl wilt maken, maakt u het contract eerst met behulp van een interface. De volgende stap is het implementeren van de interface. Deze procedure omvat het `ImageService` maken van een klasse `IImageContract` met de naam die de door de gebruiker gedefinieerde interface implementeert. Nadat u het contract hebt geïmplementeerd, configureert u de interface met behulp van een *App.config-bestand.* Het configuratiebestand bevat de nodige informatie voor de toepassing. Deze informatie omvat de naam van de service, de naam van het contract en het type protocol dat wordt gebruikt om te communiceren met de relayservice. De code die voor deze taken wordt gebruikt, wordt weergegeven in het voorbeeld volgens de procedure.

Net als bij de vorige stappen is er weinig verschil tussen het implementeren van een REST-stijl contract en een WCF Relay contract.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Een Service Bus-contract in REST-stijl implementeren

1. Maak een nieuwe klasse met de naam `ImageService` direct nadat de `IImageContract`-interface is gedefinieerd. Met de klasse `ImageService` wordt de `IImageContract`-interface geïmplementeerd.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Net als bij andere interface-implementaties kunt u de definitie implementeren in een ander bestand. In deze zelfstudie wordt de implementatie echter weergegeven in hetzelfde bestand als de interfacedefinitie en de `Main()`-methode.

1. Pas het kenmerk [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) toe op de `IImageService` klasse om aan te geven dat de klasse een implementatie van een WCF-contract is.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Zoals eerder vermeld, is deze naamruimte geen traditionele naamruimte. Het maakt deel uit van de WCF-architectuur die het contract identificeert. Zie voor meer informatie de [namen van gegevenscontracten](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Voeg een *.jpg-afbeelding* toe aan uw project. Dit bestand is een afbeelding die de service weergeeft in de ontvangende browser.

   1. Klik met de rechtermuisknop op uw project en selecteer **Toevoegen**.
   1. Selecteer vervolgens **Bestaand item**.
   1. Gebruik **Bestaand item toevoegen** om naar een geschikte .jpg te bladeren en selecteer Vervolgens **Toevoegen**. Selecteer **alle bestanden** in de vervolgkeuzelijst naast **Bestandsnaam**wanneer u het bestand toevoegt.

   De rest van deze zelfstudie gaat ervan uit dat de naam van de afbeelding *image.jpg*is. Als u een ander bestand hebt, moet u de naam van de afbeelding wijzigen of de code wijzigen om deze te compenseren.

1. Als u ervoor wilt zorgen dat de beheerde service het afbeeldingsbestand kan vinden, klikt u in **Solution Explorer** met de rechtermuisknop op het afbeeldingsbestand en kiest u **Eigenschappen**. Stel in **Eigenschappen** **kopiëren naar uitvoermap** in als ze **nieuwer zijn**.

1. Gebruik de procedure in [Een contract maken met een interface](#to-create-a-contract-with-an-interface) om een verwijzing naar de *system.drawing.dll-assemblage* aan het project toe te voegen.

1. Voeg de `using` volgende bijbehorende instructies toe:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. Voeg `ImageService` in de klasse de volgende constructor toe die de bitmap laadt en bereidt zich voor om deze naar de clientbrowser te sturen:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Voeg direct na de vorige `GetImage` code `ImageService` de volgende methode in de klasse toe om een HTTP-bericht met de afbeelding terug te sturen.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Deze implementatie `MemoryStream` wordt gebruikt om de afbeelding op te halen en voor te bereiden op streaming naar de browser. Het begint de streampositie op nul, verklaart de streaminhoud als een *.jpg*en streamt de informatie.

1. Selecteer **Build** > **Build-oplossing**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>De configuratie voor het uitvoeren van de webservice in Service Bus definiëren

1. Dubbelklik in **Solution Explorer**op **App.config** om het bestand in de Visual Studio-editor te openen.

    Het *app.config-bestand* bevat de servicenaam, het eindpunt en de binding. Het eindpunt is de locatie die Azure Relay blootlegt voor clients en hosts om met elkaar te communiceren. De binding is het type protocol dat wordt gebruikt om te communiceren. Het belangrijkste verschil hierbij is dat het geconfigureerde serviceeindpunt verwijst naar een [WebHttpRelayBinding-binding.](/dotnet/api/microsoft.servicebus.webhttprelaybinding)

1. Het `<system.serviceModel>` XML-element is een WCF-element waarmee een of meer services worden gedefinieerd. Hier wordt het gebruikt om de servicenaam en het eindpunt te definiëren. Voeg onder aan `<system.serviceModel>` het element, `<system.serviceModel>`maar `<bindings>` nog steeds binnen, een element toe met de volgende inhoud:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Deze inhoud definieert de bindingen die in de toepassing worden gebruikt. U meerdere bindingen definiëren, maar voor deze zelfstudie definieert u er slechts één.

    De vorige code definieert een WCF `relayClientAuthenticationType` Relay `None` [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) binding met ingesteld op . Deze instelling geeft aan dat een eindpunt met deze binding geen clientreferentie vereist.

1. Na het `<bindings>`-element voegt u een `<services>`-element toe. Net zoals bij bindingen kunt u meerdere services definiëren in een enkel configuratiebestand. In deze zelfstudie definieert u slechts een service.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Met deze inhoud configureert u een `webHttpRelayBinding`service die de eerder gedefinieerde standaardinstelling gebruikt. Het maakt ook `sbTokenProvider`gebruik van de standaard , die wordt gedefinieerd in de volgende stap.

1. Maak `<services>` na het `<behaviors>` element een element met `SAS_KEY` de volgende inhoud, vervangen door de SAS-toets (Shared Access Signature). Zie [Beheerreferenties](service-bus-relay-tutorial.md#get-management-credentials)ophalen als u een SAS-sleutel uit de [Azure-portal wilt verkrijgen.][Azure portal]

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Nog steeds in *App.config*, in het `<appSettings>` element, vervang de gehele verbindingstekenreeks waarde met de verbindingstekenreeks die u eerder hebt verkregen van de portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Selecteer **Build** > **Build Solution** om de hele oplossing te bouwen.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Voorbeeld van de implementatie van het WCF-servicecontract op basis van REST

De volgende code toont de contract- en serviceimplementatie voor een restservice die wordt uitgevoerd op Service Bus met behulp van de `WebHttpRelayBinding` binding.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

In het volgende voorbeeld wordt het *app.config-bestand* weergegeven dat aan de service is gekoppeld.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Host de REST-gebaseerde WCF-service om Azure Relay te gebruiken

In deze sectie wordt beschreven hoe u een webservice uitvoert met een consoletoepassing met WCF Relay. Een volledige lijst van de code geschreven in deze sectie wordt weergegeven in het voorbeeld volgens de procedure.

### <a name="to-create-a-base-address-for-the-service"></a>Een basisadres voor de service maken

1. Maak `Main()` in de functiedeclaratie een variabele om de naamruimte van uw project op te slaan. Zorg ervoor `yourNamespace` dat u de naam van de naamruimte Relais die u eerder hebt gemaakt, vervangt.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus gebruikt de naam van uw naamruimte om een unieke URI te maken.

1. Maak een `Uri`-exemplaar voor het basisadres van de service die is gebaseerd op de naamruimte.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>De webservicehost maken en configureren

Nog `Main()`steeds in , maak de webservicehost met behulp van het URI-adres dat eerder in deze sectie is gemaakt.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

De servicehost is het WCF-object waarmee de hosttoepassing wordt gestart. In dit voorbeeld wordt het type host gegeven `ImageService`dat u wilt maken, wat een , en ook het adres is waarop u de hosttoepassing wilt blootstellen.

### <a name="to-run-the-web-service-host"></a>De webservicehost uitvoeren

1. Voeg `Main()`nog steeds in , voeg de volgende regel toe om de service te openen.

    ```csharp
    host.Open();
    ```

    De service wordt nu uitgevoerd.

1. Geef een bericht weer waarin wordt aangegeven dat de service wordt uitgevoerd en hoe deze kan worden gestopt.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Sluit de servicehost wanneer u klaar bent.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Voorbeeld van het servicecontract en de uitvoering

Het volgende voorbeeld bevat het servicecontract en de implementatie uit de vorige stappen in de zelfstudie. Hierin wordt de service gehost in een consoletoepassing. Compileer de volgende code in een uitvoerbare naam *ImageListener.exe*.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>De service uitvoeren en testen

Nadat u de oplossing hebt opgebouwd, gaat u als volgt te werk om de toepassing uit te voeren:

1. Selecteer F5 of blader naar de uitvoerbare bestandslocatie *ImageListener\bin\Debug\ImageListener.exe*, om de service uit te voeren. Houd de app draaiende, omdat deze vereist is voor de volgende stap.
1. Kopieer en plak het adres van de opdrachtprompt in een browser om de afbeelding te zien.
1. Wanneer u klaar bent, selecteert u Enter in het opdrachtpromptvenster om de app te sluiten.

## <a name="next-steps"></a>Volgende stappen

Nu u een toepassing hebt gebouwd die de Azure Relay-service gebruikt, raadpleegt u de volgende artikelen voor meer informatie:

* [Wat is Azure Relay?](relay-what-is-it.md)
* [Een on-premises WCF REST-service blootstellen aan externe client met Azure WCF Relay](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
