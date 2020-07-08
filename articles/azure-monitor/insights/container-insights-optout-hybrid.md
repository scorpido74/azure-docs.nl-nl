---
title: Stoppen met het bewaken van uw hybride Kubernetes-cluster | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de bewaking van uw hybride Kubernetes-cluster met Azure Monitor voor containers kunt stoppen.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 86a774737d5269d77c4053ad61ab870b13288aa7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84885853"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Het bewaken van uw hybride cluster stoppen

Nadat u de bewaking van uw Kubernetes-cluster hebt ingeschakeld, kunt u stoppen met het bewaken van het cluster met Azure Monitor voor containers als u besluit dat u het niet meer wilt controleren. In dit artikel wordt beschreven hoe u dit kunt doen voor de volgende omgevingen:

- AKS-engine op Azure en Azure Stack
- Open Shift versie 4 en hoger
- Kubernetes met Azure Arc (preview)

## <a name="how-to-stop-monitoring-using-helm"></a>Bewaking stoppen met helm

De volgende stappen zijn van toepassing op de volgende omgevingen:

- AKS-engine op Azure en Azure Stack
- Open Shift versie 4 en hoger

1. Voer de volgende helm-opdracht uit als u eerst de Azure Monitor voor de helm-grafiek release van containers op uw cluster wilt identificeren.

    ```
    helm list
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release 1* vertegenwoordigt de helm-grafiek versie voor Azure monitor voor containers.

2. Als u de grafiek release wilt verwijderen, voert u de volgende helm-opdracht uit.

    `helm delete <releaseName>`

    Voorbeeld:

    `helm delete azmon-containers-release-1`

    Hiermee wordt de release uit het cluster verwijderd. U kunt dit controleren door de opdracht uit te voeren `helm list` :

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Het kan enkele minuten duren voordat de configuratie is gewijzigd. Omdat helm uw releases registreert, zelfs nadat u ze hebt verwijderd, kunt u de geschiedenis van een cluster controleren en zelfs een release verwijderen met `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Bewaking stoppen voor Kubernetes met Arc ingeschakeld

### <a name="using-powershell"></a>PowerShell gebruiken

1. Down load het script en sla het op in een lokale map die uw cluster configureert met de invoeg toepassing voor bewaking met behulp van de volgende opdrachten:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Configureer de `$azureArcClusterResourceId` variabele door de bijbehorende waarden in te stellen voor `subscriptionId` `resourceGroupName` en `clusterName` de resource-id van uw Azure Arc-Kubernetes-cluster resource weer te geven.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configureer de `$kubeContext` variabele met de **uitvoeren-context** van uw cluster door de opdracht uit te voeren `kubectl config get-contexts` . Als u de huidige context wilt gebruiken, stelt u de waarde in op `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Voer de volgende opdracht uit om het bewaken van het cluster te stoppen.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

### <a name="using-bash"></a>Bash gebruiken

1. Down load het script en sla het op in een lokale map die uw cluster configureert met de invoeg toepassing voor bewaking met behulp van de volgende opdrachten:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Configureer de `azureArcClusterResourceId` variabele door de bijbehorende waarden in te stellen voor `subscriptionId` `resourceGroupName` en `clusterName` de resource-id van uw Azure Arc-Kubernetes-cluster resource weer te geven.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configureer de `kubeContext` variabele met de **uitvoeren-context** van uw cluster door de opdracht uit te voeren `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Als u het cluster niet meer wilt bewaken, zijn er verschillende opdrachten beschikbaar op basis van uw implementatie scenario.

    Voer de volgende opdracht uit om het bewaken van het cluster te stoppen met de huidige context.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Voer de volgende opdracht uit om het bewaken van het cluster te stoppen door een context op te geven

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

## <a name="next-steps"></a>Volgende stappen

Als de Log Analytics-werk ruimte alleen is gemaakt ter ondersteuning van het bewaken van het cluster en het niet meer nodig is, moet u het hand matig verwijderen. Zie [een Azure log Analytics-werk ruimte verwijderen](../../log-analytics/log-analytics-manage-del-workspace.md)als u niet bekend bent met het verwijderen van een werk ruimte.
