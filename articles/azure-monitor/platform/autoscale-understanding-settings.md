---
title: Informatie over instellingen voor automatisch schalen in Azure Monitor
description: Een gedetailleerde uitsplitsing van de instellingen voor automatisch schalen en hoe ze werken. Van toepassing op virtuele machines, cloudservices, webapps
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: 9a2b94208de7ce490a0e7acfbb71175b4a7c846e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364302"
---
# <a name="understand-autoscale-settings"></a>Inzicht in instellingen voor automatisch schalen
Instellingen voor automatisch schalen zorgen ervoor dat u over de juiste hoeveelheid resources beschikt om de fluctuerende belasting van uw toepassing af te handelen. U instellingen voor automatisch schalen configureren om te worden geactiveerd op basis van statistieken die belasting of prestaties aangeven of die worden geactiveerd op een geplande datum en tijd. In dit artikel wordt de anatomie van een instelling Autoscale grondig bekeken. Het artikel begint met het schema en de eigenschappen van een instelling en loopt vervolgens door de verschillende profieltypen die kunnen worden geconfigureerd. Ten slotte wordt in het artikel besproken hoe de functie Autoscale in Azure evalueert welk profiel op een bepaald moment moet worden uitgevoerd.

## <a name="autoscale-setting-schema"></a>Schema voor het instellen van automatisch schalen
Om het instellingsschema voor autoschaal te illustreren, wordt de volgende instelling Autoscale gebruikt. Het is belangrijk op te merken dat deze instelling Autoscale:
- Eén profiel. 
- Twee metrische regels in dit profiel: een voor schaal uit, en een voor schaal in.
  - De scale-outregel wordt geactiveerd wanneer het gemiddelde cpu-metrische percentage van de virtuele machineschaal groter is dan 85 procent in de afgelopen 10 minuten.
  - De scale-in regel wordt geactiveerd wanneer het gemiddelde van de virtuele machineschaal voor de afgelopen minuut minder dan 60 procent is.

