---
title: Geavanceerde autoschaal met Azure Virtual Machines
description: Maakt gebruik van Resource Manager en VM-schaalsets met meerdere regels en profielen die url's met webhook-url's met schaalacties verzenden.
ms.topic: conceptual
ms.date: 02/22/2016
ms.subservice: autoscale
ms.openlocfilehash: e22806ff94ce2eb830bb6918bfc7f80e5ad3ba0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364217"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Geavanceerde automatische schaalconfiguratie met Resource Manager-sjablonen voor VM-schaalsets
U in- en uitschalen in virtuele machineschaalsets op basis van prestatiemetrische drempels, een terugkerend schema of op een bepaalde datum. U ook e-mail- en webhookmeldingen configureren voor schaalacties. Deze walkthrough toont een voorbeeld van het configureren van al deze objecten met behulp van een Resource Manager-sjabloon op een VM-schaalset.

> [!NOTE]
> Terwijl deze walkthrough de stappen voor VM-schaalsets uitlegt, is dezelfde informatie van toepassing op autoscaling [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)en API Management [services](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Voor een eenvoudige schaal-in/uit-instelling op een VM-schaalset op basis van een eenvoudige prestatiestatistiek zoals CPU, verwijzen naar de [Linux-](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) en [Windows-documenten](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Kennismaking
In deze walkthrough gebruiken we [Azure Resource Explorer](https://resources.azure.com/) om de instelling voor automatische schaal voor een schaalset te configureren en bij te werken. Azure Resource Explorer is een eenvoudige manier om Azure-bronnen te beheren via Resource Manager-sjablonen. Als u nieuw bent in azure resource explorer, leest u [deze inleiding](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Een nieuwe schaalset implementeren met een basisinstelling voor automatische waarden. In dit artikel wordt gebruik gemaakt van de azure quickstartgalerie, die een Windows-schaalset heeft met een sjabloon voor automatische waarden. Linux schaal sets werken op dezelfde manier.
2. Nadat de schaalset is gemaakt, navigeert u naar de schaalsetbron van Azure Resource Explorer. U ziet het volgende onder het knooppunt Microsoft.Insights.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    De sjabloonuitvoering heeft een standaardinstelling voor automatisch schalen gemaakt met de naam **'autoscalewad'.** Aan de rechterkant u de volledige definitie van deze instelling voor automatische waarden bekijken. In dit geval wordt de standaardinstelling voor automatisch schalen geleverd met een op CPU% gebaseerde scale-out- en scale-in-regel.  

3. U nu meer profielen en regels toevoegen op basis van de planning of specifieke vereisten. We maken een autoscale-instelling met drie profielen. Als u inzicht wilt krijgen in profielen en regels in autoscale, controleert [u Aanbevolen procedures voor autoscale](autoscale-best-practices.md).  

    | Profielen & regels | Beschrijving |
    |--- | --- |
    | **Profiel** |**Op basis van prestaties/statistiek** |
    | Regel |Aantal berichten in servicebus> x |
    | Regel |Aantal berichten voor servicebus< y |
    | Regel |CPU% > n |
    | Regel |CPU% < p |
    | **Profiel** |**Doordeweekse ochtenduren (geen regels)** |
    | **Profiel** |**Productstartdag (geen regels)** |

4. Hier is een hypothetisch schaling scenario dat we gebruiken voor deze walk-through.

   * **Load based** - Ik wil graag uitschalen of inop basis van de belasting van mijn toepassing gehost op mijn schaal set.*
   * **Grootte van de berichtenwachtrij** - Ik gebruik een servicebuswachtrij voor de binnenkomende berichten naar mijn toepassing. Ik gebruik het aantal berichten in de wachtrij en cpu% en configureer een standaardprofiel om een schaalactie te activeren als een van de berichten of de CPU de drempel bereikt.\*
   * **Tijd van week en dag** - Ik wil een wekelijks terugkerend 'tijd van de dag' gebaseerd profiel genaamd 'Weekday Morning Hours'. Op basis van historische gegevens, ik weet dat het beter is om een bepaald aantal VM-exemplaren om de belasting van mijn toepassing te behandelen gedurende deze tijd.\*
   * **Speciale data** - Ik heb een 'Product Launch Day' profiel toegevoegd. Ik plan vooruit voor specifieke data, zodat mijn aanvraag is klaar om de belasting te behandelen als gevolg marketing aankondigingen en wanneer we een nieuw product in de toepassing.\*
   * *De laatste twee profielen kunnen ook andere prestatiemetrische regels binnen hen hebben. In dit geval heb ik besloten om er geen te hebben en in plaats daarvan te vertrouwen op de standaard prestatiemetrische regels. Regels zijn optioneel voor de terugkerende en op datum gebaseerde profielen.*

     De prioritering van de profielen en regels van de autoscale-engine wordt ook vastgelegd in het best practices-artikel voor [automatische schaling.](autoscale-best-practices.md)
     Raadpleeg [Algemene statistieken voor Autoscale voor](autoscale-common-metrics.md) een lijst met algemene statistieken voor autoscale

5. Zorg ervoor dat u de **lees-/schrijfmodus** in Resource Explorer gebruikt

    ![Autoscalewad, standaardinstelling voor automatisch schalen](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klik op Bewerken. **Vervang** het element 'profielen' in de instelling voor automatische schaal door de volgende configuratie:

    ![Profielen](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
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
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
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
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Zie [Autoscale REST API-documentatie](https://msdn.microsoft.com/library/azure/dn931928.aspx)voor ondersteunde velden en hun waarden. Nu bevat uw instelling voor automatisch schalen de drie eerder uiteengezette profielen.

7. Tot slot, kijk op de autoscale **melding** sectie. Met meldingen voor automatisch schalen u drie dingen doen wanneer een scale-out of in actie wordt geactiveerd.
   - De beheerders en medebeheerders op de hoogte stellen van uw abonnement
   - Een set gebruikers e-mailen
   - Activeer een webhook call. Wanneer deze webhook wordt ontslagen, verzendt deze webhook metagegevens over de voorwaarde voor automatisch schalen en de bron voor de schaalset. Zie [Webhook-&-e-mailmeldingen configureren voor automatisch schalen voor](autoscale-webhook-email.md)meer informatie over de payload van webhook-webhook configureren.

   Het volgende toevoegen aan de instelling Autoscale ter vervanging van het **meldingselement** waarvan de waarde null is

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Druk op de knop **Plaatsen** in Resource Explorer om de instelling voor automatisch schalen bij te werken.

U hebt een instelling voor automatische schaal bijgewerkt op een VM-schaalset met meerdere schaalprofielen en schaalmeldingen.

## <a name="next-steps"></a>Volgende stappen
Gebruik deze koppelingen voor meer informatie over automatisch schalen.

[TroubleShoot Autoscale met virtuele machineschaalsets](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Algemene statistieken voor automatisch schalen](autoscale-common-metrics.md)

[Aanbevolen procedures voor Azure Autoscale](autoscale-best-practices.md)

[Autoscale beheren met PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Automatisch schalen beheren met CLI](cli-samples.md#autoscale)

[Webhook-& e-mailmeldingen configureren voor automatisch schalen](autoscale-webhook-email.md)

[Verwijzing naar microsoft.Insights/sjablooninstellingen](/azure/templates/microsoft.insights/autoscalesettings) voor automatisch schalen
