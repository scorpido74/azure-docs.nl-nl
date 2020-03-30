---
title: Querybereik registreren in Azure Monitor Log Analytics | Microsoft Documenten
description: Beschrijft het bereik en het tijdsbereik voor een logboekquery in Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249592"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Querybereik en -tijdsbereik registreren in Azure Monitor Log Analytics
Wanneer u een [logboekquery](log-query-overview.md) uitvoert in [Log Analytics in de Azure-portal,](get-started-portal.md)is de set gegevens die door de query zijn geëvalueerd, afhankelijk van het bereik en het tijdsbereik dat u selecteert. In dit artikel wordt het bereik en het tijdsbereik beschreven en hoe u elk aantal instellen, afhankelijk van uw vereisten. Het beschrijft ook het gedrag van verschillende soorten scopes.


## <a name="query-scope"></a>Querybereik
Het querybereik definieert de records die door de query worden geëvalueerd. Dit omvat meestal alle records in één Log Analytics-werkruimte of Application Insights-toepassing. Met Log Analytics u ook een scope instellen voor een bepaalde bewaakte Azure-bron. Hierdoor kan een resource-eigenaar zich alleen richten op zijn gegevens, zelfs als die bron naar meerdere werkruimten wordt geschreven.

Het bereik wordt altijd linksboven in het venster Log Analytics weergegeven. Een pictogram geeft aan of het bereik een log analytics-werkruimte of een application Insights-toepassing is. Geen pictogram geeft een andere Azure-bron aan.

![Bereik](media/scope/scope.png)

Het bereik wordt bepaald door de methode die u gebruikt om Log Analytics te starten, en in sommige gevallen u het bereik wijzigen door erop te klikken. In de volgende tabel worden de verschillende soorten scope weergegeven die worden gebruikt en de verschillende details voor elk.

| Querybereik | Records in bereik | Selecteren | Bereik wijzigen |
|:---|:---|:---|:---|
| Log Analytics-werkruimte | Alle records in de werkruimte Log Analytics. | Selecteer **Logboeken** in het menu **Azure Monitor** of in het menu **Logboekanalysewerkruimten.**  | Kan het bereik wijzigen in elk ander resourcetype. |
| Toepassing Application Insights | Alle records in de application Insights-toepassing. | Selecteer **Analytics** op **de overzichtspagina** van toepassingsinzichten. | Kan alleen het bereik wijzigen in een andere Application Insights-toepassing. |
| Resourcegroep | Records die zijn gemaakt door alle resources in de resourcegroep. Kan gegevens van meerdere Log Analytics-werkruimten bevatten. | Selecteer **Logboeken** in het menu resourcegroep. | Kan het bereik niet wijzigen.|
| Abonnement | Records die zijn gemaakt door alle bronnen in het abonnement. Kan gegevens van meerdere Log Analytics-werkruimten bevatten. | Selecteer **Logboeken** in het abonnementsmenu.   | Kan het bereik niet wijzigen. |
| Andere Azure-bronnen | Records die door de resource zijn gemaakt. Kan gegevens van meerdere Log Analytics-werkruimten bevatten.  | Selecteer **Logboeken** in het resourcemenu.<br>OF<br>Selecteer **Logboeken** in het menu **Azure Monitor** en selecteer vervolgens een nieuw bereik. | Kan alleen het bereik wijzigen in hetzelfde resourcetype. |

### <a name="limitations-when-scoped-to-a-resource"></a>Beperkingen wanneer deze worden beperkt tot een resource

Wanneer het querybereik een loganalytics-werkruimte of een Application Insights-toepassing is, zijn alle opties in de portal en alle queryopdrachten beschikbaar. Wanneer de volgende opties in de portal echter worden gekoppeld aan een resource, zijn de volgende opties in de portal niet beschikbaar omdat ze zijn gekoppeld aan één werkruimte of toepassing:

- Opslaan
- Queryverkenner
- Nieuwe waarschuwingsregel

U de volgende opdrachten in een query niet gebruiken wanneer deze worden ingesteld op een resource, omdat de queryscope al werkruimten bevat met gegevens voor die bron of set resources:

- [App](app-expression.md)
- [Werkruimte](workspace-expression.md)
 

