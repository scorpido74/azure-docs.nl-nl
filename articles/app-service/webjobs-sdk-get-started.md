---
title: Aan de slag met de WebJobs SDK
description: Inleiding tot de WebJobs SDK voor gebeurtenisgestuurde achtergrondverwerking. Meer informatie over hoe u toegang krijgt tot gegevens in Azure-services en services van derden.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4976be485a9b7609c6e8d23f6b897092217663fc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535669"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Aan de slag met de Azure WebJobs SDK voor gebeurtenisgestuurde verwerking op de achtergrond

In dit artikel ziet u hoe u Visual Studio 2019 gebruiken om een Azure WebJobs SDK-project te maken, lokaal uit te voeren en vervolgens te implementeren in [Azure App Service](overview.md). Versie 3.x van de WebJobs SDK ondersteunt zowel .NET Core- als .NET Framework-console-apps. Zie [De Azure WebJobs SDK gebruiken voor gebeurtenisgestuurde achtergrondverwerking](webjobs-sdk-how-to.md)voor meer informatie over het werken met de WebJobs SDK.

In dit artikel ziet u hoe u WebJobs implementeert als een .NET Core-console-app. Zie [WebJobs als .NET Framework-console-apps](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps)als .NET Framework-console-apps . Zie [WebJobs ontwikkelen en implementeren met Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md)als u geïnteresseerd bent in WebJobs SDK-versie 2.x, die alleen .NET Framework ondersteunt.

## <a name="prerequisites"></a>Vereisten

* [Installeer Visual Studio 2019](/visualstudio/install/) met de **Azure-ontwikkelworkload.** Als u visual studio al hebt, maar die werkbelasting niet hebt, voegt u de werkbelasting toe door **Hulpmiddelen > Hulpmiddelen en functies te selecteren.**

* U moet [een Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) hebben om uw WebJobs SDK-project naar Azure te publiceren.

## <a name="create-a-project"></a>Een project maken

1. Selecteer in Visual Studio de optie **Een nieuw project maken**.

2. Selecteer **Console-app (.NET-kern)**.

