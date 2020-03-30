---
title: Momentopnamefoutopbugger voor Azure Application Insights voor .NET-apps
description: Foutopsporingsmomentopnamen worden automatisch verzameld wanneer uitzonderingen worden gegenereerd in de productie .NET-apps
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275761"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Momentopnamen voor foutopsporing over uitzonderingen in .NET-apps
Wanneer een uitzondering optreedt, u automatisch een foutopsporingsmomentopname verzamelen van uw live webtoepassing. De momentopname toont de status van de broncode en variabelen op het moment dat de uitzondering werd gegooid. De momentopnamefoutbugger in [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) bewaakt uitzonderingstelemetrie vanuit uw web-app. Het verzamelt snapshots op uw top-throwing uitzonderingen, zodat u de informatie die u nodig hebt om problemen in de productie te diagnosticeren. Neem het [NuGet-pakket voor momentopnamen op](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in uw toepassing en configureer optioneel verzamelingsparameters in [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md) Momentopnamen worden weergegeven op [uitzonderingen](../../azure-monitor/app/asp-net-exceptions.md) in de Portal Application Insights.

U kunt foutopsporingsmomentopnamen in het portaal bekijken om de aanroepstack te zien en variabelen inspecteren bij elk aanroepstackframe. Open snapshots met Visual Studio 2019 Enterprise om een krachtigere foutopsporingservaring met broncode te krijgen. In Visual Studio u snappoints ook [instellen op het interactief maken van snapshots](https://aka.ms/snappoint) zonder te wachten op een uitzondering.

Foutopsporingsmomentopnamen worden 15 dagen bewaard. Dit bewaarbeleid wordt per toepassing vastgesteld. Als u deze waarde wilt verhogen, u een verhoging aanvragen door een ondersteuningsaanvraag te openen in de Azure-portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Snapshot debugger voor toepassingsinzichten inschakelen voor uw toepassing
Momentopnameverzameling is beschikbaar voor:
* .NET Framework en ASP.NET toepassingen met .NET Framework 4.5 of hoger.
* .NET Core 2.0- en ASP.NET Core 2.0-toepassingen die op Windows worden uitgevoerd.

De volgende omgevingen worden ondersteund:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met OS-familie 4 of hoger
* [Azure Service Fabric-services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) die worden uitgevoerd op Windows Server 2012 R2 of hoger
* [Azure Virtual Machines en virtuele machineschaalsets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met Windows Server 2012 R2 of hoger
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) met Windows Server 2012 R2 of hoger of Windows 8.1 of hoger

> [!NOTE]
> Clienttoepassingen (bijvoorbeeld WPF, Windows Forms of UWP) worden niet ondersteund.

Als u Snapshot Debugger hebt ingeschakeld, maar geen momentopnamen ziet, raadpleegt u de [handleiding Probleemoplossing.](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)

## <a name="grant-permissions"></a>Machtigingen verlenen

Toegang tot momentopnamen wordt beschermd door op rollen gebaseerd toegangscontrole (RBAC). Als u een momentopname wilt bekijken, moet u eerst door een eigenaar van een abonnement aan de benodigde rol worden toegevoegd.

> [!NOTE]
> Eigenaren en bijdragers hebben deze rol niet automatisch. Als ze momentopnamen willen bekijken, moeten ze zichzelf aan de rol toevoegen.

Eigenaren van abonnementen `Application Insights Snapshot Debugger` moeten de rol toewijzen aan gebruikers die momentopnamen inspecteren. Deze rol kan worden toegewezen aan individuele gebruikers of groepen door abonnementeigenaren voor de doelgroep Application Insights-bron of de brongroep of -abonnement.

1. Navigeer naar de resource Application Insights in de Azure-portal.
1. Klik op **Toegangsbeheer (IAM)**.
1. Klik op de knop **Roltoewijzing toevoegen.**
1. Selecteer **De foutopsporing van momentopnamenomslag voor toepassingsstatistieken** in de vervolgkeuzelijst **Rollen.**
1. Zoek naar en voer een naam in die de gebruiker kan toevoegen.
1. Klik **op** de knop Opslaan om de gebruiker aan de rol toe te voegen.


> [!IMPORTANT]
> Momentopnamen kunnen mogelijk persoonlijke en andere gevoelige informatie bevatten in variabele en parameterwaarden.

## <a name="view-snapshots-in-the-portal"></a>Momentopnamen bekijken in de portal

Nadat er een uitzondering is opgetreden in uw toepassing en er een momentopname is gemaakt, moet u momentopnamen hebben om te bekijken. Het kan 5 tot 10 minuten duren van een uitzondering die zich voordoet tot een momentopname die klaar en zichtbaar is vanaf het portaal. Als u momentopnamen wilt weergeven, selecteert u in het deelvenster **Fout** de knop **Bewerkingen** wanneer u het tabblad **Bewerkingen** bekijkt of selecteert u de knop **Uitzonderingen** wanneer u het tabblad **Uitzonderingen** bekijkt:

![Pagina Fouten](./media/snapshot-debugger/failures-page.png)

Selecteer een bewerking of uitzondering in het rechterdeelvenster om het deelvenster **Transactiedetails van eind tot einde te** openen en selecteer vervolgens de uitzonderingsgebeurtenis. Als er een momentopname beschikbaar is voor de gegeven uitzondering, wordt in het rechterdeelvenster met details voor de [uitzondering](../../azure-monitor/app/asp-net-exceptions.md)een knop **Foutopsporing openen** weergegeven.

![Knop Foutopsporing opslaan bij uitzondering openen](./media/snapshot-debugger/e2e-transaction-page.png)

In de weergave Foutopsporing momentopname ziet u een oproepstack en een deelvenster variabelen. Wanneer u frames van de oproepstack selecteert in het deelvenster oproepstapel, u lokale variabelen en parameters voor die functieaanroep weergeven in het deelvenster Variabelen.

![Foutopsporingsmomentopname weergeven in de portal](./media/snapshot-debugger/open-snapshot-portal.png)

Momentopnamen kunnen gevoelige informatie bevatten en zijn standaard niet zichtbaar. Als u momentopnamen wilt `Application Insights Snapshot Debugger` bekijken, moet u de rol aan u hebben toegewezen.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Momentopnamen bekijken in Visual Studio 2017 Enterprise of hoger
1. Klik **op** de knop `.diagsession` Momentopname downloaden om een bestand te downloaden, dat kan worden geopend door Visual Studio Enterprise.

2. Als u `.diagsession` het bestand wilt openen, moet u de component Snapshot Debugger Visual Studio hebben geïnstalleerd. De component Momentopnamefoutbugger is een vereist onderdeel van de ASP.net-werkbelasting in Visual Studio en kan worden geselecteerd in de lijst Afzonderlijke component in het installatieprogramma Visual Studio. Als u een versie van Visual Studio gebruikt vóór Visual Studio 2017 versie 15.5, moet u de extensie installeren via de [Visual Studio Marketplace.](https://aka.ms/snapshotdebugger)

3. Nadat u het momentopnamebestand hebt geopend, wordt de pagina Foutopsporing minidump in Visual Studio weergegeven. Klik **op Beheerde code foutopsporing** om de foutopsporing van de momentopname te beginnen. De momentopname wordt geopend voor de coderegel waar de uitzondering is gegooid, zodat u de huidige status van het proces debuggen.

    ![Foutopsporingsmomentopname weergeven in Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

De gedownloade momentopname bevat alle symboolbestanden die zijn gevonden op uw webserver. Deze symboolbestanden zijn vereist om momentopnamegegevens te koppelen aan broncode. Voor App Service-apps moet u symboolimplementatie inschakelen wanneer u uw web-apps publiceert.

## <a name="how-snapshots-work"></a>Hoe momentopnamen werken

De Snapshot Collector wordt geïmplementeerd als een [Application Insights Telemetry Processor](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Wanneer uw toepassing wordt uitgevoerd, wordt de Telemetrieprocessor snapshotcollector toegevoegd aan de telemetriepijplijn van uw toepassing.
Elke keer dat uw toepassing [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions)aanroept, berekent de momentopnamecollector een probleem-id van het type uitzondering dat wordt gegooid en de werpmethode.
Elke keer dat uw toepassing TrackException aanroept, wordt een teller verhoogd voor de juiste probleem-id. Wanneer de teller `ThresholdForSnapshotting` de waarde bereikt, wordt de probleem-id toegevoegd aan een incassoplan.

Het momentopnameprogramma controleert ook uitzonderingen wanneer ze worden gegenereerd door u te abonneren op de gebeurtenis [AppDomain.CurrentDomain.FirstChanceException.](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) Wanneer die gebeurtenis wordt geactiveerd, wordt de probleem-id van de uitzondering berekend en vergeleken met de probleem-id's in het incassoplan.
Als er een overeenkomst is, wordt een momentopname van het lopende proces gemaakt. Aan de momentopname wordt een unieke id toegewezen en de uitzondering wordt met die id gestempeld. Nadat de handler FirstChanceException is geretourneerd, wordt de foutie als normaal verwerkt. Uiteindelijk bereikt de uitzondering de TrackException-methode opnieuw, waar deze, samen met de momentopname-id, wordt gerapporteerd aan Application Insights.

Het belangrijkste proces blijft draaien en dienen verkeer aan gebruikers met weinig onderbreking. Ondertussen wordt de momentopname overhandigd aan het Snapshot Uploader-proces. De Snapshot Uploader maakt een minidump en uploadt deze naar Application Insights, samen met relevante symboolbestanden (.pdb).

> [!TIP]
> - Een momentopname van een proces is een zwevende kloon van het lopende proces.
> - Het maken van de momentopname duurt ongeveer 10 tot 20 milliseconden.
> - De standaardwaarde `ThresholdForSnapshotting` voor is 1. Dit is ook de minimumwaarde. Daarom moet uw app twee **keer** dezelfde uitzondering activeren voordat een momentopname wordt gemaakt.
> - Stel `IsEnabledInDeveloperMode` in op true als u snapshots wilt genereren tijdens het foutspelen in Visual Studio.
> - De momentopnamecreatiesnelheid wordt `SnapshotsPerTenMinutesLimit` beperkt door de instelling. Standaard is de limiet één momentopname per tien minuten.
> - Er mogen niet meer dan 50 momentopnamen per dag worden geüpload.

## <a name="limitations"></a>Beperkingen

De standaardbewaarperiode voor gegevens is 15 dagen. Voor elk instantie Application Insights is een maximum aantal van 50 momentopnamen per dag toegestaan.

### <a name="publish-symbols"></a>Symbolen publiceren
De momentopnamefoutfout opsporing vereist symboolbestanden op de productieserver om variabelen te decoderen en een foutopsporingservaring in Visual Studio te bieden.
Versie 15.2 (of hoger) van Visual Studio 2017 publiceert standaard symbolen voor releasebuilds wanneer deze wordt gepubliceerd in App Service. In eerdere versies moet u de volgende regel `.pubxml` toevoegen aan uw publicatieprofielbestand, zodat symbolen in de releasemodus worden gepubliceerd:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Controleer voor Azure Compute en andere typen of de symboolbestanden zich in dezelfde map `wwwroot/bin`van de hoofdtoepassing bevinden .dll (meestal ) of beschikbaar zijn op het huidige pad.

> [!NOTE]
> Raadpleeg de [documentatie van Visual Studio](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
)voor meer informatie over de verschillende symboolopties die beschikbaar zijn. Voor de beste resultaten raden we aan om "Vol", "Draagbaar" of "Embedded" te gebruiken.

### <a name="optimized-builds"></a>Geoptimaliseerde builds
In sommige gevallen kunnen lokale variabelen niet worden bekeken in releasebuilds vanwege optimalisaties die worden toegepast door de JIT-compiler.
In Azure App Services kan het momentopnameverzamelaar- echter de optimalisatiemethoden deoptimaliseren die deel uitmaken van het verzamelingsplan.

> [!TIP]
> Installeer de application insights-site-extensie in uw app-service om ondersteuning voor deoptimalisatie te krijgen.

## <a name="next-steps"></a>Volgende stappen
Foutopsporing van momentopnamenomzoomen voor toepassing inschakelen:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines en virtuele machineschaalsets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Beyond Application Insights Snapshot Debugger:
 
* [Stel snappunten in uw code in](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) om momentopnamen te krijgen zonder te wachten op een uitzondering.
* [In de diagnose van uitzonderingen in uw webapps](../../azure-monitor/app/asp-net-exceptions.md) wordt uitgelegd hoe u meer uitzonderingen zichtbaar maken voor Application Insights.
* [Slimme detectie](../../azure-monitor/app/proactive-diagnostics.md) detecteert automatisch prestatieafwijkingen.
