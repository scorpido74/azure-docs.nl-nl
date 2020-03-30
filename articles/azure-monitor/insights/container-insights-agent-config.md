---
title: Azure Monitor configureren voor het verzamelen van containersagentgegevens | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de Azure Monitor voor containersagent configureren om de logboekverzameling voor stdout/stderr en omgevingsvariabelen te beheren.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933028"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Agentgegevensverzameling configureren voor Azure Monitor voor containers

Azure Monitor voor containers verzamelt stdout-, stderr- en omgevingsvariabelen van containerworkloads die zijn geïmplementeerd in beheerde Kubernetes-clusters van de containeragent. U instellingen voor het verzamelen van agentgegevens configureren door een aangepaste Kubernetes ConfigMaps te maken om deze ervaring te beheren. 

In dit artikel wordt uitgelegd hoe u ConfigMap maakt en gegevensverzameling configureert op basis van uw vereisten.

>[!NOTE]
>Voor Azure Red Hat OpenShift wordt een sjabloon ConfigMap-bestand gemaakt in de naamruimte *openshift-azure-logging.* 
>

## <a name="configmap-file-settings-overview"></a>Overzicht van ConfigMap-bestandsinstellingen

Er is een sjabloon ConfigMap-bestand waarmee u het eenvoudig bewerken met uw aanpassingen zonder dat u het helemaal opnieuw hoeft te maken. Voordat u begint, moet u de Kubernetes-documentatie over [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) bekijken en vertrouwd raken met het maken, configureren en implementeren van ConfigMaps. Hiermee u stderr en stdout filteren per naamruimte of over het hele cluster, en omgevingsvariabelen voor elke container die wordt uitgevoerd op alle pods/knooppunten in het cluster.

>[!IMPORTANT]
>De minimale agentversie die wordt ondersteund voor het verzamelen van stdout-, stderr- en omgevingsvariabelen uit containerworkloads, wordt geactiveerd op 06142019 of hoger. Als u de versie van uw agent wilt verifiëren, selecteert u op het tabblad **Knooppunt** een knooppunt en in het eigenschappenvenster de waarde van de eigenschap **Agent Image Tag.** Voor meer informatie over de agentversies en wat er in elke release is opgenomen, raadpleegt [u de releasenotes van de agent.](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)

### <a name="data-collection-settings"></a>Instellingen voor gegevensverzameling

Hieronder volgen de instellingen die kunnen worden geconfigureerd om het verzamelen van gegevens te beheren.