> [!NOTE]
> Een instelling kan meerdere profielen hebben. Zie de sectie [profielen](#autoscale-profiles) voor meer informatie. Een profiel kan ook meerdere scale-out regels en scale-in regels gedefinieerd. Zie de [evaluatiesectie](#autoscale-evaluation) om te zien hoe ze worden geëvalueerd.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Sectie | Elementnaam | Beschrijving |
| --- | --- | --- |
| Instelling | Id | De resource-id van de instelling Autoscale. Instellingen voor automatisch schalen zijn een Azure Resource Manager-bron. |
| Instelling | name | De naam van de instelling Autoscale. |
| Instelling | location | De locatie van de instelling Autoscale. Deze locatie kan afwijken van de locatie van de resource die wordt geschaald. |
| properties | targetResourceUri | De resource-id van de resource die wordt geschaald. U slechts één instelling voor autoschaal per resource hebben. |
| properties | Profielen | Er is een instelling voor autoschaal samengesteld uit een of meer profielen. Elke keer dat de Autoscale-engine draait, voert deze één profiel uit. |
| profiel | name | De naam van het profiel. U elke naam kiezen waarmee u het profiel identificeren. |
| profiel | Capaciteit.maximum | De maximaal toegestane capaciteit. Het zorgt ervoor dat Autoscale, bij het uitvoeren van dit profiel, uw resource niet schaalt boven dit nummer. |
| profiel | Capaciteit.minimum | De minimale capaciteit is toegestaan. Het zorgt ervoor dat Autoscale, bij het uitvoeren van dit profiel, uw resource niet onder dit getal schaalt. |
| profiel | Capacity.default | Als er een probleem is met het lezen van de resourcestatistiek (in dit geval de CPU van 'vmss1' en de huidige capaciteit onder de standaardwaarde ligt, schaalt Autoscale uit naar de standaardwaarde. Dit is om de beschikbaarheid van de resource te garanderen. Als de huidige capaciteit al hoger is dan de standaardcapaciteit, schaalt Autoscale niet in. |
| profiel | regels | Automatisch schalen schaalt automatisch tussen de maximale en minimale capaciteiten, met behulp van de regels in het profiel. Je meerdere regels in een profiel hebben. Meestal zijn er twee regels: een om te bepalen wanneer uit te schalen, en de andere om te bepalen wanneer te schalen in. |
| Regel | metrischeTrigger | Hiermee definieert u de metrische voorwaarde van de regel. |
| metrischeTrigger | metricName | De naam van de statistiek. |
| metrischeTrigger |  metricResourceUri | De resource-ID van de resource die de statistiek uitzendt. In de meeste gevallen is het hetzelfde als de resource die wordt geschaald. In sommige gevallen kan het anders zijn. U bijvoorbeeld een virtuele machineschaalset schalen op basis van het aantal berichten in een opslagwachtrij. |
| metrischeTrigger | timeGrain | De metrische bemonsteringsduur. **TimeGrain = "PT1M"** betekent bijvoorbeeld dat de statistieken elke 1 minuut moeten worden samengevoegd met behulp van de aggregatiemethode die is opgegeven in het statistiekelement. |
| metrischeTrigger | Statistiek | De aggregatiemethode binnen de periode TijdKorrel. Statistiek **= "Gemiddeld"** en **timeGrain = "PT1M"** betekent bijvoorbeeld dat de statistieken elke 1 minuut moeten worden samengevoegd, door het gemiddelde te nemen. Deze eigenschap bepaalt hoe de statistiek wordt bemonsterd. |
| metrischeTrigger | timeWindow | De hoeveelheid tijd om terug te kijken voor statistieken. Bijvoorbeeld, **timeWindow = "PT10M"** betekent dat elke keer dat Autoscale wordt uitgevoerd, het vraagt metrische gegevens voor de afgelopen 10 minuten. Met het tijdvenster kunnen uw statistieken worden genormaliseerd en wordt voorkomen dat u reageert op tijdelijke pieken. |
| metrischeTrigger | timeAggregation | De aggregatiemethode die wordt gebruikt om de gesamplede statistieken samen te voegen. **TimeAggregation = "Gemiddeld"** moet bijvoorbeeld de gesamplede statistieken samenvoegen door het gemiddelde te nemen. In het voorgaande geval, neem de tien 1-minuut monsters, en gemiddelde hen. |
| Regel | schaalActie | De actie die moet worden ondernomen wanneer de metrischeTrigger van de regel wordt geactiveerd. |
| schaalActie | richting | "Verhogen" om uit te schalen, of "Verkleinen" om in te schalen.|
| schaalActie | waarde | Hoeveel de capaciteit van de resource te verhogen of te verlagen. |
| schaalActie | cooldown | De hoeveelheid tijd om te wachten na een schaalbewerking voordat u opnieuw wordt geschaald. Als **cooldown = "PT10M"** bijvoorbeeld , probeert Autoscale niet nog eens 10 minuten opnieuw te schalen. De cooldown is om de statistieken te stabiliseren na het toevoegen of verwijderen van exemplaren. |

## <a name="autoscale-profiles"></a>Profielen automatisch schalen

Er zijn drie soorten Autoscale-profielen:

- **Regulier profiel:** Het meest voorkomende profiel. Als u uw resource niet hoeft te schalen op basis van de dag van de week of op een bepaalde dag, u een normaal profiel gebruiken. Dit profiel kan vervolgens worden geconfigureerd met metrische regels die bepalen wanneer u moet uitschalen en wanneer moet worden inschaald. U mag slechts één regulier profiel hebben gedefinieerd.

    Het voorbeeldprofiel dat eerder in dit artikel wordt gebruikt, is een voorbeeld van een regulier profiel. Houd er rekening mee dat het ook mogelijk is om een profiel in te stellen om te schalen naar een statisch aantal instanties voor uw resource.

- **Profiel van de vaste datum:** Dit profiel is voor speciale gevallen. Stel dat er op 26 december 2017 (PST) een belangrijke gebeurtenis komt. U wilt dat de minimale en maximale capaciteit van uw resource op die dag anders is, maar toch wordt geschaald op dezelfde statistieken. In dit geval moet u een profiel met een vaste datum toevoegen aan de lijst met profielen van uw instelling. Het profiel is geconfigureerd om alleen op de dag van het evenement uit te voeren. Voor elke andere dag gebruikt Autoscale het reguliere profiel.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Herhalingsprofiel:** Met dit type profiel u ervoor zorgen dat dit profiel altijd op een bepaalde dag van de week wordt gebruikt. Recidiefprofielen hebben alleen een begintijd. Ze worden uitgevoerd totdat het volgende herhalingsprofiel of het vaste datumprofiel is ingesteld om te starten. In een instelling Autoscale met slechts één herhalingsprofiel wordt dat profiel uitgevoerd, zelfs als er een normaal profiel is gedefinieerd in dezelfde instelling. De volgende twee voorbeelden illustreren hoe dit profiel wordt gebruikt:

    **Voorbeeld 1: Weekdagen vs. weekends**
    
    Laten we zeggen dat in het weekend, u wilt dat uw maximale capaciteit te zijn 4. Op weekdagen, omdat u meer belasting verwacht, wilt u dat uw maximale capaciteit 10 is. In dit geval zou uw instelling twee herhalingsprofielen bevatten, een om in het weekend te draaien en de andere op weekdagen.
    De instelling ziet er als volgt uit:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    De vorige instelling geeft aan dat elk herhalingsprofiel een schema heeft. Dit schema bepaalt wanneer het profiel begint te lopen. Het profiel stopt wanneer het tijd is om een ander profiel uit te voeren.

    In de vorige instelling begint bijvoorbeeld 'weekdayProfile' op maandag om 12:00 uur. Dat betekent dat dit profiel begint te lopen op maandag om 12:00 uur. Het gaat door tot zaterdag om 12:00 uur, wanneer "weekendProfile" is gepland om te beginnen met draaien.

    **Voorbeeld 2: Kantooruren**
    
    Stel dat u één metrische drempel wilt hebben tijdens kantooruren (09:00 tot 17:00 uur) en een andere voor alle andere tijden. De instelling zou er als volgt uitzien:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    De vorige instelling laat zien dat 'businessHoursProfile' begint op maandag om 9:00 uur en doorgaat tot 17:00 uur. Dat is wanneer "nonBusinessHoursProfile" begint te draaien. De "nonBusinessHoursProfile" loopt tot dinsdag 09:00 uur en vervolgens neemt de "businessHoursProfile" het weer over. Dit herhaalt zich tot vrijdag om 17:00 uur. Op dat moment loopt "nonBusinessHoursProfile" helemaal tot maandag om 9:00 uur.
    
> [!Note]
> De gebruikersinterface automatisch schalen in de Azure-portal dwingt eindtijden af voor herhalingsprofielen en begint het standaardprofiel van de instelling Autoscale tussen herhalingsprofielen uit te voeren.
    
## <a name="autoscale-evaluation"></a>Evaluatie van autoschaal
Aangezien instellingen voor Autoscale meerdere profielen kunnen hebben en elk profiel meerdere metrische regels kan hebben, is het belangrijk om te begrijpen hoe een instelling voor autoschaal wordt geëvalueerd. Elke keer dat de taak Automatisch schalen wordt uitgevoerd, wordt het profiel gekozen dat van toepassing is. Vervolgens evalueert Autoscale de minimum- en maximumwaarden en eventuele metrische regels in het profiel en beslist u of een schaalactie noodzakelijk is.

### <a name="which-profile-will-autoscale-pick"></a>Welk profiel kiest Autoscale?

Autoscale gebruikt de volgende reeks om het profiel te kiezen:
1. Het zoekt eerst naar een vast datumprofiel dat is geconfigureerd om nu uit te voeren. Als dat zo is, voert Autoscale het uit. Als er meerdere vaste datumprofielen zijn die moeten worden uitgevoerd, selecteert Autoscale de eerste.
2. Als er geen profielen met een vaste datum zijn, kijkt Autoscale naar herhalingsprofielen. Als er een herhalingsprofiel wordt gevonden, wordt het uitgevoerd.
3. Als er geen vaste datum- of herhalingsprofielen zijn, voert Autoscale het reguliere profiel uit.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hoe evalueert Autoscale meerdere regels?

Nadat Autoscale bepaalt welk profiel moet worden uitgevoerd, evalueert het alle uitschaingsregels in het profiel (dit zijn regels met **richting = "Verhogen").**

Als een of meer scale-outregels worden geactiveerd, berekent Autoscale de nieuwe capaciteit die wordt bepaald door de **schaalActie** van elk van deze regels. Vervolgens wordt het geschaald tot het maximum van deze capaciteiten, om de beschikbaarheid van de service te garanderen.

Stel dat er een virtuele machineschaal is ingesteld met een huidige capaciteit van 10. Er zijn twee scale-out regels: een die de capaciteit met 10 procent verhoogt, en een die de capaciteit met 3 tellen verhoogt. De eerste regel zou resulteren in een nieuwe capaciteit van 11, en de tweede regel zou resulteren in een capaciteit van 13. Om de beschikbaarheid van de service te garanderen, kiest Autoscale de actie die resulteert in de maximale capaciteit, zodat de tweede regel wordt gekozen.

Als er geen scale-outregels worden geactiveerd, evalueert Autoscale alle schaalregels (regels met **richting = "Verkleinen").** Autoscale neemt alleen een scale-in actie als alle scale-in regels worden geactiveerd.

Autoscale berekent de nieuwe capaciteit die wordt bepaald door de **schaalActie** van elk van deze regels. Vervolgens kiest het de schaalactie die resulteert in het maximum van deze capaciteiten om de beschikbaarheid van de service te garanderen.

Stel dat er een virtuele machineschaal is ingesteld met een huidige capaciteit van 10. Er zijn twee scale-in regels: een die de capaciteit met 50 procent vermindert, en een die de capaciteit met 3 tellen vermindert. De eerste regel zou resulteren in een nieuwe capaciteit van 5, en de tweede regel zou resulteren in een capaciteit van 7. Om de beschikbaarheid van de service te garanderen, kiest Autoscale de actie die resulteert in de maximale capaciteit, zodat de tweede regel wordt gekozen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Autoscale door te verwijzen naar het volgende:

* [Overzicht van autoscale](../../azure-monitor/platform/autoscale-overview.md)
* [Algemene statistieken voor Azure Monitor automatisch schalen](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Best practices voor automatische schaalaanpassing in Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Automatische schaalacties gebruiken om meldingen van e-mail en webhook te verzenden](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST-API automatisch schalen](https://msdn.microsoft.com/library/dn931953.aspx)

