---
title: Gebeurtenisaggregatie met Windows Azure Diagnostics
description: Meer informatie over het aggregeren en verzamelen van gebeurtenissen met BEHULP van WAD voor monitoring en diagnostiek van Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: b9a448ff41c66fa3a38c124f7acde062bacbe9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282495"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Aggregatie en verzameling van gebeurtenissen met Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Wanneer u een Azure Service Fabric-cluster uitvoert, is het een goed idee om de logboeken van alle knooppunten op een centrale locatie te verzamelen. Als u de logboeken op een centrale locatie hebt, u problemen in uw cluster of problemen in de toepassingen en services die in dat cluster worden uitgevoerd, analyseren en oplossen.

Een manier om logboeken te uploaden en te verzamelen, is door de WAD-extensie (Windows Azure Diagnostics) te gebruiken, die logboeken uploadt naar Azure Storage, en ook de mogelijkheid heeft om logboeken naar Azure Application Insights of Event Hubs te verzenden. U ook een extern proces gebruiken om de gebeurtenissen uit de opslag te lezen en deze in een analyseplatformproduct te plaatsen, zoals [Azure Monitor-logboeken](../log-analytics/log-analytics-service-fabric.md) of een andere log-parsing-oplossing.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
De volgende tools worden gebruikt in dit artikel:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager-sjabloon](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric-platformevenementen
Service Fabric stelt u voor met een paar [kant-en-klare logboekkanalen,](service-fabric-diagnostics-event-generation-infra.md)waarvan de volgende kanalen vooraf zijn geconfigureerd met de extensie om monitoring- en diagnostische gegevens naar een opslagtabel of elders te verzenden:
  * [Operationele gebeurtenissen:](service-fabric-diagnostics-event-generation-operational.md)operaties op een hoger niveau die het Service Fabric-platform uitvoert. Voorbeelden hiervan zijn het maken van applicaties en services, wijzigingen in knooppuntstatussen en upgrade-informatie. Deze worden uitgezonden als Event Tracing for Windows (ETW)-logboeken
  * [Betrouwbare acteurs programmeren modelevenementen](service-fabric-reliable-actors-diagnostics.md)
  * [Programmamodelgebeurtenissen voor betrouwbare services](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>De extensie Diagnostische gegevens implementeren via de portal
De eerste stap bij het verzamelen van logboeken is het implementeren van de extensie Diagnostische gegevens op de knooppunten voor virtuele machineschaalinstelin het cluster Servicefabric. De extensie Diagnostische gegevens verzamelt logboeken op elke virtuele machine en uploadt deze naar het opslagaccount dat u opgeeft. In de volgende stappen wordt beschreven hoe u dit uitvoeren voor nieuwe en bestaande clusters via de Azure-portal- en Azure Resource Manager-sjablonen.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>De diagnostische extensie implementeren als onderdeel van het maken van clusteren via Azure-portal
Vouw bij het maken van uw cluster in de clusterconfiguratiestap de optionele instellingen uit en zorg ervoor dat Diagnostics is ingesteld op **Aan** (de standaardinstelling).

![Azure Diagnostics-instellingen in de portal voor het maken van clusteren](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

We raden u ten zeerste aan de sjabloon te downloaden voordat u in de laatste stap **op Maken klikt.** Raadpleeg voor meer informatie [een cluster van servicefabric instellen met behulp van een Azure Resource Manager-sjabloon](service-fabric-cluster-creation-via-arm.md). U hebt de sjabloon nodig om wijzigingen aan te brengen op welke kanalen (hierboven vermeld) om gegevens uit te verzamelen.

![Clustersjabloon](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Nu u gebeurtenissen in Azure Storage aggregeren, stelt u [Azure Monitor-logboeken in](service-fabric-diagnostics-oms-setup.md) om inzichten te verkrijgen en deze op te vragen in de azure-logboekenportal

>[!NOTE]
>Er is momenteel geen manier om de gebeurtenissen die naar de tabellen worden verzonden te filteren of te verzorgen. Als u geen proces implementeert om gebeurtenissen uit de tabel te verwijderen, blijft de tabel groeien (de standaardlimiet is 50 GB). Instructies over hoe dit te veranderen zijn [verder hieronder in dit artikel](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Daarnaast is er een voorbeeld van een data grooming service draait in de [Watchdog monster,](https://github.com/Azure-Samples/service-fabric-watchdog-service)en het wordt aanbevolen dat u een schrijven voor jezelf ook, tenzij er een goede reden voor u om logs op te slaan buiten een 30 of 90 dagen tijdsbestek.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>De extensie Diagnostische gegevens implementeren via Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Een cluster maken met de diagnostische extensie
Als u een cluster wilt maken met Resource Manager, moet u de JSON voor diagnostische configuratie toevoegen aan de volledige sjabloon Resourcebeheer. We bieden een voorbeeld van vijf VM-clusterresourcebeheersjabloon met de configuratie Diagnostische gegevens die eraan zijn toegevoegd als onderdeel van onze resourcemanager-sjabloonvoorbeelden. U het op deze locatie zien in de azure-galerie Voorbeelden: [cluster met vijf clientsomimetuvoor met een voorbeeld van de sjabloon diagnostics Resource Manager](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Als u de instelling Diagnostische gegevens wilt weergeven in de sjabloon Resourcemanager, opent u het bestand azuredeploy.json en zoekt u naar **IaaSDiagnostics**. Als u een cluster wilt maken met deze sjabloon, selecteert u de knop **Implementeren naar Azure** die beschikbaar is op de vorige koppeling.

U ook het voorbeeld van Resource Manager downloaden, wijzigingen aanbrengen en een `New-AzResourceGroupDeployment` cluster maken met de gewijzigde sjabloon met behulp van de opdracht in een Azure PowerShell-venster. Zie de volgende code voor de parameters die u doorgeeft aan de opdracht. Zie het artikel [Een resourcegroep implementeren met de sjabloon Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)voor gedetailleerde informatie over het implementeren van een brongroep met PowerShell.

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>De diagnostische extensie toevoegen aan een bestaand cluster
Als u een bestaand cluster hebt dat geen Diagnose heeft geïmplementeerd, u dit toevoegen of bijwerken via de clustersjabloon. Wijzig de sjabloon Resourcemanager die wordt gebruikt om het bestaande cluster te maken of download de sjabloon van de portal zoals eerder beschreven. Wijzig het bestand template.json door de volgende taken uit te voeren:

Voeg een nieuwe opslagbron toe aan de sjabloon door toe te voegen aan de sectie resources.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Voeg vervolgens toe aan de sectie parameters net `supportLogStorageAccountName`na de definities van het opslagaccount, tussen . Vervang de naam van het tijdelijke *tekstopslagaccount* vervangen door de naam van het opslagaccount dat u wilt.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Werk vervolgens `VirtualMachineProfile` het gedeelte van het bestand template.json bij door de volgende code toe te voegen in de array extensies. Zorg ervoor dat u een komma toevoegt aan het begin of het einde, afhankelijk van waar deze is ingevoegd.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Nadat u het bestand template.json hebt gewijzigd zoals beschreven, publiceert u de sjabloon Resourcebeheer opnieuw. Als de sjabloon is geëxporteerd, publiceert het deploy.ps1-bestand de sjabloon opnieuw. Nadat u hebt geïmplementeerd, moet u ervoor zorgen dat **ProvisioningState** **is geslaagd.**

> [!TIP]
> Als u containers gaat implementeren op uw cluster, u WAD inschakelen om dockerstatistieken op te halen door dit toe te voegen aan uw **> diagnosticmonitorconfiguration** sectie.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Opslagquotum bijwerken

Aangezien de tabellen die door de extensie worden ingevuld, worden groter totdat het quotum is bereikt, u overwegen de quotumgrootte te verkleinen. De standaardwaarde is 50 GB en is `overallQuotaInMB` configureerbaar in de sjabloon onder het veld onder`DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Configuraties van logboekverzamelingen
Logboeken van extra kanalen zijn ook beschikbaar voor verzameling, hier zijn enkele van de meest voorkomende configuraties die u maken in de sjabloon voor clusters die in Azure worden uitgevoerd.

* Operationeel kanaal - Basis: Standaard ingeschakeld, bewerkingen op hoog niveau uitgevoerd door Service Fabric en het cluster, inclusief gebeurtenissen voor een knooppunt dat eraan komt, een nieuwe toepassing die wordt geïmplementeerd of een upgrade-rollback, enz. Raadpleeg [operationele kanaalgebeurtenissen](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational)voor een lijst met gebeurtenissen.
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Operationeel kanaal - Gedetailleerd: Dit omvat gezondheidsrapporten en beslissingen over het balanceren van de belasting, plus alles in het operationele basiskanaal. Deze gebeurtenissen worden gegenereerd door het systeem of uw code met behulp van de API's voor status- of belastingrapportage, zoals [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) of [ReportLoad.](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx) Als u deze gebeurtenissen wilt bekijken in de Diagnostische Logboeken van Visual Studio, voegt u 'Microsoft-ServiceFabric:4:0x40000000000000000000088' toe aan de lijst met ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Gegevens- en berichtenkanaal - Basis: kritieke logboeken en gebeurtenissen die worden gegenereerd in de berichten (momenteel alleen de ReverseProxy) en het gegevenspad, naast gedetailleerde logboeken van het operationele kanaal. Deze gebeurtenissen zijn fouten in de verwerking van aanvragen en andere kritieke problemen in de ReverseProxy, evenals verwerkte aanvragen. **Dit is onze aanbeveling voor uitgebreide houtkap.** Als u deze gebeurtenissen wilt bekijken in de Diagnostische Logboekviewer van Visual Studio, voegt u 'Microsoft-ServiceFabric:4:0x40000000000000000010' toe aan de lijst met ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Gegevens & Messaging Channel - Gedetailleerd: Verbose-kanaal dat alle niet-kritieke logboeken van gegevens en berichten in het cluster en het gedetailleerde operationele kanaal bevat. Raadpleeg de [handleiding voor reverse proxy-diagnose](service-fabric-reverse-proxy-diagnostics.md)voor gedetailleerde probleemoplossing voor alle omgekeerde proxygebeurtenissen.  Als u deze gebeurtenissen wilt bekijken in de diagnostische gebeurtenisviewer van Visual Studio, voegt u 'Microsoft-ServiceFabric:4:0x40000000000000000020' toe aan de lijst met ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Dit kanaal heeft een zeer hoog aantal gebeurtenissen, waardoor het verzamelen van gebeurtenissen uit dit gedetailleerde kanaal resulteert in een heleboel sporen die snel worden gegenereerd, en kan opslagcapaciteit verbruiken. Schakel dit alleen in als het absoluut noodzakelijk is.


Om de **Base Operational Channel** onze aanbeveling voor uitgebreide logging `EtwManifestProviderConfiguration` met `WadCfg` de minste hoeveelheid ruis, De in de van uw sjabloon zou er als volgt uitzien:

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Verzamelen van nieuwe EventSource-kanalen

Als u Diagnostische gegevens wilt bijwerken om logboeken te verzamelen van nieuwe EventSource-kanalen die een nieuwe toepassing vertegenwoordigen die u gaat implementeren, voert u dezelfde stappen uit als eerder beschreven voor de installatie van Diagnostics voor een bestaand cluster.

Werk `EtwEventSourceProviderConfiguration` de sectie in het bestand template.json bij om items toe te voegen `New-AzResourceGroupDeployment` voor de nieuwe EventSource-kanalen voordat u de configuratie-update toepast met de opdracht PowerShell. De naam van de gebeurtenisbron wordt gedefinieerd als onderdeel van uw code in het door Visual Studio gegenereerde ServiceEventSource.cs bestand.

Als uw gebeurtenisbron bijvoorbeeld Mijn-Eventsource wordt genoemd, voegt u de volgende code toe om de gebeurtenissen van Mijn-Eventsource in een tabel met de naam MyDestinationTableName te plaatsen.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Als u prestatiemeteritems of gebeurtenislogboeken wilt verzamelen, wijzigt u de sjabloon Resourcebeheer met behulp van de voorbeelden in [Een virtuele windows-machine maken met bewaking en diagnose met behulp van een sjabloon Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Publiceer vervolgens de sjabloon Resourcemanager opnieuw.

## <a name="collect-performance-counters"></a>Prestatiemeteritems verzamelen

Als u prestatiestatistieken uit uw cluster wilt verzamelen, voegt u de prestatiemeteritems toe aan uw "WadCfg > DiagnosticMonitorConfiguration" in de sjabloon Resourcebeheer voor uw cluster. Zie [Prestatiebewaking met WAD](service-fabric-diagnostics-perf-wad.md) voor `WadCfg` stappen bij het wijzigen van uw om specifieke prestatiemeteritems te verzamelen. Prestatiemeteritems voor [referentieservicefabricvoor](service-fabric-diagnostics-event-generation-perf.md) een lijst met prestatiemeteritems die we aanbevelen te verzamelen.
  
Als u een sink van Application Insights gebruikt, zoals beschreven in de sectie hieronder, en wilt dat deze statistieken worden weergegeven in Application Insights, moet u de naam van de gootsteen toevoegen aan de sectie 'sinks', zoals hierboven weergegeven. Hiermee worden automatisch de prestatiemeteritems die afzonderlijk zijn geconfigureerd, verzonden naar uw Application Insights-bron.


## <a name="send-logs-to-application-insights"></a>Logboeken verzenden naar toepassingsinzichten

### <a name="configuring-application-insights-with-wad"></a>Toepassingsinzichten configureren met WAD

>[!NOTE]
>Dit is op dit moment alleen van toepassing op Windows-clusters.

Er zijn twee primaire manieren om gegevens van WAD naar Azure Application Insights te verzenden, wat wordt bereikt door een Application Insights-sink toe te voegen aan de WAD-configuratie, via de Azure-portal of via een Azure Resource Manager-sjabloon.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Een instrumentatiesleutel voor toepassingsinzichten toevoegen bij het maken van een cluster in Azure-portal

![Een AIKey toevoegen](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Bij het maken van een cluster wordt een optioneel veld weergegeven om een instrumentatietoets voor toepassingsinzichten in te voeren wanneer u een cluster maakt. Als u hier de toepassingsinformatiesleutel plakt, wordt de sink Application Insights automatisch voor u geconfigureerd in de sjabloon Resourcebeheer die wordt gebruikt om uw cluster te implementeren.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>De sink voor toepassingsinzichten toevoegen aan de sjabloon Resourcemanager

Voeg in de sjabloon "WadCfg" van de resourcemanager een 'Sink' toe door de volgende twee wijzigingen op te nemen:

1. Voeg de gootsteenconfiguratie direct toe `DiagnosticMonitorConfiguration` nadat de declareren is voltooid:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Neem de gootsteen `DiagnosticMonitorConfiguration` op door de `DiagnosticMonitorConfiguration` volgende `WadCfg` regel toe `EtwProviders` te voegen aan de (vlak voordat de worden gedeclareerd):

    ```json
    "sinks": "applicationInsights"
    ```

In beide voorgaande codefragmenten werd de naam "applicationInsights" gebruikt om de gootsteen te beschrijven. Dit is geen vereiste en zolang de naam van de gootsteen is opgenomen in wastafels, u de naam instellen op een string.

Momenteel worden logboeken uit het cluster weergegeven als **traces** in de logboekviewer van Application Insights. Aangezien de meeste van de sporen afkomstig van het platform zijn van niveau Informatief, u ook overwegen het veranderen van de gootsteen configuratie om alleen logs van het type Waarschuwing of Fout te verzenden. Dit kan worden gedaan door het toevoegen van "Kanalen" aan uw gootsteen, zoals aangetoond in [dit artikel](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Als u een onjuiste Application Insights-sleutel gebruikt in de portal of in uw resourcemanagersjabloon, moet u de sleutel handmatig wijzigen en het cluster bijwerken/ opnieuw implementeren.

## <a name="next-steps"></a>Volgende stappen

Zodra u Azure-diagnose correct hebt geconfigureerd, ziet u gegevens in uw opslagtabellen in de etw- en EventSource-logboeken. Als u ervoor kiest azure monitorlogboeken, Kibana of een ander gegevensanalyse- en visualisatieplatform te gebruiken dat niet rechtstreeks is geconfigureerd in de resourcemanagersjabloon, moet u het platform van uw keuze instellen dat u wilt lezen in de gegevens uit deze opslagtabellen. Dit doen voor Azure Monitor-logboeken is relatief triviaal en wordt uitgelegd in [gebeurtenis- en logboekanalyse.](service-fabric-diagnostics-event-analysis-oms.md) Application Insights is een beetje een speciaal geval in deze zin, omdat het kan worden geconfigureerd als onderdeel van de configuratie van de Diagnostische extensie, dus raadpleeg het [juiste artikel](service-fabric-diagnostics-event-analysis-appinsights.md) als u ervoor kiest om AI te gebruiken.

>[!NOTE]
>Er is momenteel geen manier om de gebeurtenissen die naar de tabel worden verzonden te filteren of te verzorgen. Als u geen proces implementeert om gebeurtenissen uit de tabel te verwijderen, blijft de tabel groeien. Momenteel is er een voorbeeld van een data grooming service draait in de [Watchdog monster,](https://github.com/Azure-Samples/service-fabric-watchdog-service)en het wordt aanbevolen dat u een schrijven voor jezelf ook, tenzij er een goede reden voor u om logs op te slaan buiten een 30 of 90 dagen tijdsbestek.

* [Meer informatie over het verzamelen van prestatiemeteritems of logboeken met de extensie Diagnostische gegevens](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gebeurtenisanalyse en -visualisatie met toepassingsinzichten](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Gebeurtenisanalyse en -visualisatie met Azure Monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md)
* [Gebeurtenisanalyse en -visualisatie met toepassingsinzichten](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Gebeurtenisanalyse en -visualisatie met Azure Monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md)
