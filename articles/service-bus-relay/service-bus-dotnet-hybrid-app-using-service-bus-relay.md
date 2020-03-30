---
title: Azure Windows Communication Foundation (WCF) Relay hybride on-premises/cloudapplicatie (.NET) | Microsoft Documenten
description: Ontdek hoe u een on-premises WCF-service zichtbaar maakt voor een webtoepassing in de cloud met behulp van Azure Relay
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212964"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Een on-premises WCF-service zichtbaar maken voor een webtoepassing in de cloud met behulp van Azure Relay

In dit artikel wordt beschreven hoe u een hybride cloudtoepassing opbouwt met Microsoft Azure en Visual Studio. U maakt een toepassing die meerdere Azure-bronnen in de cloud gebruikt. Deze zelfstudie helpt je leren:

* Een bestaande webservice voor verbruik door een weboplossing maken of aanpassen.
* De WCF-relayservice (Azure Windows Communication Foundation) gebruiken om gegevens te delen tussen een Azure-toepassing en een webservice die elders wordt gehost.

U doet de volgende taken in deze zelfstudie:

> [!div class="checklist"]
>
> * Installeer vereisten voor deze zelfstudie.
> * Het scenario lezen.
> * Een naamruimte maken.
> * Maak een on-premises server.
> * Maak een ASP .NET-toepassing.
> * Voer de app lokaal uit.
> * Implementeer de web-app naar Azure.
> * Voer de app uit op Azure.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com). De voorbeelden in deze tutorial gebruiken Visual Studio 2019.
* Azure-SDK voor .NET. Installeer de SDK via de [SDK-downloadpagina](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Hoe Azure hulp biedt bij hybride oplossingen

Bedrijfsoplossingen bestaan meestal uit een combinatie van aangepaste code en bestaande functionaliteit. Aangepaste code voldoet aan nieuwe en unieke zakelijke vereisten. Oplossingen en systemen die al aanwezig zijn, bieden bestaande functionaliteit.

Oplossingsarchitecten gaan nu vaak over op de cloud om gemakkelijk aan schaalvereisten te voldoen en operationele kosten te verlagen. Daarbij vinden ze dat bestaande serviceassets die ze als bouwstenen voor hun oplossingen willen gebruiken, zich binnen de bedrijfsfirewall bevinden en buiten bereik van de cloudoplossing liggen. Veel interne services worden niet zo gebouwd of gehost dat ze gemakkelijk kunnen worden blootgesteld aan de rand van het bedrijfsnetwerk.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) neemt bestaande WCF-webservices en maakt deze services veilig toegankelijk voor oplossingen die zich buiten de bedrijfsperimeter bevinden zonder dat ingrijpende wijzigingen in de bedrijfsnetwerkinfrastructuur nodig zijn. Dergelijke relayservices worden nog steeds gehost binnen hun bestaande omgeving, maar ze dragen het luisteren naar binnenkomende sessies en aanvragen over aan de relayservice in de cloud. Azure Relay beschermt deze services ook tegen ongeautoriseerde toegang door [SAS-verificatie (Shared Access Signature) te](../service-bus-messaging/service-bus-sas.md) gebruiken.

## <a name="review-the-scenario"></a>Het scenario lezen

In deze zelfstudie maakt u een ASP.NET-website waarmee u een lijst met producten op de pagina met de productinventaris kunt bekijken.

![Scenario][0]

In de zelfstudie wordt ervan uitgegaan dat u productgegevens in een bestaand on-premises systeem hebt en wordt Azure Relay gebruikt om dat systeem te bereiken. Een webservice die wordt uitgevoerd in een eenvoudige consoletoepassing, simuleert deze situatie. Het bevat een in-memory set van producten. U deze consoletoepassing op uw eigen computer uitvoeren en de webrol implementeren in Azure. Hiermee ziet u hoe de webrol die wordt uitgevoerd in het Azure-datacenter naar uw computer wordt aanroept. Deze oproep gebeurt ook al zal uw computer vrijwel zeker achter ten minste een firewall en een netwerk adres vertaling (NAT) laag.

## <a name="set-up-the-development-environment"></a>De ontwikkelomgeving instellen

Voordat u Azure-toepassingen kunt ontwikkelen, dient u de hulpprogramma's te downloaden en uw ontwikkelomgeving in te stellen:

