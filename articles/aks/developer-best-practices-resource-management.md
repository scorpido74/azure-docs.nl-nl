---
title: Aanbevolen procedures voor ontwikkelaars - Resourcebeheer in Azure Kubernetes Services (AKS)
description: Lees de aanbevolen procedures voor toepassingsontwikkelaars voor resourcebeheer in Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 66676c90e73d1886c86d8afda8cbbecce239a005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259524"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor toepassingsontwikkelaars om resources te beheren in Azure Kubernetes Service (AKS)

Terwijl u toepassingen ontwikkelt en uitvoert in Azure Kubernetes Service (AKS), zijn er een aantal belangrijke gebieden om rekening mee te houden. Hoe u uw toepassingsimplementaties beheert, kan een negatieve invloed hebben op de ervaring van de eindgebruiker van services die u aanbiedt. Om u te helpen slagen, houd in gedachten een aantal best practices die u volgen als u toepassingen in AKS ontwikkelt en uitvoert.

Dit artikel over aanbevolen procedures richt zich op het uitvoeren van uw cluster en workloads vanuit het perspectief van toepassingsontwikkelaars. Zie [Best practices voor clusteroperatoren voor isolatie en resourcebeheer in Azure Kubernetes Service (AKS)][operator-best-practices-isolation]voor informatie over aanbevolen procedures voor beheer. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Wat zijn pod resource aanvragen en limieten
> * Manieren om applicaties te ontwikkelen en te implementeren met Dev Spaces en Visual Studio Code
> * De `kube-advisor` tool gebruiken om te controleren op problemen met implementaties

## <a name="define-pod-resource-requests-and-limits"></a>Podresourceaanvragen en -limieten definiëren

**Richtlijnen voor aanbevolen procedures** - Stel podaanvragen en -limieten in op alle pods in uw YAML-manifesten. Als het AKS-cluster *resourcequota*gebruikt, kan uw implementatie worden geweigerd als u deze waarden niet definieert.

Een primaire manier om de rekenresources binnen een AKS-cluster te beheren, is door podaanvragen en -limieten te gebruiken. Met deze aanvragen en limieten kan de Kubernetes-planner weten welke rekenbronnen een pod moet worden toegewezen.

* **Pod CPU/Geheugen aanvragen** definiëren een ingestelde hoeveelheid CPU en geheugen die de pod nodig heeft op een regelmatige basis.
    * Wanneer de Kubernetes-planner een pod op een knooppunt probeert te plaatsen, worden de podaanvragen gebruikt om te bepalen welk knooppunt voldoende resources beschikbaar heeft voor het plannen.
    * Als u geen podaanvraag instelt, wordt deze standaard ingesteld op de gedefinieerde limiet.
    * Het is erg belangrijk om de prestaties van uw toepassing te controleren om deze aanvragen aan te passen. Als er onvoldoende aanvragen worden gedaan, kan uw toepassing een verminderde prestaties ontvangen als gevolg van het overplannen van een knooppunt. Als aanvragen worden overschat, kan het zijn dat uw toepassing meer moeite heeft om in gepland te worden.
* **Pod CPU/Geheugen limieten** zijn de maximale hoeveelheid CPU en geheugen die een pod kan gebruiken. Deze limieten helpen bepalen welke pods moeten worden gedood in het geval van instabiliteit van het knooppunt als gevolg van onvoldoende middelen. Zonder de juiste limieten worden pods gedood totdat de druk van de middelen wordt opgeheven.
    * Podlimieten helpen bepalen wanneer een pod de controle over het verbruik van resources heeft verloren. Wanneer een limiet wordt overschreden, wordt de pod geprioriteerd voor het doden om de status van het knooppunt te behouden en de impact op pods die het knooppunt delen te minimaliseren.
    * Als u een podlimiet niet instelt, wordt deze standaard ingesteld op de hoogste beschikbare waarde op een bepaald knooppunt.
    * Stel geen podlimiet in die hoger is dan uw knooppunten kunnen ondersteunen. Elk AKS-knooppunt reserveert een bepaalde hoeveelheid CPU en geheugen voor de belangrijkste Kubernetes-componenten. Uw toepassing kan proberen te veel resources op het knooppunt te verbruiken om andere pods met succes uit te voeren.
    * Nogmaals, het is erg belangrijk om de prestaties van uw toepassing op verschillende tijdstippen gedurende de dag of week te controleren. Bepaal wanneer de piekvraag is en stem de podlimieten af op de resources die nodig zijn om aan de maximale behoeften van de toepassing te voldoen.

In uw pod specificaties, het is **best practice en zeer belangrijk** om deze verzoeken en limieten te definiëren op basis van de bovenstaande informatie. Als u deze waarden niet opneemt, kan de Kubernetes-planner geen rekening houden met de resources die uw toepassingen nodig hebben om te helpen bij het plannen van beslissingen.

Als de planner een pod op een knooppunt plaatst met onvoldoende resources, worden de toepassingsprestaties afgebroken. Het wordt ten zeerste aanbevolen voor clusterbeheerders om *resourcequota* in te stellen op een naamruimte waarvoor u resourceaanvragen en -limieten moet instellen. Zie [resourcequota voor AKS-clusters voor][resource-quotas]meer informatie .

