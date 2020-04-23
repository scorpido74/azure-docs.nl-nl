---
title: Diagnose en oplossingstool
description: Meer informatie over hoe u problemen met uw app in Azure App Service oplossen met het hulpprogramma voor diagnose en oplossen in de Azure-portal.
keywords: app-service, azure-app-service, diagnostiek, ondersteuning, web-app, probleemoplossing, zelfhulp
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: df8f68e47776f46ae2d1331f85bd76d6cfe17b80
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869945"
---
# <a name="azure-app-service-diagnostics-overview"></a>Overzicht van Azure App Service-diagnose

Wanneer u een webtoepassing uitvoert, wilt u voorbereid zijn op eventuele problemen die zich kunnen voordoen, van 500 fouten tot uw gebruikers die u vertellen dat uw site is uitgeschakeld. App Service-diagnostiek is een intelligente en interactieve ervaring om u te helpen problemen op te lossen met uw app zonder dat u een configuratie hoeft te configureren. Wanneer u problemen met uw app tegenkomt, geeft App Service-diagnose aan wat er mis is om u naar de juiste informatie te leiden om het probleem gemakkelijker en sneller op te lossen en op te lossen.

Hoewel deze ervaring het meest nuttig is wanneer u problemen ondervindt met uw app in de afgelopen 24 uur, zijn alle diagnostische grafieken altijd beschikbaar om te analyseren.

