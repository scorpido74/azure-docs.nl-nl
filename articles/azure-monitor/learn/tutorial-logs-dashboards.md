---
title: Dashboards van Azure Log Analytics-gegevens maken en delen | Microsoft Docs
description: In deze zelf studie leert u hoe Log Analytics Dash boards al uw opgeslagen logboek query's kunt visualiseren, zodat u één lens krijgt om uw omgeving weer te geven.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 76ba79561df4a75004369d24c4c6af82de9b1cfc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77661529"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Dashboards van Log Analytics-gegevens maken en delen

Log Analytics Dash boards kunnen al uw opgeslagen logboek query's visualiseren, zodat u IT-operationele gegevens in de organisatie kunt vinden, correleren en delen.  In deze zelf studie wordt gebruikgemaakt van het maken van een logboek query die wordt gebruikt ter ondersteuning van een gedeeld dash board dat toegankelijk is voor uw IT-ondersteunings team.  Procedures voor:

> [!div class="checklist"]
> * Een gedeeld dashboard maken in de Azure-portal
> * Een query voor prestatie logboeken visualiseren 
> * Een logboek query toevoegen aan een gedeeld dash board 
> * Een tegel in een gedeeld dashboard aanpassen

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine [hebben verbonden met de Log Analytics-werkruimte](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Een gedeeld dashboard maken
Selecteer **dash board** om uw standaard [Dashboard](../../azure-portal/azure-portal-dashboards.md)te openen. Het dash board ziet er anders uit dan het onderstaande voor beeld.

![Azure Portal-dashboard](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Hier verzamelt u de belangrijkste operationele IT-gegevens van al uw Azure-resources, waaronder telemetrie van Azure Log Analytics.  Voordat we een logboek query Step Into visualiseren, gaan we eerst een dash board maken en delen.  We kunnen vervolgens zich richten op de voorbeeld query van het prestatie logboek, die als een lijn diagram wordt weer gegeven en deze aan het dash board toevoegt.  

Om een dashboard te maken, selecteert u de knop **Nieuw dashboard** naast de naam van het huidige dashboard.

![Nieuw dashboard maken in Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Met deze actie wordt een nieuw, leeg, persoonlijk dashboard gemaakt en in de aanpassingsmodus gezet. In deze modus kunt u het dashboard een naam geven en tegels toevoegen of opnieuw rangschikken. Bewerk de naam van het dash board en geef een voor *beeld-dash board* op voor deze zelf studie en selecteer vervolgens **gereed aanpassen**.<br><br> ![Aangepast Azure-dashboard opslaan](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Wanneer u een dashboard maakt, is het standaard persoonlijk. Dat betekent dat u de enige bent die het kan zien. Als u het zichtbaar wilt maken voor anderen, gebruik dan de knop **Delen** die naast de andere dashboardopdrachten wordt weergegeven.

![Een nieuw dashboard in Azure Portal delen](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

U wordt gevraagd een abonnement en resourcegroep te kiezen waarnaar uw dashboard zal worden gepubliceerd. Voor het gemak helpt de publicatie-ervaring van de portal u naar een patroon waar u dashboards kunt plaatsen in een resourcegroep die **dashboards** wordt genoemd.  Controleer het geselecteerde abonnement en klik op **Publiceren**.  Toegang tot de informatie die wordt weergegeven in het dashboard wordt geregeld met op [Azure-resources gebaseerd toegangsbeheer](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Een logboek query visualiseren
[Log Analytics](../log-query/get-started-portal.md) is een speciale portal die wordt gebruikt voor het werken met logboek query's en de bijbehorende resultaten. Voorbeelden van functies zijn de mogelijkheid om een query van meerdere regels te bewerken, code selectief uit te voeren, contextafhankelijke Intellisense en slimme analyse. In deze zelf studie gaat u Log Analytics gebruiken om een prestatie weergave te maken in grafische vorm, deze op te slaan voor een toekomstige query en deze vast te maken aan het gedeelde dash board dat u eerder hebt gemaakt.

Open Log Analytics door **Logboeken** te selecteren in het menu Azure monitor. Deze begint met een nieuwe lege query.

![Startpagina](media/tutorial-logs-dashboards/homepage.png)

Voer de volgende query in om de gegevens van het processor gebruik te retour neren voor zowel Windows-als Linux-computers, gegroepeerd op computer en TimeGenerated, en worden weer gegeven in een visueel diagram. Klik op **uitvoeren** om de query uit te voeren en de resulterende grafiek weer te geven.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Sla de query op door de knop **Opslaan** te selecteren vanaf de bovenkant van de pagina.

![Query opslaan](media/tutorial-logs-dashboards/save-query.png)

Geef in het configuratie scherm **query opslaan** een naam op zoals *Azure-Vm's-processor gebruik* en een categorie, zoals *Dash boards* , en klik vervolgens op **Opslaan**.  Op deze manier kunt u een bibliotheek met algemene query's maken die u kunt gebruiken en wijzigen.  Vervolgens kunt u deze vastmaken aan het gedeelde dash board dat u eerder hebt gemaakt door de knop **vastmaken aan dash board** te selecteren in de rechter bovenhoek van de pagina en vervolgens de naam van het dash board te selecteren.

Nu u een query hebt die is vastgemaakt aan het dashboard, ziet u dat deze een algemene naam heeft met een opmerking eronder.

![Voorbeeld van Azure-dashboard](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 U gaat de naam wijzigen in iets met meer betekenis voor gebruikers die de grafiek willen bekijken.  Klik op de knop bewerken om de titel en subtitel voor de tegel aan te passen en klik vervolgens op **bijwerken**.  Er wordt een banner weergegeven waarin u wordt gevraagd of u de wijzigingen wilt publiceren of negeren.  Klik op **een kopie opslaan**.  

![Voltooide configuratie van voorbeelddashboard](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u een dash board maakt in de Azure Portal en er een logboek query aan toevoegt.  Ga naar de volgende zelf studie voor meer informatie over de verschillende reacties die u kunt implementeren op basis van de resultaten van een logboek query.  

> [!div class="nextstepaction"]
> [Reageren op gebeurtenissen met Log Analytics-waarschuwingen](tutorial-response.md)
