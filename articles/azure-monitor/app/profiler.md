---
title: Live Azure App Service-apps profileren met toepassingsinzichten | Microsoft Documenten
description: Profiel van live-apps op Azure App Service met Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275774"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Live Azure App Service-apps profileren met Application Insights

U Profiler uitvoeren op ASP.NET en ASP.NET Core-apps die worden uitgevoerd op Azure App Service met basic servicelaag of hoger. Profiler inschakelen op Linux is momenteel alleen mogelijk via [deze methode.](profiler-aspnetcore-linux.md)

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a>Profiler inschakelen voor uw app
Volg de onderstaande instructies om Profiler voor een app in te schakelen. Als u een ander type Azure-service uitvoert, volgen hier instructies voor het inschakelen van Profiler op andere ondersteunde platforms:
* [Cloudservices](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Toepassingen voor servicefabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuele machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler is vooraf geïnstalleerd als onderdeel van de runtime van App Services. In de onderstaande stappen ziet u hoe u deze inschakelen voor uw app-service. Volg deze stappen, zelfs als u de App Insights SDK in uw toepassing hebt opgenomen tijdens het bouwen.

1. Schakel de instelling 'Altijd aan' in voor uw app-service. U de instelling bijwerken op de configuratiepagina van uw appservice onder Algemene instellingen.
1. Ga naar het deelvenster **App-services** in de Azure-portal.
1. Navigeer naar het deelvenster **Instellingen > Toepassingsstatistieken.**

   ![App-inzichten inschakelen op de Portal van App Services](./media/profiler/AppInsights-AppServices.png)

1. Volg de instructies in het deelvenster om een nieuwe bron te maken of selecteer een bestaande App Insights-bron om uw app te controleren. Zorg er ook voor dat de Profiler **aan**staat. Als uw Toepassingsstatistiekenbron zich in een ander abonnement bevindt dan uw App-service, u deze pagina niet gebruiken om Application Insights te configureren. U het echter nog steeds handmatig doen door handmatig de benodigde app-instellingen te maken. [De volgende sectie bevat instructies voor het handmatig inschakelen van Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights-site-extensie toevoegen][Enablement UI]

1. Profiler is nu ingeschakeld met behulp van een App Services-appinstelling.

    ![App-instelling voor Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Profiler handmatig of met Azure Resource Manager inschakelen
Application Insights Profiler kan worden ingeschakeld door app-instellingen te maken voor uw Azure App Service. De pagina met de bovenstaande opties maakt deze app-instellingen voor u. Maar u het maken van deze instellingen automatiseren met behulp van een sjabloon of andere middelen. Deze instellingen werken ook als uw Application Insights-bron zich in een ander abonnement bevindt dan uw Azure App Service.
Hier zijn de instellingen die nodig zijn om de profiler in te schakelen:

|App-instelling    | Waarde    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey voor uw Application Insights-bron    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


U deze waarden instellen met [Azure Resource Manager Templates](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), Azure [Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Profiler handmatig inschakelen voor andere clouds

Als u de profiler voor andere clouds wilt inschakelen, u de onderstaande app-instellingen gebruiken.

|App-instelling    | Waarden van de Amerikaanse regering| China Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Profiler uitschakelen

Ga naar de app-bron als u **Web Jobs**Profiler voor de instantie van een afzonderlijke app wilt stoppen of opnieuw starten. Als u Profiler wilt verwijderen, gaat u naar **Extensies**.

![Profiler uitschakelen voor een webtaak][disable-profiler-webjob]

We raden je aan profiler op al je apps in staat te stellen om eventuele prestatieproblemen zo vroeg mogelijk te ontdekken.

De bestanden van Profiler kunnen worden verwijderd wanneer u WebDeploy gebruikt om wijzigingen in uw webtoepassing te implementeren. U voorkomen dat de verwijdering wordt verwijderd door uit te sluiten dat de App_Data map tijdens de implementatie wordt verwijderd. 


## <a name="next-steps"></a>Volgende stappen

* [Met Application Insights werken in Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
