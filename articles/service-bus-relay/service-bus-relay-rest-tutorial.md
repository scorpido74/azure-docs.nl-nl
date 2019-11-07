---
title: 'Zelf studie: zelf studie RESTen met Azure Relay'
description: 'Zelf studie: een Azure Service Bus Relay Host-toepassing bouwen die een op REST gebaseerde interface beschikbaar stelt.'
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
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718828"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Zelf studie: zelf studie over Azure WCF Relay REST

In deze zelf studie wordt beschreven hoe u een Azure Relay-hosttoepassing bouwt die een op REST gebaseerde interface beschrijft. Met REST kan een webclient, zoals een webbrowser, toegang krijgen tot de Service Bus-API's via HTTP-aanvragen.

De zelf studie maakt gebruik van het Windows Communication Foundation (WCF) REST programmeer model voor het bouwen van een REST-service op Azure Relay. Zie voor meer informatie [WCF rest programming model](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) en het [ontwerpen en implementeren van services](/dotnet/framework/wcf/designing-and-implementing-services).

In deze zelf studie voert u de volgende taken uit:

> [!div class="checklist"]
>
> * Installeer de vereisten voor deze zelf studie.
> * Een relay-naam ruimte maken.
> * Definieer een op REST gebaseerd WCF-service contract.
> * Implementeer het op REST gebaseerde WCF-contract.
> * Host en voer de op REST gebaseerde WCF-service uit.
> * De service uitvoeren en testen.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com). In de voor beelden in deze zelf studie wordt gebruikgemaakt van Visual Studio 2019.
* Azure-SDK voor .NET. Installeer de SDK via de [SDK-downloadpagina](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Een relay-naam ruimte maken

Als u de relayfuncties in Azure wilt gebruiken, moet u eerst een servicenaamruimte maken. Een naamruimte biedt een scoping container voor het verwerken van Azure-resources in uw toepassing. Volg [deze instructies](relay-create-namespace-portal.md) om een Relay-naamruimte te maken.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definieer een op REST gebaseerd WCF-service contract voor gebruik met Azure Relay

Wanneer u een service voor de REST-stijl van WCF maakt, moet u het contract definiëren. Het contract geeft aan welke bewerkingen door de host worden ondersteund. Een service bewerking lijkt op een webservicemethode. Definieer een contract met een C++- C#,-of Visual Basic-interface. Elke methode in de interface komt overeen met een specifieke servicebewerking. Pas het kenmerk [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) toe op elke interface en pas het kenmerk [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) toe op elke bewerking. 

> [!TIP]
> Als een methode in een interface met [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) niet de [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute)heeft, wordt die methode niet weer gegeven. De code die wordt gebruikt voor deze taken wordt weer gegeven in het voor beeld na de procedure.

Het belangrijkste verschil tussen een WCF-contract en een REST-stijl contract is het toevoegen van een eigenschap aan de [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Met deze eigenschap kunt u een methode in uw interface toewijzen aan een methode aan de andere kant van de interface. In dit voor beeld wordt het attributes [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) gebruikt om een methode te koppelen aan `HTTP GET`. Deze aanpak zorgt ervoor Service Bus het nauw keurig ophalen en interpreteren van opdrachten die naar de interface worden verzonden.

### <a name="to-create-a-contract-with-an-interface"></a>Een contract met een interface maken

1. Start micro soft Visual Studio als beheerder. Als u dit wilt doen, klikt u met de rechter muisknop op het pictogram van het Visual Studio-programma en selecteert u **als administrator uitvoeren**.
1. Selecteer in Visual Studio **een nieuw project maken**.
1. Kies in **een nieuw project maken de**optie **console-app (.NET Framework)** voor C# en selecteer **volgende**.
1. Geef het project de naam *ImageListener*. Gebruik de standaard **locatie**en selecteer vervolgens **maken**.

   Voor een C# project maakt Visual Studio een *Program.cs* -bestand. Deze klasse bevat een lege `Main()`-methode, vereist voor de juiste opbouw van een consoletoepassingsproject.

1. Klik in **Solution Explorer**met de rechter muisknop op het project **ImageListener** en selecteer vervolgens **NuGet-pakketten beheren**.
1. Selecteer **Bladeren**, zoek naar en kies **WindowsAzure. ServiceBus**. Selecteer **installeren**en ga akkoord met de gebruiks voorwaarden.

    In deze stap worden referenties toegevoegd aan Service Bus en *System. service model. dll*. Met dit pakket worden automatisch verwijzingen naar de Service Bus-bibliotheken en de WCF-`System.ServiceModel`toegevoegd.

1. Voeg expliciet een verwijzing naar `System.ServiceModel.Web.dll` toe aan het project. Klik in **Solution Explorer**met de rechter muisknop op **verwijzingen** onder de projectmap en selecteer **referentie toevoegen**.
1. In **referentie toevoegen**selecteert u **Framework** en voert u *System. service model. Web* in **Zoek opdracht**in. Schakel het selectievakje **System.ServiceModel.Web** in en klik vervolgens op **OK**.

Maak vervolgens de volgende code wijzigingen in het project:

1. Voeg boven aan het *Program.cs* -bestand de volgende `using`-instructies toe.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) is de naamruimte die programmatisch toegang biedt tot de basisfuncties van WCF. WCF Relay maakt gebruik van veel van de objecten en kenmerken van WCF om service contracten te definiëren. U gebruikt deze naam ruimte in de meeste van uw relay-toepassingen.
    * [System. service model. channels](/dotnet/api/system.servicemodel.channels) helpt u bij het definiëren van het kanaal. Dit is het object waarmee u communiceert met Azure relay en de webbrowser van de client.
    * [System. service model. Web](/dotnet/api/system.servicemodel.web) bevat de typen waarmee u webtoepassingen kunt maken.

1. Wijzig de naam van de `ImageListener` naam ruimte in `Microsoft.ServiceBus.Samples`.

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Definieer direct na de openings accolade van de naam ruimte declaratie een nieuwe interface met de naam `IImageContract` en pas het kenmerk `ServiceContractAttribute` toe op de interface met de waarde `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    De naamruimtewaarde verschilt van de naamruimte die u in uw code gebruikt. De naam ruimte waarde is een unieke id voor dit contract en moet versie-informatie bevatten. Zie [Serviceversiebeheer](/dotnet/framework/wcf/service-versioning) voor meer informatie. Door de naamruimte expliciet op te geven, wordt voorkomen dat de standaardnaamruimtewaarde wordt toegevoegd aan de naam van het contract.

1. In de `IImageContract`-interface declareert u een methode voor de enkelvoudige bewerking die door het `IImageContract` contract in de interface wordt weer gegeven en past u het `OperationContract` kenmerk toe op de methode die u als onderdeel van het open bare Service Bus contract wilt weer geven.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. Voeg in het kenmerk `OperationContract` de `WebGet` waarde toe.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Door de `WebGet` waarde toe te voegen, kan de Relay-service HTTP GET-aanvragen naar `GetImage`routeren en de retour waarden van `GetImage` vertalen in een `HTTP GETRESPONSE`-antwoord. Verderop in de zelf studie gebruikt u een webbrowser om toegang te krijgen tot deze methode en om de afbeelding weer te geven in de browser.

1. Declareer direct na de `IImageContract`-definitie een kanaal dat de eigenschappen overneemt van de `IImageContract`- en `IClientChannel`-interface.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Een kanaal is het WCF-object waarmee de service en de client informatie aan elkaar doorgeven. Later maakt u het kanaal in uw host-toepassing. Azure Relay maakt vervolgens gebruik van dit kanaal om de HTTP GET-aanvragen van de browser door te geven aan uw `GetImage`-implementatie. De relay gebruikt ook het kanaal om de `GetImage` retour waarde te nemen en deze te vertalen in een `HTTP GETRESPONSE` voor de client browser.

1. Selecteer **build** > **Build-oplossing** om de nauw keurigheid van uw werk tot nu toe te bevestigen.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Voor beeld dat een WCF Relay contract definieert

De volgende code toont een basis interface die een WCF Relay contract definieert.

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

## <a name="implement-the-rest-based-wcf-service-contract"></a>Het op REST gebaseerde WCF-service contract implementeren

Als u een REST-stijl WCF Relay service wilt maken, moet u eerst het contract maken met behulp van een interface. De volgende stap is het implementeren van de interface. Deze procedure omvat het maken van een klasse met de naam `ImageService` die de door de gebruiker gedefinieerde `IImageContract` interface implementeert. Nadat u het contract hebt geïmplementeerd, configureert u de interface met behulp van een *app. config* -bestand. Het configuratie bestand bevat de benodigde informatie voor de toepassing. Deze informatie bevat de naam van de service, de naam van het contract en het type protocol dat wordt gebruikt om te communiceren met de Relay-service. De code die wordt gebruikt voor deze taken wordt weer gegeven in het voor beeld na de procedure.

Net als bij de vorige stappen is er weinig verschil tussen het implementeren van een REST-stijl contract en een WCF Relay-contract.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Een Service Bus-contract in REST-stijl implementeren

1. Maak een nieuwe klasse met de naam `ImageService` direct nadat de `IImageContract`-interface is gedefinieerd. Met de klasse `ImageService` wordt de `IImageContract`-interface geïmplementeerd.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Net als bij andere interface-implementaties kunt u de definitie implementeren in een ander bestand. In deze zelfstudie wordt de implementatie echter weergegeven in hetzelfde bestand als de interfacedefinitie en de `Main()`-methode.

1. Pas het kenmerk [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) toe op de klasse `IImageService` om aan te geven dat de klasse een implementatie van een WCF-contract is.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Zoals eerder vermeld, is deze naam ruimte geen traditionele naam ruimte. Het maakt deel uit van de WCF-architectuur waarmee het contract wordt geïdentificeerd. Zie voor meer informatie de naam van het [gegevens contract](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Voeg een *jpg* -afbeelding toe aan uw project. Dit bestand is een afbeelding die door de service wordt weer gegeven in de ontvangende browser.

   1. Klik met de rechter muisknop op het project en selecteer **toevoegen**.
   1. Selecteer vervolgens **bestaand item**.
   1. Gebruik **bestaand item toevoegen** om naar een geschikte. jpg te bladeren en selecteer vervolgens **toevoegen**. Selecteer bij het toevoegen van het bestand **alle bestanden** in de vervolg keuzelijst naast **Bestands naam**.

   In de rest van deze zelf studie wordt ervan uitgegaan dat de naam van de afbeelding *afbeelding. jpg*is. Als u een ander bestand hebt, moet u de naam van de installatie kopie wijzigen of uw code wijzigen om te compenseren.

1. Als u ervoor wilt zorgen dat de actieve service het afbeeldings bestand kan vinden, klikt u in **Solution Explorer** met de rechter muisknop op het afbeeldings bestand en kiest u **Eigenschappen**. Stel in **Eigenschappen** **kopiëren naar uitvoermap** in op **kopiëren indien nieuwer**.

1. Gebruik de procedure in [om een contract met een interface te maken](#to-create-a-contract-with-an-interface) om een verwijzing naar de *System. Drawing. dll* -assembly naar het project toe te voegen.

1. Voeg de volgende gekoppelde `using`-instructies toe:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. Voeg in de klasse `ImageService` de volgende constructor toe waarmee de bitmap wordt geladen en wordt voor bereid voor verzen ding naar de client browser:

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

1. Voeg direct na de vorige code de volgende `GetImage` methode toe aan de klasse `ImageService` om een HTTP-bericht te retour neren dat de installatie kopie bevat.

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

    Deze implementatie maakt gebruik van `MemoryStream` om de installatie kopie op te halen en deze voor te bereiden voor streamen naar de browser. De stroom positie wordt gestart op nul, declareert de stream-inhoud als een *. jpg*en streamt de informatie.

1. Selecteer **build** > **Build-oplossing**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>De configuratie voor het uitvoeren van de webservice in Service Bus definiëren

1. Dubbel klik in **Solution Explorer**op **app. config** om het bestand te openen in de Visual Studio-editor.

    Het bestand *app. config* bevat de service naam, het eind punt en de binding. Het eind punt is de locatie Azure Relay wordt weer gegeven voor clients en hosts om met elkaar te communiceren. De binding is het type protocol dat wordt gebruikt om te communiceren. Het belangrijkste verschil is hier dat het geconfigureerde service-eind punt verwijst naar een [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) -binding.

1. Het `<system.serviceModel>` XML-element is een WCF-element waarmee een of meer services worden gedefinieerd. Hier wordt de service naam en het eind punt gedefinieerd. Voeg aan de onderkant van het `<system.serviceModel>`-element, maar nog steeds binnen `<system.serviceModel>`een `<bindings>`-element toe met de volgende inhoud:

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

    Deze inhoud definieert de bindingen die in de toepassing worden gebruikt. U kunt meerdere bindingen definiëren, maar voor deze zelf studie definieert u slechts één.

    De vorige code definieert een WCF Relay [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) -binding met `relayClientAuthenticationType` ingesteld op `None`. Deze instelling geeft aan dat een eind punt dat deze binding gebruikt geen client referentie vereist.

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

    Met deze inhoud wordt een service geconfigureerd die gebruikmaakt van de eerder gedefinieerde standaard `webHttpRelayBinding`. Ook wordt de standaard `sbTokenProvider`gebruikt, die in de volgende stap is gedefinieerd.

1. Maak na het `<services>`-element een `<behaviors>`-element met de volgende inhoud en vervang `SAS_KEY` door de SAS-sleutel (Shared Access Signature). Zie [beheer referenties ophalen](service-bus-relay-tutorial.md#get-management-credentials)voor het verkrijgen van een SAS-sleutel van de [Azure Portal][Azure portal].

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

1. In *app. config*, in het element `<appSettings>`, vervangt u de gehele Connection String waarde door de Connection String die u eerder hebt verkregen via de portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Selecteer **build** > **Build-oplossing** om de volledige oplossing te bouwen.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Voor beeld dat het op REST gebaseerde WCF-service contract implementeert

De volgende code toont de contract-en service-implementatie voor een REST-gebaseerde service die wordt uitgevoerd op Service Bus met behulp van de `WebHttpRelayBinding`-binding.

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

In het volgende voor beeld ziet u het bestand *app. config* dat is gekoppeld aan de service.

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

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Host de op REST gebaseerde WCF-service voor het gebruik van Azure Relay

In deze sectie wordt beschreven hoe u een webservice uitvoert met behulp van een console toepassing met WCF Relay. In het voor beeld na de procedure wordt een volledig overzicht gegeven van de code die in deze sectie is geschreven.

### <a name="to-create-a-base-address-for-the-service"></a>Een basisadres voor de service maken

1. Maak in de declaratie van de `Main()`-functie een variabele om de naam ruimte van uw project op te slaan. Zorg ervoor dat u `yourNamespace` vervangt door de naam van de relay naam ruimte die u eerder hebt gemaakt.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus gebruikt de naam van uw naamruimte om een unieke URI te maken.

1. Maak een `Uri`-exemplaar voor het basisadres van de service die is gebaseerd op de naamruimte.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>De webservicehost maken en configureren

Maak in `Main()`nog de webservice-host met behulp van het URI-adres dat eerder in deze sectie is gemaakt.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

De servicehost is het WCF-object waarmee de hosttoepassing wordt gestart. In dit voor beeld wordt het type host door gegeven dat u wilt maken. Dit is een `ImageService`en ook het adres waarop u de hosttoepassing wilt weer geven.

### <a name="to-run-the-web-service-host"></a>De webservicehost uitvoeren

1. Voeg nog steeds in `Main()`de volgende regel toe om de service te openen.

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

### <a name="example-of-the-service-contract-and-implementation"></a>Voor beeld van het service contract en de implementatie

Het volgende voorbeeld bevat het servicecontract en de implementatie uit de vorige stappen in de zelfstudie. Hierin wordt de service gehost in een consoletoepassing. Compileer de volgende code in een uitvoerbaar bestand met de naam *ImageListener. exe*.

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

1. Selecteer F5 of blader naar de locatie van het uitvoer bare bestand, *ImageListener\bin\Debug\ImageListener.exe*om de service uit te voeren. Zorg ervoor dat de app wordt uitgevoerd, omdat deze is vereist voor de volgende stap.
1. Kopieer en plak het adres van de opdrachtprompt in een browser om de afbeelding te zien.
1. Wanneer u klaar bent, selecteert u Enter in het opdracht prompt venster om de app te sluiten.

## <a name="next-steps"></a>Volgende stappen

Nu u een toepassing hebt gebouwd die gebruikmaakt van de Azure Relay-service, raadpleegt u de volgende artikelen voor meer informatie:

* [Wat is Azure Relay?](relay-what-is-it.md)
* [Een on-premises WCF REST-service beschikbaar maken voor externe clients met behulp van Azure WCF Relay](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
