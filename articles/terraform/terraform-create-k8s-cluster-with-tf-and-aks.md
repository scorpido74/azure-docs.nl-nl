---
title: Zelfstudie - Een Kubernetes-cluster maken met Azure Kubernetes Service (AKS) met Terraform
description: In deze zelfstudie maakt u een Kubernetes-cluster met Azure Kubernetes Service en Terraform
keywords: azure devops terraform aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 0a193c8da6441a04f742894797521fe92f26b2e1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945297"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Zelfstudie: Een Kubernetes-cluster maken met Azure Kubernetes Service met Terraform

[Azure Kubernetes Service (AKS)](/azure/aks/) beheert uw gehoste Kubernetes-omgeving. Met AKS u containerized toepassingen implementeren en beheren zonder expertise op het gebied van containerorkestratie. AKS stelt u ook in staat om veel voorkomende onderhoudswerkzaamheden uit te voeren zonder uw app offline te halen. Deze bewerkingen omvatten het inrichten, upgraden en schalen van resources op aanvraag.

In deze zelfstudie leert u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * HCL (HashiCorp Configuration Language) gebruiken om een Kubernetes-cluster te definiëren
> * Terraform en AKS gebruiken om een Kubernetes-cluster te maken
> * Het hulpprogramma kubectl gebruiken om de beschikbaarheid van een Kubernetes-cluster te testen

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

- **Terraform configureren**: volg de aanwijzingen in het artikel [Terraform en toegang tot Azure configureren](terraform-install-configure.md)

- **Azure service-principal**: volg de aanwijzingen in de sectie **Create the service principal** (de service-principal maken) in het artikel [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) (een Azure-service-principal maken met Azure CLI). Noteer de waarden voor appId, displayName, password en tenant.

## <a name="create-the-directory-structure"></a>De mapstructuur maken

De eerste stap is het maken van een map voor de Terraform-configuratiebestanden voor de oefening.

