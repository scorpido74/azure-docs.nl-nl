---
title: Problemen met Azure-toepassing Insights oplossen Snapshot Debugger
description: Dit artikel bevat probleemoplossings stappen en informatie om ontwikkel aars te helpen bij het inschakelen of gebruiken van Application Insights Snapshot Debugger.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: brahmnes
ms.author: mbullwin
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: ded4a1a718d2cb061aba5f0d27565633e6cb603a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932094"
---
# <a id="troubleshooting"></a>Problemen oplossen met het inschakelen van Application Insights Snapshot Debugger of het weer geven van moment opnamen
Als u Application Insights Snapshot Debugger voor uw toepassing hebt ingeschakeld, maar geen moment opnamen voor uitzonde ringen ziet, kunt u deze instructies gebruiken om problemen op te lossen. Er kunnen verschillende redenen zijn waarom moment opnamen niet worden gegenereerd. U kunt de status controle van de moment opname uitvoeren om enkele van de mogelijke veelvoorkomende oorzaken te identificeren.

## <a name="use-the-snapshot-health-check"></a>Gebruik de momentopname-statuscontrole
Enkele veelvoorkomende problemen resulteren in de Open fouten opsporen in momentopname niet weergegeven. Met behulp van een verouderde Snapshot Collector, bijvoorbeeld; de dagelijkse uploadlimiet; is bereikt of misschien de momentopname is net lang duurt om te uploaden. Gebruik de momentopname-statuscontrole veelvoorkomende problemen op te lossen.

Er is een koppeling in het deelvenster met uitzondering van de end-to-end tracering weergave die u naar de momentopname-Serverstatus controleren gaat.

![Statuscontrole van de momentopname invoeren](./media/snapshot-debugger/enter-snapshot-health-check.png)

De interactieve, chat-achtige interface uiterlijk voor veelvoorkomende problemen en helpt u om ze te corrigeren.

![Statuscontrole](./media/snapshot-debugger/healthcheck.png)

Als dat niet het probleem is opgelost, klikt u vervolgens verwijzen naar de volgende handmatige stappen voor probleemoplossing.

## <a name="verify-the-instrumentation-key"></a>Controleer of de instrumentatiesleutel

Zorg ervoor dat u de juiste instrumentatiesleutel in uw gepubliceerde toepassing. Normaal gesproken wordt de instrumentatiesleutel gelezen uit het bestand ApplicationInsights.config. Controleer of de waarde is hetzelfde als de instrumentatiesleutel voor de Application Insights-resource die u in de portal ziet.

