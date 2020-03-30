---
title: Problemen met Visual Studio oplossen
description: Meer informatie over het oplossen van een App Service-app met behulp van hulpprogramma's voor het opsporen van externe debuggen, tracering en logboekregistratie die zijn ingebouwd in Visual Studio 2013.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: seodec18
ms.openlocfilehash: 3305cfb81980984574961b2a84a056f5d1879ead
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280051"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Problemen met een app in Azure App Service oplossen met Visual Studio
## <a name="overview"></a>Overzicht
In deze zelfstudie ziet u hoe u visual studio-hulpprogramma's gebruiken om een app in [App-service](https://go.microsoft.com/fwlink/?LinkId=529714)te debuggen, door op afstand in [de foutopsporingsmodus](https://docs.microsoft.com/visualstudio/debugger/) uit te voeren of door toepassingslogboeken en webserverlogboeken te bekijken.

U leert het volgende:

* Welke app-beheerfuncties beschikbaar zijn in Visual Studio.
* De externe weergave van Visual Studio gebruiken om snel wijzigingen aan te brengen in een externe app.
* De foutopsporingsmodus op afstand uitvoeren terwijl een project wordt uitgevoerd in Azure, zowel voor een app als voor een WebJob.
* Logboeken van toepassingstraceringslogboeken maken en deze bekijken terwijl de toepassing deze maakt.
* Hoe u webserverlogboeken bekijken, inclusief gedetailleerde foutberichten en mislukte het traceren van aanvragen.
* Diagnostische logboeken verzenden naar een Azure Storage-account en deze daar bekijken.

Als je Visual Studio Ultimate hebt, kun je [IntelliTrace](/visualstudio/debugger/intellitrace) ook gebruiken voor het debuggen. IntelliTrace is niet gedekt in deze tutorial.

## <a name="prerequisites"></a><a name="prerequisites"></a>Vereisten
Deze zelfstudie werkt met de ontwikkelomgeving, het webproject en de App Service-app die u inStelt in [Een ASP.NET-app maken in Azure App Service.](app-service-web-get-started-dotnet-framework.md) Voor de secties WebJobs hebt u de toepassing nodig die u maakt in [Aan de slag met de Azure WebJobs SDK.][GetStartedWJ]

De codevoorbeelden in deze zelfstudie zijn voor een C# MVC-webtoepassing, maar de probleemoplossingsprocedures zijn hetzelfde voor Toepassingen voor Visual Basic en WebFormulieren.

De zelfstudie gaat ervan uit dat je Visual Studio 2019 gebruikt. 

