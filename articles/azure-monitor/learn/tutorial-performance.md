---
title: Prestatieproblemen analyseren met behulp van Azure Application Insights | Microsoft Docs
description: Zelfstudie voor het vinden en diagnosticeren van prestatieproblemen in uw toepassing met behulp van Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/13/2019
ms.custom: mvc
ms.openlocfilehash: 98d7c1552a7b1f2b02ae4df1cad24e20f7ac76e1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661563"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Prestatieproblemen vinden en diagnosticeren met behulp van Azure Application Insights

Azure Application Insights verzamelt telemetrie van uw toepassing om de werking en prestaties te analyseren.  U kunt deze informatie gebruiken om problemen te identificeren die zich kunnen voordoen of om te identificeren welke verbeteringen in de toepassing het meeste effect zouden hebben voor gebruikers.  Deze zelfstudie leidt u door het proces van het analyseren van de prestaties van zowel de servercomponenten van uw applicatie als het perspectief van de client.  In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Het identificeren van de prestaties van bewerkingen aan de serverzijde
> * Het analyseren van serverbewerkingen om de hoofdoorzaak van trage prestaties vast te stellen
> * Het identificeren van de traagste bewerkingen aan de clientzijde
> * Details van paginaweergaven analyseren met een querytaal


## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de volgende werk belastingen:
    - ASP.NET-ontwikkeling en webontwikkeling
    - Azure-ontwikkeling
