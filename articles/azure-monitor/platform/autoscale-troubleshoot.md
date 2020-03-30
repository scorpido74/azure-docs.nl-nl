---
title: Azure-autoscale oplossen
description: Problemen met Azure-autoscaling opsporen die worden gebruikt in Service Fabric, Virtual Machines, Web Apps en cloudservices.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751330"
---
# <a name="troubleshooting-azure-autoscale"></a>Azure-autoscale oplossen
 
Met Azure Monitor-autoscale u de juiste hoeveelheid resources uitvoeren om de belasting van uw toepassing af te handelen. Het stelt u in staat om resources toe te voegen om de toename van de belasting aan te kunnen en ook geld te besparen door resources te verwijderen die niet actief zijn. U schalen op basis van een planning, vaste datumtijd of resourcestatistiek die u kiest. Zie [Overzicht autoscale voor](autoscale-overview.md)meer informatie.

De autoscale-service biedt u statistieken en logboeken om te begrijpen welke schaalacties hebben plaatsgevonden en de evaluatie van de voorwaarden die tot deze acties hebben geleid. U antwoorden vinden op vragen zoals:

- Waarom scaleerde mijn service of in?
- Waarom schaalde mijn service niet?
- Waarom is een actie op autoschaal mislukt?
- Waarom neemt een autoscale-actie de tijd om te schalen?
  
## <a name="autoscale-metrics"></a>Statistieken voor autoschaal