|Sleutel |Gegevenstype |Waarde |Beschrijving |
|----|----------|------|------------|
|`schema-version` |Tekenreeks (hoofdlettergevoelig) |v1 |Dit is de schemaversie die door de agent wordt gebruikt bij het ontwijs maken van deze ConfigMap. Momenteel ondersteunde schema-versie is v1. Het wijzigen van deze waarde wordt niet ondersteund en wordt afgewezen wanneer ConfigMap wordt geëvalueerd.|
|`config-version` |Tekenreeks | | Ondersteunt de mogelijkheid om de versie van dit config-bestand bij te houden in uw broncontrolesysteem/repository. Maximaal toegestane tekens zijn 10 en alle andere tekens worden afgekapt. |
|`[log_collection_settings.stdout] enabled =` |Booleaans | waar of onwaar | Hiermee wordt geregeld of het verzamelen van soeoutcontainerlogboeken is ingeschakeld. Wanneer ingesteld `true` op en geen naamruimten zijn uitgesloten`log_collection_settings.stdout.exclude_namespaces` voor stdout log verzameling (instelling hieronder), stdout logs zullen worden verzameld uit alle containers over alle pods / knooppunten in het cluster. Als deze niet is opgegeven in `enabled = true`ConfigMaps, is de standaardwaarde . |
|`[log_collection_settings.stdout] exclude_namespaces =`|Tekenreeks | Door komma's gescheiden array |Array van Kubernetes naamruimten waarvoor stdout logs niet worden verzameld. Deze instelling is `log_collection_settings.stdout.enabled` alleen effectief `true`als deze is ingesteld op . Als deze niet is opgegeven in `exclude_namespaces = ["kube-system"]`ConfigMap, is de standaardwaarde .|
|`[log_collection_settings.stderr] enabled =` |Booleaans | waar of onwaar |Hiermee wordt geregeld of het verzamelen van stderr-containerlogboeken is ingeschakeld. Wanneer ingesteld `true` op en geen naamruimten zijn uitgesloten`log_collection_settings.stderr.exclude_namespaces` voor stdout logboekverzameling (instelling), stderr logs zal worden verzameld uit alle containers over alle pods / knooppunten in het cluster. Als deze niet is opgegeven in `enabled = true`ConfigMaps, is de standaardwaarde . |
|`[log_collection_settings.stderr] exclude_namespaces =` |Tekenreeks |Door komma's gescheiden array |Array van Kubernetes naamruimten waarvoor stderr logs niet worden verzameld. Deze instelling is `log_collection_settings.stdout.enabled` alleen effectief `true`als deze is ingesteld op . Als deze niet is opgegeven in `exclude_namespaces = ["kube-system"]`ConfigMap, is de standaardwaarde . |
| `[log_collection_settings.env_var] enabled =` |Booleaans | waar of onwaar | Met deze instelling regelt de omgevingsvariabele verzameling voor alle `enabled = true` pods/knooppunten in het cluster en standaard wordt ingesteld wanneer deze niet is opgegeven in ConfigMaps. Als verzameling omgevingsvariabelen wereldwijd is ingeschakeld, u deze voor een `AZMON_COLLECT_ENV` specifieke container uitschakelen door de omgevingsvariabele in te stellen op **False** met een Dockerfile-instelling of in het [configuratiebestand voor de Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) onder de **sectie env:** Als het verzamelen van omgevingsvariabelen wereldwijd is uitgeschakeld, u verzameling voor een specifieke container niet inschakelen (dat wil zeggen, de enige overschrijving die op containerniveau kan worden toegepast, is het uitschakelen van verzameling wanneer deze al wereldwijd is ingeschakeld.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Booleaans | waar of onwaar | Met deze instelling regelt containerlogboekverrijking de eigenschapswaarden Naam en afbeelding voor elke logboekrecord die naar de containerlogtabel is geschreven voor alle containerlogboeken in het cluster. Dit is `enabled = false` standaard ingesteld op wanneer deze niet is opgegeven in ConfigMap. |

ConfigMaps is een globale lijst en er kan slechts één ConfigMap op de agent worden toegepast. U geen andere ConfigMaps hebben die de collecties overrulen.

## <a name="configure-and-deploy-configmaps"></a>ConfigMaps configureren en implementeren

Voer de volgende stappen uit om uw ConfigMap-configuratiebestand te configureren en te implementeren in uw cluster.

1. [Download](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) de template ConfigMap yaml bestand en sla het op als container-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Deze stap is niet vereist bij het werken met Azure Red Hat OpenShift, omdat de ConfigMap-sjabloon al op het cluster bestaat.

2. Bewerk het ConfigMap yaml-bestand met uw aanpassingen om stdout-, stderr- en/of omgevingsvariabelen te verzamelen. Als u het ConfigMap yaml-bestand voor Azure Red Hat OpenShift bewerkt, voert u eerst de opdracht `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` uit om het bestand in een teksteditor te openen.

    - Als u specifieke naamruimten voor stdoutlogboekverzameling wilt uitsluiten, configureert u de sleutel/waarde met het volgende voorbeeld: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Als u de verzameling omgevingsvariabele voor een `[log_collection_settings.env_var] enabled = true` specifieke container wilt uitschakelen, stelt u de sleutel/waarde in om variabele verzameling wereldwijd in te schakelen en volgt u de stappen [hier](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) om de configuratie voor de specifieke container te voltooien.
    
    - Als u clusterbrede stderr-logboekverzamelingwilt uitschakelen, configureert `[log_collection_settings.stderr] enabled = false`u de sleutel/waarde met het volgende voorbeeld: .

3. Voor andere clusters dan Azure Red Hat OpenShift maakt u ConfigMap door de volgende kubectl-opdracht uit te voeren: `kubectl apply -f <configmap_yaml_file.yaml>` op andere clusters dan Azure Red Hat OpenShift. 
    
    Bijvoorbeeld: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Voor Azure Red Hat OpenShift slaat u uw wijzigingen op in de editor.

Het kan enkele minuten duren voordat de configuratiewijziging is voltooid voordat deze van kracht wordt en alle omsagentpods in het cluster opnieuw worden opgestart. De herstart is een rollende herstart voor alle omsagent pods, niet allemaal opnieuw op hetzelfde moment. Wanneer de herstart is voltooid, wordt een bericht weergegeven dat vergelijkbaar is `configmap "container-azm-ms-agentconfig" created`met het volgende en het resultaat bevat: .

## <a name="verify-configuration"></a>Configuratie verifiëren

Als u wilt controleren of de configuratie is toegepast op een ander cluster dan Azure Red `kubectl logs omsagent-fdf58 -n=kube-system`Hat OpenShift, gebruikt u de volgende opdracht om de logboeken van een agentpod te controleren: . Als er configuratiefouten zijn van de omsagentpods, worden in de uitvoer fouten weergegeven die vergelijkbaar zijn met de volgende:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fouten in verband met het toepassen van configuratiewijzigingen zijn ook beschikbaar voor controle. De volgende opties zijn beschikbaar om aanvullende probleemoplossing voor configuratiewijzigingen uit te voeren:

- Vanuit een agentpod logboeken met dezelfde `kubectl logs` opdracht. 

    >[!NOTE]
    >Deze opdracht is niet van toepassing op azure Red Hat OpenShift-cluster.
    > 

- Van Live logs. Live logs tonen fouten die vergelijkbaar zijn met de volgende:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Ga naar de tabel **KubeMonAgentEvents** in uw log analytics-werkruimte. Gegevens worden elk uur verzonden met de ernst *van de fout* voor configuratiefouten. Als er geen fouten zijn, bevat de vermelding in de tabel gegevens met *ernstinfo,* die geen fouten rapporteert. De eigenschap **Tags** bevat meer informatie over de pod- en container-ID waarop de fout is opgetreden en ook de eerste gebeurtenis, het laatste voorval en het aantal in het laatste uur.

- Controleer met Azure Red Hat OpenShift de omsagent-logboeken door in de **containerlog-tabel** te zoeken om te controleren of logboekverzameling van openshift-azure-logging is ingeschakeld.

Nadat u de fout(en) in ConfigMap hebt gecorrigeerd op andere clusters dan Azure Red Hat OpenShift, slaat `kubectl apply -f <configmap_yaml_file.yaml`u het yaml-bestand op en past u de bijgewerkte ConfigMaps toe door de opdracht uit te voeren: . Bewerk en sla de bijgewerkte ConfigMaps voor Azure Red Hat OpenShift op door de opdracht uit te voeren:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Bijgewerkte ConfigMap toepassen

Als u al een ConfigMap hebt geïmplementeerd op andere clusters dan Azure Red Hat OpenShift en u deze wilt bijwerken met een nieuwere configuratie, u `kubectl apply -f <configmap_yaml_file.yaml`het ConfigMap-bestand dat u eerder hebt gebruikt bewerken en vervolgens toepassen met dezelfde opdracht als voorheen,. Bewerk en sla de bijgewerkte ConfigMaps voor Azure Red Hat OpenShift op door de opdracht uit te voeren:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Het kan enkele minuten duren voordat de configuratiewijziging is voltooid voordat deze van kracht wordt en alle omsagentpods in het cluster opnieuw worden opgestart. De herstart is een rollende herstart voor alle omsagent pods, niet allemaal opnieuw op hetzelfde moment. Wanneer de herstart is voltooid, wordt een bericht weergegeven dat vergelijkbaar is `configmap "container-azm-ms-agentconfig" updated`met het volgende en het resultaat bevat: .

## <a name="verifying-schema-version"></a>Schemaversie verifiëren

Ondersteunde config-schemaversies zijn beschikbaar als podannotatie (schemaversies) op de omsagentpod. Je ze zien met de volgende opdracht kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

De uitvoer wordt weergegeven als het volgende met de annotatieschema-versies:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Volgende stappen

- Azure Monitor voor containers bevat geen vooraf gedefinieerde set waarschuwingen. Bekijk de [prestatiewaarschuwingen maken met Azure Monitor voor containers](container-insights-alerts.md) voor meer informatie over het maken van aanbevolen waarschuwingen voor een hoog CPU- en geheugengebruik ter ondersteuning van uw DevOps of operationele processen en procedures.

- Als monitoring is ingeschakeld om de status en het gebruik van resources van uw AKS- of hybride cluster en workloads die op deze pagina's worden uitgevoerd, te verzamelen, leest u hoe u Azure Monitor voor containers [gebruiken.](container-insights-analyze.md)

- Voorbeelden [van logboekquery's](container-insights-log-search.md#search-logs-to-analyze-data) weergeven om vooraf gedefinieerde query's en voorbeelden te bekijken die u wilt evalueren of aanpassen voor het waarschuwen, visualiseren of analyseren van uw clusters.