App Service-diagnose werkt niet alleen voor uw app op Windows, maar ook voor apps op [Linux/containers,](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) [App Service-omgeving](https://docs.microsoft.com/azure/app-service/environment/intro)en [Azure-functies](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Diagnostische gegevens van App-service openen

Als u toegang wilt krijgen tot diagnostische gegevens van App Service, navigeert u naar uw web-app of App Service-omgeving in de [Azure-portal.](https://portal.azure.com) Klik in de linkernavigatie op **Diagnosticeren en los problemen op.**

Ga voor Azure-functies naar uw functie-app en klik in de bovenste navigatie op **Platformfuncties**en selecteer **Problemen diagnosticeren en oplossen** in de sectie **Resourcebeheer.**

In de startpagina voor diagnostische gegevens van App Service u de categorie kiezen die het probleem met uw app het beste beschrijft met behulp van de zoekwoorden in elke startpaginategel. Op deze pagina vindt u **ook Diagnostische hulpprogramma's voor Windows-apps.** Zie [Diagnostische hulpprogramma's (alleen voor Windows-apps).](#diagnostic-tools-only-for-windows-app)

![Startpagina](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Als uw app is uitgeschakeld of traag presteert, u [een profileringstraceer om](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) de hoofdoorzaak van het probleem te identificeren. Profilering is licht van gewicht en is ontworpen voor productiescenario's.
>

## <a name="interactive-interface"></a>Interactieve interface

Zodra u een homepagecategorie hebt geselecteerd die het beste aansluit bij het probleem van uw app, kan de interactieve interface van App Service diagnostics, Genie, u begeleiden bij het diagnosticeren en oplossen van problemen met uw app. U de tegelsneltoetsen van Genie gebruiken om het volledige diagnostische rapport van de probleemcategorie te bekijken die u hebt. De tegelsneltoetsen bieden u een directe manier om toegang te krijgen tot uw diagnostische statistieken.

![Tegelsneltoetsen](./media/app-service-diagnostics/tile-shortcuts-2.png)

Nadat u op deze tegels hebt geklikt, ziet u een lijst met onderwerpen die verband houden met het probleem dat in de tegel wordt beschreven. Deze onderwerpen bieden fragmenten van opmerkelijke informatie uit het volledige rapport. U op een van deze onderwerpen klikken om de problemen verder te onderzoeken. U ook op **Volledig rapport weergeven** klikken om alle onderwerpen op één pagina te verkennen.

![Onderwerpen](./media/app-service-diagnostics/application-logs-insights-3.png)

![Volledig rapport weergeven](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnostisch rapport

Nadat u ervoor kiest om het probleem verder te onderzoeken door te klikken op een onderwerp, u meer details over het onderwerp vaak aangevuld met grafieken en markdowns bekijken. Diagnostisch rapport kan een krachtig hulpmiddel zijn om het probleem met uw app te lokaliseren.

![Diagnostisch rapport](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Statuscontrole

Als u niet weet wat er mis is met uw app of niet weet waar u moet beginnen met het oplossen van problemen, is de statuscontrole een goede plek om te beginnen. De statuscontrole analyseert uw toepassingen om u een snel, interactief overzicht te geven dat aangeeft wat gezond is en wat er mis is, en u te vertellen waar u moet zoeken om het probleem te onderzoeken. De intelligente en interactieve interface biedt u begeleiding bij het probleemoplossingsproces. Health checkup is geïntegreerd met de Genie-ervaring voor Windows-apps en het diagnostisch rapport voor Linux-apps voor de web-app down.

### <a name="health-checkup-graphs"></a>Grafieken voor statuscontrole

Er zijn vier verschillende grafieken in de health checkup.

- **verzoeken en fouten:** Een grafiek met het aantal aanvragen dat in de afgelopen 24 uur is gedaan, samen met HTTP-serverfouten.
- **app-prestaties:** Een grafiek die de reactietijd van de afgelopen 24 uur voor verschillende percentielgroepen weergeeft.
- **CPU-gebruik:** Een grafiek die het totale cpu-gebruik per exemplaar in de afgelopen 24 uur weergeeft.  
- **geheugengebruik:** Een grafiek die het totale fysieke geheugengebruik per exemplaar in de afgelopen 24 uur weergeeft.

![Statuscontrole](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Problemen met toepassingscode onderzoeken (alleen voor Windows-apps)

Omdat veel app-problemen verband houden met problemen in uw toepassingscode, integreert App Service-diagnose met [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) om uitzonderingen en afhankelijkheidsproblemen te markeren om te correleren met de geselecteerde downtime. Application Insights moet apart ingeschakeld worden.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Als u uitzonderingen en afhankelijkheden van Application Insights wilt weergeven, selecteert u de snelkoppelingen van de **web-app omlaag** of **voor langzame** tegels.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Stappen voor probleemoplossing (alleen voor Windows-app)

Als er in de afgelopen 24 uur een probleem wordt gedetecteerd met een specifieke probleemcategorie, u het volledige diagnostische rapport bekijken en kan app-servicediagnostiek u vragen om meer advies over probleemoplossing en volgende stappen te bekijken voor een meer begeleide ervaring.

![Inzichten in toepassingen en probleemoplossing en volgende stappen](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnostische hulpprogramma's (alleen voor Windows-app)

Diagnostische hulpprogramma's bevatten meer geavanceerde diagnostische hulpprogramma's die u helpen bij het onderzoeken van problemen met toepassingscode, traagheid, verbindingstekenreeksen en meer. en proactieve tools waarmee u problemen met CPU-gebruik, aanvragen en geheugen beperken.

### <a name="proactive-cpu-monitoring"></a>Proactieve CPU-monitoring

Proactieve CPU-monitoring biedt u een eenvoudige, proactieve manier om actie te ondernemen wanneer uw app of onderliggend proces voor uw app veel CPU-bronnen verbruikt. U uw eigen CPU-drempelregels instellen om een hoog CPU-probleem tijdelijk te beperken totdat de werkelijke oorzaak voor het onverwachte probleem is gevonden. Zie [Uw CPU-problemen beperken voordat ze zich voordoen](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)voor meer informatie.

![Proactieve CPU-monitoring](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Auto-healing en proactieve auto-healing

Auto-healing is een beperkende actie die u nemen wanneer uw app onverwacht gedrag vertoont. U uw eigen regels instellen op basis van het aantal aanvragen, de trage aanvraag, de geheugenlimiet en de HTTP-statuscode om beperkende acties te activeren. Gebruik de tool om een onverwacht gedrag tijdelijk te beperken totdat u de hoofdoorzaak hebt gevonden. Zie [De aankondiging van de nieuwe autohealing-ervaring in de diagnose van app-service](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)voor meer informatie.

![Auto-healing](./media/app-service-diagnostics/auto-healing-10.png)

Net als proactieve CPU-monitoring is proactieve automatische genezing een turn-key oplossing om onverwacht gedrag van uw app te verzachten. Proactieve automatische genezing start uw app opnieuw wanneer app-service vaststelt dat uw app zich in een onherstelbare status bevindt. Zie [Introductie van Proactieve Auto Heal](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)voor meer informatie.

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigator- en wijzigingsanalyse (alleen voor Windows-app)

In een groot team met continue integratie en waar uw app veel afhankelijkheden heeft, kan het moeilijk zijn om de specifieke verandering te lokaliseren die een ongezond gedrag veroorzaakt. Navigator helpt inzicht te krijgen in de topologie van uw app door automatisch een afhankelijkheidskaart van uw app en alle bronnen in hetzelfde abonnement weer te geven. Met Navigator u een geconsolideerde lijst met wijzigingen bekijken die door uw app en de afhankelijkheden zijn aangebracht en een wijziging beperken die ongezond gedrag veroorzaakt. Het kan worden geopend via de homepage tegel **Navigator** en moet worden ingeschakeld voordat u het de eerste keer. Zie [Inzicht krijgen in de afhankelijkheden van uw app met Navigator](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)voor meer informatie.

![Standaardpagina voor navigator](./media/app-service-diagnostics/navigator-default-page-11.png)

![Diff-weergave](./media/app-service-diagnostics/diff-view-12.png)

Wijzigingsanalyse voor app-wijzigingen kan worden geopend via tegelsnelkoppelingen, **toepassingswijzigingen** en **toepassingsvastten** in **beschikbaarheid en prestaties,** zodat u deze gelijktijdig met andere statistieken gebruiken. Voordat u de functie gebruikt, moet u deze eerst inschakelen. Zie [De nieuwe ervaring met wijzigingsanalyse in App Service Diagnostics aankondigen](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)voor meer informatie.

Plaats uw vragen of feedback op [UserVoice](https://feedback.azure.com/forums/169385-web-apps) door "[Diag]" toe te voegen in de titel.
