---
title: Azure-toepassing Insights-Snapshot Debugger voor .NET-Apps
description: Fouten opsporen in momentopnamen worden automatisch verzameld wanneer er uitzonderingen zijn opgetreden in productie .NET-apps
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/23/2019
ms.reviewer: brahmnes
ms.openlocfilehash: 681190a23fd09402ae96c328b7d98044baf3444e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406398"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Fouten opsporen in momentopnamen op uitzonderingen in .NET-apps
Wanneer er een uitzondering optreedt, kunt u automatisch een momentopname voor foutopsporing verzamelen van uw live webtoepassing. De momentopname toont de status van de broncode en variabelen op het moment dat de uitzondering is opgetreden. De Snapshot Debugger (preview) in [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) bewaakt uitzonderingstelemetrie van uw web-app. Deze verzamelt momentopnamen op uw uitzonderingen boven activerende zodat u de informatie die u nodig hebt om problemen in de productieomgeving te diagnosticeren. Neem het [snap shot Collector NuGet-pakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) op in uw toepassing en configureer optioneel verzamelings parameters in [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Moment opnamen worden weer gegeven op [uitzonde ringen](../../azure-monitor/app/asp-net-exceptions.md) in de Application Insights Portal.

U kunt foutopsporingsmomentopnamen in het portaal bekijken om de aanroepstack te zien en variabelen inspecteren bij elk aanroepstackframe. Open moment opnamen met Visual Studio 2019 Enter prise voor een krachtigere fout opsporing met de bron code. In Visual Studio, kunt u ook [Snappoints om interactief momentopnamen te instellen](https://aka.ms/snappoint) zonder te wachten op een uitzondering.

Debug-moment opnamen worden 15 dagen bewaard. Deze bewaarbeleid is ingesteld op basis van de per toepassing. Als u nodig hebt om deze waarde te verhogen, kunt u een toename van aanvragen door een ondersteuningsaanvraag opent in de Azure-portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Application Insights Snapshot Debugger inschakelen voor uw toepassing
Verzameling van de momentopname is beschikbaar voor:
* .NET framework en ASP.NET-toepassingen met .NET Framework 4.5 of hoger.
* .NET core 2.0 en ASP.NET Core 2.0-toepassingen worden uitgevoerd op Windows.

De volgende omgevingen worden ondersteund:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met OS Family 4 of hoger
* [Azure service Fabric Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) die worden uitgevoerd op Windows Server 2012 R2 of hoger
* [Azure virtual machines-en virtuele-machine schaal sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met Windows Server 2012 R2 of hoger
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met Windows Server 2012 R2 of hoger

> [!NOTE]
> Client-toepassingen (bijvoorbeeld WPF, Windows Forms of UWP) worden niet ondersteund.

Raadpleeg de gids voor het [oplossen van problemen](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)als u snapshot debugger hebt ingeschakeld, maar geen moment opnamen ziet.

## <a name="grant-permissions"></a>Machtigingen verlenen

Toegang tot momentopnamen is beveiligd met op rollen gebaseerd toegangsbeheer (RBAC). Als u wilt controleren van een momentopname, moet u eerst worden toegevoegd aan de benodigde rol door de eigenaar van een abonnement.

> [!NOTE]
> Eigenaren en medewerkers automatisch geen deze rol. Als ze momentopnamen weergeven willen, moeten ze zelf toevoegen aan de rol.

Eigenaars van abonnementen toe te kennen de `Application Insights Snapshot Debugger` rol voor gebruikers die zullen momentopnamen inspecteren. Deze rol kan worden toegewezen aan individuele gebruikers of groepen door abonnementseigenaren voor Application Insights-resource van het doel of de resourcegroep of abonnement.

1. Navigeer naar de Application Insights-resource in Azure portal.
1. Klik op **Toegangsbeheer (IAM)** .
1. Klik op de **+ roltoewijzing toevoegen** knop.
1. Selecteer **Application Insights Snapshot Debugger** uit de **rollen** vervolgkeuzelijst.
1. Zoek en voer een naam voor de gebruiker toe te voegen.
1. Klik op de **opslaan** om toe te voegen van de gebruiker aan de rol.


> [!IMPORTANT]
> Momentopnamen kunnen mogelijk persoonlijke en andere gevoelige gegevens in waarden van variabelen en parameter bevatten.

## <a name="view-snapshots-in-the-portal"></a>Moment opnamen weer geven in de portal

Nadat er een uitzonde ring is opgetreden in uw toepassing en er een moment opname is gemaakt, moet u moment opnamen hebben om weer te geven. Het kan 5 tot 10 minuten duren als er een uitzonde ring optreedt in een moment opname die in de portal kan worden weer gegeven. Als u moment opnamen wilt weer geven, selecteert u in het deel venster **fout** de knop **bewerkingen** bij het weer geven van het tabblad **bewerkingen** of selecteert u de knop **uitzonde ringen** bij het weer geven van het tabblad **uitzonde** ringen:

![Pagina fouten](./media/snapshot-debugger/failures-page.png)

Selecteer een bewerking of uitzonde ring in het rechterdeel venster om het deel venster **end-to-end trans actie Details** te openen en selecteer vervolgens de uitzonderings gebeurtenis. Als er een moment opname beschikbaar is voor de gegeven uitzonde ring, wordt in het rechterdeel venster een knop voor het maken van een **moment opname** met details weer gegeven met informatie over de [uitzonde ring](../../azure-monitor/app/asp-net-exceptions.md).

![De knop openen fouten opsporen in momentopname van uitzondering](./media/snapshot-debugger/e2e-transaction-page.png)

In de weergave fouten opsporen in momentopname ziet u een aanroepstack en een deelvenster Variabelen. Wanneer u frames van de aanroepstack in het deelvenster met call stack selecteert, vindt u lokale variabelen en parameters voor deze functie aanroepen in het deelvenster Variabelen.

![Weergave fouten opsporen in momentopname in de portal](./media/snapshot-debugger/open-snapshot-portal.png)

Momentopnamen kunnen gevoelige gegevens bevatten, en standaard ze niet kan worden weergegeven. Als u momentopnamen, hebt u de `Application Insights Snapshot Debugger` rol die aan u zijn toegewezen.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Moment opnamen weer geven in Visual Studio 2017 Enter prise of hoger
1. Klik op de knop **moment opname downloaden** om een `.diagsession` bestand te downloaden dat door Visual Studio Enter prise kan worden geopend.

2. Als u het `.diagsession` bestand wilt openen, moet u het onderdeel Snapshot Debugger Visual Studio hebben geïnstalleerd. Het Snapshot Debugger onderdeel is een vereist onderdeel van de ASP.net-werk belasting in Visual Studio en kan worden geselecteerd in de lijst met afzonderlijke onderdelen in het Visual Studio-installatie programma. Als u een versie van Visual Studio gebruikt die ouder is dan Visual Studio 2017 versie 15,5, moet u de uitbrei ding installeren vanuit de [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Nadat u de momentopnamebestand opent, wordt de pagina Minidump foutopsporing in Visual Studio weergegeven. Klik op **fouten opsporen in beheerde Code** foutopsporing van de momentopname te starten. De momentopname wordt geopend aan de coderegel waar de uitzondering is opgetreden, zodat u kunt fouten opsporen in de huidige status van het proces.

    ![Weergave-momentopname voor foutopsporing in Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

De momentopname van het gedownloade bevat symboolbestanden die zijn gevonden op uw webserver van de toepassing. Deze symboolbestanden moet momentopname van de gegevens koppelen met de broncode. Voor App Service-apps, zorg ervoor dat u het symbool implementatie in te schakelen wanneer u uw web-apps publiceren.

## <a name="how-snapshots-work"></a>De werking van momentopnamen

De Snapshot Collector wordt geïmplementeerd als een [Application Insights Telemetry Processor](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Wanneer uw toepassing wordt uitgevoerd, wordt de telemetrie-Processor van Snapshot Collector wordt toegevoegd aan van de toepassing telemetrie pijplijn.
Telkens wanneer die uw toepassing aanroepen [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), de Snapshot Collector berekent een probleem-ID van het type uitzondering en de activerend methode.
Telkens wanneer uw toepassing TrackException, roept wordt een item verhoogd voor de juiste probleem-ID. Wanneer de teller de `ThresholdForSnapshotting` waarde, de probleem-ID wordt toegevoegd aan een verzameling van plan bent.

De Snapshot Collector bewaakt ook uitzonderingen als ze zijn gegenereerd met een abonnement op de [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) gebeurtenis. Wanneer die gebeurtenis wordt geactiveerd, wordt de probleem-ID van de uitzondering berekend en vergeleken met de probleem-id's in de verzameling van plan bent.
Als er een overeenkomst is, wordt een momentopname van het actieve proces gemaakt. De momentopname is een unieke id toegewezen en de uitzondering is een factureringslabel die id. Nadat de handler FirstChanceException worden geretourneerd, is de thrown uitzondering wordt verwerkt die normaal werken. De uitzondering bereikt uiteindelijk de methode TrackException opnieuw waar, samen met de id van de momentopname, wordt dit apparaat gerapporteerd naar Application Insights.

De belangrijkste proces wordt voortgezet uit te voeren en verkeer voor gebruikers met weinig onderbreking fungeren. De momentopname wordt ondertussen doorgegeven aan de momentopname Uploader-proces. De momentopname van Uploader maakt u een minidump en geüpload naar Application Insights, samen met eventuele relevante (.pdb)-symboolbestanden.

> [!TIP]
> - Een momentopname van een proces is een onderbroken kloon van het proces dat wordt uitgevoerd.
> - Het maken van de momentopname van het duurt ongeveer 10 tot 20 milliseconden.
> - De standaardwaarde voor `ThresholdForSnapshotting` is 1. Dit is ook de minimale waarde. Daarom uw app heeft voor het activeren van de dezelfde uitzondering **tweemaal** voordat er een momentopname wordt gemaakt.
> - Stel `IsEnabledInDeveloperMode` in op true als u genereren van momentopnamen wilt bij foutopsporing in Visual Studio.
> - De frequentie voor het maken van momentopnamen wordt beperkt door de `SnapshotsPerTenMinutesLimit` instelling. Standaard heeft de limiet is een momentopname van elke tien minuten.
> - Niet meer dan 50 momentopnamen per dag kunnen worden geüpload.

## <a name="limitations"></a>Beperkingen

De standaard Bewaar periode voor gegevens is 15 dagen. Voor elke Application Insights-instantie is een maximum aantal moment opnamen van 50 per dag toegestaan.

### <a name="publish-symbols"></a>Symbolen publiceren
De Snapshot Debugger vereist symboolbestanden op de productieserver moet worden gedecodeerd variabelen en om een probleemoplossingservaring in Visual Studio.
Versie 15.2 (of hoger) van Visual Studio 2017 publiceert symbolen voor release standaard bouwt wanneer deze wordt gepubliceerd naar App Service. In eerdere versies, moet u de volgende regel toevoegen aan uw publicatieprofiel `.pubxml` bestand, zodat de symbolen in de releasemodus worden gepubliceerd:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Voor Azure Compute en andere typen, zorg ervoor dat het symboolbestanden zich in dezelfde map van de hoofdtoepassing dll-bestanden (normaal gesproken `wwwroot/bin`) of beschikbaar zijn op het huidige pad.

> [!NOTE]
> Raadpleeg de [documentatie van Visual Studio](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
)voor meer informatie over de verschillende beschik bare symbool opties. Voor de beste resultaten raden wij u aan ' Full ', ' Portable ' of ' embedded ' te gebruiken.

### <a name="optimized-builds"></a>Geoptimaliseerde builds
In sommige gevallen kunnen geen lokale variabelen worden weergegeven in de release builds vanwege optimalisaties die worden toegepast door de JIT-compiler.
De Snapshot Collector kan echter in Azure App Services deoptimize activerend methoden die deel van de verzameling van plan bent uitmaken.

> [!TIP]
> Installeer de Application Insights-Site-extensie in uw App Service deoptimization ondersteuning krijgen.

## <a name="next-steps"></a>Volgende stappen
Application Insights Snapshot Debugger voor uw toepassing inschakelen:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines-en virtuele-machine schaal sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Meer dan Application Insights Snapshot Debugger:
 
* [Snappoints instellen in uw code](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) momentopnamen ophalen zonder te wachten op een uitzondering.
* [Diagnose-uitzonderingen in uw web-apps](../../azure-monitor/app/asp-net-exceptions.md) wordt uitgelegd hoe u meer uitzonderingen zichtbaar maken voor Application Insights.
* [Slimme detectie](../../azure-monitor/app/proactive-diagnostics.md) automatisch detecteert afwijkende prestaties.