1. Blader naar [Azure Portal](https://portal.azure.com).

1. Azure [Cloud Shell openen](/azure/cloud-shell/overview). Als u nog geen omgeving hebt geselecteerd, selecteert u **Bash** als uw omgeving.

    ![Cloud Shell-prompt](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Ga naar de map `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Maak een map met de naam `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Maak de nieuwe directory de actieve directory:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>De Azure-provider declareren

Maak het Terraform-configuratiebestand waarin de Azure-provider wordt gedeclareerd.

1. Maak in Cloud Shell een bestand met de naam `main.tf`.

    ```bash
    code main.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    provider "azurerm" {
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="define-a-kubernetes-cluster"></a>Een Kubernetes-cluster definiëren

Maak het Terraform-configuratiebestand waarin de resources voor het Kubernetes-cluster worden gedeclareerd.

1. Maak in Cloud Shell een bestand met de naam `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    De vorige code stelt de naam van het cluster, de locatie en de naam van de brongroep in. Het voorvoegsel voor de volledig gekwalificeerde domeinnaam (FQDN) is ook ingesteld. De FQDN wordt gebruikt om toegang te krijgen tot het cluster.

    Met `linux_profile` de record u de instellingen configureren waarmee u inloggen op de werknemersknooppunten met SSH.

    Met AKS betaalt u alleen voor de werkknooppunten. De `default_node_pool` record configureert de details voor deze werkknooppunten. Het `default_node_pool record` omvat het aantal te maken werkknooppunten en het type werknemersknooppunten. Als u het cluster in de toekomst moet opschalen `count` of schalen, wijzigt u de waarde in deze record.

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="declare-the-variables"></a>De variabelen declareren

1. Maak in Cloud Shell een bestand met de naam `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="create-a-terraform-output-file"></a>Een Terraform-uitvoerbestand maken

Met [Terraform-uitvoer](https://www.terraform.io/docs/configuration/outputs.html) kunt u waarden definiëren die voor de gebruiker worden gemarkeerd wanneer in Terraform een plan wordt toegepast. Deze uitvoer kan worden opgevraagd met de opdracht `terraform output`. In deze sectie maakt u een uitvoerbestand waarmee via [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) toegang wordt verkregen tot het cluster.

1. Maak in Cloud Shell een bestand met de naam `output.tf`.

    ```bash
    code output.tf
    ```

1. Plak de volgende code in de editor:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }
    ```

1. Sla het**&lt;** bestand op ( Ctrl>S ) en sluit de editor af**&lt;(Ctrl>Q).**

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Azure Storage instellen voor opslag van de Terraform-status

In Terraform wordt de status lokaal bijgehouden via het bestand `terraform.tfstate`. Dit patroon werkt goed in een omgeving met één persoon. In een omgeving voor meerdere personen wordt [Azure-opslag](/azure/storage/) gebruikt om de status bij te houden.

In deze sectie ziet u hoe u de volgende taken uitvoert:
- Opslagaccountgegevens ophalen (accountnaam en accountsleutel)
- Maak een opslagcontainer waarin Terraform staatsinformatie wordt opgeslagen.

1. Selecteer in Azure Portal de optie **Alle services** in het linkermenu.

1. Selecteer **Opslagaccounts**.

1. Selecteer op het tabblad **Opslagaccounts** de naam van het opslagaccount waarin de Terraform-status moet worden opgeslagen. U kunt bijvoorbeeld het opslagaccount gebruiken dat u hebt gemaakt toen u Cloud Shell de eerste keer opende.  De opslagaccountnaam die door Cloud Shell wordt gemaakt, begint gewoonlijk met `cs`, gevolgd door een willekeurige tekenreeks met cijfers en letters. Let op het opslagaccount dat u selecteert. Deze waarde is later nodig.

1. Selecteer op het opslagaccounttabblad **Toegangssleutels**.

    ![Opslagaccountmenu](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Noteer de waarde van de **key1-** **sleutel**. (Als u het pictogram rechts van de sleutel selecteert, wordt de waarde gekopieerd naar het klembord.)

    ![Toegangssleutels voor opslagaccount](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Maak in Cloud Shell een container in uw Azure-opslagaccount. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw omgeving.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Het Kubernetes-cluster maken

In deze sectie ziet u hoe u de opdracht `terraform init` gebruikt om de resources te maken die zijn gedefinieerd in de configuratiebestanden die u in de vorige secties hebt gemaakt.

1. In Cloud Shell, initialiseren Terraform. Vervang de tijdelijke aanduidingen door de juiste waarden voor uw omgeving.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    De `terraform init` opdracht geeft het succes weer van het initialiseren van de backend en providerplug-in:

    ![Voorbeeld van de resultaten van de opdracht 'terraform init'](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exporteer uw service-principal-referenties. Vervang de tijdelijke aanduidingen door de juiste waarden van uw serviceprincipal.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Voer de opdracht `terraform plan` uit om het Terraform-plan te maken waarmee de infrastructuurelementen worden gedefinieerd. 

    ```bash
    terraform plan -out out.plan
    ```

    Met de opdracht `terraform plan` geeft u de resources weer die worden gemaakt tijdens het uitvoeren van de opdracht `terraform apply`:

    ![Voorbeeld van de resultaten van de opdracht 'terraform plan'](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Voer de opdracht `terraform apply` uit om het plan voor het maken van het Kubernetes-cluster toe te passen. Het proces om een Kubernetes-cluster te maken kan enkele minuten duren, wat resulteert in de timing van de Cloud Shell-sessie. Als de Cloud Shell-sessie een time-out heeft, u de stappen volgen in de sectie 'Herstellen van een time-out van Cloud Shell' zodat u de zelfstudie voltooien.

    ```bash
    terraform apply out.plan
    ```

    Met de opdracht `terraform apply` geeft u de resultaten weer van het maken van de resources die zijn gedefinieerd in uw configuratiebestanden:

    ![Voorbeeld van de resultaten van de opdracht 'terraform apply'](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Selecteer in de Azure-portal **Alle bronnen** in het linkermenu om de bronnen te bekijken die zijn gemaakt voor uw nieuwe Kubernetes-cluster.

    ![Alle bronnen in de Azure-portal](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Herstellen na een time-out van Cloud Shell

Als de Cloud Shell-sessie een time-out heeft, u de volgende stappen uitvoeren om te herstellen:

1. Start een Cloud Shell-sessie.

1. Ga naar de map met uw Terraform-configuratiebestanden.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Voer de volgende opdracht uit:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Het Kubernetes-cluster testen

U kunt het gemaakte cluster controleren met de Kubernetes-hulpprogramma's.

1. Haal de Kubernetes-configuratie op uit de Terraform-status en sla deze op in een bestand dat door kubectl kan worden gelezen.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Stel een omgevingsvariabele in zodat de juiste configuratie wordt opgehaald door kubectl.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Controleer de status van het cluster.

    ```bash
    kubectl get nodes
    ```

    De details van de werkknooppunten worden weergegeven. De status van al deze werkknooppunten moet **Gereed** zijn, zoals in de volgende afbeelding is weergegeven:

    ![Met het hulpprogramma kubectl kunt u de status van uw Kubernetes-cluster controleren](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Status en logboeken controleren

Toen het AKS-cluster werd gemaakt, werd controleren ingeschakeld om metrische gegevens over de status van de clusterknooppunten en -pods vast te leggen. Deze metrische gegevens over de status zijn in de Azure-portal beschikbaar. Zie [Status van Azure Kubernetes Service controleren](/azure/azure-monitor/insights/container-insights-overview) voor meer informatie over het controleren van de status van de container.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van Terraform in Azure](/azure/terraform)