1. Installeer de Azure-SDK voor .NET via de [pagina met downloads](https://azure.microsoft.com/downloads/) voor SDK.
1. Kies in de kolom **.NET** de versie van [Visual Studio](https://www.visualstudio.com) die u gebruikt. Deze tutorial maakt gebruik van Visual Studio 2019.
1. Wanneer u wordt gevraagd het installatieprogramma uit te voeren of op te slaan, selecteert u **Uitvoeren**.
1. **Selecteer** installeren en doorgaan met de installatie in het dialoogvenster Installatie **platform installatie.**

Zodra de installatie is voltooid, heb je alles wat nodig is om te beginnen met het ontwikkelen van de app. De SDK bevat hulpprogramma's waarmee u eenvoudig Azure-toepassingen kunt ontwikkelen in Visual Studio.

## <a name="create-a-namespace"></a>Een naamruimte maken

De eerste stap is het maken van een naamruimte en ophalen van een SAS-sleutel ([Shared Access Signature](../service-bus-messaging/service-bus-sas.md)). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via de relayservice. Een SAS-sleutel wordt automatisch gegenereerd door het systeem wanneer een servicenaamruimte wordt gemaakt. De combinatie van servicenaamruimte en SAS-sleutel biedt Service Bus de benodigde referenties voor het verifiëren van toegang tot een toepassing.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Een on-premises server maken

U bouwt eerst een gesimuleerd on-premises systeem voor de productcatalogus op.  Dit project is een Visual Studio-consoletoepassing en gebruikt het [Azure Service Bus NuGet-pakket](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) om de Service Bus-bibliotheken en configuratie-instellingen op te nemen. <a name="create-the-project"></a>

1. Start Microsoft Visual Studio als beheerder. Klik hiervoor met de rechtermuisknop op het programmapictogram van Visual Studio en selecteer **Uitvoeren als beheerder**.
1. Selecteer in Visual Studio De optie **Een nieuw project maken**.
1. Selecteer **console-app (.NET Framework)** voor C# **in Een nieuw project maken**en selecteer **Volgende**.
1. Geef het project *ProductsServer* een naam en selecteer **Maken**.

   ![Uw nieuwe project configureren][11]

1. Klik in **Solution Explorer**met de rechtermuisknop op het **ProductsServer-project** en selecteer **NuGet-pakketten beheren**.
1. Selecteer **Bladeren**en zoek naar en kies **WindowsAzure.ServiceBus**. Selecteer **Installeren**en accepteer de gebruiksvoorwaarden.

   ![NuGet-pakket selecteren][13]

   Er wordt nu naar de vereiste clientassembly's verwezen.

1. Voeg een nieuwe klasse voor uw productcontract toe. Klik in **Solution Explorer**met de rechtermuisknop op het **Project ProductsServer** en selecteer**Klasse** **toevoegen** > .
1. Voer in **Naam**de naam *ProductsContract.cs* in en selecteer **Toevoegen**.

Breng de volgende codewijzigingen aan in uw oplossing:

1. Vervang in *ProductsContract.cs* de naamruimtedefinitie door de volgende code waarmee het contract voor de service wordt gedefinieerd.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. Vervang *in Program.cs*de definitie van naamruimte door de volgende code, die de profielservice en de host hiervoor toevoegt.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. Dubbelklik in **Solution Explorer**op **App.config** om het bestand in de Visual Studio-editor te openen. Voeg onder aan `<system.ServiceModel>` het element, `<system.ServiceModel>`maar nog steeds binnen, de volgende XML-code toe. Zorg ervoor `yourServiceNamespace` dat u de naam van `yourKey` uw naamruimte vervangt en door de SAS-sleutel die u eerder uit de portal hebt opgehaald:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > De fout `transportClientEndpointBehavior` veroorzaakt door is slechts een waarschuwing en is niet een blokkering probleem voor dit voorbeeld.

1. Nog steeds in *App.config*, in het `<appSettings>` element, vervang de waarde van de verbindingstekenreeks door de verbindingstekenreeks die u eerder van de portal hebt verkregen.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Selecteer Ctrl+Shift+B of selecteer **Build** > **Build Solution** om de toepassing te bouwen en controleer de nauwkeurigheid van uw werk tot nu toe.

## <a name="create-an-aspnet-application"></a>Een ASP.NET-toepassing maken

In deze sectie bouwt u een eenvoudige ASP.NET-toepassing op waarmee gegevens worden weergegeven die u uit uw productservice hebt opgehaald.

### <a name="create-the-project"></a>Het project maken

1. Controleer of Visual Studio wordt uitgevoerd als beheerder.
1. Selecteer in Visual Studio De optie **Een nieuw project maken**.
1. Selecteer in **Een nieuw project maken**ASP.NET **webtoepassing (.NET Framework)** voor C# en selecteer **Volgende**.
1. Geef het project *ProductsPortal* een naam en selecteer **Maken**.
1. Kies **MVC** en selecteer **Wijzigen** onder **Verificatie**in Een nieuwe **ASP.NET WebApplication**maken .

   ![ASP.NET-webtoepassing selecteren][16]

1. Kies **in Verificatie wijzigen**de optie Geen **verificatie** en selecteer **OK**. Voor deze zelfstudie implementeert u een app waarvoor geen gebruiker hoeft zich aan te melden.

    ![Verificatie opgeven][18]

1. Terug in **Een nieuwe ASP.NET-webtoepassing maken,** selecteert u **Maken** om de MVC-app te maken.
1. Configureer Azure-bronnen voor een nieuwe web-app. Volg de stappen in [Uw web-app publiceren](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Ga vervolgens terug naar deze zelfstudie en ga verder naar de volgende stap.
1. Klik in **Solution Explorer**met de rechtermuisknop op **Modellen** en selecteer**Klasse** **toevoegen** > .
1. Geef de klasse *Product.cs*en selecteer **Toevoegen**.

    ![Productmodel maken][17]

### <a name="modify-the-web-application"></a>De webtoepassing wijzigen

1. Vervang in het *Product.cs-bestand* in Visual Studio de bestaande definitie van naamruimte door de volgende code:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. Vouw controllers **uit**in **Solution Explorer**en dubbelklik vervolgens **op HomeController.cs** om het bestand in Visual Studio te openen.
1. In *HomeController.cs* vervangt u de bestaande naamruimtedefinitie door de volgende code:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. Dubbelklik in **Solution Explorer** **Weergaven** > **gedeeld**uit en dubbelklik op **_Layout.cshtml** om het bestand in de Visual Studio-editor te openen.
1. Wijzig alle gebeurtenissen `My ASP.NET Application` van naar *Northwind Traders Producten*.
1. Verwijder `Home`de `About`, `Contact` en koppelingen. Verwijder de gemarkeerde code in het volgende voorbeeld.

    ![De gegenereerde lijst met items verwijderen][41]

1. Dubbelklik in **Solution Explorer**, Bekijk **de startvan weergaven** > **Home**en dubbelklik vervolgens op **Index.cshtml** om het bestand in de Visual Studio-editor te openen. Vervang de volledige inhoud van het bestand door de volgende code:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Als u de nauwkeurigheid van uw werk tot nu toe wilt controleren, u Ctrl+Shift+B selecteren om het project te bouwen.

### <a name="run-the-app-locally"></a>De app lokaal uitvoeren

Voer de toepassing uit om te controleren of deze werkt.

1. Zorg ervoor dat **ProductsPortal** het actieve project is. Klik met de rechtermuisknop op de projectnaam in **Solution Explorer** en selecteer Instellen **als opstartproject**.
1. Selecteer F5 in Visual Studio.

Uw toepassing moet dan in een browser worden weergegeven.

![Webtoepassing][21]

## <a name="put-the-pieces-together"></a>De softwareonderdelen samenstellen

In de volgende stap koppelt u de on-premises productenserver aan de ASP.NET-toepassing.

1. Als deze nog niet is geopend, opent u in Visual Studio het **ProductsPortal-project** dat u hebt gemaakt in de sectie [Een ASP.NET-toepassing maken.](#create-an-aspnet-application)
1. Net als bij de stap in de sectie [Een on-premises server maken,](#create-an-on-premises-server) voegt u het NuGet-pakket toe aan de projectverwijzingen. Klik in **Solution Explorer**met de rechtermuisknop op het **ProductsPortal-project** en selecteer **NuGet-pakketten beheren.**
1. Zoek naar *WindowsAzure.ServiceBus* en selecteer het item **WindowsAzure.ServiceBus**. Maak vervolgens de installatie af en sluit dit dialoogvenster.
1. Klik in **Solution Explorer**met de rechtermuisknop op het **project ProductsPortal** en selecteer **Bestaand** > **item**toevoegen .
1. Ga naar het bestand *ProductsContract.cs* vanuit het **ProductsServer**-consoleproject. Hoogtepunt *ProductsContract.cs*. Selecteer de pijl-omlaag naast **Toevoegen**en kies **Toevoegen als koppeling**.

   ![Toevoegen als een koppeling][24]

1. Open nu het bestand *HomeController.cs* in de Visual Studio-editor en vervang de naamruimtedefinitie door de volgende code. Zorg ervoor `yourServiceNamespace` dat u de naam van `yourKey` uw servicenaamruimte en uw SAS-sleutel vervangt. Met deze code kan de client de on-premises service bellen en het resultaat van het gesprek retourneren.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. Klik in **Solution Explorer**met de rechtermuisknop op de **ProductsPortal-oplossing.** Zorg ervoor dat u met de rechtermuisknop op de oplossing klikt, niet op het project. Selecteer**Bestaand project** **toevoegen** > .
1. Ga naar het **ProductsServer**-project en dubbelklik op het *ProductsServer.csproj*-oplossingsbestand om het toe te voegen.
1. **ProductsServer** moet actief zijn om de gegevens weer te geven op **ProductsPortal**. Klik in **Solution Explorer**met de rechtermuisknop op de **Oplossing ProductsPortal** en selecteer **Eigenschappen** om **eigenschappenpagina's**weer te geven.
1. Selecteer **Opstartproject algemene eigenschappen** > **Startup Project** en kies **Meerdere opstartprojecten**. Controleer of **ProductsServer** en **ProductsPortal** in die volgorde worden weergegeven en dat de **actie** voor beide **start**is.

      ![Meerdere opstartprojecten][25]

1. Selecteer **Algemene eigenschappen** > **Project Afhankelijkheden** aan de linkerkant.
1. Kies voor **projecten** **ProductsPortal**. Zorg ervoor dat **ProductsServer** is geselecteerd.

    ![Projectafhankelijkheden][26]

1. Kies **ProductsServer**voor **projecten**. Controleer of **ProductsPortal** niet is geselecteerd en selecteer **OK** om uw wijzigingen op te slaan.

## <a name="run-the-project-locally"></a>Het project lokaal uitvoeren

Als u de toepassing lokaal wilt testen, selecteert u in Visual Studio F5. De on-premises server, **ProductsServer,** moet eerst starten, dan moet de **ProductsPortal-toepassing** in een browservenster worden gestart. De productinventaris bevat nu de gegevens die zijn opgehaald uit het on-premises systeem van de productservice.

![Webtoepassing][10]

Selecteer **Vernieuwen** op de pagina **ProductsPortal.** Elke keer dat u de pagina vernieuwt, wordt met de server-app een bericht weergegeven wanneer `GetProducts()` vanuit **ProductsServer** wordt aangeroepen.

Sluit beide aanvragen voordat u doorgaat naar de volgende sectie.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Het project ProductsPortal in een Azure-web-app implementeren

De volgende stap is het opnieuw publiceren van de Front-end van Azure Web-app **ProductsPortal:**

1. Klik in **Solution Explorer**met de rechtermuisknop op het **ProductsPortal-project** en selecteer **Publiceren**. Selecteer **Publiceren** op **Publish**de pagina Publiceren .

   > [!NOTE]
   > Mogelijk wordt er een foutbericht weergegeven in het browservenster wanneer het **ProductsPortal**-webproject automatisch wordt gestart na de implementatie. Dit is normaal en doet zich voor omdat de **ProductsServer**-toepassing nog niet wordt uitgevoerd.
   >

1. Kopieer de URL van de geïmplementeerde web-app. Je hebt de URL later nodig. U deze URL ook ophalen in het venster **Azure App Service Activity** in Visual Studio:

   ![URL van de geïmplementeerde app][9]

1. Sluit het browservenster om het uitvoeren van de toepassing te stoppen.

<a name="set-productsportal-as-web-app"></a>Voordat u de toepassing in de cloud uitvoert, moet u ervoor zorgen dat **ProductsPortal** vanuit Visual Studio als web-app wordt gestart.

1. Klik in Visual Studio met de rechtermuisknop op het **project ProductsPortal** en selecteer **Eigenschappen**.
1. Selecteer **Web**. Kies URL **starten**onder **Actie starten**. Voer in dit voorbeeld de URL in voor `https://productsportal20190906122808.azurewebsites.net/`uw eerder geïmplementeerde web-app.

    ![Start-URL][27]

1. Selecteer**Alles opslaan van** **bestand** > .
1. Selecteer**Oplossing voor opnieuw** **bouwen** > .

## <a name="run-the-application"></a>De toepassing uitvoeren

Selecteer F5 om de toepassing te bouwen en uit te voeren. De on-premises server, **ProductsServer** de ProductsServer-consoletoepassing, moet eerst worden gestart, waarna de **ProductsPortal-toepassing** moet worden gestart in een browservenster, zoals hier wordt weergegeven:

   ![De web-app in Azure uitvoeren][1]

In de productvoorraad worden gegevens weergegeven die zijn opgehaald uit het on-premises systeem van de productservice en worden deze gegevens weergegeven in de web-app. Controleer de URL om ervoor te zorgen dat **ProductsPortal** als een Azure-web-app wordt uitgevoerd in de cloud.

   > [!IMPORTANT]
   > De **ProductsServer**-consoletoepassing moet worden uitgevoerd en moet de gegevens aan de **ProductsPortal**-toepassing kunnen leveren. Als de browser een fout weergeeft, wacht u nog enkele seconden tot **ProductsServer** het volgende bericht laadt en weergeeft en vernieuwt u de browser.
   >

Vernieuw in de browser de pagina **ProductsPortal.** Elke keer dat u de pagina vernieuwt, wordt met de server-app een bericht weergegeven wanneer `GetProducts()` vanuit **ProductsServer** wordt aangeroepen.

![Bijgewerkte uitvoer][38]

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Een on-premises WCF-service zichtbaar maken voor een WCF-client buiten uw netwerk](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
