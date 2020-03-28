---
title: Dashboards van Azure Log Analytics-gegevens maken en delen | Microsoft Docs
description: Met deze zelfstudie u begrijpen hoe Logboekanalyse-dashboards al uw opgeslagen logboekquery's kunnen visualiseren, zodat u één lens hebt om uw omgeving te bekijken.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 76ba79561df4a75004369d24c4c6af82de9b1cfc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661529"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Dashboards van Log Analytics-gegevens maken en delen

Log Analytics-dashboards kunnen al uw opgeslagen logboekquery's visualiseren, zodat u operationele IT-gegevens in de organisatie vinden, correleren en delen.  Deze zelfstudie heeft betrekking op het maken van een logboekquery die wordt gebruikt om een gedeeld dashboard te ondersteunen dat wordt geopend door uw ondersteuningsteam voor IT-bewerkingen.  Procedures voor:

> [!div class="checklist"]
> * Een gedeeld dashboard maken in de Azure-portal
> * Een prestatielogboekquery visualiseren 
> * Een logboekquery toevoegen aan een gedeeld dashboard 
> * Een tegel in een gedeeld dashboard aanpassen

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine [hebben verbonden met de Log Analytics-werkruimte](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal
Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op . 

## <a name="create-a-shared-dashboard"></a>Een gedeeld dashboard maken
Selecteer **Dashboard** om uw [standaarddashboard](../../azure-portal/azure-portal-dashboards.md)te openen . Uw dashboard ziet er anders uit dan het onderstaande voorbeeld.

![Azure Portal-dashboard](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Hier verzamelt u de belangrijkste operationele IT-gegevens van al uw Azure-resources, waaronder telemetrie van Azure Log Analytics.  Voordat we een logboekquery visualiseren, maken we eerst een dashboard en delen we deze.  We kunnen ons dan richten op onze voorbeeldprestatielogboekquery, die wordt weergegeven als een lijndiagram, en deze toevoegen aan het dashboard.  

Om een dashboard te maken, selecteert u de knop **Nieuw dashboard** naast de naam van het huidige dashboard.

![Nieuw dashboard maken in Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Met deze actie wordt een nieuw, leeg, persoonlijk dashboard gemaakt en in de aanpassingsmodus gezet. In deze modus kunt u het dashboard een naam geven en tegels toevoegen of opnieuw rangschikken. Bewerk de naam van het dashboard en geef *voorbeelddashboard* op voor deze zelfstudie en selecteer **Gereed aanpassen**.<br><br> ![Aangepast Azure-dashboard opslaan](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Wanneer u een dashboard maakt, is het standaard persoonlijk. Dat betekent dat u de enige bent die het kan zien. Als u het zichtbaar wilt maken voor anderen, gebruik dan de knop **Delen** die naast de andere dashboardopdrachten wordt weergegeven.

![Een nieuw dashboard in Azure Portal delen](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

U wordt gevraagd een abonnement en resourcegroep te kiezen waarnaar uw dashboard zal worden gepubliceerd. Voor het gemak helpt de publicatie-ervaring van de portal u naar een patroon waar u dashboards kunt plaatsen in een resourcegroep die **dashboards** wordt genoemd.  Controleer het geselecteerde abonnement en klik op **Publiceren**.  Toegang tot de informatie die wordt weergegeven in het dashboard wordt geregeld met op [Azure-resources gebaseerd toegangsbeheer](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Een logboekquery visualiseren
[Log Analytics](../log-query/get-started-portal.md) is een speciale portal die wordt gebruikt om te werken met logquery's en hun resultaten. Voorbeelden van functies zijn de mogelijkheid om een query van meerdere regels te bewerken, code selectief uit te voeren, contextafhankelijke Intellisense en slimme analyse. In deze zelfstudie gebruikt u Log Analytics om een prestatieweergave in grafische vorm te maken, op te slaan voor een toekomstige query en deze vast te maken aan het eerder gemaakte gedeelde dashboard.

Open Logboekanalyse door **Logboeken** te selecteren in het menu Azure Monitor. Het begint met een nieuwe lege query.

![Startpagina](media/tutorial-logs-dashboards/homepage.png)

Voer de volgende query in om processorgebruiksrecords voor zowel Windows- als Linux-computers, gegroepeerd op computer en timegenerated, terug te sturen en in een visueel diagram weer te geven. Klik **op Uitvoeren** om de query uit te voeren en het resulterende diagram weer te geven.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Sla de query op door de knop **Opslaan** boven aan de pagina te selecteren.

![Query opslaan](media/tutorial-logs-dashboards/save-query.png)

Geef in het configuratiescherm **Query opslaan** een naam op zoals *Azure VM's - Processorgebruik* en een categorie zoals *Dashboards* en klik op **Opslaan**.  Op deze manier u een bibliotheek maken met veelvoorkomende query's die u gebruiken en wijzigen.  Ten slotte u dit vastmaken aan het eerder gemaakte gedeelde dashboard door de knop **Vastmaken aan dashboard** te selecteren in de rechterbovenhoek van de pagina en vervolgens de naam van het dashboard te selecteren.

Nu u een query hebt die is vastgemaakt aan het dashboard, ziet u dat deze een algemene naam heeft met een opmerking eronder.

![Voorbeeld van Azure-dashboard](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 U gaat de naam wijzigen in iets met meer betekenis voor gebruikers die de grafiek willen bekijken.  Klik op de knop Bewerken om de titel en ondertitel voor de tegel aan te passen en klik vervolgens op **Bijwerken**.  Er wordt een banner weergegeven waarin u wordt gevraagd of u de wijzigingen wilt publiceren of negeren.  Klik **op Een kopie opslaan**.  

![Voltooide configuratie van voorbeelddashboard](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een dashboard in de Azure-portal maakt en er een logboekquery aan toevoegt.  Ga door naar de volgende zelfstudie om de verschillende antwoorden te leren die u implementeren op basis van de resultaten van logboekquery's.  

> [!div class="nextstepaction"]
> [Reageren op gebeurtenissen met Log Analytics-waarschuwingen](tutorial-response.md)
