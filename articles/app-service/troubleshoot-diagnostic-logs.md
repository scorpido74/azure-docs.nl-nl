---
title: Diagnostische logboekregistratie inschakelen
description: Meer informatie over het inschakelen van diagnostische logboekregistratie en het toevoegen van instrumentatie aan uw toepassing, en hoe u toegang krijgt tot de door Azure vastgelegde informatie.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769990"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Diagnostische logboekregistratie inschakelen voor apps in Azure App Service
## <a name="overview"></a>Overzicht
Azure biedt ingebouwde diagnostiek om te helpen bij het debuggen van een [App Service-app.](overview.md) In dit artikel leert u hoe u diagnostische logboekregistratie inschakelt en instrumentatie toevoegt aan uw toepassing, en hoe u toegang krijgt tot de gegevens die door Azure zijn geregistreerd.

In dit artikel worden de [Azure-portal](https://portal.azure.com) en Azure CLI gebruikt om met diagnostische logboeken te werken. Zie [Azure oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)voor informatie over het werken met diagnostische logboeken met Visual Studio.

> [!NOTE]
> Naast de registratie-instructies in dit artikel, is er nieuwe, geïntegreerde logging mogelijkheid met Azure Monitoring. Meer informatie over deze mogelijkheid vindt u in de sectie [Logboeken verzenden naar Azure Monitor (voorbeeld).](#send-logs-to-azure-monitor-preview) 
>
>

|Type|Platform|Locatie|Beschrijving|
|-|-|-|-|
| Toepassingslogboeken | Windows, Linux | App Service-bestandssysteem en/of Azure Storage-blobs | Registreert berichten die worden gegenereerd door uw toepassingscode. De berichten kunnen worden gegenereerd door het webframework dat u kiest, of uit uw toepassingscode rechtstreeks met behulp van het standaard registratiepatroon van uw taal. Aan elk bericht wordt een van de volgende categorieën toegewezen: **Kritiek**, **Fout**, **Waarschuwing**, **Info**, **Foutopsporing**en **Traceren**. U selecteren hoe verbose u wilt dat de logboekregistratie is door het ernstniveau in te stellen wanneer u toepassingslogboekregistratie inschakelt.|
| Logboekregistratie van webserver| Windows | App Service-bestandssysteem of Azure Storage-blobs| Ruwe HTTP-aanvraaggegevens in de [uitgebreide logboekbestandsindeling w3C](/windows/desktop/Http/w3c-logging). Elk logboekbericht bevat gegevens zoals de HTTP-methode, resource URI, client-IP, clientpoort, gebruikersagent, antwoordcode, enzovoort. |
| Gedetailleerde foutberichten| Windows | App Service-bestandssysteem | Kopieën *.htm* van de .htm-foutpagina's die naar de clientbrowser zouden zijn verzonden. Om veiligheidsredenen mogen gedetailleerde foutpagina's niet worden verzonden naar clients in productie, maar App Service kan de foutpagina opslaan telkens wanneer er een toepassingsfout optreedt met HTTP-code 400 of hoger. De pagina kan informatie bevatten die kan bepalen waarom de server de foutcode retourneert. |
| Mislukte tracering van aanvragen | Windows | App Service-bestandssysteem | Gedetailleerde traceringsinformatie over mislukte aanvragen, inclusief een spoor van de IIS-componenten die worden gebruikt om de aanvraag te verwerken en de tijd die in elk onderdeel is opgenomen. Het is handig als u de prestaties van de site wilt verbeteren of een specifieke HTTP-fout wilt isoleren. Voor elke mislukte aanvraag, die het XML-logboekbestand bevat, wordt één map gegenereerd en het XSL-stijlblad waarmee het logboekbestand moet worden weergegeven. |
| Logboekregistratie voor implementatie | Windows, Linux | App Service-bestandssysteem | Logboeken voor wanneer u inhoud publiceert in een app. Implementatielogboekregistratie gebeurt automatisch en er zijn geen configureerbare instellingen voor implementatielogboekregistratie. Hiermee u bepalen waarom een implementatie is mislukt. Als u bijvoorbeeld een [aangepast implementatiescript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)gebruikt, u implementatielogboekregistratie gebruiken om te bepalen waarom het script mislukt. |

> [!NOTE]
> App Service biedt een speciale, interactieve diagnostische tool om u te helpen problemen op te lossen met uw toepassing. Zie overzicht van [Azure App Service-diagnoses](overview-diagnostics.md)voor meer informatie.
>
> Bovendien u andere Azure-services gebruiken om de beheer- en bewakingsmogelijkheden van uw app te verbeteren, zoals [Azure Monitor.](../azure-monitor/app/azure-web-apps.md)
>

## <a name="enable-application-logging-windows"></a>Toepassingslogboekregistratie inschakelen (Windows)

> [!NOTE]
> Toepassingslogboekregistratie voor blobopslag kan alleen opslagaccounts gebruiken in dezelfde regio als de App-service

Als u logboekregistratie voor Windows-apps in schakelt in de [Azure-portal,](https://portal.azure.com)navigeert u naar uw app en selecteert u **Logboeken van App-service**.

Selecteer **Aan** voor **application logging (Filesystem)** of **Application Logging (Blob)** of beide. 

De **filesystem** optie is voor tijdelijke debugging doeleinden, en schakelt zichzelf uit in 12 uur. De **blob-optie** is voor langdurige logboekregistratie en heeft een blobopslagcontainer nodig om logboeken naar te schrijven.  De **blob-optie** bevat ook aanvullende informatie in de logboekberichten, zoals de ID`InstanceId`van de`Tid`instantie van de oorsprongVM van het logboekbericht ( ), thread-id ( ) en een meer gedetailleerde tijdstempel ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)).

> [!NOTE]
> Momenteel kunnen alleen .NET-toepassingslogboeken naar de blobopslag worden geschreven. Java, PHP, Node.js, Python-toepassingslogboeken kunnen alleen worden opgeslagen op het App Service-bestandssysteem (zonder codewijzigingen om logboeken naar externe opslag te schrijven).
>
> Als u [de toegangssleutels van uw opslagaccount opnieuw genereert,](../storage/common/storage-create-storage-account.md)moet u ook de desbetreffende logboekconfiguratie opnieuw instellen om de bijgewerkte toegangssleutels te gebruiken. Om dit te doen:
>
> 1. Stel op het tabblad **Configureren** de desbetreffende logboekfunctie in **op Uit**. Sla uw instelling op.
> 2. Schakel logboekregistratie weer in bij de blob van het opslagaccount. Sla uw instelling op.
>
>

Selecteer het **niveau**of het niveau van de details om u aan te melden. In de volgende tabel worden de logboekcategorieën weergegeven die in elk niveau zijn opgenomen:

| Niveau | Opgenomen categorieën |
|-|-|
|**Handicap** | Geen |
|**Fout** | Fout, kritiek |
|**Waarschuwing** | Waarschuwing, fout, kritiek|
|**Informatie** | Info, Waarschuwing, Fout, Kritiek|
|**Uitgebreide** | Tracering, Foutopsporing, Info, Waarschuwing, Fout, Kritiek (alle categorieën) |

Als u klaar bent, selecteert u **Opslaan**.

## <a name="enable-application-logging-linuxcontainer"></a>Toepassingslogboekregistratie inschakelen (Linux/Container)

Als u logboekregistratie voor Linux-apps of aangepaste container-apps in de [Azure-portal](https://portal.azure.com)wilt inschakelen, navigeert u naar uw app en selecteert u **Logboeken van App-service**.

Selecteer **Bestandssysteem**in **Toepassingslogboekregistratie**.

Geef **in Quota (MB)** het schijfquotum voor de toepassingslogboeken op. Stel in **bewaarperiode (dagen)** het aantal dagen in dat de logboeken moeten worden bewaard.

Als u klaar bent, selecteert u **Opslaan**.

## <a name="enable-web-server-logging"></a>Logboekregistratie van webserverinschakelen inschakelen

Als u serverlogboekregistratie voor Windows-apps in schakelt in de [Azure-portal,](https://portal.azure.com)navigeert u naar uw app en selecteert u **Logboeken van App-service**.

Selecteer **Opslag** voor logboekregistratie op blobopslag of **Bestandssysteem** om logboeken op te slaan in het app-servicebestandssysteem voor **logboekregistratie.** 

Stel in **bewaarperiode (dagen)** het aantal dagen in dat de logboeken moeten worden bewaard.

> [!NOTE]
> Als u [de toegangssleutels van uw opslagaccount regenereert,](../storage/common/storage-create-storage-account.md)moet u de desbetreffende logboekconfiguratie opnieuw instellen om de bijgewerkte sleutels te gebruiken. Om dit te doen:
>
> 1. Stel op het tabblad **Configureren** de desbetreffende logboekfunctie in **op Uit**. Sla uw instelling op.
> 2. Schakel logboekregistratie weer in bij de blob van het opslagaccount. Sla uw instelling op.
>
>

Als u klaar bent, selecteert u **Opslaan**.

## <a name="log-detailed-errors"></a>Gedetailleerde fouten registreren

Als u de foutpagina of mislukte aanvraagtracering voor Windows-apps in de [Azure-portal](https://portal.azure.com)wilt opslaan, navigeert u naar uw app en selecteert u **Logboeken van App-service**.

Selecteer onder **Gedetailleerde foutlogboekregistratie** of **Tracering mislukte**aanvragen **de**optie **Op,** en selecteer Opslaan .

Beide typen logboeken worden opgeslagen in het app-servicebestandssysteem. Er blijven maximaal 50 fouten (bestanden/mappen) behouden. Wanneer het aantal HTML-bestanden meer dan 50 bedraagt, worden de oudste 26 fouten automatisch verwijderd.

## <a name="add-log-messages-in-code"></a>Logboekberichten toevoegen in code

In uw toepassingscode gebruikt u de gebruikelijke registratiefaciliteiten om logboekberichten naar de toepassingslogboeken te verzenden. Bijvoorbeeld:

- ASP.NET toepassingen de klasse [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) kunnen gebruiken om informatie aan te melden bij het logboek voor toepassingsdiagnostiek. Bijvoorbeeld:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Standaard gebruikt ASP.NET Core de [microsoft.extensions.logging.AzureAppServices-logboekprovider.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Zie [ASP.NET Core-logboekregistratie in Azure voor](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)meer informatie.

## <a name="stream-logs"></a>Logboeken streamen

Voordat u logboeken in realtime streamt, schakelt u het gewenste logboektype in. Alle informatie die is geschreven naar bestanden die eindigen in .txt, .log of .htm die zijn opgeslagen in de map */LogFiles* (d:/home/logfiles) wordt gestreamd door App Service.

> [!NOTE]
> Sommige typen logboekbuffer schrijven naar het logboekbestand, wat kan resulteren in gebeurtenissen buiten de volgorde in de stream. Een vermelding van een toepassingslogboek dat optreedt wanneer een gebruiker een pagina bezoekt, kan bijvoorbeeld in de stream worden weergegeven vóór de bijbehorende HTTP-logboekvermelding voor de paginaaanvraag.
>

### <a name="in-azure-portal"></a>In Azure-portal

Als u logboeken wilt streamen in de [Azure-portal,](https://portal.azure.com)navigeert u naar uw app en selecteert u **Logboekstream**. 

### <a name="in-cloud-shell"></a>In Cloud Shell

Als u logboeken live wilt streamen in [Cloud Shell,](../cloud-shell/overview.md)gebruikt u de volgende opdracht:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Als u specifieke gebeurtenissen, zoals fouten, wilt filteren, gebruikt u de parameter **--Filter.** Bijvoorbeeld:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Als u specifieke logboektypen, zoals HTTP, wilt filteren, gebruikt u de parameter **--Pad.** Bijvoorbeeld:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>In lokale terminal

Als u logboeken wilt streamen in de lokale console, [installeert u Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) en [meldt u zich aan bij uw account.](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) Eenmaal aangemeld, volgde u de [instructies voor Cloud Shell](#in-cloud-shell)

## <a name="access-log-files"></a>Logboekbestanden openen

Als u de optie Azure Storage blobs configureert voor een logboektype, hebt u een clienthulpprogramma nodig dat werkt met Azure Storage. Zie [Azure Storage Client Tools](../storage/common/storage-explorers.md)voor meer informatie.

Voor logboeken die zijn opgeslagen in het app-bestandssysteem, u het ZIP-bestand in de browser downloaden op:

- Linux/container apps:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-apps:`https://<app-name>.scm.azurewebsites.net/api/dump`

Voor Linux/container-apps bevat het ZIP-bestand consoleuitvoerlogboeken voor zowel de dockerhost als de dockercontainer. Voor een geschaalde app bevat het ZIP-bestand één set logboeken voor elke instantie. In het app-servicebestandssysteem zijn deze logboekbestanden de inhoud van de map */home/LogFiles.*

Voor Windows-apps bevat het ZIP-bestand de inhoud van de map *D:\Home\LogFiles* in het app-servicebestandssysteem. Het heeft de volgende structuur:

| Logboektype | Directory | Beschrijving |
|-|-|-|
| **Toepassingslogboeken** |*/LogFiles/Application/* | Bevat een of meer tekstbestanden. De indeling van de logboekberichten is afhankelijk van de registratieprovider die u gebruikt. |
| **Mislukte aanvraagsporen** | */LogFiles/W3SVC##########/* | Bevat XML-bestanden en een XSL-bestand. U de opgemaakte XML-bestanden in de browser bekijken. |
| **Gedetailleerde foutlogboeken** | */LogFiles/DetailedErrors/* | Bevat HTM-foutbestanden. U de HTM-bestanden in de browser bekijken.<br/>Een andere manier om de mislukte aanvraagsporen weer te geven, is door naar uw app-pagina in de portal te navigeren. Selecteer in het linkermenu **Diagnose en los problemen op**en zoek vervolgens naar logboeken voor het traceren van mislukte **aanvragen**en klik vervolgens op het pictogram om te bladeren en het gewenste spoor weer te geven. |
| **Webserverlogboeken** | */LogFiles/http/RawLogs/* | Bevat tekstbestanden die zijn opgemaakt met de [w3C-indeling voor uitgebreide logboekbestanden](/windows/desktop/Http/w3c-logging). Deze informatie kan worden gelezen met behulp van een teksteditor of een hulpprogramma zoals [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>App Service ondersteunt de `s-computername` `s-ip`velden `cs-version` of velden niet. |
| **Implementatielogboeken** | */LogFiles/Git/* *en/deployments/* | Bevatten logboeken die zijn gegenereerd door de interne implementatieprocessen en logboeken voor Git-implementaties. |

## <a name="send-logs-to-azure-monitor-preview"></a>Logboeken verzenden naar Azure Monitor (voorbeeld)

Met de nieuwe [Azure Monitor-integratie](https://aka.ms/appsvcblog-azmon)u [diagnostische instellingen (voorbeeld) maken](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) om logboeken naar opslagaccounts, gebeurtenishubs en logboekanalyse te verzenden. 

> [!div class="mx-imgBorder"]
> ![Diagnostische instellingen (voorbeeld)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Ondersteunde logboektypen

In de volgende tabel worden de ondersteunde logboektypen en -beschrijvingen weergegeven: 

| Logboektype | Windows-ondersteuning | Ondersteuning voor Linux (Docker) | Beschrijving |
|-|-|-|
| AppServiceConsoleLogs | Tba | Ja | Standaarduitvoer en standaardfout |
| AppServiceHTTPLogs | Ja | Ja | Webserverlogboeken |
| AppServiceEnvironmentPlatformLogs | Ja | Ja | App-serviceomgeving: schalen, configuratiewijzigingen en statuslogboeken|
| AppServiceAuditLogs | Ja | Ja | Inlogactiviteit via FTP en Kudu |
| AppServiceFileAuditLogs | Ja | NOG TE BEPALEN | Bestandswijzigingen via FTP en Kudu |
| AppServiceAppLogs AppServiceAppLogs | Tba | Java SE & Tomcat | Toepassingslogboeken |

## <a name="next-steps"></a><a name="nextsteps"></a>Volgende stappen
* [Querylogboeken met Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Azure-appservice controleren](web-sites-monitor.md)
* [Azure App-service oplossen in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [App-logboeken analyseren in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