## <a name="preview-versions-of-net-core"></a>Preview-versies van .NET core
Als de toepassing gebruikmaakt van een preview-versie van .NET core en Snapshot Debugger is ingeschakeld via het [deel venster Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) in de portal, wordt Snapshot debugger mogelijk niet gestart. Volg de instructies in [enable snapshot debugger voor andere omgevingen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) eerst om het [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket met de toepassing toe te voegen, naast het inschakelen ***van*** het [deel venster Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Een upgrade uitvoert naar de nieuwste versie van het NuGet-pakket

Als Snapshot Debugger is ingeschakeld via het [deel venster Application Insights in de portal](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), moet uw toepassing al het meest recente NuGet-pakket uitvoeren. Als Snapshot Debugger is ingeschakeld door het pakket [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet op te nemen, gebruikt u Visual Studio NuGet Package Manager om te controleren of u de nieuwste versie van micro soft. ApplicationInsights. SnapshotCollector gebruikt. Opmerkingen bij de release kunnen u vinden op https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Raadpleeg de logboeken uploader

Nadat een momentopname is gemaakt, wordt een minidump-bestand (dmp) wordt gemaakt op de schijf. Een afzonderlijke uploader-proces dat minidump-bestand maakt en uploadt, samen met eventuele gekoppelde PDB-bestanden, naar Application Insights Snapshot Debugger opslag. Nadat de minidump heeft geüpload, wordt deze verwijderd van schijf. De logboekbestanden voor het proces uploader zijn opgeslagen op schijf. In een App Service-omgeving, vindt u deze logboeken in `D:\Home\LogFiles`. De Kudu-site voor beheer voor App Service gebruiken om deze logboekbestanden.

1. Open uw App Service-toepassing in Azure portal.
2. Klik op **geavanceerde hulpmiddelen**, of zoek naar **Kudu**.
3. Klik op **gaat**.
4. In de **Foutopsporingsconsole** vervolgkeuzelijst Schakel **CMD**.
5. Klik op **logboekbestanden**.

Er is ten minste één bestand met een naam die met begint `Uploader_` of `SnapshotUploader_` en een `.log` extensie. Klik op het juiste pictogram om te downloaden van de logboekbestanden of in een browser openen.
De bestandsnaam bevat een uniek achtervoegsel waarmee het App Service-exemplaar. Als uw App Service-exemplaar wordt gehost op meer dan één machine, zijn er afzonderlijke logboekbestanden voor elke machine. Wanneer u de uploader detecteert een nieuw minidump-bestand, wordt deze in het logboekbestand vastgelegd. Hier volgt een voorbeeld van een geslaagde momentopname en te uploaden:

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
> Het bovenstaande voorbeeld is versie 1.2.0 van het Microsoft.ApplicationInsights.SnapshotCollector NuGet-pakket. In eerdere versies, de uploader-proces wordt genoemd `MinidumpUploader.exe` en het logboek is minder gedetailleerd.

In het vorige voorbeeld de instrumentatiesleutel is `c12a605e73c44346a984e00000000000`. Deze waarde moet overeenkomen met de instrumentatiesleutel voor uw toepassing.
De minidump is gekoppeld aan een momentopname met de ID `139e411a23934dc0b9ea08a626db16c5`. U kunt deze ID later gebruiken vinden van de bijbehorende uitzonderingstelemetrie in Application Insights Analytics.

De uploader scant op nieuwe PDB-bestanden over om de 15 minuten. Hier volgt een voorbeeld:

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

Voor toepassingen die _worden niet_ gehost in App Service, de uploader-logboeken worden in dezelfde map als de minidumps: `%TEMP%\Dumps\<ikey>` (waarbij `<ikey>` uw instrumentatiesleutel is).

## <a name="troubleshooting-cloud-services"></a>Problemen met Cloudservices oplossen
Voor de rollen in Cloud Services zijn de tijdelijke standaardmap te klein voor het opslaan van de minidump-bestanden, waardoor er verloren momentopnamen.
De ruimte die nodig zijn, is afhankelijk van de totale werkset van uw toepassing en het aantal gelijktijdige momentopnamen.
De werkset van een 32-bits ASP.NET-Webrol is meestal tussen 200 MB en 500 MB.
Wacht ten minste twee gelijktijdige momentopnamen.
Bijvoorbeeld, als uw toepassing gebruikmaakt van 1 GB aan totale werkset, moet u ervoor dat er ten minste 2 GB aan schijfruimte voor het opslaan van momentopnamen.
Volg deze stappen voor het configureren van uw rol Service in de Cloud met een eigen lokale resources voor momentopnamen.

1. Een nieuwe lokale resource toevoegen aan uw Service in de Cloud door de Service in de Cloud-definitie (.csdef)-bestand te bewerken. Het volgende voorbeeld definieert een resource met de naam `SnapshotStore` met een grootte van 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Wijzigen van uw rol opstartcode voor het toevoegen van een omgevingsvariabele die naar verwijst de `SnapshotStore` lokale bron. Voor werkrollen, de code moet worden toegevoegd aan van uw rol `OnStart` methode:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Voor Web-rollen (ASP.NET), de code moet worden toegevoegd aan uw webtoepassing `Application_Start` methode:
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

3. Bijwerken van uw rol ApplicationInsights.config-bestand als u wilt overschrijven van de locatie van de tijdelijke map die wordt gebruikt door `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>De schaduwkopie-map te overschrijven

Wanneer de momentopname-Collector wordt gestart, probeert het vinden van een map op de schijf die geschikt is voor het uitvoeren van de momentopname Uploader-proces. De door u gekozen map staat bekend als de Shadow Copy-map.

De Snapshot Collector controleert enkele bekende locaties, te zorgen dat deze machtigingen heeft om te kopiëren van de momentopname Uploader binaire bestanden. De volgende omgevingsvariabelen worden gebruikt:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Als een geschikte map kan niet worden gevonden, Snapshot Collector meldt een fout met de melding _"Kan een geschikte shadow copy-map niet vinden."_

Rapporten van Snapshot Collector als het kopiëren mislukt, een `ShadowCopyFailed` fout.

Als de uploader kan niet worden gestart, Snapshot Collector rapporteert een `UploaderCannotStartFromShadowCopy` fout. De hoofdtekst van het bericht bevat vaak `System.UnauthorizedAccessException`. Deze fout treedt meestal op omdat de toepassing wordt uitgevoerd onder een account met beperkte machtigingen. Het account heeft machtigingen voor schrijven naar de shadow copy-map, maar het is niet gemachtigd voor het uitvoeren van code.

Omdat deze fouten worden gewoonlijk tijdens het opstarten optreden, ze meestal moeten worden gevolgd door een `ExceptionDuringConnect` fout uitspraak _"Uploader kan niet worden gestart."_

Als tijdelijke oplossing voor deze fouten, kunt u de shadow copy-map handmatig via de `ShadowCopyFolder` configuratie-optie. Bijvoorbeeld, u ApplicationInsights.config gebruikt:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Of, als u appsettings.json met een .NET Core-toepassing:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Gebruik Application Insights zoeken naar uitzonderingen met momentopnamen

Wanneer een momentopname wordt gemaakt, is de activerend uitzondering gemarkeerd met een momentopname-ID. Deze momentopname-ID is opgenomen als een aangepaste eigenschap wanneer de uitzonderingstelemetrie naar Application Insights wordt gemeld. Met behulp van **zoeken** in Application Insights, vindt u alle telemetrie met de `ai.snapshot.id` aangepaste eigenschap.

1. Blader naar uw Application Insights-resource in Azure portal.
2. Klik op **Zoeken**.
3. Type `ai.snapshot.id` in het zoekvak en druk op Enter.

![Zoek telemetrie met een momentopname-ID in de portal](./media/snapshot-debugger/search-snapshot-portal.png)

Als deze zoekopdracht geen resultaten oplevert, zijn wordt er zijn geen momentopnamen gerapporteerd naar Application Insights voor uw toepassing in het geselecteerde tijdsbereik.

Als u wilt zoeken naar een specifieke momentopname-ID van de Uploader-Logboeken, typt u deze ID in het zoekvak in. Als u geen telemetrie voor een momentopname waarvan u weet dat is geüpload vinden, volgt u deze stappen:

1. Controleer dat u geïnteresseerd bent in de juiste Application Insights-resource door te controleren of de instrumentatiesleutel.

2. Met behulp van de tijdstempel van het logboek Uploader, pas het filter tijdsbereik van de zoekopdracht om te kunnen krijgen die tijdsbereik.

Als u een uitzondering met deze momentopname-ID nog steeds niet ziet, is niet naar Application Insights-uitzonderingstelemetrie de gerapporteerd. Deze situatie kan zich voordoen als uw toepassing is vastgelopen nadat de momentopname heeft geduurd, maar voordat deze de uitzonderingstelemetrie gemeld. In dit geval, controleert u de App Service-Logboeken onder `Diagnose and solve problems` om te zien of er onverwacht opnieuw wordt opgestart zijn of er is een onverwerkte uitzonderingen.

## <a name="edit-network-proxy-or-firewall-rules"></a>De netwerk-proxy of firewall-regels bewerken

Als uw toepassing verbinding met Internet via een proxy of firewall maakt, moet u mogelijk de regels voor het toestaan van uw toepassing om te communiceren met de service Snapshot Debugger bewerken. Hier volgt [een lijst met IP-adressen en poorten die worden gebruikt door de Snapshot Debugger](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
