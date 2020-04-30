---
title: Quick start-ASP.NET Core-Azure Monitor Application Insights
description: Biedt instructies om snel een ASP.NET Core web-app in te stellen voor bewaking met Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: 9aa19dfa0626ff0726911815addbb03945ee36f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82144853"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Beginnen met controleren van de ASP.NET Core-webtoepassing

Met Azure Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een webtoepassing controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. 

Deze Snelstartgids helpt u bij het toevoegen van de Application Insights SDK aan een bestaande ASP.NET Core-webtoepassing. Voor meer informatie over het configureren van Application Insights zonder Visual Studio dit [artikel](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)uit te checken.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

- [Installeer Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) met de volgende werk belastingen:
  - ASP.NET-ontwikkeling en webontwikkeling
  - Azure-ontwikkeling
- [.NET Core 2.0 SDK installeren](https://dotnet.microsoft.com/download)
- U hebt een Azure-abonnement en een bestaande .NET Core-webtoepassing nodig.

Als u geen ASP.NET Core-webtoepassing hebt, kunt u onze stapsgewijze hand leiding gebruiken om [een ASP.net core-app te maken en Application Insights toe te voegen.](../../azure-monitor/app/asp-net-core.md)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Selecteer **een resource** > **maken hulp programma** > voor ontwikkel aars**Application Insights**.

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights resource maakt, kunt u meer informatie vinden op het document [Create a Application Insights resource](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

    Er wordt een configuratiescherm weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen.

   | Instellingen        |  Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app beschrijft die u wilt controleren |
   | **Resource groep**     | myResourceGroup      | Naam voor de nieuwe resource groep om app Insights-gegevens te hosten. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

2. Klik op **maken**.



## <a name="configure-app-insights-sdk"></a>App Insights-SDK configureren

1. Open het **project** uit ASP.NET Core-web-app in Visual Studio > Klik met de rechtermuisknop op de naam van de app in **Solution Explorer** > Selecteer **Toevoegen** > **Application Insights Telemetry**.

    ![Application Insights Telemetry toevoegen](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klik op de knop **aan de slag**

3. Selecteer uw account en abonnement > Selecteer de **bestaande resource** die u hebt gemaakt in de Azure Portal > Klik op **registreren**.

4. Selecteer **project** > **Manage NuGet** > **packages Bron: nuget.org** > de Application Insights SDK-pakketten**bijwerken** naar de laatste stabiele release.

5. Selecteer **fout opsporing** > **starten zonder fout opsporing** (CTRL + F5) om uw app te starten

    ![Menu Overzicht van Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Het duurt 3-5 minuten voordat de gegevens worden weergegeven in de portal. Let op: als deze app een test-app met weinig verkeer is, worden de meeste metrische gegevens alleen vastgelegd bij actieve aanvragen en bewerkingen.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. Open de pagina **overzicht** van Application Insights in de Azure portal door **Start** en onder recente resources te selecteren. Selecteer de resource die u eerder hebt gemaakt en Bekijk de details van de toepassing die momenteel wordt uitgevoerd.

   ![Menu Overzicht van Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Klik op **Toepassingsoverzicht** voor een visueel overzicht van de afhankelijkheidsrelaties tussen de onderdelen van de toepassing. Voor elk onderdeel worden KPI's weergegeven, zoals belasting, prestaties, fouten en waarschuwingen.

   ![Toepassingskaart](./media/dotnetcore-quick-start/5appmap.png)

3. Klik op de](./media/dotnetcore-quick-start/006.png) pictogram weergave van ![het pictogram toepassings overzicht van **app-analyse** **in Analytics**. Hierdoor wordt **Application Insights Analytics** geopend. Dit biedt een querytaal met opmaak voor het analyseren van alle gegevens die zijn verzameld met Application Insights. In dit geval wordt er een query gegenereerd waarmee het aantal aanvragen wordt weergegeven als een grafiek. U kunt uw eigen query's schrijven om andere gegevens te analyseren.

   ![Analytics-grafiek met gebruikersaanvragen gedurende een tijdsperiode](./media/dotnetcore-quick-start/6analytics.png)

4. Ga terug naar de pagina **Overzicht** en bekijk de KPI-dashboards.  Dit dashboard biedt statistische gegevens over de toepassingsstatus, waaronder het aantal inkomende aanvragen, de duur van deze aanvragen en eventuele fouten die optreden. 

   ![Tijdlijngrafieken voor het Statusoverzicht](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Klik aan de linkerkant op **metrische gegevens**. Gebruik metrische gegevens Verkenner om de status en het gebruik van uw resource te onderzoeken. U kunt op **Nieuwe grafiek toevoegen** klikken om aanvullende aangepaste weergaven te maken, of **Bewerken** selecteren om type, hoogte, kleurenpalet, groeperingen en metrische gegevens van bestaande grafieken te wijzigen. U kunt bijvoorbeeld een grafiek maken waarin de gemiddelde laad tijd van een browser pagina wordt weer gegeven door ' browser pagina laadtijd ' te kiezen in de vervolg keuzelijst metrische gegevens en ' Gem ' van aggregatie. Ga voor meer informatie over Azure Metrics Explorer [aan de slag met azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

     ![Tabblad metrische gegevens: gemiddelde laad tijd grafiek van browser pagina](./media/dotnetcore-quick-start/8metrics.png)

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u klaar bent met testen, kunt u de resource groep en alle gerelateerde resources verwijderen. Volg hiervoor de onderstaande stappen.

> [!NOTE]
> Als u een bestaande resource groep hebt gebruikt, werken de onderstaande instructies niet en hoeft u alleen de afzonderlijke Application Insights resource te verwijderen. Houd er rekening mee dat u een resource groep verwijdert alle underyling-resources die lid zijn van die groep worden verwijderd.

1. Klik in het menu aan de linkerkant in het Azure Portal op **resource groepen** en klik vervolgens op **myResourceGroup**.
2. Klik op de pagina van uw resource groep op **verwijderen**, typ **myResourceGroup** in het tekstvak en klik vervolgens op **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Runtime-uitzonderingen zoeken en diagnoses uitvoeren](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
