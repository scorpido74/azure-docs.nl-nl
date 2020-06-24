---
title: Azure Red Hat open Shift v4. x configureren met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de bewaking van een Kubernetes-cluster configureert met Azure Monitor die worden gehost op Azure Red Hat open Shift versie 4 en hoger.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 872d842f02e19313940dfeba5258feb7d3799547
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888450"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Azure Red Hat open Shift v4. x configureren met Azure Monitor voor containers

Azure Monitor voor containers biedt uitgebreide bewakings ervaring voor de Azure Kubernetes-service (AKS) en AKS-engine clusters. In dit artikel wordt beschreven hoe u de bewaking van Kubernetes-clusters die worden gehost op [Azure Red Hat open Shift](../../openshift/intro-openshift.md) versie 4. x kunt inschakelen om een vergelijk bare bewakings ervaring te krijgen.

>[!NOTE]
>De ondersteuning voor Azure Red Hat open Shift is op dit moment een functie in Public Preview.
>

Azure Monitor voor containers kunnen worden ingeschakeld voor een of meer bestaande implementaties van Azure Red Hat open Shift v4. x met behulp van de volgende ondersteunde methoden:

- Voor een bestaand cluster met behulp van het meegeleverde bash-script en uitgevoerd in de [Azure cli](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Ondersteunde en niet-ondersteunde functies

Azure Monitor voor containers ondersteunt de bewaking van Azure Red Hat open Shift v4. x, zoals beschreven in het [overzichts](container-insights-overview.md) artikel, met uitzonde ring van de volgende functies:

- Live-gegevens (preview-versie)
- [Metrische gegevens verzamelen](container-insights-update-metrics.md) van cluster knooppunten en peulen en deze opslaan in de data base met Azure monitor gegevens

## <a name="prerequisites"></a>Vereisten

- Azure CLI-versie 2.0.72 of hoger

- [Helm 3](https://helm.sh/docs/intro/install/) CLI-hulp programma

- [Bash-versie 4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) -opdracht regel programma

- Als u de functies in Azure Monitor voor containers wilt inschakelen en openen, moet u Mini maal lid zijn van de rol Azure *contributor* in het Azure-abonnement en een lid van de rol [*log Analytics Inzender*](../platform/manage-access.md#manage-access-using-azure-permissions) van de werk ruimte log Analytics die is geconfigureerd met Azure monitor voor containers.

- Als u de bewakings gegevens wilt bekijken, bent u lid van de machtiging [*log Analytics lezer*](../platform/manage-access.md#manage-access-using-azure-permissions) -rol met de log Analytics werk ruimte die is geconfigureerd met Azure monitor voor containers.

## <a name="enable-for-an-existing-cluster"></a>Inschakelen voor een bestaand cluster

Voer de volgende stappen uit om de bewaking van een Azure Red Hat open Shift versie 4 en hoger-cluster in Azure in te scha kelen met behulp van het meegeleverde bash-script.

1. Aanmelden bij Azure

    ```azurecli
    az login
    ```

2. Down load het script en sla het op in een lokale map die uw cluster configureert met de invoeg toepassing voor bewaking met behulp van de volgende opdrachten:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Als u de **uitvoeren-context** van uw cluster wilt identificeren, `oc login` voert u de opdracht uit en kopieert u de waarde op uw cluster `kubectl config current-context` .

### <a name="integrate-with-an-existing-workspace"></a>Integreren met een bestaande werk ruimte

Met de volgende stap wordt het controleren van uw cluster ingeschakeld met het bash-script dat u eerder hebt gedownload. Als u wilt integreren met een bestaande Log Analytics-werk ruimte, voert u de volgende stappen uit om eerst de volledige Resource-ID te identificeren van uw Log Analytics werk ruimte die voor de `workspaceResourceId` para meter is vereist en voert u vervolgens de opdracht uit om de invoeg toepassing bewaking met de opgegeven werk ruimte in te scha kelen. Als u geen werk ruimte hebt om op te geven, kunt u de sectie overs Laan [met de standaardwerk ruimte](#integrate-with-default-workspace) en kunt u het script een nieuwe werk ruimte laten maken.

1. Een lijst met alle abonnementen waartoe u toegang hebt met behulp van de volgende opdracht:

    ```azurecli
    az account list --all -o table
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Kopieer de waarde voor **SubscriptionId**.

2. Schakel over naar het abonnement dat als host fungeert voor de Log Analytics-werk ruimte met behulp van de volgende opdracht:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. In het volgende voor beeld wordt de lijst met werk ruimten in uw abonnementen weer gegeven in de standaard JSON-indeling.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Zoek in de uitvoer de naam van de werk ruimte en kopieer de volledige Resource-ID van die Log Analytics werk ruimte onder de veld **-id**.

4. Voer de volgende opdracht uit om de bewaking in te scha kelen en de waarde voor de `workspaceResourceId` `azureAroV4ResourceIdparameter` para meters te vervangen: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <workspaceResourceId>`

    Voorbeeld:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4 /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische gegevens van de status voor het cluster kunt weer geven.

### <a name="integrate-with-default-workspace"></a>Integreren met standaard werkruimte

Met de volgende stap wordt bewaking van uw Azure Red Hat open Shift v4. x-cluster ingeschakeld met behulp van het bash-script dat u hebt gedownload. In dit voor beeld hoeft u geen bestaande werk ruimte te maken of op te geven. Met deze opdracht wordt het proces voor u vereenvoudigd door een standaardwerk ruimte te maken in de standaard resource groep van het cluster abonnement als deze nog niet in de regio bestaat. De standaardwerk ruimte die wordt gemaakt, lijkt op de indeling van *DefaultWorkspace- \<GUID> - \<Region> *.  

`bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

Bijvoorbeeld:

`bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Nadat u bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de metrische gegevens van de status voor het cluster kunt weer geven.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

In de weer gave met meerdere clusters in Azure Monitor voor containers worden uw Azure Red Hat open Shift-clusters gemarkeerd waarvoor geen bewaking is ingeschakeld op het tabblad **niet-bewaakte clusters** . De optie **inschakelen** naast het cluster initieert geen controle van de bewaking vanuit de portal. U wordt doorgestuurd naar dit artikel om de bewaking hand matig in te scha kelen volgens de stappen eerder in dit artikel.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer in het menu Azure Portal of op de start pagina **Azure monitor**. Selecteer in de sectie **insightss** de optie **containers**.

3. Selecteer de **niet-bewaakte clusters**op de pagina **monitor-containers** .

4. Zoek in de lijst met niet-bewaakte clusters het cluster in de lijst en klik op **inschakelen**. U kunt de resultaten in de lijst identificeren door te zoeken naar de waarde **Aro** onder het kolom **cluster type**. Nadat u op **inschakelen**hebt geklikt, wordt u doorgestuurd naar dit artikel.

## <a name="next-steps"></a>Volgende stappen

- Als bewaking is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw versie 4. x-cluster en werk belastingen van Red Hat open Shift, leert u Azure Monitor voor containers [te gebruiken](container-insights-analyze.md) .

- De container agent verzamelt standaard de container/stderr-logboeken van alle containers die worden uitgevoerd in alle naam ruimten, met uitzonde ring van uitvoeren-System. Als u de container logboek verzameling specifiek wilt configureren voor bepaalde naam ruimten of naam ruimten, bekijkt u de configuratie van de [container Insights-agent](container-insights-agent-config.md) om de gewenste instellingen voor het verzamelen van gegevens te configureren voor uw ConfigMap-configuratie bestand.

- Als u Prometheus-metrische gegevens uit uw cluster wilt opwaarderen en analyseren, raadpleegt u [Prometheus metrische gegevens](container-insights-prometheus-integration.md) weer geven

- Zie [het bewaken van uw Azure Red Hat open Shift-cluster stoppen](container-insights-optout-openshift.md)voor meer informatie over het stoppen van het bewaken van uw cluster met Azure monitor voor containers.
