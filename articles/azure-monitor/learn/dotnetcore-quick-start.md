---
title: Quickstart ASP.NET Core - Azure Monitor Application Insights
description: In dit artikel vindt u instructies om een ASP.NET Core-web-app snel in te stellen voor controle door Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: e066ef1910373734d9a6607500e45e8a9ccc80b2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539751"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Beginnen met controleren van de ASP.NET Core-webtoepassing

Met Azure Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een webtoepassing controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. 

Deze snelstartgids helpt u de Application Insights SDK toe te voegen aan een bestaande ASP.NET Core-webtoepassing. Lees dit [artikel](../app/asp-net-core.md) als u meer wilt weten over het configureren van Application Insights zonder Visual Studio.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

- [Installeer Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) met de volgende werkbelastingen:
  - ASP.NET-ontwikkeling en webontwikkeling
  - Azure-ontwikkeling
- [.NET Core 2.0 SDK installeren](https://dotnet.microsoft.com/download)
- U hebt een Azure-abonnement en een bestaande .NET Core-webtoepassing nodig.

Als u geen ASP.NET Core-webtoepassing hebt, kunt u onze stapsgewijze handleiding gebruiken om [een ASP.NET Core-app te maken en Application Insights toe te voegen](../../azure-monitor/app/asp-net-core.md).

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Selecteer **Een resource maken** > **Hulpprogramma's voor ontwikkelaars** > **Application Insights**.

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights-resource maakt, kunt u meer informatie vinden door naar het document [Een Application Insights-resource maken](../app/create-new-resource.md) te gaan.

    Er wordt een configuratiescherm weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen.

   | Instellingen        |  Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app beschrijft die u wilt controleren |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe resourcegroep waarin App Insights-gegevens worden gehost Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

2. Klik op **Create**.



## <a name="configure-app-insights-sdk"></a>App Insights-SDK configureren

1. Open het **project** uit ASP.NET Core-web-app in Visual Studio > Klik met de rechtermuisknop op de naam van de app in **Solution Explorer** > Selecteer **Toevoegen** > **Application Insights Telemetry**.

    ![Application Insights Telemetry toevoegen](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klik op de knop **Aan de slag**.

3. Selecteer uw account en abonnement > selecteer de **Bestaande resource** die u hebt gemaakt in de Azure-portal > klik op **Registreren**.

4. Selecteer **Project** > **NuGet-pakketten beheren** > **Pakketbron: nuget.org** > **Update** de Application Insights SDK-pakketten naar de nieuwste stabiele release.

5. Selecteer **Foutopsporing** > **Starten zonder foutopsporing** (Ctrl + F5) om de app te starten

    ![Menu Overzicht van Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Het duurt 3-5 minuten voordat de gegevens worden weergegeven in de portal. Let op: als deze app een test-app met weinig verkeer is, worden de meeste metrische gegevens alleen vastgelegd bij actieve aanvragen en bewerkingen.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. Open de pagina **Overzicht** van Application Insights in Azure Portal opnieuw door **Start** te selecteren en selecteer bij de recente resources de resource te die u eerder hebt gemaakt, om de details te bekijken van de toepassing die momenteel wordt uitgevoerd.

   ![Menu Overzicht van Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Klik op **Toepassingsoverzicht** voor een visueel overzicht van de afhankelijkheidsrelaties tussen de onderdelen van de toepassing. Voor elk onderdeel worden KPI's weergegeven, zoals belasting, prestaties, fouten en waarschuwingen.

   ![Toepassingskaart](./media/dotnetcore-quick-start/5appmap.png)

3. Klik op het **App Analytics**-pictogram ![Pictogram Toepassingskaart](./media/dotnetcore-quick-start/006.png) **Weergeven in Analytics**. Hierdoor wordt **Application Insights Analytics** geopend. Dit biedt een querytaal met opmaak voor het analyseren van alle gegevens die zijn verzameld met Application Insights. In dit geval wordt er een query gegenereerd waarmee het aantal aanvragen wordt weergegeven als een grafiek. U kunt uw eigen query's schrijven om andere gegevens te analyseren.

   ![Analytics-grafiek met gebruikersaanvragen gedurende een tijdsperiode](./media/dotnetcore-quick-start/6analytics.png)

4. Ga terug naar de pagina **Overzicht** en bekijk de KPI-dashboards.  Dit dashboard biedt statistische gegevens over de toepassingsstatus, waaronder het aantal inkomende aanvragen, de duur van deze aanvragen en eventuele fouten die optreden. 

   ![Tijdlijngrafieken voor het Statusoverzicht](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Klik links op **Metrische gegevens**. Gebruik Metrics Explorer om de status en het gebruik van uw resource te onderzoeken. U kunt op **Nieuwe grafiek toevoegen** klikken om aanvullende aangepaste weergaven te maken, of **Bewerken** selecteren om type, hoogte, kleurenpalet, groeperingen en metrische gegevens van bestaande grafieken te wijzigen. U kunt bijvoorbeeld een grafiek maken die de gemiddelde laadtijd van browserpagina's weergeeft door 'Browser page load time' (gemiddelde laadtijd browserpagina) te kiezen uit de vervolgkeuzelijst met metrische gegevens en 'Avg' (gemiddelde) bij aggregatie. Ga naar [Aan de slag met Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md) voor meer informatie over Azure Metrics Explorer.

     ![Tabblad Metrische gegevens: Grafiek met gemiddelde laadtijd van browserpagina's](./media/dotnetcore-quick-start/8metrics.png)

## <a name="clean-up-resources"></a>Resources opschonen
U kunt de resourcegroep en alle gerelateerde resources verwijderen wanneer u klaar bent met testen. Volg de onderstaande stappen om dit te doen.

> [!NOTE]
> Als u een bestaande resourcegroep hebt gebruikt, werken de onderstaande instructies niet en hoeft u alleen de afzonderlijke Application Insights-resource te verwijderen. Houd er rekening mee dat wanneer u een resourcegroep verwijdert, alle onderliggende resources die lid zijn van die groep worden verwijderd.

1. Klik in het menu links in Azure Portal op **Resourcegroepen** en klik vervolgens op **myResourceGroup**.
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ **myResourceGroup** in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Runtime-uitzonderingen zoeken en diagnoses uitvoeren](./tutorial-runtime-exceptions.md)