3. Geef het project *WebJobsSDKSample*een naam en selecteer **Vervolgens Maken**.

   ![Het dialoogvenster Nieuw project](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet-pakketten

1. Installeer de nieuwste stabiele 3.x versie van `Microsoft.Azure.WebJobs`het [ `Microsoft.Azure.WebJobs.Extensions` NuGet-pakket,](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)inclusief .

     Hier is de opdracht **Package Manager Console:**

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    Vervang in deze `<3_X_VERSION>` opdracht een ondersteunde versie van het pakket. 

## <a name="create-the-host"></a>De host maken

De host is de runtime-container voor functies die naar triggers en aanroepenfuncties luistert. Met de volgende stappen wordt [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost)een host gemaakt die implementeert , de algemene host in ASP.NET Kern.

1. Voeg *in*Program.cs `using` deze instructies toe:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Vervang de `Main`-methode door de volgende code:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

In ASP.NET Core worden hostconfiguraties ingesteld door [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) methoden op te roepen in de instantie. Zie [.NET Generic Host](/aspnet/core/fundamentals/host/generic-host)voor meer informatie. De `ConfigureWebJobs` extensiemethode initialiseert de WebJobs-host. In `ConfigureWebJobs`initialiseert u specifieke WebJobs-extensies en stelt u eigenschappen van deze extensies in.  

## <a name="enable-console-logging"></a>Consolelogboekregistratie inschakelen

In deze sectie stelt u consolelogboekregistratie in die gebruikmaakt van het [ASP.NET Core-logboekregistratieframework.](/aspnet/core/fundamentals/logging)

1. Installeer de nieuwste stabiele versie van het `Microsoft.Extensions.Logging` [ `Microsoft.Extensions.Logging.Console` NuGet-pakket,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)inclusief .

   Hier is de opdracht **Package Manager Console:**

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```

1. Voeg *Program.cs*in Program.cs `using` een verklaring toe:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

    Vervang in deze `<3_X_VERSION>` opdracht een ondersteunde 3.x-versie van het pakket.

1. Roep [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) de [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)methode aan op . De [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) methode voegt consolelogboekregistratie toe aan de configuratie.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    De `Main` methode ziet er nu als volgt uit:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Deze update doet het volgende:

    * Hiermee schakelt u [dashboardlogboekregistratie](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)uit. Het dashboard is een verouderd controlehulpmiddel en dashboardlogboekregistratie wordt niet aanbevolen voor productiescenario's met hoge doorvoer.
    * Hiermee voegt u de consoleprovider toe met [standaardfiltering](webjobs-sdk-how-to.md#log-filtering).

U nu een functie toevoegen die wordt geactiveerd door berichten die in een [Azure Storage-wachtrij](../azure-functions/functions-bindings-storage-queue.md)binnenkomen.

## <a name="install-the-storage-binding-extension"></a>De Storage-bindingextensie installeren

Vanaf versie 3.x moet u de extensie Opslagbinding die vereist is door de WebJobs SDK expliciet installeren. In eerdere versies werden de opslagbindingen opgenomen in de SDK.

1. Installeer de nieuwste stabiele versie van het [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-pakket, versie 3.x. 

    Hier is de opdracht **Package Manager Console:**

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    Vervang in deze `<3_X_VERSION>` opdracht een ondersteunde versie van het pakket. 

2. Roep `ConfigureWebJobs` in de extensiemethode `AddAzureStorage` de [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) methode aan op de instantie om de extensie Opslag te initialiseren. Op dit punt `ConfigureWebJobs` ziet de methode eruit als het volgende voorbeeld:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Een functie maken

1. Klik met de rechtermuisknop op het project, selecteer Nieuw item **toevoegen...** > **New Item...**, kies **Klasse,** geef het nieuwe C#-klassebestand *Functions.cs*en selecteer **Toevoegen**.

1. Vervang in Functions.cs de gegenereerde sjabloon door de volgende code:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   Het `QueueTrigger` kenmerk vertelt de runtime om deze functie aan te roepen `queue`wanneer een nieuw bericht wordt geschreven in een Azure Storage-wachtrij met de naam . De inhoud van het wachtrijbericht wordt verstrekt `message` aan de methodecode in de parameter. De hoofdtekst van de methode is waar u de triggergegevens verwerkt. In dit voorbeeld wordt het bericht alleen maar door de code logboeken.

   De `message` parameter hoeft geen tekenreeks te zijn. U ook binden aan een JSON-object, een bytearray of een [CloudQueueMessage-object.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) [Zie Gebruik van wachtrijtriggering](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Elk bindingstype (zoals wachtrijen, blobs of tabellen) heeft een andere set parametertypen waaraan u binden.

## <a name="create-a-storage-account"></a>Create a storage account

De Azure Storage-emulator die lokaal wordt uitgevoerd, heeft niet alle functies die de WebJobs SDK nodig heeft. In deze sectie maakt u dus een opslagaccount in Azure en configureert u het project om het te gebruiken. Als je al een opslagaccount hebt, ga je door naar stap 6.

1. Open **Server Explorer** in Visual studio en meld u aan bij Azure. Klik met de rechtermuisknop op het **Azure-knooppunt** en selecteer **Verbinding maken met Microsoft Azure-abonnement**.

   ![Aanmelden bij Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. Klik onder het **Azure-knooppunt** in **Server Explorer**met de rechtermuisknop op **Opslag**en selecteer **Vervolgens Opslagaccount maken**.

   ![Menu Opslagaccount maken](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Voer in het dialoogvenster **Opslagaccount maken** een unieke naam in voor het opslagaccount.

1. Kies dezelfde **regio waarin** u uw App Service-app hebt gemaakt of een regio die bij u in de buurt staat.

1. Selecteer **Maken**.

   ![Opslagaccount maken](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Selecteer onder het **knooppunt Opslag** in **Server Explorer**het nieuwe opslagaccount. Selecteer **in** het venster Eigenschappen de ellips (**...**) rechts van het veld Waarde van de **verbindingstekenreeks.**

   ![Verbindingstekenreeksellips](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Kopieer de verbindingstekenreeks en sla deze waarde ergens op zodat u deze eenvoudig opnieuw kopiëren.

   ![Verbindingstekenreeks kopiëren](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Opslag configureren om lokaal uit te voeren

De WebJobs SDK zoekt naar de tekenreeks voor opslagverbindingen in de toepassingsinstellingen in Azure. Wanneer u lokaal wordt uitgevoerd, wordt naar deze waarde gezocht in het lokale configuratiebestand of in omgevingsvariabelen.

1. Klik met de rechtermuisknop op het project, selecteer Nieuw item **toevoegen...** > **New Item...**, kies **JavaScript JSON-configuratiebestand,** geef de naam van het nieuwe bestand *van bestandsappsettings.json* en selecteer **Toevoegen**. 

1. Voeg in het nieuwe `AzureWebJobsStorage` bestand een veld toe, zoals in het volgende voorbeeld:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Vervang *{storage connection string}* door de verbindingstekenreeks die u eerder hebt gekopieerd.

1. Selecteer het bestand *appsettings.json* in Solution Explorer en stel in het venster **Eigenschappen** De optie **Kopiëren naar uitvoermap** in om **te kopiëren als nieuwer**.

Later voegt u dezelfde verbindingsreeks-app-instelling toe in uw app in Azure App Service.

## <a name="test-locally"></a>Lokaal testen

In deze sectie bouwt en voert u het project lokaal uit en activeert u de functie door een wachtrijbericht te maken.

1. Druk op **Ctrl+F5** om het project uit te voeren.

   De console laat zien dat de runtime uw functie heeft gevonden en wacht op wachtrijberichten om deze te activeren. De volgende uitvoer wordt gegenereerd door de v3.x-host:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Sluit het consolevenster.

1. Vouw in **Server Explorer** in Visual Studio het knooppunt uit voor uw nieuwe opslagaccount en klik vervolgens met de rechtermuisknop op **Wachtrijen**.

1. Selecteer **Wachtrij maken**.

1. Voer *de wachtrij* in als de naam voor de wachtrij en selecteer **OK**.

   ![Wachtrij maken](./media/webjobs-sdk-get-started/create-queue.png)

1. Klik met de rechtermuisknop op het knooppunt voor de nieuwe wachtrij en selecteer **Wachtrij weergeven**.

1. Selecteer het pictogram **Bericht toevoegen.**

   ![Wachtrij maken](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Typ *Hello World* in het dialoogvenster Bericht **toevoegen!** als de **berichttekst**en selecteer **OK**. Er staat nu een bericht in de wachtrij.

   ![Wachtrij maken](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Voer het project opnieuw uit.

   Omdat u `QueueTrigger` het kenmerk `ProcessQueueMessage` in de functie hebt gebruikt, luistert de runtime van WeJobs SDK naar wachtrijberichten wanneer deze wordt opgestart. Er wordt een nieuw wachtrijbericht gevonden in de wachtrij met de naam *wachtrij* en roept de functie aan.

   Als gevolg [van wachtrij polling exponentiële backoff](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm), kan het zo lang duren als 2 minuten voor de runtime om het bericht te vinden en de functie aan te roepen. Deze wachttijd kan worden verkort door in [de ontwikkelingsmodus](webjobs-sdk-how-to.md#host-development-settings)te draaien.

   De console-uitvoer ziet er als volgt uit:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Sluit het consolevenster. 

1. Ga terug naar het wachtrijvenster en vernieuw het. Het bericht is verdwenen, omdat het is verwerkt door uw functie die lokaal wordt uitgevoerd. 

## <a name="add-application-insights-logging"></a>Logboekregistratie voor toepassingsinzichten toevoegen

Wanneer het project wordt uitgevoerd in Azure, u de uitvoering van de functie niet controleren door de uitvoer van de console te bekijken. De monitoringoplossing die wij aanbevelen is [Application Insights.](../azure-monitor/app/app-insights-overview.md) Zie [Azure-functies controleren](../azure-functions/functions-monitoring.md)voor meer informatie .

In deze sectie u de volgende taken uitvoeren om logboekregistratie voor toepassingsinzichten in te stellen voordat u implementeert naar Azure:

* Zorg ervoor dat u een App Service-app en een instantie Application Insights hebt om mee te werken.
* Configureer de App Service-app om het exemplaar Application Insights en het opslagaccount te gebruiken dat u eerder hebt gemaakt.
* Stel het project in voor logboekregistratie bij Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Instantie App Service-app en Toepassingsinzichten maken

1. Als u nog geen App Service-app hebt die u gebruiken, [maakt u er een.](app-service-web-get-started-dotnet-framework.md) Wanneer u uw app maakt, u ook een verbonden Application Insights-bron maken. Wanneer u dit `APPINSIGHTS_INSTRUMENTATIONKEY` doet, is het voor u ingesteld in uw app.

1. Als u nog geen Application Insights-bron hebt die u gebruiken, [maakt u er een.](../azure-monitor/app/create-new-resource.md ) **Toepassingstype** instellen op **Algemeen**en de secties overslaan die volgen **De instrumentatietoets**kopiëren .

1. Als u al een Application Insights-bron hebt die u wilt gebruiken, [kopieert u de instrumentatiesleutel](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>App-instellingen configureren 

1. Vouw in **Server Explorer** in Visual Studio het knooppunt **App Service** uit onder **Azure**.

1. Vouw de brongroep uit waarin uw App Service-app zich bevindt en klik vervolgens met de rechtermuisknop op de App Service-app.

1. Selecteer **Weergave-instellingen**.

1. Voeg in het vak **Verbindingstekenreeksen** het volgende item toe.

   |Naam  |Verbindingsreeks  |Databasetype|
   |---------|---------|------|
   |AzureWebJobsStorage | {de tekenreeks opslagverbinding die u eerder hebt gekopieerd}|Aangepast telefoonnummer|

1. Als het vak **Toepassingsinstellingen** geen instrumentatiesleutel application insights heeft, voegt u de toets toe die u eerder hebt gekopieerd. (De instrumentatiesleutel kan er al zijn, afhankelijk van hoe u de App Service-app hebt gemaakt.)

   |Naam  |Waarde  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentatiesleutel} |

1. Vervang *{instrumentatiesleutel}* door de instrumentatiesleutel van de toepassingsbron die u gebruikt.

1. Selecteer **Opslaan**.

1. Voeg de verbinding Application Insights toe aan het project, zodat u deze lokaal uitvoeren. Voeg in het bestand *appsettings.json* een `APPINSIGHTS_INSTRUMENTATIONKEY` veld toe, zoals in het volgende voorbeeld:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Vervang *{instrumentatiesleutel}* door de instrumentatiesleutel van de toepassingsbron die u gebruikt.

1. Sla uw wijzigingen op.

### <a name="add-application-insights-logging-provider"></a>Logboekregistratieprovider voor toepassingsinzichten toevoegen

Update uw logboekcode om gebruik te maken van [de logboekregistratie](../azure-monitor/app/app-insights-overview.md) van Application Insights als volgt:

* Voeg een logboekprovider voor Application Insights toe met [standaardfiltering.](webjobs-sdk-how-to.md#log-filtering) Wanneer u lokaal wordt uitgevoerd, worden alle gegevens en logboeken op een hoger niveau naar zowel de console als de toepassingsinzichten geschreven.
* Plaats het [loggerfactory-object](./webjobs-sdk-how-to.md#logging-and-monitoring) in een `using` blok om ervoor te zorgen dat de logboekuitvoer wordt gespoeld wanneer de host wordt afgesloten.

1. Installeer de nieuwste stabiele 3.x-versie van het [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` NuGet-pakket.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)

   Hier is de opdracht **Package Manager Console:**

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    Vervang in deze `<3_X_VERSION>` opdracht een ondersteunde versie van het pakket.