Wanneer u een CPU-aanvraag of -limiet definieert, wordt de waarde gemeten in CPU-eenheden. 
* *1.0* CPU komt overeen met een onderliggende virtuele CPU-kern op het knooppunt. 
* Dezelfde meting wordt gebruikt voor GPU's.
* U fracties definiëren die in millicores worden gemeten. *100m* is bijvoorbeeld *0,1* van een onderliggende vCPU-kern.

In het volgende basisvoorbeeld voor een enkele NGINX-pod vraagt de pod *100 m* CPU-tijd en *128 Mi* geheugen. De resourcelimieten voor de pod zijn ingesteld op *250m* CPU en *256Mi* geheugen:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Zie [Compute resources voor containers beheren voor][k8s-resource-limits]meer informatie over resourcemetingen en toewijzingen.

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Toepassingen ontwikkelen en debuggen tegen een AKS-cluster

**Richtlijnen voor aanbevolen procedures** - Ontwikkelteams moeten een AKS-cluster implementeren en debuggen met Dev Spaces. Dit ontwikkelingsmodel zorgt ervoor dat op rollen gebaseerde toegangsbesturingselementen, netwerk- of opslagbehoeften worden geïmplementeerd voordat de app wordt geïmplementeerd in productie.

Met Azure Dev Spaces ontwikkelt, debugt en test u toepassingen rechtstreeks tegen een AKS-cluster. Ontwikkelaars binnen een team werken samen om te bouwen en te testen gedurende de levenscyclus van de toepassing. U bestaande hulpprogramma's zoals Visual Studio of Visual Studio Code blijven gebruiken. Er is een extensie geïnstalleerd voor Dev Spaces die de optie biedt om de toepassing uit te voeren en te debuggen in een AKS-cluster:

![Foutopsporingstoepassingen in een AKS-cluster met Dev Spaces](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Dit geïntegreerde ontwikkelings- en testproces met Dev Spaces vermindert de behoefte aan lokale testomgevingen, zoals [minikube.][minikube] In plaats daarvan ontwikkelt en test u tegen een AKS-cluster. Dit cluster kan worden beveiligd en geïsoleerd zoals in de vorige sectie over het gebruik van naamruimten om een cluster logisch te isoleren. Wanneer uw apps klaar zijn om te worden geïmplementeerd voor productie, u vol vertrouwen implementeren omdat uw ontwikkeling allemaal is uitgevoerd tegen een echt AKS-cluster.

Azure Dev Spaces is bedoeld voor gebruik met toepassingen die worden uitgevoerd op Linux-pods en -knooppunten.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>De Visual Studio Code-extensie gebruiken voor Kubernetes

**Richtlijnen voor best practices** - Installeer en gebruik de VS Code-extensie voor Kubernetes wanneer u YAML-manifesten schrijft. U de extensie ook gebruiken voor geïntegreerde implementatieoplossing, waarmee toepassingseigenaren die zelden met het AKS-cluster communiceren, kunnen helpen.

De [Visual Studio Code-extensie voor Kubernetes][vscode-kubernetes] helpt u bij het ontwikkelen en implementeren van applicaties naar AKS. De extensie biedt intellisense voor Kubernetes-bronnen en Helm-grafieken en sjablonen. U kubernetes-bronnen ook bekijken, implementeren en bewerken vanuit VS-code. De extensie biedt ook een intellisense controle op resourceaanvragen of -limieten die worden ingesteld in de podspecificaties:

![VS Code-extensie voor Kubernetes-waarschuwing over ontbrekende geheugenlimieten](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Controleer regelmatig op sollicitatieproblemen met kube-adviseur

**Richtlijnen voor aanbevolen procedures** - Voer `kube-advisor` regelmatig de nieuwste versie van het open source-hulpprogramma uit om problemen in uw cluster op te sporen. Als u resourcequota toepast op een `kube-advisor` bestaand AKS-cluster, voert u eerst pods uit die geen resourceaanvragen en -limieten hebben gedefinieerd.

De [kube-advisor][kube-advisor] tool is een bijbehorend AKS open source project dat een Kubernetes cluster scant en rapporteert over problemen die het vindt. Een handige controle is om pods te identificeren die geen resourceaanvragen en -limieten hebben.

De kube-advisor tool kan rapporteren over resource request en beperkingen ontbreken in PodSpecs voor Windows-toepassingen en Linux-toepassingen, maar de kube-advisor tool zelf moet worden gepland op een Linux-pod. U een pod plannen om een knooppuntgroep met een specifiek besturingssysteem uit te voeren met behulp van een [knooppuntkiezer][k8s-node-selector] in de configuratie van de pod.

In een AKS-cluster met veel ontwikkelteams en -toepassingen kan het moeilijk zijn om pods bij te houden zonder dat deze resourceaanvragen en limieten zijn ingesteld. Als een best practice, `kube-advisor` regelmatig draaien op uw AKS clusters.

## <a name="next-steps"></a>Volgende stappen

Dit artikel over aanbevolen procedures richtte zich op het uitvoeren van uw cluster en workloads vanuit het perspectief van de clusteroperator. Zie [Best practices voor clusteroperatoren voor isolatie en resourcebeheer in Azure Kubernetes Service (AKS)][operator-best-practices-isolation]voor informatie over aanbevolen procedures voor beheer.

Zie de volgende artikelen om een aantal van deze aanbevolen procedures te implementeren:

* [Ontwikkelen met Dev Spaces][dev-spaces]
* [Controleer op problemen met kube-adviseur][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
