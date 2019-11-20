---
title: Azure Red Hat open Shift-clusters configureren met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers kunt configureren voor het bewaken van Kubernetes-clusters die worden gehost op Azure Red Hat open SHIFT.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 6fc81fa84d4d40d5318d6bf3690aa4b90c4e3c9b
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196811"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Azure Red Hat open Shift-clusters met Azure Monitor voor containers configureren

Azure Monitor voor containers biedt uitgebreide bewakings ervaring voor de Azure Kubernetes-service (AKS) en AKS-engine clusters. In dit artikel wordt beschreven hoe u de bewaking van Kubernetes-clusters die worden gehost op [Azure Red Hat open Shift](../../openshift/intro-openshift.md) , inschakelt om een vergelijk bare bewakings ervaring te krijgen.

Azure Monitor voor containers kunnen worden ingeschakeld voor nieuwe of een of meer bestaande implementaties van Azure Red Hat open SHIFT met behulp van de volgende ondersteunde methoden:

- Voor een bestaand cluster van de Azure Portal of met Azure Resource Manager sjabloon
- Voor een nieuw cluster met Azure Resource Manager sjabloon 

## <a name="supported-and-unsupported-features"></a>Ondersteunde en niet-ondersteunde functies

Azure Monitor voor containers biedt ondersteuning voor het bewaken van Azure Red Hat open Shift zoals beschreven in het artikel [overzicht](container-insights-overview.md) , met uitzonde ring van de volgende functies:

- Live-gegevens
- Prometheus metrische gegevens
- Metrische gegevens verzamelen
- Status functie

## <a name="prerequisites"></a>Vereisten

