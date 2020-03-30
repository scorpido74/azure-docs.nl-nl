---
title: Het instellingenvenster Azure Application Insights Profiler gebruiken | Microsoft Documenten
description: Profiler-status bekijken en profileringssessies starten
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671627"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler configureren

## <a name="updated-profiler-agent"></a>Bijgewerkt Profiler Agent
De triggerfuncties werken alleen met versie 2.6 of nieuwer van de profiler-agent. Als u een Azure App-service uitvoert, wordt uw agent automatisch bijgewerkt. U zien welke versie van de agent u uitvoert als u naar de Kudu-URL voor uw https://yourwebsite.scm.azurewebsites.net/diagnosticserviceswebsite gaat en \DiagnosticServices aan het einde ervan toetetoevoegen, zoals dit:. De Application Insights Profiler Webjob moet versie 2.6 of nieuwer zijn. U een upgrade forceren door uw web-app opnieuw op te starten. 

Als u de profiler uitvoert op een VM of Cloud Service, moet u windows Azure Diagnostics (WAD) extensieversie 16.0.4 of nieuwer hebben geïnstalleerd. U de versie van WAD controleren door u aan te melden bij uw VM en deze map te bekijken: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. De naam van de directory is de versie van WAD die is geïnstalleerd. De Azure VM-agent werkt WAD automatisch bij wanneer er nieuwe versies beschikbaar zijn.

## <a name="profiler-settings-page"></a>Pagina Profiler-instellingen

Als u het instellingenvenster Azure Application Insights Profiler wilt openen, gaat u naar het deelvenster Prestatievenster Toepassingsinzichten en selecteert u de knop **Profiler configureren.**

![Koppeling naar de pagina Profiler-instellingen openen][configure-profiler-entry]

Dat opent een pagina die er als volgt uitziet:

![Pagina Profiler-instellingen][configure-profiler-page]

De profilerpagina **Application Insights Profiler configureren** heeft de volgende functies:

| | |
|-|-|
Profiel nu | Hiermee start u profileringssessies voor alle apps die zijn gekoppeld aan dit exemplaar van Application Insights.
Triggers | Hiermee u triggers configureren die ervoor zorgen dat de profiler wordt uitgevoerd. 
Recente profileringssessies | Geeft informatie weer over eerdere profileringssessies.

## <a name="profile-now"></a>Profiel nu
Met deze optie u op aanvraag een profileringssessie starten. Wanneer u op deze koppeling klikt, beginnen alle profileragents die gegevens naar deze instantie Application Insights verzenden een profiel vast te leggen. Na 5 tot 10 minuten wordt de profielsessie weergegeven in de onderstaande lijst.

Als een gebruiker handmatig een profilersessie kan activeren, hebben ze minimaal "schrijf" toegang nodig voor zijn rol voor de component Application Insights. In de meeste gevallen krijgt u deze toegang automatisch en is er geen extra werk nodig. Als u problemen ondervindt, is de rol van het abonnementsbereik die u wilt toevoegen de rol 'Componentbijdrager component toepassing' . [Meer informatie over beheer van roltoegang met Azure Monitoring](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

## <a name="trigger-settings"></a>Triggerinstellingen
![Flyout triggerinstellingen][trigger-settings-flyout]

Als u op de knop Triggers op de menubalk klikt, wordt het vak triggerinstellingen geopend. U trigger instellen om te beginnen met profileren wanneer het percentage CPU- of geheugengebruik het ingestelde niveau bereikt.

| | |
|-|-|
Aan/ uit knop | Op: profiler kan worden gestart door deze trigger; Uit: profiler wordt niet gestart door deze trigger.
Geheugendrempel | Wanneer dit geheugenpercentage in gebruik is, wordt de profiler gestart.
Duur | Hiermee stelt u de duur in van de duur die de profiler uitvoert wanneer deze wordt geactiveerd.
Cooldown | Hiermee stelt u de tijdindat de profiler wacht voordat u opnieuw controleert op het geheugen- of CPU-gebruik nadat deze is geactiveerd.

## <a name="recent-profiling-sessions"></a>Recente profileringssessies
In dit gedeelte van de pagina vindt u informatie over recente profileringssessies. Een profileringssessie vertegenwoordigt de periode waarin de profileragent een profiel aannam op een van de machines die uw toepassing hosten. U de profielen van een sessie openen door op een van de rijen te klikken. Voor elke sessie laten we zien:

| | |
|-|-|
Getriggerd door | Hoe de sessie is gestart, door een trigger, Profiel Nu of standaardsampling. 
App-naam | Naam van de toepassing die is geprofileerd.
Machine-instantie | Naam van de machine waarop de profiler agent reed.
Tijdstempel | Tijd dat het profiel werd vastgelegd.
Tracee | Aantal sporen dat aan individuele aanvragen is gekoppeld.
CPU-percentage | Percentage CPU dat werd gebruikt terwijl de profiler werd uitgevoerd.
Geheugen % | Percentage geheugen dat werd gebruikt terwijl de profiler werd uitgevoerd.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>Webprestatietests gebruiken om verkeer naar uw toepassing te genereren

U Profiler handmatig activeren met één klik. Stel dat u een webprestatietest uitvoert. U hebt sporen nodig om te begrijpen hoe uw web-app onder belasting wordt uitgevoerd. Controle hebben over wanneer sporen worden vastgelegd is cruciaal, omdat u weet wanneer de load test zal worden uitgevoerd. Maar het willekeurige bemonsteringsinterval kan het missen.

In de volgende secties wordt geïllustreerd hoe dit scenario werkt:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Stap 1: Verkeer genereren naar uw web-app door een webprestatietest te starten

Als uw web-app al binnenkomend verkeer heeft of als u gewoon handmatig verkeer wilt genereren, slaat u deze sectie over en gaat u door naar stap 2.

1. Selecteer**Prestatietesten** **configureren** > in de portal Application Insights . 

1. Als u een nieuwe prestatietest wilt starten, selecteert u de knop **Nieuw.**

   ![nieuwe prestatietest maken][create-performance-test]

1. Configureer in het **testvenster Nieuwe prestaties** de URL van het testdoel. Accepteer alle standaardinstellingen en selecteer **Test uitvoeren** om de belastingstest uit te voeren.

    ![Laadtest configureren][configure-performance-test]

    De nieuwe test wordt eerst in de wachtrij geplaatst, gevolgd door een status van *in uitvoering*.

    ![Load test is ingediend en in de wachtrij][load-test-queued]

    ![Load test wordt uitgevoerd in uitvoering][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Stap 2: Een profiler on-demand sessie starten

1. Wanneer de belastingstest wordt uitgevoerd, start u Profiler om sporen op de web-app vast te leggen terwijl deze wordt geladen.

1. Ga naar het deelvenster **Profiler configureren.**


### <a name="step-3-view-traces"></a>Stap 3: Traceersporen weergeven

Nadat Profiler klaar is met hardlopen, volgt u de instructies op de melding om naar het deelvenster Prestaties te gaan en sporen weer te geven.

## <a name="next-steps"></a>Volgende stappen
[Profiler inschakelen en traceringen weergeven](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
