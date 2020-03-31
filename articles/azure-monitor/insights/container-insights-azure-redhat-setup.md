---
title: Azure Red Hat OpenShift-clusters configureren met Azure Monitor voor containers | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de bewaking van een Kubernetes-cluster configureert met Azure Monitor die wordt gehost op Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275514"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Azure Red Hat OpenShift-clusters configureren met Azure Monitor voor containers

Azure Monitor voor containers biedt uitgebreide monitoringervaring voor de clusters Azure Kubernetes Service (AKS) en AKS Engine. In dit artikel wordt beschreven hoe u het monitoren van Kubernetes-clusters die worden gehost op [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) mogelijk maakt om een vergelijkbare bewakingservaring te bereiken.

>[!NOTE]
>Ondersteuning voor Azure Red Hat OpenShift is een functie in openbare preview op dit moment.
>

Azure Monitor voor containers kan worden ingeschakeld voor nieuwe of een of meer bestaande implementaties van Azure Red Hat OpenShift met behulp van de volgende ondersteunde methoden:

- Voor een bestaand cluster uit de Azure-portal of voor het gebruik van azure resource manager-sjabloon.
- Voor een nieuw cluster met azure resource manager-sjabloon of tijdens het maken van een nieuw cluster met de [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Ondersteunde en niet-ondersteunde functies

Azure Monitor voor containers ondersteunt het bewaken van Azure Red Hat OpenShift zoals beschreven in het artikel [Overzicht,](container-insights-overview.md) met uitzondering van de volgende functies:

- Live gegevens (voorbeeld)
- [Statistieken verzamelen](container-insights-update-metrics.md) van clusterknooppunten en pods en deze opslaan in de Azure Monitor-metrische gegevensdatabase

## <a name="prerequisites"></a>Vereisten

- Als u de functies in Azure Monitor voor containers wilt inschakelen en openen, moet u minimaal lid zijn van de *azure-bijdragerol* in het Azure-abonnement en lid zijn van de rol [*Log Analytics-bijdrager van*](../platform/manage-access.md#manage-access-using-azure-permissions) de werkruimte Log Analytics die is geconfigureerd met Azure Monitor voor containers.

- Als u de bewakingsgegevens wilt weergeven, bent u lid van de machtigingen voor de [*leeslezer van Logboekanalyse*](../platform/manage-access.md#manage-access-using-azure-permissions) met de werkruimte Log Analytics die is geconfigureerd met Azure Monitor voor containers.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Inschakelen voor een nieuw cluster met een Azure Resource Manager-sjabloon

Voer de volgende stappen uit om een Azure Red Hat OpenShift-cluster te implementeren met ingeschakelde bewaking. Voordat u verdergaat, bekijkt u de zelfstudie [Een Azure Red Hat OpenShift-cluster maken](../../openshift/tutorial-create-cluster.md#prerequisites) om inzicht te krijgen in de afhankelijkheden die u moet configureren, zodat uw omgeving correct is ingesteld.

Deze methode bevat twee JSON-sjablonen. Eén sjabloon geeft de configuratie op om het cluster te implementeren met ingeschakelde bewaking en de andere bevat parameterwaarden die u configureert om het volgende op te geven:

- De Azure Red Hat OpenShift-clusterbron-id.

- De resourcegroep waarin het cluster is geïmplementeerd.

- [Azure Active Directory-tenant-id](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) die is genoteerd na het uitvoeren van de stappen om een of een al gemaakte te maken.

- [Azure Active Directory-clienttoepassings-id](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) die is genoteerd na het uitvoeren van de stappen om een of een al gemaakte te maken.

- [Het geheim van Azure Active Directory-client](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) is opgemerkt nadat de stappen zijn uitgevoerd om een of een al gemaakt azure te maken.

- [Azure AD-beveiligingsgroep](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) heeft na het uitvoeren van de stappen om een of een reeds gemaakte stap te maken opgemerkt.

- Resource-id van een bestaande Log Analytics-werkruimte.

- Het aantal hoofdknooppunten dat in het cluster moet worden gemaakt.

- Het aantal compute nodes in het agentpoolprofiel.

- Het aantal infrastructuurknooppunten in het agentpoolprofiel.

Zie als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u de Azure CLI moet installeren of upgraden.

De werkruimte Log Analytics moet worden gemaakt voordat u bewaking inschakelt met Azure PowerShell of CLI. Als u de werkruimte wilt maken, u deze instellen via [Azure Resource Manager,](../../azure-monitor/platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure-portal.](../../azure-monitor/learn/quick-create-workspace.md)

1. Download en sla op in een lokale map, de sjabloon Azure Resource Manager en het parameterbestand, om een cluster te maken met de bewakingsadd-on met behulp van de volgende opdrachten:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Aanmelden bij Azure

    ```azurecli
    az login    
    ```

    Als u toegang hebt tot `az account set -s {subscription ID}` meerdere `{subscription ID}` abonnementen, voert u de vervanging uit van het abonnement dat u wilt gebruiken.

3. Maak een resourcegroep voor uw cluster als u er nog geen hebt. Zie [Ondersteunde regio's](../../openshift/supported-resources.md#azure-regions)voor een lijst met Azure-regio's die OpenShift op Azure ondersteunt.

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Bewerk het JSON-parameterbestand **newClusterWithMonitoringParam.json** en werk de volgende waarden bij:

    - *Locatie*
    - *clusterNaam*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. In de volgende stap wordt het cluster geïmplementeerd met bewaking die is ingeschakeld met behulp van azure cli.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    De uitvoer lijkt op het volgende:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Inschakelen voor een bestaand cluster

Voer de volgende stappen uit om de bewaking van een Azure Red Hat OpenShift-cluster dat in Azure is geïmplementeerd, in te schakelen. U dit doen vanuit de Azure-portal of met behulp van de meegeleverde sjablonen.

### <a name="from-the-azure-portal"></a>Vanuit Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **Azure Monitor**in het menu Azure portal of op de startpagina. Selecteer **containers** in **Containers**de sectie Inzichten .

3. Selecteer op de pagina **Monitor - containers** de optie **Niet-bewaakte clusters**.

4. Zoek in de lijst met niet-bewaakte clusters het cluster in de lijst en klik op **Inschakelen**. U de resultaten in de lijst identificeren door te zoeken naar de waarde **ARO** onder het **kolomCLUSTERTYPE**.

5. Als u op de pagina **Onboarding naar Azure Monitor voor containers** een bestaande Log Analytics-werkruimte in hetzelfde abonnement als het cluster hebt, selecteert u deze in de vervolgkeuzelijst.  
    De lijst selecteert vooraf de standaardwerkruimte en locatie waaraan het cluster in het abonnement is geïmplementeerd.

    ![Bewaking inschakelen voor niet-bewaakte clusters](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Als u een nieuwe werkruimte Log Analytics wilt maken voor het opslaan van de bewakingsgegevens uit het cluster, volgt u de instructies in [Een werkruimte Logboekanalyse maken.](../../azure-monitor/learn/quick-create-workspace.md) Zorg ervoor dat u de werkruimte maakt in hetzelfde abonnement waarop het RedHat OpenShift-cluster is geïmplementeerd.

Nadat u de bewaking hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u statusstatistieken voor het cluster weergeven.

### <a name="enable-using-an-azure-resource-manager-template"></a>Inschakelen met een Azure Resource Manager-sjabloon

Deze methode bevat twee JSON-sjablonen. Eén sjabloon geeft de configuratie op om bewaking in te schakelen en de andere bevat parameterwaarden die u configureert om het volgende op te geven:

- De Azure RedHat OpenShift-clusterbron-id.

- De resourcegroep waarin het cluster is geïmplementeerd.

- Een Log Analytics-werkruimte.

Zie als u niet bekend bent met het concept van het implementeren van resources met behulp van een sjabloon:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u ervoor kiest de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u de Azure CLI moet installeren of upgraden.

De werkruimte Log Analytics moet worden gemaakt voordat u bewaking inschakelt met Azure PowerShell of CLI. Als u de werkruimte wilt maken, u deze instellen via [Azure Resource Manager,](../../azure-monitor/platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure-portal.](../../azure-monitor/learn/quick-create-workspace.md)

1. Download de sjabloon en het parameterbestand om uw cluster bij te werken met de bewakingsadd-on met behulp van de volgende opdrachten:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Aanmelden bij Azure

    ```azurecli
    az login    
    ```

    Als u toegang hebt tot `az account set -s {subscription ID}` meerdere `{subscription ID}` abonnementen, voert u de vervanging uit van het abonnement dat u wilt gebruiken.

3. Geef het abonnement op van het Azure RedHat OpenShift-cluster.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Voer de volgende opdracht uit om de clusterlocatie en resource-id te identificeren:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Bewerk het JSON-parameterbestand **existingClusterParam.json** en werk de waarden *araResourceId* en *araResoruceLocation*bij . De waarde voor **workspaceResourceId** is de volledige resource-id van uw Log Analytics-werkruimte, die de naam van de werkruimte bevat.

6. Voer de volgende opdrachten uit om te implementeren met Azure CLI:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    De uitvoer lijkt op het volgende:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Volgende stappen

- Als monitoring is ingeschakeld om de status en het gebruik van resources van uw RedHat OpenShift-cluster en workloads die op deze services worden uitgevoerd, te verzamelen, leest u hoe u Azure Monitor voor containers [gebruiken.](container-insights-analyze.md)

- Zie [Het bewaken van uw Azure Red Hat OpenShift-cluster](container-insights-optout-openshift.md)stoppen met het bewaken van uw cluster met Azure Monitor.