Autoscale biedt u [vier statistieken](metrics-supported.md#microsoftinsightsautoscalesettings) om de werking ervan te begrijpen. 

- **Waargenomen metrische waarde** - De waarde van de statistiek waarop u hebt gekozen om de schaalactie uit te voeren, zoals gezien of berekend door de autoscale-engine. Omdat één instelling voor automatische waarden meerdere regels en dus meerdere metrische bronnen kan hebben, u filteren met behulp van 'metrische bron' als dimensie.
- **Metrische drempelwaarde** : de drempel die u instelt om de schaalactie uit te voeren. Omdat één instelling voor automatische waarden meerdere regels en dus meerdere metrische bronnen kan hebben, u filteren met behulp van 'metrische regel' als dimensie.
- **Waargenomen capaciteit** - Het actieve aantal exemplaren van de doelbron zoals gezien door autoscale-engine.
- **Gestarte schaalacties**: het aantal acties voor uit- en inschalen dat door de engine voor automatisch schalen wordt gestart. U filteren op schaal-out versus schaal in acties.

U de [Metrics Explorer](metrics-getting-started.md) gebruiken om de bovenstaande statistieken allemaal op één plaats in kaart te brengen. De grafiek moet het als eerste te zien zijn:

  - de werkelijke statistiek
  - de statistiek zoals gezien/berekend door autoscale engine
  - de drempel voor een schaalactie
  - de verandering in capaciteit 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Voorbeeld 1 - Een eenvoudige regel voor automatisch schalen analyseren 

We hebben een eenvoudige autoscale instelling voor een virtuele machine schaal set die:

- schaalt uit wanneer het gemiddelde CPU-percentage van een set groter is dan 70% gedurende 10 minuten 
- schalen in wanneer het CPU-percentage van de set is minder dan 5% voor meer dan 10 minuten. 

Bekijk de statistieken van de autoscale-service.
 
![Voorbeeld van virtuele machineschaalsetpercentage CPU-voorbeeld](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Voorbeeld van virtuele machineschaalsetpercentage CPU-voorbeeld](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Figuur 1a - Percentage CPU-statistiek voor virtuele machineschaalset en de statistiek Waargenomen metrische waarde voor instelling automatisch schalen***

![Metrische drempel en waargenomen capaciteit](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Figuur 1b - Metrische drempel en waargenomen capaciteit***

In figuur 1b is de **metrische drempel** (lichtblauwe lijn) voor de uitschalijnregel 70.  De **waargenomen capaciteit** (donkerblauwe lijn) toont het aantal actieve exemplaren, dat momenteel 3 is. 

> [!NOTE]
> U moet de **metrische drempelwaarde** filteren op de dimensieschaal schaal van de metrische triggerregel (verhoging) om de schaaldrempel en de schaal in regel (afname) te zien. 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Voorbeeld 2 - Geavanceerde automatische schaling voor een virtuele machineschaalset

We hebben een instelling voor automatische schaal waarmee een bron voor virtuele machineschaalkan worden uitgeschaald op basis van zijn eigen metrische **uitgaande stromen.** De optie **Verdeling per instantietelling** voor de metrische drempelwaarde is ingeschakeld. 

De regel schaalactie is: 

Als de waarde van **Uitgaande stroom per instantie** groter is dan 10, moet de automatische schaalservice met 1 instantie worden uitschaald. 

In dit geval wordt de waargenomen metrische waarde van de automatische schaalmotor berekend als de werkelijke metrische waarde gedeeld door het aantal exemplaren. Als de waargenomen metrische waarde lager is dan de drempelwaarde, wordt er geen scale-outactie gestart. 
 
![Voorbeeld van het voorbeeld van automatische schaaldiagrammen voor automatische schaal](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Voorbeeld van het voorbeeld van automatische schaaldiagrammen voor automatische schaal](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Figuur 2 - Voorbeeld van automatische schaalsetmetstabellen voor automatische schaal***

In figuur 2 ziet u twee metrische grafieken. 

De grafiek bovenaan toont de werkelijke waarde van de statistiek **Uitgaande stromen.** De werkelijke waarde is 6. 

De grafiek aan de onderkant toont een paar waarden. 
 - De **waargenomen metrische waarde** (lichtblauw) is 3 omdat er 2 actieve exemplaren zijn en 6 gedeeld door 2 is 3. 
 - De **waargenomen capaciteit** (paars) toont het aantal gevallen van autoscale-engine. 
 - De **metrische drempel** (lichtgroen) is ingesteld op 10. 

Als er meerdere regels voor schaalactie zijn, u splitsen of de optie **Filter toevoegen gebruiken** in het grafiek Metrics explorer om metrische gegevens te bekijken op basis van een specifieke bron of regel. Zie Geavanceerde functies van metrische [grafieken voor](metrics-charts.md#apply-splitting-to-a-chart) meer informatie over het splitsen van een metrische grafiek : splitsen

## <a name="example-3---understanding-autoscale-events"></a>Voorbeeld 3 - Gebeurtenissen voor automatisch schalen begrijpen

Ga in het instellingsscherm automatisch schalen naar het tabblad **Geschiedenis uitvoeren** om de meest recente schaalacties te bekijken. Het tabblad toont ook de wijziging in **de waargenomen capaciteit in de** loop van de tijd. Als u meer informatie wilt over alle acties voor automatisch schalen, inclusief bewerkingen zoals instellingen voor automatisch schalen bijwerken/verwijderen, bekijkt u het activiteitenlogboek en filtert u op bewerkingen voor automatische schaal.

![Voergeschiedenis voor automatische schaal](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Bronlogboeken automatisch schalen

Net als elke andere Azure-bron biedt de autoscale-service [bronlogboeken.](platform-logs-overview.md) Er zijn twee categorieën logboeken.

- **Autoscale Evaluaties** - De autoscale engine registreert logboekvermeldingen voor elke conditie-evaluatie elke keer dat het een controle doet.  De vermelding bevat details over de waargenomen waarden van de statistieken, de geëvalueerde regels en of de evaluatie heeft geleid tot een schaalactie of niet.

- **Acties voor schaalvergroting** automatisch schalen : de actiegebeurtenissen voor de schaal van de engine worden opgenomen die zijn geïnitieerd door de autoscale-service en de resultaten van deze schalen (succes, fout en hoeveel schaling heeft plaatsgevonden zoals die is gezien door de autoscale-service).

Zoals bij elke azure monitor ondersteunde service, u [diagnostische instellingen](diagnostic-settings.md) gebruiken om deze logboeken te routeren:

- naar uw Log Analytics-werkruimte voor gedetailleerde analyses
- naar Gebeurtenishubs en vervolgens naar niet-Azure-hulpprogramma's
- naar uw Azure-opslagaccount voor archivering  

![Diagnostische instellingen automatisch schalen](media/autoscale-troubleshoot/diagnostic-settings.png)

De vorige afbeelding toont de diagnostische instellingen voor automatische schaal van de Azure-portal. Daar u het tabblad Diagnostische/bronlogboeken selecteren en logboekverzameling en -routering inschakelen. U dezelfde actie ook uitvoeren met REST API, CLI, PowerShell, Resource Manager-sjablonen voor diagnostische instellingen door het brontype te kiezen als *Microsoft.Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Probleemoplossing met logboeken voor automatisch schalen 

Voor de beste probleemoplossingservaring raden we u aan uw logboeken via een werkruimte te routeren naar Azure Monitor Logs (Log Analytics) wanneer u de instelling voor automatisch schalen maakt. Dit proces wordt weergegeven in de afbeelding in de vorige sectie. U de evaluaties valideren en acties beter schalen met Behulp van Log Analytics.

Nadat u de automatische schaallogboeken hebt geconfigureerd om naar de werkruimte Log Analytics te worden verzonden, u de volgende query's uitvoeren om de logboeken te controleren. 

Probeer deze query om aan de slag te gaan om de meest recente evaluatielogboeken voor automatische waarden weer te geven:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Of probeer de volgende query om de meest recente schaalactielogboeken weer te geven:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Gebruik de volgende secties voor deze vragen. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Er is een schaalactie opgetreden die ik niet had verwacht

Voer eerst de query uit voor schaalactie om de schaalactie te vinden waarin u geïnteresseerd bent. Als dit de laatste schaalactie is, gebruikt u de volgende query:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Selecteer het veld CorrelationId in het logboek van schaalacties. Gebruik de CorrelationId om het juiste evaluatielogboek te vinden. Als u de onderstaande query uitvoert, worden alle geëvalueerde regels en voorwaarden weergegeven die tot die schaalactie leiden.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Welk profiel heeft een schaalactie veroorzaakt?

Er is een geschaalde actie opgetreden, maar u hebt overlappende regels en profielen en moet worden opgespoord welke actie de oorzaak is van de actie. 

Zoek de correlationId van de schaalactie (zoals uitgelegd in voorbeeld 1) en voer de query uit op evaluatielogboeken om meer te weten te komen over het profiel.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

De hele profielevaluatie kan ook beter worden begrepen aan de hand van de volgende vraag

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Er is geen schaalactie uitgevoerd

Ik verwachtte een schaal actie en het gebeurde niet. Er mogen geen schaalactiegebeurtenissen of logboeken zijn.

Bekijk de statistieken voor automatisch schalen als u een op metrische gegevens gebaseerde schaalregel gebruikt. Het is mogelijk dat de **waargenomen metrische waarde** of **waargenomen capaciteit** niet zijn wat u verwachtte dat ze zouden zijn en daarom heeft de schaalregel niet brand gesticht. Je zou nog steeds evaluaties zien, maar geen scale-out regel. Het is ook mogelijk dat de afkoeltijd een schaalactie voorkwam. 

 Bekijk de evaluatielogboeken voor automatische schaal gedurende de periode waarin u verwachtte dat de schaalactie zou plaatsvinden. Bekijk alle evaluaties die het deed en waarom het besloot om geen schaalactie op gang te brengen.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Schaalactie is mislukt

Er kan een geval zijn waarin de autoscale-service de schaalactie heeft ondernomen, maar het systeem heeft besloten de schaalactie niet te schalen of niet te voltooien. Gebruik deze query om de mislukte schaalacties te vinden.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Maak waarschuwingsregels om op de hoogte te worden gesteld van acties of fouten op de automatische schaal. U ook waarschuwingsregels maken om een melding te krijgen over automatisch schaalgebeurtenissen.

## <a name="schema-of-autoscale-resource-logs"></a>Schema van bronlogboeken voor automatisch schalen

Zie [bronlogboeken voor automatisch schalen voor](autoscale-resource-log-schema.md) meer informatie

## <a name="next-steps"></a>Volgende stappen
Lees informatie over [de aanbevolen procedures voor automatische schaal](autoscale-best-practices.md). 