- Als u de functies in Azure Monitor voor containers wilt inschakelen en openen, moet u Mini maal lid zijn van de rol Azure *contributor* in het Azure-abonnement en een lid van de rol [*log Analytics Inzender*](../platform/manage-access.md#manage-access-using-azure-permissions) van de werk ruimte log Analytics die is geconfigureerd met Azure monitor voor containers.

- Als u de bewakings gegevens wilt bekijken, bent u lid van de machtiging [*log Analytics lezer*](../platform/manage-access.md#manage-access-using-azure-permissions) -rol met de log Analytics werk ruimte die is geconfigureerd met Azure monitor voor containers.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Inschakelen voor een nieuw cluster met behulp van een Azure Resource Manager sjabloon

Voer de volgende stappen uit om een Azure Red Hat open Shift-cluster te implementeren waarvoor bewaking is ingeschakeld. Voordat u verdergaat, leest u de zelf studie [een Azure Red Hat open Shift-cluster maken](../../openshift/tutorial-create-cluster.md#prerequisites) om inzicht te krijgen in de afhankelijkheden die u moet configureren zodat uw omgeving correct is ingesteld.

Deze methode omvat twee JSON-sjablonen. Met één sjabloon wordt de configuratie opgegeven voor het implementeren van het cluster waarvoor bewaking is ingeschakeld. de andere bevat parameter waarden die u configureert om het volgende op te geven:

- De resource-ID van de Azure Red Hat open Shift-cluster. 

- De resource groep waarin het cluster is geïmplementeerd.

- [Azure Active Directory Tenant-id](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) die is geconstateerd na het uitvoeren van de stappen voor het maken van een of een al gemaakte.

- [Azure Active Directory client toepassings-id](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) na het uitvoeren van de stappen voor het maken van een of een al gemaakt.

- [Azure Active Directory client geheim](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) dat is vermeld na het uitvoeren van de stappen voor het maken van een of een al gemaakt.

- [Azure AD-beveiligings groep](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) genoteerd na het uitvoeren van de stappen voor het maken van een of een al gemaakte.

- Resource-ID van een bestaande Log Analytics-werk ruimte.

- Het aantal hoofd knooppunten dat in het cluster moet worden gemaakt.

- Het aantal reken knooppunten in het profiel van de agent groep.

- Het aantal infrastructuur knooppunten in het profiel van de agent groep. 

Als u niet bekend met het concept bent van het implementeren van resources met behulp van een sjabloon, Zie:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Als u ervoor de Azure CLI gebruiken kiest, moet u eerst installeren en de CLI lokaal gebruikt. U moet de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voor het identificeren van uw versie uitvoeren `az --version`. Als u wilt installeren of upgraden van de Azure CLI, Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli). 

De Log Analytics-werk ruimte moet worden gemaakt voordat u bewaking met behulp van Azure PowerShell of CLI inschakelt. Voor het maken van de werkruimte, u kunt dit instellen via [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), tot en met [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), of in de [Azure-portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Down load en sla de bestanden op in een lokale map, het Azure Resource Manager sjabloon en het parameter bestand, om een cluster met de invoeg toepassing voor bewaking te maken met behulp van de volgende opdrachten:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json` 

2. Aanmelden bij Azure 

    ```azurecli
    az login    
    ```
    
    Als u toegang hebt tot meerdere abonnementen, voert u `az account set -s {subscription ID}` vervangen `{subscription ID}` uit met het abonnement dat u wilt gebruiken.
 
3. Maak een resource groep voor het cluster als u er nog geen hebt. Zie [ondersteunde regio's](../../openshift/supported-resources.md#azure-regions)voor een lijst met Azure-regio's die openshift op Azure ondersteunen. 

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location> 
    ```

4. Bewerk het JSON-parameter bestand **newClusterWithMonitoringParam. json** en werk de volgende waarden bij:

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret* 
    - *aadCustomerAdminGroupId* 
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. In de volgende stap wordt het cluster geïmplementeerd met bewaking met behulp van de Azure CLI. 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json 
    ```
 
    De uitvoer ziet er ongeveer als volgt uit:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Inschakelen voor een bestaand cluster

Voer de volgende stappen uit om de bewaking in te scha kelen van een Azure Red Hat open Shift-cluster dat is geïmplementeerd in Azure. U kunt dit doen met behulp van de Azure Portal of door de meegeleverde sjablonen te gebruiken.

### <a name="from-the-azure-portal"></a>Van de Azure Portal
 
1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Selecteer in het menu Azure Portal of op de start pagina **Azure monitor**. Onder de **Insights** sectie, selecteer **Containers**. 

3. Op de **Monitor - containers** weergeeft, schakelt **clusters niet bewaakt**.

4. Zoek in de lijst met niet-bewaakte clusters het cluster in de lijst en klik op **inschakelen**. U kunt de resultaten in de lijst identificeren door te zoeken naar de waarde **Aro** onder het kolom **cluster type**.

5. Op de **Onboarding naar Azure Monitor voor containers** pagina, hebt u een bestaande Log Analytics-werkruimte in hetzelfde abonnement bevinden als het cluster, selecteert u deze in de vervolgkeuzelijst.  
    De lijst preselecteert de standaardwerk ruimte en de standaard locatie waarin het cluster is geïmplementeerd in het abonnement. 

    ![Bewaking voor niet-bewaakte clusters inschakelen](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Als u maken van een nieuwe werkruimte voor logboekanalyse wilt voor het opslaan van de gegevens uit het cluster, volg de instructies in [een Log Analytics-werkruimte maken](../../azure-monitor/learn/quick-create-workspace.md). Zorg ervoor dat u de werk ruimte maakt in hetzelfde abonnement dat het RedHat open Shift-cluster is geïmplementeerd. 
 
Wanneer u bewaking inschakelt, is het duurt ongeveer 15 minuten voordat u de gezondheid van metrische gegevens voor het cluster kunt weergeven. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Inschakelen met behulp van een Azure Resource Manager sjabloon

Deze methode omvat twee JSON-sjablonen. Een sjabloon Hiermee geeft u de configuratie voor bewaking en de andere bevat parameterwaarden die u configureert voor het volgende opgeven:

- De resource-ID van Azure RedHat open Shift cluster. 

- De resource groep waarin het cluster is geïmplementeerd.

- Een Log Analytics-werkruimte.

Als u niet bekend met het concept bent van het implementeren van resources met behulp van een sjabloon, Zie:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)

- [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Als u ervoor de Azure CLI gebruiken kiest, moet u eerst installeren en de CLI lokaal gebruikt. U moet de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voor het identificeren van uw versie uitvoeren `az --version`. Als u wilt installeren of upgraden van de Azure CLI, Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli). 

De Log Analytics-werk ruimte moet worden gemaakt voordat u bewaking met behulp van Azure PowerShell of CLI inschakelt. Voor het maken van de werkruimte, u kunt dit instellen via [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), tot en met [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), of in de [Azure-portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Down load de sjabloon en het parameter bestand om uw cluster bij te werken met de invoeg toepassing bewaking met behulp van de volgende opdrachten:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json` 

2. Aanmelden bij Azure 

    ```azurecli
    az login    
    ```

    Als u toegang hebt tot meerdere abonnementen, voert u `az account set -s {subscription ID}` vervangen `{subscription ID}` uit met het abonnement dat u wilt gebruiken.

3. Geef het abonnement van het Azure RedHat open Shift-cluster op.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Voer de volgende opdracht uit om de cluster locatie en de resource-ID te identificeren:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName> 
    ```

5. Bewerk het JSON-parameter bestand **existingClusterParam. json** en werk de waarden *araResourceId* en *araResoruceLocation*bij. De waarde voor **workspaceResourceId** is de volledige resource-ID van uw Log Analytics-werkruimte, waaronder de naam van de werkruimte. 

6. Als u wilt implementeren met Azure CLI, voert u de volgende opdrachten uit: 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json 
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Volgende stappen

Als bewaking is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw RedHat open Shift-cluster en workloads die hierop worden uitgevoerd, leert [u hoe u Azure monitor gebruikt](container-insights-analyze.md) voor containers.