- Implementeer een .NET-toepassing in Azure en [schakel de Application Insights-SDK](../../azure-monitor/app/asp-net.md)in.
- [Schakel de Application Insights-profiler in](../../azure-monitor/app/profiler.md#installation) voor uw toepassing.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij de Azure-portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="identify-slow-server-operations"></a>Trage serverbewerkingen identificeren
Application Insights verzamelt prestatiedetails over de verschillende bewerkingen in uw toepassing. Door de bewerkingen met de langste duur te identificeren, kunt u potentiële problemen diagnosticeren of de beste ontwikkelingsrichting bepalen voor het verbeteren van de algehele prestaties van de toepassing.

1. Selecteer **Application Insights** en selecteer vervolgens uw abonnement.  
1. U kunt het deelvenster **Prestaties** openen door **Prestaties** te selecteren in het menu **Onderzoeken** of door op de grafiek **Serverreactietijd** te klikken.

    ![Prestaties](media/tutorial-performance/1-overview.png)

2. In het deelvenster **Prestaties** deelvenster ziet u het aantal en de gemiddelde duur van elke bewerking voor de toepassing.  U kunt deze informatie gebruiken om de bewerkingen te identificeren die de meeste impact hebben voor gebruikers. In dit voorbeeld zijn **GET Customers/Details** en **GET Home/Index** goede kandidaten om te onderzoeken, vanwege hun relatief lange duur en hoge aantal aanroepen.  Andere bewerkingen hebben wellicht een langere duur, maar werden zelden aangeroepen, zodat het effect van een verbetering ervan minimaal zou zijn.  

    ![Paneel voor de prestaties server](media/tutorial-performance/2-server-operations.png)

3. In de grafiek ziet u nu de gemiddelde duur van de geselecteerde bewerkingen gedurende een bepaalde periode. U kunt overschakelen naar het 95e percentiel om de prestatieproblemen te zoeken. Voeg de bewerkingen waarin u geïnteresseerd bent toe door ze vast te maken aan de grafiek.  Dit laat zien dat er enkele pieken zijn die het onderzoeken waard zijn.  Isoleer dit verder door het tijdvenster van de grafiek te verkleinen.

    ![Bewerkingen vastmaken](media/tutorial-performance/3-server-operations-95th.png)

4.  In het prestatiedeelvenster rechts ziet u de verdeling van de duur voor verschillende aanvragen voor de geselecteerde bewerking.  Maak het venster kleiner om bij ongeveer het 95e percentiel te beginnen. Op de inzichtenkaart 'Top 3 afhankelijkheden' ziet u meteen dat de externe afhankelijkheden waarschijnlijk bijdragen aan de trage transacties.  Klik op de knop met het aantal steekproeven voor een lijst met de steekproeven. U kunt dan elke steekproef selecteren om de transactiedetails te bekijken.

5.  U kunt in één oogopslag zien dat de aanroep van de Azure-tabel Fabrikamaccount het meeste bijdraagt aan de totale duur van de transactie. U kunt ook zien dat deze is mislukt vanwege een uitzondering. U kunt op elk item in de lijst klikken om de details hiervan rechts weer te geven. [Meer informatie over de functionaliteit voor transactiediagnoses](../../azure-monitor/app/transaction-diagnostics.md)

    ![End-to-end-details van bewerking](media/tutorial-performance/4-end-to-end.png)
    

6.  De **Profiler** helpt u verder met diagnoses op codeniveau door de werkelijke code te laten zien die voor de bewerking is uitgevoerd en de tijd die elke stap kostte. Voor bepaalde bewerkingen is er mogelijk geen tracering, omdat de profiler periodiek wordt uitgevoerd.  Na verloop van tijd moeten meer bewerkingen traceringen hebben.  Klik op **Profilertraceringen** om de profiler voor de bewerking te starten.
5.  De tracering toont de afzonderlijke gebeurtenissen voor elke bewerking, zodat u de hoofdoorzaak voor de duur van de algehele bewerking kunt onderzoeken.  Klik op een van de bovenste voorbeelden, die de langste duur hebben.
6.  Klik op **warme pad** om het specifieke pad naar de gebeurtenissen te markeren die het meest bij te dragen aan de totale duur van de bewerking.  In dit voorbeeld ziet u dat de traagste aanroep afkomstig is van de methode *FabrikamFiberAzureStorage.GetStorageTableData*. Het onderdeel dat de meeste tijd in beslag neemt is de methode *CloudTable.CreateIfNotExist*. Als deze regel code wordt uitgevoerd telkens wanneer de functie wordt aangeroepen, worden er onnodige netwerkaanroepen en CPU-bronnen verbruikt. De beste manier om uw code te verbeteren is door deze regel in een opstartmethode te plaatsen die slechts één keer wordt uitgevoerd.

    ![Profiler-details](media/tutorial-performance/5-hot-path.png)

7.  De **Prestatietip** bovenaan het scherm ondersteunt de gedachte dat de lange duur te wijten is aan wachten.  Klik op de koppeling **wachten** voor documentatie over het interpreteren van de verschillende typen gebeurtenissen.

    ![Prestatietip](media/tutorial-performance/6-perf-tip.png)

8.   Voor verdere analyse kunt u klikken op **tracering downloaden** om de tracering te downloaden. U kunt deze gegevens weer geven met behulp van [PerfView](https://github.com/Microsoft/perfview#perfview-overview).

## <a name="use-logs-data-for-server"></a>Logboek gegevens gebruiken voor Server
 Logboeken bieden een uitgebreide query taal waarmee u alle gegevens kunt analyseren die zijn verzameld door Application Insights. U kunt deze gebruiken om diepgaande analysen uit te voeren op aanvraag- en prestatiegegevens.

1. Ga terug naar het deel venster bewerking detail en klik op ![logboeken](media/tutorial-performance/app-viewinlogs-icon.png)**weer geven in Logboeken (analyse)**

2. Logboeken wordt geopend met een query voor elk van de weer gaven in het paneel.  U kunt deze query’s zó uitvoeren, of ze aanpassen aan uw behoeften.  De eerste query laat de duur van deze bewerking gedurende een bepaalde periode zien.

    ![Logboeken query](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Trage clientbewerkingen identificeren
Naast het identificeren van serverprocessen om te optimaliseren, kan Application Insights ook het perspectief van clientbrowsers analyseren.  Dit kan u helpen potentiële verbeteringen aan clientcomponenten te identificeren en zelfs problemen met verschillende browsers of verschillende locaties te identificeren.

1. Selecteer **browser** onder **onderzoeken** en klik vervolgens op **browser prestaties** of selecteer **prestaties** onder **onderzoeken** en schakel over naar het tabblad **browser** door te klikken op de wissel knop Server/browser in de rechter bovenhoek om de samen vatting van de browser prestaties te openen. Dit geeft een visueel overzicht van verschillende telemetrieën van uw toepassing vanuit het perspectief van de browser.

    ![Browseroverzicht](media/tutorial-performance/8-browser.png)

2. Selecteer een van de bewerkings namen en klik vervolgens op de knop met blauwe steek proeven in de rechter benedenhoek en selecteer een bewerking. Hiermee worden de end-to-end-transactie details weer gegeven en aan de rechter kant kunt u de **Eigenschappen van de pagina weergave**bekijken. Hiermee kunt u de details weer geven van de client die de pagina aanvraagt, inclusief het type browser en de locatie ervan. Deze informatie kan u helpen bepalen of er prestatieproblemen zijn die te maken hebben met specifieke typen clients.

    ![Paginaweergave](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Logboek gegevens gebruiken voor client
Net als de gegevens die worden verzameld voor de server prestaties, maakt Application Insights alle client gegevens beschikbaar voor diepe analyse met behulp van Logboeken.

1. Ga terug naar de browser-samen vatting en klik op ![pictogram logboeken](media/tutorial-performance/app-viewinlogs-icon.png) **weer gave in Logboeken (Analytics)**

2. Logboeken wordt geopend met een query voor elk van de weer gaven in het paneel. De eerste query laat de duur zien van verschillende paginaweergaven gedurende een bepaalde periode.

    ![Logboeken query](media/tutorial-performance/10-page-view-logs.png)

3.  Smart Diagnostics is een functie van Logboeken waarmee unieke patronen in de gegevens worden geïdentificeerd. Wanneer u op de Smart Diagnostics-stip in het lijndiagram klikt, wordt dezelfde query uitgevoerd zonder de records die de anomalie hebben veroorzaakt. Details van deze records worden weergegeven in de commentaarsectie van de query, zodat u de eigenschappen van die paginaweergaven kunt identificeren die de overmatige duur veroorzaken.

    ![Logboeken met Smart Diagnostics](media/tutorial-performance/11-page-view-logs-dsmart.png)


## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd runtime-uitzonderingen te identificeren, kunt u doorgaan naar de volgende zelfstudie om te leren hoe u waarschuwingen kunt maken in reactie op fouten.

> [!div class="nextstepaction"]
> [Waarschuwing bij toepassingsstatus](../../azure-monitor/learn/tutorial-alert.md)