De functie streaminglogs werkt alleen voor toepassingen die zich richten op .NET Framework 4 of hoger.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>App-configuratie en -beheer
Visual Studio biedt toegang tot een subset van de app-beheerfuncties en configuratie-instellingen die beschikbaar zijn in de [Azure-portal.](https://go.microsoft.com/fwlink/?LinkId=529715) In deze sectie ziet u wat er beschikbaar is met **Server Explorer.** Als u de nieuwste Azure-integratiefuncties wilt bekijken, probeert u **Cloud Explorer** ook uit. U beide vensters openen in het menu **Weergave.**

1. Als u nog niet bent aangemeld bij Azure in Visual Studio, klikt u met de rechtermuisknop op **Azure** en selecteert u Verbinding maken met **Microsoft Azure-abonnement** in **Server Explorer**.

    Een alternatief is het installeren van een beheercertificaat waarmee toegang tot uw account mogelijk is. Als u ervoor kiest een certificaat te installeren, klikt u met de rechtermuisknop op het **Azure-knooppunt** in **Server Explorer**en selecteert u **Abonnementen beheren en filteren** in het contextmenu. Klik in het dialoogvenster **Microsoft Azure-abonnementen beheren** op het tabblad **Certificaten** en klik vervolgens op **Importeren**. Volg de aanwijzingen om te downloaden en importeer vervolgens een abonnementsbestand (ook wel *een .publishsettings-bestand* genoemd) voor uw Azure-account.

   > [!NOTE]
   > Als u een abonnementsbestand downloadt, slaat u het op in een map buiten uw broncodemappen (bijvoorbeeld in de map Downloads) en verwijdert u het bestand zodra de import is voltooid. Een kwaadwillende gebruiker die toegang krijgt tot het abonnementsbestand, kan uw Azure-services bewerken, maken en verwijderen.
   >
   >

    Zie [Accounts, Abonnementen en Beheerdersrollen beheren](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)voor meer informatie over verbinding maken met Azure-bronnen vanuit Visual Studio.
2. Vouw in **Server Explorer** **Azure** uit en vouw **de App-service**uit.
3. Vouw de brongroep uit die de app bevat die u hebt gemaakt in [Een ASP.NET-app maken in Azure App Service](app-service-web-get-started-dotnet-framework.md)en klik vervolgens met de rechtermuisknop op het app-knooppunt en klik op Instellingen **weergeven**.

    ![Instellingen weergeven in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Het tabblad **Azure Web App** wordt weergegeven en u daar de app-beheer- en configuratietaken zien die beschikbaar zijn in Visual Studio.

    ![Azure Web App-venster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    In deze zelfstudie gebruikt u de vervolgkeuzelijst logboekregistratie en tracering. U gebruikt ook foutopsporing op afstand, maar u gebruikt een andere methode om deze in te schakelen.

    Zie [Azure App Service: Hoe toepassingstekenreeksen en verbindingstekenreeksen werken](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)voor informatie over de vakken App-instellingen en verbindingstekenreeksen in dit venster.

    Als u een app-beheertaak wilt uitvoeren die niet in dit venster kan worden uitgevoerd, klikt u op **Openen in beheerportal** om een browservenster te openen voor de Azure-portal.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Toegang tot app-bestanden in Server Explorer
U implementeert meestal een `customErrors` webproject met de vlag in `On` `RemoteOnly`het web.config-bestand ingesteld op of , wat betekent dat u geen handig foutbericht krijgt wanneer er iets misgaat. Voor veel fouten, alles wat je krijgt is een pagina als een van de volgende:

**Serverfout in '/' toepassing:**

![Onhandige foutpagina](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Er is een fout opgetreden:**

![Onhandige foutpagina](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**De website kan de pagina niet weergeven**

![Onhandige foutpagina](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Vaak is de eenvoudigste manier om de oorzaak van de fout te vinden het inschakelen van gedetailleerde foutmeldingen, wat de eerste van de voorgaande schermafbeeldingen uitlegt hoe te doen. Dat vereist een wijziging in het geïmplementeerde Web.config-bestand. U het *web.config-bestand* in het project bewerken en het project opnieuw implementeren, of een [Web.config-transformatie](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) maken en een foutopsporingsbuild implementeren, maar er is een snellere manier: in **Solution Explorer**u bestanden rechtstreeks bekijken en bewerken in de externe app met behulp van de functie voor externe *weergave.*

1. Vouw **in Server Explorer** **Azure**uit, vouw App **Service**uit, vouw de resourcegroep uit waarin uw app zich bevindt en vouw vervolgens het knooppunt voor uw app uit.

    U ziet knooppunten die u toegang geven tot de inhoudsbestanden en logboekbestanden van de app.
2. Vouw het knooppunt **Bestanden** uit en dubbelklik op het bestand *Web.config.*

    ![Web.config openen](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio opent het web.config-bestand vanuit de externe app en toont [Remote] naast de bestandsnaam in de titelbalk.
3. Voeg de volgende `system.web` regel toe aan het element:

    `<customErrors mode="Off"></customErrors>`

    ![Web.config bewerken](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Vernieuw de browser met het nutteloze foutbericht en nu krijgt u een gedetailleerd foutbericht, zoals het volgende voorbeeld:

    ![Gedetailleerd foutbericht](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (De weergegeven fout is gemaakt door de lijn in het rood toe te voegen aan *Weergaven\Home\Index.cshtml*.)

Het bewerken van het Web.config-bestand is slechts één voorbeeld van scenario's waarin het oplossen van problemen met bestanden in uw App Service-app eenvoudiger wordt.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Apps voor het opsporen van externe problemen
Als het gedetailleerde foutbericht niet genoeg informatie bevat en u de fout niet lokaal opnieuw maken, u op afstand de fout uitvoeren in de foutopsporingsmodus. U breekpunten instellen, het geheugen direct manipuleren, code doorlopen en zelfs het codepad wijzigen.

Het op afstand opsporen werkt niet in Express-edities van Visual Studio.

In deze sectie ziet u hoe u op afstand debuggen met behulp van het project dat u maakt in [Een ASP.NET-app maken in Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. Open het webproject dat u hebt gemaakt in [Een ASP.NET-app maken in Azure App Service](app-service-web-get-started-dotnet-framework.md).

1. *Controllers openen\HomeController.cs*.

1. Verwijder `About()` de methode en plaats de volgende code op zijn plaats.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Stel een](https://docs.microsoft.com/visualstudio/debugger/) breakpoint `ViewBag.Message` op de lijn.

1. Klik in **Solution Explorer**met de rechtermuisknop op het project en klik op **Publiceren**.

1. Selecteer in de vervolgkeuzelijst **Profiel** hetzelfde profiel dat u hebt gebruikt in [Een ASP.NET-app maken in Azure App Service](app-service-web-get-started-dotnet-framework.md). Klik vervolgens op Instellingen.

1. Klik **in** het dialoogvenster Publiceren op het tabblad **Instellingen** en wijzig **Configuration** in **Debug**en klik vervolgens op **Opslaan**.

    ![Publiceren in de foutopsporingsmodus](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Klik op **Publish**. Nadat de implementatie is voltooid en uw browser wordt geopend voor de Azure-URL van uw app, sluit u de browser.

1. Klik in **Server Explorer**met de rechtermuisknop op uw app en klik vervolgens op **Foutopsporing bijvoegen**.

    ![Foutopsporing koppelen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    De browser wordt automatisch geopend naar uw startpagina die wordt uitgevoerd in Azure. Mogelijk moet u 20 seconden wachten terwijl Azure de server instelt voor foutopsporing. Deze vertraging gebeurt alleen de eerste keer dat u in de foutopsporingsmodus in een app wordt uitgevoerd in een periode van 48 uur. Wanneer u in dezelfde periode opnieuw begint met foutopsporing, is er geen vertraging.

    > [!NOTE] 
    > Als u problemen ondervindt bij het starten van de foutopsporing, probeert u dit te doen met **Cloud Explorer** in plaats van **Server Explorer.**
    >

1. Klik **op Over** in het menu.

    Visual Studio stopt op het breekpunt en de code wordt uitgevoerd in Azure, niet op uw lokale computer.

1. Plaats de `currentTime` boven de variabele om de tijdwaarde te zien.

    ![Variabele weergeven in de foutopsporingsmodus die wordt uitgevoerd in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    De tijd die u ziet is de Azure-servertijd, die zich mogelijk in een andere tijdzone bevindt dan uw lokale computer.

1. Voer een nieuwe `currentTime` waarde in voor de variabele, zoals 'Nu wordt uitgevoerd in Azure'.

1. Druk op F5 om door te gaan met draaien.

     De pagina Info die in Azure wordt uitgevoerd, geeft de nieuwe waarde weer die u hebt ingevoerd in de variabele currentTime.

     ![Informatie over pagina met nieuwe waarde](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a>WebJobs op afstand debuggen
In deze sectie ziet u hoe u op afstand debuggen met behulp van het project en de app die u maakt in [Aan de slag met de Azure WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

De functies in deze sectie zijn alleen beschikbaar in Visual Studio 2013 met Update 4 of hoger.

Het afluisteren op afstand werkt alleen met continue WebJobs. Geplande en on-demand WebJobs ondersteunen geen foutopsporing.

1. Open het webproject dat u hebt gemaakt in [Aan de slag met de Azure WebJobs SDK.][GetStartedWJ]

2. Open *Functions.cs*in het ContosoAdsWebJob-project.

3. [Stel een breekpunt](https://docs.microsoft.com/visualstudio/debugger/) in `GnerateThumbnail` op de eerste instructie in de methode.

    ![Breekpunt instellen](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. Klik in **Solution Explorer**met de rechtermuisknop op het webproject (niet op het WebJob-project) en klik op **Publiceren**.

5. Selecteer in de vervolgkeuzelijst **Profiel** hetzelfde profiel dat u hebt gebruikt in [Aan de slag met de Azure WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki)

6. Klik op het tabblad **Instellingen** en wijzig **Configuratie** in **Foutopsporing**en klik vervolgens op **Publiceren**.

    Visual Studio implementeert de web- en WebJob-projecten en uw browser wordt geopend voor de Azure-URL van uw app.

7. Vouw in **Server Explorer**Azure > App Service uit > **uw brongroep > uw app > WebJobs > Continu**en klik vervolgens met de rechtermuisknop op **ContosoAdsWebJob**.

8. Klik **op Foutopsporing bijvoegen**.

    ![Foutopsporing koppelen](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    De browser wordt automatisch geopend naar uw startpagina die wordt uitgevoerd in Azure. Mogelijk moet u 20 seconden wachten terwijl Azure de server instelt voor foutopsporing. Deze vertraging gebeurt alleen de eerste keer dat u in de foutopsporingsmodus in een app wordt uitgevoerd in een periode van 48 uur. Wanneer u in dezelfde periode opnieuw begint met foutopsporing, is er geen vertraging.

9. Maak in de webbrowser die is geopend voor de startpagina van Contoso Ads een nieuwe advertentie.

    Als u een advertentie maakt, wordt een wachtrijbericht gemaakt, dat wordt opgehaald door de WebJob en wordt verwerkt. Wanneer de WebJobs SDK de functie aanroept om het wachtrijbericht te verwerken, raakt de code uw breekpunt.

10. Wanneer de foutopsporing op uw breekpunt breekt, u variabele waarden onderzoeken en wijzigen terwijl het programma de cloud uitvoert. In de volgende afbeelding toont de foutopsporing de inhoud van `GenerateThumbnail` het blobInfo-object dat aan de methode is doorgegeven.

     ![blobInfo-object in foutopsporing](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Druk op F5 om door te gaan met draaien.

     De `GenerateThumbnail` methode is klaar met het maken van de miniatuur.

12. Vernieuw in de browser de indexpagina en u ziet de miniatuur.

13. Druk in Visual Studio op SHIFT+F5 om foutopsporing te stoppen.

14. Klik in **Server Explorer**met de rechtermuisknop op het knooppunt ContosoAdsWebJob en klik op **Dashboard weergeven**.

15. Meld u aan met uw Azure-referenties en klik vervolgens op de webjobnaam om naar de pagina voor uw WebJob te gaan.

     ![Klik op ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Het dashboard laat `GenerateThumbnail` zien dat de functie onlangs is uitgevoerd.

     (De volgende keer dat u op **Dashboard weergeven**klikt, hoeft u zich niet aan te melden en gaat de browser rechtstreeks naar de pagina voor uw WebJob.)

16. Klik op de functienaam om details over de functieuitvoering te bekijken.

     ![Functiedetails](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Als uw functie [logboeken heeft geschreven,](https://github.com/Azure/azure-webjobs-sdk/wiki)u op **ToggleOutput** klikken om deze te bekijken.

## <a name="notes-about-remote-debugging"></a>Opmerkingen over foutopsporing op afstand

* Uitvoeren in de foutopsporingsmodus in productie wordt afgeraden. Als uw productie-app niet is geschaald naar meerdere serverinstanties, voorkomt foutopsporing dat de webserver reageert op andere verzoeken. Als u meerdere webserverexemplaren hebt, krijgt u bij het koppelen aan de foutopsporing een willekeurige instantie en u er niet voor zorgen dat volgende browseraanvragen naar dezelfde instantie gaan. Ook implementeert u doorgaans geen foutopsporingsbuild naar productie en kunnen compileroptimalisaties voor releasebuilds het onmogelijk maken om aan te geven wat er regel voor regel in uw broncode gebeurt. Voor het oplossen van productieproblemen is uw beste bron het traceren van toepassingen en webserverlogboeken.
* Vermijd lange stops bij breekpunten bij het opsporen van externe debuggen op afstand. Azure behandelt een proces dat langer dan een paar minuten wordt gestopt als een niet-reagerend proces en sluit het af.
* Terwijl u debugging, verzendt de server gegevens naar Visual Studio, wat van invloed kan zijn op bandbreedtekosten. Zie [Azure Pricing](https://azure.microsoft.com/pricing/calculator/)voor informatie over bandbreedtesnelheden.
* Zorg ervoor `debug` dat het `compilation` kenmerk van het element in het *bestand Web.config* is ingesteld op true. Deze is standaard ingesteld op true wanneer u een foutopsporingsbuildconfiguratie publiceert.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Als u merkt dat de foutopsporing niet in de code stapt die u wilt debuggen, moet u mogelijk de instelling Alleen mijn code wijzigen.  Zie Opgeven of alleen [gebruikerscode moet worden gedebugd met Alleen Mijn code in Visual Studio](https://docs.microsoft.com/visualstudio/debugger/just-my-code)voor meer informatie.
* Er wordt een timer gestart op de server wanneer u de functie voor het opsporen van externe debuggen inschakelt en na 48 uur wordt de functie automatisch uitgeschakeld. Deze limiet van 48 uur wordt gedaan om beveiligings- en prestatieredenen. U de functie eenvoudig zo vaak weer inschakelen als u wilt. We raden u aan het apparaat uitgeschakeld te laten wanneer u niet actief debuggen.
* U de foutopsporing handmatig koppelen aan elk proces, niet alleen aan het app-proces (w3wp.exe). Zie [Foutopsporing in Visual Studio](/visualstudio/debugger/debugging-in-visual-studio)voor meer informatie over het gebruik van de foutopsporingsmodus in Visual Studio.

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Overzicht van diagnostische logboeken
Een ASP.NET-toepassing die wordt uitgevoerd in een App Service-app, kan de volgende soorten logboeken maken:

* **Logboeken voor het traceren van toepassingen**<br/>
  De toepassing maakt deze logboeken door methoden van de klasse [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) aan te roepen.
* **Webserverlogboeken**<br/>
  De webserver maakt een logboekvermelding voor elk HTTP-verzoek naar de app.
* **Gedetailleerde foutberichtenlogboeken**<br/>
  De webserver maakt een HTML-pagina met aanvullende informatie voor mislukte HTTP-aanvragen (aanvragen die resulteren in statuscode 400 of hoger).
* **Logboeken voor het traceren van mislukte aanvragen**<br/>
  De webserver maakt een XML-bestand met gedetailleerde traceringsgegevens voor mislukte HTTP-aanvragen. De webserver biedt ook een XSL-bestand om de XML in een browser op te maken.

Logboekregistratie is van invloed op de prestaties van apps, dus Azure biedt u de mogelijkheid om elk type logboek in- of uit te schakelen als dat nodig is. Voor toepassingslogboeken u opgeven dat alleen logboeken boven een bepaald ernstniveau moeten worden geschreven. Wanneer u een nieuwe app maakt, wordt standaard alle logboekregistratie uitgeschakeld.

Logboeken worden geschreven naar bestanden in een map *LogFiles* in het bestandssysteem van uw app en zijn toegankelijk via FTP. Webserverlogboeken en toepassingslogboeken kunnen ook naar een Azure Storage-account worden geschreven. U een groter aantal logboeken in een opslagaccount behouden dan mogelijk is in het bestandssysteem. U bent beperkt tot maximaal 100 megabyte aan logboeken wanneer u het bestandssysteem gebruikt. (Bestandssysteemlogboeken zijn alleen voor retentie op korte termijn. Azure verwijdert oude logboekbestanden om ruimte te maken voor nieuwe nadat de limiet is bereikt.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Logboeken van toepassingstraceringen maken en weergeven
In deze sectie doet u de volgende taken:

* Voeg traceringsinstructies toe aan het webproject dat u hebt gemaakt in [Aan de slag met Azure en ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Bekijk de logboeken wanneer u het project lokaal uitvoert.
* Bekijk de logboeken zoals ze worden gegenereerd door de toepassing die in Azure wordt uitgevoerd.

Zie [Hoe u met Azure-wachtrijopslag werken met Azure-wachtrijopslag met behulp van de WebJobs SDK - Logboeken schrijven](https://github.com/Azure/azure-webjobs-sdk/wiki)voor informatie over het maken van toepassingslogboeken in WebJobs. De volgende instructies voor het weergeven van logboeken en het bepalen hoe ze zijn opgeslagen in Azure, zijn ook van toepassing op toepassingslogboeken die zijn gemaakt door WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Traceringsinstructies toevoegen aan de toepassing
1. Open *Controllers\HomeController.cs*en `Index`vervang `About`de `Contact` , en methoden door de `Trace` volgende `using` code `System.Diagnostics`om instructies toe te voegen en een instructie voor:

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Voeg `using System.Diagnostics;` een instructie toe aan de bovenkant van het bestand.

### <a name="view-the-tracing-output-locally"></a>De traceringsuitvoer lokaal weergeven
1. Druk op F5 om de toepassing in de foutopsporingsmodus uit te voeren.

    De standaardtraceringslistener schrijft alle traceringsuitvoer naar het venster **Uitvoer,** samen met andere foutopsporingsuitvoer. In de volgende afbeelding ziet u de uitvoer `Index` van de traceringsinstructies die u aan de methode hebt toegevoegd.

    ![Tracering in het venster Foutopsporing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    In de volgende stappen wordt weergegeven hoe u de traceringsuitvoer op een webpagina weergeven, zonder dat u in de foutopsporingsmodus worden samengesteld.
1. Open het bestand Web.config van toepassing (het bestand `<system.diagnostics>` in de projectmap) en voeg `</configuration>` een element toe aan het einde van het bestand vlak voor het slotelement:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

Hiermee `WebPageTraceListener` u de traceringsuitvoer bekijken door te bladeren naar `/trace.axd`.
1. Voeg een <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">traceerelement</a> toe onder `<system.web>` in het bestand Web.config, zoals het volgende voorbeeld:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Druk op CTRL + F5 om de toepassing uit te voeren.
1. Voeg in de adresbalk van het browservenster *trace.axd* toe aan de URL `http://localhost:53370/trace.axd`en druk op Enter (de URL is vergelijkbaar met ).
1. Klik op de pagina **Toepassingstracering** op **Details weergeven** op de eerste regel (niet op de browserkoppelingregel).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    De pagina **Details aanvragen** wordt weergegeven en in de sectie **Traceinformation** ziet `Index` u de uitvoer van de traceringsinstructies die u aan de methode hebt toegevoegd.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Standaard `trace.axd` is alleen lokaal beschikbaar. Als u het beschikbaar wilt maken vanuit een `localOnly="false"` externe `trace` app, u het element in het *bestand Web.config* toevoegen, zoals in het volgende voorbeeld wordt weergegeven:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Het inschakelen `trace.axd` in een productie-app wordt echter om veiligheidsredenen niet aanbevolen. In de volgende secties ziet u een eenvoudigere manier om traceerlogboeken in een App Service-app te lezen.

### <a name="view-the-tracing-output-in-azure"></a>De traceringsuitvoer in Azure weergeven
1. Klik in **Solution Explorer**met de rechtermuisknop op het webproject en klik op **Publiceren**.
2. Klik in het dialoogvenster **Web publiceren** op **Publiceren**.

    Nadat Visual Studio uw update heeft gepubliceerd, wordt een browservenster naar uw startpagina geopend (ervan uitgaande dat u **de bestemmings-URL** op het tabblad **Verbinding** niet hebt gewist).
3. Klik in **Server Explorer**met de rechtermuisknop op uw app en selecteer **Streaminglogboeken weergeven**.

    ![Streaminglogboeken weergeven in contextmenu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    In het venster **Uitvoer** ziet u dat u bent verbonden met de log-streamingservice en wordt elke minuut een meldingsregel toegevoegd die voorbijgaat zonder dat er een logboek wordt weergegeven.

    ![Streaminglogboeken weergeven in contextmenu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Klik in het browservenster waarin de startpagina van uw toepassing wordt weergegeven op **Contactpersoon**.

    Binnen enkele seconden wordt de uitvoer van de tracering op foutniveau die u aan de `Contact` methode hebt toegevoegd, weergegeven in het venster **Uitvoer.**

    ![Fouttracering in uitvoervenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio toont alleen foutniveausporen, omdat dit de standaardinstelling is wanneer u de logboekcontroleservice inschakelt. Wanneer u een nieuwe App Service-app maakt, wordt alle logboekregistratie standaard uitgeschakeld, zoals u zag toen u de pagina Instellingen eerder opende:

    ![Toepassingsafmelden](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Wanneer u echter **Streaminglogs bekijken**hebt geselecteerd, heeft Visual Studio automatisch **Application Logging(Bestandssysteem)** gewijzigd in **Fout,** wat betekent dat logboeken op foutniveau worden gerapporteerd. Om al uw traceringslogboeken te bekijken, u deze instelling wijzigen in **Verbose.** Wanneer u een ernstniveau selecteert dat lager is dan fout, worden ook alle logboeken voor hogere ernstniveaus gerapporteerd. Dus wanneer u verbose selecteert, ziet u ook informatie, waarschuwingen en foutlogboeken.  

5. Klik in **Server Explorer**met de rechtermuisknop op de app en klik vervolgens op **Instellingen weergeven** zoals u eerder deed.
6. **Wijziging Application Logging (Bestandssysteem)** in **Verbose**en klik op **Opslaan**.

    ![Traceniveau instellen op Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Klik in het browservenster dat nu uw pagina **Contactpersoon** weergeeft op **Start,** klik vervolgens op **Over**en klik vervolgens op **Contact**.

    Binnen enkele seconden wordt in het venster **Uitvoer** al uw traceringsuitvoer weergegeven.

    ![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    In deze sectie hebt u logboekregistratie ingeschakeld en uitgeschakeld met behulp van app-instellingen. U tracelisteners ook inschakelen en uitschakelen door het bestand Web.config te wijzigen. Als u het web.config-bestand wijzigt, wordt het app-domein echter gerecycled, terwijl het inschakelen van logboekregistratie via de app-configuratie dat niet doet. Als het probleem lang duurt om te reproduceren of met tussenpozen is, kan het recyclen van het app-domein het "oplossen" en u dwingen te wachten tot het opnieuw gebeurt. Als u diagnostische gegevens in Azure inschakelt, u onmiddellijk beginnen met het vastleggen van foutgegevens zonder het app-domein te recyclen.

### <a name="output-window-features"></a>Eigenschappenvenster
Het tabblad **Microsoft Azure Logs** van het **uitvoervenster** heeft verschillende knoppen en een tekstvak:

![Tabbladknoppen logboeken](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Deze voeren de volgende functies uit:

* Het venster **Uitvoer wissen.**
* Woordenomloop in- of uitschakelen.
* Logboeken starten of stoppen.
* Geef op welke logboeken u wilt controleren.
* Logboeken downloaden.
* Filterlogboeken op basis van een zoektekenreeks of een reguliere expressie.
* Sluit het **venster Uitvoer.**

Als u een zoektekenreeks of reguliere expressie invoert, filtert Visual Studio logboekregistratiegegevens bij de client. Dat betekent dat u de criteria invoeren nadat de logboeken in het venster **Uitvoer** worden weergegeven en u filtercriteria wijzigen zonder de logboeken opnieuw te hoeven genereren.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Webserverlogboeken weergeven
Webserverlogboeken registreren alle HTTP-activiteiten voor de app. Als u ze in het venster **Uitvoer** wilt zien, moet u ze inschakelen voor de app en aan Visual Studio laten weten dat u ze wilt controleren.

1. Wijzig op het tabblad **Configuratie van Azure Web App** dat u vanuit Server **Explorer**hebt geopend, wijzigserverlogboekregistratie in **Aan**en klik vervolgens op **Opslaan**.

    ![Logboekregistratie van webserverinschakelen inschakelen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Klik in het **uitvoervenster** op **de knop Opgeven welke Microsoft Azure-logboeken moeten worden bewaakt.**

    ![Opgeven welke Azure-logboeken moeten worden gecontroleerd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Selecteer in het dialoogvenster **Microsoft Azure-logboekregistratieopties** de optie **Webserverlogboeken**en klik op **OK**.

    ![Webserverlogboeken controleren](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Klik in het browservenster waarin de app wordt weergegeven op **Start**en klik vervolgens op **Over**en klik vervolgens op **Contact**.

    De toepassingslogboeken verschijnen over het algemeen eerst, gevolgd door de webserverlogboeken. Het kan zijn dat u een tijdje moet wachten tot de logboeken worden weergegeven.

    ![Webserverlogboeken in uitvoervenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Wanneer u standaard webserverlogboeken inschakelt met Visual Studio, schrijft Azure de logboeken naar het bestandssysteem. Als alternatief u de Azure-portal gebruiken om op te geven dat webserverlogboeken moeten worden geschreven naar een blobcontainer in een opslagaccount.

Als u de portal gebruikt om serverlogboekregistratie in te schakelen voor een Azure-opslagaccount en vervolgens logboekregistratie in Visual Studio uitschakelt, worden de instellingen voor uw opslagaccount hersteld wanneer u de logboekregistratie in Visual Studio opnieuw inschakelt.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Gedetailleerde foutberichtlogboeken weergeven
Gedetailleerde foutlogboeken bevatten aanvullende informatie over HTTP-aanvragen die resulteren in foutreactiecodes (400 of hoger). Om ze in het **venster Uitvoer** te kunnen zien, moet u ze inschakelen voor de app en aan Visual Studio vertellen dat u ze wilt controleren.

1. Wijzig op het tabblad **Configuratie van Azure Web App** dat u vanuit Server **Explorer**hebt geopend, **gedetailleerde foutberichten** in **Aan**en klik vervolgens op **Opslaan**.

    ![Gedetailleerde foutberichten inschakelen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Klik in het **uitvoervenster** op **de knop Opgeven welke Microsoft Azure-logboeken moeten worden bewaakt.**

3. Klik in het dialoogvenster **Microsoft Azure-logboekregistratieopties** op **Alle logboeken**en klik vervolgens op **OK**.

    ![Alle logboeken controleren](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Voeg in de adresbalk van het browservenster een extra teken toe aan de `http://localhost:53370/Home/Contactx`URL om een fout van 404 te veroorzaken (bijvoorbeeld) en druk op Enter.

    Na enkele seconden wordt het gedetailleerde foutlogboek weergegeven in het venster Visual Studio **Output.**

    ![Gedetailleerd foutlogboek - Uitvoervenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Control+klik op de koppeling om de logboekuitvoer te bekijken die in een browser is opgemaakt:

    ![Gedetailleerd foutlogboek - browservenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Logboeken van bestandssystemen downloaden
Alle logboeken die u controleren in het **venster Uitvoer,** kunnen ook worden gedownload als een *.zip-bestand.*

1. Klik **in** het venster Uitvoer op **Streaminglogboeken downloaden**.

    ![Tabbladknoppen logboeken](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Verkenner wordt geopend in de map *Downloads* met het gedownloade bestand geselecteerd.

    ![Gedownload bestand](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Haal het *.zip-bestand eruit* en u ziet de volgende mapstructuur:

    ![Gedownload bestand](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Toepassingstraceringlogboeken bevinden zich in *.txt-bestanden* in de map *LogFiles\Application.*
   * Webserverlogboeken bevinden zich in *.log-bestanden* in de map *LogFiles\http\RawLogs.* U een tool zoals [Log Parser](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) gebruiken om deze bestanden weer te geven en te manipuleren.
   * Gedetailleerde foutberichtenlogboeken bevinden zich in *.html-bestanden* in de map *LogFiles\DetailedErrors.*

     (De map *implementaties* is voor bestanden die zijn gemaakt door publicatie van bronbeheer; er is niets met betrekking tot Visual Studio-publicatie. De *Git-map* is voor traces die betrekking hebben op het publiceren van bronbeheer en de streamingservice voor logboekbestanden.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Logboeken voor het traceren van mislukte aanvragen weergeven
Mislukte logboeken voor het traceren van aanvragen zijn handig wanneer u de details moet begrijpen van de manier waarop IIS een HTTP-aanvraag verwerkt, in scenario's zoals url-herschrijf- of verificatieproblemen.

App Service-apps gebruiken dezelfde mislukte functie voor het traceren van aanvragen die beschikbaar is met IIS 7.0 en hoger. U hebt echter geen toegang tot de IIS-instellingen die configureren welke fouten worden geregistreerd. Wanneer u mislukte aanvraagtracering inschakelt, worden alle fouten vastgelegd.

U mislukte aanvraagtracering inschakelen met Behulp van Visual Studio, maar u ze niet bekijken in Visual Studio. Deze logboeken zijn XML-bestanden. De streaminglogservice controleert alleen bestanden die als leesbaar worden beschouwd in de modus Platte tekst: *.txt,* *.html*en *.log-bestanden.*

U mislukte aanvraagtraceringslogboeken in een browser rechtstreeks via FTP of lokaal bekijken nadat u een FTP-tool hebt gebruikt om ze naar uw lokale computer te downloaden. In deze sectie bekijk je ze rechtstreeks in een browser.

1. Wijzig op het tabblad **Configuratie** van het **Azure Web App-venster** dat u vanuit **Server Verkenner**hebt geopend, wijzig **De tracering van mislukte aanvragen** in **Aan**en klik vervolgens op **Opslaan**.

    ![Tracering van mislukte aanvragen inschakelen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Voeg in de adresbalk van het browservenster waarin de app wordt weergegeven, een extra teken toe aan de URL en klik op Enter om een fout van 404 te veroorzaken.

    Hierdoor wordt een mislukt logboek voor het traceren van aanvragen gemaakt en wordt in de volgende stappen weergegeven hoe u het logboek bekijken of downloaden.

3. Klik in Visual Studio op het tabblad **Configuratie** van het Azure **Web App-venster** op Openen **in Beheerportal**.

4. Klik op de pagina [Azure-portalinstellingen](https://portal.azure.com) **Settings** voor uw app op **Implementatiereferenties**en voer vervolgens een nieuwe gebruikersnaam en wachtwoord in.

    ![Nieuwe FTP-gebruikersnaam en -wachtwoord](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Wanneer u zich aanmeldt, moet u de volledige gebruikersnaam gebruiken met de app-naam die eraan vooraf is bevestigd. Als u bijvoorbeeld 'myid' als gebruikersnaam invoert en de site 'myexample' is, logt u in als 'myexample\myid'.
    >

5. Ga in een nieuw browservenster naar de URL die wordt weergegeven onder **FTP-hostnaam** of **FTPS-hostnaam** op de pagina **Overzicht** voor uw app.

6. Meld u aan met de FTP-referenties die u eerder hebt gemaakt (inclusief het voorvoegsel van de naam van de app voor de gebruikersnaam).

    De browser toont de hoofdmap van de app.

7. Open de map *LogFiles.*

    ![Map LogBestanden openen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Open de map met de naam W3SVC plus een numerieke waarde.

    ![W3SVC-map openen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    De map bevat XML-bestanden voor eventuele fouten die zijn geregistreerd nadat u mislukte aanvraagtracering hebt ingeschakeld en een XSL-bestand dat een browser kan gebruiken om de XML op te maken.

    ![W3SVC-map](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klik op het XML-bestand voor het mislukte verzoek waarvoor u traceringsgegevens wilt zien.

    In de volgende afbeelding ziet u een deel van de traceringsgegevens voor een voorbeeldfout.

    ![Mislukte tracering van aanvragen in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Volgende stappen
U hebt gezien hoe Visual Studio het eenvoudig maakt om logboeken te bekijken die zijn gemaakt door een App Service-app. In de volgende secties vindt u links naar meer bronnen over gerelateerde onderwerpen:

* Problemen met app-service oplossen
* Foutopsporing in Visual Studio
* Foutopsporing op afstand in Azure
* Tracering in ASP.NET toepassingen
* Webserverlogboeken analyseren
* Logboeken voor het traceren van mislukte aanvragen analyseren
* Cloudservices debuggen

### <a name="app-service-troubleshooting"></a>Problemen met app-service oplossen
Zie de volgende bronnen voor meer informatie over het oplossen van problemen met apps in Azure App Service:

* [Apps controleren](web-sites-monitor.md)
* [Geheugenlekken in Azure App-service onderzoeken met Visual Studio 2013](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Microsoft ALM-blogpost over Visual Studio-functies voor het analyseren van beheerde geheugenproblemen.
* [Azure App Service online-hulpprogramma's die u moet weten.](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/) Blogpost van Amit Apple.

Voor hulp bij een specifieke probleemoplossingsvraag start u een thread in een van de volgende forums:

* [Het Azure-forum op de ASP.NET site](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Het Azure-forum op Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-webapps.html).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Foutopsporing in Visual Studio
Zie [Foutopsporing in Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) en [Debugging Tips met Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)voor meer informatie over het gebruik van de foutopsporingsmodus in Visual Studio.

### <a name="remote-debugging-in-azure"></a>Foutopsporing op afstand in Azure
Zie de volgende bronnen voor meer informatie over het opsporen van externe debuggen voor App Service-apps en WebJobs:

* [Inleiding tot Azure App-service voor het opsporen van extern opsporen](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Inleiding tot remote Debugging Azure App Service deel 2 - In Remote debugging](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Inleiding tot externe foutopsporing op Azure App Service deel 3 - Omgeving met meerdere instance's en GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs-foutopsporing (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Zie [Debuging .NET Backend in Visual Studio](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx)als uw app een back-end van Azure Web API of Mobile Services gebruikt en u moet die fout opsporen, zie Foutopsporing .NET Backend in Visual Studio .

### <a name="tracing-in-aspnet-applications"></a>Tracering in ASP.NET toepassingen
Er zijn geen grondige en up-to-date introducties van ASP.NET tracing beschikbaar op het internet. Het beste wat je doen is aan de slag met oude inleidende materialen geschreven voor webformulieren, omdat MVC nog niet bestond, en aan te vullen dat met nieuwere blog berichten die zich richten op specifieke kwesties. Enkele goede plaatsen om te beginnen zijn de volgende bronnen:

* [Monitoring en telemetrie (Real-World Cloud Apps bouwen met Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Hoofdstuk e-book met aanbevelingen voor tracering in Azure-cloudtoepassingen.
* [ASP.NET Tracering](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Oud, maar nog steeds een goede bron voor een fundamentele inleiding tot het onderwerp.
* [Listeners traceren](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informatie over trace listeners, maar vermeldt niet de [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener).
* [Walkthrough: ASP.NET Tracing integreren met System.Diagnostics Tracing](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Dit artikel is ook oud, maar bevat wat aanvullende informatie die het inleidende artikel niet dekt.
* [Tracering in ASP.NET MVC Razor Views](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Naast tracering in Razor-weergaven wordt in het bericht ook uitgelegd hoe u een foutfilter maken om alle onbehandelde uitzonderingen in een MVC-toepassing in te loggen. Zie het voorbeeld Global.asax in [Voorbeeld voor fouthandlers](/previous-versions/bb397417(v=vs.140)) op MSDN voor informatie over het registreren van alle niet-afgehandelde uitzonderingen in een webformulierentoepassing. Als u in MVC- of webformulieren bepaalde uitzonderingen wilt registreren, maar de standaardframeworkafhandeling voor deze uitzonderingen wilt laten uitvoeren, u deze vangen en opnieuw gooien, zoals in het volgende voorbeeld:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Logboekregistratie van streamingdiagnoses vanaf de Azure Command Line (plus Glimp!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  De opdrachtregel gebruiken om te doen wat deze zelfstudie laat zien hoe je moet doen in Visual Studio. [Glimpse](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) is een hulpmiddel voor het debuggen van ASP.NET toepassingen.
* [Web Apps Logging and Diagnostics gebruiken - met David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) en [streaming logs van Web Apps - met David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Video's van Scott Hanselman en David Ebbo.

Voor foutlogging is een alternatief voor het schrijven van uw eigen traceringscode het gebruik van een open-source logging framework zoals [ELMAH.](https://nuget.org/packages/elmah/) Voor meer informatie, zie [Scott Hanselman's blog posts over ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

U hoeft ook geen ASP.NET of `System.Diagnostics` tracering te gebruiken om streaminglogboeken van Azure te krijgen. De streaminglogservice voor app-apps streamt een *.txt-* of *.log-bestand* dat wordt gevonden in de map *LogFiles.* *.html* Daarom u uw eigen logboeksysteem maken dat naar het bestandssysteem van de app wordt geschreven en wordt uw bestand automatisch gestreamd en gedownload. Het enige wat u hoeft te doen is toepassingscode schrijven die bestanden maakt in de map *d:\home\logfiles.*

### <a name="analyzing-web-server-logs"></a>Webserverlogboeken analyseren
Zie de volgende bronnen voor meer informatie over het analyseren van webserverlogboeken:

* [LogParser (LogParser)](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Een tool voor het weergeven van gegevens in webserverlogboeken (*.log-bestanden).*
* [IIS-prestatieproblemen of toepassingsfouten oplossen met LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Een inleiding tot de Log Parser-tool die u gebruiken om webserverlogboeken te analyseren.
* [Blog posts van Robert McMurray over het gebruik van LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [De HTTP-statuscode in IIS 7.0, IIS 7.5 en IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Logboeken voor het traceren van mislukte aanvragen analyseren
De Microsoft TechNet-website bevat een sectie [Mislukte aanvraagtracering,](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) die handig kan zijn om te begrijpen hoe u deze logboeken gebruiken. Deze documentatie is echter voornamelijk gericht op het configureren van mislukte aanvraagtracering in IIS, wat u niet doen in Azure App Service.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
