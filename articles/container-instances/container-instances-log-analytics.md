---
title: Bronlogboeken verzamelen & analyseren
description: Informatie over het verzenden van bronlogboeken en gebeurtenisgegevens van containergroepen in Azure Container Instances naar Azure Monitor-logboeken
ms.topic: article
ms.date: 01/08/2020
ms.author: danlep
ms.openlocfilehash: 304e98fff386911b878877d2f03d489d0eef5dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770540"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Logboekregistratie van containergroepen en instance met Azure Monitor-logboeken

Log Analytics-werkruimten bieden een centrale locatie voor het opslaan en opvragen van logboekgegevens, niet alleen vanuit Azure-bronnen, maar ook on-premises resources en resources in andere clouds. Azure Container Instances bevat ingebouwde ondersteuning voor het verzenden van logboeken en gebeurtenisgegevens naar Azure Monitor-logboeken.

Als u logboek- en gebeurtenisgegevens voor containergroepen naar Azure Monitor-logboeken wilt verzenden, geeft u een bestaande log Analytics-werkruimte-id en werkruimtesleutel op bij het maken van een containergroep. In de volgende secties wordt beschreven hoe u een containergroep met registratieingeschakeld maakt en hoe logboeken worden opgevraagd.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Momenteel u alleen gebeurtenisgegevens van Linux-containerinstances naar Log Analytics verzenden.

## <a name="prerequisites"></a>Vereisten

Voor het inschakelen van logboekregistratie voor uw containerexemplaren hebt u het volgende nodig:

* [Log Analytics-werkruimte](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (of [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Log Analytics-referenties verkrijgen

Azure Container Instances heeft toestemming nodig om gegevens te verzenden naar uw Log Analytics-werkruimte. U kunt deze machtiging verlenen en de mogelijkheid tot logboekregistratie inschakelen als u de Log Analytics-werkruimte-id en een van de sleutels ervan opgeeft (primaire of secundaire) op het moment dat u de containergroep maakt.

De Log Analytics-werkruimte-id en de primaire sleutel verkrijgt u als volgt:

1. Navigeer in Azure Portal naar uw Log Analytics-werkruimte.
1. Selecteer Geavanceerde **instellingen** **onder Instellingen**
1. Selecteer**Windows-servers van** **Verbonden bronnen** > (of **Linux-servers**- de ID en de toetsen zijn voor beide hetzelfde)
1. Noteer het volgende:
   * **WERKRUIMTE-ID**
   * **PRIMAIRE SLEUTEL**

## <a name="create-container-group"></a>Containergroep maken

Nu dat u de Log Analytics-werkruimte-id en de primaire sleutel hebt, kunt u een containergroep maken die geschikt is voor logboekregistratie.

In de volgende voorbeelden worden twee manieren gedemonstreerd om een containergroep te maken die bestaat uit één [vloeiende][fluentd] container: Azure CLI en Azure CLI met een YAML-sjabloon. De vloeiend gemaakte container produceert verschillende uitvoerlijnen in de standaardconfiguratie. Omdat deze uitvoer naar de Log Analytics-werkruimte wordt verzonden, is deze heel geschikt is om te illustreren hoe logboeken kunnen worden weergegeven en hoe er query's op kunnen worden uitgevoerd.

### <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

Als u Azure CLI wilt gebruiken om te implementeren, moet u de parameters `--log-analytics-workspace` en `--log-analytics-workspace-key` opgeven in de opdracht [az container create][az-container-create]. Vervang de twee werkruimtewaarden door de waarden die u hebt verkregen in de vorige stap (en werk de naam van de resourcegroep bij) voordat u de volgende opdracht gaat uitvoeren.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Implementeren met YAML

Gebruik deze methode als u liever containergroepen met YAML wilt implementeren. Met de volgende YAML wordt een containergroep met een enkele container gedefinieerd. Kopieer de YAML naar een nieuw bestand en vervang `LOG_ANALYTICS_WORKSPACE_ID` en `LOG_ANALYTICS_WORKSPACE_KEY` door de waarden die u hebt verkregen in de vorige stap. Sla het bestand op als **deploy-aci.yaml**.

```yaml
apiVersion: 2018-10-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Voer vervolgens de volgende opdracht uit om de containergroep te implementeren. Vervang `myResourceGroup` een resourcegroep in uw abonnement (of maak eerst een resourcegroep met de naam 'myResourceGroup'):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Kort nadat u de opdracht hebt opgegeven, zou u een reactie van Azure met implementatiedetails moeten ontvangen.

## <a name="view-logs"></a>Logboeken weergeven

Nadat u de containergroep hebt geïmplementeerd, kan het enkele minuten (wel tien minuten) duren voor de eerste logboekvermeldingen worden weergegeven in Azure Portal. Ga als u de logboeken `ContainerInstanceLog_CL` van de containergroep in de tabel weergeven:

1. Navigeer in Azure Portal naar uw Log Analytics-werkruimte.
1. Selecteer **Logboeken** onder **Algemeen**  
1. Typ de volgende query:`ContainerInstanceLog_CL | limit 50`
1. Selecteer **Uitvoeren**

U ziet verschillende resultaten die door de query worden weergegeven. Als u eerst geen resultaten ziet, wacht u een paar minuten en selecteert u de knop **Uitvoeren** om de query opnieuw uit te voeren. Logberichten worden standaard weergegeven in **tabelindeling.** U kunt vervolgens een rij uitbreiden om de inhoud van een afzonderlijke logboekvermelding te zien.

![Resultaten van zoekopdrachten in logboeken in Azure Portal][log-search-01]

## <a name="view-events"></a>Gebeurtenissen weergeven

U ook gebeurtenissen voor containerexemplaren weergeven in de Azure-portal. Gebeurtenissen omvatten de tijd waarop de instantie wordt gemaakt en wanneer deze wordt gestart. Ga als bedoeld als `ContainerEvent_CL` het gaat om de gebeurtenisgegevens in de tabel weer te geven:

1. Navigeer in Azure Portal naar uw Log Analytics-werkruimte.
1. Selecteer **Logboeken** onder **Algemeen**  
1. Typ de volgende query:`ContainerEvent_CL | limit 50`
1. Selecteer **Uitvoeren**

U ziet verschillende resultaten die door de query worden weergegeven. Als u eerst geen resultaten ziet, wacht u een paar minuten en selecteert u de knop **Uitvoeren** om de query opnieuw uit te voeren. Standaard worden items weergegeven in **tabelindeling.** U vervolgens een rij uitbreiden om de inhoud van een afzonderlijke vermelding te bekijken.

![Zoekresultaten voor gebeurtenissen in de Azure-portal][log-search-02]

## <a name="query-container-logs"></a>Query's uitvoeren op containerlogboeken

Azure Monitor-logboeken bevat een uitgebreide [querytaal][query_lang] voor het ophalen van gegevens uit mogelijk duizenden regels aan logboekuitvoer.

De basisstructuur van een query is de `ContainerInstanceLog_CL` brontabel (in dit artikel of `ContainerEvent_CL`)`|`gevolgd door een reeks operatoren gescheiden door het pijpteken ( ). U kunt verschillende operatoren aan elkaar koppelen om de resultaten te verfijnen en geavanceerde functies uit te voeren.

Als u voorbeeldqueryresultaten wilt weergeven, plakt u de volgende query in het querytekstvak en selecteert u de knop **Uitvoeren** om de query uit te voeren. Deze query geeft alle logboekvermeldingen weer waarvan het veld 'Bericht' het woord 'waarschuwen' bevat:

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Complexere query's worden ook ondersteund. Met deze query worden bijvoorbeeld de logboekvermeldingen voor de containergroep 'mycontainergroup001' weergegeven die het afgelopen uur zijn gegenereerd:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Volgende stappen

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Voor meer informatie over het uitvoeren van query's op logboeken en het configureren van waarschuwingen in Azure Monitor-logboeken, zie:

* [Zoekopdrachten in Azure Monitor-logboeken begrijpen](../log-analytics/log-analytics-log-search.md)
* [Consistente waarschuwingen in Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>CPU en geheugen bewaken

Voor informatie over het bewaken van CPU- en geheugenresources van containerinstanties, zie:

* [Containerresources in Azure Container Instances bewaken](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create