---
title: Quickstart ASP.NET Core - Azure Monitor Application Insights
description: Geeft instructies om snel een ASP.NET Core Web App in te stellen voor bewaking met Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ef46b86186d1f5e26360de891b3a090ab0ece66b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78894818"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Beginnen met controleren van de ASP.NET Core-webtoepassing

Met Azure Application Insights kunt u eenvoudig de beschikbaarheid, de prestaties en het gebruik van een webtoepassing controleren. U kunt ook snel fouten in de toepassing identificeren en er een diagnose voor uitvoeren, zonder dat u hoeft te wachten totdat een gebruiker ze heeft gerapporteerd. 

Deze quickstart begeleidt u bij het toevoegen van de Application Insights SDK aan een bestaande ASP.NET Core-webtoepassing. Voor meer informatie over het configureren van Application Insights zonder Visual Studio checkout dit [artikel](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

- [Installeer Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) met de volgende workloads:
  - ASP.NET-ontwikkeling en webontwikkeling
  - Azure-ontwikkeling
- [.NET Core 2.0 SDK installeren](https://dotnet.microsoft.com/download)
- U hebt een Azure-abonnement en een bestaande .NET Core-webtoepassing nodig.

Als u geen ASP.NET Core-webtoepassing hebt, u onze stapsgewijze handleiding gebruiken om [een ASP.NET Core-app](../../azure-monitor/app/asp-net-core.md) te maken en Application Insights toe te voegen.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Application Insights inschakelen

Met Application Insights kunnen telemetriegegevens worden verzameld vanuit elke toepassing met een internetverbinding, ongeacht of deze on-premises wordt uitgevoerd of in de cloud. Gebruik de volgende stappen om deze gegevens te bekijken.

1. Selecteer De**toepassingsinzichten van hulpprogramma's** >  **voor resourceontwikkelaars** > **maken**.

   > [!NOTE]
   >Als dit de eerste keer is dat u een Application Insights-bron maakt, u meer informatie krijgen door het document Resource [van Toepassingsinzichten maken te](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) bezoeken.

    Er wordt een configuratiescherm weergegeven. Gebruik de volgende tabel om de invoervelden in te vullen.

   | Instellingen        |  Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Globaal unieke waarde | Naam die de app beschrijft die u wilt controleren |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe brongroep om App Insights-gegevens te hosten. Maak een nieuwe resourcegroep of gebruik een bestaande. |
   | **Locatie** | VS - oost | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost |

2. Klik **op Maken**.



## <a name="configure-app-insights-sdk"></a>App Insights-SDK configureren

1. Open het **project** uit ASP.NET Core-web-app in Visual Studio > Klik met de rechtermuisknop op de naam van de app in **Solution Explorer** > Selecteer **Toevoegen** > **Application Insights Telemetry**.

    ![Application Insights Telemetry toevoegen](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klik **op** de knop Aan de slag

3. Selecteer uw account en abonnement > Selecteer de **bestaande bron** die u hebt gemaakt in de Azure-portal > Klik **op Register**.

4. Selecteer **de** > bron**van Project Manage NuGet Packages:** > **nuget.org** > **de** SDK-pakketten van Application Insights bijwerken naar de nieuwste stabiele release.

5. Selecteer **Foutopsporing** > **starten zonder foutopsporing** (Ctrl+F5) om uw app te starten

    ![Menu Overzicht van Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Het duurt 3-5 minuten voordat de gegevens worden weergegeven in de portal. Let op: als deze app een test-app met weinig verkeer is, worden de meeste metrische gegevens alleen vastgelegd bij actieve aanvragen en bewerkingen.

## <a name="start-monitoring-in-the-azure-portal"></a>Beginnen met controleren in Azure Portal

1. Open de pagina **Overzicht** van toepassingsinzichten opnieuw in de Azure-portal door **Start** te selecteren en selecteer onder recente resources de bron die u eerder hebt gemaakt om details over uw momenteel draaiende toepassing weer te geven.

   ![Menu Overzicht van Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Klik op **Toepassingsoverzicht** voor een visueel overzicht van de afhankelijkheidsrelaties tussen de onderdelen van de toepassing. Voor elk onderdeel worden KPI's weergegeven, zoals belasting, prestaties, fouten en waarschuwingen.

   ![Toepassingskaart](./media/dotnetcore-quick-start/5appmap.png)

3. Klik op **App Analytics** het ![pictogram](./media/dotnetcore-quick-start/006.png) Toepassingskaart van App Analytics in **Analytics**. Hierdoor wordt **Application Insights Analytics** geopend. Dit biedt een querytaal met opmaak voor het analyseren van alle gegevens die zijn verzameld met Application Insights. In dit geval wordt er een query gegenereerd waarmee het aantal aanvragen wordt weergegeven als een grafiek. U kunt uw eigen query's schrijven om andere gegevens te analyseren.

   ![Analytics-grafiek met gebruikersaanvragen gedurende een tijdsperiode](./media/dotnetcore-quick-start/6analytics.png)

4. Ga terug naar de pagina **Overzicht** en bekijk de KPI-dashboards.  Dit dashboard biedt statistische gegevens over de toepassingsstatus, waaronder het aantal inkomende aanvragen, de duur van deze aanvragen en eventuele fouten die optreden. 

   ![Tijdlijngrafieken voor het Statusoverzicht](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Klik links op **Statistieken**. Gebruik de verkenner met statistieken om de status en het gebruik van uw resource te onderzoeken. U kunt op **Nieuwe grafiek toevoegen** klikken om aanvullende aangepaste weergaven te maken, of **Bewerken** selecteren om type, hoogte, kleurenpalet, groeperingen en metrische gegevens van bestaande grafieken te wijzigen. U bijvoorbeeld een grafiek maken met de gemiddelde laadtijd van de browserpagina door 'Laadtijd van browserpagina's' te kiezen in de vervolgkeuzelijst met statistieken en 'Avg' van aggregatie. Ga aan de slag [met Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)voor meer informatie over Azure Metrics Explorer.

     ![Tabblad Statistieken: Gemiddelde laadtijdvan de laadtijd van browserpagina's](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Video

- Externe stapsgewijze video over [het configureren van Application Insights met .NET Core en Visual Studio.](https://www.youtube.com/watch?v=NoS9UhcR4gA&t)
- Externe stapsgewijze video over [het configureren van Application Insights met .NET Core en Visual Studio Code](https://youtu.be/ygGt84GDync) vanaf nul.

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u klaar bent met testen, u de brongroep en alle gerelateerde bronnen verwijderen. Volg hiervoor de onderstaande stappen.

> [!NOTE]
> Als u een bestaande resourcegroep hebt gebruikt, werken de onderstaande instructies niet en moet u alleen de afzonderlijke application insights-bron verwijderen. Houd er rekening mee dat wanneer u een brongroep verwijdert, alle onderunderylingbronnen die lid zijn van die groep, worden verwijderd.

1. Klik in het linkermenu in de Azure-portal op **Resourcegroepen** en klik vervolgens op **myResourceGroup**.
2. Klik op de pagina Resourcegroep op **Verwijderen,** typ **myResourceGroup** in het tekstvak en klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Runtime-uitzonderingen zoeken en diagnoses uitvoeren](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
