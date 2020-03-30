---
title: Kosten van het verbruiksplan schatten in Azure-functies
description: Meer informatie over hoe u de kosten die u maken bij het uitvoeren van uw functie-app in een verbruiksplan in Azure, beter inschatten.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963985"
---
# <a name="estimating-consumption-plan-costs"></a>Kosten van het verbruiksplan schatten

Er zijn momenteel drie typen hostingplannen voor een app die wordt uitgevoerd in Azure-functies, waarbij elk abonnement een eigen prijsmodel heeft: 

| Plannen | Beschrijving |
| ---- | ----------- |
| [**Verbruik**](functions-scale.md#consumption-plan) | Er worden alleen kosten in rekening gebracht voor de tijd die uw functie-app wordt uitgevoerd. Dit abonnement bevat een [gratis][subsidieprijspagina] per abonnementsbasis.|
| [**Premium**](functions-scale.md#premium-plan) | Biedt u dezelfde functies en schalingsmechanismen als het verbruiksplan, maar met verbeterde prestaties en VNET-toegang. De kosten zijn gebaseerd op de door u gekozen prijscategorie. Zie [Azure Functions Premium-abonnement](functions-premium-plan.md)voor meer informatie. |
| [**Dedicated (App Service)**](functions-scale.md#app-service-plan) <br/>(basislaag of hoger) | Wanneer u in speciale VM's of in isolatie moet worden uitgevoerd, gebruikt u aangepaste afbeeldingen of wilt u de capaciteit van uw overtollige App Service-abonnement gebruiken. Maakt gebruik [van facturering met een regulier App Service-abonnement](https://azure.microsoft.com/pricing/details/app-service/). De kosten zijn gebaseerd op de door u gekozen prijscategorie.|

U hebt het plan gekozen dat uw functieprestaties en kostenvereisten het beste ondersteunt. Zie [Azure Functions schalen en hosten](functions-scale.md)voor meer informatie.

Dit artikel behandelt slechts het plan van de Consumptie, aangezien dit plan in veranderlijke kosten resulteert. Dit artikel vervangt het [faq-artikel over de facturering van het kostenplan.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

Duurzame functies kunnen ook worden uitgevoerd in een verbruiksplan. Zie Facturering met duurzame [functies](./durable/durable-functions-billing.md)voor meer informatie over de kostenoverwegingen bij het gebruik van duurzame functies.

## <a name="consumption-plan-costs"></a>Kosten van verbruiksabonnement

De *uitvoeringskosten* van één functieuitvoering worden gemeten in *GB-seconden.* Uitvoeringskosten worden berekend door het geheugengebruik te combineren met de uitvoeringstijd. Een functie die langer wordt uitgevoerd kost meer, net als een functie die meer geheugen verbruikt. 

Houd rekening met een geval waarin de hoeveelheid geheugen die door de functie wordt gebruikt, constant blijft. In dit geval is het berekenen van de kosten een eenvoudige vermenigvuldiging. Stel dat uw functie gedurende 3 seconden 0,5 GB verbruikt. Dan zijn `0.5GB * 3s = 1.5 GB-seconds`de uitvoeringskosten. 

Aangezien het geheugengebruik in de loop van de tijd verandert, is de berekening in wezen de integraal van het geheugengebruik in de loop van de tijd.  Het systeem doet deze berekening door het geheugengebruik van het proces (samen met onderliggende processen) op regelmatige tijdstippen te bemonsteren. Zoals vermeld op de [prijspagina,]wordt het geheugengebruik afgerond op de dichtstbijzijnde emmer van 128 MB. Wanneer uw proces 160 MB gebruikt, worden er kosten in rekening gebracht voor 256 MB. De berekening houdt rekening met gelijktijdigheid, wat meerdere gelijktijdige functieuitvoeringen in hetzelfde proces is.

> [!NOTE]
> Hoewel het CPU-gebruik niet direct wordt meegenomen in uitvoeringskosten, kan dit van invloed zijn op de kosten wanneer dit van invloed is op de uitvoeringstijd van de functie.

## <a name="other-related-costs"></a>Overige gerelateerde kosten

Houd er bij het schatten van de totale kosten van het uitvoeren van uw functies in een abonnement rekening mee dat de runtime van de functies verschillende andere Azure-services gebruikt, die elk afzonderlijk worden gefactureerd. Bij het berekenen van de prijzen voor functie-apps, alle triggers en bindingen die u hebt die integreren met andere Azure-services, moet u deze extra services maken en betalen. 

Voor functies die worden uitgevoerd in een verbruiksplan, zijn de totale kosten de uitvoeringskosten van uw functies, plus de kosten van bandbreedte en aanvullende services. 

Wanneer u de totale kosten van uw functie-app en gerelateerde services inschat, gebruikt u de [Azure-prijscalculator.](https://azure.microsoft.com/pricing/calculator/?service=functions) 

| Gerelateerde kosten | Beschrijving |
| ------------ | ----------- |
| **Opslagaccount** | Elke functie-app vereist dat u een gekoppeld [Azure Storage-account](../storage/common/storage-introduction.md#types-of-storage-accounts)voor algemeen gebruik hebt, dat [afzonderlijk wordt gefactureerd.](https://azure.microsoft.com/pricing/details/storage/) Dit account wordt intern gebruikt door de runtime Functies, maar u het ook gebruiken voor storagetriggers en bindingen. Als u geen opslagaccount hebt, wordt er een voor u gemaakt wanneer de functie-app wordt gemaakt. Zie [Vereisten voor opslagaccount](storage-considerations.md#storage-account-requirements)voor meer informatie.|
| **Application Insights** | Functies zijn gebaseerd op [Application Insights](../azure-monitor/app/app-insights-overview.md) om een krachtige monitoringervaring te bieden voor uw functie-apps. Hoewel dit niet nodig is, moet u [de integratie van Application Insights inschakelen.](functions-monitoring.md#enable-application-insights-integration) Elke maand wordt een gratis toekenning van telemetriegegevens opgenomen. Zie [de prijspagina azure monitor](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie . |
| **Netwerkbandbreedte** | U betaalt niet voor gegevensoverdracht tussen Azure-services in dezelfde regio. U echter kosten maken voor uitgaande gegevensoverdrachten naar een andere regio of buiten Azure. Zie [Details van de bandbreedteprijzen](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie. |

## <a name="behaviors-affecting-execution-time"></a>Gedrag dat van invloed is op de uitvoeringstijd

De volgende gedragingen van uw functies kunnen van invloed zijn op de uitvoeringstijd:

+ **Triggers en bindingen**: De tijd die nodig is om input van uw [functiebindingen](functions-triggers-bindings.md) te lezen en te schrijven, wordt geteld als uitvoeringstijd. Wanneer uw functie bijvoorbeeld een uitvoerbinding gebruikt om een bericht naar een Azure-opslagwachtrij te schrijven, bevat uw uitvoeringstijd de tijd die nodig is om het bericht naar de wachtrij te schrijven, die is opgenomen in de berekening van de functiekosten. 

+ **Asynchrone uitvoering**: De tijd dat uw functie wacht`await` op de resultaten van een async-aanvraag (in C#) wordt geteld als uitvoeringstijd. De berekening gb-seconde is gebaseerd op de begin- en eindtijd van de functie en het geheugengebruik over die periode. Wat er gebeurt in die tijd in termen van CPU-activiteit is niet meegenomen in de berekening. Mogelijk u de kosten tijdens asynchrone bewerkingen verlagen met behulp van [duurzame functies.](durable/durable-functions-overview.md) Je wordt niet gefactureerd voor tijd doorgebracht in wacht in orchestrator functies.

## <a name="view-execution-data"></a>Uitvoeringsgegevens weergeven

In [uw factuur](/azure/billing/billing-download-azure-invoice)u de kostengerelateerde gegevens van Total **Executions - Functions** en Execution Time - **Functions**bekijken, samen met de werkelijke gefactureerde kosten. Deze factuurgegevens zijn echter een maandelijks aggregaat voor een vorige factuurperiode. 

Om de kostenimpact van uw functies beter te begrijpen, u Azure Monitor gebruiken om kostengerelateerde statistieken weer te geven die momenteel worden gegenereerd door uw functie-apps. U [Azure Monitor metrics explorer](../azure-monitor/platform/metrics-getting-started.md) gebruiken in de [Azure-portal] of REST API's om deze gegevens op te halen.

### <a name="monitor-metrics-explorer"></a>Statistieken verkenner controleren

Gebruik [Azure Monitor metrics explorer](../azure-monitor/platform/metrics-getting-started.md) om kostengerelateerde gegevens voor uw functie-apps voor het verbruiksplan in een grafische indeling weer te geven. 

1. Zoek en selecteer **Monitor** onder **Services**boven aan de `monitor` [Azure-portal] in **zoekservices, resources en documenten.**

1. Selecteer aan de linkerkant **Metrics** > **Selecteer een resource**en gebruik vervolgens de instellingen onder de afbeelding om de functie-app te kiezen.

    ![Uw functie-app-bron selecteren](media/functions-consumption-costing/select-a-resource.png)

      
    |Instelling  |Voorgestelde waarde  |Beschrijving  |
    |---------|---------|---------|
    | Abonnement    |  Uw abonnement  | Het abonnement met uw functie-app.  |
    | Resourcegroep     | Uw resourcegroep  | De resourcegroep die uw functie-app bevat.   |
    | Resourcetype     |  App Services | Functie-apps worden weergegeven als App Services-instanties in Monitor. |
    | Resource     |  Uw functie-app  | De functie-app om te controleren.        |

1. Selecteer **Toepassen** om uw functie-app te kiezen als de bron die u wilt controleren.

1. Kies **functieuitvoeringsaantal** en **som** voor **aggregatie**vanuit **Metric**. Hiermee wordt de som van de uitvoeringstellingen tijdens de gekozen periode toegevoegd aan de grafiek.

    ![Een statistiek van de functie-app definiëren die aan de grafiek moet worden toegevoegd](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selecteer **Metrische en** herhaalstap2-4 toevoegen om **functieuitvoeringseenheden** aan de grafiek toe te voegen. 

De resulterende grafiek bevat de totalen voor beide uitvoeringsstatistieken in het gekozen tijdsbereik, wat in dit geval twee uur is.

![Grafiek van functieuitvoeringstellingen en uitvoeringseenheden](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Omdat het aantal uitvoeringseenheden zoveel groter is dan het aantal uitvoeringen, toont de grafiek alleen uitvoeringseenheden.

Deze grafiek toont een totaal van `Function Execution Units` 1,11 miljard verbruikt in een periode van twee uur, gemeten in MB-milliseconden. Als u wilt converteren naar GB-seconden, deelt u door 1024000. In dit voorbeeld verbruikt `1110000000 / 1024000 = 1083.98` de functie-app GB-seconden. U deze waarde nemen en vermenigvuldigen met de huidige prijs van uitvoeringstijd op de pagina [Prijzen van functies]prijzen[pagina], die u de kosten van deze twee uur geeft, ervan uitgaande dat u al gebruik hebt gemaakt van gratis subsidies van de uitvoering tijd. 

### <a name="azure-cli"></a>Azure-CLI

De [Azure CLI](/cli/azure/) heeft opdrachten voor het ophalen van metrische gegevens. U de CLI gebruiken vanuit een lokale opdrachtomgeving of rechtstreeks vanuit de portal met [Azure Cloud Shell.](../cloud-shell/overview.md) De volgende [opdracht met AZ-monitorstatistieken](/cli/azure/monitor/metrics#az-monitor-metrics-list) retourneert bijvoorbeeld uurgegevens in dezelfde periode die eerder waren gebruikt.

Zorg ervoor `<AZURE_SUBSCRIPTON_ID>` dat u de opdracht vervangt door uw Azure-abonnements-id waarop de opdracht wordt uitgevoerd.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Met deze opdracht wordt een JSON-payload geretourneerd die lijkt op het volgende voorbeeld:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Deze specifieke reactie `2019-09-11T21:46` toont `2019-09-11T23:18`aan dat de app verbruikt 11100000000 MB-milliseconden (1083,98 GB-seconden).

## <a name="determine-memory-usage"></a>Geheugengebruik bepalen

Functie-uitvoeringseenheden zijn een combinatie van uitvoeringstijd en uw geheugengebruik, waardoor het een moeilijke statistiek is voor het begrijpen van het geheugengebruik. Geheugengegevens zijn geen statistiek die momenteel beschikbaar is via Azure Monitor. Als u echter het geheugengebruik van uw app wilt optimaliseren, u de prestatiemetergegevens gebruiken die zijn verzameld door Application Insights.  

Als u dit nog niet hebt gedaan, [schakelt u Toepassingsinzichten in uw functie-app](functions-monitoring.md#enable-application-insights-integration)in. Als deze integratie is ingeschakeld, u [deze telemetriegegevens opvragen in de portal.](functions-monitoring.md#query-telemetry-data)  

Selecteer **onder Controle** **logboeken (Analytics)** en kopieer vervolgens de volgende telemetriequery en plak deze in het queryvenster en selecteer **Uitvoeren**. Met deze query wordt het totale geheugengebruik op elk bemonsterd tijdstip geretourneerd.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

De resultaten zien eruit als het volgende voorbeeld:

| timestamp \[UTC\]          | name          | waarde       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14\.947 | Privébytes | 209,932,288 |
| 9/12/2019, 1:06:14\.994 AM | Privébytes | 212,189,184 |
| 9/12/2019, 1:06:30\.010 | Privébytes | 231,714,816 |
| 9/12/2019, 1:07:15\.040 | Privébytes | 210,591,744 |
| 9/12/2019, 1:12:16\.285 AM | Privébytes | 216,285,184 |
| 9/12/2019, 1:12:31\.376 AM | Privébytes | 235,806,720 |

## <a name="function-level-metrics"></a>Statistieken op functieniveau

Azure Monitor houdt statistieken bij op resourceniveau, wat voor Functies de functie-app is. Application Insights-integratie zendt per functie statistieken uit. Hier is een voorbeeldanalysequery om de gemiddelde duur van een functie te krijgen:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | gemiddeldeDurationMilliseconden |
|----------------------------|-----------------------------|
| AvgDurationMs voor queuetriggeren | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over functie-apps voor bewaken](functions-monitoring.md)

[prijspagina]:https://azure.microsoft.com/pricing/details/functions/
[Azure-portal]: https://portal.azure.com