1. Open *Program.cs* en vervang `Main` de code in de methode door de volgende code:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Hiermee wordt de Application Insights-provider toegevoegd aan de logboekregistratie, met behulp van de sleutel die u eerder aan uw app-instellingen hebt toegevoegd.

## <a name="test-application-insights-logging"></a>Logboekregistratie voor toepassingsinzichten testen

In deze sectie wordt opnieuw lokaal uitgevoerd om te controleren of logboekgegevens nu naar Application Insights en naar de console gaan.

1. Gebruik **Server Explorer** in Visual Studio om een wachtrijbericht te maken zoals u [eerder](#test-locally)deed, behalve hello *app Insights!* als de berichttekst.

1. Voer het project uit.

   De WebJobs SDK verwerkt het wachtrijbericht en u ziet de logboeken in het consolevenster.

1. Sluit het consolevenster.

1. Ga naar de [Azure-portal](https://portal.azure.com/) om uw Application Insights-bron te bekijken. Zoeken naar en selecteer **Toepassingsinzichten**.

1. Kies het exemplaar Application Insights.

1. Selecteer **Zoeken**.

   ![Zoeken selecteren](./media/webjobs-sdk-get-started/select-search.png)

1. Als je de Hello App Insights niet *ziet!* bericht, selecteer **Periodiek vernieuwen** gedurende enkele minuten. (Logboeken worden niet onmiddellijk weergegeven, omdat het een tijdje duurt voordat de client Application Insights de logboeken die deze verwerkt doorspoelt.)

   ![Logboeken in toepassingsinzichten](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Sluit het consolevenster.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Implementeren in Azure

Tijdens de implementatie maakt u een app-service-exemplaar waarin u uw functies uitvoeren. Wanneer u een .NET Core-console-app publiceert naar App Service in Azure, wordt deze automatisch uitgevoerd als webjob. Zie [WebJobs ontwikkelen en implementeren met Visual Studio](webjobs-dotnet-deploy-vs.md)voor meer informatie over publiceren.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>De functie activeren in Azure

1. Zorg ervoor dat u niet lokaal wordt uitgevoerd (sluit het consolevenster als deze nog open is). Anders kan de lokale instantie de eerste zijn die wachtrijberichten verwerkt die u maakt.

1. Voeg in de **pagina Wachtrij** in Visual Studio een bericht toe aan de wachtrij zoals voorheen.

1. Vernieuw de **wachtrijpagina** en het nieuwe bericht verdwijnt omdat het is verwerkt door de functie die in Azure wordt uitgevoerd.

   > [!TIP]
   > Wanneer u in Azure test, gebruikt u [de ontwikkelingsmodus](webjobs-sdk-how-to.md#host-development-settings) om ervoor te zorgen dat een wachtrijtriggerfunctie meteen wordt aangeroepen en voorkomt u vertragingen als gevolg van [exponentiële back-off van wachtrijpolling.](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm)

### <a name="view-logs-in-application-insights"></a>Logboeken weergeven in toepassingsinzichten

1. Open de [Azure-portal](https://portal.azure.com/)en ga naar uw Application Insights-bron.

1. Selecteer **Zoeken**.

1. Als u de Hello Azure niet *ziet!* bericht, selecteer **Periodiek vernieuwen** gedurende enkele minuten.

   U ziet de logboeken van de functie die wordt uitgevoerd in een WebJob, inclusief de *Hello Azure!* tekst die u in de vorige sectie hebt ingevoerd.

## <a name="add-an-input-binding"></a>Een invoerbinding toevoegen

Invoerbindingen vereenvoudigen code die gegevens leest. In dit voorbeeld is het wachtrijbericht een blobnaam en gebruikt u de blobnaam om een blob in Azure Storage te zoeken en te lezen.

1. Vervang *in*Functions.cs `ProcessQueueMessage` de methode door de volgende code:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   In deze `queueTrigger` code is een [bindende expressie,](../azure-functions/functions-bindings-expressions-patterns.md)wat betekent dat deze wordt opgelost tot een andere waarde bij runtime.  Bij runtime heeft het de inhoud van het wachtrijbericht.

1. Voeg `using`een :

   ```cs
   using System.IO;
   ```

1. Maak een blobcontainer in uw opslagaccount.

   a. Vouw in **Server Explorer** in Visual Studio het knooppunt voor uw opslagaccount uit, klik met de rechtermuisknop op **Blobs**en selecteer **BlobContainer maken.**

   b. Voer in het dialoogvenster **Blobcontainer maken** *de container* in als de containernaam en klik op **OK**.

1. Upload het *Program.cs-bestand* naar de blobcontainer. (Dit bestand wordt hier als voorbeeld gebruikt; u elk tekstbestand uploaden en een wachtrijbericht maken met de naam van het bestand.)

   a. Dubbelklik in **Server Explorer**op het knooppunt voor de container die u hebt gemaakt.

   b. Selecteer **in** het venster Container de knop **Uploaden.**

   ![Blob-uploadknop](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Zoek en selecteer *Program.cs*en selecteer **VERVOLGENS OK**.

1. Maak een wachtrijbericht in de wachtrij die u eerder hebt gemaakt, met *Program.cs* als de tekst van het bericht.

   ![Wachtrijbericht Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Voer het project lokaal uit.

   Het wachtrijbericht activeert de functie, die vervolgens de blob leest en de lengte ervan registreert. De console-uitvoer ziet er als volgt uit:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Een uitvoerbinding toevoegen

Uitvoerbindingen vereenvoudigen code die gegevens schrijft. In dit voorbeeld wordt het vorige gewijzigd door een kopie van de blob te schrijven in plaats van de grootte ervan te registreren. Blob-opslagbindingen zijn opgenomen in het Azure Storage-uitbreidingspakket dat we eerder hebben geïnstalleerd.

1. Vervang de `ProcessQueueMessage`-methode door de volgende code:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Maak een ander wachtrijbericht met *Program.cs* als de tekst van het bericht.

1. Voer het project lokaal uit.

   Het wachtrijbericht activeert de functie, die vervolgens de blob leest, de lengte ervan registreert en een nieuwe blob maakt. De console-uitvoer is hetzelfde, maar wanneer u naar het blobcontainervenster gaat en **Vernieuwen**selecteert, ziet u een nieuwe blob met de naam *copy-Program.cs.*

## <a name="republish-the-updates-to-azure"></a>De updates opnieuw publiceren naar Azure

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Controleer **in het** dialoogvenster Publiceren of het huidige profiel is geselecteerd en kies **Publiceren.** De resultaten van de publicatie worden beschreven in het venster **Uitvoer.**
 
1. Controleer de functie in Azure door opnieuw een bestand te uploaden naar de blobcontainer en een bericht toe te voegen aan de wachtrij die de naam is van het geüploade bestand. U ziet dat het bericht uit de wachtrij wordt verwijderd en een kopie van het bestand dat in de blobcontainer is gemaakt. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel ziet u hoe u een WebJobs SDK 3.x-project maken, uitvoeren en implementeren.

> [!div class="nextstepaction"]
> [Meer informatie over de WebJobs SDK](webjobs-sdk-how-to.md)