## <a name="query-limits"></a>Querylimieten
Mogelijk hebt u zakelijke vereisten voor een Azure-bron om gegevens naar meerdere Log Analytics-werkruimten te schrijven. De werkruimte hoeft zich niet in dezelfde regio als de resource te bevinden en één werkruimte verzamelt mogelijk gegevens uit bronnen in verschillende regio's.  

Het instellen van het bereik op een resource of set resources is een bijzonder krachtige functie van Log Analytics, omdat u hiermee gedistribueerde gegevens automatisch consolideren in één query. Het kan echter de prestaties aanzienlijk beïnvloeden als gegevens moeten worden opgehaald uit werkruimten in meerdere Azure-regio's.

Log Analytics beschermt tegen overmatige overhead van query's die werkplekken in meerdere regio's omvatten door een waarschuwing of fout uit te geven wanneer een bepaald aantal regio's wordt gebruikt. Uw query ontvangt een waarschuwing als het bereik werkruimten in 5 of meer regio's bevat. het zal nog steeds draaien, maar het kan te veel tijd in beslag nemen.

![Querywaarschuwing](media/scope/query-warning.png)

Uw query wordt geblokkeerd als het bereik werkruimten in 20 of meer regio's bevat. In dit geval wordt u gevraagd het aantal werkruimtegebieden te verminderen en te proberen de query opnieuw uit te voeren. In de vervolgkeuzelijst worden alle regio's in het bereik van de query weergegeven en moet u het aantal regio's verminderen voordat u probeert de query opnieuw uit te voeren.

![Query is mislukt](media/scope/query-failed.png)


## <a name="time-range"></a>Tijdsbereik
Het tijdsbereik geeft de set records op die voor de query worden geëvalueerd op basis van het moment waarop de record is gemaakt. Dit wordt gedefinieerd door een standaardeigenschap op elke record in de werkruimte of toepassing zoals opgegeven in de volgende tabel.

| Locatie | Eigenschap |
|:---|:---|
| Log Analytics-werkruimte          | TimeGenerated |
| Toepassing Application Insights | tijdstempel     |

Stel het tijdsbereik in door het te selecteren in de tijdkiezer boven aan het venster Logboekanalyse.  U een vooraf gedefinieerde periode selecteren of **Aangepast** selecteren om een specifiek tijdsbereik op te geven.

![Tijdkiezer](media/scope/time-picker.png)

Als u een filter instelt in de query waarin de eigenschap Standaardtijd wordt gebruikt zoals in de bovenstaande tabel wordt weergegeven, wordt de tijdkiezer gewijzigd **in De query instellen**en is de tijdkiezer uitgeschakeld. In dit geval is het het meest efficiënt om het filter boven aan de query te plaatsen, zodat elke volgende verwerking alleen hoeft te werken met de gefilterde records.

![Gefilterde query](media/scope/query-filtered.png)

Als u de [opdracht werkruimte](workspace-expression.md) of [app](app-expression.md) gebruikt om gegevens uit een andere werkruimte of toepassing op te halen, kan de tijdkiezer zich anders gedragen. Als het bereik een werkruimte Log Analytics is en u **de app**gebruikt of als het bereik een Application Insights-toepassing is en u **werkruimte**gebruikt, begrijpt Log Analytics mogelijk niet dat de eigenschap die in het filter wordt gebruikt, het tijdfilter moet bepalen.

In het volgende voorbeeld wordt het bereik ingesteld op een werkruimte Log Analytics.  De query maakt gebruik van **werkruimte** om gegevens op te halen uit een andere Log Analytics-werkruimte. De tijdkiezer wijzigt **in De query instellen** omdat er een filter wordt geplaatst dat de verwachte eigenschap **TimeGenerated** gebruikt.

![Query met werkruimte](media/scope/query-workspace.png)

Als de query **app** gebruikt om gegevens uit een Application Insights-toepassing op te halen, herkent Log Analytics de eigenschap **timestamp** in het filter niet en blijft de tijdkiezer ongewijzigd. In dit geval worden beide filters toegepast. In het voorbeeld worden alleen records die in de afgelopen 24 uur zijn gemaakt, in de query opgenomen, ook al worden 7 dagen in de **waarbeding** opgegeven.

![Query met app](media/scope/query-app.png)

## <a name="next-steps"></a>Volgende stappen

- Loop door een [zelfstudie over het gebruik van Log Analytics in de Azure-portal.](get-started-portal.md)
- Loop door een [tutorial over het schrijven van query's.](get-started-queries.md)
