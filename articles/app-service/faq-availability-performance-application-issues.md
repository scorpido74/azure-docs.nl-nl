---
title: Veelgestelde vragen over de prestaties van toepassingen
description: Krijg antwoorden op veelgestelde vragen over beschikbaarheid, prestaties en toepassingsproblemen in Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 433f5885c7f057226e78c4ae57e03d7619004d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259862"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Veelgestelde vragen over de prestaties van toepassingen voor Web Apps in Azure

> [!NOTE]
> Sommige van de onderstaande richtlijnen werken mogelijk alleen op Windows- of Linux App-services. Linux App Services worden bijvoorbeeld standaard in 64-bits modus uitgevoerd.
>

In dit artikel vindt u antwoorden op veelgestelde vragen (veelgestelde vragen) over problemen met de prestaties van toepassingen voor de [Web Apps-functie van Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Waarom is mijn app traag?

Meerdere factoren kunnen bijdragen aan trage app-prestaties. Zie Trage prestaties [van webapps oplossen voor](troubleshoot-performance-degradation.md)gedetailleerde stappen voor het oplossen van problemen.

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hoe los ik een hoog CPU-verbruikscenario op?

In sommige scenario's met een hoog CPU-verbruik heeft uw app mogelijk meer computerbronnen nodig.In dat geval u overwegen te schalen naar een hogere servicelaag, zodat de toepassing alle resources krijgt die deze nodig heeft. Andere keren, hoog CPU-verbruik kan worden veroorzaakt door een slechte lus of door een codering praktijk. Inzicht krijgen in wat leidt tot een verhoogd CPU-verbruik is een tweedelige proces. Maak eerst een procesdump en analyseer vervolgens de procesdump. Zie Een dumpbestand vastleggen en analyseren voor een [hoog CPU-verbruik voor Web Apps voor](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/)meer informatie.

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hoe los ik een scenario met een hoog geheugenverbruik op?

In sommige scenario's met een hoog geheugenverbruik heeft uw app mogelijk meer computerbronnen nodig.In dat geval u overwegen te schalen naar een hogere servicelaag, zodat de toepassing alle resources krijgt die deze nodig heeft. Andere keren kan een bug in de code een geheugenlek veroorzaken. Een coderingspraktijk kan ook het geheugenverbruik verhogen.Inzicht krijgen in wat een hoog geheugenverbruik teweegbrengt, is een proces in twee delen. Maak eerst een procesdump en analyseer vervolgens de procesdump. Crash Diagnoser uit de Azure Site Extension Gallery kan beide stappen efficiënt uitvoeren. Zie [Een dumpbestand vastleggen en analyseren voor met tussenpozen met veel geheugen voor webapps voor](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/)meer informatie.

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hoe automatiseer ik App Service webapps met PowerShell?

U PowerShell-cmdlets gebruiken om webapps van App Service te beheren en te onderhouden. In onze blogpost [Automatiseer we webapps die worden gehost in Azure App Service met PowerShell,](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)hoe u PowerShell-cmdlets op basis van Azure Resource Manager gebruiken om veelvoorkomende taken te automatiseren. De blogpost bevat ook voorbeeldcode voor verschillende beheertaken van webapps. Zie [Az.Websites](/powershell/module/az.websites)voor beschrijvingen en syntaxis voor alle cmdlets van App Service-webapps.

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hoe kan ik de gebeurtenislogboeken van mijn web-app bekijken?

Ga als het gaat om het weergeven van de gebeurtenislogboeken van uw web-app:

1. Meld u aan op uw [Kudu-website.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Selecteer in het menu **DEBug Console** > **CMD**.
3. Selecteer de map **LogFiles.**
4. Als u gebeurtenislogboeken wilt weergeven, selecteert u het potloodpictogram naast **eventlog.xml**.
5. Voer de PowerShell-cmdlet `Save-AzureWebSiteLog -Name webappname`uit om de logboeken te downloaden.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hoe leg ik een geheugendump in de gebruikersmodus van mijn web-app vast?

Ga als lid van het nieuwe app-geheugen met de gebruiker:

1. Meld u aan op uw [Kudu-website.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Selecteer het menu **Process Explorer.**
3. Klik met de rechtermuisknop op het **w3wp.exe-proces** of uw WebJob-proces.
4. Selecteer Volledige > **dump en volledige dump** **downloaden.**

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hoe kan ik informatie op procesniveau weergeven voor mijn web-app?

U hebt twee opties voor het weergeven van informatie op procesniveau voor uw web-app:

*   In Azure Portal:
    1. Open de **Process Explorer** voor de web-app.
    2. Als u de details wilt zien, selecteert u het **w3wp.exe-proces.**
*   In de Kudu console:
    1. Meld u aan op uw [Kudu-website.](https://*yourwebsitename*.scm.azurewebsites.net)
    2. Selecteer het menu **Process Explorer.**
    3. Selecteer **Eigenschappen**voor het **w3wp.exe-proces** .

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Wanneer ik naar mijn app blader, zie ik 'Fout 403 - Deze web-app is gestopt'. Hoe los ik dit op?

Drie voorwaarden kunnen deze fout veroorzaken:

* De web-app heeft een factureringslimiet bereikt en uw site is uitgeschakeld.
* De web-app is gestopt in de portal.
* De web-app heeft een limiet voor het resourcequotum bereikt die mogelijk van toepassing is op een serviceplan voor vrije of gedeelde schaal.

Volg de stappen in [Web Apps: 'Fout 403 – Deze webapp is gestopt'](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/)om te zien wat de oorzaak van de fout is en om het probleem op te lossen.

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Waar kan ik meer te weten komen over quota en limieten voor verschillende App Service-abonnementen?

Zie [App-servicelimieten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor informatie over quota en limieten. 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hoe verklein ik de reactietijd voor de eerste aanvraag na inactieve tijd?

Web-apps worden standaard leeggemaakt als ze gedurende een bepaalde periode niet actief zijn. Op deze manier kan het systeem grondstoffen besparen. Het nadeel is dat het antwoord op het eerste verzoek nadat de web-app is gelost langer is, zodat de web-app reacties kan laden en weergeven. In Serviceplannen voor basis en standaard u de instelling **Altijd ingeschakeld** inschakelen om de app altijd geladen te houden. Dit elimineert langere laadtijden nadat de app niet actief is. Ga als u de instelling **Altijd op** om te gaan:

1. Ga in de Azure-portal naar uw web-app.
2. **Configuratie selecteren**
3. Selecteer **Algemene instellingen**.
4. Selecteer **Always On** **Aan**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hoe schakel ik mislukte aanvraagtracering in?

Mislukte aanvraagtracering inschakelen:

1. Ga in de Azure-portal naar uw web-app.
3. Selecteer **Alle diagnostische logboeken voor instellingen** > **Diagnostics Logs**.
4. Selecteer **Failed Request Tracing** **Op**.
5. Selecteer **Opslaan**.
6. Selecteer in het web-app-blad De optie **Extra**.
7. Selecteer **Visual Studio Online**.
8. Als de instelling niet **aan**staat, selecteert u **Aan**.
9. Selecteer **Gaan**.
10. Selecteer **Web.config**.
11. Voeg in system.webServer deze configuratie toe (om een specifieke URL vast te leggen):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Als u problemen met de trage prestaties wilt oplossen, voegt u deze configuratie toe (als het vastleggen van het verzoek meer dan 30 seconden duurt):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Om de mislukte aanvraagsporen te downloaden, ga je in de [portal](https://portal.azure.com)naar je website.
15. Selecteer **Tools** > **Kudu** > **Go**.
18. Selecteer in het menu **DEBug Console** > **CMD**.
19. Selecteer de map **LogFiles** en selecteer de map met een naam die begint met **W3SVC**.
20. Als u het XML-bestand wilt zien, selecteert u het potloodpictogram.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Ik zie het bericht 'Worker Process requested recycle due to 'Percent Memory' limit." Hoe pak ik dit probleem aan?

De maximaal beschikbare hoeveelheid geheugen voor een 32-bits proces (zelfs op een 64-bits besturingssysteem) is 2 GB. Standaard is het werkproces ingesteld op 32-bits in App Service (voor compatibiliteit met verouderde webtoepassingen).

Overweeg over te schakelen naar 64-bits processen, zodat u profiteren van het extra geheugen dat beschikbaar is in uw webworker-rol. Dit activeert een web-app opnieuw opstarten, dus dienovereenkomstig plannen.

Houd er ook rekening mee dat een 64-bits omgeving een Basic- of Standard-serviceplan vereist. Gratis en gedeelde abonnementen worden altijd uitgevoerd in een 32-bits omgeving.

Zie [Web-apps configureren in App-service voor](configure-common.md)meer informatie.

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Waarom is mijn verzoek time-out na 230 seconden?

Azure Load Balancer heeft een standaard instelling voor idle time-out van vier minuten. Dit is over het algemeen een redelijke reactietermijn voor een webaanvraag. Als uw web-app achtergrondverwerking vereist, raden we u aan Azure WebJobs te gebruiken. De Azure-webapp kan WebJobs aanroepen en een melding krijgen wanneer de achtergrondverwerking is voltooid. U kiezen uit meerdere methoden voor het gebruik van WebJobs, waaronder wachtrijen en triggers.

WebJobs is ontworpen voor achtergrondverwerking. U zoveel achtergrondverwerking doen als u wilt in een WebJob. Zie [Achtergrondtaken uitvoeren met WebJobs](webjobs-create.md)voor meer informatie over WebJobs.

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core-toepassingen die worden gehost in App Service, reageren soms niet meer. Hoe los ik dit probleem op?

Een bekend probleem met een eerdere [Torenvalk-versie](https://github.com/aspnet/KestrelHttpServer/issues/1182) kan ertoe leiden dat een ASP.NET Core 1.0-app die wordt gehost in App Service, met tussenpozen niet meer reageert. Mogelijk ziet u ook dit bericht: 'Er is een fout opgetreden in de opgegeven CGI-toepassing en heeft de server het proces beëindigd.'

Dit probleem is opgelost in Torenvalk versie 1.0.2. Deze versie is opgenomen in de ASP.NET Core 1.0.3 update. Als u dit probleem wilt oplossen, moet u ervoor zorgen dat u uw app-afhankelijkheden bijwerkt om Torenvalk 1.0.2 te gebruiken. U ook een van de twee tijdelijke oplossingen gebruiken die worden beschreven in de blogpost [ASP.NET Core 1.0 trage perf-problemen in app service-webapps.](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites)


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Ik kan mijn logbestanden niet vinden in de bestandsstructuur van mijn web-app. Hoe kan ik ze vinden?

Als u de functie Lokale cache van App Service gebruikt, wordt de mapstructuur van de map LogFiles en Gegevens voor uw app-service-exemplaar beïnvloed. Wanneer lokale cache wordt gebruikt, worden submappen gemaakt in de mappen Logboekbestanden en Gegevens voor opslag. De submappen gebruiken het naamgevingspatroon "unieke id" + tijdstempel. Elke submap komt overeen met een VM-exemplaar waarin de web-app wordt uitgevoerd of is uitgevoerd.

Als u wilt bepalen of u lokale cache gebruikt, controleert u het tabblad Instellingen van de **app-servicetoepassing.** Als lokale cache wordt gebruikt, `WEBSITE_LOCAL_CACHE_OPTION` wordt `Always`de app-instelling ingesteld op .

Als u lokale cache niet gebruikt en dit probleem ondervindt, dient u een ondersteuningsverzoek in.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Ik zie het bericht "Er is een poging gedaan om toegang te krijgen tot een socket op een manier die verboden is door de toegangsmachtigingen." Hoe los ik dit op?

Deze fout treedt meestal op als de uitgaande TCP-verbindingen op de VM-instantie zijn uitgeput. In App-service worden limieten afgedwongen voor het maximum aantal uitgaande verbindingen dat voor elke VM-instantie kan worden gemaakt. Zie [Numerieke limieten voor cross-VM](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)voor meer informatie .

Deze fout kan ook optreden als u probeert toegang te krijgen tot een lokaal adres van uw toepassing. Zie [Lokale adresaanvragen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests)voor meer informatie.

Zie het blogbericht over uitgaande verbindingen met [Azure-websites](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)voor meer informatie over uitgaande verbindingen in uw web-app.

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hoe gebruik ik Visual Studio om mijn App Service-webapp op afstand te debuggen?

Zie Web-app Afstandsbediening voor een gedetailleerde walkthrough die u laat zien hoe u uw web-app [debuggen](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/)met Visual Studio.
