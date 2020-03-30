---
title: Problemen met Azure Application Insights Snapshot Debuggerbugger
description: In dit artikel worden stappen en informatie over probleemoplossing gepresenteerd om ontwikkelaars te helpen die problemen hebben met het inschakelen of gebruiken van de foutopsporing van application insights snapshot.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671406"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a>Problemen oplossen waardoor Defoutfout op momentopnamen van toepassingsstatistieken wordt in- of als u momentopnamen bekijken
Als u De buggerbugger van Application Insights voor uw toepassing hebt ingeschakeld, maar geen momentopnamen voor uitzonderingen ziet, u deze instructies gebruiken om problemen op te lossen. Er kunnen veel verschillende redenen zijn waarom momentopnamen niet worden gegenereerd. U de momentopnamestatuscontrole uitvoeren om enkele van de mogelijke veelvoorkomende oorzaken te identificeren.

## <a name="use-the-snapshot-health-check"></a>De momentopnamestatuscontrole gebruiken
Verschillende veelvoorkomende problemen resulteren in de Momentopname Open Foutopsporing die niet wordt weergegeven. Bijvoorbeeld een verouderde Snapshot Collector gebruiken; het bereiken van de dagelijkse uploadlimiet; of misschien is de momentopname is gewoon een lange tijd te uploaden. Gebruik de momentopnamestatuscontrole om veelvoorkomende problemen op te lossen.

Er is een koppeling in het uitzonderingsvenster van de end-to-end trace-weergave die u naar de momentopnamestatuscontrole brengt.

![Momentopnamestatuscontrole invoeren](./media/snapshot-debugger/enter-snapshot-health-check.png)

De interactieve, chat-achtige interface zoekt naar veelvoorkomende problemen en begeleidt u om ze op te lossen.

![Gezondheidscontrole](./media/snapshot-debugger/healthcheck.png)

Als dat het probleem niet oplost, raadpleegt u de volgende handmatige stappen voor het oplossen van problemen.

## <a name="verify-the-instrumentation-key"></a>Controleer de instrumentatiesleutel

Zorg ervoor dat u de juiste instrumentatiesleutel gebruikt in uw gepubliceerde toepassing. Meestal wordt de instrumentatiesleutel gelezen uit het bestand ApplicationInsights.config. Controleer of de waarde hetzelfde is als de instrumentatiesleutel voor de application insights-bron die u in de portal ziet.

