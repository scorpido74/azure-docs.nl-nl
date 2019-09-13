---
title: Quickstart met Azure Application Insights | Microsoft Docs
description: Biedt instructies om snel een ASP.NET Core web-app in te stellen voor bewaking met Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 313e0c6cb487ed986c38610131c4bc19f2eeb846
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916190"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Beginnen met controleren van de ASP.NET Core-webtoepassing

Met Azure Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een webtoepassing controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. 

Deze Snelstartgids helpt u bij het toevoegen van de Application Insights SDK aan een bestaande ASP.NET Core-webtoepassing. Voor meer informatie over het configureren van Application Insights zonder Visual Studio dit [artikel](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)uit te checken.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

- [Installeer Visual Studio 2019](https://www.visualstudio.com/downloads/) met de volgende werk belastingen:
  - ASP.NET-ontwikkeling en webontwikkeling
  - Azure-ontwikkeling
- [.NET Core 2.0 SDK installeren](https://www.microsoft.com/net/core)
- U hebt een Azure-abonnement en een bestaande .NET Core-webtoepassing nodig.

Als u geen ASP.NET Core-webtoepassing hebt, kunt u onze stapsgewijze hand leiding gebruiken om [een ASP.net core-app te maken en Application Insights toe te voegen.](../../azure-monitor/app/asp-net-core.md)

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Selecteer **Een resource maken** > **Hulpprogramma's voor ontwikkelaars** > **Application Insights**.

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights resource maakt, kunt u meer informatie vinden op het document [Create a Application Insights resource](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

    Er wordt een configuratiescherm weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen.

   | Instellingen        |  Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Name**      | Globaal unieke waarde | Naam die de app beschrijft die u wilt controleren |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resourcegroep waarin App Insights-gegevens worden gehost |
   | **Location** | East US | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

2. Klik op **Create**.

## <a name="configure-app-insights-sdk"></a>App Insights-SDK configureren

1. Open het **project** uit ASP.NET Core-web-app in Visual Studio > Klik met de rechtermuisknop op de naam van de app in **Solution Explorer** > Selecteer **Toevoegen** > **Application Insights Telemetry**.

    ![Application Insights Telemetry toevoegen](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klik op de knop **aan de slag**

3. Selecteer uw account en abonnement > Selecteer de **bestaande resource** die u hebt gemaakt in de Azure Portal > Klik op **registreren**.

4. Selecteer **project** >  > **Manage NuGet** > **packages Bron: nuget.org**de Application Insights SDK-pakketten**bijwerken** naar de laatste stabiele release.

5. Selecteer **Foutopsporing** > **Starten zonder foutopsporing** (Ctrl + F5) om de app te starten

    ![Menu Overzicht van Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Het duurt 3-5 minuten voordat de gegevens worden weergegeven in de portal. Let op: als deze app een test-app met weinig verkeer is, worden de meeste metrische gegevens alleen vastgelegd bij actieve aanvragen en bewerkingen.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. Open de pagina **overzicht** van Application Insights in de Azure portal door **Start** en onder recente resources te selecteren. Selecteer de resource die u eerder hebt gemaakt en Bekijk de details van de toepassing die momenteel wordt uitgevoerd.

   ![Menu Overzicht van Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Klik op **Toepassingsoverzicht** voor een visueel overzicht van de afhankelijkheidsrelaties tussen de onderdelen van de toepassing. Voor elk onderdeel worden KPI's weergegeven, zoals belasting, prestaties, fouten en waarschuwingen.

   ![Toepassingskaart](./media/dotnetcore-quick-start/5appmap.png)

3. Klik op de pictogram **app-analyse** ![het pictogram toepassings overzicht van](./media/dotnetcore-quick-start/006.png) weergave van **in Analytics**. Hierdoor wordt **Application Insights Analytics** geopend. Dit biedt een querytaal met opmaak voor het analyseren van alle gegevens die zijn verzameld met Application Insights. In dit geval wordt er een query gegenereerd waarmee het aantal aanvragen wordt weergegeven als een grafiek. U kunt uw eigen query's schrijven om andere gegevens te analyseren.

   ![Analytics-grafiek met gebruikersaanvragen gedurende een tijdsperiode](./media/dotnetcore-quick-start/6analytics.png)

4. Ga terug naar de pagina **Overzicht** en bekijk de KPI-dashboards.  Dit dashboard biedt statistische gegevens over de toepassingsstatus, waaronder het aantal inkomende aanvragen, de duur van deze aanvragen en eventuele fouten die optreden. 

   ![Tijdlijngrafieken voor het Statusoverzicht](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Klik aan de linkerkant op **metrische gegevens**. Gebruik metrische gegevens Verkenner om de status en het gebruik van uw resource te onderzoeken. U kunt op **Nieuwe grafiek toevoegen** klikken om aanvullende aangepaste weergaven te maken, of **Bewerken** selecteren om type, hoogte, kleurenpalet, groeperingen en metrische gegevens van bestaande grafieken te wijzigen. U kunt bijvoorbeeld een grafiek maken waarin de gemiddelde laad tijd van een browser pagina wordt weer gegeven door ' browser pagina laadtijd ' te kiezen in de vervolg keuzelijst metrische gegevens en ' Gem ' van aggregatie. Ga voor meer informatie over Azure Metrics Explorer [aan de slag met azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

     ![Tabblad metrische gegevens: Grafiek met gemiddelde laad tijd van browser pagina's](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Video

- Externe stapsgewijze video over het [configureren van Application Insights met .net core en Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) .
- Externe stapsgewijze video over het [configureren van Application Insights met .net core-en Visual Studio code](https://youtu.be/ygGt84GDync) .

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u klaar bent met testen, kunt u de resource groep en alle gerelateerde resources verwijderen. Volg hiervoor de onderstaande stappen.

1. Klik in het menu links in Azure Portal op **Resourcegroepen** en klik vervolgens op **myResourceGroup**.
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ **myResourceGroup** in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Runtime-uitzonderingen zoeken en diagnoses uitvoeren](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
