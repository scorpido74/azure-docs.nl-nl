---
title: Omgaan met Linux-knooppunt reboots met kured
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het bijwerken van Linux-knooppunten en het automatisch opnieuw opstarten met kured in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 8006baa3025ee1e794359bed854094cc9005dd14
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668388"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Beveiligings- en kernelupdates toepassen op Linux-knooppunten in Azure Kubernetes Service (AKS)

Om uw clusters te beschermen, worden beveiligingsupdates automatisch toegepast op Linux-knooppunten in AKS. Deze updates omvatten beveiligingsoplossingen van het besturingssysteem of kernelupdates. Voor sommige van deze updates is een herstart van het knooppunt vereist om het proces te voltooien. AKS start deze Linux-knooppunten niet automatisch opnieuw op om het updateproces te voltooien.

Het proces om Windows Server-knooppunten (momenteel in preview in AKS) up-to-date te houden, is een beetje anders. Windows Server-knooppunten ontvangen geen dagelijkse updates. In plaats daarvan voert u een AKS-upgrade uit waarmee nieuwe knooppunten worden geïmplementeerd met de nieuwste basiswindowserver-afbeelding en -patches. Zie [Een knooppuntgroep in AKS][nodepool-upgrade]bijwerken voor AKS-clusters die Windows Server-knooppunten gebruiken.

In dit artikel ziet u hoe u de open-source [kured (KUbernetes REboot Daemon)][kured] gebruiken om te kijken naar Linux-knooppunten waarvoor een reboot nodig is, en vervolgens automatisch de herschikking van lopende pods en het herstartproces van het knooppunt verwerken.

> [!NOTE]
> `Kured`is een open-source project van Weaveworks. Ondersteuning voor dit project in AKS wordt op een best-effort basis verstrekt. Extra ondersteuning is te vinden in het slack-kanaal van de #weave-community.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U hebt ook de Azure CLI-versie 2.0.59 of hoger geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>De aks-nodeupdate-ervaring begrijpen

In een AKS-cluster worden uw Kubernetes-knooppunten uitgevoerd als Virtuele Azure-machines (VM's). Deze Linux-gebaseerde VM's gebruiken een Ubuntu-afbeelding, waarbij het besturingssysteem is geconfigureerd om elke avond automatisch te controleren op updates. Als er beveiligings- of kernelupdates beschikbaar zijn, worden ze automatisch gedownload en geïnstalleerd.

![AKS node update en reboot proces met kured](media/node-updates-kured/node-reboot-process.png)

Voor sommige beveiligingsupdates, zoals kernelupdates, is een herstart van het knooppunt nodig om het proces af te ronden. Een Linux-knooppunt waarvoor een reboot vereist is, maakt een bestand met de naam */var/run/reboot-vereist.* Dit herstartproces gebeurt niet automatisch.

U uw eigen werkstromen en processen gebruiken `kured` om opnieuw opstarten van knooppunten af te handelen of om het proces te orkestreren. Met `kured`, wordt een [DaemonSet][DaemonSet] geïmplementeerd die een pod op elk Linux-knooppunt in het cluster uitvoert. Deze pods in het DaemonSet-horloge voor het bestaan van het */var/run/reboot-vereist* bestand en starten vervolgens een proces om de knooppunten opnieuw op te starten.

### <a name="node-upgrades"></a>Knooppuntupgrades

Er is een extra proces in AKS waarmee u een cluster *upgraden.* Een upgrade is meestal om over te gaan naar een nieuwere versie van Kubernetes, niet alleen van toepassing knooppunt beveiligingsupdates. Een AKS-upgrade voert de volgende acties uit:

* Er wordt een nieuw knooppunt geïmplementeerd met de nieuwste beveiligingsupdates en kubernetes-versie.
* Een oud knooppunt wordt afgezet en afgevoerd.
* Pods zijn gepland op het nieuwe knooppunt.
* Het oude knooppunt wordt verwijderd.

U niet op dezelfde Kubernetes-versie blijven tijdens een upgradegebeurtenis. U moet een nieuwere versie van Kubernetes opgeven. Als u wilt upgraden naar de nieuwste versie van Kubernetes, u [uw AKS-cluster upgraden.][aks-upgrade]

## <a name="deploy-kured-in-an-aks-cluster"></a>Kured implementeren in een AKS-cluster

Als u `kured` de DaemonSet wilt implementeren, installeert u de volgende officiële Koerste-Helm-grafiek. Hiermee wordt een rol- en clusterrol, bindingen en een serviceaccount gemaakt en wordt de DaemonSet vervolgens geïmplementeerd met behulp van `kured`.

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

U ook aanvullende `kured`parameters configureren voor, zoals integratie met Prometheus of Slack. Zie de grafiek van de [Helm][kured-install]voor meer informatie over aanvullende configuratieparameters.

## <a name="update-cluster-nodes"></a>Clusterknooppunten bijwerken

Standaard controleren Linux-knooppunten in AKS elke avond op updates. Als u niet wilt wachten, u handmatig een `kured` update uitvoeren om te controleren of deze correct wordt uitgevoerd. Volg eerst de stappen naar [SSH naar een van uw AKS-knooppunten.][aks-ssh] Zodra u een SSH-verbinding met het Linux-knooppunt hebt, controleert u op updates en past u deze als volgt toe:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Als er updates zijn toegepast waarvoor een knooppunt opnieuw moet worden opgestart, wordt een bestand geschreven naar */var/run/reboot-vereist.* `Kured`controleert standaard voor knooppunten waarvoor standaard elke 60 minuten een reboot nodig is.

## <a name="monitor-and-review-reboot-process"></a>Opnieuw opstarten controleren en controleren

Wanneer een van de replica's in de DaemonSet heeft gedetecteerd dat een node reboot nodig is, wordt er een vergrendeling op het knooppunt geplaatst via de Kubernetes API. Dit slot voorkomt dat er extra pods op het knooppunt worden gepland. Het slot geeft ook aan dat slechts één knooppunt tegelijk opnieuw moet worden opgestart. Met het knooppunt afgezet, worden lopende peulen uit het knooppunt afgevoerd en wordt het knooppunt opnieuw opgestart.

U de status van de knooppunten controleren met de opdracht [kubectl get nodes.][kubectl-get-nodes] In de volgende voorbeelduitvoer wordt een knooppunt weergegeven met de status *SchedulingDisabled* terwijl het knooppunt zich voorbereidt op het herstartproces:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Zodra het updateproces is voltooid, u de status van de knooppunten `--output wide` bekijken met behulp van de opdracht [kubectl get nodes][kubectl-get-nodes] met de parameter. Met deze extra uitvoer ziet u een verschil in *kernel-versie* van de onderliggende knooppunten, zoals wordt weergegeven in de volgende voorbeelduitvoer. De *aks-nodepool1-28993262-0* is in een vorige stap bijgewerkt en toont kernelversie *4.15.0-1039-azure*. De node *aks-nodepool1-28993262-1* die niet is bijgewerkt toont kernelversie *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel `kured` wordt beschreven hoe u Linux-knooppunten automatisch opnieuw opstarten als onderdeel van het beveiligingsupdateproces. Als u wilt upgraden naar de nieuwste versie van Kubernetes, u [uw AKS-cluster upgraden.][aks-upgrade]

Zie [Een knooppuntgroep in AKS][nodepool-upgrade]bijwerken voor AKS-clusters die Windows Server-knooppunten gebruiken.

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