## <a name="preview-versions-of-net-core"></a>Voorbeeld van versies van .NET Core
Als de toepassing een voorbeeldversie van .NET Core gebruikt en Snapshot Debugger is ingeschakeld via het [deelvenster Toepassingsinzichten](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) in de portal, wordt de foutopsporing van Momentopname n.m. mogelijk niet gestart. Volg de instructies bij [Snapshot Debugger inschakelen voor andere omgevingen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) om eerst het [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket met de toepassing op te nemen, ***naast*** het inschakelen via het [deelvenster Toepassingsinzichten.](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade naar de nieuwste versie van het NuGet-pakket

Als Snapshot Debugger is ingeschakeld via het [deelvenster Toepassingsinzichten in de portal,](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)moet uw toepassing al het nieuwste NuGet-pakket uitvoeren. Als Snapshot Debugger is ingeschakeld door het [NuGet-pakket Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) op te nemen, gebruikt u nuGet-pakketbeheer van Visual Studio om ervoor te zorgen dat u de nieuwste versie van Microsoft.ApplicationInsights.SnapshotCollector gebruikt. Release notes zijn te vinden ophttps://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>De logboeken van de uploader controleren

Nadat een momentopname is gemaakt, wordt een minidumpbestand (.dmp) op schijf gemaakt. Een afzonderlijk uploaderproces maakt dat minidump-bestand en uploadt het, samen met eventuele bijbehorende PDF's, naar de opslag van Snapshot Debugger van Application Insights. Nadat de minidump is geüpload, wordt deze van de schijf verwijderd. De logbestanden voor het uploaderproces worden op schijf bewaard. In een App Service-omgeving `D:\Home\LogFiles`u deze logboeken vinden in. Gebruik de Kudu-beheersite voor App-service om deze logboekbestanden te vinden.

1. Open uw App Service-toepassing in de Azure-portal.
2. Klik op **Geavanceerd hulpprogramma's**of zoek naar **Kudu**.
3. Klik **op Ga**.
4. Selecteer **CMD**in de vervolgkeuzelijst **Debug-console** .
5. Klik **op LogFiles**.

U moet ten minste één bestand zien `Uploader_` `SnapshotUploader_` met `.log` een naam die begint met of een extensie. Klik op het juiste pictogram om logboekbestanden te downloaden of te openen in een browser.
De bestandsnaam bevat een uniek achtervoegsel dat het exemplaar van de App-service identificeert. Als uw App Service-instantie op meer dan één machine wordt gehost, zijn er afzonderlijke logboekbestanden voor elke machine. Wanneer de uploader een nieuw minidumpbestand detecteert, wordt het opgenomen in het logboekbestand. Hier is een voorbeeld van een succesvolle momentopname en upload:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Het bovenstaande voorbeeld is van versie 1.2.0 van het Microsoft.ApplicationInsights.SnapshotCollector NuGet-pakket. In eerdere versies wordt het uploaderproces aangeroepen `MinidumpUploader.exe` en is het logboek minder gedetailleerd.

In het vorige voorbeeld is `c12a605e73c44346a984e00000000000`de instrumentatietoets . Deze waarde moet overeenkomen met de instrumentatiesleutel voor uw toepassing.
De minidump is gekoppeld aan `139e411a23934dc0b9ea08a626db16c5`een momentopname met de ID . U deze id later gebruiken om de bijbehorende uitzondering telemetrie te vinden in Application Insights Analytics.

De uploader scant ongeveer eens per 15 minuten naar nieuwe PDF's. Hier volgt een voorbeeld:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Voor toepassingen die _niet worden_ gehost in App Service, de uploader logs `%TEMP%\Dumps\<ikey>` zijn `<ikey>` in dezelfde map als de minidumps: (waar is uw instrumentatiesleutel).

## <a name="troubleshooting-cloud-services"></a>Problemen met cloudservices oplossen
Voor rollen in Cloud Services is de standaard tijdelijke map mogelijk te klein om de minidumpbestanden vast te houden, wat leidt tot verloren momentopnamen.
De benodigde ruimte is afhankelijk van de totale werkset van uw toepassing en het aantal gelijktijdige momentopnamen.
De werkset van een 32-bits ASP.NET webrol ligt meestal tussen 200 MB en 500 MB.
Laat ten minste twee gelijktijdige momentopnamen toe.
Als uw toepassing bijvoorbeeld 1 GB aan totale werkset gebruikt, moet u ervoor zorgen dat er ten minste 2 GB schijfruimte is om momentopnamen op te slaan.
Volg deze stappen om uw Cloud Service-rol te configureren met een speciale lokale bron voor momentopnamen.

1. Voeg een nieuwe lokale bron toe aan uw Cloud Service door het cloudservicedefinitiebestand (.csdef) te bewerken. In het volgende voorbeeld `SnapshotStore` wordt een resource gedefinieerd met een grootte van 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Wijzig de opstartcode van uw rol om `SnapshotStore` een omgevingsvariabele toe te voegen die verwijst naar de lokale resource. Voor Werknemersrollen moet de code worden toegevoegd `OnStart` aan de methode van uw rol:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Voor webrollen (ASP.NET) moet de code worden `Application_Start` toegevoegd aan de methode van uw webtoepassing:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Update het bestand ApplicationInsights.config van uw rol bij om de tijdelijke maplocatie te overschrijven die wordt gebruikt door`SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>De map Schaduwkopie overschrijven

Wanneer het momentopnameverzamelaar wordt opgestart, wordt geprobeerd een map op schijf te vinden die geschikt is voor het uitvoeren van het proces momentopnameuploader. De gekozen map staat bekend als de map Schaduwkopie.

De Momentopnameverzamelaar controleert een aantal bekende locaties en controleert of het machtigingen heeft om de binaries van snapshot-uploader te kopiëren. De volgende omgevingsvariabelen worden gebruikt:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- Appdata
- Temp

Als er geen geschikte map kan worden gevonden, meldt Momentopnameverzamelaar een foutmelding met de tekst _'Kon geen geschikte map voor schaduwkopieën vinden'._

Als de kopie mislukt, `ShadowCopyFailed` meldt Momentopnameverzamelaar een fout.

Als de uploader niet kan worden gestart, meldt Snapshot Collector een `UploaderCannotStartFromShadowCopy` fout. Het hoofd van het `System.UnauthorizedAccessException`bericht bevat vaak . Deze fout treedt meestal op omdat de toepassing wordt uitgevoerd onder een account met beperkte machtigingen. Het account heeft toestemming om naar de map met schaduwkopieën te schrijven, maar heeft geen toestemming om code uit te voeren.

Aangezien deze fouten meestal gebeuren tijdens het opstarten, `ExceptionDuringConnect` worden ze meestal gevolgd door een foutmelding met de tekst _'Uploader is niet gestart'._

Als u deze fouten wilt omzeilen, u `ShadowCopyFolder` de map voor schaduwkopieën handmatig opgeven via de configuratieoptie. Bijvoorbeeld met Behulp van ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Of als u appsettings.json gebruikt met een .NET Core-toepassing:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Zoeken naar Toepassingsinzichten gebruiken om uitzonderingen met momentopnamen te vinden

Wanneer een momentopname wordt gemaakt, wordt de werpuitzondering getagd met een momentopname-id. Die momentopname-id wordt opgenomen als een aangepaste eigenschap wanneer de uitzondering telemetrie wordt gerapporteerd aan Application Insights. Met **Zoeken** in Application Insights u `ai.snapshot.id` alle telemetrie vinden met de aangepaste eigenschap.

1. Blader naar uw Application Insights-bron in de Azure-portal.
2. Klik op **Zoeken**.
3. Typ `ai.snapshot.id` het tekstvak Zoeken en druk op Enter.

![Zoeken naar telemetrie met een momentopname-id in de portal](./media/snapshot-debugger/search-snapshot-portal.png)

Als deze zoekopdracht geen resultaten oplevert, zijn er geen momentopnamen gerapporteerd aan Application Insights voor uw toepassing in het geselecteerde tijdsbereik.

Als u wilt zoeken naar een specifieke momentopname-id in de logboeken van Uploader, typt u die id in het vak Zoeken. Als u geen telemetrie vinden voor een momentopname waarvan u weet dat deze is geüpload, voert u de volgende stappen uit:

1. Controleer of u de juiste Application Insights-bron bekijkt door de instrumentatiesleutel te verifiëren.

2. Met behulp van de tijdstempel uit het logboek Uploader past u het filter Tijdbereik van de zoekopdracht aan om dat tijdsbereik te dekken.

Als u nog steeds geen uitzondering ziet met die momentopname-id, is de uitzonderingstelemetrie niet gerapporteerd aan Application Insights. Deze situatie kan gebeuren als uw toepassing is gecrasht nadat deze de momentopname heeft gemaakt, maar voordat de uitzondering telemetrie is gerapporteerd. Controleer in dit geval de `Diagnose and solve problems` logboeken van de appservice onder om te zien of er onverwachte opnieuw opstarten of onverwerkte uitzonderingen zijn geweest.

## <a name="edit-network-proxy-or-firewall-rules"></a>Netwerkproxy- of firewallregels bewerken

Als uw toepassing verbinding maakt met internet via een proxy of een firewall, moet u mogelijk de regels bewerken om uw toepassing te laten communiceren met de Snapshot Debugger-service. De IP's die door Snapshot Debugger worden gebruikt, zijn opgenomen in de Azure Monitor-servicetag.
